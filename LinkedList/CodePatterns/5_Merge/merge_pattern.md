# 🔗 Pattern 5: Merge Lists

---

## When to Use

- Merge two sorted lists
- Merge k sorted lists
- Sort a linked list (merge sort)
- Interleave two lists

---

## The Template

```python
def merge(l1, l2):
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
    
    # Attach remaining nodes
    curr.next = l1 if l1 else l2
    
    return dummy.next
```

---

## Visual Understanding

```
Merge: 1→3→5 and 2→4→6

Step 1: Compare 1 vs 2 → take 1
d → 1
    c   l1=3, l2=2

Step 2: Compare 3 vs 2 → take 2
d → 1 → 2
        c   l1=3, l2=4

Step 3: Compare 3 vs 4 → take 3
d → 1 → 2 → 3
            c   l1=5, l2=4

Step 4: Compare 5 vs 4 → take 4
d → 1 → 2 → 3 → 4
                c   l1=5, l2=6

Step 5: Compare 5 vs 6 → take 5
d → 1 → 2 → 3 → 4 → 5
                    c   l1=None, l2=6

Step 6: l1 is None, attach remaining l2
d → 1 → 2 → 3 → 4 → 5 → 6

Result: 1 → 2 → 3 → 4 → 5 → 6
```

---

## Example 1: Merge Two Sorted Lists (LC 21)

**Problem:** Merge two sorted linked lists.

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

**Recursive Version:**
```python
def mergeTwoLists(l1, l2):
    if not l1:
        return l2
    if not l2:
        return l1
    
    if l1.val <= l2.val:
        l1.next = mergeTwoLists(l1.next, l2)
        return l1
    else:
        l2.next = mergeTwoLists(l1, l2.next)
        return l2
```

---

## Example 2: Merge K Sorted Lists (LC 23)

**Problem:** Merge k sorted linked lists.

**Approach 1: Using Min Heap**
```python
import heapq

def mergeKLists(lists):
    dummy = ListNode(0)
    curr = dummy
    
    # Min heap: (value, index, node)
    # Index breaks ties when values are equal
    heap = []
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst.val, i, lst))
    
    while heap:
        val, i, node = heapq.heappop(heap)
        curr.next = node
        curr = curr.next
        
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next
```

**Approach 2: Divide and Conquer**
```python
def mergeKLists(lists):
    if not lists:
        return None
    
    while len(lists) > 1:
        merged = []
        for i in range(0, len(lists), 2):
            l1 = lists[i]
            l2 = lists[i + 1] if i + 1 < len(lists) else None
            merged.append(mergeTwoLists(l1, l2))
        lists = merged
    
    return lists[0]
```

---

## Example 3: Sort List (LC 148) - Merge Sort

**Problem:** Sort a linked list in O(n log n) time.

```python
def sortList(head):
    # Base case: empty or single node
    if not head or not head.next:
        return head
    
    # Step 1: Find middle and split
    slow = head
    fast = head.next  # Start fast one ahead for clean split
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    mid = slow.next
    slow.next = None  # Cut the list
    
    # Step 2: Recursively sort both halves
    left = sortList(head)
    right = sortList(mid)
    
    # Step 3: Merge sorted halves
    return mergeTwoLists(left, right)

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

## Example 4: Add Two Numbers (LC 2)

**Problem:** Add two numbers represented as linked lists (reverse order).

```python
def addTwoNumbers(l1, l2):
    dummy = ListNode(0)
    curr = dummy
    carry = 0
    
    while l1 or l2 or carry:
        val1 = l1.val if l1 else 0
        val2 = l2.val if l2 else 0
        
        total = val1 + val2 + carry
        carry = total // 10
        curr.next = ListNode(total % 10)
        curr = curr.next
        
        l1 = l1.next if l1 else None
        l2 = l2.next if l2 else None
    
    return dummy.next
```

---

## Example 5: Intersection of Two Lists (LC 160)

**Problem:** Find node where two lists intersect.

```python
def getIntersectionNode(headA, headB):
    if not headA or not headB:
        return None
    
    pA = headA
    pB = headB
    
    # Traverse both lists
    # When one reaches end, redirect to other list's head
    # They will meet at intersection or both become None
    while pA != pB:
        pA = pA.next if pA else headB
        pB = pB.next if pB else headA
    
    return pA
```

**Why it works:**
```
List A: a1 → a2 → c1 → c2 → c3
List B: b1 → b2 → b3 → c1 → c2 → c3

Path A: a1→a2→c1→c2→c3→b1→b2→b3→c1
Path B: b1→b2→b3→c1→c2→c3→a1→a2→c1

Both paths have same length! They meet at c1.
```

---

## Complexity

```
Merge Two Lists:
  Time:  O(n + m)
  Space: O(1)

Merge K Lists (Heap):
  Time:  O(n log k) where n = total nodes
  Space: O(k) for heap

Merge K Lists (Divide & Conquer):
  Time:  O(n log k)
  Space: O(log k) for recursion

Sort List (Merge Sort):
  Time:  O(n log n)
  Space: O(log n) for recursion
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to handle remaining nodes
   while l1 and l2:  # Loop ends when one is empty
       ...
   curr.next = l1 if l1 else l2  # Don't forget this!

❌ Mistake 2: Creating new nodes instead of reusing
   curr.next = ListNode(l1.val)  # Unnecessary!
   curr.next = l1  # Reuse existing node

❌ Mistake 3: Off-by-one in split for merge sort
   fast = head.next  # Important! Otherwise uneven split
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   MERGE PATTERN                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   dummy = ListNode(0)                                       │
│   curr = dummy                                              │
│                                                             │
│   while l1 and l2:                                          │
│       if l1.val <= l2.val:                                  │
│           curr.next = l1                                    │
│           l1 = l1.next                                      │
│       else:                                                 │
│           curr.next = l2                                    │
│           l2 = l2.next                                      │
│       curr = curr.next                                      │
│                                                             │
│   curr.next = l1 or l2  # Remaining nodes                   │
│   return dummy.next                                         │
│                                                             │
│   KEY: Compare → attach smaller → advance → repeat          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


