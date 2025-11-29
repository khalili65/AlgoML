# 🎬 Tutorial Script: Merge Overlapping Intervals Pattern (Example → Pattern → Template)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back. I'm really glad you're here.

Today we're going to learn one of the most frequently tested patterns in coding interviews: the **Merge Overlapping Intervals** pattern.

Instead of starting with a definition, I want to start with something you already understand intuitively, and then we'll turn it into a reusable template you can use for many interview questions."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: a list of intervals.)**

Show on screen:

```text
intervals = [[1,3], [2,6], [8,10], [15,18]]
```

**Instructor:**
"Here's our problem:

> We have a list of **time intervals** representing meetings on a calendar. Some of these meetings overlap. Our task is to **merge all overlapping intervals** and return a list where no two intervals overlap.

Think of it like this: if you have a meeting from 1pm to 3pm, and another from 2pm to 6pm, those overlap, right? So we'd combine them into one big block: 1pm to 6pm."

**Instructor (thinking out loud, visual approach):**

"Let's visualize these intervals on a number line."

**(Visual: number line with intervals drawn as bars.)**

```
[1,3]:      ███
[2,6]:       █████
[8,10]:              ███
[15,18]:                      ████

Number line:
0   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18
    |-------|
        |-----------|
                        |-----|
                                                |---------|
```

**Instructor:**
"Now I can see it clearly:

* `[1,3]` and `[2,6]` **overlap** because 2 is between 1 and 3.
* `[8,10]` is alone — no overlap with neighbors.
* `[15,18]` is alone too.

So the answer should be: `[[1,6], [8,10], [15,18]]`."

**(Short pause.)**

"But how do we systematically figure this out in code? Let's think about what our eyes just did naturally."

---

## Part 2 – The "Aha!" Moment (Noticing the Key Insight)

**(Visual highlights `[1,3]` and `[2,6]`.)**

**Instructor:**
"Let me ask you: how did we know that `[1,3]` and `[2,6]` overlap?

First, let me show you the **complete overlap condition** for any two intervals A and B:"

**(Visual: Full overlap definition.)**

```
┌─────────────────────────────────────────────────────────────┐
│           COMPLETE OVERLAP CONDITION                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Two intervals [A.start, A.end] and [B.start, B.end]       │
│   OVERLAP if and only if BOTH are true:                     │
│                                                             │
│     1. A.start <= B.end   (A starts before B ends)          │
│     2. B.start <= A.end   (B starts before A ends)          │
│                                                             │
│   Equivalently, they DON'T overlap if:                      │
│     A.end < B.start  OR  B.end < A.start                    │
│     (One ends before the other starts)                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**(Visual: Overlap vs No Overlap.)**

```
OVERLAP EXAMPLE:
    [---A---]
       [---B---]
    A.start(1) ≤ B.end(6) ✓ AND B.start(2) ≤ A.end(3) ✓
    
NO OVERLAP EXAMPLE:
    [---A---]
              [---B---]
    A.start ≤ B.end ✓ BUT B.start > A.end ✗
```

**Instructor:**
"Now here's the clever part. For our specific problem, we only check **one condition**:

> The **start** of the second interval (2) is **less than or equal to** the **end** of the first interval (3).

Why can we skip the other condition? 🤔"

**(Pause for emphasis.)**

**Instructor:**
"The answer lies in **sorting**! If we sort intervals by start time first, then when we compare interval B with interval A (where A comes before B in the sorted list):

- We **know** `A.start <= B.start` (guaranteed by sorting!)
- So `A.start <= B.start <= B.end` means `A.start <= B.end` is **automatically true**

This means we only need to check the **second condition**: `B.start <= A.end`!"

```
After sorting by start time:
[1,3] comes before [2,6] because 1 < 2

Since 1 < 2, we know: A.start(1) <= B.end(6) is ALWAYS true
So we only check: B.start(2) <= A.end(3)? 

2 <= 3? YES → They overlap!
```

**Instructor:**
"This is a crucial insight:

> **Sorting by start time eliminates the need to check one of the overlap conditions!**

---

## 💡 Tip for Students

**Instructor:**
"If the simplified condition feels confusing, you can always use the **full overlap condition** instead:

```python
# Simplified (what we teach):
if current[0] <= last[1]:

# Full condition (also correct!):
if last[0] <= current[1] and current[0] <= last[1]:
```

Both are **100% correct**! The simplified version works because sorting guarantees the first part. But if you find it easier to remember the full condition, use it! The important thing is understanding **why** intervals overlap."

---

But wait — there's a catch. What if the intervals were given in a different order?"

**(Visual: scrambled intervals.)**

```text
intervals = [[8,10], [2,6], [15,18], [1,3]]
```

**Instructor:**
"Now `[8,10]` comes first, `[2,6]` second... this is messy!

How do we know which intervals to compare with which?

Here's the key insight:

> **If we sort the intervals by their start times first, then we only need to compare each interval with the one right before it.**

After sorting:

```text
[[1,3], [2,6], [8,10], [15,18]]
```

Now they're in order on the number line. We can just walk through them left to right, checking if each one overlaps with the previous."

---

## Part 3 – The Core Idea of Merging

**(Visual shows the sorted intervals side by side.)**

**Instructor:**
"Let's walk through the merging logic step by step.

We'll keep a `merged` list to build our answer. We start by adding the first interval to it."

**(Visual: Step-by-step walkthrough.)**

```
merged = [[1,3]]

Now check [2,6]:
  - Last in merged: [1,3]
  - Does [2,6] overlap with [1,3]?
  - Check: 2 <= 3? YES!
  - Merge them: new end = max(3, 6) = 6
  - Update merged: [[1,6]]

Now check [8,10]:
  - Last in merged: [1,6]
  - Does [8,10] overlap with [1,6]?
  - Check: 8 <= 6? NO!
  - No overlap. Add [8,10] as a new interval.
  - merged = [[1,6], [8,10]]

Now check [15,18]:
  - Last in merged: [8,10]
  - Does [15,18] overlap with [8,10]?
  - Check: 15 <= 10? NO!
  - No overlap. Add [15,18].
  - merged = [[1,6], [8,10], [15,18]]
```

**Instructor:**
"And we're done! The answer is `[[1,6], [8,10], [15,18]]`.

The key operations are:

1. **If overlap**: extend the end of the last merged interval.
2. **If no overlap**: add the current interval as a new entry."

---

## Part 4 – Turning It into a 4-Step Pattern

**(Visual: simple list of steps.)**

**Instructor:**
"Now that we've seen *how* the merging works, let's turn it into a **4-step core pattern** you can follow.

This is a **pseudo-code template**. The exact implementation might change slightly depending on the problem, but these 4 steps will guide you through almost every merge overlapping intervals problem."

1. **Sort the intervals by start time.**
   "This is essential. It ensures that when we compare intervals, we only need to look at the immediately previous one.
   
   *Note: Sometimes you might sort by end time for certain problems, but for merging, sort by start.*"

2. **Initialize the result with the first interval.**
   "Add the first interval to your `merged` list. This gives us something to compare against.
   
   *Note: Handle the edge case of an empty input before this step.*"

3. **Loop through the remaining intervals.**
   "For each interval, compare it with the **last interval in the merged list**.
   
   *Note: We always compare with the last merged interval because that's the only one that could possibly overlap with the current one (since we're sorted).*"

4. **Decide: Merge or Add.**
   "For each interval:
   
   * **If it overlaps** (current.start <= last.end):  
     Extend the last interval's end: `last.end = max(last.end, current.end)`
   
   * **If it doesn't overlap**:  
     Add the current interval to the merged list.
   
   *Note: The overlap check is always `current.start <= last.end` because we've sorted by start time.*"

**Instructor (summary line):**
"The heart of merging intervals is step 4:

> **Compare current.start with last.end. If overlap, extend. If not, add new.**

Remember: This is a **core pattern**. You might adjust details for variants (like counting merged intervals or finding gaps), but following these 4 steps will help you solve any merge overlapping intervals problem."

---

## Part 5 – Python Code for Our Example (Filled-In Version)

**Instructor:**
"Let's write this out in Python for our specific problem:

> 'Merge all overlapping intervals and return the list of merged intervals.'"

**(Visual: Python code.)**

```python
def merge_intervals(intervals):
    """
    Merge all overlapping intervals.
    Example: [[1,3], [2,6], [8,10], [15,18]] -> [[1,6], [8,10], [15,18]]
    """

    # Edge case: empty or single interval
    if len(intervals) <= 1:
        return intervals

    # Step 1: Sort intervals by start time
    intervals.sort(key=lambda x: x[0])

    # Step 2: Initialize result with the first interval
    merged = [intervals[0]]

    # Step 3: Loop through remaining intervals
    for i in range(1, len(intervals)):
        current = intervals[i]
        last = merged[-1]

        # Step 4: Decide - merge or add
        if current[0] <= last[1]:
            # Overlap! Extend the end of the last interval
            last[1] = max(last[1], current[1])
        else:
            # No overlap. Add current as a new interval
            merged.append(current)

    return merged
