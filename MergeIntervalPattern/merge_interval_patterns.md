# 🔀 Merge Interval Pattern — Complete Guide

The **Merge Interval** pattern is one of the most frequently tested patterns in coding interviews. It deals with overlapping intervals and requires understanding how intervals interact with each other.

---

## 📌 Core Concept

An **interval** is typically represented as `[start, end]`. 

### Understanding Overlap (Critical!)

Two intervals **overlap** if and only if **BOTH** conditions are true:

```
Condition 1: interval1.start <= interval2.end   (A starts before B ends)
Condition 2: interval2.start <= interval1.end   (B starts before A ends)
```

**Visual:**
```
OVERLAP (both conditions true):
    [----A----]
         [----B----]
    A.start ≤ B.end ✓  AND  B.start ≤ A.end ✓

NO OVERLAP (at least one condition false):
    [----A----]
                   [----B----]
    A.start ≤ B.end ✓  BUT  B.start ≤ A.end ✗ (B starts after A ends)
```

**Equivalently, NO overlap if:**
```
interval1.end < interval2.start  OR  interval2.end < interval1.start
(One ends before the other starts)
```

### Why We Can Simplify After Sorting

When intervals are **sorted by start time**, we compare each interval with the previous one. Since they're sorted:
- We know: `current.start >= previous.start` (guaranteed by sorting)
- So we only need to check: `current.start <= previous.end`

This is why the merge condition simplifies to just **one check**!

### 💡 Tip for Students

If the simplified condition feels confusing, you can always use the **full overlap condition** instead:

```python
# Simplified (after sorting):
if current[0] <= last[1]:   # Just one check!

# Full condition (also correct!):
if last[0] <= current[1] and current[0] <= last[1]:   # Both checks
```

**Both are 100% correct!** The simplified version is an optimization. Use whichever feels more natural to you.

The key insight: **Sort intervals by start time first**, then process them sequentially.

---

## 🎯 The 6 Subpatterns

| # | Subpattern | Key Operation | Example Problems |
|---|-----------|---------------|------------------|
| 1 | Merge Overlapping | Combine overlapping intervals | LC 56, LC 986 |
| 2 | Insert Interval | Add new interval & merge | LC 57 |
| 3 | Interval Intersection | Find common parts | LC 986 |
| 4 | Non-overlapping Count | Min removals for no overlap | LC 435, LC 452 |
| 5 | Meeting Rooms | Scheduling & conflicts | LC 252, LC 253 |
| 6 | Interval Coverage | Cover a range | LC 1024, LC 45 |

---

## 📂 Subpattern 1: Merge Overlapping Intervals

### Problem Statement
Given a collection of intervals, merge all overlapping intervals.

### Template
```python
def merge(intervals):
    if not intervals:
        return []
    
    # Step 1: Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        last = merged[-1]
        
        # Check if overlapping
        if current[0] <= last[1]:
            # Merge: extend the end
            last[1] = max(last[1], current[1])
        else:
            # No overlap: add new interval
            merged.append(current)
    
    return merged
```

### Visual Example
```
Input:  [[1,3], [2,6], [8,10], [15,18]]

After sorting: [[1,3], [2,6], [8,10], [15,18]]

Step 1: merged = [[1,3]]
Step 2: [2,6] overlaps with [1,3] → merge to [1,6]
        merged = [[1,6]]
Step 3: [8,10] doesn't overlap → add
        merged = [[1,6], [8,10]]
Step 4: [15,18] doesn't overlap → add
        merged = [[1,6], [8,10], [15,18]]

Output: [[1,6], [8,10], [15,18]]
```

### LeetCode Problems
- **LC 56 - Merge Intervals** ⭐ (Classic)
- **LC 759 - Employee Free Time**

---

## 📂 Subpattern 2: Insert Interval

### Problem Statement
Insert a new interval into a sorted list of non-overlapping intervals and merge if necessary.

### Template
```python
def insert(intervals, newInterval):
    result = []
    i = 0
    n = len(intervals)
    
    # Part 1: Add all intervals that come BEFORE newInterval
    while i < n and intervals[i][1] < newInterval[0]:
        result.append(intervals[i])
        i += 1
    
    # Part 2: Merge all overlapping intervals with newInterval
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    result.append(newInterval)
    
    # Part 3: Add all intervals that come AFTER
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

### Visual Example
```
intervals = [[1,2], [3,5], [6,7], [8,10], [12,16]]
newInterval = [4,8]

