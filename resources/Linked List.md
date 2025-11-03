---
tags:
  - javascript
  - programming
  - dsa
---
# JavaScript Linked List - Step by Step Guide with Examples

## What is a Linked List?

Imagine a treasure hunt where each clue points to the next location. That's a linked list! Each "node" (location) has:

- Some data (the treasure)
- A pointer to the next node (the next clue)

```javascript
// Visual representation:
// head -> [1|next] -> [2|next] -> [3|null]
//         node1       node2       node3
```

## The Two Classes

### Node Class

```javascript
class Node {
    constructor(data) {
        this.data = data;  // The actual value
        this.next = null;  // Points to next node (null = no next node)
    }
}

// Example:
const node1 = new Node(5);
// node1 = { data: 5, next: null }
```

### LinkedList Class

```javascript
class LinkedList {
    constructor() {
        this.head = null;  // Points to first node (null = empty list)
    }
}

// Example:
const list = new LinkedList();
// list = { head: null }  <- empty list
```

---

## Method 1: size()

**What it does:** Counts total nodes in the list

```javascript
size() {
    let count = 0;
    let current = this.head;
    while (current) {  // Loop until current becomes null
        count++;
        current = current.next;
    }
    return count;
}

// Example walkthrough:
// List: head -> [10|next] -> [20|next] -> [30|null]

// Step 1: count = 0, current = head (points to first node with data 10)
// Step 2: count = 1, current = node with data 20
// Step 3: count = 2, current = node with data 30
// Step 4: count = 3, current = null (stop!)
// Return: 3

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(30);
console.log(list.size());  // Output: 3
```

**Common Bug:**

```javascript
// WRONG:
while (current.next) {  // Stops one node early!
    count++;
    current = current.next;
}
// If list has [10|next] -> [20|null]
// Loop stops at node 10, only counts 1 instead of 2

// CORRECT:
while (current) {  // Counts all nodes including last one
    count++;
    current = current.next;
}
```

---

## Method 2: addFirst(data)

**What it does:** Adds new node at the beginning

```javascript
addFirst(data) {
    const newNode = new Node(data);  // Create new node
    newNode.next = this.head;        // New node points to old head
    this.head = newNode;             // Head now points to new node
}

// Example walkthrough:
// Initial: head -> [20|next] -> [30|null]
// Call: list.addFirst(10)

// Step 1: Create newNode = { data: 10, next: null }
// Step 2: newNode.next = this.head
//         newNode = { data: 10, next: points to node with 20 }
// Step 3: this.head = newNode
//         Now: head -> [10|next] -> [20|next] -> [30|null]

const list = new LinkedList();
list.addFirst(30);  // head -> [30|null]
list.addFirst(20);  // head -> [20|next] -> [30|null]
list.addFirst(10);  // head -> [10|next] -> [20|next] -> [30|null]
list.print();       // Output: 10, 20, 30
```

---

## Method 3: addLast(data)

**What it does:** Adds new node at the end

```javascript
addLast(data) {
    const newNode = new Node(data);
    
    if (!this.head) {           // If list is empty
        this.head = newNode;    // New node becomes head
        return;
    }
    
    let current = this.head;
    while (current.next) {      // Walk to last node
        current = current.next;
    }
    current.next = newNode;     // Last node now points to new node
}

// Example walkthrough:
// Initial: head -> [10|next] -> [20|null]
// Call: list.addLast(30)

// Step 1: Create newNode = { data: 30, next: null }
// Step 2: this.head exists, so skip the if block
// Step 3: current = head (node with 10)
// Step 4: current.next exists (node with 20), so current = node with 20
// Step 5: current.next is null, exit loop
// Step 6: current.next = newNode
//         Now: head -> [10|next] -> [20|next] -> [30|null]

const list = new LinkedList();
list.addLast(10);   // head -> [10|null]
list.addLast(20);   // head -> [10|next] -> [20|null]
list.addLast(30);   // head -> [10|next] -> [20|next] -> [30|null]
list.print();       // Output: 10, 20, 30
```

**Empty list case:**

```javascript
const list = new LinkedList();  // head = null
list.addLast(100);              // head -> [100|null]
// Since head was null, new node becomes the head directly
```

---

## Method 4: addAt(index, data)

**What it does:** Inserts node at specific position

