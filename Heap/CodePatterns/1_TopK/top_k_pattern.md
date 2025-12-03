# 🏔️ Pattern 1: Top K Elements

---

## When to Use

- Find K largest elements
- Find K smallest elements
- Find Kth largest/smallest element
- Top K frequent elements
- K closest points

---

## The Template

```python
import heapq

def topKLargest(nums, k):
    min_heap = []
    
    for num in nums:
        heapq.heappush(min_heap, num)
        
        if len(min_heap) > k:
            heapq.heappop(min_heap)  # Remove smallest
    
    return min_heap  # Contains K largest elements
    # min_heap[0] is the Kth largest
```

---

## Why Min Heap for K Largest?

This is counterintuitive at first! Let me explain:

```
We want: K LARGEST elements
We use: MIN heap of size K
Why?   The SMALLEST in our heap = Kth LARGEST overall!

Think of it as a "bouncer" at a club:
- Club capacity = K people
- We want the K tallest people
- When someone new arrives:
  - If club not full → let them in
  - If club full → compare with SHORTEST person inside
    - If new person is taller → kick out shortest, let new person in
    - If new person is shorter → reject them

The shortest person in club = Kth tallest overall!
```

**Visual Walkthrough:**
```
nums = [3, 1, 4, 1, 5, 9, 2, 6], k = 3

Process 3: heap = [3]           (size 1 < k, just add)
Process 1: heap = [1, 3]        (size 2 < k, just add)
Process 4: heap = [1, 3, 4]     (size 3 = k, full now)
Process 1: heap = [1, 1, 3, 4]  → pop smallest → [1, 3, 4]
Process 5: heap = [1, 3, 4, 5]  → pop smallest → [3, 4, 5]
Process 9: heap = [3, 4, 5, 9]  → pop smallest → [4, 5, 9]
Process 2: 2 < 4 (heap min), add then pop → [4, 5, 9] (2 rejected)
Process 6: heap = [4, 5, 6, 9]  → pop smallest → [5, 6, 9]

Result: [5, 6, 9] = top 3 largest
heap[0] = 5 = 3rd largest
```

---

## Example 1: Kth Largest Element (LC 215)

**Problem:** Find the kth largest element in an array.

```python
def findKthLargest(nums, k):
    min_heap = []
    
    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    
    return min_heap[0]  # Kth largest
```

**Why it works:**
```
After processing all elements:
- Heap has exactly K elements
- These are the K largest
- Heap[0] (the minimum of these K) = Kth largest overall
```

---

## Example 2: Top K Frequent Elements (LC 347)

**Problem:** Return the k most frequent elements.

```python
from collections import Counter

def topKFrequent(nums, k):
    count = Counter(nums)
    
    # Heap of (frequency, num) - min heap by frequency
    min_heap = []
    
    for num, freq in count.items():
        heapq.heappush(min_heap, (freq, num))
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    
    return [num for freq, num in min_heap]
```

**Visual:**
```
nums = [1,1,1,2,2,3], k = 2

count = {1: 3, 2: 2, 3: 1}

Process (3, 1): heap = [(3, 1)]
Process (2, 2): heap = [(2, 2), (3, 1)]
Process (1, 3): heap = [(1, 3), (2, 2), (3, 1)] → pop → [(2, 2), (3, 1)]

Result: [2, 1] (top 2 frequent)
```

---

## Example 3: K Closest Points to Origin (LC 973)

**Problem:** Return K closest points to origin (0, 0).

```python
def kClosest(points, k):
    # Use MAX heap (negate distance) to keep K smallest distances
    max_heap = []
    
    for x, y in points:
        dist = -(x*x + y*y)  # Negate for max heap behavior
        heapq.heappush(max_heap, (dist, x, y))
        if len(max_heap) > k:
            heapq.heappop(max_heap)
    
    return [[x, y] for dist, x, y in max_heap]
```

**Why MAX heap here?**
```
We want: K SMALLEST distances
We use: MAX heap of size K
Why?   The LARGEST in our heap = Kth SMALLEST overall!

It's the opposite of finding K largest!
- K largest → use MIN heap (kick out smallest)
- K smallest → use MAX heap (kick out largest)
```

---

## Example 4: Kth Largest in a Stream (LC 703)

**Problem:** Design a class to find kth largest in a stream.

```python
class KthLargest:
    def __init__(self, k, nums):
        self.k = k
        self.min_heap = []
        
        for num in nums:
            self.add(num)
    
    def add(self, val):
        heapq.heappush(self.min_heap, val)
        if len(self.min_heap) > self.k:
            heapq.heappop(self.min_heap)
        return self.min_heap[0]
```

---

## Example 5: Top K Frequent Words (LC 692)

**Problem:** Return K most frequent words. If same frequency, sort alphabetically.

```python
from collections import Counter

def topKFrequent(words, k):
    count = Counter(words)
    
    # Custom comparison: (-freq, word) for max freq, then alphabetical
    heap = []
    
    for word, freq in count.items():
        # Negate freq for max heap, word stays for alphabetical
        heapq.heappush(heap, (-freq, word))
    
    return [heapq.heappop(heap)[1] for _ in range(k)]
```

---

## The Two Variations

```
┌─────────────────────────────────────────────────────────────┐
│   TOP K VARIATIONS                                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Finding K LARGEST:                                        │
│   • Use MIN heap of size K                                  │
│   • Push all elements                                       │
│   • When size > K, pop (removes smallest)                   │
│   • Result: K largest remain, heap[0] = Kth largest         │
│                                                             │
│   Finding K SMALLEST:                                       │
│   • Use MAX heap of size K (negate values!)                 │
│   • Push all elements (negated)                             │
│   • When size > K, pop (removes "smallest" = largest real)  │
│   • Result: K smallest remain                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n log k)
       - n elements to process
       - Each push/pop is O(log k) since heap size is k

Space: O(k)
       - Heap stores at most k elements

Compare to sorting: O(n log n) time, O(n) space
Top K is better when k << n!
```

---

## Common Mistakes

```
❌ Mistake 1: Using wrong heap type
   K largest → MIN heap (not max!)
   K smallest → MAX heap (not min!)

❌ Mistake 2: Forgetting to negate for max heap
   heappush(heap, -val)  # Don't forget the minus!
   result = -heappop(heap)  # Negate back!

❌ Mistake 3: Checking size before push
   # WRONG:
   if len(heap) < k:
       heappush(heap, num)
   
   # RIGHT:
   heappush(heap, num)
   if len(heap) > k:
       heappop(heap)
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   TOP K PATTERN                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   min_heap = []                                             │
│                                                             │
│   for num in nums:                                          │
│       heapq.heappush(min_heap, num)                         │
│       if len(min_heap) > k:                                 │
│           heapq.heappop(min_heap)                           │
│                                                             │
│   # min_heap now contains K largest                         │
│   # min_heap[0] is the Kth largest                          │
│                                                             │
│   KEY INSIGHT:                                              │
│   K largest → MIN heap (kick out smallest)                  │
│   K smallest → MAX heap (kick out largest)                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


