---
tags:
  - javascript
  - programming
---


# 🌀 JavaScript Iterators and Generators — Easy Guide

---

## 🧠 1. What are Iterators?

An **iterator** is a special object that helps you **iterate (loop)** over data (like arrays, strings, etc.) _one element at a time_.

In JavaScript, every object that implements the `Symbol.iterator` method is **iterable** (like arrays, strings, maps, sets...).

---

### 🔹 Basic Example — Array Iterator

```js
var x = ["Apple", "Orange", "Grapes"];

let y = x[Symbol.iterator]();

console.log(y.next());
console.log(y.next());
console.log(y.next());
console.log(y.next());
```

#### 🧩 Output:

```
{ value: 'Apple', done: false }
{ value: 'Orange', done: false }
{ value: 'Grapes', done: false }
{ value: undefined, done: true }
```

Each call to `.next()` returns an object with:

- `value`: the current item
    
- `done`: whether the iterator is finished
    

---

## 🧮 2. Manual Iterator with a Loop

We can also manually iterate using a `while` loop.

```js
let numbers = [100, 200, 300];

let iter = numbers[Symbol.iterator]();

let result = iter.next();

while (!result.done) {
  console.log(result.value);
  result = iter.next();
}
```

#### 🧩 Output:

```
100
200
300
```

This pattern is how JavaScript internally handles loops like `for...of`.

---

## 🧵 3. Custom Iterator Function

You can even **create your own iterator** manually!

```js
function numberIterator(arr) {
  var nextNum = 0;
  return {
    next() {
      if (nextNum < arr.length) {
        return {
          value: arr[nextNum++],
          done: false
        };
      } else {
        return {
          value: undefined,
          done: true
        };
      }
    }
  };
}

let num = [10, 20, 30, 40];
let numIter = numberIterator(num);

console.log(numIter.next());
console.log(numIter.next());
console.log(numIter.next());
console.log(numIter.next());
console.log(numIter.next());
```

#### 🧩 Output:

```
{ value: 10, done: false }
{ value: 20, done: false }
{ value: 30, done: false }
{ value: 40, done: false }
{ value: undefined, done: true }
```

This is how **custom data structures** can define their own iteration logic.

---

## ⚙️ 4. Generators — A Simpler Way to Create Iterators

A **generator function** makes creating iterators easier.

Use `function*` syntax (notice the `*`).

```js
function* generateIt() {
  console.log("First message");
  yield "Yield 1";
  console.log("Second message");
  yield "Yield 2";
}

const g = generateIt();

console.log(g.next());
console.log(g.next());
console.log(g.next());
```

#### 🧩 Output:

```
First message
{ value: 'Yield 1', done: false }
Second message
{ value: 'Yield 2', done: false }
{ value: undefined, done: true }
```

- `yield` pauses the generator and returns a value.
    
- `next()` resumes execution from the last yield.
    

---

## 🔁 5. Infinite Generator Example

You can even create **infinite sequences** with generators!

```js
function* gen() {
  let nextNum = 300;
  while (true) {
    yield nextNum++;
  }
}

let g2 = gen();

console.log(g2.next());
console.log(g2.next());
console.log(g2.next());
console.log(g2.next());
```

#### 🧩 Output:

```
{ value: 300, done: false }
{ value: 301, done: false }
{ value: 302, done: false }
{ value: 303, done: false }
```

You can also iterate it safely using a loop:

```js
for (let value of g2) {
  if (value > 308) break;
  console.log(value);
}
```

#### 🧩 Output:

```
304
305
306
307
308
```

---

## 🔢 6. Sending Data _into_ Generators

You can send values **into** a generator with `next(value)`.

```js
function* gen2() {
  let result = (yield) + 10;
  console.log(`Result is ${result}`);
}

const g3 = gen2();
console.log(g3.next());      // start generator
console.log(g3.next(500));   // send value into generator
```

#### 🧩 Output:

```
{ value: undefined, done: false }
Result is 510
{ value: undefined, done: true }
```

---

## 📦 7. Multiple Yields and Collecting Values

```js
function* gen3() {
  let result = [yield, yield, yield];
  console.log(`Result is ${result[2]}`);
}

let g4 = gen3();
console.log(g4.next());
console.log(g4.next(100));
console.log(g4.next(200));
console.log(g4.next(300));
```

#### 🧩 Output:

```
{ value: undefined, done: false }
{ value: undefined, done: false }
{ value: undefined, done: false }
Result is 300
{ value: undefined, done: true }
```

Each `yield` receives its next value from the next `.next()` call.

---

## 🌐 8. Delegating Generators — `yield*`

`yield*` allows one generator to **delegate** to another iterable (like an array).

```js
function* gen4() {
  yield 50;
  yield* ["Node", "Angular", "React"];
}

let g5 = gen4();
console.log(g5.next());
console.log(g5.next());
console.log(g5.next());
console.log(g5.next());
```

#### 🧩 Output:

```
{ value: 50, done: false }
{ value: 'Node', done: false }
{ value: 'Angular', done: false }
{ value: 'React', done: false }
```

You can also spread generator output:

```js
console.log([...gen4()]);
// Output: [50, 'Node', 'Angular', 'React']
```

---

## 🛑 9. Stopping a Generator — `.return()`

`.return()` lets you end a generator early.

```js
function* gen5() {
  yield 50;
  yield* ["Node", "Angular", "React"];
}

let g6 = gen5();

console.log(g6.next());
console.log(g6.return("Ending now"));
console.log(g6.next());
```

#### 🧩 Output:

```
{ value: 50, done: false }
{ value: 'Ending now', done: true }
{ value: undefined, done: true }
```

---

## 🧭 Summary

|Concept|Description|Example|
|---|---|---|
|**Iterator**|Object with `.next()` that returns `{value, done}`|`arr[Symbol.iterator]()`|
|**Iterable**|Object implementing `Symbol.iterator`|Arrays, Strings|
|**Generator**|Function that automatically creates an iterator|`function*`|
|**yield**|Pauses and returns value|`yield value`|
|**yield***|Delegates to another iterable|`yield* ['A', 'B']`|
|**next(value)**|Sends data into generator|`g.next(100)`|
|**return(value)**|Ends generator early|`g.return("stop")`|

---

## ✅ Key Takeaways

- Iterators let you manually control how data is accessed.
    
- Generators make iterator creation much simpler.
    
- `yield` pauses execution, and `next()` resumes it.
    
- You can pass data both ways (to and from generators).
    
- Perfect for **streams, async control flow, and custom iteration logic.**
    
