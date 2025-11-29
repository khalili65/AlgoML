# 🔗 Pattern 2: Gap Pointers (N Apart)

---

## When to Use

- Find nth node from end
- Remove nth node from end
- Rotate list by k positions
- Any problem requiring "distance from end"

---

## The Template

```python
def gapPointers(head, n):
    first = head
    second = head
    
    # Step 1: Move first pointer n steps ahead
    for _ in range(n):
        first = first.next
    
    # Step 2: Move both until first reaches end
    while first:
        first = first.next
        second = second.next
    
    return second  # second is n steps from end
```

---

## Why It Works

```
Create a "gap" of n nodes between two pointers.
When first reaches end, second is n nodes from end.

Find 2nd from end (n=2):

Step 1: Move first 2 steps ahead
1 → 2 → 3 → 4 → 5
s       f

Step 2: Move both until first is None
1 → 2 → 3 → 4 → 5 → None
    s       f

1 → 2 → 3 → 4 → 5 → None
        s       f

1 → 2 → 3 → 4 → 5 → None
            s       f (None)

second points to 4 (2nd from end)!
```

---

## Example 1: Nth Node From End

**Problem:** Return the nth node from the end.

```python
def nthFromEnd(head, n):
    first = head
    second = head
    
    # Move first n steps ahead
    for _ in range(n):
        first = first.next
    
    # Move both until first reaches end
    while first:
        first = first.next
        second = second.next
    
    return second
```

---

## Example 2: Remove Nth From End (LC 19)

**Problem:** Remove the nth node from the end and return head.

```python
def removeNthFromEnd(head, n):
    dummy = ListNode(0)
    dummy.next = head
    first = dummy
    second = dummy
    
    # Move first n+1 steps ahead (to be BEFORE the target)
    for _ in range(n + 1):
        first = first.next
    
    # Move both until first reaches end
    while first:
        first = first.next
        second = second.next
    
    # second is now BEFORE the node to delete
    second.next = second.next.next
    
    return dummy.next
```

**Why n+1?**
```
To delete a node, we need pointer to the node BEFORE it.
So we want second to be at (n+1)th from end.

Delete 2nd from end:
1 → 2 → 3 → 4 → 5

With n+1 = 3 gap:
d → 1 → 2 → 3 → 4 → 5 → None
            s           f

second.next = 4 (the node to delete)
second.next = second.next.next → skip node 4
```

---

## 🔑 Understanding Python References (IMPORTANT!)

This section explains **why `dummy` doesn't change when `first` changes**.

### What does `first = dummy` mean?

```python
dummy = ListNode(0)   # Create a node, dummy POINTS to it
first = dummy         # first POINTS to the SAME node as dummy
```

**Visual:**
```
                    ┌─────────────┐
    dummy ─────────►│ ListNode(0) │
                    │  next: None │
    first ─────────►│             │
                    └─────────────┘

Both dummy and first are like "arrows" pointing to the SAME box.
The box is the actual node in memory.
```

### What does `dummy.next = head` mean?

```python
dummy.next = head    # Modify the node's "next" field
```

**Visual:**
```
                    ┌─────────────┐      ┌─────────────┐
    dummy ─────────►│ ListNode(0) │─────►│ head (1)    │──► ...
                    │  next: head │      │             │
    first ─────────►│             │      └─────────────┘
                    └─────────────┘
```

### CRITICAL: Assignment vs Mutation

There are **TWO different operations**:

**1. REASSIGNMENT (`=`)** - Change where the arrow points
```python
first = first.next   # Move the "first" arrow to point elsewhere
```

**Visual - BEFORE:**
```
    dummy ─────────►┌─────────────┐      ┌─────────────┐
                    │ ListNode(0) │─────►│ head (1)    │──► ...
    first ─────────►└─────────────┘      └─────────────┘
```

**Visual - AFTER `first = first.next`:**
```
    dummy ─────────►┌─────────────┐      ┌─────────────┐
                    │ ListNode(0) │─────►│ head (1)    │──► ...
                    └─────────────┘      └─────────────┘
                                               ▲
    first ─────────────────────────────────────┘
    
    first moved! But dummy STILL points to the same node!
```

