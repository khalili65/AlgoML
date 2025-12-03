# 🏔️ Pattern 2: K-way Merge

---

## When to Use

- Merge K sorted lists/arrays
- Find smallest/largest from K sources
- Matrix problems with sorted rows/columns
- External sorting (merge sorted files)

---

## The Template

```python
import heapq

def kWayMerge(lists):
    min_heap = []
    result = []
    
    # Initialize: push first element from each list
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(min_heap, (lst[0], i, 0))
            # (value, which_list, index_in_list)
    
    while min_heap:
        val, list_idx, elem_idx = heapq.heappop(min_heap)
        result.append(val)
        
        # Push next element from same list
        if elem_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][elem_idx + 1]
            heapq.heappush(min_heap, (next_val, list_idx, elem_idx + 1))
    
    return result
```

---

## Why This Works

```
The heap always contains the SMALLEST unprocessed element from each list.
Pop the overall smallest, then add the next element from that same list.

Think of it like K runners on a track:
- Each runner represents a sorted list
- Heap tracks current position of each runner
- We always record the runner who is currently in the lead (smallest value)
- That runner then takes their next step (next element)
```

**Visual:**
```
Lists: [1, 4, 7], [2, 5, 8], [3, 6, 9]
       ↑          ↑          ↑
       Start at first element of each

Initial heap: [(1, 0, 0), (2, 1, 0), (3, 2, 0)]
              smallest from each list

Pop (1, 0, 0) → output 1 → push (4, 0, 1) from list 0
Heap: [(2, 1, 0), (3, 2, 0), (4, 0, 1)]

Pop (2, 1, 0) → output 2 → push (5, 1, 1) from list 1
Heap: [(3, 2, 0), (4, 0, 1), (5, 1, 1)]

Pop (3, 2, 0) → output 3 → push (6, 2, 1) from list 2
...

Result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

## The Tuple: (value, list_idx, elem_idx)

```python
heappush(min_heap, (val, list_idx, elem_idx))
```

Why do we need three parts?

```
val       → For heap ordering (smallest first)
list_idx  → To know which list this came from
elem_idx  → To know where we are in that list (for getting next)

When we pop (val, list_idx, elem_idx):
- We know the value to output
- We know which list to get the next element from
- We know the index of the next element: elem_idx + 1
```

---

## Example 1: Merge K Sorted Lists (LC 23)

**Problem:** Merge k sorted linked lists into one sorted list.

```python
def mergeKLists(lists):
    min_heap = []
    dummy = ListNode(0)
    curr = dummy
    
    # Push first node from each list
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(min_heap, (lst.val, i, lst))
    
    while min_heap:
        val, i, node = heapq.heappop(min_heap)
        curr.next = node
        curr = curr.next
        
        if node.next:
            heapq.heappush(min_heap, (node.next.val, i, node.next))
    
    return dummy.next
```

**Note:** We use `i` (list index) as a tiebreaker because ListNode objects can't be compared directly.

---

## Example 2: Kth Smallest in Sorted Matrix (LC 378)

**Problem:** Find kth smallest element in a matrix where rows and columns are sorted.

```python
def kthSmallest(matrix, k):
    n = len(matrix)
    min_heap = []
    
    # Push first element of each row
    for i in range(min(n, k)):  # Only need first k rows
        heapq.heappush(min_heap, (matrix[i][0], i, 0))
    
    # Pop k-1 times
    for _ in range(k - 1):
        val, row, col = heapq.heappop(min_heap)
        if col + 1 < n:
            heapq.heappush(min_heap, (matrix[row][col + 1], row, col + 1))
    
    return min_heap[0][0]  # Kth smallest is now at top
```

**Visual:**
```
Matrix (each row and column sorted):
[1,  5,  9]
[10, 11, 13]
[12, 13, 15]

Find 5th smallest:

Initial heap (first column): [(1, 0, 0), (10, 1, 0), (12, 2, 0)]

Pop 1 → push 5  → heap: [(5, 0, 1), (10, 1, 0), (12, 2, 0)]
Pop 5 → push 9  → heap: [(9, 0, 2), (10, 1, 0), (12, 2, 0)]
Pop 9 → push nothing → heap: [(10, 1, 0), (12, 2, 0)]
Pop 10 → push 11 → heap: [(11, 1, 1), (12, 2, 0)]

Top of heap = 11 = 5th smallest ✓
```

---

## Example 3: Find K Pairs with Smallest Sums (LC 373)

**Problem:** Given two sorted arrays, find k pairs with smallest sums.

```python
def kSmallestPairs(nums1, nums2, k):
    if not nums1 or not nums2:
        return []
    
    min_heap = []
    result = []
    
    # Start with (nums1[0] + nums2[j]) for all j
    for j in range(min(len(nums2), k)):
        heapq.heappush(min_heap, (nums1[0] + nums2[j], 0, j))
    
    while min_heap and len(result) < k:
        total, i, j = heapq.heappop(min_heap)
        result.append([nums1[i], nums2[j]])
        
        # Next candidate: same j, next i
        if i + 1 < len(nums1):
            heapq.heappush(min_heap, (nums1[i + 1] + nums2[j], i + 1, j))
    
    return result
```

---

## Example 4: Smallest Range Covering K Lists (LC 632)

**Problem:** Find smallest range that includes at least one number from each list.

```python
def smallestRange(nums):
    min_heap = []
    current_max = float('-inf')
    
    # Push first element from each list, track max
    for i, lst in enumerate(nums):
        heapq.heappush(min_heap, (lst[0], i, 0))
        current_max = max(current_max, lst[0])
    
    result = [float('-inf'), float('inf')]
    
    while True:
        current_min, list_idx, elem_idx = heapq.heappop(min_heap)
        
        # Update result if this range is smaller
        if current_max - current_min < result[1] - result[0]:
            result = [current_min, current_max]
        
        # If any list is exhausted, we're done
        if elem_idx + 1 >= len(nums[list_idx]):
            break
        
        # Push next element and update max
        next_val = nums[list_idx][elem_idx + 1]
        heapq.heappush(min_heap, (next_val, list_idx, elem_idx + 1))
        current_max = max(current_max, next_val)
    
    return result
```

---

## Complexity

```
Time:  O(n log k)
       - n = total elements across all lists
       - k = number of lists
       - Each element is pushed/popped once: O(log k)

Space: O(k)
       - Heap holds at most one element from each list
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to check if list is empty
   for i, lst in enumerate(lists):
       if lst:  # Don't forget this check!
           heappush(...)

❌ Mistake 2: Forgetting to check bounds before pushing next
   if elem_idx + 1 < len(lists[list_idx]):  # Check first!
       heappush(...)

❌ Mistake 3: Not using tiebreaker for non-comparable objects
   # For linked list nodes, include index as tiebreaker:
   heappush(heap, (node.val, i, node))  # 'i' breaks ties
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   K-WAY MERGE PATTERN                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   # Initialize with first element from each list            │
│   for i, lst in enumerate(lists):                           │
│       if lst:                                               │
│           heappush(heap, (lst[0], i, 0))                    │
│                                                             │
│   while heap:                                               │
│       val, list_idx, elem_idx = heappop(heap)               │
│       process(val)                                          │
│                                                             │
│       # Push next from same list                            │
│       if elem_idx + 1 < len(lists[list_idx]):               │
│           next_val = lists[list_idx][elem_idx + 1]          │
│           heappush(heap, (next_val, list_idx, elem_idx+1))  │
│                                                             │
│   KEY: Heap always has smallest unprocessed from each list  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


