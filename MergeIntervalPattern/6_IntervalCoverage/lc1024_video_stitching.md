# 🎬 Tutorial Script: LC 1024 — Video Stitching

---

## Part 1 – Problem Introduction

**(Visual: LeetCode problem header.)**

**Instructor:**
"Let's solve **LeetCode 1024: Video Stitching** — a perfect application of our Interval Coverage pattern!"

---

## Part 2 – Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:"

```
You are given a series of video clips from a sporting event.
Each clip is described by clips[i] = [start_i, end_i].

We can cut these clips into segments freely.

Return the minimum number of clips needed so that we can 
cut the clips into segments that cover the sporting event 
[0, time].

If the task is impossible, return -1.
```

**(Visual: Examples.)**

```
Example 1:
Input: clips = [[0,2],[4,6],[8,10],[1,9],[1,5],[5,9]], time = 10
Output: 3
Explanation: Take clips [0,2], [1,9], [8,10].
             They cover [0,2] + [1,9] + [8,10] = [0,10] ✓

Example 2:
Input: clips = [[0,1],[1,2]], time = 5
Output: -1
Explanation: Can only cover [0,2], can't reach 5!

Example 3:
Input: clips = [[0,4],[2,8]], time = 5
Output: 2
Explanation: Take both clips to cover [0,8] which includes [0,5].
```

---

## Part 3 – Quick Pattern Review

**(Visual: Pattern Reference Card.)**

**Instructor:**
"This is a direct application of our Interval Coverage pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL COVERAGE PATTERN                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT by start time                                      │
│                                                             │
│  2. GREEDY: At each position, extend to FARTHEST            │
│     - current_end = where we've covered                     │
│     - farthest = max end of clips starting <= current_end   │
│                                                             │
│  3. JUMP when we've collected all options                   │
│     - current_end = farthest                                │
│     - count++                                               │
│                                                             │
│  4. GAP CHECK: if farthest == current_end → impossible      │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Always extend to the farthest reachable point!        │
└─────────────────────────────────────────────────────────────┘
```

💡 **Tip:** Think of it as "laying tiles" — each clip is a tile, and we want to cover the floor [0, time] with minimum tiles!

---

## Part 4 – Step-by-Step Walkthrough

**(Visual: Example 1 walkthrough.)**

**Instructor:**
"Let's trace through Example 1 step by step."

```
clips = [[0,2], [4,6], [8,10], [1,9], [1,5], [5,9]]
time = 10
```

### Step 0: Sort by Start Time
```
Sorted: [[0,2], [1,5], [1,9], [4,6], [5,9], [8,10]]
```

### Round 1: current_end = 0
```
Find clips with start <= 0:
  [0,2] → end = 2

farthest = 2
No more clips with start <= 0
JUMP! current_end = 2, count = 1
```

### Round 2: current_end = 2
```
Find clips with start <= 2:
  [1,5] → end = 5
  [1,9] → end = 9 ← farthest!

farthest = 9
No more clips with start <= 2
JUMP! current_end = 9, count = 2
```

### Round 3: current_end = 9
```
Find clips with start <= 9:
  [4,6] → end = 6 (not better)
  [5,9] → end = 9 (not better)
  [8,10] → end = 10 ← farthest!

farthest = 10
JUMP! current_end = 10, count = 3
```

### Done!
```
current_end (10) >= time (10)
Return 3
```

---

## Part 5 – Visual Understanding

**(Visual: Timeline.)**

**Instructor:**
"Let's visualize the greedy selection:"

```
Timeline: 0---1---2---3---4---5---6---7---8---9---10
Target:   |=====================================|

Clips:
[0,2]:    |===|
[1,5]:      |=======|
[1,9]:      |===================|
[4,6]:            |===|
[5,9]:              |=======|
[8,10]:                       |===|

Greedy selection:
Step 1: Pick [0,2]       → covers [0,2]
Step 2: Pick [1,9]       → covers [0,9]  (best extension!)
Step 3: Pick [8,10]      → covers [0,10] ✓

Result: 3 clips
```

---

## Part 6 – Complete Solution

**(Visual: Full code.)**

**Instructor:**
"Here's the complete solution:"

```python
def videoStitching(clips, time):
    # Sort by start time
    clips.sort()
    
    count = 0
    current_end = 0
    farthest = 0
    i = 0
    n = len(clips)
    
    while current_end < time:
        # Find farthest reachable from current position
        while i < n and clips[i][0] <= current_end:
            farthest = max(farthest, clips[i][1])
            i += 1
        
        # Check if we can make progress
        if farthest == current_end:
            return -1  # Gap! Cannot cover
        
        # Jump to farthest
        current_end = farthest
        count += 1
    
    return count