**2. MUTATION (`.next =`)** - Change the content inside the box
```python
second.next = second.next.next   # Modify the node's next pointer
```

This changes what's INSIDE the node, not where `second` points.

### Understanding Mutation Better

**Think of a node as a box with two compartments:**
```
┌─────────────────────────┐
│   val   │     next      │
│   (1)   │  (arrow out)  │──────►  (points to next node)
└─────────────────────────┘
```

**What is `second.next`?**
```
second ────────►┌─────────────────────────┐
                │   val   │     next      │
                │   (1)   │      ●────────┼──────► node 2
                └─────────────────────────┘
                              ▲
                              │
                    THIS is second.next
                    (the arrow INSIDE the box)
```

**What does `second.next = second.next.next` do?**

BEFORE:
```
second ────────►┌───────────────┐      ┌───────────────┐      ┌───────────────┐
                │ val │  next   │      │ val │  next   │      │ val │  next   │
                │  1  │    ●────┼─────►│  2  │    ●────┼─────►│  3  │    ●────┼──► None
                └───────────────┘      └───────────────┘      └───────────────┘
                          ▲
                    second.next         second.next.next
                    (points to 2)       (points to 3)
```

AFTER `second.next = second.next.next`:
```
second ────────►┌───────────────┐      ┌───────────────┐      ┌───────────────┐
                │ val │  next   │      │ val │  next   │      │ val │  next   │
                │  1  │    ●────┼──┐   │  2  │    ●────┼─────►│  3  │    ●────┼──► None
                └───────────────┘  │   └───────────────┘      └───────────────┘
                          ▲        │                                 ▲
                          │        └─────────────────────────────────┘
                    We changed THIS arrow!
                    (the content INSIDE node 1)
```

Result: Node 2 is skipped!
```
                ┌───────────────┐                              ┌───────────────┐
                │ val │  next   │                              │ val │  next   │
                │  1  │    ●────┼─────────────────────────────►│  3  │    ●────┼──► None
                └───────────────┘                              └───────────────┘

List is now: 1 → 3  (node 2 is gone!)
```

**The Key Difference:**
```
┌────────────────────────────┬──────────────────────────┬─────────────────┐
│ Code                       │ What Changes             │ second moves?   │
├────────────────────────────┼──────────────────────────┼─────────────────┤
│ second = second.next       │ Where second points      │ ✅ YES          │
│ second.next = something    │ Arrow INSIDE the node    │ ❌ NO           │
└────────────────────────────┴──────────────────────────┴─────────────────┘
```

**Simple Analogy - Think of it like a house:**
```
second       = house address (which house you're at)
second.next  = the door inside the house (where the door leads)

second = new_address       → You MOVE to a different house
second.next = new_door     → You CHANGE the door inside your current house
                             (you're still in the same house!)
```

---

## 📚 Python Fundamentals: Assignment vs Mutation

### Assignment (`=`) — Change what a variable POINTS TO

```python
x = [1, 2, 3]    # x points to a list
x = [4, 5, 6]    # x now points to a DIFFERENT list
```

```
BEFORE:                      AFTER x = [4, 5, 6]:
                             
x ────► [1, 2, 3]            x ────► [4, 5, 6]    (new list)
                             
                             [1, 2, 3]  (old list, orphaned)
```

### Mutation — Change what's INSIDE the object

```python
x = [1, 2, 3]    # x points to a list
x[0] = 99        # Change content INSIDE the list
x.append(4)      # Add to the list
```

```
BEFORE:                      AFTER x[0] = 99:
                             
x ────► [1, 2, 3]            x ────► [99, 2, 3]
                             
        Same list, different content!
```

### Two Variables Pointing to Same Object

```python
a = [1, 2, 3]
b = a            # b points to SAME list as a
```

```
a ────►┌─────────────┐
       │  [1, 2, 3]  │
b ────►└─────────────┘

Both point to the SAME box!
```

### What happens with ASSIGNMENT?

