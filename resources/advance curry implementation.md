---
tags:
  - programming
  - javascript
  - design-patterns
---
# Mastering JavaScript Currying: Complete Implementation Guide with Proxy Approach

## What is Currying?

Currying transforms a function that takes multiple arguments into a sequence of functions that each take a single argument. This enables partial application - calling a function with some arguments now and the rest later.

## Implementation 1: Traditional Recursive Approach

```javascript
function curry(func) {
  return function curried(...args) {
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };
}
```

### How It Works: Step by Step

#### Example: `curriedSum(1)(2)(3)`

**Original Function:**
```javascript
function sum(a, b, c) {
  return a + b + c;
}
const curriedSum = curry(sum);
```

#### Step 1: `curriedSum(1)`
```javascript
// Inside first curried call:
args = [1]
func.length = 3
1 >= 3? NO

// Return new function that closes over current context
return function(...args2) {
  return curried.apply(this, [1].concat(args2));
}
```

#### Step 2: `(2)` - First Recursive Call
```javascript
// Inside returned function:
args2 = [2]

// Execute recursive call with preserved context:
return curried.apply(this, [1, 2]);

// Now inside curried again:
args = [1, 2]
func.length = 3  
2 >= 3? NO

// Return another function:
return function(...args2) {
  return curried.apply(this, [1, 2].concat(args2));
}
```

#### Step 3: `(3)` - Final Call
```javascript
// Inside second returned function:
args2 = [3]

// Execute final recursive call:
return curried.apply(this, [1, 2, 3]);

// Now inside curried:
args = [1, 2, 3]
func.length = 3
3 >= 3? YES

// Call original function with preserved context:
return func.apply(this, [1, 2, 3]);
```

---

## Implementation 2: Proxy-Based Approach

Now let's examine the innovative Proxy-based implementation:

```javascript
function curry(f) {
  return new Proxy(f, {
    apply(target, thisArg, args) {
      return (args.length >= target.length) ?
        Reflect.apply(...arguments) :
        curry(target).bind(thisArg, ...args);
    }
  });
}
```

### Line-by-Line Explanation

#### Line 1: `function curry(f) {`
- **Purpose**: Defines the curry function that takes a function `f` to be curried
- **Parameter**: `f` - the original function we want to curry

#### Line 2: `return new Proxy(f, {`
- **Purpose**: Creates a Proxy wrapper around the original function
- **Proxy**: A special object that allows us to intercept and customize operations on the target object/function
- **Target**: The original function `f` becomes the target of the proxy

#### Line 3: `apply(target, thisArg, args) {`
- **Purpose**: Defines the trap for function calls
- **Triggered when**: The proxy is called as a function
- **Parameters**:
  - `target`: The original function (`f`)
  - `thisArg`: The `this` context for the function call
  - `args`: Array of arguments passed to the function

#### Line 4: `return (args.length >= target.length) ?`
- **Purpose**: Checks if we have enough arguments
- `args.length`: Number of arguments provided in current call
- `target.length`: Number of parameters the original function expects
- This is the same logic as the traditional approach

#### Line 5: `Reflect.apply(...arguments) :`
- **Purpose**: If we have enough arguments, call the original function
- `Reflect.apply(target, thisArg, args)`: Equivalent to `target.apply(thisArg, args)`
- `...arguments`: Spreads the same arguments (target, thisArg, args) to Reflect.apply
- **Result**: Executes the original function with all collected arguments

#### Line 6: `curry(target).bind(thisArg, ...args);`
- **Purpose**: If not enough arguments, create a new curried function with bound arguments
- `curry(target)`: Recursively creates a new curried proxy for the same function
- `.bind(thisArg, ...args)`: Binds the current context and pre-applies collected arguments
- **Result**: Returns a new function that remembers the context and arguments so far

### How the Proxy Approach Works

#### Step 1: `curriedSum(1)`
```javascript
// Proxy intercepts the call:
target = sum function
thisArg = undefined (global context)
args = [1]

// Check: 1 >= 3? NO
// Return: curry(sum).bind(undefined, 1)
// This creates a new curried function with 1 pre-bound
```

