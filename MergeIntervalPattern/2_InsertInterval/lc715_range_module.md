# 🎬 Tutorial Script: LC 715 — Range Module (Insert Interval + Remove)

---

## Part 0 – Opening

**(Instructor on camera.)**

**Instructor:**
"Welcome back! Today we're tackling **LC 715 - Range Module**, a **Hard** problem.

But don't worry — if you understand the Insert Interval pattern, this becomes manageable. It's essentially the same pattern applied to multiple operations!"

---

## Part 1 – The Problem Statement

**(Visual: LeetCode problem.)**

**Instructor:**
"A **Range Module** is a data structure that tracks ranges of numbers. Implement three operations:

1. **addRange(left, right)**: Add the interval `[left, right)` (half-open)
2. **queryRange(left, right)**: Return true if every number in `[left, right)` is tracked
3. **removeRange(left, right)**: Remove tracking for numbers in `[left, right)`"

**Example:**
```python
rm = RangeModule()
rm.addRange(10, 20)      # tracked: [10, 20)
rm.removeRange(14, 16)   # tracked: [10, 14), [16, 20)
rm.queryRange(10, 14)    # True - fully covered
rm.queryRange(13, 15)    # False - 14-15 not covered
rm.queryRange(16, 17)    # True - fully covered
```

---

## Part 2 – The Key Insight

**(Visual: Connection to Insert Interval.)**

**Instructor:**
"Here's the insight:

> **addRange** is just **Insert Interval** with half-open intervals!
> **removeRange** is the **inverse** — we keep what's NOT in the range.

We maintain a sorted list of non-overlapping intervals, just like in LC 57."

**(Visual: Comparison.)**

```
Insert Interval (LC 57):
  - Add new interval
  - Merge overlapping

addRange (LC 715):
  - Same thing!
  - But with half-open intervals [left, right)

removeRange (LC 715):
  - Split intervals that overlap with the range
  - Keep parts outside the removed range
```

**Recall - Why the Insert Interval conditions work:**

The full overlap condition requires TWO checks: `new.start <= interval.end AND interval.start <= new.end`

But in our 3-phase approach:
- Phase 1 stopping guarantees `new.start <= interval.end` (first condition)
- Phase 2 only needs to check `interval.start <= new.end` (second condition)

This same logic applies to `addRange` in Range Module!

💡 **Tip:** If the simplified conditions feel confusing, you can always use the full overlap condition — it's also correct!

---

## Part 3 – Understanding Half-Open Intervals

**(Visual: Half-open vs closed.)**

**Instructor:**
"Quick note on **half-open intervals** `[left, right)`:

- Includes `left`
- Excludes `right`
- `[10, 20)` means 10, 11, 12, ..., 19

This is common in programming because it makes math easier:
- Length = right - left
- No off-by-one errors when concatenating"

---

## Part 4 – addRange Implementation

**(Visual: addRange logic.)**

**Instructor:**
"Let's implement `addRange`. It's the Insert Interval pattern!"

```python
def addRange(self, left: int, right: int) -> None:
    result = []
    i = 0
    n = len(self.intervals)
    
    # Phase 1: Add intervals that end BEFORE [left, right)
    while i < n and self.intervals[i][1] < left:
        result.append(self.intervals[i])
        i += 1
    
    # Phase 2: Merge overlapping intervals
    while i < n and self.intervals[i][0] <= right:
        left = min(left, self.intervals[i][0])
        right = max(right, self.intervals[i][1])
        i += 1
    result.append([left, right])
    
    # Phase 3: Add remaining intervals
    while i < n:
        result.append(self.intervals[i])
        i += 1
    
    self.intervals = result
```

"This is almost identical to LC 57!"

---

## Part 5 – removeRange Implementation

**(Visual: removeRange logic.)**

**Instructor:**
"Now for `removeRange`. This is the **inverse** operation.

When we encounter an overlapping interval, we need to **keep the parts outside** the removed range."

```
Before: [10, 20)
Remove: [14, 16)
After:  [10, 14), [16, 20)

The overlap [14, 16) is removed.
We keep [10, 14) and [16, 20).
```

**(Visual: Code.)**

```python
def removeRange(self, left: int, right: int) -> None:
    result = []
    
    for interval in self.intervals:
        start, end = interval
        
        # No overlap: interval is completely BEFORE or AFTER
        if end <= left or start >= right:
            result.append(interval)
        
        # Overlap: keep parts outside [left, right)
        else:
            # Keep the LEFT part (if exists)
            if start < left:
                result.append([start, left])
            
            # Keep the RIGHT part (if exists)
            if end > right:
                result.append([right, end])
    
    self.intervals = result
```

**Instructor:**
"The key insight:

- If interval ends before `left` or starts after `right` → no overlap, keep it
- Otherwise, we might keep a left piece `[start, left)` and/or a right piece `[right, end)`"

---

## Part 6 – queryRange Implementation

**(Visual: queryRange logic.)**

**Instructor:**
"Finally, `queryRange` checks if a range is fully covered."

```python
def queryRange(self, left: int, right: int) -> bool:
    # Find an interval that fully contains [left, right)
    for interval in self.intervals:
        if interval[0] <= left and right <= interval[1]:
            return True
        # Optimization: if we've passed where it could be, stop early
        if interval[0] > left:
            break
    return False
```

"We just need to find one interval where `start ≤ left` and `right ≤ end`."

---

## Part 7 – Complete Solution

**(Visual: Full class.)**