```python
a = [1, 2, 3]
b = a
a = [7, 8, 9]    # ASSIGNMENT: a points to new list
print(b)         # [1, 2, 3] — b unchanged!
```

**Step by step:**
```
Step 1: a = [1, 2, 3]
                    ┌─────────────┐
        a ─────────►│  [1, 2, 3]  │
                    └─────────────┘


Step 2: b = a (both point to SAME object)
                    ┌─────────────┐
        a ─────────►│  [1, 2, 3]  │
        b ─────────►│             │
                    └─────────────┘


Step 3: a = [7, 8, 9] (ASSIGNMENT - a moves to NEW object)
                    ┌─────────────┐
        a ─────────►│  [7, 8, 9]  │   ← NEW object
                    └─────────────┘
        
                    ┌─────────────┐
        b ─────────►│  [1, 2, 3]  │   ← OLD object (b unaffected!)
                    └─────────────┘

        a moved to a new house.
        b is still in the old house.
        b doesn't care what a does!
```

### What happens with MUTATION?

```python
a = [1, 2, 3]
b = a
a[0] = 99        # MUTATION: change content inside
print(b)         # [99, 2, 3] — b ALSO changed!
```

```
BEFORE a[0] = 99:            AFTER a[0] = 99:

a ────►┌─────────────┐       a ────►┌──────────────┐
       │  [1, 2, 3]  │              │  [99, 2, 3]  │
b ────►└─────────────┘       b ────►└──────────────┘

Same box, both see the change!
```

### Summary Table

```
┌─────────────────────┬────────────┬───────────────────────────────────────┐
│ Operation           │ Type       │ Effect                                │
├─────────────────────┼────────────┼───────────────────────────────────────┤
│ x = new_value       │ Assignment │ x points to something new             │
│ x[0] = value        │ Mutation   │ Change content inside                 │
│ x.append(value)     │ Mutation   │ Change content inside                 │
│ x.next = value      │ Mutation   │ Change content inside                 │
│ x = x.next          │ Assignment │ x points to something new             │
└─────────────────────┴────────────┴───────────────────────────────────────┘
```

### The Golden Rule

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   ASSIGNMENT (=)                                            │
│   • Changes which object a variable points to               │
│   • Other variables pointing to old object are UNAFFECTED   │
│   • Think: "I'm moving to a new house"                      │
│                                                             │
│   MUTATION (.something = or [index] =)                      │
│   • Changes the content INSIDE an object                    │
│   • ALL variables pointing to that object see the change    │
│   • Think: "I'm redecorating the house I'm in"              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Step-by-Step Walkthrough

```python
dummy = ListNode(0)      # Create node, dummy points to it
dummy.next = head        # Connect dummy to head
first = dummy            # first points to same node as dummy
second = dummy           # second points to same node as dummy
```

**Initial State:**
```
                    ┌───────┐    ┌───┐    ┌───┐    ┌───┐
    dummy ─────────►│   0   │───►│ 1 │───►│ 2 │───►│ 3 │──► None
    first ─────────►│       │    │   │    │   │    │   │
    second ────────►└───────┘    └───┘    └───┘    └───┘
```

**After `first = first.next` (3 times for n+1=3):**
```
                    ┌───────┐    ┌───┐    ┌───┐    ┌───┐
    dummy ─────────►│   0   │───►│ 1 │───►│ 2 │───►│ 3 │──► None
    second ────────►│       │    │   │    │   │    │   │
                    └───────┘    └───┘    └───┘    └───┘
                                               ▲
    first ─────────────────────────────────────┘
```

**dummy and second DIDN'T MOVE!** Only `first` moved.

**After moving both until first is None:**
```
                    ┌───────┐    ┌───┐    ┌───┐    ┌───┐
    dummy ─────────►│   0   │───►│ 1 │───►│ 2 │───►│ 3 │──► None
                    └───────┘    └───┘    └───┘    └───┘
                                    ▲                        ▲
    second ─────────────────────────┘                        │
    first ───────────────────────────────────────────────────┘ (None)
```

