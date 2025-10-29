---
tags:
  - javascript
  - programming
---
# BetterFetch Class - Retry Logic Implementation

## Overview

A custom fetch wrapper that adds **automatic retry logic** with exponential backoff for handling failed HTTP requests. Perfect for making API calls more resilient to temporary failures.

---

## Complete Code

```javascript
class BetterFetch {
    constructor(globalConfig = {}, maxRetries = 3, apiTimeout = 3000) {
        this.globalConfig = globalConfig;
        this.maxRetries = maxRetries;
        this.apiTimeout = apiTimeout;
    }

    get(url, config = {}) {
        return fetch(url, { ...this.globalConfig, ...config });
    }

    wait(time) {
        return new Promise(resolve => setTimeout(resolve, time));
    }

    // Calculate exponential backoff with jitter
    getBackoffDelay(attempt) {
        const base = 300;
        const max = 2000;
        const expo = Math.min(max, base * 2 ** (attempt - 1));
        const jitter = Math.random() * 0.3 * expo;
        return Math.round(expo + jitter);
    }   

    // Determine if error/response is retryable
    shouldRetry(response, error) {
        if (error) {
            // Retry on network errors, timeouts, and aborts
            return error.name === "AbortError" || error.name === "TypeError";
        }
        // Retry server errors and rate limits
        return response && (response.status >= 500 || response.status === 429 || response.status === 408);
    }

    // Single fetch attempt with timeout
    async fetchWithTimeout(url, config) {
        const abortController = new AbortController();
        const timeoutId = setTimeout(
            () => abortController.abort(new Error("Request timeout")),
            this.apiTimeout
        );

        try {
            const response = await fetch(url, {
                ...config,
                signal: abortController.signal
            });
            return response;
        } finally {
            clearTimeout(timeoutId);
        }
    }

    async getWithRetry(url, config = {}) {
        let lastError;
        const mergedConfig = { ...this.globalConfig, ...config };

        for (let attempt = 1; attempt <= this.maxRetries; attempt++) {
            console.log(`Attempt ${attempt}/${this.maxRetries}`);

            try {
                // Each attempt gets its own AbortController
                const response = await this.fetchWithTimeout(url, mergedConfig);

                if (!response.ok) {
                    // Check if we should retry this status code
                    if (this.shouldRetry(response, null) && attempt < this.maxRetries) {
                        const delay = this.getBackoffDelay(attempt);
                        console.log(`Got ${response.status}, retrying in ${delay}ms...`);
                        await this.wait(delay);
                        continue;
                    }
                    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                }

                console.log("Request succeeded");
                return response;

            } catch (error) {
                lastError = error instanceof Error ? error : new Error(String(error));

                // Check if we should retry this error
                if (attempt < this.maxRetries && this.shouldRetry(null, lastError)) {
                    const delay = this.getBackoffDelay(attempt);
                    console.log(`Error: ${`lastError`.message}, retrying in ${delay}ms...`);
                    await this.wait(delay);
                    continue;
                }

                // No more retries or non-retryable error
                throw lastError;
            }
        }

        // Should not reach here, but just in case
        throw lastError || new Error("Max retries exceeded");
    }
}

// Usage Example
document.addEventListener("DOMContentLoaded", async function () {
    const betterFetch = new BetterFetch({}, 3, 5000); // 5 second timeout
    const apiUrl = "https://get.geojs.io/v2/ip/country.json?ip=8.8.8.8";

    betterFetch.getWithRetry(apiUrl)
        .then(res => res.json())
        .then(res => console.log("API call success:", res))
        .catch(err => console.error("Final error:", err.message));
});
```

---

## Constructor Parameters

```javascript
new BetterFetch(globalConfig, maxRetries, apiTimeout)
```

