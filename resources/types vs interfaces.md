---
tags:
  - typescript
  - red
aliases: 
cssclasses:
  - interface-red
---
# TypeScript: Types vs Interfaces - Complete Guide

## Overview

Both `type` and `interface` are ways to define the shape of data in TypeScript, but they have different capabilities and use cases.

## Basic Syntax

### Interface

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}
```

### Type

```typescript
type User = {
  id: number;
  name: string;
  email: string;
}
```

## Key Differences

### 1. Declaration Merging

**Interfaces** support declaration merging - you can declare the same interface multiple times and TypeScript will merge them:

```typescript
interface User {
  id: number;
  name: string;
}

interface User {
  email: string;
}

// Result: User now has id, name, AND email
const user: User = {
  id: 1,
  name: "John",
  email: "john@example.com"
}
```

**Types** cannot be merged - you'll get an error:

```typescript
type User = {
  id: number;
  name: string;
}

// ❌ Error: Duplicate identifier 'User'
type User = {
  email: string;
}
```

### 2. Extending/Inheritance

**Interfaces** use `extends` keyword:

```typescript
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const myDog: Dog = {
  name: "Buddy",
  breed: "Golden Retriever"
}
```

**Types** use intersection (`&`):

```typescript
type Animal = {
  name: string;
}

type Dog = Animal & {
  breed: string;
}

const myDog: Dog = {
  name: "Buddy",
  breed: "Golden Retriever"
}
```

### 3. Union Types

**Types** can represent union types:

```typescript
type Status = "pending" | "approved" | "rejected";

type ID = string | number;

type Response = {
  data: string;
} | {
  error: string;
}
```

**Interfaces** cannot represent union types directly:

```typescript
// ❌ This doesn't work
interface Status = "pending" | "approved" | "rejected";
```

### 4. Primitive Types

**Types** can alias primitive types:

```typescript
type ID = string;
type Count = number;
type IsActive = boolean;
```

**Interfaces** cannot alias primitives:

```typescript
// ❌ This doesn't work
interface ID = string;
```

### 5. Computed Properties

**Types** support computed properties:

```typescript
type EventHandlers = {
  [K in "click" | "focus" | "blur"]: (event: Event) => void;
}

// Results in:
// {
//   click: (event: Event) => void;
//   focus: (event: Event) => void;
//   blur: (event: Event) => void;
// }
```

**Interfaces** have limited support for computed properties:

```typescript
interface EventHandlers {
  [key: string]: (event: Event) => void;
}
```

## Advanced Examples

### Complex Type Definitions

```typescript
// Union of object types
type ApiResponse<T> = {
  success: true;
  data: T;
} | {
  success: false;
  error: string;
}

// Conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

// Template literal types
type EventName = `on${Capitalize<string>}`;
```

### Interface with Methods

```typescript
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
  readonly result: number;
}

class BasicCalculator implements Calculator {
  result: number = 0;
  
  add(a: number, b: number): number {
    this.result = a + b;
    return this.result;
  }
  
  subtract(a: number, b: number): number {
    this.result = a - b;
    return this.result;
  }
}
```

### Type with Function Signatures

```typescript
type EventHandler = {
  (event: MouseEvent): void;
  (event: KeyboardEvent): void;
}

type DatabaseConnection = {
  connect(): Promise<void>;
  disconnect(): Promise<void>;
  query<T>(sql: string): Promise<T[]>;
}
```

## When to Use Which?

### Use **Interfaces** when:

- Defining object shapes that might be extended by others
- Working with classes (interfaces work better with `implements`)
- You want declaration merging capability
- Building public APIs that others will extend
- Working in object-oriented patterns

```typescript
// Good for public APIs
interface DatabaseDriver {
  connect(): Promise<void>;
  query(sql: string): Promise<any[]>;
}

// Others can extend this
interface PostgresDriver extends DatabaseDriver {
  getVersion(): string;
}
```

### Use **Types** when:

- Creating union types
- Aliasing primitive types
- Complex type transformations
- One-time type definitions
- Working with utility types

```typescript
// Complex type manipulations
type Partial<T> = {
  [P in keyof T]?: T[P];
}

type UserStatus = "active" | "inactive" | "pending";

type ApiEndpoint = `/api/${"users" | "posts" | "comments"}`;
```

## Performance Considerations

- **Interfaces** are generally faster for the TypeScript compiler to process
- **Types** with complex computations can slow down compilation
- For simple object shapes, performance difference is negligible

## Best Practices

1. **Consistency**: Pick one approach for your project and stick with it
2. **Start with interfaces** for object shapes, switch to types when you need union types or complex transformations
3. **Use interfaces for public APIs** that others might extend
4. **Use types for internal helper types** and complex type manipulations

## Quick Reference

|Feature|Interface|Type|
|---|---|---|
|Object shapes|✅|✅|
|Declaration merging|✅|❌|
|Extends/implements|✅|✅ (with &)|
|Union types|❌|✅|
|Primitive aliases|❌|✅|
|Computed properties|Limited|✅|
|Complex transformations|❌|✅|
|Performance|Better|Good|

## Conclusion

Both types and interfaces are powerful tools in TypeScript. Choose based on your specific needs:

- **Interfaces** for extensible object contracts
- **Types** for flexible type definitions and unions

The choice often comes down to team preferences and specific use cases rather than strict rules.