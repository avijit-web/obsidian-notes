---
tags:
  - javascript
  - dsa
  - programming
---

# Palindrome Linked List Check

## Overview

This function checks if a singly linked list forms a palindrome by comparing values from the beginning and end.

## Algorithm Explanation

The function builds two strings:

- `string1`: Values read from **start to end** (forward)
- `string2`: Values read from **end to start** (backward, built during traversal)

Then it compares if `string1 === string2` to determine if it's a palindrome.

## Code with Detailed Comments

```javascript
var isPalindrome = function(head) {
    let string1 = string2 = "";  // Initialize both strings as empty
    let node = head;              // Start at the head of the list

    while(node !== null) {
        // Build string1: append current value to the END (forward reading)
        string1 = `${string1}${node.val}`
        
        // Build string2: prepend current value to the START (backward reading)
        string2 = `${node.val}${string2}`
        
        // Move to next node
        node = node.next
    }

    // Compare: if both strings are equal, it's a palindrome
    return string1 === string2
}
```

## Example Walkthrough

### Input: `1 → 2 → 2 → 1`

|Iteration|node.val|string1|string2|node.next|
|---|---|---|---|---|
|Initial|-|""|""|1→2→2→1|
|1|1|"1"|"1"|2→2→1|
|2|2|"12"|"21"|2→1|
|3|2|"122"|"221"|1|
|4|1|"1221"|"1221"|null|

**Result**: `"1221" === "1221"` → `true` ✅ (palindrome)

### Input: `1 → 2 → 3`

|Iteration|node.val|string1|string2|node.next|
|---|---|---|---|---|
|Initial|-|""|""|1→2→3|
|1|1|"1"|"1"|2→3|
|2|2|"12"|"21"|3|
|3|3|"123"|"321"|null|

**Result**: `"123" === "321"` → `false` ❌ (not a palindrome)

## Key Insights

- **string1 building**: `string1 + node.val` → appends to end (maintains forward order)
- **string2 building**: `node.val + string2` → prepends to start (reverses order)
- **Time Complexity**: O(n) - single traversal
- **Space Complexity**: O(n) - storing string representations

## Why This Works

By the end of traversal:

- `string1` contains values in **original order**
- `string2` contains values in **reversed order**
- If they match → the list reads the same forwards and backwards → palindrome!


# Reverse a Linked List


---

```javascript
function reverseLinkedList(head){
    let prev = null;
    let current = head;
    while(current!==null){
        const nextNode = current.next;
        current.next = prev;
        prev = current
        current = nextNode
    }
    return prev
}
```

---

## Step-by-Step Example

Imagine a linked list:

```
head → A → B → C → null
```

### **Iteration 1**

```javascript
current = A
prev = null
```

- `nextNode = current.next` → `nextNode` now points to **B**
    
- `current.next = prev` → the link from **A** now points to **null**
    
- `prev = current` → move `prev` to **A**
    
- `current = nextNode` → move `current` to **B**
    

📊 **Current state:**

- Reversed part: **A → null**
    
- Remaining list: **B → C → null**
    

---

### **Iteration 2**

```javascript
current = B
prev = A
```

- `nextNode = current.next` → `nextNode` now points to **C**
    
- `current.next = prev` → the link from **B** now points to **A**
    
- `prev = current` → move `prev` to **B**
    
- `current = nextNode` → move `current` to **C**
    

📊 **Current state:**

- Reversed part: **B → A → null**
    
- Remaining list: **C → null**
    

---

### **Iteration 3**

```javascript
current = C
prev = B
```

- `nextNode = current.next` → `nextNode` now points to **null**
    
- `current.next = prev` → the link from **C** now points to **B**
    
- `prev = current` → move `prev` to **C**
    
- `current = nextNode` → move `current` to **null**
    

📊 **Current state:**

- Reversed part: **C → B → A → null**
    

---

### **Loop Ends**

The loop stops because `current` is now `null`.

Return `prev`, which points to **C**, the new head of the reversed list.

✅ **Final list:**

```
head → C → B → A → null
```

---

## Pseudocode