Part 1: [1,2] ends before [4,8] starts → add [1,2]
Part 2: [3,5], [6,7], [8,10] all overlap with [4,8]
        Merge: min(4,3,6,8)=3, max(8,5,7,10)=10 → [3,10]
Part 3: [12,16] comes after → add

Output: [[1,2], [3,10], [12,16]]
```

### LeetCode Problems
- **LC 57 - Insert Interval** ⭐

---

## 📂 Subpattern 3: Interval Intersection

### Problem Statement
Given two lists of sorted intervals, find their intersection.

### Template
```python
def intervalIntersection(firstList, secondList):
    result = []
    i, j = 0, 0
    
    while i < len(firstList) and j < len(secondList):
        # Find the overlap
        start = max(firstList[i][0], secondList[j][0])
        end = min(firstList[i][1], secondList[j][1])
        
        # Valid intersection exists
        if start <= end:
            result.append([start, end])
        
        # Move the pointer with smaller end
        if firstList[i][1] < secondList[j][1]:
            i += 1
        else:
            j += 1
    
    return result
```

### Visual Example
```
firstList:  [[0,2], [5,10], [13,23], [24,25]]
secondList: [[1,5], [8,12], [15,24], [25,26]]

i=0, j=0: [0,2] ∩ [1,5] → max(0,1)=1, min(2,5)=2 → [1,2] ✓
          2 < 5, so i++
i=1, j=0: [5,10] ∩ [1,5] → max(5,1)=5, min(10,5)=5 → [5,5] ✓
          5 < 10, so j++
i=1, j=1: [5,10] ∩ [8,12] → max(5,8)=8, min(10,12)=10 → [8,10] ✓
          10 < 12, so i++
...

Output: [[1,2], [5,5], [8,10], [15,23], [24,24], [25,25]]
```

### LeetCode Problems
- **LC 986 - Interval List Intersections** ⭐

---

## 📂 Subpattern 4: Non-overlapping Intervals (Greedy)

### Problem Statement
Find minimum number of intervals to remove to make the rest non-overlapping.

### Key Insight
**Greedy approach**: Always keep the interval with the smaller end time (leaves more room for future intervals).

### Template
```python
def eraseOverlapIntervals(intervals):
    if not intervals:
        return 0
    
    # Sort by END time (greedy choice)
    intervals.sort(key=lambda x: x[1])
    
    count = 0
    prev_end = float('-inf')
    
    for start, end in intervals:
        if start >= prev_end:
            # No overlap, keep this interval
            prev_end = end
        else:
            # Overlap, remove this interval
            count += 1
    
    return count
```

### Visual Example
```
Input: [[1,2], [2,3], [3,4], [1,3]]

After sorting by end: [[1,2], [2,3], [1,3], [3,4]]

prev_end = -∞
[1,2]: 1 >= -∞ ✓ keep, prev_end = 2
[2,3]: 2 >= 2 ✓ keep, prev_end = 3
[1,3]: 1 < 3 ✗ overlap, remove (count = 1)
[3,4]: 3 >= 3 ✓ keep, prev_end = 4

Output: 1 (remove [1,3])
```

### LeetCode Problems
- **LC 435 - Non-overlapping Intervals** ⭐
- **LC 452 - Minimum Number of Arrows to Burst Balloons**
- **LC 646 - Maximum Length of Pair Chain**

---

## 📂 Subpattern 5: Meeting Rooms (Scheduling)

### 5a. Can Attend All Meetings?
Check if one person can attend all meetings (no overlaps).

```python
def canAttendMeetings(intervals):
    intervals.sort(key=lambda x: x[0])
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False
    
    return True
```

### 5b. Minimum Meeting Rooms Required
Find the minimum number of rooms needed for all meetings.

```python
import heapq

def minMeetingRooms(intervals):
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[0])
    
    # Min-heap to track end times of ongoing meetings
    heap = []
    
    for start, end in intervals:
        # If earliest ending meeting is done, reuse that room
        if heap and heap[0] <= start:
            heapq.heappop(heap)
        
        heapq.heappush(heap, end)
    
    return len(heap)
```

### Alternative: Sweep Line Algorithm
```python
def minMeetingRooms(intervals):
    events = []
    
    for start, end in intervals:
        events.append((start, 1))   # Meeting starts
        events.append((end, -1))    # Meeting ends
    
    events.sort()
    
    rooms = 0
    max_rooms = 0
    
    for time, delta in events:
        rooms += delta
        max_rooms = max(max_rooms, rooms)
    
    return max_rooms
