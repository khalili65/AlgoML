# 🏔️ Pattern 5: Lazy Deletion

---

## When to Use

- Need to "remove" elements from heap (heaps don't support this!)
- Sliding window problems with heap
- When actual removal is expensive or impossible
- Need to invalidate elements without restructuring heap

---

## The Problem

```
Python's heapq does NOT support removing arbitrary elements!

heap = [1, 3, 5, 7]
# I want to remove 3... but how?

heapq.remove(heap, 3)  # ❌ Doesn't exist!
heap.remove(3)         # ❌ Breaks heap property!
```

**Solution: Don't actually remove — just mark as deleted!**

---

## The Template

```python
import heapq

class LazyHeap:
    def __init__(self):
        self.heap = []
        self.deleted = {}  # {value: count of deleted}
    
    def push(self, val):
        heapq.heappush(self.heap, val)
    
    def remove(self, val):
        # Don't actually remove — just mark as deleted
        self.deleted[val] = self.deleted.get(val, 0) + 1
    
    def top(self):
        self._clean()  # Remove deleted elements from top
        return self.heap[0] if self.heap else None
    
    def pop(self):
        self._clean()  # Remove deleted elements from top
        return heapq.heappop(self.heap) if self.heap else None
    
    def _clean(self):
        # Skip elements that were marked as deleted
        while self.heap and self.deleted.get(self.heap[0], 0) > 0:
            self.deleted[self.heap[0]] -= 1
            heapq.heappop(self.heap)
```

---

## Visual Understanding

```
heap = [1, 3, 5, 7]
Want to remove 3

Step 1: Mark 3 as deleted
  heap = [1, 3, 5, 7]
  deleted = {3: 1}
  
  3 is still in heap, but marked!

Step 2: Pop elements
  pop() → _clean() → 1 not in deleted → return 1
  heap = [3, 5, 7]
  
  pop() → _clean() → 3 is deleted! Skip it.
          heap = [5, 7]
          deleted = {3: 0}
          5 not in deleted → return 5
  
  pop() → return 7
```

**Key Insight:**
```
We don't remove immediately.
We remove LAZILY — only when the deleted element reaches the top.
This way, we don't need to search or restructure the heap!
```

---

## Example 1: Sliding Window Maximum (LC 239)

**Problem:** Find maximum in each sliding window of size k.

```python
from collections import defaultdict

def maxSlidingWindow(nums, k):
    max_heap = []  # Max heap (negated)
    deleted = defaultdict(int)
    result = []
    
    for i in range(len(nums)):
        # Add current element
        heapq.heappush(max_heap, -nums[i])
        
        # Mark outgoing element as deleted
        if i >= k:
            deleted[nums[i - k]] += 1
        
        # Clean: remove deleted elements from top
        while max_heap and deleted[-max_heap[0]] > 0:
            deleted[-max_heap[0]] -= 1
            heapq.heappop(max_heap)
        
        # Record maximum for current window
        if i >= k - 1:
            result.append(-max_heap[0])
    
    return result
```

**Visual:**
```
nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3

Window [1, 3, -1]:
  heap = [-3, -1, -1] (negated: [3, 1, -1])
  max = 3 ✓

Move window, remove 1, add -3:
  deleted = {1: 1}
  heap = [-3, -1, -1, 3] (with 1 marked deleted)
  _clean: -3 not deleted, so max = 3 ✓

Move window, remove 3, add 5:
  deleted = {1: 1, 3: 1}
  heap = [-5, -3, -1, -1, 3]
  _clean: -5 → 5 not deleted, max = 5 ✓
```

---

## Example 2: Sliding Window Median (LC 480)

**Problem:** Find median in each sliding window of size k.

```python
from collections import defaultdict

def medianSlidingWindow(nums, k):
    small = []  # Max heap (negated) - smaller half
    large = []  # Min heap - larger half
    deleted = defaultdict(int)
    result = []
    
    def clean(heap, is_max_heap):
        while heap:
            val = -heap[0] if is_max_heap else heap[0]
            if deleted[val] > 0:
                deleted[val] -= 1
                heapq.heappop(heap)
            else:
                break
    
    def get_median():
        if k % 2:
            return float(-small[0])
        return (-small[0] + large[0]) / 2.0
    
    def balance():
        # Balance sizes after cleaning
        clean(small, True)
        clean(large, False)
        
        if len(small) > len(large) + 1:
            heapq.heappush(large, -heapq.heappop(small))
            clean(small, True)
        elif len(large) > len(small):
            heapq.heappush(small, -heapq.heappop(large))
            clean(large, False)
    
    # Initialize first window
    for i in range(k):
        heapq.heappush(small, -nums[i])
    
    # Move half to large
    for _ in range(k // 2):
        heapq.heappush(large, -heapq.heappop(small))
    
    result.append(get_median())
    
    # Slide window
    for i in range(k, len(nums)):
        out_num = nums[i - k]
        in_num = nums[i]
        
        # Mark outgoing as deleted
        deleted[out_num] += 1
        
        # Add incoming
        if in_num <= -small[0]:
            heapq.heappush(small, -in_num)
        else:
            heapq.heappush(large, in_num)
        
        balance()
        result.append(get_median())
    
    return result
```

---

## Example 3: The Skyline Problem (LC 218)

**Problem:** Find building skyline silhouette.

```python
def getSkyline(buildings):
    # Events: (x, type, height)
    # type: 0 = start, 1 = end (process starts first)
    events = []
    for left, right, height in buildings:
        events.append((left, 0, height))  # Building starts
        events.append((right, 1, height))  # Building ends
    
    events.sort()
    
    max_heap = [0]  # Heights of active buildings
    deleted = defaultdict(int)
    result = []
    prev_max = 0
    
    i = 0
    while i < len(events):
        curr_x = events[i][0]
        
        # Process all events at same x
        while i < len(events) and events[i][0] == curr_x:
            x, event_type, height = events[i]
            
            if event_type == 0:  # Building starts
                heapq.heappush(max_heap, -height)
            else:  # Building ends
                deleted[height] += 1
            
            i += 1
        
        # Clean deleted heights
        while max_heap and deleted[-max_heap[0]] > 0:
            deleted[-max_heap[0]] -= 1
            heapq.heappop(max_heap)
        
        curr_max = -max_heap[0]
        
        if curr_max != prev_max:
            result.append([curr_x, curr_max])
            prev_max = curr_max
    
    return result
```

---

## When to Use Lazy Deletion

```
┌─────────────────────────────────────────────────────────────┐
│   USE LAZY DELETION WHEN:                                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ✅ Sliding window + heap                                  │
│      Elements leave window but can't be removed from heap   │
│                                                             │
│   ✅ Multiple heaps with shared elements                    │
│      Element removed from one but exists in other           │
│                                                             │
│   ✅ Priority changes over time                             │
│      Instead of updating, mark old as deleted, add new      │
│                                                             │
│   ❌ DON'T use if:                                          │
│      Simple top-k (no removal needed)                       │
│      Elements are never invalidated                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  Amortized O(log n) per operation
       - Each element is pushed once and popped once
       - Lazy deletion just postpones the pop

Space: O(n) for heap + O(k) for deleted map
       - k = number of unique deleted values
```

---

## Common Mistakes

```
❌ Mistake 1: Cleaning at wrong time
   # Clean BEFORE accessing top, not after
   def top(self):
       self._clean()  # Clean first!
       return self.heap[0]

❌ Mistake 2: Forgetting to track deletion count
   deleted[val] += 1  # Not just deleted[val] = True
   # Because same value might appear multiple times!

❌ Mistake 3: Not cleaning after balance operations
   # After moving elements between heaps, clean again
   heappush(large, -heappop(small))
   clean(small)  # Don't forget!
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LAZY DELETION PATTERN                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   deleted = {}                                              │
│                                                             │
│   def remove(val):                                          │
│       deleted[val] = deleted.get(val, 0) + 1                │
│                                                             │
│   def clean():                                              │
│       while heap and deleted.get(heap[0], 0) > 0:           │
│           deleted[heap[0]] -= 1                             │
│           heappop(heap)                                     │
│                                                             │
│   def top():                                                │
│       clean()  # Always clean before accessing!             │
│       return heap[0]                                        │
│                                                             │
│   KEY: Mark as deleted, skip when it reaches top            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