```
FUNCTION reverseLinkedList(head):
    prev ← null
    current ← head
    WHILE current ≠ null DO
        nextNode ← current.next
        current.next ← prev
        prev ← current
        current ← nextNode
    END WHILE
    RETURN prev
END FUNCTION
```

---

## Visual Trace

```
Before reversal:
head → A → B → C → null

Step 1:
A → null
B → C → null

Step 2:
B → A → null
C → null

Step 3:
C → B → A → null

After reversal:
head → C → B → A → null
```

---

## Why This Works

- `current` keeps track of the node we’re currently processing.
    
- `nextNode` saves the next node temporarily, so we don’t lose access to it.
    
- `current.next = prev` flips the link direction.
    
- `prev` builds up the reversed list one node at a time.
    
- When `current` becomes `null`, the entire list is reversed and `prev` points to the new head.
    

---

## Complexity

|Type|Explanation|
|---|---|
|**Time Complexity**|O(n) — each node is visited once|
|**Space Complexity**|O(1) — done in place, no extra memory used|

---

## Summary

- Always store `nextNode` before reversing the link.
    
- `prev`, `current`, and `nextNode` work together to safely reverse the list.
    
- The function finally returns `prev`, which becomes the **new head** of the reversed linked list.
    

---

Would you like me to add a short **diagram with arrows and pointer labels (`prev`, `current`, `nextNode`)** that fits nicely inside Obsidian’s code block style? It makes the pointer movement super easy to visualize.



## Delete a node


---

```javascript
function deleteNode(node){
    node.val = node.next.val;
    node.next = node.next.next
}
```

---

## 🧠 What This Function Does

This function **deletes a node** from a **singly linked list**,  
but it does it **without access to the head of the list**.

You’re only given a **reference to the node that needs to be deleted**.

---

## ⚙️ Key Idea

Normally, to delete a node in a linked list,  
you need the **previous node** to change its `.next` pointer.

But here, since we don’t have the previous node,  
we use a **trick**:

👉 We **copy the next node’s data** into the current node,  
and then **skip over** the next node.

So effectively, the current node “becomes” the next node.

---

## 🔍 Step-by-Step Example

Let’s take this linked list:

```
A → B → C → D → null
```

Suppose we want to **delete node B**,  
and we are given a direct reference to **B** (not to A).

---

### **Before:**

```
node = B
node.val = 'B'
node.next = C
```

---

### **Step 1:**

```javascript
node.val = node.next.val
```

- Copies value `'C'` (from next node) into `'B'`
    
- Now node B’s value becomes `'C'`
    

📊 **Current values:**

```
A → C → C → D → null
```

(Temporarily, two nodes hold the same value `'C'`)

---

### **Step 2:**

```javascript
node.next = node.next.next
```

- This makes the current node skip over the original `next` node.
    
- So now the first `'C'` links directly to `'D'`.
    

📊 **After operation:**

```
A → C → D → null
```

The original node that contained `'C'` (after B) is no longer referenced,  
so it’s effectively **deleted** from memory.

---

## ✅ Final State

```
A → C → D → null
```

The list now looks like **node B was deleted**,  
even though we never touched node A.

---

## 🧩 Pseudocode

```
FUNCTION deleteNode(node):
    node.val ← node.next.val
    node.next ← node.next.next
END FUNCTION
```

---

## 🪄 Why This Works

- The node to delete is **replaced** by its next node’s value.
    
- The `.next` pointer is adjusted to **skip the next node**,  
    effectively removing it from the chain.
    
- No need for the previous node reference.
    

🧩 Think of it as:

> “Copy the next node over the current one, then erase the next node.”

---

## ⚠️ Important Note

- This only works **if the node to delete is not the last node**  
    (because the last node has no `next` to copy from).
    
- If you try this on the last node, it will cause an error  
    (since `node.next` would be `null`).
    

---

## ⏱️ Complexity

|Type|Description|
|---|---|
|**Time Complexity**|O(1) — constant time, no traversal|
|**Space Complexity**|O(1) — only modifies existing pointers|

---

## 🧾 Summary