#### Step 2: `(2)` - Second Call
```javascript
// Now calling the bound function from step 1
target = sum function  
thisArg = undefined (preserved from bind)
args = [1, 2] (1 from bind, 2 from current call)

// Check: 2 >= 3? NO
// Return: curry(sum).bind(undefined, 1, 2)
```

#### Step 3: `(3)` - Final Call
```javascript
// Now calling the bound function from step 2
target = sum function
thisArg = undefined (preserved)
args = [1, 2, 3] (1,2 from bind, 3 from current call)

// Check: 3 >= 3? YES
// Return: Reflect.apply(sum, undefined, [1, 2, 3])
// Executes: sum(1, 2, 3) = 6
```

### Context Preservation in Proxy Approach

The Proxy approach preserves context differently:

```javascript
const calculator = {
  multiplier: 10,
  multiply: function(a, b, c) {
    return this.multiplier * (a + b + c);
  }
};

const curriedMultiply = curry(calculator.multiply);

// Context flows through .bind():
curriedMultiply.call(calculator, 1)(2)(3);
```

**How context is preserved:**
1. First call: `thisArg = calculator` captured in proxy trap
2. Partial application: `.bind(calculator, 1)` preserves context
3. Subsequent calls: Bound context maintained through chain

## Comparison: Traditional vs Proxy Approach

### Traditional Approach
**Pros:**
- More explicit and easier to understand
- Better browser support
- No ES6 Proxy requirement

**Cons:**
- More code
- Manual closure management

### Proxy Approach  
**Pros:**
- More elegant and concise
- Automatic interception of calls
- Cleaner separation of concerns

**Cons:**
- Requires ES6 Proxy support
- Less explicit about what's happening
- Slightly more complex debugging

## Real-World Usage Examples

### 1. Configuration and Setup (Both Approaches)
```javascript
// API configuration
const createAPIRequest = curry(function(baseURL, endpoint, method, data) {
  return fetch(`${baseURL}/${endpoint}`, {
    method: method,
    body: JSON.stringify(data)
  });
});

// Partial application for specific API
const myAPI = createAPIRequest('https://api.example.com');
const usersEndpoint = myAPI('users');
const getUser = usersEndpoint('GET');

// Usage:
getUser(); // GET https://api.example.com/users
```

### 2. Event Handling with Proxy Approach
```javascript
const handleEvent = curry(function(eventType, element, callback, event) {
  console.log(`${eventType} on ${element}`);
  callback(event);
});

// The proxy approach handles this seamlessly:
const handleButtonClick = handleEvent('click')('button');
document.querySelector('button').addEventListener('click', handleButtonClick(console.log));
```

### 3. Validation Functions
```javascript
const validate = curry(function(field, rule, value) {
  return rule(value) ? `${field} is valid` : `${field} is invalid`;
});

const validateEmail = validate('email');
const emailValidator = validateEmail(val => val.includes('@'));

console.log(emailValidator('test@example.com')); // "email is valid"
```

## Key Differences in Implementation

### Argument Collection
- **Traditional**: Manual concatenation with `.concat()`
- **Proxy**: Automatic through function calls and `.bind()`

### Context Preservation  
- **Traditional**: Explicit `apply(this)` calls
- **Proxy**: Automatic through `.bind(thisArg, ...args)`

### Recursion
- **Traditional**: Explicit recursive function calls
- **Proxy**: Implicit through proxy re-wrapping

## Performance Considerations

**Traditional approach** is generally faster as it doesn't involve Proxy overhead.

**Proxy approach** is more modern and can be more flexible for advanced use cases.

## Browser Compatibility

- **Traditional**: Works in all browsers
- **Proxy**: Requires modern browsers (IE11 not supported)

## Recommendation

For production code, use the **traditional approach** for better compatibility and performance. Use the **proxy approach** for learning, modern applications, or when you need the additional flexibility that proxies provide.

Both implementations achieve the same result but demonstrate different JavaScript metaprogramming techniques. Understanding both will make you a better JavaScript developer!