**Instructor:**
"Here's the complete implementation."

```python
class RangeModule:
    def __init__(self):
        self.intervals = []
    
    def addRange(self, left: int, right: int) -> None:
        result = []
        i = 0
        n = len(self.intervals)
        
        # Phase 1: Add intervals before [left, right)
        while i < n and self.intervals[i][1] < left:
            result.append(self.intervals[i])
            i += 1
        
        # Phase 2: Merge overlapping
        while i < n and self.intervals[i][0] <= right:
            left = min(left, self.intervals[i][0])
            right = max(right, self.intervals[i][1])
            i += 1
        result.append([left, right])
        
        # Phase 3: Add remaining
        while i < n:
            result.append(self.intervals[i])
            i += 1
        
        self.intervals = result
    
    def queryRange(self, left: int, right: int) -> bool:
        for start, end in self.intervals:
            if start <= left and right <= end:
                return True
            if start > left:
                break
        return False
    
    def removeRange(self, left: int, right: int) -> None:
        result = []
        
        for start, end in self.intervals:
            # No overlap
            if end <= left or start >= right:
                result.append([start, end])
            else:
                # Keep left part
                if start < left:
                    result.append([start, left])
                # Keep right part
                if end > right:
                    result.append([right, end])
        
        self.intervals = result
```

---

## Part 8 – Walkthrough Example

**(Visual: Step-by-step.)**

**Instructor:**
"Let's trace through the example."

```python
rm = RangeModule()

# addRange(10, 20)
intervals = []
→ Phase 2: Add [10, 20)
intervals = [[10, 20]]

# removeRange(14, 16)
[10, 20) overlaps with [14, 16)
→ Keep left part: [10, 14)
→ Keep right part: [16, 20)
intervals = [[10, 14], [16, 20]]

# queryRange(10, 14)
[10, 14] → 10 <= 10 and 14 <= 14? YES!
→ True ✓

# queryRange(13, 15)
[10, 14] → 10 <= 13 but 15 > 14? NO
[16, 20] → 16 > 13, break
→ False ✓

# queryRange(16, 17)
[16, 20] → 16 <= 16 and 17 <= 20? YES!
→ True ✓
```

---

## Part 9 – Optimized Version with Binary Search

**(Visual: Binary search optimization.)**

**Instructor:**
"For better performance, we can use binary search to find intervals faster."

```python
import bisect

class RangeModule:
    def __init__(self):
        # Store as flat list: [start1, end1, start2, end2, ...]
        self.track = []
    
    def addRange(self, left: int, right: int) -> None:
        i = bisect.bisect_left(self.track, left)
        j = bisect.bisect_right(self.track, right)
        
        # Adjust boundaries based on insertion points
        subtrack = []
        if i % 2 == 0:  # left not inside existing interval
            subtrack.append(left)
        if j % 2 == 0:  # right not inside existing interval
            subtrack.append(right)
        
        self.track[i:j] = subtrack
    
    def queryRange(self, left: int, right: int) -> bool:
        i = bisect.bisect_right(self.track, left)
        j = bisect.bisect_left(self.track, right)
        return i == j and i % 2 == 1
    
    def removeRange(self, left: int, right: int) -> None:
        i = bisect.bisect_left(self.track, left)
        j = bisect.bisect_right(self.track, right)
        
        subtrack = []
        if i % 2 == 1:  # left inside existing interval
            subtrack.append(left)
        if j % 2 == 1:  # right inside existing interval
            subtrack.append(right)
        
        self.track[i:j] = subtrack
```

"This is more complex but achieves O(log n) for finding insertion points."

---

## Part 10 – Complexity Analysis

**(Visual: Complexity table.)**

### Simple List Approach

| Operation | Time | Space |
|-----------|------|-------|
| addRange | O(n) | O(n) |
| queryRange | O(n) | O(1) |
| removeRange | O(n) | O(n) |

### Binary Search Approach

| Operation | Time | Space |
|-----------|------|-------|
| addRange | O(n) worst, O(log n) find | O(n) |
| queryRange | O(log n) | O(1) |
| removeRange | O(n) worst, O(log n) find | O(n) |

---

## Part 11 – Key Insights

**Instructor:**
"Let's summarize the key patterns used."

| Operation | Pattern |
|-----------|---------|
| addRange | Insert Interval (3-phase) |
| removeRange | Inverse of Insert — keep outsides |
| queryRange | Find containing interval |

**The big idea:**

> "Complex data structures can be built from simple patterns.  
> Range Module = Insert Interval + Remove logic + Query"

---

## Part 12 – Wrap-Up

**Instructor:**
"LC 715 is a Hard problem, but it's really just:

1. **addRange** = Insert Interval pattern
2. **removeRange** = Keep parts outside the removed range
3. **queryRange** = Check if any interval fully contains the range

Master Insert Interval, and Range Module becomes approachable.

Thanks for watching!"

---

## Quick Reference

```python
# addRange: Insert Interval pattern
while i < n and intervals[i][1] < left: # Before
while i < n and intervals[i][0] <= right: # Merge

# removeRange: Keep parts outside
if start < left: keep [start, left)
if end > right: keep [right, end)

# queryRange: Find container
if start <= left and right <= end: return True
```

| Aspect | Details |
|--------|---------|
| Pattern | Insert Interval (for add) |
| Remove Logic | Split and keep outsides |
| Query Logic | Find containing interval |
| Time | O(n) per operation |
| Key Insight | Inverse of Insert = Remove |

