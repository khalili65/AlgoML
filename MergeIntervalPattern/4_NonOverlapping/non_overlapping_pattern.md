# 🎬 Tutorial Script: Non-overlapping Intervals Pattern (Greedy Approach)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

Today we're going to learn a fascinating pattern: the **Non-overlapping Intervals** pattern. This pattern uses a **greedy approach** that might surprise you — we sort by END time, not start time!

This is different from all the other interval patterns we've learned, and understanding WHY we sort by end time is the key insight that unlocks multiple LeetCode problems."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: a list of intervals.)**

Show on screen:

```text
intervals = [[1,2], [2,3], [3,4], [1,3]]
```

**Instructor:**
"Here's our problem:

> Given a list of intervals, find the **minimum number of intervals to remove** so that the remaining intervals don't overlap.

Think of it like this: you have conflicting meetings on your calendar. How many meetings do you need to cancel so the rest don't conflict?"

**(Visual: intervals on a timeline.)**

```
[1,2]:      ██
[2,3]:        ██
[3,4]:          ██
[1,3]:      ████

Number line:
0   1   2   3   4   5
    |---|
        |---|
            |---|
    |-------|
        ↑
    Overlap here!
```

**Instructor:**
"We can see that `[1,3]` overlaps with both `[1,2]` and `[2,3]`. If we remove `[1,3]`, the remaining intervals `[[1,2], [2,3], [3,4]]` don't overlap!

So the answer is **1** — we only need to remove one interval."

---

## Part 2 – The "Aha!" Moment (Why Sort by End Time?)

**(Visual: Comparison of sorting strategies.)**

**Instructor:**
"Here's the key question: **how should we sort the intervals?**

Let me show you why sorting by END time is the right choice."

**(Visual: Two sorting approaches.)**

```
APPROACH 1: Sort by START time
[[1,2], [1,3], [2,3], [3,4]]

Start with [1,2], end=2
Check [1,3]: 1 < 2 → overlap!
  Which one to keep? 🤔

APPROACH 2: Sort by END time
[[1,2], [2,3], [1,3], [3,4]]

Start with [1,2], end=2
Check [2,3]: 2 >= 2 → no overlap! Keep it, end=3
Check [1,3]: 1 < 3 → overlap! Remove it
Check [3,4]: 3 >= 3 → no overlap! Keep it
```

**Instructor:**
"With sorting by end time, the decision becomes automatic:
- If the current interval **starts before** the previous end → it overlaps → **remove it**
- If the current interval **starts at or after** the previous end → no overlap → **keep it**"

---

## Part 3 – The Greedy Insight

**(Visual: Why greedy works.)**

**Instructor:**
"But WHY does keeping the interval that ends earliest work?"

```
┌─────────────────────────────────────────────────────────────┐
│           THE GREEDY INSIGHT                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   The interval that ENDS earlier leaves MORE ROOM           │
│   for future intervals!                                     │
│                                                             │
│   Example:                                                  │
│   Option A: Keep [1,3] → ends at 3                          │
│   Option B: Keep [1,2] → ends at 2                          │
│                                                             │
│   [2,4] can fit after [1,2] but NOT after [1,3]!            │
│                                                             │
│   By always keeping the interval that ends earliest,        │
│   we maximize the number of intervals we can keep.          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**(Visual: Comparison.)**

```
Keep [1,3]:
[███████]           → ends at 3
        [XXX]       → [2,4] can't fit (starts at 2 < 3)

Keep [1,2]:
[████]              → ends at 2
      [████████]    → [2,4] fits! (starts at 2 >= 2)
```

**Instructor:**
"This is the essence of the greedy approach:

> **Always keep the interval that ends earliest — it leaves the most room for future intervals.**"

---

## Part 4 – The 3-Step Pattern

**(Visual: Pattern summary card.)**

**Instructor:**
"Let's crystallize this into our **3-step pattern**:"

```
┌─────────────────────────────────────────────────────────────┐
│           NON-OVERLAPPING INTERVALS PATTERN                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT by END time (not start!)                           │
│     intervals.sort(key=lambda x: x[1])                      │
│                                                             │
│  2. TRACK the end of the last kept interval                 │
│     prev_end = first interval's end                         │
│                                                             │
│  3. FOR EACH interval:                                      │
│     if start >= prev_end:  # No overlap                     │
│         Keep it, update prev_end = end                      │
│     else:                   # Overlap                       │
│         Remove it (count += 1)                              │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Sort by END time, not START time!                     │
│  GREEDY: Keep interval that ends earliest.                  │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – Step-by-Step Walkthrough

