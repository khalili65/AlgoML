# 🎬 Tutorial Script: Insert Interval Pattern (Example → Pattern → Template)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

In our last lesson, we learned how to **merge overlapping intervals**. Today we're going to tackle a related but different problem: **Insert Interval**.

The idea is simple: given a list of *already sorted, non-overlapping* intervals, insert a *new* interval and merge if necessary.

This is a classic interview problem, and once you see the pattern, it becomes surprisingly straightforward."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: sorted intervals and new interval.)**

Show on screen:

```text
intervals = [[1,2], [3,5], [6,7], [8,10], [12,16]]
newInterval = [4,8]
```

**Instructor:**
"Here's our problem:

> We have a sorted list of non-overlapping intervals. We need to insert a new interval `[4,8]` and merge any overlapping intervals.

Let's visualize this on a number line."

**(Visual: number line with intervals.)**

```
[1,2]:    ██
[3,5]:      ███
[6,7]:          ██
[8,10]:           ███
[12,16]:                █████

newInterval [4,8]:
          ─────────────
            [4      8]

Number line:
0  1  2  3  4  5  6  7  8  9  10  11  12  13  14  15  16
   |--|  |-----|  |--|  |---|       |---------|
          |-----------|
            new interval
```

**Instructor:**
"Looking at this, I can see that `[4,8]` overlaps with:
- `[3,5]` (because 4 ≤ 5)
- `[6,7]` (because 6 ≤ 8)
- `[8,10]` (because 8 ≤ 8)

So the result should be:

`[[1,2], [3,10], [12,16]]`

The three intervals `[3,5], [6,7], [8,10]` and our new `[4,8]` all merge into `[3,10]`."

---

## Part 2 – The "Aha!" Moment (The Three Phases)

**(Visual: intervals divided into three groups.)**

**Instructor:**
"Here's the key insight. When inserting a new interval, we can divide all existing intervals into **three phases**:

1. **BEFORE**: Intervals that end *before* the new interval starts
2. **OVERLAP**: Intervals that overlap with the new interval
3. **AFTER**: Intervals that start *after* the new interval ends"

**(Visual: color-coded intervals.)**

```
intervals = [[1,2], [3,5], [6,7], [8,10], [12,16]]
newInterval = [4,8]

Phase 1 - BEFORE (end < new.start):
  [1,2] → ends at 2, which is < 4 ✓ → Add directly

Phase 2 - OVERLAP (start <= new.end):
  [3,5] → starts at 3, which is <= 8 ✓ → Merge!
  [6,7] → starts at 6, which is <= 8 ✓ → Merge!
  [8,10] → starts at 8, which is <= 8 ✓ → Merge!

Phase 3 - AFTER (remaining):
  [12,16] → starts at 12, which is > 8 ✓ → Add directly
```

---

## 🔑 Understanding Why Phase 2's Condition Works

**(Visual: Complete overlap definition.)**

**Instructor:**
"Wait — you might be wondering: 'Isn't the complete overlap condition more complex?'

You're right! The **full overlap condition** for two intervals is:"

```
┌─────────────────────────────────────────────────────────────┐
│           COMPLETE OVERLAP CONDITION                        │
├─────────────────────────────────────────────────────────────┤
│   Interval A and B overlap if BOTH are true:                │
│                                                             │
│     1. A.start <= B.end   (A starts before B ends)          │
│     2. B.start <= A.end   (B starts before A ends)          │
│                                                             │
│   For us: interval overlaps with newInterval if:            │
│     1. interval.start <= new.end                            │
│     2. new.start <= interval.end                            │
└─────────────────────────────────────────────────────────────┘
```

**Instructor:**
"So why do we only check `interval.start <= new.end` in Phase 2?

Here's the clever insight:

> **Phase 1 already guarantees the other condition!**"

**(Visual: Logic flow.)**

```
Phase 1 loop condition: interval.end < new.start
                        ↓
When Phase 1 STOPS, the condition becomes FALSE:
    NOT (interval.end < new.start)
    → interval.end >= new.start
    → new.start <= interval.end  ✓  ← FIRST OVERLAP CONDITION!

So when we enter Phase 2, we ALREADY KNOW:
    new.start <= interval.end  (guaranteed by Phase 1 stopping)

We only need to check:
    interval.start <= new.end  ← SECOND OVERLAP CONDITION

Together: COMPLETE OVERLAP! ✓
```

