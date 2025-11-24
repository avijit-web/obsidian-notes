---
tags:
  - react
  - frontend
---
React renders your application in two main steps:

1. **Render Phase**: React figures out what needs to change.
    
    - It starts at the top of your component tree and goes down.
    - It converts your JSX code into **React elements**, which are like simple JavaScript instructions for building the UI. 
    - For re-renders, it finds components that need updates (e.g., from `useState`). 
    - It compares the new React elements with the old ones (this is called **reconciliation** or comparing the **virtual DOM**). 
    - It creates a list of only the necessary changes. 
2. **Commit Phase**: React actually updates the browser.
    
    - The changes determined in the Render Phase are applied to the **actual DOM** (what you see on the screen). 
    - Important: Rendering (Render Phase) doesn't always mean the screen changes. If the new React elements are the same as before, no changes are made to the actual DOM. 

![[Pasted image 20251121184306.png]]

![[Pasted image 20251121184327.png]]
## Render and usestate

- Calling the setter function from `useState` causes the component to re-render (4:47).
- In development mode, React's Strict Mode (enabled by Create React App) intentionally double-invokes function components, leading to two initial renders (3:43).
- When updating state to the same value as the current state:
    - If it's the **initial render** and you set the state to the same value, the component will **not re-render** (7:55).
    - If the component has **already re-rendered** and you then set the state to the same value, it will re-render **one more time** before bailing out from subsequent renders (8:44).
- React uses the `Object.is` comparison algorithm to check if the previous and current state values are the same (12:17).

## usereducer and render


- **Default Behavior**: Anytime you **dispatch an action** using `useReducer`, the component will **re-render** (3:42, 4:20).
- **Exception: Updating to the Same Value**:
    - If you dispatch an action that sets the state to the **same value as the initial state** (after the initial render), the component **will not re-render** 
    - If the component has **already re-rendered at least once**, and you then dispatch an action that sets the state to the **same value**, React will **re-render the component one more time** before bailing out of any _subsequent_ renders for that same value 