# 🎬 Tutorial Script: LC 352 — Data Stream as Disjoint Intervals

---

## Part 0 – Opening

**(Instructor on camera.)**

**Instructor:**
"Welcome back! Today we're solving **LC 352 - Data Stream as Disjoint Intervals**, another **Hard** problem.

But here's the good news: it's really just **Insert Interval** applied to single numbers! Let me show you."

---

## Part 1 – The Problem Statement

**(Visual: LeetCode problem.)**

**Instructor:**
"Here's the problem:

> Given a data stream of **non-negative integers** a₁, a₂, ..., aₙ, summarize the numbers seen so far as a list of **disjoint intervals**.

Implement:
- **addNum(val)**: Add an integer to the data stream
- **getIntervals()**: Return the current disjoint intervals"

**Example:**
```python
ds = SummaryRanges()
ds.addNum(1)       # intervals: [[1,1]]
ds.addNum(3)       # intervals: [[1,1], [3,3]]
ds.addNum(7)       # intervals: [[1,1], [3,3], [7,7]]
ds.addNum(2)       # intervals: [[1,3], [7,7]]  ← 1,2,3 merge!
ds.addNum(6)       # intervals: [[1,3], [6,7]]  ← 6,7 merge!
```

---

## Part 2 – The Key Insight

**(Visual: Connection to Insert Interval.)**

**Instructor:**
"Here's the insight that makes this easy:

> Adding a single number `val` is the same as inserting interval `[val, val]`!

But there's a twist: consecutive numbers should merge.

```
[1,1] and [2,2] should become [1,2]
[3,5] and [6,6] should become [3,6]
```

So when we insert `[val, val]`, we need to check for **adjacent** intervals too, not just overlapping ones."

---

## Part 3 – Modifying the Insert Interval Pattern

**(Visual: Modified conditions.)**

**Instructor:**
"The standard Insert Interval pattern uses:

```
Phase 1: interval.end < new.start  (before, no overlap)
Phase 2: interval.start <= new.end (overlap or touching)
```

**Recall:** The full overlap condition is `new.start <= interval.end AND interval.start <= new.end`. Phase 1 stopping guarantees the first part, so Phase 2 only checks the second.

For this problem, we need to handle **consecutive** numbers:

```
Phase 1: interval.end < val - 1    (before, with gap)
Phase 2: interval.start <= val + 1 (overlap OR adjacent)
```

The `val - 1` and `val + 1` account for consecutive integers!

**Same logic applies:** When Phase 1 stops with `interval.end >= val - 1`, we know there's potential overlap or adjacency, so Phase 2 checks the other side.

💡 **Tip:** If you find the simplified conditions confusing, you can use the full overlap/adjacency condition instead — it's also 100% correct!"

**(Visual: Example.)**

```
Current: [[1,3], [7,9]]
Add: 5

Standard Insert: [5,5] doesn't overlap with any → [[1,3], [5,5], [7,9]]

But if we add 4:
[4,4] is adjacent to [1,3] (3+1=4) → merge to [[1,4], [7,9]]

Then add 5:
[5,5] is adjacent to [1,4] (4+1=5) → merge to [[1,5], [7,9]]

Then add 6:
[6,6] is adjacent to [1,5] AND [7,9] → merge to [[1,9]]
```

---

## Part 4 – Step-by-Step Algorithm

**(Visual: Algorithm steps.)**

**Instructor:**
"Here's our modified 3-phase approach."

### Phase 1: Add Intervals BEFORE (with gap)

```python
# Condition: interval ends before val-1
# This means there's at least one number gap
while i < n and intervals[i][1] < val - 1:
    result.append(intervals[i])
    i += 1
```

### Phase 2: Merge Adjacent/Overlapping

```python
# Condition: interval starts at or before val+1
# This catches both overlap AND adjacency
new_start = val
new_end = val

while i < n and intervals[i][0] <= val + 1:
    new_start = min(new_start, intervals[i][0])
    new_end = max(new_end, intervals[i][1])
    i += 1

result.append([new_start, new_end])
```

### Phase 3: Add Remaining Intervals

```python
while i < n:
    result.append(intervals[i])
    i += 1
```

---

## Part 5 – Complete Solution

**(Visual: Full implementation.)**

**Instructor:**
"Here's the complete solution."

