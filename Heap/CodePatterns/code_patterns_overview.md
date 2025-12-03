# 🏔️ Heap Code Patterns - Template-Based Approach

Heaps are used when you need **quick access to min/max** element. Here are the **5 core code patterns**.

---

## 📁 Folder Structure

```
CodePatterns/
├── code_patterns_overview.md    ← You are here
├── all_templates_reference.md   ← Quick reference
├── 1_TopK/
│   └── top_k_pattern.md
├── 2_KWayMerge/
│   └── k_way_merge_pattern.md
├── 3_TwoHeaps/
│   └── two_heaps_pattern.md
├── 4_GreedyHeap/
│   └── greedy_heap_pattern.md
└── 5_LazyDeletion/
    └── lazy_deletion_pattern.md
```

---

## 🐍 Python Heap Basics

```python
import heapq

# Python has MIN heap by default
nums = [3, 1, 4, 1, 5]
heapq.heapify(nums)              # Convert list to heap: O(n)
heapq.heappush(nums, 2)          # Add element: O(log n)
smallest = heapq.heappop(nums)   # Remove & return smallest: O(log n)
peek = nums[0]                   # Look at smallest (don't remove): O(1)

# For MAX heap: negate values!
max_heap = []
heapq.heappush(max_heap, -5)     # Push -5 (actually storing 5)
largest = -heapq.heappop(max_heap)  # Pop and negate back
```

---

## 📊 The 5 Core Heap Code Patterns

| # | Pattern | Key Idea | Example Problems |
|---|---------|----------|------------------|
| 1 | Top K Elements | Keep heap of size K | LC 215, 347, 692, 973 |
| 2 | K-way Merge | Merge K sorted sources | LC 23, 378, 373 |
| 3 | Two Heaps | Balance max-heap + min-heap | LC 295, 480, 502 |
| 4 | Greedy + Heap | Always pick optimal next | LC 253, 621, 767 |
| 5 | Lazy Deletion | Mark deleted, skip later | LC 480, 218 |

---

## 🧠 Decision Flowchart: Which Pattern to Use?

```
START: What does the problem ask for?
│
├─ "Find K largest/smallest elements" ──────► Pattern 1 (Top K)
│
├─ "Merge K sorted lists/arrays" ───────────► Pattern 2 (K-way Merge)
│
├─ "Find median / balance two halves" ──────► Pattern 3 (Two Heaps)
│
├─ "Schedule tasks / always pick best" ─────► Pattern 4 (Greedy + Heap)
│
├─ "Sliding window + need removal" ─────────► Pattern 5 (Lazy Deletion)
│
└─ Not sure? Ask: "Do I need quick access to min or max?"
   If yes → probably needs a heap!
```

---

## 📋 Quick Reference: All 5 Templates

```python
# PATTERN 1: Top K (Min Heap of size K for K largest)
for num in nums:
    heapq.heappush(heap, num)
    if len(heap) > k:
        heapq.heappop(heap)

# PATTERN 2: K-way Merge
heap = [(lists[i][0], i, 0) for i in range(len(lists)) if lists[i]]
while heap:
    val, list_idx, elem_idx = heapq.heappop(heap)
    # push next from same list

# PATTERN 3: Two Heaps (Median)
heapq.heappush(max_heap, -num)  # small half
heapq.heappush(min_heap, -heapq.heappop(max_heap))
# balance sizes

# PATTERN 4: Greedy + Heap
tasks.sort()
for task in tasks:
    while heap and CONDITION:
        heapq.heappop(heap)
    heapq.heappush(heap, task)

# PATTERN 5: Lazy Deletion
deleted = {}
def remove(val): deleted[val] = deleted.get(val, 0) + 1
def clean(): 
    while heap and deleted.get(heap[0], 0) > 0:
        deleted[heapq.heappop(heap)] -= 1
```

---

## 🔑 Golden Rules for Heaps

```
┌─────────────────────────────────────────────────────────────┐
│   HEAP GOLDEN RULES:                                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Python heapq is MIN heap                               │
│      For MAX heap: negate values!                           │
│                                                             │
│   2. For K largest: use MIN heap of size K                  │
│      For K smallest: use MAX heap of size K                 │
│                                                             │
│   3. Heap elements can be tuples: (priority, data)          │
│      Sorted by first element, then second, etc.             │
│                                                             │
│   4. Can't remove arbitrary element → use Lazy Deletion     │
│                                                             │
│   5. heapify() is O(n), not O(n log n)!                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary

| Pattern | Key Line | When to Use |
|---------|----------|-------------|
| 1. Top K | `if len(heap) > k: heappop()` | K largest/smallest |
| 2. K-way Merge | `(val, list_idx, elem_idx)` | Merge sorted sources |
| 3. Two Heaps | `max_heap + min_heap` | Median, balance halves |
| 4. Greedy | `sort + heap` | Scheduling, best next |
| 5. Lazy Delete | `deleted = {}` | Remove from heap |

**Master these 5 templates, and you can solve most heap problems!**


