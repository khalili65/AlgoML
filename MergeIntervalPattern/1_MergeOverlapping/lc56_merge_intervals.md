# 🎬 Tutorial Script: LC 56 — Merge Intervals (Direct Template Application)

---

## Part 0 – Opening

**(Instructor on camera.)**

**Instructor:**
"Welcome back! Now that we've learned the **Merge Overlapping Intervals** pattern, let's apply it to the classic LeetCode problem: **LC 56 - Merge Intervals**.

This is literally *the* problem that the pattern was designed for. So this will be a direct application of our template."

---

## Part 1 – The Problem Statement

**(Visual: LeetCode problem.)**

**Instructor:**
"Here's the problem:

> Given an array of intervals where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input."

**Example 1:**
```
Input:  [[1,3], [2,6], [8,10], [15,18]]
Output: [[1,6], [8,10], [15,18]]

Explanation: [1,3] and [2,6] overlap, so merge them into [1,6].
```

**Example 2:**
```
Input:  [[1,4], [4,5]]
Output: [[1,5]]

Explanation: [1,4] and [4,5] touch at point 4, so they overlap.
```

---

## Part 2 – Quick Pattern Review

**(Visual: Pattern Reference Card.)**

**Instructor:**
"Let's quickly recall our 4-step pattern for merging overlapping intervals:"

```
┌─────────────────────────────────────────────────────────────┐
│           MERGE OVERLAPPING INTERVALS PATTERN               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT by start time                                      │
│     intervals.sort(key=lambda x: x[0])                      │
│                                                             │
│  2. INITIALIZE with first interval                          │
│     merged = [intervals[0]]                                 │
│                                                             │
│  3. LOOP through remaining intervals                        │
│     for i in range(1, len(intervals)):                      │
│                                                             │
│  4. DECIDE: Merge or Add                                    │
│     if current[0] <= last[1]:   # OVERLAP                   │
│         last[1] = max(last[1], current[1])                  │
│     else:                        # NO OVERLAP               │
│         merged.append(current)                              │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  OVERLAP CONDITION:  current.start <= last.end              │
│  MERGE OPERATION:    last.end = max(last.end, current.end)  │
└─────────────────────────────────────────────────────────────┘
```

**Instructor:**
"This problem is a **perfect match** for our template. We don't need any customization!"

**Quick Note on the Overlap Condition:**

"You might wonder: 'Isn't the full overlap condition TWO checks?' Yes! The complete condition is:
```
last.start <= current.end  AND  current.start <= last.end
```

But because we **sorted by start time**, we know `last.start <= current.start <= current.end`, so the first condition is **always true**. We only need to check the second one!

💡 **Tip:** If the simplified condition feels confusing, you can use the full condition instead — it's also correct!"

---

## Part 3 – Applying the Template Step by Step

**(Visual: Side-by-side — template on left, problem walkthrough on right.)**

**Instructor:**
"Let's walk through the example with our pattern."

```
Input: [[1,3], [2,6], [8,10], [15,18]]
```

### Step 1: Sort by Start Time

```
Already sorted: [[1,3], [2,6], [8,10], [15,18]]
```

"In this example, the input is already sorted. But always sort first — don't assume!"

### Step 2: Initialize with First Interval

```
merged = [[1,3]]
```

### Step 3 & 4: Loop and Decide

**Iteration 1: Check `[2,6]`**
```
current = [2,6]
last = [1,3]

Overlap check: current[0] <= last[1]
               2 <= 3 ✓ YES, overlap!

Merge: last[1] = max(3, 6) = 6
merged = [[1,6]]
```

**Iteration 2: Check `[8,10]`**
```
current = [8,10]
last = [1,6]

Overlap check: 8 <= 6? NO, no overlap.

Add new interval.
merged = [[1,6], [8,10]]
```

**Iteration 3: Check `[15,18]`**
```
current = [15,18]
last = [8,10]

Overlap check: 15 <= 10? NO, no overlap.

Add new interval.
merged = [[1,6], [8,10], [15,18]]
```

**Final Answer:** `[[1,6], [8,10], [15,18]]` ✓

---

## Part 4 – The Complete Solution

**(Visual: Python code.)**