```python
class SummaryRanges:
    def __init__(self):
        self.intervals = []
    
    def addNum(self, val: int) -> None:
        result = []
        i = 0
        n = len(self.intervals)
        
        # Phase 1: Add intervals that end before val-1 (gap exists)
        while i < n and self.intervals[i][1] < val - 1:
            result.append(self.intervals[i])
            i += 1
        
        # Phase 2: Merge overlapping and adjacent intervals
        new_start = val
        new_end = val
        
        while i < n and self.intervals[i][0] <= val + 1:
            new_start = min(new_start, self.intervals[i][0])
            new_end = max(new_end, self.intervals[i][1])
            i += 1
        
        result.append([new_start, new_end])
        
        # Phase 3: Add remaining intervals
        while i < n:
            result.append(self.intervals[i])
            i += 1
        
        self.intervals = result
    
    def getIntervals(self) -> List[List[int]]:
        return self.intervals
```

---

## Part 6 – Walkthrough Example

**(Visual: Step-by-step trace.)**

**Instructor:**
"Let's trace through the example."

```python
ds = SummaryRanges()

# addNum(1)
intervals = []
→ Phase 1: nothing
→ Phase 2: [1,1]
intervals = [[1,1]]

# addNum(3)
intervals = [[1,1]]
→ Phase 1: [1,1] ends at 1, 1 < 3-1=2? YES → add [1,1]
→ Phase 2: no overlap (empty loop)
→ [3,3] added
intervals = [[1,1], [3,3]]

# addNum(7)
→ Phase 1: [1,1] → 1 < 6? YES → add
           [3,3] → 3 < 6? YES → add
→ Phase 2: no overlap
→ [7,7] added
intervals = [[1,1], [3,3], [7,7]]

# addNum(2)
→ Phase 1: [1,1] → 1 < 1? NO → stop
→ Phase 2: [1,1] → 1 <= 3? YES → merge [1,1]
           [3,3] → 3 <= 3? YES → merge [3,3]
           [7,7] → 7 <= 3? NO → stop
           new_interval = [min(2,1,3), max(2,1,3)] = [1,3]
→ Phase 3: [7,7] added
intervals = [[1,3], [7,7]]

# addNum(6)
→ Phase 1: [1,3] → 3 < 5? YES → add
→ Phase 2: [7,7] → 7 <= 7? YES → merge
           new_interval = [min(6,7), max(6,7)] = [6,7]
→ Phase 3: nothing
intervals = [[1,3], [6,7]]
```

**Final Result:** `[[1,3], [6,7]]` ✓

---

## Part 7 – Alternative: Binary Search + TreeMap

**(Visual: Optimized approach.)**

**Instructor:**
"For better performance with many operations, we can use a TreeMap (or sorted container)."

```python
from sortedcontainers import SortedDict

class SummaryRanges:
    def __init__(self):
        self.intervals = SortedDict()  # start -> end
    
    def addNum(self, val: int) -> None:
        starts = self.intervals.keys()
        
        # Find the insertion position
        idx = self.intervals.bisect_right(val)
        
        # Check left neighbor (interval that might contain or be adjacent to val)
        merge_left = False
        if idx > 0:
            prev_start = starts[idx - 1]
            prev_end = self.intervals[prev_start]
            if prev_end >= val - 1:  # Contains or adjacent
                merge_left = True
        
        # Check right neighbor (interval that might be adjacent)
        merge_right = False
        if idx < len(self.intervals):
            next_start = starts[idx]
            if next_start <= val + 1:  # Adjacent or overlapping
                merge_right = True
        
        # Merge logic
        if merge_left and merge_right:
            # Merge left and right through val
            prev_start = starts[idx - 1]
            next_start = starts[idx]
            new_end = max(self.intervals[prev_start], self.intervals[next_start], val)
            self.intervals[prev_start] = new_end
            del self.intervals[next_start]
        elif merge_left:
            prev_start = starts[idx - 1]
            self.intervals[prev_start] = max(self.intervals[prev_start], val)
        elif merge_right:
            next_start = starts[idx]
            next_end = self.intervals[next_start]
            del self.intervals[next_start]
            self.intervals[val] = next_end
        else:
            self.intervals[val] = val
    
    def getIntervals(self) -> List[List[int]]:
        return [[s, e] for s, e in self.intervals.items()]
```

---

## Part 8 – Using a Set (Simpler Alternative)

**(Visual: Set-based approach.)**

**Instructor:**
"If the value range is small, we can use a set and build intervals on demand."

