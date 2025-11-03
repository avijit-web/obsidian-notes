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



## Remove nth from head


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

Would you like me to add a **small pointer diagram** (with arrows and labels for `slow`, `fast`, and `n`) to visualize how the gap between pointers works? It’s really helpful for remembering this one later in Obsidian.