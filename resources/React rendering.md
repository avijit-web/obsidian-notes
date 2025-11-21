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
