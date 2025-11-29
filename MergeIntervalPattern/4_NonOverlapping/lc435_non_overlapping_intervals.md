# 🎬 Tutorial Script: LC 435 — Non-overlapping Intervals

---

## Part 1 – Problem Introduction

**(Visual: LeetCode problem header.)**

**Instructor:**
"Let's solve **LeetCode 435: Non-overlapping Intervals** — the classic problem that directly applies our greedy interval pattern!"

---

## Part 2 – Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:"

```
Given an array of intervals where intervals[i] = [start_i, end_i], 
return the minimum number of intervals you need to remove to make 
the rest of the intervals non-overlapping.
```

**(Visual: Examples.)**

```
Example 1:
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest don't overlap.

Example 2:
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make non-overlapping.

Example 3:
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: No overlap — touching at a point is OK!
```

---

## Part 3 – Quick Pattern Review

**(Visual: Pattern Reference Card.)**

**Instructor:**
"Let's recall our Non-overlapping Intervals pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│       NON-OVERLAPPING INTERVALS PATTERN                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT by END time (greedy choice!)                       │
│     intervals.sort(key=lambda x: x[1])                      │
│                                                             │
│  2. TRACK previous end                                      │
│     prev_end = -infinity                                    │
│                                                             │
│  3. FOR EACH interval:                                      │
│     if start >= prev_end → keep, update prev_end            │
│     else → remove, count++                                  │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Sort by END time enables greedy selection!            │
└─────────────────────────────────────────────────────────────┘
```

**Why sort by END time?**

"The interval that ends earlier leaves more room for future intervals. By always keeping the earlier-ending interval, we maximize how many we can keep!"

💡 **Tip:** If this feels counterintuitive, remember: "Finish early, make room for more!"

---

## Part 4 – Step-by-Step Walkthrough

**(Visual: Example 1 walkthrough.)**

**Instructor:**
"Let's trace through Example 1 step by step."

```
Input: [[1,2], [2,3], [3,4], [1,3]]
```

### Step 0: Sort by End Time
```
Before: [[1,2], [2,3], [3,4], [1,3]]
After:  [[1,2], [2,3], [1,3], [3,4]]
                       ↑
               [1,3] has end=3, same as [2,3]
```

### Step 1: Initialize
```
prev_end = -∞
count = 0
```

### Step 2: Process [1,2]
```
[1,2]: start=1
  1 >= -∞? YES
  ✓ KEEP this interval
  prev_end = 2
```

### Step 3: Process [2,3]
```
[2,3]: start=2
  2 >= 2? YES (touching is OK!)
  ✓ KEEP this interval
  prev_end = 3
```

### Step 4: Process [1,3]
```
[1,3]: start=1
  1 >= 3? NO
  ✗ OVERLAP! Remove this interval
  count = 1
  (prev_end stays 3)
```

### Step 5: Process [3,4]
```
[3,4]: start=3
  3 >= 3? YES
  ✓ KEEP this interval
  prev_end = 4
```

**Final Answer:** `1`

---

## Part 5 – Visual Understanding

**(Visual: Timeline showing the greedy choice.)**

**Instructor:**
"Let's visualize why we keep [1,2] and [2,3] instead of [1,3]:"

```
Timeline:
0   1   2   3   4   5
    |---|           [1,2] ends at 2
        |---|       [2,3] ends at 3
            |---|   [3,4] ends at 4
    |-------|       [1,3] ends at 3 (conflicts with [2,3]!)

If we keep [1,3]:
    |-------|
            |---|
    Only 2 intervals fit!

If we keep [1,2] and [2,3]:
    |---|---|---|
    All 3 intervals fit! ✓
```

**Instructor:**
"[1,2] ends earlier than [1,3], so it leaves room for [2,3]. The greedy choice of keeping shorter intervals pays off!"

---

## Part 6 – Complete Solution

**(Visual: Full code.)**

**Instructor:**
"Here's the complete solution:"

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    
    # Sort by END time - the greedy choice!
    intervals.sort(key=lambda x: x[1])
    
    count = 0
    prev_end = float('-inf')
    
    for start, end in intervals:
        if start >= prev_end:
            # No overlap - keep this interval
            prev_end = end
        else:
            # Overlap - must remove this interval
            count += 1
    
    return count
```

---

## Part 7 – Code Walkthrough

**(Visual: Annotated code.)**