```python
class SummaryRanges:
    def __init__(self):
        self.seen = set()
    
    def addNum(self, val: int) -> None:
        self.seen.add(val)
    
    def getIntervals(self) -> List[List[int]]:
        if not self.seen:
            return []
        
        intervals = []
        sorted_vals = sorted(self.seen)
        
        start = end = sorted_vals[0]
        
        for val in sorted_vals[1:]:
            if val == end + 1:
                end = val
            else:
                intervals.append([start, end])
                start = end = val
        
        intervals.append([start, end])
        return intervals
```

"This is O(1) for addNum but O(n log n) for getIntervals. Good if getIntervals is called rarely."

---

## Part 9 – Complexity Analysis

**(Visual: Complexity table.)**

### Approach 1: List-based (Our Solution)

| Operation | Time | Space |
|-----------|------|-------|
| addNum | O(n) | O(n) |
| getIntervals | O(1) | O(1) |

### Approach 2: TreeMap/SortedDict

| Operation | Time | Space |
|-----------|------|-------|
| addNum | O(log n) | O(n) |
| getIntervals | O(n) | O(n) |

### Approach 3: Set-based

| Operation | Time | Space |
|-----------|------|-------|
| addNum | O(1) | O(n) |
| getIntervals | O(n log n) | O(n) |

---

## Part 10 – Key Differences from Standard Insert Interval

**(Visual: Comparison table.)**

**Instructor:**
"Let's compare this with standard Insert Interval."

| Aspect | LC 57 Insert Interval | LC 352 Data Stream |
|--------|----------------------|-------------------|
| Input | Interval [left, right] | Single number val |
| Treated as | [left, right] | [val, val] |
| Phase 1 | end < new.start | end < val - 1 |
| Phase 2 | start <= new.end | start <= val + 1 |
| Adjacent | Not considered | Must merge |

**The key difference:**

> "We use `val - 1` and `val + 1` to handle consecutive integers!"

---

## Part 11 – Edge Cases

**(Visual: Edge cases.)**

**Instructor:**
"Let's verify edge cases."

### Edge Case 1: Adding Same Number Twice
```python
ds.addNum(5)  # [[5,5]]
ds.addNum(5)  # [[5,5]] (no change)
```
✓ Handled — merging [5,5] with itself

### Edge Case 2: Sequential Numbers
```python
ds.addNum(1)  # [[1,1]]
ds.addNum(2)  # [[1,2]]
ds.addNum(3)  # [[1,3]]
```
✓ Correctly merges adjacent numbers

### Edge Case 3: Filling a Gap
```python
ds.addNum(1)  # [[1,1]]
ds.addNum(3)  # [[1,1], [3,3]]
ds.addNum(2)  # [[1,3]] ← gap filled!
```
✓ Merges both neighbors when gap is filled

### Edge Case 4: First Element
```python
ds.addNum(5)  # [[5,5]]
```
✓ Handles empty intervals list

---

## Part 12 – Wrap-Up

**Instructor:**
"LC 352 looks intimidating, but it's really:

> Insert Interval + handle consecutive numbers

Key modifications:
1. Treat single number `val` as interval `[val, val]`
2. Phase 1 condition: `end < val - 1` (leaves gap)
3. Phase 2 condition: `start <= val + 1` (catches adjacency)

The pattern stays the same: **Before → Merge → After**

Thanks for watching, and I'll see you in the next video!"

---

## Quick Reference

```python
def addNum(self, val: int) -> None:
    result = []
    i = 0
    n = len(self.intervals)
    
    # Phase 1: Before (with gap)
    while i < n and self.intervals[i][1] < val - 1:
        result.append(self.intervals[i])
        i += 1
    
    # Phase 2: Merge (including adjacent)
    new_start, new_end = val, val
    while i < n and self.intervals[i][0] <= val + 1:
        new_start = min(new_start, self.intervals[i][0])
        new_end = max(new_end, self.intervals[i][1])
        i += 1
    result.append([new_start, new_end])
    
    # Phase 3: After
    while i < n:
        result.append(self.intervals[i])
        i += 1
    
    self.intervals = result
```

| Aspect | Details |
|--------|---------|
| Pattern | Insert Interval (modified) |
| Key Mod | val-1 and val+1 for adjacency |
| Phase 1 | end < val - 1 |
| Phase 2 | start <= val + 1 |
| Time | O(n) per addNum |
| Space | O(n) |

