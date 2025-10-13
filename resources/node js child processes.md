---
tags:
  - backend
  - node
  - programming
---
# Node.js `child_process.fork()` — Obsidian-ready tutorial (with working example)

This guide walks you through creating a tiny Express API that offloads a CPU-heavy task (checking if a number is prime) to a **forked child process**. The child computes and sends the result back to the parent, which then responds to the HTTP request—**exactly like the screenshots**.

---

## Why fork a child process?

JavaScript in Node runs on a single thread. CPU-intensive work (like big prime checks) can block the event loop and make your API feel sluggish. `child_process.fork()` spins up a **separate Node process** to run heavy work in parallel, keeping your main server responsive.

---

## What you’ll build

- `server.js` — Express server with route `GET /:num`
    
- `child.js` — a worker process that:
    
    - receives a number
        
    - checks if it’s prime
        
    - `process.send(...)` the result to the parent
        
    - exits after a short delay (to mirror the screenshot)
        

**Folder structure:**

```
process-nodejs/
├─ node_modules/
├─ package.json
├─ server.js
└─ child.js
```

---

## 1) Initialize the project

```bash
mkdir process-nodejs
cd process-nodejs
npm init -y
npm i express
```

OPTIONAL (nice dev experience):

```bash
npm i -D nodemon
```

Add a start script to `package.json`:

```json
{
  "name": "process-nodejs",
  "version": "1.0.0",
  "type": "commonjs",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}
```

> [!note] CommonJS vs ESM  
> The screenshots use `require(...)`, so `"type": "commonjs"` is used here. If you prefer ESM, convert imports/exports accordingly.

---

## 2) Create `child.js` (the worker)

```js
// child.js
console.log("CHILD CREATED!", process.pid);

// Listen for messages from the parent process
process.on("message", (message) => {
  console.log("Child received:", message);

  const num = Number(message);
  const result = {
    input: num,
    isPrime: is_prime(num),
    pid: process.pid
  };

  // Send result back to the parent
  process.send(result);

  // Mirror the screenshot behavior: exit a bit later
  setTimeout(() => process.exit(0), 5000);
});

// Naive prime checker (good enough for the demo)
function is_prime(number) {
  if (number < 2) return false;
  if (number === 2) return true;
  if (number % 2 === 0) return false;

  const limit = Math.floor(Math.sqrt(number));
  for (let i = 3; i <= limit; i += 2) {
    if (number % i === 0) return false;
  }
  return true;
}
```

**What’s happening:**

- `process.on("message", handler)` lets the child react to values sent by the parent.
    
- `process.send(payload)` sends a value back up to the parent.
    
- We exit after 5 seconds to match the screenshot’s `setTimeout(process.exit, 5000)`.
    

---

## 3) Create `server.js` (the parent / API)

```js
// server.js
const { fork } = require("child_process");
const express = require("express");
const app = express();

// GET /:num  --> check whether :num is prime
app.get("/:num", (req, res) => {
  const num = Number(req.params.num);
  if (Number.isNaN(num)) {
    return res.status(400).json({ error: "Please provide a valid number" });
  }

  // Start a child process running child.js
  const child = fork("./child.js");

  // Send the number to the child
  child.send(num);

  // When the child replies, respond to the HTTP request
  child.on("message", (message) => {
    console.log("Parent received:", message);
    // e.g. { input: 97, isPrime: true, pid: 12345 }
    res.json(message);
  });

  // Log when the child exits (mirrors screenshot)
  child.on("exit", (code) => {
    console.log("Child exited with code", code);
  });

  // Basic error handling
  child.on("error", (err) => {
    console.error("Child process error:", err);
    if (!res.headersSent) {
      res.status(500).json({ error: "Worker failed" });
    }
  });
});

// Start the server
const PORT = process.env.PORT || 8080;
app.listen(PORT, () => {
  console.log(`Server listening on http://localhost:${PORT}`);
});
```

**Key bits that match the screenshot:**

- `const { fork } = require("child_process");`
    
- `const express = require("express");`
    
- `const child = fork("child.js");`
    
- `child.send(num);`
    
- `child.on("message", ...)` to `res.json(...)`
    
- `child.on("exit", ...)` to log exit code
    

---

## 4) Run it

```bash
npm run start
# or during development:
npm run dev
```

Open in your browser or use `curl`:

```bash
curl http://localhost:8080/97
# -> {"input":97,"isPrime":true,"pid":12345}

curl http://localhost:8080/10000019
# -> might be true/false depending on the number
```

> [!tip] Try a biggish number  
> Use a larger prime candidate (e.g., `982451653`). You’ll see the parent stays responsive because the heavy check runs in the child.

---

## 5) How the parent/child talk

- **Parent → Child:** `child.send(value)`
    
- **Child → Parent:** `process.send(value)`
    
- Suitable for JSON-serializable data (behind the scenes it uses an IPC channel created by `fork`).
    

---

## 6) Common pitfalls (and fixes)

> [!warning] Returning twice / hanging responses  
> If you attach multiple `child.on("message", ...)` handlers for one request or keep the child alive, you could accidentally try to `res.json(...)` more than once. In this demo, the child exits after 5s and we only send once per request.

> [!warning] Not validating inputs  
> Always coerce to `Number` and guard against `NaN` before sending to the child.

> [!warning] Leaking child processes  
> For long-running servers, either:
> 
> - call `child.kill()` after responding, or
>     
> - reuse a pool of workers, or
>     
> - ensure the child exits itself (as we do here).
>     

---

## 7) Optional improvements

- **Kill child immediately after response**
    
    ```js
    child.on("message", (message) => {
      res.json(message);
      child.kill(); // don’t wait 5s
    });
    ```
    
- **Timeout protection** (avoid waiting forever)
    
    ```js
    const timer = setTimeout(() => {
      if (!res.headersSent) res.status(504).json({ error: "Worker timed out" });
      child.kill();
    }, 10000);
    
    child.on("message", (msg) => {
      clearTimeout(timer);
      res.json(msg);
      child.kill();
    });
    ```
    
- **Use a worker pool** if you expect many concurrent CPU jobs (libraries like `generic-pool` help).
    
- **Consider `worker_threads`** for CPU-bound JS in the same process; for separate memory/process isolation, stick with `fork()`.
    

---

## 8) Quick reference

```txt
fork(scriptPath[, args][, options])  -> ChildProcess
child.send(message)                  // parent -> child
child.on("message", handler)         // receive from child
process.on("message", handler)       // in child: get from parent
process.send(message)                // in child: send to parent
child.on("exit", code => { ... })
child.kill([signal])
```

---

## 9) What to paste into Obsidian

- This entire note works as-is.
    
- Copy the two code blocks for `server.js` and `child.js` into files in your project folder.
    
- Run `npm i` then `npm run start`.
    
- Hit `http://localhost:8080/97`.
    

---

## 10) Example console output (matches the screenshots’ flow)

```
Server listening on http://localhost:8080
CHILD CREATED! 34210
Child received: 97
Parent received: { input: 97, isPrime: true, pid: 34210 }
Child exited with code 0
```

That’s it! You now have a clean, screenshot-faithful `fork()` example you can drop into Obsidian and your project.