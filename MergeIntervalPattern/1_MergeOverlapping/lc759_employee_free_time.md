# 🎬 Tutorial Script: LC 759 — Employee Free Time (Merge + Find Gaps)

---

## Part 0 – Opening

**(Instructor on camera.)**

**Instructor:**
"Welcome back! Now let's tackle a more interesting problem: **LC 759 - Employee Free Time**.

This is a **Hard** problem on LeetCode, but don't worry — we'll use our **Merge Overlapping Intervals** pattern as a building block, and it becomes surprisingly straightforward."

---

## Part 1 – The Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:

> We have a list of employees, and each employee has a list of non-overlapping working intervals (sorted).  
> Find the **common free time** for all employees — the intervals when **nobody is working**.

Think of it like this: You're a manager scheduling a team meeting. You need to find time slots when *everyone* is available."

**Example 1:**
```
Input: schedule = [[[1,2],[5,6]], [[1,3]], [[4,10]]]

Employee 1: works [1,2] and [5,6]
Employee 2: works [1,3]
Employee 3: works [4,10]

Visualized on timeline:
Emp1:   ██     ██
Emp2:   ███
Emp3:         ███████
        ─────────────────
Time:   1 2 3 4 5 6 7 8 9 10

Free time: [3,4] — the gap where nobody is working!

Output: [[3,4]]
```

**Example 2:**
```
Input: schedule = [[[1,3],[6,7]], [[2,4]], [[2,5],[9,12]]]

Output: [[5,6], [7,9]]
```

---

## Part 2 – Breaking Down the Problem

**(Visual: Step-by-step thinking.)**

**Instructor:**
"Let's think about this step by step.

What are we really looking for? **Gaps** — time when no one is working.

How do we find gaps? We need to know when *everyone combined* is working, then look for holes in that schedule.

This sounds like:
1. **Flatten** all employee schedules into one big list of intervals
2. **Merge** all overlapping intervals (our pattern!)
3. **Find gaps** between the merged intervals

It's our merge pattern with one extra step at the end!"

---

## Part 3 – Quick Pattern Review

**(Visual: Pattern Reference.)**

**Instructor:**
"Let's recall our merge pattern and see how we'll extend it:"

```
MERGE OVERLAPPING PATTERN:
1. Sort by start time
2. Initialize with first interval
3. Loop through remaining
4. Decide: Merge or Add → if current.start <= last.end

+ NEW STEP:
5. Find gaps between merged intervals
```

**Recall:** The full overlap condition is `last.start <= current.end AND current.start <= last.end`. Since we sort by start time, we know `last.start <= current.start`, so the first condition is always true. We only check `current.start <= last.end`!

💡 **Tip:** If the simplified condition feels confusing, you can use the full overlap condition — it's also correct!

**For finding gaps:**
```
Gap between merged[i] and merged[i+1]:
  gap_start = merged[i][1]      # end of current interval
  gap_end   = merged[i+1][0]    # start of next interval
  
  If gap_start < gap_end → there's a gap!
```

---

## Part 4 – Walkthrough with Example

**(Visual: Step-by-step with the example.)**

**Instructor:**
"Let's walk through Example 1:"

```
Input: [[[1,2],[5,6]], [[1,3]], [[4,10]]]
```

### Step 1: Flatten All Intervals

"First, combine all employee schedules into one list:"

```python
Employee 1: [1,2], [5,6]
Employee 2: [1,3]
Employee 3: [4,10]

Flattened: [[1,2], [5,6], [1,3], [4,10]]
```

### Step 2: Sort by Start Time

```python
Sorted: [[1,2], [1,3], [4,10], [5,6]]
```

### Step 3: Merge Overlapping Intervals

"Now apply our merge pattern:"

```
merged = [[1,2]]

Check [1,3]:
  1 <= 2? YES → merge
  merged = [[1,3]]

Check [4,10]:
  4 <= 3? NO → add new
  merged = [[1,3], [4,10]]

Check [5,6]:
  5 <= 10? YES → merge
  merged = [[1,3], [4,10]]  (10 is already >= 6, no change)

Final merged: [[1,3], [4,10]]
```