**(Visual: Diagram.)**

```
Phase 1 adds intervals here (no overlap possible):
    [---]  [---]  
                 |← new.start
                 [====new====]

Phase 1 STOPS when interval.end >= new.start:
              [...interval...]
                 |← now they might touch/overlap!
                 [====new====]
                 
Phase 2 checks if interval.start <= new.end:
              [...interval...]
                 [====new====]
                            |← new.end
    If start is before this point → OVERLAP!
```

**Instructor:**
"This is why the pattern is elegant — each phase's condition **implicitly carries information** from the previous phase!"

---

## 💡 Tip for Students

**Instructor:**
"If the simplified condition feels confusing, you can always use the **full overlap condition** instead:

```python
# Simplified (what we teach):
while i < n and intervals[i][0] <= newInterval[1]:

# Full condition (also correct!):
while i < n and (newInterval[0] <= intervals[i][1] and intervals[i][0] <= newInterval[1]):
```

Both are **100% correct**! The simplified version is an optimization, but the full condition is easier to understand and remember. Use whichever feels more natural to you."

---

**Instructor:**
"This three-phase approach is the key to solving this problem efficiently!"

---

## Part 3 – The Core Logic

**(Visual: flowchart of the three phases.)**

**Instructor:**
"Let's break down each phase in detail."

### Phase 1: Add All Intervals BEFORE

```
Condition: interval.end < newInterval.start

These intervals are completely to the LEFT of the new interval.
No overlap possible. Just add them to the result.
```

### Phase 2: Merge All Overlapping Intervals

```
Condition: interval.start <= newInterval.end

These intervals OVERLAP with the new interval.
Merge by:
  - Taking the MIN of starts
  - Taking the MAX of ends
```

### Phase 3: Add All Intervals AFTER

```
Condition: Everything remaining

These intervals are completely to the RIGHT.
Just add them to the result.
```

**(Visual: The merging formula.)**

**Instructor:**
"During the merge phase, we update the new interval like this:

```
newInterval.start = min(newInterval.start, current.start)
newInterval.end = max(newInterval.end, current.end)
```

Then after all overlapping intervals are processed, we add the merged new interval to the result."

---

## Part 4 – Step-by-Step Walkthrough

**(Visual: animated walkthrough.)**

**Instructor:**
"Let's trace through our example step by step."

```
intervals = [[1,2], [3,5], [6,7], [8,10], [12,16]]
newInterval = [4,8]
result = []
i = 0

PHASE 1: Add intervals that end before newInterval starts (before 4)
─────────────────────────────────────────────────────────────────────
i=0: [1,2] → 2 < 4? YES → Add [1,2]
result = [[1,2]]
i = 1

i=1: [3,5] → 5 < 4? NO → Stop Phase 1


PHASE 2: Merge overlapping intervals (start <= 8)
─────────────────────────────────────────────────────────────────────
i=1: [3,5] → 3 <= 8? YES → Merge!
     newInterval = [min(4,3), max(8,5)] = [3,8]
i = 2

i=2: [6,7] → 6 <= 8? YES → Merge!
     newInterval = [min(3,6), max(8,7)] = [3,8]
i = 3

i=3: [8,10] → 8 <= 8? YES → Merge!
     newInterval = [min(3,8), max(8,10)] = [3,10]
i = 4

i=4: [12,16] → 12 <= 10? NO → Stop Phase 2

Add merged newInterval: [3,10]
result = [[1,2], [3,10]]


PHASE 3: Add remaining intervals
─────────────────────────────────────────────────────────────────────
i=4: Add [12,16]
result = [[1,2], [3,10], [12,16]]


FINAL ANSWER: [[1,2], [3,10], [12,16]] ✓
```

---

## Part 5 – Turning It into a 3-Phase Pattern

**(Visual: simple list of steps.)**

**Instructor:**
"Now let's turn this into a **3-phase core pattern** you can follow."

1. **Add all intervals that come BEFORE the new interval.**
   ```
   while i < n and intervals[i].end < newInterval.start:
       result.append(intervals[i])
       i += 1
   ```
   "These intervals end before the new one starts — no overlap."

2. **Merge all overlapping intervals with the new interval.**
   ```
   while i < n and intervals[i].start <= newInterval.end:
       newInterval.start = min(newInterval.start, intervals[i].start)
       newInterval.end = max(newInterval.end, intervals[i].end)
       i += 1
   result.append(newInterval)
   ```
   "Keep expanding the new interval as long as there's overlap."

