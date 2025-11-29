# 🎬 Tutorial Script: LC 57 — Insert Interval (Direct Template Application)

---

## Part 0 – Opening

**(Instructor on camera.)**

**Instructor:**
"Welcome back! Now that we've learned the **Insert Interval** pattern, let's apply it to the classic LeetCode problem: **LC 57 - Insert Interval**.

This is *the* problem the pattern was designed for. Direct application!"

---

## Part 1 – The Problem Statement

**(Visual: LeetCode problem.)**

**Instructor:**
"Here's the problem:

> You are given an array of non-overlapping intervals `intervals` where `intervals[i] = [start_i, end_i]` represent the start and end of the i-th interval, and intervals is sorted in ascending order by start_i.  
> You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.  
> Insert newInterval into intervals such that intervals is still sorted and non-overlapping (merge overlapping intervals if necessary)."

**Example 1:**
```
Input:  intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

**Example 2:**
```
Input:  intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: The new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

---

## Part 2 – Quick Pattern Review

**(Visual: Pattern Reference Card.)**

**Instructor:**
"Let's recall our 3-phase pattern for Insert Interval:"

```
┌─────────────────────────────────────────────────────────────┐
│              INSERT INTERVAL PATTERN                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PHASE 1: Add BEFORE intervals                              │
│     while interval.end < new.start                          │
│                                                             │
│  PHASE 2: MERGE overlapping intervals                       │
│     while interval.start <= new.end                         │
│     → Expand new with min/max                               │
│                                                             │
│  PHASE 3: Add AFTER intervals                               │
│     Add all remaining                                       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Three sequential while loops                          │
│  TIME: O(n)    SPACE: O(n)                                  │
└─────────────────────────────────────────────────────────────┘
```

**Quick Note on Phase 2's Condition:**

"The **full overlap condition** requires TWO checks:
```
new.start <= interval.end  AND  interval.start <= new.end
```

But Phase 2 only checks `interval.start <= new.end`. Why?

Because when Phase 1 **stops** (its condition becomes false), we know:
```
NOT (interval.end < new.start)  →  interval.end >= new.start  →  new.start <= interval.end ✓
```

Phase 1 stopping **guarantees** the first condition! So Phase 2 only checks the second.

💡 **Tip:** If you find the simplified condition confusing, you can always use the full overlap condition in Phase 2 — it's also 100% correct!"

---

## Part 3 – Applying the Template Step by Step

**(Visual: walkthrough.)**

**Instructor:**
"Let's trace through Example 2."

```
intervals = [[1,2], [3,5], [6,7], [8,10], [12,16]]
newInterval = [4,8]
```

### Phase 1: Add BEFORE (end < 4)

```
[1,2] → 2 < 4? YES → Add to result
result = [[1,2]]

[3,5] → 5 < 4? NO → Stop Phase 1
```

### Phase 2: Merge Overlapping (start ≤ 8)

```
[3,5] → 3 ≤ 8? YES → Merge!
  new = [min(4,3), max(8,5)] = [3,8]

[6,7] → 6 ≤ 8? YES → Merge!
  new = [min(3,6), max(8,7)] = [3,8]

[8,10] → 8 ≤ 8? YES → Merge!
  new = [min(3,8), max(8,10)] = [3,10]

[12,16] → 12 ≤ 10? NO → Stop Phase 2

Add merged interval: [3,10]
result = [[1,2], [3,10]]
```

### Phase 3: Add AFTER (remaining)

```
[12,16] → Add to result
result = [[1,2], [3,10], [12,16]]
```

**Final Answer:** `[[1,2], [3,10], [12,16]]` ✓

---

## Part 4 – The Complete Solution

**(Visual: Python code.)**

**Instructor:**
"Here's the complete solution mapping directly to our template."

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        result = []
        i = 0
        n = len(intervals)
        
        # Phase 1: Add all intervals that end BEFORE newInterval starts
        while i < n and intervals[i][1] < newInterval[0]:
            result.append(intervals[i])
            i += 1
        
        # Phase 2: Merge all overlapping intervals
        while i < n and intervals[i][0] <= newInterval[1]:
            newInterval[0] = min(newInterval[0], intervals[i][0])
            newInterval[1] = max(newInterval[1], intervals[i][1])
            i += 1
        result.append(newInterval)
        
        # Phase 3: Add all remaining intervals
        while i < n:
            result.append(intervals[i])
            i += 1
        
        return result