```javascript
addAt(index, data) {
    if (index < 0 || index > this.size()) {
        console.error("Invalid index");
        return;
    }
    
    const newNode = new Node(data);
    
    if (index === 0) {                    // Insert at beginning
        newNode.next = this.head;
        this.head = newNode;
        return;
    }
    
    let current = this.head;
    for (let i = 0; i < index - 1; i++) { // Walk to node BEFORE index
        current = current.next;
    }
    newNode.next = current.next;          // New node points to next
    current.next = newNode;               // Previous node points to new
}

// Example walkthrough:
// Initial: head -> [10|next] -> [20|next] -> [40|null]
//          index:      0            1            2
// Call: list.addAt(2, 30)  // Insert 30 at index 2

// Step 1: index = 2, size = 3, valid ✓
// Step 2: Create newNode = { data: 30, next: null }
// Step 3: index != 0, so skip that block
// Step 4: current = head (node with 10)
// Step 5: Loop i = 0, i < 1, so current = node with 20
// Step 6: Loop exits (i = 1, not < 1)
// Step 7: newNode.next = current.next (node with 40)
//         newNode = { data: 30, next: points to node with 40 }
// Step 8: current.next = newNode
//         Now: head -> [10] -> [20] -> [30] -> [40|null]

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(40);
list.addAt(2, 30);  // Insert 30 between 20 and 40
list.print();       // Output: 10, 20, 30, 40

// Insert at beginning:
list.addAt(0, 5);   // head -> [5] -> [10] -> [20] -> [30] -> [40|null]
list.print();       // Output: 5, 10, 20, 30, 40

// Insert at end:
list.addAt(5, 50);  // Same as addLast
list.print();       // Output: 5, 10, 20, 30, 40, 50
```

---

## Method 5: removeTop()

**What it does:** Removes first node

```javascript
removeTop() {
    if (!this.head) {        // If list is empty, do nothing
        return;
    }
    this.head = this.head.next;  // Head jumps to second node
}

// Example walkthrough:
// Initial: head -> [10|next] -> [20|next] -> [30|null]
// Call: list.removeTop()

// Step 1: this.head exists (not null), so continue
// Step 2: this.head = this.head.next
//         head now points to node with 20
//         Now: head -> [20|next] -> [30|null]
//         (Node with 10 is disconnected, garbage collected)

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(30);
// List: [10] -> [20] -> [30]

list.removeTop();   // Removes 10
list.print();       // Output: 20, 30

list.removeTop();   // Removes 20
list.print();       // Output: 30

list.removeTop();   // Removes 30
list.print();       // Output: (nothing, list is empty)
```

**Edge case - Empty list:**

```javascript
const list = new LinkedList();  // head = null
list.removeTop();               // Does nothing, no error
```

---

## Method 6: removeLast()

**What it does:** Removes last node

```javascript
removeLast() {
    if (!this.head) {              // Empty list
        return;
    }
    
    if (!this.head.next) {         // Only one node
        this.head = null;
        return;
    }
    
    let current = this.head;
    while (current.next.next) {    // Walk to second-to-last node
        current = current.next;
    }
    current.next = null;           // Disconnect last node
}

// Example walkthrough:
// Initial: head -> [10|next] -> [20|next] -> [30|null]
// Call: list.removeLast()

// Step 1: this.head exists ✓
// Step 2: this.head.next exists (not single node) ✓
// Step 3: current = head (node with 10)
// Step 4: current.next.next exists (node with 30), so current = node with 20
// Step 5: current.next.next is null, exit loop
//         current is now at node with 20 (second-to-last)
// Step 6: current.next = null
//         Now: head -> [10|next] -> [20|null]
//         (Node with 30 is disconnected)

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(30);
// List: [10] -> [20] -> [30]

list.removeLast();  // Removes 30
list.print();       // Output: 10, 20

list.removeLast();  // Removes 20
list.print();       // Output: 10
```

**Edge case - Single node:**

```javascript
const list = new LinkedList();
list.addFirst(100);     // head -> [100|null]
list.removeLast();      // head.next is null, so head = null
console.log(list.size()); // Output: 0
```

**Common Bug:**

```javascript
// WRONG - Crashes on single node:
let current = this.head;
while (current.next.next) {  // If only one node, current.next is null
    current = current.next;  // Trying to access null.next crashes!
}

// If list is: head -> [10|null]
// current.next is null
// null.next throws error!

// CORRECT - Check for single node first:
if (!this.head.next) {
    this.head = null;
    return;
}
```

---

## Method 7: removeAt(index)

**What it does:** Removes node at specific position

