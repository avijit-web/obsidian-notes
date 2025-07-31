---
tags:
  - typescript
cssclasses:
  - interface-red
---
# TypeScript Strictness

## What is Strictness?

TypeScript offers different levels of strictness to match what different developers need:

- **Loose/Flexible**: Default TypeScript experience - types are optional, more forgiving
- **Strict**: More validation and checking - catches more potential issues

Think of strictness like a dial - you can turn it up or down based on your needs.

## Why Use Strict Mode?

- **Better error catching**: Finds bugs before they happen
- **Improved tooling**: Better autocomplete and IDE support
- **Long-term benefits**: More work upfront, but saves time later
- **Recommended for new projects**: Always start with strict mode when possible

## Key Strictness Flags

### The `strict` Flag

- **CLI**: Use `--strict` flag
- **Config**: Add `"strict": true` in `tsconfig.json`
- **Effect**: Turns on ALL strict checks at once
- **Tip**: You can still turn off individual checks if needed

### Important Individual Flags

#### `noImplicitAny`

**Problem**: TypeScript sometimes gives up on figuring out types and uses `any`

- Using `any` defeats the purpose of TypeScript
- Less type safety = more potential bugs

**Solution**: This flag shows errors when TypeScript can't figure out a type

```typescript
// Without noImplicitAny - no error
function greet(name) { // 'name' is implicitly 'any'
  return "Hello " + name;
}

// With noImplicitAny - shows error
function greet(name) { // Error: Parameter 'name' implicitly has an 'any' type
  return "Hello " + name;
}
```

#### `strictNullChecks`

**Problem**: By default, `null` and `undefined` can be assigned to any type

- This causes countless bugs (called "billion dollar mistake")
- Easy to forget to handle these cases

**Solution**: Makes handling `null` and `undefined` explicit

```typescript
// Without strictNullChecks - no error
let name: string = null; // This is allowed

// With strictNullChecks - shows error
let name: string = null; // Error: Type 'null' is not assignable to type 'string'
```

## Quick Setup

1. **For new projects**: Always use strict mode
2. **For existing JavaScript**: Start loose, gradually increase strictness
3. **Configuration**: Add to `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

## Remember

- More strictness = more type safety = fewer bugs
- Start strict on new projects
- Migrate existing code gradually
- The extra work upfront pays off later



# TypeScript "as const" Assertion

## What is "as const"?

Makes TypeScript treat an array or object as read-only with exact literal types instead of general types.

## Example

```typescript
// Without as const
const options = ["Click me!", "Click me again!"];
// Type: string[]

// With as const
const options = ["Click me!", "Click me again!"] as const;
// Type: readonly ["Click me!", "Click me again!"]
```

## Why use it?

- **Prevents mutations** - Array becomes read-only
- **Exact types** - Each string gets its literal type instead of just "string"
- **Better IntelliSense** - IDE knows the exact values
- **Type safety** - Helps catch errors when the exact values matter

## Common use cases

- Configuration arrays that shouldn't change
- Union types from array values
- When you need TypeScript to know the exact literal values



# TypeScript Omit Utility Type

## What is Omit?

`Omit<T, K>` creates a new type by removing specific properties from an existing type.

## Example

```typescript
type User = {
  sessionId: string;
  name: string;
};

type Guest = Omit<User, "name">;
// Guest now only has: { sessionId: string }
```

## Key Points

- Takes two parameters: the original type (T) and properties to remove (K)
- K can be a single property or union of properties: `string | number | symbol`
- Useful for creating variations of existing types
- Helps maintain type safety when you need similar but not identical types





# TypeScript Generic React Components

## What are Generic Components?

Components that can work with different types by using generic type parameters (`<T>`).

## Example

```typescript
type ButtonProps<T> = {
  countValue: T;
  countHistory: T[];
};

export default function Button<T>({
  countValue,
  countHistory,
}: ButtonProps<T>) {
  return <button>Click me!</button>;
}
```

## Key Points

- **`<T>`** is a type parameter that can be any type
- **Props type uses `<T>`** - `ButtonProps<T>` makes props generic
- **Component uses `<T>`** - `Button<T>` makes the whole component generic
- **Flexible typing** - `T` could be `number`, `string`, `boolean`, etc.

## Why use Generic Components?

- **Reusability** - Same component works with different data types
- **Type safety** - TypeScript still checks types, but they're flexible
- **Code reduction** - Don't need separate components for different types

## Common use cases

- Lists that can hold different item types
- Form components that work with various value types
- Data display components that adapt to different data shapes

# When Do You Need TypeScript Generics?

## The Problem

You want the same component/function to work with different types, but you don't know what type it will be ahead of time.

## How TypeScript Automatically Assigns Types

```typescript
// Generic function
function getValue<T>(item: T): T {
  return item;
}

// TypeScript automatically figures out the types
const numberResult = getValue(42);        // T becomes number
const stringResult = getValue("hello");   // T becomes string
const boolResult = getValue(true);        // T becomes boolean

// No need to write getValue<number>(42) - TypeScript is smart!
```

## React Component Example

```typescript
function Button<T>(props: {value: T, history: T[]}) {
  return <button>{String(props.value)}</button>;
}

// Usage - TypeScript automatically knows the types
<Button value={5} history={[1, 2, 3]} />        // T = number
<Button value="click" history={["a", "b"]} />    // T = string
<Button value={true} history={[false]} />        // T = boolean
```

## Key Point

You write the generic once, but TypeScript automatically figures out what `T` should be based on what you pass in. No extra work needed!

## Simple Rule

If you find yourself copying the same component/function just to change the types, you probably need generics.