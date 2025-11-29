# 🎬 Tutorial Script: Interval Coverage Pattern (Greedy Coverage)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

Today we're learning the **Interval Coverage** pattern — the final piece of our interval pattern collection! This pattern answers the question:

> **What's the minimum number of intervals needed to cover an entire range?**

Think of it like laying tiles to cover a floor, or stitching video clips to create a complete movie. You want to use the **fewest pieces** to cover everything from start to finish!"

---

## Part 1 – The Scenario (Concrete Example)

**(Visual: Intervals on a timeline.)**

**Instructor:**
"Here's our classic problem:"

```
Given a target range [0, n] and a list of intervals,
find the MINIMUM number of intervals to cover the entire range.

Example:
intervals = [[0,2], [4,6], [8,10], [1,9], [1,5], [5,9]]
n = 10

Which intervals do we pick?
```

**(Visual: Timeline showing all intervals.)**

```
Target: [0 ==================== 10]

[0,2]:    |===|
[4,6]:          |===|
[8,10]:               |===|
[1,9]:      |=============|
[1,5]:      |=======|
[5,9]:            |=======|

Best choice: [0,2] + [1,9] + [8,10] = 3 intervals
or:          [0,2] + [1,5] + [5,9] + [8,10]? = 4 intervals (worse!)
```

**Instructor:**
"The trick is to be **greedy** — always extend as far as possible at each step!"

---

## Part 2 – The Greedy Insight

**(Visual: Greedy strategy.)**

**Instructor:**
"Here's the key insight:"

```
┌─────────────────────────────────────────────────────────────┐
│           THE GREEDY INSIGHT                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   At each position, we have a "current end" that we've      │
│   covered so far. From all intervals that START at or       │
│   before this position, pick the one that ENDS the          │
│   FARTHEST!                                                 │
│                                                             │
│   Why? Because extending as far as possible minimizes       │
│   the total number of intervals needed.                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**(Visual: Greedy vs Non-greedy.)**

```
At position 2, we've covered [0,2]
Available intervals starting at or before 2: [1,9], [1,5]

GREEDY: Pick [1,9] → extends to 9! 
NON-GREEDY: Pick [1,5] → only extends to 5

Greedy wins because one jump covers more ground!
```

---

## Part 3 – The Jump Game Connection

**(Visual: Mapping to Jump Game.)**

**Instructor:**
"This pattern is actually the **same** as Jump Game II! Here's the connection:"

```
INTERVAL COVERAGE:
- intervals = [[0,2], [1,5], [3,7]]
- Cover range [0, 7]

JUMP GAME EQUIVALENT:
- At position 0, can "jump" to intervals starting at 0
- Find farthest reach, then "jump" there
- Repeat until we reach the end
```

```
The pattern is sometimes called:
- "Greedy Interval Covering"
- "Jump Game on Intervals"
- "Video Stitching Pattern"
```

---

## Part 4 – The 3-Step Pattern

**(Visual: Pattern summary card.)**

**Instructor:**
"Let's crystallize this into our pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL COVERAGE PATTERN                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT intervals by start time                            │
│                                                             │
│  2. GREEDY LOOP:                                            │
│     current_end = 0 (where we've covered so far)            │
│     farthest = 0 (farthest we can reach in this step)       │
│                                                             │
│     While current_end < target:                             │
│       - Find all intervals with start <= current_end        │
│       - Track the farthest end among them                   │
│       - If no progress possible → return -1                 │
│       - Jump to farthest, increment count                   │
│                                                             │
│  3. RETURN count                                            │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Always extend to the FARTHEST reachable point!        │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – Step-by-Step Walkthrough

**(Visual: Example walkthrough.)**

**Instructor:**
"Let's trace through the example:"

```
intervals = [[0,2], [4,6], [8,10], [1,9], [1,5], [5,9]]
target = 10

Step 0: Sort by start time
Sorted: [[0,2], [1,5], [1,9], [4,6], [5,9], [8,10]]
```

### Round 1: current_end = 0
```
Find intervals with start <= 0:
  [0,2] → end = 2

farthest = 2
Jump! current_end = 2, count = 1
```

### Round 2: current_end = 2
```
Find intervals with start <= 2:
  [1,5] → end = 5
  [1,9] → end = 9  ← farthest!

farthest = 9
Jump! current_end = 9, count = 2
```

### Round 3: current_end = 9
```
Find intervals with start <= 9:
  [4,6] → already passed
  [5,9] → end = 9 (no improvement)
  [8,10] → end = 10 ← farthest!

farthest = 10
Jump! current_end = 10, count = 3
```

### Done!
```
current_end (10) >= target (10)
Return 3 intervals: [0,2], [1,9], [8,10]
```

---

## Part 6 – The Complete Python Solution

**(Visual: Code on screen.)**

**Instructor:**
"Here's the complete solution:"

```python
def minIntervalsToCover(intervals, target):
    if not intervals:
        return -1 if target > 0 else 0
    
    # Step 1: Sort by start time
    intervals.sort()
    
    count = 0
    current_end = 0
    farthest = 0
    i = 0
    n = len(intervals)
    
    # Step 2: Greedy covering
    while current_end < target:
        # Find farthest reachable from current position
        while i < n and intervals[i][0] <= current_end:
            farthest = max(farthest, intervals[i][1])
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

## Part 7 – The Reusable Template

**(Visual: Template card.)**

**Instructor:**
"Here's your reusable template:"

