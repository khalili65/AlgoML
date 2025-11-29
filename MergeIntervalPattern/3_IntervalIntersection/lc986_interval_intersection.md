# 🎬 Tutorial Script: LC 986 — Interval List Intersections

---

## Part 1 – Problem Introduction

**(Visual: LeetCode problem header.)**

**Instructor:**
"Let's solve **LeetCode 986: Interval List Intersections** — this is the classic problem that directly applies our Interval Intersection pattern!"

---

## Part 2 – Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:"

```
You are given two lists of closed intervals, firstList and secondList, 
where firstList[i] = [start_i, end_i] and secondList[j] = [start_j, end_j].

Each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

A closed interval [a, b] (with a <= b) denotes the set of real numbers x 
with a <= x <= b.

The intersection of two closed intervals is a set of real numbers that 
are either empty or represented as a closed interval.
```

**(Visual: Examples.)**

```
Example 1:
Input: firstList  = [[0,2],[5,10],[13,23],[24,25]]
       secondList = [[1,5],[8,12],[15,24],[25,26]]
Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]

Example 2:
Input: firstList = [[1,3],[5,9]]
       secondList = []
Output: []
```

---

## Part 3 – Quick Pattern Review

**(Visual: Pattern Reference Card.)**

**Instructor:**
"Let's recall our Interval Intersection pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL INTERSECTION PATTERN                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. TWO POINTERS: i for firstList, j for secondList         │
│                                                             │
│  2. FIND INTERSECTION:                                      │
│     start = max(A.start, B.start)                           │
│     end = min(A.end, B.end)                                 │
│     if start <= end → add [start, end]                      │
│                                                             │
│  3. ADVANCE POINTER:                                        │
│     Move the one whose interval ends first                  │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  TIME: O(m + n)    SPACE: O(1)                              │
└─────────────────────────────────────────────────────────────┘
```

**Why the formula works:**

"The intersection of two intervals `[a,b]` and `[c,d]` is:
- `start = max(a, c)` — can't start before either interval
- `end = min(b, d)` — can't extend past either interval
- If `start <= end`, we have a valid intersection!"

💡 **Tip:** This formula handles all overlap scenarios automatically. No need to check multiple cases!

---

## Part 4 – Step-by-Step Walkthrough

**(Visual: Interactive walkthrough.)**

**Instructor:**
"Let's trace through Example 1 step by step."

```
firstList  = [[0,2], [5,10], [13,23], [24,25]]
secondList = [[1,5], [8,12], [15,24], [25,26]]
```

### Iteration 1: i=0, j=0
```
A = [0, 2]
B = [1, 5]

Timeline:
[---A---]
   [------B------]
   |--∩--|

start = max(0, 1) = 1
end = min(2, 5) = 2
1 <= 2 ✓ → Add [1, 2]

Which ends first? A.end(2) < B.end(5) → i++

result = [[1, 2]]
```

### Iteration 2: i=1, j=0
```
A = [5, 10]
B = [1, 5]

Timeline:
         [----A----]
   [----B----]
             |

start = max(5, 1) = 5
end = min(10, 5) = 5
5 <= 5 ✓ → Add [5, 5]  (single point!)

Which ends first? A.end(10) > B.end(5) → j++

result = [[1, 2], [5, 5]]
```

### Iteration 3: i=1, j=1
```
A = [5, 10]
B = [8, 12]

Timeline:
   [----A----]
         [----B----]
         |--∩--|

start = max(5, 8) = 8
end = min(10, 12) = 10
8 <= 10 ✓ → Add [8, 10]

Which ends first? A.end(10) < B.end(12) → i++

result = [[1, 2], [5, 5], [8, 10]]
```

### Iteration 4: i=2, j=1
```
A = [13, 23]
B = [8, 12]

Timeline:
                    [----A----]
         [----B----]
   No overlap!

start = max(13, 8) = 13
end = min(23, 12) = 12
13 > 12 ✗ → No intersection

Which ends first? A.end(23) > B.end(12) → j++

result = [[1, 2], [5, 5], [8, 10]]  (unchanged)
```

### Iteration 5: i=2, j=2
```
A = [13, 23]
B = [15, 24]

start = max(13, 15) = 15
end = min(23, 24) = 23
15 <= 23 ✓ → Add [15, 23]

A.end(23) < B.end(24) → i++

result = [[1, 2], [5, 5], [8, 10], [15, 23]]
```

### Iteration 6: i=3, j=2
```
A = [24, 25]
B = [15, 24]

start = max(24, 15) = 24
end = min(25, 24) = 24
24 <= 24 ✓ → Add [24, 24]

A.end(25) > B.end(24) → j++

result = [[1, 2], [5, 5], [8, 10], [15, 23], [24, 24]]
```

### Iteration 7: i=3, j=3
```
A = [24, 25]
B = [25, 26]

start = max(24, 25) = 25
end = min(25, 26) = 25
25 <= 25 ✓ → Add [25, 25]

A.end(25) < B.end(26) → i++