```

---

## Part 7 – Code Walkthrough

**(Visual: Annotated code.)**

**Instructor:**
"Let's break down each part:"

```python
def videoStitching(clips, time):
    # Sort clips by start time
    # This allows us to process clips in order
    clips.sort()
    
    count = 0              # Number of clips used
    current_end = 0        # Where we've covered up to
    farthest = 0           # Farthest we can reach from current position
    i = 0                  # Pointer through sorted clips
    n = len(clips)
    
    # Keep going until we've covered [0, time]
    while current_end < time:
        # Collect all clips that can extend from current position
        # clips[i][0] <= current_end means this clip starts within our range
        while i < n and clips[i][0] <= current_end:
            # Track the farthest end we can reach
            farthest = max(farthest, clips[i][1])
            i += 1
        
        # If we can't extend further, there's a gap
        if farthest == current_end:
            return -1
        
        # Make the greedy jump
        current_end = farthest
        count += 1
    
    return count
```

---

## Part 8 – Edge Cases

**(Visual: Edge case examples.)**

**Instructor:**
"Let's handle the edge cases:"

### Case 1: Gap in Clips
```python
clips = [[0,2], [4,6]]
time = 6

Round 1: current_end = 0 → farthest = 2
Round 2: current_end = 2
         No clips with start <= 2 that end > 2!
         farthest = 2 = current_end → GAP!

Return -1
```

### Case 2: Doesn't Start at 0
```python
clips = [[1,3], [2,5]]
time = 5

Round 1: current_end = 0
         No clips with start <= 0
         farthest = 0 = current_end → GAP!

Return -1
```

### Case 3: Single Clip Covers All
```python
clips = [[0,10]]
time = 5

Round 1: farthest = 10
         current_end = 10, count = 1
         10 >= 5 → Done!

Return 1
```

### Case 4: time = 0
```python
clips = [[0,2]]
time = 0

current_end (0) >= time (0) → Already covered!
Return 0
```

---

## Part 9 – Why Greedy Works

**(Visual: Proof intuition.)**

**Instructor:**
"Why does the greedy approach give the optimal solution?"

```
CLAIM: Picking the farthest-reaching clip at each step 
       gives the minimum number of clips.

PROOF INTUITION:
- At each step, we MUST pick a clip (to make progress)
- Picking the one that extends farthest is NEVER worse 
  than picking any other
- If we picked a shorter one, we'd need at least one more 
  clip to reach the same point

Therefore, greedy = optimal!
```

---

## Part 10 – Complexity Analysis

**Instructor:**
"Complexity analysis:"

```
Time Complexity: O(n log n)
- Sorting: O(n log n)
- Single pass through clips: O(n)
- Each clip is visited exactly once!

Space Complexity: O(1) extra
- Only a few variables (count, current_end, farthest, i)
- O(log n) for sorting stack
```

---

## Part 11 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**
"Watch out for these:"

```
❌ Mistake 1: Not sorting clips first
   Always sort by start time!

❌ Mistake 2: Using > instead of >= for farthest check
   if farthest == current_end → means no progress!

❌ Mistake 3: Checking time == 0 incorrectly
   If time is 0, we need 0 clips (nothing to cover)

❌ Mistake 4: Off-by-one error
   "Cover [0, time]" means we need to reach AT LEAST time

❌ Mistake 5: Not checking if first clip starts at 0
   If no clip covers position 0, return -1
```

---

## Part 12 – Summary

**Instructor:**
"That's LC 1024! Key takeaways:

1. **Sort by start time**
2. **Greedy**: Pick clip that extends farthest
3. **Gap detection**: If no progress → return -1
4. **Same pattern as Jump Game II**

This pattern applies to any 'cover a range with minimum intervals' problem!"

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│              LC 1024 SOLUTION TEMPLATE                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  clips.sort()  # Sort by start                              │
│                                                             │
│  count = current_end = farthest = i = 0                     │
│                                                             │
│  while current_end < time:                                  │
│      while i < n and clips[i][0] <= current_end:            │
│          farthest = max(farthest, clips[i][1])              │
│          i += 1                                             │
│                                                             │
│      if farthest == current_end:                            │
│          return -1  # Gap!                                  │
│                                                             │
│      current_end = farthest                                 │
│      count += 1                                             │
│                                                             │
│  return count                                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