### Step 4: Find Gaps Between Merged Intervals

```
Between [1,3] and [4,10]:
  gap_start = 3
  gap_end = 4
  3 < 4 → Gap exists: [3,4] ✓

Output: [[3,4]]
```

---

## Part 5 – The Complete Solution

**(Visual: Python code.)**

**Instructor:**
"Here's the complete solution. I'll add detailed comments mapping each section to our pattern."

```python
"""
Definition for an Interval:
class Interval:
    def __init__(self, start=None, end=None):
        self.start = start
        self.end = end
"""

class Solution:
    def employeeFreeTime(self, schedule: '[[Interval]]') -> '[Interval]':
        
        # ============================================
        # STEP 1: Flatten all intervals into one list
        # ============================================
        all_intervals = []
        for employee in schedule:
            for interval in employee:
                all_intervals.append([interval.start, interval.end])
        
        # Edge case: no intervals
        if not all_intervals:
            return []
        
        # ============================================
        # STEP 2: Sort by start time (Pattern Step 1)
        # ============================================
        all_intervals.sort(key=lambda x: x[0])
        
        # ============================================
        # STEP 3: Merge overlapping (Pattern Steps 2-4)
        # ============================================
        merged = [all_intervals[0]]
        
        for current in all_intervals[1:]:
            last = merged[-1]
            
            if current[0] <= last[1]:
                # Overlap: extend
                last[1] = max(last[1], current[1])
            else:
                # No overlap: add new
                merged.append(current)
        
        # ============================================
        # STEP 4: Find gaps between merged intervals
        # ============================================
        free_time = []
        
        for i in range(len(merged) - 1):
            gap_start = merged[i][1]       # end of current
            gap_end = merged[i + 1][0]     # start of next
            
            if gap_start < gap_end:
                free_time.append(Interval(gap_start, gap_end))
        
        return free_time
```

---

## Part 6 – Alternative: One-Pass with Heap (More Elegant)

**(Visual: Alternative approach.)**

**Instructor:**
"There's a more elegant approach using a min-heap that processes intervals in sorted order without explicitly sorting. But the logic is the same — merge and find gaps."

```python
import heapq

class Solution:
    def employeeFreeTime(self, schedule: '[[Interval]]') -> '[Interval]':
        # Use heap to process intervals in order of start time
        # (start, end, employee_idx, interval_idx)
        heap = []
        
        for emp_idx, employee in enumerate(schedule):
            if employee:
                interval = employee[0]
                heapq.heappush(heap, (interval.start, interval.end, emp_idx, 0))
        
        result = []
        # Track the end of the current "merged" busy period
        prev_end = None
        
        while heap:
            start, end, emp_idx, int_idx = heapq.heappop(heap)
            
            # If there's a gap, record it
            if prev_end is not None and start > prev_end:
                result.append(Interval(prev_end, start))
            
            # Update the merged busy period
            prev_end = max(prev_end, end) if prev_end else end
            
            # Push next interval from this employee
            if int_idx + 1 < len(schedule[emp_idx]):
                next_interval = schedule[emp_idx][int_idx + 1]
                heapq.heappush(heap, (next_interval.start, next_interval.end, emp_idx, int_idx + 1))
        
        return result
```

"This is more complex, so I recommend the flatten → merge → find gaps approach for interviews. It's clearer and easier to explain."

---

## Part 7 – Visual Summary

**(Visual: The 4-step process.)**

**Instructor:**
"Let's visualize the entire process:"

```
INPUT: Employee schedules (each is a list of intervals)

    Employee 1: [1,2] [5,6]      ──┐
    Employee 2: [1,3]            ──┼──► FLATTEN
    Employee 3: [4,10]           ──┘
                                   │
                                   ▼
    All intervals: [1,2] [5,6] [1,3] [4,10]
                                   │
                                   ▼ SORT
    Sorted: [1,2] [1,3] [4,10] [5,6]
                                   │
                                   ▼ MERGE (our pattern!)
    Merged: [1,3] [4,10]
                                   │
                                   ▼ FIND GAPS
    Gaps: [3,4]
                                   │
                                   ▼
OUTPUT: [[3,4]] ← Free time!
```