```

---

## Part 5 – Alternative: Single-Loop Version

**Instructor:**
"Here's an alternative using a for-loop with early return:"

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        result = []
        
        for i, interval in enumerate(intervals):
            # Current interval ends before new starts → add directly
            if interval[1] < newInterval[0]:
                result.append(interval)
            
            # Current interval starts after new ends → add new, then rest
            elif interval[0] > newInterval[1]:
                result.append(newInterval)
                return result + intervals[i:]
            
            # Overlap → merge into newInterval
            else:
                newInterval[0] = min(newInterval[0], interval[0])
                newInterval[1] = max(newInterval[1], interval[1])
        
        # Add newInterval at the end (if not returned early)
        result.append(newInterval)
        return result
```

"Both work. The three-phase version is clearer for explaining."

---

## Part 6 – Edge Cases

**(Visual: Edge case examples.)**

**Instructor:**
"Let's verify our solution handles all edge cases."

### Edge Case 1: Empty Intervals
```
Input:  intervals = [], newInterval = [5,7]
Output: [[5,7]]
```
✓ Phase 1 & 2 loops don't execute, Phase 2 adds [5,7]

### Edge Case 2: Insert at Beginning
```
Input:  intervals = [[3,5],[8,10]], newInterval = [1,2]
Output: [[1,2],[3,5],[8,10]]
```
✓ Phase 1 adds nothing, Phase 2 adds [1,2], Phase 3 adds rest

### Edge Case 3: Insert at End
```
Input:  intervals = [[1,2],[3,5]], newInterval = [8,10]
Output: [[1,2],[3,5],[8,10]]
```
✓ Phase 1 adds all, Phase 2 adds [8,10], Phase 3 adds nothing

### Edge Case 4: New Interval Covers All
```
Input:  intervals = [[2,3],[4,5],[6,7]], newInterval = [1,10]
Output: [[1,10]]
```
✓ Phase 2 merges everything into [1,10]

### Edge Case 5: Insert in Gap (No Overlap)
```
Input:  intervals = [[1,2],[5,6]], newInterval = [3,4]
Output: [[1,2],[3,4],[5,6]]
```
✓ No merging needed, just insert in position

### Edge Case 6: Touching Intervals
```
Input:  intervals = [[1,5]], newInterval = [5,7]
Output: [[1,7]]
```
✓ 5 ≤ 5 means overlap → merge

---

## Part 7 – Complexity Analysis

**(Visual: Complexity table.)**

**Instructor:**
"Let's analyze time and space."

### Time Complexity: O(n)

| Phase | Iterations |
|-------|------------|
| Phase 1 | k intervals |
| Phase 2 | m intervals |
| Phase 3 | n-k-m intervals |
| **Total** | **O(n)** |

"Each interval is visited exactly once!"

### Space Complexity: O(n)

"Output can have up to n+1 intervals (if no merging occurs)."

**Note:** This is O(n), not O(n log n), because the input is already sorted!

---

## Part 8 – Common Mistakes

**Instructor:**
"Watch out for these common mistakes."

### ❌ Mistake 1: Wrong Phase 1 Condition
```python
# WRONG
while intervals[i][1] <= newInterval[0]:  # <= instead of <

# This misses the edge case [1,5] insert [5,7]
# 5 <= 5 is true, but they should merge!
```

### ❌ Mistake 2: Forgetting to Add newInterval
```python
# WRONG - forgetting to append newInterval after Phase 2
while i < n and intervals[i][0] <= newInterval[1]:
    # merge...
    i += 1
# Missing: result.append(newInterval) ← DON'T FORGET!
```

### ❌ Mistake 3: Modifying Input
```python
# CAREFUL - this modifies the original newInterval
newInterval[0] = min(...)  # This mutates the input!

# SAFER version (if you need to preserve input):
new = newInterval[:]  # Make a copy first
```

### ✓ Correct Pattern
```python
# Phase 1: end < start (no overlap, strictly before)
while i < n and intervals[i][1] < newInterval[0]:

# Phase 2: start <= end (overlap or touching)
while i < n and intervals[i][0] <= newInterval[1]:
```

---

## Part 9 – Wrap-Up

**Instructor:**
"This problem is the **foundation** for the Insert Interval pattern.

Key takeaways:

1. **Three phases**: Before → Merge → After
2. **Phase 1 condition**: `interval.end < new.start`
3. **Phase 2 condition**: `interval.start <= new.end`
4. **Use min/max** to expand during merge
5. **O(n) time** — no sorting needed!

This pattern extends to more complex problems like Range Module and Data Stream Intervals.

Thanks for watching!"

---

## Quick Reference

```python
def insert(intervals, newInterval):
    result = []
    i = 0
    n = len(intervals)
    
    # Phase 1: Before
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Phase 2: Merge
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Phase 3: After
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

| Aspect | Details |
|--------|---------|
| Pattern | Insert Interval (3-Phase) |
| Time | O(n) |
| Space | O(n) |
| Phase 1 | end < new.start |
| Phase 2 | start ≤ new.end |
| Key Operation | min/max to expand |