**(Visual: Full walkthrough with the example.)**

**Instructor:**
"Let's trace through our example step by step."

```
intervals = [[1,2], [2,3], [3,4], [1,3]]
```

### Step 0: Sort by End Time
```
Original: [[1,2], [2,3], [3,4], [1,3]]
Sorted:   [[1,2], [2,3], [1,3], [3,4]]
                              ↑
                      [1,3] comes after [2,3] because 3 > 3? No, 3 == 3
                      With equal ends, order doesn't matter
```

### Step 1: Initialize
```
prev_end = -∞ (or we can start with first interval)
count = 0

First interval [1,2]:
  1 >= -∞? YES → Keep it
  prev_end = 2
```

### Step 2: Process [2,3]
```
[2,3]: start=2, end=3
  2 >= 2? YES → No overlap, keep it!
  prev_end = 3
  count = 0
```

### Step 3: Process [1,3]
```
[1,3]: start=1, end=3
  1 >= 3? NO → Overlap! Remove it.
  count = 1
  (prev_end stays 3)
```

### Step 4: Process [3,4]
```
[3,4]: start=3, end=4
  3 >= 3? YES → No overlap, keep it!
  prev_end = 4
  count = 1
```

**Final Answer:** Remove **1** interval (`[1,3]`)

---

## Part 6 – The Complete Python Solution

**(Visual: Code on screen.)**

**Instructor:**
"Here's the complete solution:"

```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    
    # Step 1: Sort by END time (greedy choice!)
    intervals.sort(key=lambda x: x[1])
    
    # Step 2: Track removals and previous end
    count = 0
    prev_end = float('-inf')
    
    # Step 3: Process each interval
    for start, end in intervals:
        if start >= prev_end:
            # No overlap - keep this interval
            prev_end = end
        else:
            # Overlap - remove this interval
            count += 1
    
    return count
```

---

## Part 7 – The Reusable Template

**(Visual: Template card.)**

**Instructor:**
"Here's your reusable template for non-overlapping interval problems:"

```python
def max_non_overlapping(intervals):
    """
    Find maximum number of non-overlapping intervals.
    (Equivalently: minimum removals = total - max_non_overlapping)
    
    Args:
        intervals: List of [start, end] intervals
    
    Returns:
        Maximum count of non-overlapping intervals
    """
    if not intervals:
        return 0
    
    # KEY: Sort by END time for greedy approach
    intervals.sort(key=lambda x: x[1])
    
    count = 1  # Keep first interval
    prev_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        start, end = intervals[i]
        
        # Check: does current interval start after previous ends?
        if start >= prev_end:
            count += 1           # Keep this interval
            prev_end = end       # Update the end marker
        # else: skip this interval (it overlaps)
    
    return count

# To get minimum removals:
# removals = len(intervals) - max_non_overlapping(intervals)
```

---

## Part 8 – Alternative Formulation: Count What We Keep

**(Visual: Two ways to think about it.)**

**Instructor:**
"There are two equivalent ways to solve this problem:"

```
APPROACH 1: Count what we REMOVE
- For each overlap, increment count
- Return count

APPROACH 2: Count what we KEEP
- For each non-overlap, increment kept
- Return len(intervals) - kept

Both give the same answer!
```

```python
# Approach 1: Count removals
def count_removals(intervals):
    intervals.sort(key=lambda x: x[1])
    count = 0
    prev_end = float('-inf')
    
    for start, end in intervals:
        if start >= prev_end:
            prev_end = end
        else:
            count += 1  # Count removals
    
    return count

# Approach 2: Count what we keep
def count_removals_v2(intervals):
    intervals.sort(key=lambda x: x[1])
    kept = 0
    prev_end = float('-inf')
    
    for start, end in intervals:
        if start >= prev_end:
            kept += 1   # Count what we keep
            prev_end = end
    
    return len(intervals) - kept
```