**Instructor:**
"Let's break down the code:"

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0      # Edge case: empty input
    
    # KEY STEP: Sort by END time
    # This enables our greedy approach
    # Earlier ending = more room for future intervals
    intervals.sort(key=lambda x: x[1])
    
    count = 0                        # Count of removed intervals
    prev_end = float('-inf')         # End of last kept interval
    
    for start, end in intervals:
        if start >= prev_end:        # No overlap
            # Keep this interval
            prev_end = end           # Update our "boundary"
        else:                        # Overlap!
            # Remove this interval (don't update prev_end)
            count += 1
    
    return count
```

---

## Part 8 – Why >= and Not >?

**(Visual: Touching intervals.)**

**Instructor:**
"An important detail: we use `>=`, not `>`."

```
[1,2] and [2,3]:
    |---|
        |---|
        ↑
    They TOUCH at point 2, but don't OVERLAP!

start >= prev_end means:
  2 >= 2 → TRUE → Not overlapping ✓

If we used start > prev_end:
  2 > 2 → FALSE → Would incorrectly count as overlap!
```

**Key insight:** Two intervals touching at a single point are NOT overlapping. They can both be kept!

---

## Part 9 – Edge Cases

**(Visual: Edge case examples.)**

**Instructor:**
"Let's consider edge cases:"

### Case 1: All Same Intervals
```python
intervals = [[1,2], [1,2], [1,2]]
# Output: 2

After sorting: [[1,2], [1,2], [1,2]]
Keep first [1,2], prev_end = 2
[1,2]: 1 >= 2? NO → remove, count = 1
[1,2]: 1 >= 2? NO → remove, count = 2
```

### Case 2: No Overlaps
```python
intervals = [[1,2], [3,4], [5,6]]
# Output: 0

All intervals already non-overlapping!
```

### Case 3: All Overlapping
```python
intervals = [[1,4], [2,5], [3,6]]
# Output: 2

After sorting by end: [[1,4], [2,5], [3,6]]
Keep [1,4], prev_end = 4
[2,5]: 2 >= 4? NO → remove
[3,6]: 3 >= 4? NO → remove
```

### Case 4: Single Interval
```python
intervals = [[1,2]]
# Output: 0

Nothing to remove!
```

---

## Part 10 – Alternative: Count What We Keep

**(Visual: Alternative approach.)**

**Instructor:**
"Here's an alternative way to think about it — count what we KEEP:"

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[1])
    
    kept = 1  # Keep the first interval
    prev_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        start, end = intervals[i]
        if start >= prev_end:
            kept += 1
            prev_end = end
    
    return len(intervals) - kept
```

**Instructor:**
"Both approaches give the same answer:
- `count removals` directly
- `len(intervals) - kept`"

---

## Part 11 – Complexity Analysis

**Instructor:**
"Let's analyze:"

```
Time Complexity: O(n log n)
- Sorting: O(n log n)
- Single pass: O(n)
- Sorting dominates

Space Complexity: O(1) extra
- Only a few variables
- O(log n) if counting sort's stack
```

---

## Part 12 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**
"Watch out for these:"

```
❌ Mistake 1: Sorting by START instead of END
   Wrong: intervals.sort(key=lambda x: x[0])
   Right: intervals.sort(key=lambda x: x[1])

❌ Mistake 2: Using > instead of >=
   Wrong: if start > prev_end
   Right: if start >= prev_end
   (Touching intervals are NOT overlapping!)

❌ Mistake 3: Updating prev_end when removing
   Wrong: Always update prev_end
   Right: Only update when KEEPING an interval

❌ Mistake 4: Starting prev_end at 0
   Wrong: prev_end = 0 (fails for negative intervals)
   Right: prev_end = float('-inf') or intervals[0][1]
```

---

## Part 13 – Summary

**Instructor:**
"That's LC 435! Key takeaways:

1. **Sort by END time** — the greedy choice
2. **Keep intervals that start >= prev_end**
3. **Touching is OK** — use >= not >
4. Count removals OR (total - kept)

This greedy approach guarantees the optimal solution because keeping earlier-ending intervals maximizes how many we can fit!"

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│              LC 435 SOLUTION TEMPLATE                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  intervals.sort(key=lambda x: x[1])  # Sort by END!         │
│                                                             │
│  count = 0                                                  │
│  prev_end = float('-inf')                                   │
│                                                             │
│  for start, end in intervals:                               │
│      if start >= prev_end:                                  │
│          prev_end = end    # Keep, update boundary          │
│      else:                                                  │
│          count += 1        # Remove                         │
│                                                             │
│  return count                                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