- `node.val = node.next.val` → copy data from next node
    
- `node.next = node.next.next` → bypass the next node
    
- Works instantly, without needing the head of the list
    
- Can’t be used on the last node
    

---

Would you like me to include a **small ASCII visual** showing how the links shift step-by-step (arrows moving) for this one too? It fits nicely in Obsidian and helps visualize the “copy & skip” trick.



# Remove nth from head


---

```javascript
function removeNthFromHead(head,n){
    let slow = head;
    let fast = head;
    for(let i=0;i<n;i++){
        fast = fast.next
    }
    if(!fast){
        return head.next
    }
    while(fast.next){
        fast = fast.next
        slow = slow.next
    }
    slow.next = slow.next.next
    return head
    
}
```

---

## 🧠 What This Function Does

This function removes the **n-th node from the end** of a **singly linked list**  
in **one pass** using the **two-pointer technique**.

It’s a common problem often phrased as:

> “Remove the n-th node from the end of the list in one traversal.”

---

## 🧩 Key Idea

We use **two pointers** — `fast` and `slow`.

- `fast` moves **n steps ahead** first.
    
- Then both move **together** until `fast` reaches the end.
    
- At that point, `slow` will be **just before** the node we want to remove.
    

This way, we don’t need to count the total length of the list.

---

## 🔍 Step-by-Step Example

Let’s take this linked list:

```
head → A → B → C → D → E → null
```

and suppose we want to remove the **2nd node from the end** (`D`).

So:  
`n = 2`

---

### **Initial Setup**

```
slow = head (A)
fast = head (A)
```

---

### **Step 1: Move `fast` n steps ahead**

We move `fast` two steps ahead.

|Step|fast moves to|
|---|---|
|1|B|
|2|C|

Now:

```
slow → A
fast → C
```

---

### **Step 2: Move both pointers until `fast` reaches the last node**

We move both pointers **one step at a time**:

|Move|fast points to|slow points to|
|---|---|---|
|1|D|B|
|2|E|C|

Now, `fast` is at the **last node (E)**.  
`slow` is right **before** the node we want to delete (`D`).

---

### **Step 3: Remove the target node**

```javascript
slow.next = slow.next.next
```

- `slow.next` was pointing to `D`
    
- `slow.next.next` points to `E`
    

Now `C` will directly link to `E`.

---

### ✅ **Final State**

```
head → A → B → C → E → null
```

Node `D` has been skipped and effectively removed.

---

## ⚠️ Special Case — When Removing the Head

If `n` equals the **length of the list**,  
then after the first loop:

```javascript
fast = null
```

That means we must delete the head itself.

So this line handles that:

```javascript
if (!fast) {
    return head.next;
}
```

Example:

```
head → A → B → C → null
n = 3
```

After moving `fast` 3 steps, `fast` becomes `null`.  
We return `head.next`, effectively deleting `A`.

---

## 🧾 Pseudocode

```
FUNCTION removeNthFromHead(head, n):
    slow ← head
    fast ← head

    FOR i FROM 0 TO n-1:
        fast ← fast.next

    IF fast IS null:
        RETURN head.next

    WHILE fast.next IS NOT null:
        fast ← fast.next
        slow ← slow.next

    slow.next ← slow.next.next
    RETURN head
END FUNCTION
```

---

## 🔍 Visual Trace

```
Before:
A → B → C → D → E → null
n = 2

Step 1: fast moves 2 ahead → (fast at C)
Step 2: move both until fast.next = null
         fast at E, slow at C
Step 3: slow.next = slow.next.next
         C → E

After:
A → B → C → E → null
```

---

## 🧩 Why This Works

- `fast` gets a head start of `n` nodes.
    
- When `fast` reaches the end, `slow` is just before the node to delete.
    
- This eliminates the need to count total nodes.
    
- Works in a **single traversal (O(n))**.
    

---

## ⏱️ Complexity

|Type|Explanation|
|---|---|
|**Time Complexity**|O(n) — only one pass through the list|
|**Space Complexity**|O(1) — uses constant extra space|

---

## 🧾 Summary