```javascript
removeAt(index) {
    if (index < 0 || index >= this.size()) {
        console.error("Invalid index");
        return;
    }
    
    if (index === 0) {              // Remove first node
        this.head = this.head.next;
        return;
    }
    
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {  // Walk to node BEFORE index
        current = current.next;
    }
    
    if (current.next) {
        current.next = current.next.next;  // Skip over target node
    }
}

// Example walkthrough:
// Initial: head -> [10|next] -> [20|next] -> [30|next] -> [40|null]
//          index:      0            1            2            3
// Call: list.removeAt(2)  // Remove node at index 2 (value 30)

// Step 1: index = 2, size = 4, 2 >= 0 and 2 < 4, valid ✓
// Step 2: index != 0, so skip that block
// Step 3: current = head (node with 10)
// Step 4: Loop i = 0, i < 1, so current = node with 20
// Step 5: Loop exits (i = 1, not < 1)
//         current is now at node BEFORE target (node with 20)
// Step 6: current.next exists ✓
// Step 7: current.next = current.next.next
//         current.next was node with 30
//         current.next.next is node with 40
//         So now node with 20 points directly to node with 40
//         Now: head -> [10] -> [20] -> [40|null]

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(30);
list.addLast(40);
// List: [10] -> [20] -> [30] -> [40]

list.removeAt(2);   // Remove 30 (index 2)
list.print();       // Output: 10, 20, 40

list.removeAt(0);   // Remove 10 (first node)
list.print();       // Output: 20, 40

list.removeAt(1);   // Remove 40 (last node, index 1 now)
list.print();       // Output: 20
```

**Visual of skipping:**

```javascript
// Before: [20] -> [30] -> [40]
//         current  target  next
//
// current.next = current.next.next means:
// [20] points to what [30] was pointing to
//
// After: [20] -----> [40]
//             (30 is skipped/disconnected)
```

---

## Method 8: print()

**What it does:** Displays all values in the list

```javascript
print() {
    let current = this.head;
    while (current) {
        console.log(current.data);
        current = current.next;
    }
}

// Example walkthrough:
// List: head -> [10|next] -> [20|next] -> [30|null]
// Call: list.print()

// Step 1: current = head (node with 10)
// Step 2: console.log(10), current = node with 20
// Step 3: console.log(20), current = node with 30
// Step 4: console.log(30), current = null
// Step 5: current is null, exit loop

const list = new LinkedList();
list.addLast(10);
list.addLast(20);
list.addLast(30);
list.print();
// Output:
// 10
// 20
// 30
```

---

## Complete Working Example

```javascript
const list = new LinkedList();

// Build list: [5] -> [10] -> [15] -> [20]
list.addLast(10);    // [10]
list.addFirst(5);    // [5] -> [10]
list.addLast(20);    // [5] -> [10] -> [20]
list.addAt(2, 15);   // [5] -> [10] -> [15] -> [20]

console.log("Initial list:");
list.print();
// Output: 5, 10, 15, 20

console.log("Size:", list.size());
// Output: Size: 4

// Remove operations
list.removeTop();    // Remove 5: [10] -> [15] -> [20]
console.log("\nAfter removeTop:");
list.print();
// Output: 10, 15, 20

list.removeLast();   // Remove 20: [10] -> [15]
console.log("\nAfter removeLast:");
list.print();
// Output: 10, 15

list.removeAt(1);    // Remove 15 (index 1): [10]
console.log("\nAfter removeAt(1):");
list.print();
// Output: 10

console.log("Final size:", list.size());
// Output: Final size: 1
```

---

## Common Mistakes & Fixes

### 1. Counting nodes incorrectly

```javascript
// WRONG:
while (current.next) {  // Misses last node
    count++;
    current = current.next;
}
// For [10] -> [20], this returns 1 instead of 2

// CORRECT:
while (current) {  // Counts all nodes
    count++;
    current = current.next;
}
```

### 2. Forgetting single-node case in removeLast

```javascript
// WRONG:
while (current.next.next) {  // Crashes if only one node!
    current = current.next;
}

// If list is [10|null]:
// current = node with 10
// current.next = null
// null.next throws error!

// CORRECT:
if (!this.head.next) {  // Check for single node first
    this.head = null;
    return;
}
```

### 3. Wrong index validation

```javascript
// For removeAt:
// WRONG: if (index > this.size())
// Can't remove at index = size, that's beyond last node

// CORRECT: if (index >= this.size())
// Valid indices: 0 to size-1
```

---

## Time Complexity Summary

|Operation|Time|Why|
|---|---|---|
|addFirst|O(1)|Just change head pointer|
|addLast|O(n)|Walk entire list to find end|
|addAt|O(n)|Walk to index position|
|removeTop|O(1)|Just change head pointer|
|removeLast|O(n)|Walk to second-to-last node|
|removeAt|O(n)|Walk to index position|
|size|O(n)|Count all nodes|
|print|O(n)|Visit all nodes|

n = number of nodes in list

---

## When to Use Linked Lists

**Use when:**

- Adding/removing from beginning frequently
- Size changes often
- Don't need random access by index

**Don't use when:**

- Need fast access to middle elements
- Working with small amounts of data
- Need to know size frequently