```

**Instructor:**
"Notice how clean and straightforward this is. The key comparison is just:

```python
if current[0] <= last[1]:  # Does current start before last ends?
```

If yes, they overlap, so we extend. If no, we add a new interval."

**Quick check-in question:**
"If I have `[[1,4], [4,5]]`, do they overlap?

Check: Does 4 <= 4? YES! So they merge into `[[1,5]]`.

If you got that, you understand the overlap condition."

---

## Part 6 – The Reusable Python Template (Pattern You Can Reuse)

**Instructor:**
"Now I want to do something very important.

I don't want you to memorize **just** this solution. I want you to have a **template** you can reuse for many interval merging problems."

**(Visual: title "Merge Overlapping Intervals – Python Template")**

**Instructor:**
"Here is a generic Python template for any **merge overlapping intervals** problem.

This is a **core pseudo-code pattern**. For new problems, you will:

* Keep this structure the same,
* But customize how you handle the merge operation and what you return.
* You might need to count merges, find gaps, or track additional data.

The key is: **Follow these 4 steps as your mental framework**, then adapt the implementation details to fit the problem."

**(Visual: Python template code.)**

```python
def solve_with_merge_intervals(intervals):
    """
    Generic merge overlapping intervals template.

    You customize:
      - what happens during the merge (extend, count, etc.)
      - what you return (merged list, count, gaps, etc.)
      - additional tracking if needed
    """

    # Edge case: handle empty or single interval
    if len(intervals) <= 1:
        return intervals  # or appropriate default

    # 1) Sort intervals by start time
    intervals.sort(key=lambda x: x[0])

    # 2) Initialize result with the first interval
    merged = [intervals[0][:]]  # copy to avoid mutating input

    # 3) Loop through remaining intervals
    for i in range(1, len(intervals)):
        current_start = intervals[i][0]
        current_end = intervals[i][1]
        last = merged[-1]

        # 4) Decide: Merge or Add
        if current_start <= last[1]:
            # OVERLAP DETECTED
            # Merge operation: extend the end
            last[1] = max(last[1], current_end)

            # CUSTOMIZE: You might also:
            # - increment a merge counter
            # - update other properties
            # - track which intervals were merged

        else:
            # NO OVERLAP
            # Add operation: append as new interval
            merged.append([current_start, current_end])

            # CUSTOMIZE: You might also:
            # - calculate gap between intervals
            # - track non-overlapping count

    return merged
```

**Instructor (explaining the template):**
"When you face a new interval problem, you **don't** rewrite everything from scratch.

You just answer three questions:"

1. **What am I sorting by?**

   * Usually by start time: `key=lambda x: x[0]`
   * Sometimes by end time for greedy problems: `key=lambda x: x[1]`

2. **What does the overlap condition look like?**

   * For standard merging: `current_start <= last_end`
   * For strict overlap (not touching): `current_start < last_end`

3. **What do I do when I detect overlap vs. no overlap?**

   * Overlap: extend, merge, count, etc.
   * No overlap: add new, calculate gap, etc.

---

## Part 7 – Connecting the Example to the Template

**Instructor:**
"Let's connect our concrete example back to this template, so you clearly see how they match."

"For our **merge overlapping intervals** problem, we made the following choices:"

* Sort by: `key=lambda x: x[0]`
  → *Sort by start time.*

* Overlap condition: `current[0] <= last[1]`
  → *Current interval starts before or when the last one ends.*

* On overlap: `last[1] = max(last[1], current[1])`
  → *Extend the end of the last interval.*

* On no overlap: `merged.append(current)`
  → *Add current as a new separate interval.*

**(Visual: side-by-side snippet – template on the left, `merge_intervals` on the right.)**

**Instructor:**
"So the **structure** of the code stays the same, but the **specific operations** depend on what the problem is asking."

---

## Part 8 – Other Problems That Use This Pattern

**Instructor:**
"Now let me show you how this same pattern applies to other problems. The template stays the same — only the customization changes."

### Example 1: Count Number of Merged Intervals

```python
def count_merged_intervals(intervals):
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[0])
    merged_count = 1  # Start with 1 (first interval)
    last_end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] <= last_end:
            # Overlap: extend (just update end, don't increment count)
            last_end = max(last_end, intervals[i][1])
        else:
            # No overlap: this is a new merged interval
            merged_count += 1
            last_end = intervals[i][1]
    
    return merged_count