- Use two pointers (`fast`, `slow`)
    
- Move `fast` ahead by `n` nodes
    
- Move both together until `fast` reaches the end
    
- Remove `slow.next`
    
- Handles edge case when head must be removed
    

✅ Efficient, clean, and works in **one pass**!

---




# Add Two Numbers - Linked List Algorithm

## Problem Statement

Add two non-negative integers represented as linked lists where digits are stored in **reverse order**. Each node contains a single digit.

**Example:**

- Input: `2 → 4 → 3` (represents 342) and `5 → 6 → 4` (represents 465)
- Output: `7 → 0 → 8` (represents 807)
- Calculation: 342 + 465 = 807

---

## Complete Code

```javascript
function addTwoNumbers(l1, l2) {
    var dummy = new ListNode(0);
    var current = dummy;
    var carry = 0;
    
    while(l1 != null || l2 != null || carry > 0) {
        var val1 = l1 ? l1.val : 0;
        var val2 = l2 ? l2.val : 0;
        
        var sum = val1 + val2 + carry;
        carry = Math.floor(sum / 10);
        sum %= 10;
        
        current.next = new ListNode(sum);
        current = current.next;
        
        if(l1) l1 = l1.next;
        if(l2) l2 = l2.next;
    }
    
    return dummy.next;
}
```


---

## Algorithm Steps

### Step 1: Initialize Variables

```javascript
dummy = new ListNode(0)
current = dummy
carry = 0
```

### Step 2: Loop Condition

Continue while **any** of these is true:

- `l1 != null` (first list has more digits)
- `l2 != null` (second list has more digits)
- `carry > 0` (there's a carry to process)

### Step 3: Extract Values

```javascript
val1 = l1 ? l1.val : 0
val2 = l2 ? l2.val : 0
```

If a list has ended, treat its value as 0.

### Step 4: Calculate Sum and Carry

```javascript
sum = val1 + val2 + carry
carry = Math.floor(sum / 10)
sum %= 10
```

- Add the two digits plus any carry
- Extract new carry (integer division by 10)
- Keep only last digit of sum (modulo 10)

### Step 5: Create New Node

```javascript
current.next = new ListNode(sum)
current = current.next
```

- First line: Create a new node with the digit and attach it
- Second line: Move current pointer to this new node

### Step 6: Move Input Pointers

```javascript
if(l1) l1 = l1.next
if(l2) l2 = l2.next
```

Move to next nodes in input lists if they exist.

### Step 7: Return Result

```javascript
return dummy.next
```

Skip the dummy node and return the actual result list.

---

## Detailed Walkthrough: 342 + 465 = 807

### Initial State

```
l1: 2 → 4 → 3
l2: 5 → 6 → 4
dummy: [0]
current: points to dummy
carry: 0
```

---

### Iteration 1

**Extract values:**

```
val1 = 2 (from l1)
val2 = 5 (from l2)
```

**Calculate:**

```
sum = 2 + 5 + 0 = 7
carry = floor(7/10) = 0
sum = 7 % 10 = 7
```

**Create node:**

```
current.next = new ListNode(7)
```

Structure now: `dummy → [7]`

**Move current:**

```
current = current.next
```

Current now points to the node containing 7

**Move input pointers:**

```
l1 = l1.next  (now points to 4)
l2 = l2.next  (now points to 6)
```

**State after iteration 1:**

- List: `dummy → 7`
- carry: 0
- l1: `4 → 3`
- l2: `6 → 4`

---

### Iteration 2

**Extract values:**

```
val1 = 4
val2 = 6
```

**Calculate:**

```
sum = 4 + 6 + 0 = 10
carry = floor(10/10) = 1  (carry generated!)
sum = 10 % 10 = 0
```

**Create node:**

```
current.next = new ListNode(0)
```

Structure now: `dummy → 7 → [0]`

**Move current:**

```
current = current.next
```

Current now points to the node containing 0

**Move input pointers:**

```
l1 = l1.next  (now points to 3)
l2 = l2.next  (now points to 4)
```

**State after iteration 2:**

- List: `dummy → 7 → 0`
- carry: 1
- l1: `3`
- l2: `4`

---

### Iteration 3

**Extract values:**

```
val1 = 3
val2 = 4
```

**Calculate:**

```
sum = 3 + 4 + 1 = 8  (includes carry from previous iteration)
carry = floor(8/10) = 0
sum = 8 % 10 = 8
```

**Create node:**

```
current.next = new ListNode(8)
```

Structure now: `dummy → 7 → 0 → [8]`

**Move current:**

```
current = current.next
```

Current now points to the node containing 8

**Move input pointers:**

```
l1 = l1.next  (now null)
l2 = l2.next  (now null)
```

**State after iteration 3:**

- List: `dummy → 7 → 0 → 8`
- carry: 0
- l1: `null`
- l2: `null`

---

### Loop Termination

Check condition: `l1 != null || l2 != null || carry > 0`

- l1 is null ✗
- l2 is null ✗
- carry is 0 ✗

All conditions are false, so loop ends.

**Return:**

```
return dummy.next
```

Returns: `7 → 0 → 8` (skips the dummy node)

---

## Key Concepts

### Why Dummy Node?

Without dummy node, you need special logic for the first node:

```javascript
// Without dummy (complex)
if (result == null) {
    result = new ListNode(sum);
    current = result;
} else {
    current.next = new ListNode(sum);
    current = current.next;
}

// With dummy (simple)
current.next = new ListNode(sum);
current = current.next;
```

### Pointer Movement Explained

```javascript
current.next = new ListNode(sum);  // Line 1
current = current.next;             // Line 2
```

**Line 1:** Creates a new node and attaches it to the current node's next pointer **Line 2:** Moves the current pointer to the newly created node

**Why both lines are needed:**

- Without line 1: No new node is created
- Without line 2: current stays at the same position, and next iteration would overwrite the same next pointer

**Visual representation:**

```
After line 1:
dummy → [7]
  ↑
current (still pointing to dummy)

After line 2:
dummy → [7]
        ↑
      current (moved to node with 7)
```

### Handling Different Length Lists

**Example: 99 + 1 = 100**

```
l1: 9 → 9
l2: 1

Iteration 1: val1=9, val2=1, sum=10, carry=1, digit=0
Iteration 2: val1=9, val2=0, sum=10, carry=1, digit=0
Iteration 3: val1=0, val2=0, sum=1, carry=0, digit=1

Result: 0 → 0 → 1 (represents 100)
```

The ternary operator `l1 ? l1.val : 0` handles this by using 0 when a list has ended.

### Carry Propagation

**Example: 999 + 1 = 1000**

```
Iteration 1: 9+1+0=10, carry=1, digit=0
Iteration 2: 9+0+1=10, carry=1, digit=0
Iteration 3: 9+0+1=10, carry=1, digit=0
Iteration 4: 0+0+1=1, carry=0, digit=1

Result: 0 → 0 → 0 → 1
```

The loop continues as long as carry exists, even when both lists are exhausted.

---

## Time and Space Complexity

**Time Complexity:** O(max(m, n))

- m is length of first list
- n is length of second list
- We iterate through the longer list once

**Space Complexity:** O(max(m, n))

- Result list length is at most max(m, n) + 1
- No extra data structures used

---

## Common Mistakes to Avoid

1. **Forgetting to move current pointer**
    - Results in only one node being created
2. **Not checking carry after both lists end**
    - Loop must continue if carry > 0
3. **Not handling different length lists**
    - Must use 0 for exhausted lists
4. **Returning dummy instead of dummy.next**
    - dummy is just a placeholder, skip it
5. **Not moving l1 and l2 pointers**
    - Would process same digits repeatedly

---

## Summary

This algorithm simulates manual addition:

1. Start from the least significant digit (rightmost)
2. Add corresponding digits plus any carry
3. Store the result digit (sum % 10)
4. Calculate new carry (sum / 10)
5. Move to next digits
6. Continue until all digits processed and no carry remains
7. Return the result list

The linked list structure stores each digit in a separate node, and we build the result by creating nodes one by one from least to most significant digit.