**Instructor:**
"Here's the complete solution. Notice how it maps directly to our template."

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        # Edge case
        if len(intervals) <= 1:
            return intervals
        
        # Step 1: Sort by start time
        intervals.sort(key=lambda x: x[0])
        
        # Step 2: Initialize with first interval
        merged = [intervals[0]]
        
        # Step 3: Loop through remaining intervals
        for i in range(1, len(intervals)):
            current = intervals[i]
            last = merged[-1]
            
            # Step 4: Decide - Merge or Add
            if current[0] <= last[1]:
                # Overlap: extend the end
                last[1] = max(last[1], current[1])
            else:
                # No overlap: add as new interval
                merged.append(current)
        
        return merged
```

---

## Part 5 – Alternative: Cleaner Loop with `for current in intervals[1:]`

**Instructor:**
"Here's a slightly cleaner version using Python's slice syntax:"

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        if not intervals:
            return []
        
        intervals.sort(key=lambda x: x[0])
        merged = [intervals[0]]
        
        for current in intervals[1:]:
            last = merged[-1]
            
            if current[0] <= last[1]:
                last[1] = max(last[1], current[1])
            else:
                merged.append(current)
        
        return merged
```

"Both versions work. Use whichever feels more natural to you."

---

## Part 6 – Edge Cases to Consider

**(Visual: Edge case examples.)**

**Instructor:**
"Let's think about edge cases our solution handles:"

### Edge Case 1: Empty Input
```
Input:  []
Output: []
```
"Handled by `if not intervals: return []`"

### Edge Case 2: Single Interval
```
Input:  [[1,5]]
Output: [[1,5]]
```
"Handled — the loop doesn't execute, we just return the first interval."

### Edge Case 3: All Intervals Overlap
```
Input:  [[1,4], [2,5], [3,6]]
Output: [[1,6]]
```
"Each interval extends the previous one."

### Edge Case 4: No Intervals Overlap
```
Input:  [[1,2], [4,5], [7,8]]
Output: [[1,2], [4,5], [7,8]]
```
"Each interval is added separately."

### Edge Case 5: Touching Intervals (like [1,4] and [4,5])
```
Input:  [[1,4], [4,5]]
Output: [[1,5]]
```
"Our condition `current[0] <= last[1]` correctly handles this! 4 <= 4 is true."

---

## Part 7 – Complexity Analysis

**(Visual: Complexity table.)**

**Instructor:**
"Let's analyze the time and space complexity."

### Time Complexity: O(n log n)

| Operation | Time |
|-----------|------|
| Sorting | O(n log n) |
| Single pass through intervals | O(n) |
| **Total** | **O(n log n)** |

"Dominated by the sorting step."

### Space Complexity: O(n)

"In the worst case (no overlaps), the output contains all n intervals."

*Note: Some languages sort in-place (O(1) extra space for sorting), but the output still requires O(n) space.*

---

## Part 8 – Common Mistakes

**Instructor:**
"Quick reminder of common mistakes to avoid:"

### ❌ Mistake 1: Forgetting to Sort
```python
# WRONG - forgot to sort!
merged = [intervals[0]]
for current in intervals[1:]:
    ...
```

### ❌ Mistake 2: Wrong Overlap Condition
```python
# WRONG - using < instead of <=
if current[0] < last[1]:  # Misses touching intervals!
```

### ❌ Mistake 3: Not Updating with `max()`
```python
# WRONG - just replacing the end
last[1] = current[1]  # What if current ends before last?

# CORRECT
last[1] = max(last[1], current[1])
```

---

## Part 9 – Wrap-Up

**Instructor:**
"This problem is the **poster child** for the Merge Overlapping Intervals pattern.

Key takeaways:

1. **Sort first** — always by start time
2. **Compare with the last merged interval** — that's the only one that could overlap
3. **Use `<=` for overlap** — handles touching intervals
4. **Use `max()` when extending** — don't assume the current interval ends later

This is a **must-know** problem for interviews. Practice it until the pattern feels automatic.

In the next problem, we'll see how to use this same merging logic as a *building block* for a harder problem: **Employee Free Time**.

Thanks for watching!"

---

## Quick Reference

```python
def merge(intervals):
    if not intervals:
        return []
    
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        last = merged[-1]
        if current[0] <= last[1]:
            last[1] = max(last[1], current[1])
        else:
            merged.append(current)
    
    return merged
```

| Aspect | Details |
|--------|---------|
| Pattern | Merge Overlapping Intervals |
| Time | O(n log n) |
| Space | O(n) |
| Key Insight | Sort + compare with last merged |
| Overlap Condition | `current.start <= last.end` |

