# 🏔️ Heap - All Templates Reference

Quick reference for all 5 heap code patterns with examples.

---

## Pattern 1: Top K Elements

**Template:**
```python
import heapq

min_heap = []

for num in nums:
    heapq.heappush(min_heap, num)
    if len(min_heap) > k:
        heapq.heappop(min_heap)

# min_heap contains K largest
# min_heap[0] is Kth largest
```

**Example - Kth Largest Element (LC 215):**
```python
def findKthLargest(nums, k):
    min_heap = []
    
    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    
    return min_heap[0]
```

---

## Pattern 2: K-way Merge

**Template:**
```python
import heapq

heap = []

# Initialize with first element from each list
for i, lst in enumerate(lists):
    if lst:
        heapq.heappush(heap, (lst[0], i, 0))

while heap:
    val, list_idx, elem_idx = heapq.heappop(heap)
    process(val)
    
    # Push next from same list
    if elem_idx + 1 < len(lists[list_idx]):
        next_val = lists[list_idx][elem_idx + 1]
        heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
```

**Example - Merge K Sorted Lists (LC 23):**
```python
def mergeKLists(lists):
    heap = []
    dummy = ListNode(0)
    curr = dummy
    
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

---

## Pattern 3: Two Heaps

**Template:**
```python
import heapq

small = []  # Max heap (negated) - smaller half
large = []  # Min heap - larger half

def add(num):
    heapq.heappush(small, -num)
    heapq.heappush(large, -heapq.heappop(small))
    
    if len(large) > len(small):
        heapq.heappush(small, -heapq.heappop(large))

def median():
    if len(small) > len(large):
        return -small[0]
    return (-small[0] + large[0]) / 2
```

**Example - Find Median from Data Stream (LC 295):**
```python
class MedianFinder:
    def __init__(self):
        self.small = []  # Max heap (negated)
        self.large = []  # Min heap
    
    def addNum(self, num):
        heapq.heappush(self.small, -num)
        heapq.heappush(self.large, -heapq.heappop(self.small))
        
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))
    
    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

---

## Pattern 4: Greedy + Heap

**Template:**
```python
import heapq

# Sort by relevant criteria
items.sort(key=lambda x: x[0])

heap = []

for item in items:
    # Remove expired entries
    while heap and CONDITION(heap[0], item):
        heapq.heappop(heap)
    
    # Add current item
    heapq.heappush(heap, item_value)
    
    # Track result
    result = max(result, len(heap))
```

**Example - Meeting Rooms II (LC 253):**
```python
def minMeetingRooms(intervals):
    intervals.sort(key=lambda x: x[0])
    end_times = []
    
    for start, end in intervals:
        if end_times and end_times[0] <= start:
            heapq.heappop(end_times)
        heapq.heappush(end_times, end)
    
    return len(end_times)
```

---

## Pattern 5: Lazy Deletion

**Template:**
```python
import heapq
from collections import defaultdict

heap = []
deleted = defaultdict(int)

def remove(val):
    deleted[val] += 1

def clean():
    while heap and deleted[heap[0]] > 0:
        deleted[heap[0]] -= 1
        heapq.heappop(heap)

def top():
    clean()
    return heap[0]
```

**Example - Sliding Window Maximum (LC 239):**
```python
def maxSlidingWindow(nums, k):
    max_heap = []
    deleted = defaultdict(int)
    result = []
    
    for i in range(len(nums)):
        heapq.heappush(max_heap, -nums[i])
        
        if i >= k:
            deleted[nums[i - k]] += 1
        
        while max_heap and deleted[-max_heap[0]] > 0:
            deleted[-max_heap[0]] -= 1
            heapq.heappop(max_heap)
        
        if i >= k - 1:
            result.append(-max_heap[0])
    
    return result
```

---

## 🎯 Quick Selection Guide

| Problem Type | Pattern |
|--------------|---------|
| K largest/smallest | 1. Top K |
| Merge K sorted sources | 2. K-way Merge |
| Find median / balance halves | 3. Two Heaps |
| Scheduling / best next choice | 4. Greedy + Heap |
| Sliding window + heap | 5. Lazy Deletion |

---

## 🔑 Python Heap Cheat Sheet

```python
import heapq

# MIN HEAP (default)
heapq.heappush(heap, val)
smallest = heapq.heappop(heap)
peek = heap[0]

# MAX HEAP (negate values)
heapq.heappush(heap, -val)
largest = -heapq.heappop(heap)
peek = -heap[0]

# Heapify list
heapq.heapify(list)  # O(n)

# Push and pop in one operation
heapq.heapreplace(heap, val)  # pop then push
heapq.heappushpop(heap, val)  # push then pop
```