3. **Add all remaining intervals that come AFTER.**
   ```
   while i < n:
       result.append(intervals[i])
       i += 1
   ```
   "These start after the new interval ends — no overlap."

**Instructor (summary line):**
"The heart of this pattern is **three sequential while loops**:

> **Before → Merge → After**

No sorting needed because the input is already sorted!"

---

## Part 6 – Python Code (Filled-In Version)

**(Visual: Python code.)**

**Instructor:**
"Here's the complete Python solution."

```python
def insert(intervals, newInterval):
    """
    Insert newInterval into sorted intervals and merge if necessary.
    Example: [[1,2],[3,5],[6,7],[8,10],[12,16]], [4,8] → [[1,2],[3,10],[12,16]]
    """
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

**Instructor:**
"Notice the elegance:

- **Phase 1 condition**: `intervals[i][1] < newInterval[0]` (end before start)
- **Phase 2 condition**: `intervals[i][0] <= newInterval[1]` (start before end)
- **Phase 3**: Just add the rest!"

---

## Part 7 – The Reusable Template

**(Visual: Python template.)**

**Instructor:**
"Here's the generic template you can reuse for insert interval problems."

```python
def insert_interval_template(intervals, newInterval):
    """
    Insert Interval Template.
    
    Three phases:
      1. Add intervals BEFORE (no overlap)
      2. MERGE overlapping intervals
      3. Add intervals AFTER (no overlap)
    """
    result = []
    i = 0
    n = len(intervals)
    
    # ═══════════════════════════════════════════════
    # PHASE 1: Add intervals that come BEFORE
    # Condition: interval.end < newInterval.start
    # ═══════════════════════════════════════════════
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # ═══════════════════════════════════════════════
    # PHASE 2: Merge overlapping intervals
    # Condition: interval.start <= newInterval.end
    # ═══════════════════════════════════════════════
    while i < n and intervals[i][0] <= newInterval[1]:
        # Expand newInterval to include current
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    
    # Add the (possibly merged) newInterval
    result.append(newInterval)
    
    # ═══════════════════════════════════════════════
    # PHASE 3: Add remaining intervals (come AFTER)
    # ═══════════════════════════════════════════════
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

---

## Part 8 – Alternative: Single-Pass Approach

**(Visual: alternative code.)**

**Instructor:**
"There's an alternative way to write this using a for-loop with conditionals:"

```python
def insert_single_pass(intervals, newInterval):
    result = []
    
    for i, interval in enumerate(intervals):
        # Current interval is completely BEFORE new interval
        if interval[1] < newInterval[0]:
            result.append(interval)
        
        # Current interval is completely AFTER new interval
        elif interval[0] > newInterval[1]:
            result.append(newInterval)
            return result + intervals[i:]
        
        # Overlap: merge into newInterval
        else:
            newInterval[0] = min(newInterval[0], interval[0])
            newInterval[1] = max(newInterval[1], interval[1])
    
    # Don't forget to add newInterval at the end
    result.append(newInterval)
    return result
```

**Instructor:**
"Both approaches work. The three-phase approach is more explicit and easier to explain in interviews. Choose whichever feels more natural to you."

---

## Part 9 – Connecting to the Template

**(Visual: mapping.)**

**Instructor:**
"Let me connect our example to the template, so you can see the pattern clearly."

| Phase | Condition | Action |
|-------|-----------|--------|
| 1. Before | `interval.end < new.start` | Add interval directly |
| 2. Merge | `interval.start <= new.end` | Expand newInterval |
| 3. After | (remaining) | Add interval directly |

**Key insight:**

> "The conditions are based on comparing **endpoints**:
> - Phase 1 checks if interval *ends* before new *starts*
> - Phase 2 checks if interval *starts* before new *ends*"

---

## Part 10 – Edge Cases

**(Visual: edge case examples.)**

**Instructor:**
"Let's consider important edge cases."

### Edge Case 1: Insert at the Beginning
```
intervals = [[3,5], [8,10]]
newInterval = [1,2]
Output: [[1,2], [3,5], [8,10]]
```
"Phase 1 adds nothing, Phase 2 adds [1,2], Phase 3 adds rest."

