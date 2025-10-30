---
tags:
  - typescript
  - react
  - design-patterns
  - localStorage
---

# useLocalStorage Hook - TypeScript Deep Dive

A complete guide to understanding the `useLocalStorage` custom React hook with TypeScript type explanations.

---

## Full Code

```typescript
import { useEffect, useState } from "react"

export function useLocalStorage<T>(key: string, initialValue: T | (() => T)) {
  const [value, setValue] = useState<T>(() => {
    const jsonValue = localStorage.getItem(key)
    if (jsonValue != null) return JSON.parse(jsonValue)
    if (typeof initialValue === "function") {
      return (initialValue as () => T)()
    } else {
      return initialValue
    }
  })
  
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])
  
  return [value, setValue] as [typeof value, typeof setValue]
}
```

---

## Step-by-Step Breakdown

### 1. Function Declaration

```typescript
export function useLocalStorage<T>(key: string, initialValue: T | (() => T))
```

**Type Parameters:**

- `<T>` - Generic type parameter (placeholder for any type)
    - Example: `useLocalStorage<string>()` → T becomes `string`
    - Example: `useLocalStorage<number>()` → T becomes `number`

**Parameters:**

- `key: string` - The localStorage key (e.g., "username", "theme")
- `initialValue: T | (() => T)` - Either:
    - Direct value of type T: `"hello"`
    - Function returning type T: `() => "hello"`

**Why allow functions?** To avoid expensive calculations on every render. The function only runs once during initialization.

---

### 2. useState Hook

```typescript
const [value, setValue] = useState<T>(() => {
```

**Types:**

- `value` → `T`
- `setValue` → `Dispatch<SetStateAction<T>>`

**Return type:** `[T, Dispatch<SetStateAction<T>>]`

The arrow function `() => { }` is the **lazy initializer** - it only runs once when the component mounts.

---

### 3. Getting Stored Value

```typescript
const jsonValue = localStorage.getItem(key)
```

**Type:** `string | null`

- Returns the stored string if exists
- Returns `null` if key doesn't exist

---

### 4. Parsing Stored Value

```typescript
if (jsonValue != null) return JSON.parse(jsonValue)
```

**Type Flow:**

- `JSON.parse(jsonValue)` → returns `any`
- TypeScript trusts this is type `T` because we control what we store

**Note:** `!=` checks for both `null` and `undefined`

---

### 5. Handling Function initialValue

```typescript
if (typeof initialValue === "function") {
  return (initialValue as () => T)()
}
```

**Type assertion:** `(initialValue as () => T)`

- Tells TypeScript: "This is a function that returns T"
- The final `()` calls that function
- **Returns:** `T`

**Why the assertion?** TypeScript can't narrow the union type `T | (() => T)` automatically when T itself might be a function type.

---

### 6. Handling Direct Value

```typescript
else {
  return initialValue
}
```

**Returns:** `T` (the direct value)

---

### 7. Syncing to localStorage

```typescript
useEffect(() => {
  localStorage.setItem(key, JSON.stringify(value))
}, [key, value])
```

**Type Flow:**

- `JSON.stringify(value)` → converts `T` to `string`
- `setItem(string, string)` → returns `void`

**Dependencies:** Runs whenever `key` or `value` changes

---

### 8. Return Statement

```typescript
return [value, setValue] as [typeof value, typeof setValue]
```

**Without type assertion:**

- Type would be: `(T | Dispatch<SetStateAction<T>>)[]`
- Each element could be EITHER value OR setValue (not useful!)

**With type assertion:**

- Type becomes: `[T, Dispatch<SetStateAction<T>>]`
- A **tuple** with exact types at each position

**Tuple vs Array:**

- Array: `string[]` - any number of strings
- Tuple: `[string, number]` - exactly 2 elements with specific types

---

## Type Reference Table

|Element|Type|Description|
|---|---|---|
|`T`|Generic|Whatever type you specify|
|`key`|`string`|localStorage key|
|`initialValue`|`T \| (() => T)`|Value or function returning value|
|`jsonValue`|`string \| null`|Stored JSON string or null|
|`value`|`T`|Current state value|
|`setValue`|`Dispatch<SetStateAction<T>>`|State setter function|
|**Return**|`[T, Dispatch<SetStateAction<T>>]`|Tuple of value and setter|

---

## Usage Examples

### Example 1: String Value

```typescript
const [username, setUsername] = useLocalStorage<string>("user", "Guest")
//     ^string                                                   ^string

setUsername("Alice")  // ✅ Works
setUsername(42)       // ❌ Error: number not assignable to string
```

### Example 2: Object Value

```typescript
interface User {
  name: string
  age: number
}

const [user, setUser] = useLocalStorage<User>("currentUser", {
  name: "Anonymous",
  age: 0
})

setUser({ name: "Bob", age: 25 })  // ✅ Works
setUser({ name: "Bob" })           // ❌ Error: missing 'age'
```

### Example 3: Function Initializer

```typescript
// Expensive calculation only runs once
const [data, setData] = useLocalStorage<number[]>(
  "scores",
  () => Array.from({ length: 1000 }, (_, i) => i * 2)
)
```

### Example 4: Updater Function

```typescript
const [count, setCount] = useLocalStorage<number>("counter", 0)

// Using updater function (like regular useState)
setCount(prev => prev + 1)  // ✅ Works
setCount(count + 1)          // ✅ Also works
```

---

## Key TypeScript Concepts

### 1. Generics (`<T>`)

Allow writing reusable code that works with any type while maintaining type safety.

```typescript
// Without generics - only works with strings
function useStringStorage(key: string, initial: string) { ... }

// With generics - works with any type
function useLocalStorage<T>(key: string, initial: T) { ... }
```

### 2. Union Types (`A | B`)

A value can be one type OR another.

```typescript
string | number  // Can be string OR number
T | (() => T)    // Can be T OR a function returning T
```

### 3. Type Assertions (`as Type`)

Tell TypeScript to trust you about a type.

```typescript
const value = something as string  // "Trust me, this is a string"
```

### 4. `typeof` Operator

Gets the TypeScript type of a variable.

```typescript
const name = "Alice"
type NameType = typeof name  // type is "string"
```

### 5. Tuples

Fixed-length arrays with specific types at each position.

```typescript
[string, number]           // Exactly 2 elements
[string, number, boolean]  // Exactly 3 elements
```

---

## Common Pitfalls

### ❌ Problem: Type mismatch

```typescript
const [age, setAge] = useLocalStorage<number>("age", "25")
// Error: string not assignable to number
```

**✅ Solution:** Match types

```typescript
const [age, setAge] = useLocalStorage<number>("age", 25)
```

---

### ❌ Problem: Complex object not updating

```typescript
const [user, setUser] = useLocalStorage<User>("user", defaultUser)
user.name = "Bob"  // Doesn't trigger localStorage update!
```

**✅ Solution:** Create new object

```typescript
setUser({ ...user, name: "Bob" })  // Triggers update
```

---

## When to Use This Hook

✅ **Good use cases:**

- User preferences (theme, language)
- Form data persistence
- Shopping cart
- Authentication tokens (with caution)
- UI state that should survive refresh

❌ **Avoid for:**

- Sensitive data (use secure storage)
- Large datasets (use IndexedDB instead)
- Data that changes frequently (performance issues)
- Server-synced data (use proper state management)

---

## Additional Resources

- [React Hooks Documentation](https://react.dev/reference/react)
- [TypeScript Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [localStorage MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)

---

**Tags:** #react #typescript #hooks #localStorage #webdev

**Last Updated:** October 2025