```

### Visual Example (Sweep Line)
```
Meetings: [[0,30], [5,10], [15,20]]

Events: [(0,+1), (5,+1), (10,-1), (15,+1), (20,-1), (30,-1)]
Sorted: [(0,+1), (5,+1), (10,-1), (15,+1), (20,-1), (30,-1)]

Time 0:  rooms = 1, max = 1
Time 5:  rooms = 2, max = 2  ← Peak!
Time 10: rooms = 1
Time 15: rooms = 2, max = 2
Time 20: rooms = 1
Time 30: rooms = 0

Output: 2 rooms needed
```

### LeetCode Problems
- **LC 252 - Meeting Rooms** (can attend all?)
- **LC 253 - Meeting Rooms II** ⭐ (min rooms)
- **LC 1094 - Car Pooling**

---

## 📂 Subpattern 6: Interval Coverage

### Problem Statement
Given a range `[0, n]` and a list of intervals, find the minimum number of intervals to cover the entire range.

### Template (Jump Game II variant)
```python
def minIntervalsToCover(intervals, n):
    intervals.sort()
    
    count = 0
    current_end = 0
    farthest = 0
    i = 0
    
    while current_end < n:
        # Find farthest reachable point from current position
        while i < len(intervals) and intervals[i][0] <= current_end:
            farthest = max(farthest, intervals[i][1])
            i += 1
        
        if farthest == current_end:
            return -1  # Cannot cover
        
        current_end = farthest
        count += 1
    
    return count
```

### LeetCode Problems
- **LC 1024 - Video Stitching**
- **LC 45 - Jump Game II** (intervals as jumps)
- **LC 1326 - Minimum Number of Taps to Open**

---

## 🧠 Pattern Recognition Cheat Sheet

| If the problem asks... | Use this subpattern |
|------------------------|---------------------|
| "Merge overlapping intervals" | Subpattern 1 |
| "Insert new interval" | Subpattern 2 |
| "Find intersection/common time" | Subpattern 3 |
| "Minimum removals for no overlap" | Subpattern 4 |
| "Can attend all meetings?" | Subpattern 5a |
| "How many rooms/resources needed?" | Subpattern 5b |
| "Cover a range with minimum intervals" | Subpattern 6 |

---

## 🔑 Key Techniques to Remember

### 1. Sorting Strategy
- **By start time**: Merge, Insert, Intersection
- **By end time**: Non-overlapping (greedy)

### 2. Two Ways to Check Overlap
```python
# Overlap exists if:
not (a[1] < b[0] or b[1] < a[0])

# Equivalent to:
a[0] <= b[1] and b[0] <= a[1]
```

### 3. Sweep Line for Concurrent Events
When counting simultaneous events:
```python
events = []
for start, end in intervals:
    events.append((start, +1))
    events.append((end, -1))
events.sort()
```

### 4. Min-Heap for Dynamic End Times
When you need to track the earliest ending interval:
```python
import heapq
heap = []  # stores end times
heapq.heappush(heap, end)
earliest = heap[0]
```

---

## 📊 Time & Space Complexity Summary

| Subpattern | Time | Space |
|------------|------|-------|
| Merge Overlapping | O(n log n) | O(n) |
| Insert Interval | O(n) | O(n) |
| Interval Intersection | O(m + n) | O(m + n) |
| Non-overlapping | O(n log n) | O(1) |
| Meeting Rooms (Heap) | O(n log n) | O(n) |
| Meeting Rooms (Sweep) | O(n log n) | O(n) |
| Interval Coverage | O(n log n) | O(1) |

---

## 📝 Practice Order (Recommended)

1. **LC 56 - Merge Intervals** → Foundation
2. **LC 57 - Insert Interval** → Build on foundation
3. **LC 986 - Interval List Intersections** → Two-pointer variation
4. **LC 252 - Meeting Rooms** → Simple overlap check
5. **LC 253 - Meeting Rooms II** → Heap/Sweep line
6. **LC 435 - Non-overlapping Intervals** → Greedy thinking
7. **LC 452 - Minimum Arrows** → Greedy variant
8. **LC 1024 - Video Stitching** → Coverage problem

---

## 💡 Pro Tips

1. **Always draw the intervals** on a number line when confused
2. **Sort first** — almost every interval problem requires sorting
3. **Edge cases**: empty input, single interval, all overlapping, none overlapping
4. **When in doubt**, try both sorting by start AND sorting by end to see which fits

Happy coding! 🚀

