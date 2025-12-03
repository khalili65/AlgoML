# 🏔️ Pattern 3: Two Heaps

---

## When to Use

- Find median in data stream
- Balance two halves of data
- Track running median/middle
- Problems requiring quick access to both min AND max of different parts

---

## The Core Idea

```
Split data into TWO halves:
┌─────────────────────┬─────────────────────┐
│    SMALLER HALF     │     LARGER HALF     │
│    (max heap)       │     (min heap)      │
│                     │                     │
│   Need the MAX      │    Need the MIN     │
│   of smaller half   │    of larger half   │
└─────────────────────┴─────────────────────┘
         ↓                     ↓
    Median is here!      Or average of both tops
```

**Why this structure?**
- Median is the "middle" element
- We need quick access to the largest of the small half
- And the smallest of the large half
- These two might BE the median (or we average them)

---

## The Template

```python
import heapq

class TwoHeaps:
    def __init__(self):
        self.max_heap = []  # Left half (smaller elements) - NEGATED!
        self.min_heap = []  # Right half (larger elements)
    
    def add(self, num):
        # Step 1: Add to max_heap (smaller half)
        heapq.heappush(self.max_heap, -num)
        
        # Step 2: Balance - largest of small half should <= smallest of large half
        heapq.heappush(self.min_heap, -heapq.heappop(self.max_heap))
        
        # Step 3: Ensure sizes: len(max_heap) >= len(min_heap)
        if len(self.min_heap) > len(self.max_heap):
            heapq.heappush(self.max_heap, -heapq.heappop(self.min_heap))
    
    def get_median(self):
        if len(self.max_heap) > len(self.min_heap):
            return -self.max_heap[0]
        return (-self.max_heap[0] + self.min_heap[0]) / 2
```

---

## Visual Walkthrough

```
Data stream: 5, 2, 8, 1, 9

After 5:
  max_heap = [-5]  (represents [5])
  min_heap = []
  Median = 5

After 2:
  max_heap = [-2]  (represents [2])
  min_heap = [5]
  Median = (2 + 5) / 2 = 3.5

After 8:
  max_heap = [-5, -2]  (represents [2, 5], max is 5)
  min_heap = [8]
  Median = 5

After 1:
  max_heap = [-2, -1]  (represents [1, 2], max is 2)
  min_heap = [5, 8]
  Median = (2 + 5) / 2 = 3.5

After 9:
  max_heap = [-5, -2, -1]  (represents [1, 2, 5], max is 5)
  min_heap = [8, 9]
  Median = 5

The sorted data: [1, 2, 5, 8, 9]
                      ↑
                   Median!
```

---

## Why Negate for Max Heap?

Python only has min heap. To simulate max heap, we negate values:

```python
# To store 5 in a "max heap":
heapq.heappush(max_heap, -5)  # Store -5

# To get the maximum:
max_val = -max_heap[0]  # Negate back: -(-5) = 5

# To pop the maximum:
max_val = -heapq.heappop(max_heap)  # -(-5) = 5
```

**Why it works:**
```
Real values:     5, 3, 8, 1
Stored (negated): -5, -3, -8, -1

Min heap order of negated: [-8, -5, -3, -1]
This means real max (8) is at top!

When we pop -8 and negate: -(-8) = 8 ✓
```

---

## Example 1: Find Median from Data Stream (LC 295)

**Problem:** Design a class that supports addNum() and findMedian().

```python
class MedianFinder:
    def __init__(self):
        self.small = []  # Max heap (negated) - smaller half
        self.large = []  # Min heap - larger half
    
    def addNum(self, num):
        # Add to small (max heap)
        heapq.heappush(self.small, -num)
        
        # Ensure: max of small <= min of large
        if self.small and self.large and (-self.small[0] > self.large[0]):
            heapq.heappush(self.large, -heapq.heappop(self.small))
        
        # Balance sizes: small can have at most 1 more than large
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))
    
    def findMedian(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

---

## Example 2: Sliding Window Median (LC 480)

**Problem:** Find median of each sliding window of size k.

```python
from collections import defaultdict

def medianSlidingWindow(nums, k):
    small = []  # Max heap (negated)
    large = []  # Min heap
    result = []
    
    def get_median():
        if k % 2:
            return -small[0]
        return (-small[0] + large[0]) / 2
    
    def balance():
        # Ensure size difference is at most 1
        if len(small) > len(large) + 1:
            heapq.heappush(large, -heapq.heappop(small))
        elif len(large) > len(small):
            heapq.heappush(small, -heapq.heappop(large))
    
    for i, num in enumerate(nums):
        # Add number
        if not small or num <= -small[0]:
            heapq.heappush(small, -num)
        else:
            heapq.heappush(large, num)
        
        balance()
        
        # Window is full
        if i >= k - 1:
            result.append(get_median())
            
            # Remove outgoing element (conceptually)
            # Note: Full implementation needs lazy deletion
            out = nums[i - k + 1]
            # ... lazy deletion logic here
    
    return result
```

**Note:** Full solution requires lazy deletion (Pattern 5) because we can't directly remove from heap.

---

## Example 3: IPO (LC 502)

**Problem:** Maximize capital by choosing at most k projects.

```python
def findMaximizedCapital(k, w, profits, capital):
    # Max heap of profits for affordable projects
    available = []
    # Min heap of (capital_needed, profit) for all projects
    projects = [(c, p) for c, p in zip(capital, profits)]
    heapq.heapify(projects)
    
    for _ in range(k):
        # Move all affordable projects to available heap
        while projects and projects[0][0] <= w:
            c, p = heapq.heappop(projects)
            heapq.heappush(available, -p)  # Max heap for profit
        
        # Pick the most profitable available project
        if available:
            w += -heapq.heappop(available)
        else:
            break  # No affordable project
    
    return w
```

---

## The Balancing Rules

```
┌─────────────────────────────────────────────────────────────┐
│   TWO HEAPS BALANCING RULES                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Rule 1: ORDERING                                          │
│   max(small) <= min(large)                                  │
│   All elements in small <= All elements in large            │
│                                                             │
│   Rule 2: SIZE BALANCE                                      │
│   |len(small) - len(large)| <= 1                            │
│   Usually: len(small) >= len(large)                         │
│                                                             │
│   Rule 3: MEDIAN LOCATION                                   │
│   If sizes equal: median = average of both tops             │
│   If small has more: median = top of small                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(log n) per add
       O(1) for get_median

Space: O(n) - storing all elements in two heaps
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to negate for max heap
   heappush(small, num)    # WRONG!
   heappush(small, -num)   # RIGHT

❌ Mistake 2: Forgetting to negate when reading
   return small[0]         # WRONG! Returns negative
   return -small[0]        # RIGHT

❌ Mistake 3: Wrong balancing logic
   # Always check BOTH ordering and size constraints
   # Order: ensure max of small <= min of large
   # Size: ensure difference is at most 1
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   TWO HEAPS PATTERN                                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   small = []  # Max heap (negated) - smaller half           │
│   large = []  # Min heap - larger half                      │
│                                                             │
│   def add(num):                                             │
│       heappush(small, -num)                                 │
│       # Balance ordering                                    │
│       if -small[0] > large[0]:                              │
│           heappush(large, -heappop(small))                  │
│       # Balance sizes                                       │
│       if len(large) > len(small):                           │
│           heappush(small, -heappop(large))                  │
│                                                             │
│   def median():                                             │
│       if len(small) > len(large): return -small[0]          │
│       return (-small[0] + large[0]) / 2                     │
│                                                             │
│   KEY: small's max and large's min are the "middle"         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