**After `second.next = second.next.next` (delete node 2):**
```
                    ┌───────┐    ┌───┐              ┌───┐
    dummy ─────────►│   0   │───►│ 1 │─────────────►│ 3 │──► None
                    └───────┘    └───┘              └───┘
                                    ▲
    second ─────────────────────────┘

Node 2 is skipped! List is now: 0 → 1 → 3
```

**`return dummy.next`** returns node 1 (the new head).

### Why Does `dummy` Still Have the List?

```
dummy never moved! It still points to the node we created.
That node's .next was connected to head.
Even after first and second moved around, dummy stayed put.
So dummy.next still gives us the (possibly modified) list.
```

### Summary Box

```
┌─────────────────────────────────────────────────────────────┐
│   PYTHON REFERENCES - KEY INSIGHT                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   first = dummy        →  first points to same node         │
│   first = first.next   →  first moves, dummy STAYS          │
│                                                             │
│   REASSIGNMENT (=): Changes where variable points           │
│   MUTATION (.next=): Changes content inside the node        │
│                                                             │
│   dummy acts as an "anchor" that never moves!               │
│   return dummy.next always gives the correct head.          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Why Dummy Node for Delete?

### Problem: What if we delete the HEAD?

```
List: 1 → 2 → 3
Remove 3rd from end = Remove node 1 (the HEAD!)

Without dummy:
- We need to return a NEW head (node 2)
- But our algorithm finds the node BEFORE the target
- There's NO node before the head! ❌
```

### Solution: Dummy Creates a "Node Before Head"

```
d → 1 → 2 → 3
↑
dummy is "before" the head!

Now we CAN find the node before 1 (it's dummy!)
After: dummy.next = 2
Return dummy.next → 2 → 3 ✓
```

### Quick Rule

```
- Find nth from end      → gap = n
- Delete nth from end    → gap = n+1 (to land BEFORE target)
- Head might be deleted  → USE DUMMY NODE
```

---

## Example 3: Rotate List (LC 61)

**Problem:** Rotate the list to the right by k places.

```python
def rotateRight(head, k):
    if not head or not head.next or k == 0:
        return head
    
    # Step 1: Find length and tail
    length = 1
    tail = head
    while tail.next:
        tail = tail.next
        length += 1
    
    # Step 2: Handle k > length
    k = k % length
    if k == 0:
        return head
    
    # Step 3: Find new tail (length - k - 1 from head)
    new_tail = head
    for _ in range(length - k - 1):
        new_tail = new_tail.next
    
    # Step 4: Rearrange
    new_head = new_tail.next
    new_tail.next = None
    tail.next = head
    
    return new_head
```

**Visual:**
```
Rotate by k=2:
1 → 2 → 3 → 4 → 5

new_tail = node 3 (length - k - 1 = 5 - 2 - 1 = 2 steps from head)
new_head = node 4

Result: 4 → 5 → 1 → 2 → 3
```

---

## Example 4: Split List in Half

**Problem:** Split list into two halves.

```python
def splitList(head):
    if not head or not head.next:
        return head, None
    
    slow = head
    fast = head.next  # Start fast one ahead for even split
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    second_half = slow.next
    slow.next = None  # Cut the connection
    
    return head, second_half
```

---

## Complexity

```
Time:  O(n) - traverse list once or twice
Space: O(1) - only two pointers
```

---

## Common Mistakes

```
❌ Mistake 1: Off-by-one error with n
   - To find nth from end: gap = n
   - To find node BEFORE nth: gap = n + 1

❌ Mistake 2: Forgetting dummy head when head might be deleted
   - If n equals list length, head is deleted
   - Use dummy to handle this edge case

❌ Mistake 3: Not handling k > length in rotation
   k = k % length  # Always do this!
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   GAP POINTERS PATTERN                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   first = second = head                                     │
│   for _ in range(n):                                        │
│       first = first.next    # Create gap                    │
│   while first:                                              │
│       first = first.next                                    │
│       second = second.next  # Maintain gap                  │
│                                                             │
│   USE FOR:                                                  │
│   • Find nth from end                                       │
│   • Remove nth from end (use gap = n+1 with dummy)          │
│   • Rotate list                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


