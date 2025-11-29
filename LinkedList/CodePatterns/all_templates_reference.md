# 🔗 Linked List - All Templates Reference

Quick reference for all 6 linked list code patterns with examples.

---

## Pattern 1: Fast & Slow Pointers

**Template:**
```python
slow = head
fast = head

while fast and fast.next:
    slow = slow.next
    fast = fast.next.next

# slow is at middle OR they met (cycle)
```

**Example - Find Middle (LC 876):**
```python
def middleNode(head):
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow
```

---

## Pattern 2: Gap Pointers

**Template:**
```python
first = head
second = head

# Create gap of n nodes
for _ in range(n):
    first = first.next

# Move both until first reaches end
while first:
    first = first.next
    second = second.next

# second is n nodes from end
```

**Example - Remove Nth From End (LC 19):**
```python
def removeNthFromEnd(head, n):
    dummy = ListNode(0)
    dummy.next = head
    first = second = dummy
    
    for _ in range(n + 1):
        first = first.next
    
    while first:
        first = first.next
        second = second.next
    
    second.next = second.next.next
    return dummy.next
```

---

## Pattern 3: Dummy Head

**Template:**
```python
dummy = ListNode(0)
dummy.next = head
curr = dummy

while curr.next:
    if CONDITION:
        curr.next = curr.next.next  # Delete
    else:
        curr = curr.next

return dummy.next
```

**Example - Remove Elements (LC 203):**
```python
def removeElements(head, val):
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

## Pattern 4: Reverse

**Template:**
```python
prev = None
curr = head

while curr:
    next_temp = curr.next  # Save
    curr.next = prev       # Reverse
    prev = curr            # Move prev
    curr = next_temp       # Move curr

return prev  # New head
```

**Example - Reverse List (LC 206):**
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

## Pattern 5: Merge

**Template:**
```python
dummy = ListNode(0)
curr = dummy

while l1 and l2:
    if l1.val <= l2.val:
        curr.next = l1
        l1 = l1.next
    else:
        curr.next = l2
        l2 = l2.next
    curr = curr.next

curr.next = l1 or l2
return dummy.next
```

**Example - Merge Two Lists (LC 21):**
```python
def mergeTwoLists(l1, l2):
    dummy = ListNode(0)
    curr = dummy
    
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    
    curr.next = l1 if l1 else l2
    return dummy.next
```

---

## Pattern 6: HashMap Copy

**Template:**
```python
old_to_new = {}

# Pass 1: Create nodes
curr = head
while curr:
    old_to_new[curr] = Node(curr.val)
    curr = curr.next

# Pass 2: Connect pointers
curr = head
while curr:
    old_to_new[curr].next = old_to_new.get(curr.next)
    old_to_new[curr].random = old_to_new.get(curr.random)
    curr = curr.next

return old_to_new[head]
```

**Example - Copy Random List (LC 138):**
```python
def copyRandomList(head):
    if not head:
        return None
    
    old_to_new = {}
    
    curr = head
    while curr:
        old_to_new[curr] = Node(curr.val)
        curr = curr.next
    
    curr = head
    while curr:
        old_to_new[curr].next = old_to_new.get(curr.next)
        old_to_new[curr].random = old_to_new.get(curr.random)
        curr = curr.next
    
    return old_to_new[head]
```

---

## 🎯 Quick Selection Guide

| Problem Type | Pattern |
|--------------|---------|
| Cycle detection / Find middle | 1. Fast & Slow |
| Nth from end / Rotate | 2. Gap Pointers |
| Head might change | 3. Dummy Head |
| Flip pointers | 4. Reverse |
| Combine sorted lists | 5. Merge |
| Clone with extra pointers | 6. HashMap Copy |