### Edge Case 2: Insert at the End
```
intervals = [[1,2], [3,5]]
newInterval = [8,10]
Output: [[1,2], [3,5], [8,10]]
```
"Phase 1 adds all, Phase 2 adds [8,10], Phase 3 adds nothing."

### Edge Case 3: New Interval Covers All
```
intervals = [[2,3], [4,5], [6,7]]
newInterval = [1,10]
Output: [[1,10]]
```
"Phase 1 adds nothing, Phase 2 merges all into [1,10], Phase 3 adds nothing."

### Edge Case 4: No Overlap
```
intervals = [[1,2], [5,6]]
newInterval = [3,4]
Output: [[1,2], [3,4], [5,6]]
```
"New interval fits in the gap — no merging needed."

### Edge Case 5: Empty Intervals
```
intervals = []
newInterval = [1,5]
Output: [[1,5]]
```
"Just return the new interval."

---

## Part 11 – When to Use This Pattern

**(Visual: recognition clues.)**

**Instructor:**
"How do you recognize an Insert Interval problem?"

1. **Sorted, non-overlapping input**
   * The intervals are already sorted by start time
   * No existing overlaps in the input

2. **Insert/Add operation**
   * You're adding a new interval to an existing list
   * Need to maintain the sorted, non-overlapping property

3. **Keywords**
   * "Insert", "Add"
   * "Maintain sorted order"
   * "Merge if overlapping"

**Instructor:**
"If you see:
* sorted non-overlapping intervals
* insert new interval
* merge as needed

Then use the **Insert Interval pattern** with three phases: Before → Merge → After."

---

## Part 12 – Complexity Analysis

**(Visual: complexity table.)**

**Instructor:**
"Let's analyze time and space complexity."

### Time Complexity: O(n)

| Operation | Time |
|-----------|------|
| Phase 1 loop | O(k) where k = intervals before |
| Phase 2 loop | O(m) where m = overlapping intervals |
| Phase 3 loop | O(n-k-m) = remaining intervals |
| **Total** | **O(n)** |

"We visit each interval exactly once!"

### Space Complexity: O(n)

"The output can contain up to n+1 intervals."

*Note: This is more efficient than Merge Intervals O(n log n) because the input is already sorted!*

---

## Part 13 – Wrap-Up

**(Visual: summary.)**

**Instructor:**
"Let's recap what we learned today."

1. **Insert Interval** is about adding a new interval to sorted, non-overlapping intervals.

2. The key insight is the **three-phase approach**:
   - **Before**: Add intervals that end before new starts
   - **Merge**: Combine overlapping intervals into the new one
   - **After**: Add intervals that start after new ends

3. The conditions are:
   ```
   Phase 1: interval.end < newInterval.start
   Phase 2: interval.start <= newInterval.end
   Phase 3: everything remaining
   ```

4. **Time: O(n), Space: O(n)** — better than sorting!

**Instructor (closing):**
"This pattern comes up in many variations — range modules, booking systems, calendar apps. Master the three-phase approach, and you'll handle them all.

In the next lessons, we'll solve real LeetCode problems using this pattern.

Thanks for watching, and I'll see you in the next video!"

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│              INSERT INTERVAL PATTERN                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PHASE 1: Add BEFORE intervals                              │
│     while interval.end < new.start:                         │
│         result.append(interval)                             │
│                                                             │
│  PHASE 2: MERGE overlapping intervals                       │
│     while interval.start <= new.end:                        │
│         new.start = min(new.start, interval.start)          │
│         new.end = max(new.end, interval.end)                │
│     result.append(new)                                      │
│                                                             │
│  PHASE 3: Add AFTER intervals                               │
│     while more intervals:                                   │
│         result.append(interval)                             │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  Phase 1 condition: interval.end < new.start                │
│  Phase 2 condition: interval.start <= new.end               │
│  TIME: O(n)    SPACE: O(n)                                  │
└─────────────────────────────────────────────────────────────┘
```

---

## LeetCode Problems to Practice

| Problem | Difficulty | Notes |
|---------|------------|-------|
| LC 57 - Insert Interval | Medium | Classic, use this template directly |
| LC 715 - Range Module | Hard | Multiple insert/remove operations |
| LC 352 - Data Stream as Disjoint Intervals | Hard | Dynamic insertion |

---

Next lesson, we'll solve **LC 57 - Insert Interval** step by step using this pattern!