---

## Part 9 – Why NOT Sort by Start Time?

**(Visual: Counterexample.)**

**Instructor:**
"A common mistake is sorting by start time. Let's see why that fails:"

```
intervals = [[1,100], [2,3], [4,5]]

WRONG: Sort by start time
[[1,100], [2,3], [4,5]]

If we keep [1,100]:
  - [2,3] overlaps → remove
  - [4,5] overlaps → remove
  - Total removals: 2 ❌

RIGHT: Sort by end time
[[2,3], [4,5], [1,100]]

Keep [2,3], end=3
Keep [4,5], end=5
[1,100] overlaps → remove
Total removals: 1 ✓
```

**Instructor:**
"Sorting by end time lets us identify and skip the 'greedy' intervals that end late and block many future intervals!"

---

## 💡 Tip for Students

**Instructor:**
"If you're ever unsure about the greedy choice, remember this:

> **The interval that ends earliest leaves the most room for future intervals.**

This is why we sort by END time, not START time. The earlier an interval ends, the more compatible it is with future intervals.

You can also think of it as: 'finish early, make room for more!'"

---

## Part 10 – Other Problems Using This Pattern

**(Visual: Related problems.)**

**Instructor:**
"Here are other problems that use the Non-overlapping pattern:"

| Problem | Twist |
|---------|-------|
| **LC 435 - Non-overlapping Intervals** | Classic — min removals |
| **LC 452 - Minimum Arrows to Burst Balloons** | Count groups of overlapping |
| **LC 646 - Maximum Length of Pair Chain** | Pairs instead of intervals |
| **Activity Selection Problem** | Classic greedy algorithm |

---

## Part 11 – Pattern Recognition Clues

**(Visual: When to use this pattern.)**

**Instructor:**
"Use this pattern when you see:

✅ **Minimize removals** to eliminate overlaps
✅ **Maximize selections** of non-overlapping items
✅ **Activity selection** or scheduling problems
✅ Intervals where you need to **choose which to keep**

**Key difference from other patterns:**
- Merge: Combine overlapping → sort by START
- Non-overlapping: Choose which to keep → sort by END"

---

## Part 12 – Complexity Analysis

**Instructor:**
"Let's analyze the complexity:"

```
Time Complexity: O(n log n)
- Sorting: O(n log n)
- Single pass through intervals: O(n)
- Sorting dominates!

Space Complexity: O(1) or O(log n)
- O(1) extra space for variables
- O(log n) if counting sorting's stack space
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls to avoid.)**

**Instructor:**
"Watch out for these common mistakes:"

```
❌ Mistake 1: Sorting by START time instead of END time
   This is the most common error! Sort by END.

❌ Mistake 2: Using < instead of >= for no-overlap check
   start >= prev_end means NO overlap (touching is OK)
   start > prev_end would incorrectly count touching as overlap

❌ Mistake 3: Forgetting to update prev_end
   Only update prev_end when you KEEP an interval!

❌ Mistake 4: Updating prev_end on removal
   When removing, prev_end stays the same — we keep the
   earlier-ending interval!
```

---

## Part 14 – Closing

**Instructor:**
"And that's the Non-overlapping Intervals pattern!

**Key takeaways:**
1. **Sort by END time** (the greedy choice!)
2. Keep intervals that **start after previous end**
3. The greedy approach works because **ending early leaves more room**
4. Either count removals or count keeps

Practice with LC 435 and LC 452, and you'll master this pattern!"

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│       NON-OVERLAPPING INTERVALS CHEAT SHEET                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  SORT: By END time (not start!)                             │
│        intervals.sort(key=lambda x: x[1])                   │
│                                                             │
│  GREEDY RULE:                                               │
│    if start >= prev_end:  KEEP, update prev_end             │
│    else:                  REMOVE (count++)                  │
│                                                             │
│  WHY END TIME?                                              │
│    Ending early → more room for future intervals            │
│                                                             │
│  FORMULA:                                                   │
│    min_removals = total - max_kept                          │
│                                                             │
│  TIME: O(n log n)    SPACE: O(1)                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