```

### Example 2: Find Gaps Between Intervals

```python
def find_gaps(intervals):
    if len(intervals) <= 1:
        return []
    
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0][:]]
    gaps = []
    
    for i in range(1, len(intervals)):
        current = intervals[i]
        last = merged[-1]
        
        if current[0] <= last[1]:
            last[1] = max(last[1], current[1])
        else:
            # Gap detected! Record it.
            gaps.append([last[1], current[0]])
            merged.append(current[:])
    
    return gaps
```

**Instructor:**
"See? The **4-step structure** is identical. We just customize what happens during the merge/add phases."

---

## Part 9 – When Should You Use This Pattern?

**Instructor:**
"Now you might be wondering:

> 'How do I know when to use the merge intervals pattern in an interview?'"

"Here are three big clues:"

1. **Intervals or ranges**

   * The input is a list of **intervals**, **ranges**, or **time slots**.
   * Often represented as `[start, end]` pairs.

2. **Keywords that signal merging**

   * "**Merge**", "**combine**", "**union**"
   * "**Overlapping**", "**intersecting**"
   * "**Non-overlapping result**"

3. **Before-and-after relationship**

   * The problem asks you to simplify a list of intervals.
   * Or count how many distinct intervals exist after merging.
   * Or find gaps/free time between intervals.

**Instructor:**
"If you see:

* list of intervals
* overlapping / merge / combine
* simplify or consolidate

then that's a big green light to use the **merge overlapping intervals pattern**."

---

## Part 10 – Complexity Analysis

**Instructor:**
"Let's quickly analyze the time and space complexity."

### Time Complexity: O(n log n)

* Sorting takes O(n log n)
* The single pass through intervals takes O(n)
* Overall: O(n log n) — dominated by sorting

### Space Complexity: O(n)

* In the worst case (no overlaps), the merged list has all n intervals
* If we don't count the output, and sort in-place, it's O(1) extra space

**(Visual: complexity summary table.)**

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | O(n log n) | O(1) or O(n) |
| Merging pass | O(n) | O(n) output |
| **Total** | **O(n log n)** | **O(n)** |

---

## Part 11 – Common Mistakes to Avoid

**Instructor:**
"Before we wrap up, let me share some common mistakes I see students make."

### Mistake 1: Forgetting to Sort

"If you don't sort first, you can't reliably compare adjacent intervals. Always sort by start time!"

### Mistake 2: Using `<` Instead of `<=` for Overlap

"The overlap condition is `current.start <= last.end`, not `<`. 

Intervals like `[1,4]` and `[4,5]` **do overlap** (they touch at point 4)."

### Mistake 3: Replacing Instead of Extending

```python
# WRONG: Replacing the entire interval
last = current  # This doesn't update the list!

# CORRECT: Extending the end
last[1] = max(last[1], current[1])
```

### Mistake 4: Not Handling Edge Cases

"Always check:
* Empty list → return `[]`
* Single interval → return it as-is"

---

## Part 12 – Wrap-Up

**Instructor:**
"Let's recap what we did today:"

1. "We started with a concrete problem:
   **Merge all overlapping intervals**."

2. "We visualized intervals on a number line and noticed the overlap pattern."

3. "We discovered the key insight:

   > **Sort by start time, then compare each interval with the last merged one.**"

4. "We identified the overlap condition:

   > **current.start <= last.end**"

5. "We turned that into a 4-step pattern and wrote the actual Python solution."

6. "Then we generalized it into a **Python template** that you can reuse for many interval merging problems."

**Instructor (closing):**
"If the overlap condition or the merge logic still feels a bit tricky, that's completely normal.

In the next lesson, we'll take a different interval problem — **Insert Interval** — and walk through it step-by-step using a similar approach, so you can see how these interval patterns connect.

Thanks for watching, and I'll see you in the next video."

---

## Quick Reference Card

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
│  TIME: O(n log n)    SPACE: O(n)                            │
└─────────────────────────────────────────────────────────────┘
```

---

## LeetCode Problems to Practice

| Problem | Difficulty | Notes |
|---------|------------|-------|
| LC 56 - Merge Intervals | Medium | Classic, use this template directly |
| LC 57 - Insert Interval | Medium | Slight variation (next lesson) |
| LC 986 - Interval List Intersections | Medium | Two-pointer variant |
| LC 759 - Employee Free Time | Hard | Find gaps after merging |

---

Next lesson, we can write **Insert Interval** using a similar teaching style, showing how to insert a new interval into an already sorted list and merge if necessary.