result = [[1, 2], [5, 5], [8, 10], [15, 23], [24, 24], [25, 25]]
```

### Exit: i=4, out of bounds

**Final Result:** `[[1,2], [5,5], [8,10], [15,23], [24,24], [25,25]]` ✓

---

## Part 5 – Complete Solution

**(Visual: Full code.)**

**Instructor:**
"Here's the complete solution:"

```python
def intervalIntersection(firstList, secondList):
    result = []
    i, j = 0, 0
    
    while i < len(firstList) and j < len(secondList):
        # Get current intervals
        A = firstList[i]
        B = secondList[j]
        
        # Calculate intersection bounds
        start = max(A[0], B[0])
        end = min(A[1], B[1])
        
        # Check if valid intersection
        if start <= end:
            result.append([start, end])
        
        # Advance the pointer whose interval ends first
        if A[1] < B[1]:
            i += 1
        else:
            j += 1
    
    return result
```

---

## Part 6 – Code Explanation

**(Visual: Annotated code.)**

**Instructor:**
"Let's break down each part:"

```python
def intervalIntersection(firstList, secondList):
    result = []          # Store all intersection intervals
    i, j = 0, 0          # Two pointers, one for each list
    
    # Process until we exhaust either list
    while i < len(firstList) and j < len(secondList):
        A = firstList[i]
        B = secondList[j]
        
        # The magic formula:
        # - max gives us the later start (where overlap begins)
        # - min gives us the earlier end (where overlap ends)
        start = max(A[0], B[0])
        end = min(A[1], B[1])
        
        # Valid intersection only if start <= end
        # (handles all 6 possible interval positions!)
        if start <= end:
            result.append([start, end])
        
        # Key insight: the interval that ends first
        # can't possibly intersect with future intervals
        # from the other list
        if A[1] < B[1]:
            i += 1      # A ends first, move to next in firstList
        else:
            j += 1      # B ends first (or equal), move to next in secondList
    
    return result
```

---

## Part 7 – Edge Cases

**(Visual: Edge case examples.)**

**Instructor:**
"Let's consider the edge cases:"

### Case 1: Empty List
```python
firstList = [[1,3], [5,9]]
secondList = []
# Output: []
# The while loop never executes — no intersections possible!
```

### Case 2: No Overlaps
```python
firstList = [[1,2], [10,12]]
secondList = [[5,7], [15,20]]
# Output: []
# Intervals never overlap
```

### Case 3: Single Point Intersection
```python
firstList = [[1,5]]
secondList = [[5,10]]
# Output: [[5,5]]
# Touching at a single point IS a valid intersection!
```

### Case 4: One Contains the Other
```python
firstList = [[1,10]]
secondList = [[3,5]]
# Output: [[3,5]]
# The smaller interval IS the intersection
```

### Case 5: Identical Intervals
```python
firstList = [[1,5]]
secondList = [[1,5]]
# Output: [[1,5]]
# Intersection is the interval itself
```

---

## Part 8 – Complexity Analysis

**Instructor:**
"Let's verify the complexity:"

```
Time Complexity: O(m + n)
- m = len(firstList), n = len(secondList)
- Each pointer advances exactly once per iteration
- Maximum m + n iterations total
- Each iteration does O(1) work

Space Complexity: O(1) extra
- We only use constant extra space (two pointers)
- The result list is required for output, not counted as extra
```

---

## Part 9 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**
"Watch out for these mistakes:"

```
❌ Mistake 1: Checking overlap manually with multiple if statements
   Wrong:
   if A[0] >= B[0] and A[0] <= B[1]:  # A starts in B
   elif B[0] >= A[0] and B[0] <= A[1]:  # B starts in A
   ...

   Right:
   start = max(A[0], B[0])
   end = min(A[1], B[1])
   if start <= end:  # This handles ALL cases!

❌ Mistake 2: Moving the wrong pointer
   Wrong: Move based on which START is smaller
   Right: Move based on which END is smaller

❌ Mistake 3: Forgetting single-point intersections
   [1,5] ∩ [5,8] = [5,5] is valid! (start == end is OK)

❌ Mistake 4: Off-by-one with empty lists
   Always check bounds before accessing elements
```

---

## Part 10 – Summary

**Instructor:**
"That's LC 986! The key takeaways:

1. **Two pointers** traverse the sorted lists
2. **max(starts), min(ends)** finds the intersection
3. **Move the pointer** whose interval ends first
4. The formula handles ALL overlap scenarios automatically

This is a clean O(m + n) solution with O(1) extra space!"

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│              LC 986 SOLUTION TEMPLATE                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  i, j = 0, 0                                                │
│  while i < len(list1) and j < len(list2):                   │
│      start = max(list1[i][0], list2[j][0])                  │
│      end = min(list1[i][1], list2[j][1])                    │
│                                                             │
│      if start <= end:                                       │
│          result.append([start, end])                        │
│                                                             │
│      if list1[i][1] < list2[j][1]:                          │
│          i += 1                                             │
│      else:                                                  │
│          j += 1                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