---

## Part 8 – Edge Cases

**(Visual: Edge case examples.)**

**Instructor:**
"Let's think about edge cases:"

### Edge Case 1: No Gaps (Everyone's Schedule Overlaps Completely)
```
Input: [[[1,5]], [[2,6]], [[4,8]]]
Merged: [[1,8]]
Output: []  (no free time)
```

### Edge Case 2: Single Employee
```
Input: [[[1,3], [5,7]]]
Merged: [[1,3], [5,7]]
Output: [[3,5]]  (gap between their work blocks)
```

### Edge Case 3: Non-overlapping Employees
```
Input: [[[1,2]], [[5,6]], [[10,12]]]
Merged: [[1,2], [5,6], [10,12]]
Output: [[2,5], [6,10]]  (two gaps)
```

### Edge Case 4: All Work at Different Times
```
Input: [[[1,2]], [[3,4]]]
Merged: [[1,2], [3,4]]
Output: [[2,3]]
```

---

## Part 9 – Complexity Analysis

**(Visual: Complexity table.)**

**Instructor:**
"Let's analyze the complexity."

### Variables
- `n` = total number of intervals across all employees
- `k` = number of employees

### Time Complexity: O(n log n)

| Operation | Time |
|-----------|------|
| Flatten | O(n) |
| Sort | O(n log n) |
| Merge | O(n) |
| Find gaps | O(n) |
| **Total** | **O(n log n)** |

### Space Complexity: O(n)

"We store all flattened intervals and the merged result."

---

## Part 10 – Key Insight: Pattern as Building Block

**(Visual: Pattern reuse diagram.)**

**Instructor:**
"This problem teaches an important lesson:

> **Patterns are building blocks, not just templates.**

We didn't use the merge pattern directly. We used it as a *step* in a larger solution:

```
┌─────────────────────────────────────────────┐
│          EMPLOYEE FREE TIME SOLUTION        │
├─────────────────────────────────────────────┤
│                                             │
│   1. Flatten  ─────► Preprocessing          │
│                                             │
│   2. Merge    ─────► OUR PATTERN! ⭐        │
│                                             │
│   3. Find Gaps ────► Post-processing        │
│                                             │
└─────────────────────────────────────────────┘
```

This is how you tackle hard problems: **break them down** and apply patterns to the pieces."

---

## Part 11 – Wrap-Up

**Instructor:**
"Let's recap what we learned:

1. **Employee Free Time** looks scary, but it's just merge + find gaps.

2. The solution has three phases:
   - **Flatten**: Combine all employee schedules
   - **Merge**: Apply our Merge Overlapping Intervals pattern
   - **Find Gaps**: Look at spaces between merged intervals

3. **Finding gaps is simple**:
   ```python
   for i in range(len(merged) - 1):
       gap = [merged[i][1], merged[i+1][0]]
   ```

4. **Patterns are building blocks** — you can combine and extend them.

This is a great problem to practice because it reinforces the merge pattern while teaching you to think in terms of *composing* solutions from simpler parts.

Thanks for watching, and I'll see you in the next video!"

---

## Quick Reference

```python
def employeeFreeTime(schedule):
    # 1. Flatten
    all_intervals = []
    for employee in schedule:
        for interval in employee:
            all_intervals.append([interval.start, interval.end])
    
    # 2. Merge (our pattern)
    all_intervals.sort(key=lambda x: x[0])
    merged = [all_intervals[0]]
    
    for current in all_intervals[1:]:
        last = merged[-1]
        if current[0] <= last[1]:
            last[1] = max(last[1], current[1])
        else:
            merged.append(current)
    
    # 3. Find gaps
    free_time = []
    for i in range(len(merged) - 1):
        free_time.append([merged[i][1], merged[i+1][0]])
    
    return free_time
```

| Aspect | Details |
|--------|---------|
| Pattern Used | Merge Overlapping Intervals |
| Extra Step | Find gaps between merged intervals |
| Time | O(n log n) |
| Space | O(n) |
| Key Insight | Flatten → Merge → Find Gaps |
| Gap Formula | `[merged[i].end, merged[i+1].start]` |

