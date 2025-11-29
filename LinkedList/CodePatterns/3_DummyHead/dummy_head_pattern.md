# 🔗 Pattern 3: Dummy Head

---

## When to Use

- Head might be deleted
- Insert at the beginning
- Operations that might change the head
- Simplify edge cases

---

## The Template

```python
def useDummy(head):
    dummy = ListNode(0)  # Create dummy node
    dummy.next = head    # Point to original head
    
    curr = dummy  # Start from dummy (not head!)
    
    while curr.next:  # Check curr.next, not curr
        if CONDITION:
            curr.next = curr.next.next  # Delete next node
        else:
            curr = curr.next
    
    return dummy.next  # Return new head (might have changed!)
```

---

## Why It Works

```
The dummy node acts as a "fake head" that never gets deleted.
This eliminates special cases for the actual head.

WITHOUT dummy (handling head separately):
def remove(head, val):
    while head and head.val == val:  # Special case for head
        head = head.next
    if not head:
        return None
    curr = head
    while curr.next:
        if curr.next.val == val:
            curr.next = curr.next.next
        else:
            curr = curr.next
    return head

WITH dummy (no special cases):
def remove(head, val):
    dummy = ListNode(0)
    dummy.next = head
    curr = dummy
    while curr.next:
        if curr.next.val == val:
            curr.next = curr.next.next
        else:
            curr = curr.next
    return dummy.next
```

---

## Example 1: Remove Elements (LC 203)

**Problem:** Remove all nodes with value equal to val.

```python
def removeElements(head, val):
    dummy = ListNode(0)
    dummy.next = head
    
    curr = dummy
    while curr.next:
        if curr.next.val == val:
            curr.next = curr.next.next  # Skip the node
        else:
            curr = curr.next
    
    return dummy.next
```

---

## Example 2: Remove Duplicates from Sorted List (LC 83)

**Problem:** Remove duplicates so each element appears only once.

```python
def deleteDuplicates(head):
    if not head:
        return None
    
    curr = head
    while curr.next:
        if curr.val == curr.next.val:
            curr.next = curr.next.next  # Skip duplicate
        else:
            curr = curr.next
    
    return head
```

**Note:** This one doesn't need dummy because head is never deleted (we keep first occurrence).

---

## Example 3: Remove Duplicates II - Remove ALL duplicates (LC 82)

**Problem:** Remove all nodes that have duplicate numbers.

```python
def deleteDuplicates(head):
    dummy = ListNode(0)
    dummy.next = head
    
    prev = dummy  # Points to last node we're keeping
    
    while head:
        # Check if head is start of duplicates
        if head.next and head.val == head.next.val:
            # Skip ALL nodes with this value
            while head.next and head.val == head.next.val:
                head = head.next
            # Skip the last duplicate too
            prev.next = head.next
        else:
            # No duplicate, keep this node
            prev = prev.next
        head = head.next
    
    return dummy.next
```

**Visual:**
```
1 → 2 → 3 → 3 → 4 → 4 → 5

d → 1 → 2 → 3 → 3 → 4 → 4 → 5
p   h

d → 1 → 2 → 3 → 3 → 4 → 4 → 5
    p   h

d → 1 → 2 → 3 → 3 → 4 → 4 → 5
        p   h (duplicate detected!)
        
Skip all 3s: prev.next = 4
d → 1 → 2 → 4 → 4 → 5
        p   h
        
Skip all 4s: prev.next = 5
d → 1 → 2 → 5
        p   h

Result: 1 → 2 → 5
```

---

## Example 4: Partition List (LC 86)

**Problem:** Partition list so all nodes < x come before nodes >= x.

```python
def partition(head, x):
    # Two dummy heads for two lists
    before_dummy = ListNode(0)
    after_dummy = ListNode(0)
    
    before = before_dummy
    after = after_dummy
    
    while head:
        if head.val < x:
            before.next = head
            before = before.next
        else:
            after.next = head
            after = after.next
        head = head.next
    
    # Connect the two lists
    after.next = None  # Important! Avoid cycle
    before.next = after_dummy.next
    
    return before_dummy.next
```

---

## Example 5: Swap Nodes in Pairs (LC 24)

**Problem:** Swap every two adjacent nodes.

```python
def swapPairs(head):
    dummy = ListNode(0)
    dummy.next = head
    
    prev = dummy
    
    while prev.next and prev.next.next:
        first = prev.next
        second = prev.next.next
        
        # Swap
        prev.next = second
        first.next = second.next
        second.next = first
        
        # Move prev
        prev = first
    
    return dummy.next
```

**Visual:**
```
d → 1 → 2 → 3 → 4
p   f   s

After swap:
d → 2 → 1 → 3 → 4
    s   f=p

d → 2 → 1 → 3 → 4
        p   f   s

After swap:
d → 2 → 1 → 4 → 3
            s   f

Result: 2 → 1 → 4 → 3
```

---

## Complexity

```
Time:  O(n) - single pass through list
Space: O(1) - only dummy node (constant space)
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to return dummy.next
   return head  # WRONG - head might have been deleted!
   return dummy.next  # RIGHT

❌ Mistake 2: Starting curr at head instead of dummy
   curr = head  # WRONG - can't delete head this way
   curr = dummy  # RIGHT

❌ Mistake 3: Checking curr instead of curr.next
   while curr:  # WRONG - we need to modify curr.next
   while curr.next:  # RIGHT
```

---

## When to Use vs When Not

```
USE DUMMY WHEN:
✅ Head might be deleted
✅ Inserting at the beginning
✅ Partitioning into multiple lists
✅ Swapping/reordering that affects head

NO DUMMY NEEDED WHEN:
❌ Just traversing (read-only)
❌ Head is guaranteed to stay (e.g., remove duplicates keeping first)
❌ Modifying values only (not structure)
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   DUMMY HEAD PATTERN                                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   dummy = ListNode(0)                                       │
│   dummy.next = head                                         │
│   curr = dummy                                              │
│                                                             │
│   while curr.next:                                          │
│       # Work with curr.next                                 │
│       ...                                                   │
│                                                             │
│   return dummy.next  # NEW head                             │
│                                                             │
│   USE WHEN: Head might change (delete, insert, swap)        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