|Parameter|Default|Purpose|
|---|---|---|
|`globalConfig`|`{}`|Default fetch options applied to all requests|
|`maxRetries`|`3`|Maximum number of retry attempts|
|`apiTimeout`|`3000`|Timeout in milliseconds (3 seconds)|

---

## Key Methods Explained

### 1. `get(url, config)`

Basic fetch wrapper that merges global and local configs.

**Purpose:** Simple fetch without retry logic.

---

### 2. `getWithRetry(url, config)` ⭐ Main Method

Makes a fetch request with automatic retry logic.

**How it works:**

1. Tries the request up to `maxRetries` times
2. On failure, checks if error is retryable using `shouldRetry()`
3. Waits using exponential backoff before retrying
4. Returns response if successful
5. Throws error if all retries exhausted

**Loop structure:**

- Uses `for` loop for attempts
- `continue` skips to next iteration for retries
- `throw` exits immediately for non-retryable errors

---

### 3. `fetchWithTimeout(url, config)`

Wraps fetch with a timeout mechanism using `AbortController`.

**Why needed?** Prevents requests from hanging indefinitely.

**How it works:**

- Creates `AbortController` for each request
- Sets timeout that triggers `abort()`
- Cleans up timeout in `finally` block (runs whether success or fail)

---

### 4. `shouldRetry(response, error)`

Decides if a failed request should be retried.

**Retries these errors:**

- `AbortError` - Request timed out
- `TypeError` - Network error (no internet, DNS failure, CORS)

**Retries these status codes:**

- `500+` - Server errors (temporary issues)
- `429` - Rate limiting (too many requests)
- `408` - Request timeout

**Does NOT retry:**

