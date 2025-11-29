# 🔗 Pattern 4: Reverse Linked List

---

## When to Use

- Reverse entire list
- Reverse a portion of the list
- Reverse in groups of k
- Check palindrome

---

## The Template (Iterative)

```python
def reverse(head):
    prev = None
    curr = head
    
    while curr:
        next_temp = curr.next  # 1. Save next
        curr.next = prev       # 2. Reverse pointer
        prev = curr            # 3. Move prev forward
        curr = next_temp       # 4. Move curr forward
    
    return prev  # prev is new head
```

---

## Visual Understanding

```
Original: 1 → 2 → 3 → None

Step 1: curr=1
        None ← 1   2 → 3 → None
        prev  curr next

Step 2: curr=2
        None ← 1 ← 2   3 → None
              prev curr next

Step 3: curr=3
        None ← 1 ← 2 ← 3   None
                   prev curr next

Step 4: curr=None (exit loop)
        None ← 1 ← 2 ← 3
                       prev (new head!)

Result: 3 → 2 → 1 → None
```

---

## The Template (Recursive)

```python
def reverseRecursive(head):
    # Base case: empty or single node
    if not head or not head.next:
        return head
    
    # Recursively reverse the rest
    new_head = reverseRecursive(head.next)
    
    # Reverse the link
    head.next.next = head  # Point next node back to current
    head.next = None       # Remove old forward link
    
    return new_head  # new_head propagates up unchanged
```

**Recursive Visual:**
```
reverseRecursive(1→2→3→None)
  reverseRecursive(2→3→None)
    reverseRecursive(3→None)
      return 3 (base case)
    
    Now: 2.next.next = 2  →  3 → 2
         2.next = None    →  3 → 2 → None
    return 3
  
  Now: 1.next.next = 1  →  3 → 2 → 1
       1.next = None    →  3 → 2 → 1 → None
  return 3

Result: 3 → 2 → 1 → None
```

---

## Example 1: Reverse Linked List (LC 206)

**Problem:** Reverse the entire linked list.

```python
def reverseList(head):
    prev = None
    curr = head
    
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    
    return prev
```

---

## Example 2: Reverse Between Positions (LC 92)

**Problem:** Reverse nodes from position left to right.

```python
def reverseBetween(head, left, right):
    if not head or left == right:
        return head
    
    dummy = ListNode(0)
    dummy.next = head
    
    # Step 1: Find node before left position
    prev = dummy
    for _ in range(left - 1):
        prev = prev.next
    
    # Step 2: Reverse from left to right
    # Use the "insertion" method
    curr = prev.next
    
    for _ in range(right - left):
        # Take the next node and move it to front
        next_node = curr.next
        curr.next = next_node.next
        next_node.next = prev.next
        prev.next = next_node
    
    return dummy.next
```

**Visual (left=2, right=4):**
```
d → 1 → 2 → 3 → 4 → 5
    p   c   n

Move 3 to front of reversed section:
d → 1 → 3 → 2 → 4 → 5
    p   n   c

Move 4 to front of reversed section:
d → 1 → 4 → 3 → 2 → 5
    p   n       c

Result: 1 → 4 → 3 → 2 → 5
```

---

## Example 3: Reverse in K-Groups (LC 25)

**Problem:** Reverse nodes in groups of k.

```python
def reverseKGroup(head, k):
    dummy = ListNode(0)
    dummy.next = head
    prev_group = dummy
    
    while True:
        # Check if k nodes remaining
        kth = prev_group
        for _ in range(k):
            kth = kth.next
            if not kth:
                return dummy.next  # Less than k nodes, done
        
        # Mark boundaries
        group_start = prev_group.next
        group_end = kth
        next_group = kth.next
        
        # Reverse the group
        prev = next_group
        curr = group_start
        while curr != next_group:
            next_temp = curr.next
            curr.next = prev
            prev = curr
            curr = next_temp
        
        # Connect with previous part
        prev_group.next = group_end
        prev_group = group_start
    
    return dummy.next
```

---

## Example 4: Palindrome Linked List (LC 234)

**Problem:** Check if linked list is a palindrome.

```python
def isPalindrome(head):
    if not head or not head.next:
        return True
    
    # Step 1: Find middle
    slow = head
    fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Step 2: Reverse second half
    second_half = reverse(slow.next)
    
    # Step 3: Compare
    first_half = head
    while second_half:
        if first_half.val != second_half.val:
            return False
        first_half = first_half.next
        second_half = second_half.next
    
    return True

def reverse(head):
    prev = None
    curr = head
    while curr:
        next_temp = curr.next
        curr.next = prev
        prev = curr
        curr = next_temp
    return prev
```

---

## Example 5: Reorder List (LC 143)

**Problem:** Reorder L0→L1→...→Ln to L0→Ln→L1→Ln-1→...

```python
def reorderList(head):
    if not head or not head.next:
        return
    
    # Step 1: Find middle
    slow = head
    fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Step 2: Reverse second half
    second = reverse(slow.next)
    slow.next = None  # Cut first half
    
    # Step 3: Merge alternating
    first = head
    while second:
        temp1 = first.next
        temp2 = second.next
        
        first.next = second
        second.next = temp1
        
        first = temp1
        second = temp2
```

---

## Complexity

```
Time:  O(n) - visit each node once
Space: O(1) iterative, O(n) recursive (call stack)
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to save next before modifying
   curr.next = prev  # Lost reference to rest of list!
   
   next_temp = curr.next  # Save first!
   curr.next = prev
   curr = next_temp

❌ Mistake 2: Returning head instead of prev
   return head  # head is now the TAIL!
   return prev  # prev is the new head

❌ Mistake 3: Off-by-one in partial reverse
   Draw it out! Count positions carefully.
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   REVERSE PATTERN                                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ITERATIVE (Preferred):                                    │
│   prev = None                                               │
│   curr = head                                               │
│   while curr:                                               │
│       next_temp = curr.next  # Save                         │
│       curr.next = prev       # Reverse                      │
│       prev = curr            # Move prev                    │
│       curr = next_temp       # Move curr                    │
│   return prev                                               │
│                                                             │
│   KEY INSIGHT: Save next BEFORE modifying curr.next         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


