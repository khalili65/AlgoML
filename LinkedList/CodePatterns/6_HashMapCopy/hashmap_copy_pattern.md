# 🔗 Pattern 6: HashMap Copy (Deep Clone)

---

## When to Use

- Clone linked list with random/arbitrary pointers
- Deep copy complex list structures
- Any problem requiring node-to-node mapping

---

## The Template

```python
def copyList(head):
    if not head:
        return None
    
    # Map: original node → copied node
    old_to_new = {}
    
    # First pass: create all nodes
    curr = head
    while curr:
        old_to_new[curr] = Node(curr.val)
        curr = curr.next
    
    # Second pass: connect pointers
    curr = head
    while curr:
        old_to_new[curr].next = old_to_new.get(curr.next)
        old_to_new[curr].random = old_to_new.get(curr.random)
        curr = curr.next
    
    return old_to_new[head]
```

---

## Why Two Passes?

```
Problem: When copying node A, its random pointer might point to node C.
But if we haven't created node C yet, we can't set the pointer!

Solution: 
Pass 1 - Create ALL nodes first (no connections)
Pass 2 - Now all nodes exist, connect them using the map

Original: A → B → C
          ↓   ↓   ↓
Random:   C   A   B

Pass 1: Create nodes
old_to_new = {
    A: A',
    B: B',
    C: C'
}

Pass 2: Connect
A'.next = B'
A'.random = C'
B'.next = C'
B'.random = A'
C'.next = None
C'.random = B'
```

---

## Example 1: Copy List with Random Pointer (LC 138)

**Problem:** Deep copy a linked list where each node has a random pointer.

```python
class Node:
    def __init__(self, val, next=None, random=None):
        self.val = val
        self.next = next
        self.random = random

def copyRandomList(head):
    if not head:
        return None
    
    # Map old nodes to new nodes
    old_to_new = {}
    
    # First pass: create all new nodes
    curr = head
    while curr:
        old_to_new[curr] = Node(curr.val)
        curr = curr.next
    
    # Second pass: connect next and random pointers
    curr = head
    while curr:
        new_node = old_to_new[curr]
        new_node.next = old_to_new.get(curr.next)
        new_node.random = old_to_new.get(curr.random)
        curr = curr.next
    
    return old_to_new[head]
```

---

## Example 2: Alternative - O(1) Space (Interweaving)

Instead of a HashMap, interweave new nodes with old ones.

```python
def copyRandomList(head):
    if not head:
        return None
    
    # Step 1: Create interweaved list
    # A → A' → B → B' → C → C'
    curr = head
    while curr:
        new_node = Node(curr.val)
        new_node.next = curr.next
        curr.next = new_node
        curr = new_node.next
    
    # Step 2: Set random pointers for new nodes
    curr = head
    while curr:
        if curr.random:
            curr.next.random = curr.random.next  # random's copy
        curr = curr.next.next
    
    # Step 3: Separate the two lists
    dummy = Node(0)
    curr_new = dummy
    curr = head
    while curr:
        # Extract new node
        curr_new.next = curr.next
        curr_new = curr_new.next
        
        # Restore original list
        curr.next = curr.next.next
        curr = curr.next
    
    return dummy.next
```

**Visual:**
```
Step 1: Interweave
A → B → C
becomes
A → A' → B → B' → C → C'

Step 2: Set random pointers
If A.random = C, then A'.random = C' (which is C.next)

Step 3: Separate
Extract: A' → B' → C' (our copy)
Restore: A → B → C (original)
```

---

## Example 3: Single Pass with getdefault

Using a recursive approach or a dictionary with default factory.

```python
def copyRandomList(head):
    if not head:
        return None
    
    old_to_new = {}
    
    def getClone(node):
        if not node:
            return None
        if node not in old_to_new:
            old_to_new[node] = Node(node.val)
            old_to_new[node].next = getClone(node.next)
            old_to_new[node].random = getClone(node.random)
        return old_to_new[node]
    
    return getClone(head)
```

---

## Example 4: Clone Graph (LC 133)

Same pattern applies to graphs!

```python
def cloneGraph(node):
    if not node:
        return None
    
    old_to_new = {}
    
    def dfs(node):
        if node in old_to_new:
            return old_to_new[node]
        
        # Create clone
        clone = Node(node.val)
        old_to_new[node] = clone
        
        # Clone neighbors
        for neighbor in node.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

---

## Comparison of Approaches

| Approach | Time | Space | Pros | Cons |
|----------|------|-------|------|------|
| HashMap (2 pass) | O(n) | O(n) | Simple, clear | Extra space |
| HashMap (recursive) | O(n) | O(n) | Elegant | Recursion limit |
| Interweaving | O(n) | O(1) | No extra space | Complex, modifies original |

---

## Complexity

```
HashMap Approach:
  Time:  O(n) - two passes
  Space: O(n) - for the map

Interweaving Approach:
  Time:  O(n) - three passes
  Space: O(1) - no extra data structure
```

---

## Common Mistakes

```
❌ Mistake 1: Using node.next directly for the copy
   new_node.random = old.random  # WRONG - points to old node!
   new_node.random = old_to_new.get(old.random)  # RIGHT

❌ Mistake 2: Forgetting None check in get()
   old_to_new[curr.random]  # Crashes if random is None!
   old_to_new.get(curr.random)  # Returns None safely

❌ Mistake 3: Not creating all nodes before connecting
   # Can't set random pointers if target node doesn't exist yet
   # Always use two passes or recursive with memoization
```

---

## When to Use HashMap vs Interweaving

```
USE HASHMAP WHEN:
✅ Code clarity is priority
✅ Not worried about extra space
✅ Need to clone graphs too

USE INTERWEAVING WHEN:
✅ O(1) space required
✅ Only linked lists (not graphs)
✅ Okay with more complex code
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   HASHMAP COPY PATTERN                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   old_to_new = {}                                           │
│                                                             │
│   # Pass 1: Create all nodes                                │
│   curr = head                                               │
│   while curr:                                               │
│       old_to_new[curr] = Node(curr.val)                     │
│       curr = curr.next                                      │
│                                                             │
│   # Pass 2: Connect pointers                                │
│   curr = head                                               │
│   while curr:                                               │
│       old_to_new[curr].next = old_to_new.get(curr.next)     │
│       old_to_new[curr].random = old_to_new.get(curr.random) │
│       curr = curr.next                                      │
│                                                             │
│   return old_to_new[head]                                   │
│                                                             │
│   KEY: Map old→new, then use map to connect                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