- `400` - Bad request (your request is wrong)
- `401` - Unauthorized (need authentication)
- `403` - Forbidden (no permission)
- `404` - Not found (resource doesn't exist)

**Why?** Client errors won't fix themselves by retrying.

---

### 5. `getBackoffDelay(attempt)` - Exponential Backoff

Calculates wait time before next retry.

**Formula:**

```
delay = min(2000, 300 × 2^(attempt-1)) + random_jitter
```

**Components:**

- **Base:** 300ms starting delay
- **Max:** 2000ms cap on exponential growth
- **Jitter:** Random 0-30% variation

**Example delays:**

- Attempt 1: ~300-390ms
- Attempt 2: ~600-780ms
- Attempt 3: ~1200-1560ms

**Why exponential?** Each retry waits longer, giving struggling servers more time to recover.

**Why jitter?** Prevents multiple clients from retrying at exact same time (thundering herd problem).

---

### 6. `wait(time)`

Simple promise-based delay helper.

**Implementation:**

```javascript
return new Promise(resolve => setTimeout(resolve, time));
```

Creates a promise that resolves after `time` milliseconds. Used with `await` to pause execution.

---

## Important Concepts

### Exponential Backoff

Wait time **doubles** with each retry.

- Prevents overwhelming a struggling server
- Industry standard for retry logic
- Used by AWS, Google Cloud, etc.

### Jitter (Randomization)

Adds random variation (30%) to retry delays.

- Spreads out retry attempts over time
- Prevents synchronized retry storms
- Example: 1000 failed clients don't all retry at exactly 1 second

### AbortController

Browser API for canceling fetch requests.

- Creates a `signal` passed to fetch
- Calling `abort()` cancels the request
- Triggers `AbortError` that can be caught

---

## Request Flow Diagram

```
Start Request
    ↓
Attempt 1, 2, 3...
    ↓
fetchWithTimeout() → Set timeout → fetch()
    ↓
Response received?
    ↓
┌───YES──────────────┐
│   Status OK?       │
│   YES → Return     │
│   NO → shouldRetry?│
│      YES → Backoff │
│      NO → Throw    │
└────────────────────┘
    ↓
┌───NO (Error)───────┐
│  shouldRetry?      │
│  YES → Backoff     │
│  NO → Throw        │
└────────────────────┘
```

---

## Usage Example Breakdown

```javascript
const betterFetch = new BetterFetch(
    {},      // no global headers/config
    3,       // try up to 3 times total
    5000     // 5 second timeout per request
);

const apiUrl = "https://get.geojs.io/v2/ip/country.json?ip=8.8.8.8";

betterFetch.getWithRetry(apiUrl)
    .then(res => res.json())           // Parse JSON from response
    .then(data => console.log(data))   // Log the data
    .catch(err => console.error(err)); // Handle final failure
```

**What happens:**

1. First attempt with 5s timeout
2. If fails and retryable → wait ~300-390ms → retry
3. If fails again → wait ~600-780ms → retry
4. If fails third time → throw error
5. If any attempt succeeds → return response

---

## Interview Talking Points

### Why do we need retry logic?

Network requests can fail due to temporary issues:

- Network glitches or packet loss
- Server temporary overload
- Rate limiting
- Server restarts/deployments

Retries make applications **resilient** to these temporary failures.

### Why exponential backoff instead of fixed delays?

- **Fixed delay (bad):** Keeps hitting server at same rate
- **Exponential (good):** Gives server more breathing room with each retry
- Prevents making the problem worse

### Why add jitter (randomization)?

**The thundering herd problem:**

- 1000 clients make a request
- Server crashes, all fail at once
- All wait exactly 1 second
- All retry at same time → another crash

**With jitter:** Retries spread out over 300-390ms window instead of all at 300ms.

### Why not retry all errors?

Some errors are **permanent:**

- `404 Not Found` - URL is wrong
- `401 Unauthorized` - Missing credentials
- `400 Bad Request` - Request format is wrong

Retrying these wastes resources and delays error reporting.

### Why use AbortController?

Without timeout:

- Request could hang forever if server doesn't respond
- User waits indefinitely
- Resources (memory, connections) not freed

AbortController lets us cancel the request after a timeout.

### Real-world scenarios this handles:

1. **API rate limiting** - Retry after `429` status
2. **Temporary network blip** - User's WiFi drops for 500ms
3. **Server deployment** - Server restarts during your request
4. **Database connection pool full** - Server returns `503`, frees up connection, retry succeeds
5. **CDN cache miss** - First request times out fetching from origin, second hits cache

---

## Limitations & Improvements

### Current Limitations:

1. **No request deduplication** - Multiple identical requests all retry independently
2. **No circuit breaker** - Doesn't stop trying if service is clearly down
3. **Console logging** - Not production-ready (should use proper logger)
4. **No metrics** - Can't track retry rates or success/failure patterns
5. **Memory** - Each request creates new AbortController

### Possible Improvements:

1. Add circuit breaker pattern (stop all requests after X consecutive failures)
2. Request caching/deduplication
3. Configurable logging
4. Retry metrics and monitoring hooks
5. Support for POST/PUT/DELETE (current design is GET-focused)
6. Retry budget (limit total retries across all requests)

---

## Quick Reference Card

|Feature|Value|
|---|---|
|**Default retries**|3 attempts total|
|**Default timeout**|3000ms (3 seconds)|
|**Backoff base**|300ms|
|**Backoff max**|2000ms|
|**Jitter**|30% random variation|
|**Retryable errors**|`AbortError`, `TypeError`|
|**Retryable status**|`408`, `429`, `500+`|
|**Non-retryable**|`400`, `401`, `403`, `404`|

---

## Testing Scenarios to Mention

1. **Network offline** → TypeError → Retry 3 times → Fail
2. **API returns 500** → Retry → Server recovers → Success on attempt 2
3. **Request times out** → AbortError → Retry → Success
4. **API returns 404** → Don't retry → Fail immediately
5. **Rate limited (429)** → Retry with backoff → Success after delay

---

## Tags

`#javascript` `#fetch-api` `#retry-logic` `#exponential-backoff` `#error-handling` `#api` `#network` `#resilience`