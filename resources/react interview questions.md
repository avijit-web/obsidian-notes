---
tags:
  - frontend
  - react
  - webdev
---
## 1. What is React? Describe the benefits of React.

React is a **JavaScript library for building user interfaces**, especially single-page applications. It was developed by Meta (Facebook).

React lets us build UIs using **reusable components**, where the UI is efficiently updated when application data changes.

**Main benefits of React:**

- **Component-based:** We can break the UI into small, reusable components.
- **Virtual DOM:** React efficiently updates only the parts of the UI that need to change.
- **Reusable code:** Components can be reused across different parts of an application.
- **Declarative:** We describe what the UI should look like for a given state, and React handles updating the DOM.
- **One-way data flow:** Data generally flows from parent to child through props, making applications easier to understand.
- **Large ecosystem:** There are many libraries and tools available for routing, state management, testing, etc.
- **Strong community:** React has extensive documentation, learning resources, and community support.

**Interview-friendly short answer:**

> "React is a JavaScript library developed by Meta for building user interfaces using reusable components. Its main benefits are component reusability, declarative programming, efficient UI updates through its rendering model, one-way data flow, and a large ecosystem."


### 2. What is the difference between React Node, React Element, and a React Component?

This is a common interview question. The easiest way to understand it is:

**React Node → anything React can render**  
**React Element → description of what should be rendered**  
**React Component → reusable function/class that produces elements**

#### React Node

A **React Node** is anything that can be rendered by React.

It can be:

- React elements
    
- Strings
    
- Numbers
    
- `null`
    
- `undefined`
    
- Booleans
    
- Arrays/fragments containing renderable nodes
    

```jsx
<div>Hello</div>
```

The `<div>` element and `"Hello"` are both React nodes.

---

#### React Element

A **React Element** is an object describing what React should render.

```jsx
const element = <h1>Hello</h1>;
```

`element` is **not the actual DOM element**. It is a JavaScript object that tells React:

> "I want an `h1` containing `Hello`."

You can think of it as a **blueprint/description of UI**.

---

#### React Component

A **component** is a reusable piece of UI, usually a function.

```jsx
function Welcome() {
  return <h1>Hello</h1>;
}
```

Here, `Welcome` is a component.

When we use:

```jsx
<Welcome />
```

React creates a **React Element** representing that component, and React then uses the component to determine what UI to render.

### Simple way to remember

> **Node = something React can render**  
> **Element = description of what React should render**  
> **Component = reusable logic that returns/render elements**

### Interview-ready answer

> "A React Node is anything that React can render, such as an element, string, number, or array of nodes. A React Element is an immutable JavaScript object that describes what should appear in the UI. A React Component is a reusable function or class that produces React elements and encapsulates UI logic."



### 3. What is JSX and how does it work?

**JSX (JavaScript XML)** is a syntax extension for JavaScript that allows us to write **HTML-like code inside JavaScript** when building React components.

For example:

```jsx
const element = <h1>Hello World</h1>;
```

JSX is **not HTML** and browsers cannot understand JSX directly.

Before the code runs in the browser, a tool such as **Babel** transforms JSX into JavaScript.

Conceptually:

```jsx
<h1>Hello World</h1>
```

gets transformed into something like:

```js
React.createElement('h1', null, 'Hello World');
```

In modern React, the JSX transform can use the automatic JSX runtime rather than requiring `React.createElement` directly, but the important idea remains: **JSX is transformed into JavaScript that creates React elements.**

### Why use JSX?

- Makes UI code easier to read.
    
- Allows JavaScript logic and UI markup to be written together.
    
- Supports expressions using `{}`.
    

Example:

```jsx
const name = "John";

return <h1>Hello {name}</h1>;
```

### Interview-ready answer

> **"JSX is a syntax extension for JavaScript used by React to describe UI using HTML-like syntax. Browsers don't understand JSX directly, so it is transformed into JavaScript by tools such as Babel. JSX makes React code more readable and allows us to easily combine UI markup with JavaScript expressions."**