```python
def interval_coverage(intervals, target):
    """
    Find minimum intervals to cover range [0, target].
    
    Args:
        intervals: List of [start, end] intervals
        target: The end of the range to cover (starting from 0)
    
    Returns:
        Minimum count, or -1 if impossible
    """
    if not intervals:
        return -1 if target > 0 else 0
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    count = 0
    current_end = 0  # Where we've covered so far
    farthest = 0     # Farthest we can reach in current step
    i = 0
    
    while current_end < target:
        # Collect all intervals starting at or before current_end
        while i < len(intervals) and intervals[i][0] <= current_end:
            farthest = max(farthest, intervals[i][1])
            i += 1
        
        # Check for gap (no progress possible)
        if farthest == current_end:
            return -1
        
        # Make the greedy jump
        current_end = farthest
        count += 1
    
    return count
```

---

## Part 8 – Handling Edge Cases

**(Visual: Edge cases.)**

**Instructor:**
"Let's handle the tricky edge cases:"

### Case 1: Gap in Coverage
```
intervals = [[0,2], [4,6]]
target = 6

Round 1: current_end = 0 → farthest = 2
Round 2: current_end = 2 → no intervals start at ≤ 2 and end > 2

farthest = 2 = current_end → GAP!
Return -1
```

### Case 2: Doesn't Start at 0
```
intervals = [[1,3], [2,5]]
target = 5

Round 1: current_end = 0
  No intervals with start <= 0
  farthest = 0 = current_end → GAP!
Return -1
```

### Case 3: Single Interval Covers All
```
intervals = [[0,10]]
target = 5

Round 1: farthest = 10, current_end = 10
10 >= 5 → Done!
Return 1
```

---

## 💡 Tip for Students

**Instructor:**
"The key to this pattern is understanding the **two-pointer like** structure:

1. **i** iterates through sorted intervals (never goes back)
2. **current_end** tracks where we've covered to
3. **farthest** finds the best option in the current 'window'

Think of it as:
> 'From where I am, what's the farthest I can reach?'
> Then jump there and repeat!

This is exactly the **Jump Game II** strategy applied to intervals!"

---

## Part 9 – Alternative: Jump Game II Direct Mapping

**(Visual: Direct Jump Game solution.)**

**Instructor:**
"Here's how Jump Game II maps to this pattern:"

```python
# LC 45: Jump Game II
def jump(nums):
    n = len(nums)
    if n <= 1:
        return 0
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(n - 1):  # Don't need to jump from last position
        farthest = max(farthest, i + nums[i])
        
        if i == current_end:  # Must jump!
            jumps += 1
            current_end = farthest
            
            if current_end >= n - 1:
                break
    
    return jumps
```

**Connection:**
- `i + nums[i]` = the "end" of an interval starting at position `i`
- Same greedy logic: jump to farthest reachable!

---

## Part 10 – Other Problems Using This Pattern

**(Visual: Related problems.)**

**Instructor:**
"Here are problems that use the Interval Coverage pattern:"

| Problem | Description |
|---------|-------------|
| **LC 1024 - Video Stitching** | Cover [0, time] with video clips |
| **LC 45 - Jump Game II** | Minimum jumps to reach end |
| **LC 1326 - Minimum Taps** | Cover garden with water taps |
| **LC 1055 - Shortest Way** | Cover string with subsequences |

---

## Part 11 – Pattern Recognition Clues

**(Visual: When to use this pattern.)**

**Instructor:**
"Use the Interval Coverage pattern when:

✅ Need to **cover a continuous range**
✅ Have multiple **overlapping options** to choose from
✅ Want **minimum number** of selections
✅ Intervals can **extend/overlap** each other

**Key indicator:** 'Minimum intervals to cover from A to B'"

---

## Part 12 – Complexity Analysis

**Instructor:**
"Let's analyze the complexity:"

```
Time Complexity: O(n log n)
- Sorting: O(n log n)
- Single pass through intervals: O(n)
- Each interval is visited exactly once!

Space Complexity: O(1) extra
- Only a few variables
- O(log n) if counting sort's stack space
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls to avoid.)**

**Instructor:**
"Watch out for these common mistakes:"

```
❌ Mistake 1: Not sorting first
   Intervals must be sorted by start time!

❌ Mistake 2: Forgetting to check for gaps
   if farthest == current_end: return -1

❌ Mistake 3: Wrong loop termination
   Continue while current_end < target, not <=

❌ Mistake 4: Not handling "doesn't start at 0"
   If no interval starts at 0, we can't cover!

❌ Mistake 5: Off-by-one with target
   Make sure you understand if target is inclusive/exclusive
```

---

## Part 14 – Closing

**Instructor:**
"And that's the Interval Coverage pattern!

**Key takeaways:**
1. **Sort by start time**
2. **Greedy**: Always extend to the farthest reachable point
3. **Two-pointer style**: current_end and farthest
4. **Same as Jump Game II** logic!

This completes our interval pattern collection! Practice with LC 1024, LC 45, and LC 1326!"

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL COVERAGE CHEAT SHEET                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  GOAL: Minimum intervals to cover [0, target]               │
│                                                             │
│  ALGORITHM:                                                 │
│    1. Sort by start time                                    │
│    2. current_end = 0, farthest = 0                         │
│    3. While current_end < target:                           │
│       - Find max end among intervals with start <= cur_end  │
│       - If no progress: return -1 (gap!)                    │
│       - Jump: current_end = farthest, count++               │
│    4. Return count                                          │
│                                                             │
│  SAME AS: Jump Game II strategy!                            │
│                                                             │
│  TIME: O(n log n)    SPACE: O(1)                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

