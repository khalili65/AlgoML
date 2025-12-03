# 👆👆 Pattern 5: Merge Arrays

---

## When to Use

- Merge two sorted arrays
- Process two arrays in parallel
- Interval intersection
- Compare elements from two sources

---

## The Template

```python
def mergeArrays(arr1, arr2):
    i, j = 0, 0
    result = []
    
    while i < len(arr1) and j < len(arr2):
        if arr1[i] < arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    
    # Add remaining elements
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    
    return result
```

---

## Visual Understanding

```
Merge [1, 3, 5] and [2, 4, 6]

i=0  j=0
[1, 3, 5]  [2, 4, 6]   1 < 2 → take 1, i++
 ↑          ↑

i=1  j=0
[1, 3, 5]  [2, 4, 6]   3 > 2 → take 2, j++
    ↑       ↑

i=1  j=1
[1, 3, 5]  [2, 4, 6]   3 < 4 → take 3, i++
    ↑          ↑

i=2  j=1
[1, 3, 5]  [2, 4, 6]   5 > 4 → take 4, j++
       ↑       ↑

i=2  j=2
[1, 3, 5]  [2, 4, 6]   5 < 6 → take 5, i++
       ↑          ↑

i=3  j=2
[1, 3, 5]  [2, 4, 6]   i exhausted, take remaining [6]
          ↑       ↑

Result: [1, 2, 3, 4, 5, 6]
```

---

## Example 1: Merge Sorted Array In-Place (LC 88)

**Problem:** Merge nums2 into nums1 (nums1 has extra space).

```python
def merge(nums1, m, nums2, n):
    # Start from the END to avoid overwriting
    p1 = m - 1      # Last element in nums1
    p2 = n - 1      # Last element in nums2
    p = m + n - 1   # Last position in merged array
    
    while p2 >= 0:
        if p1 >= 0 and nums1[p1] > nums2[p2]:
            nums1[p] = nums1[p1]
            p1 -= 1
        else:
            nums1[p] = nums2[p2]
            p2 -= 1
        p -= 1
```

**Why from the end?**
```
nums1 = [1, 2, 3, 0, 0, 0], m = 3
nums2 = [2, 5, 6], n = 3

If we merge from the start:
- We'd overwrite nums1[3] before using it!

From the end:
- nums1[5] is empty, safe to write
- Work backwards, never overwriting unprocessed elements
```

---

## Example 2: Squares of Sorted Array (LC 977)

**Problem:** Square each element, return in sorted order.

```python
def sortedSquares(nums):
    n = len(nums)
    result = [0] * n
    left, right = 0, n - 1
    pos = n - 1  # Fill from end (largest first)
    
    while left <= right:
        left_sq = nums[left] ** 2
        right_sq = nums[right] ** 2
        
        if left_sq > right_sq:
            result[pos] = left_sq
            left += 1
        else:
            result[pos] = right_sq
            right -= 1
        pos -= 1
    
    return result
```

**Why two pointers from ends?**
```
nums = [-4, -1, 0, 3, 10]

After squaring: [16, 1, 0, 9, 100]

Largest squares are at the ENDS (most negative or most positive).
Use two pointers from ends, fill result from largest to smallest.
```

---

## Example 3: Interval List Intersections (LC 986)

**Problem:** Find intersections of two interval lists.

```python
def intervalIntersection(A, B):
    i, j = 0, 0
    result = []
    
    while i < len(A) and j < len(B):
        # Find intersection
        start = max(A[i][0], B[j][0])
        end = min(A[i][1], B[j][1])
        
        if start <= end:  # Valid intersection
            result.append([start, end])
        
        # Move pointer with smaller end time
        if A[i][1] < B[j][1]:
            i += 1
        else:
            j += 1
    
    return result
```

**Visual:**
```
A: [[0,2], [5,10], [13,23]]
B: [[1,5], [8,12], [15,24]]

i=0, j=0: [0,2] ∩ [1,5] = [max(0,1), min(2,5)] = [1,2] ✓
          A[0] ends first (2 < 5), i++

i=1, j=0: [5,10] ∩ [1,5] = [max(5,1), min(10,5)] = [5,5] ✓
          B[0] ends first (5 < 10), j++

i=1, j=1: [5,10] ∩ [8,12] = [max(5,8), min(10,12)] = [8,10] ✓
          A[1] ends first (10 < 12), i++

...
```

---

## Example 4: Compare Version Numbers (LC 165)

**Problem:** Compare two version strings.

```python
def compareVersion(version1, version2):
    v1 = list(map(int, version1.split('.')))
    v2 = list(map(int, version2.split('.')))
    
    i, j = 0, 0
    
    while i < len(v1) or j < len(v2):
        num1 = v1[i] if i < len(v1) else 0
        num2 = v2[j] if j < len(v2) else 0
        
        if num1 < num2:
            return -1
        elif num1 > num2:
            return 1
        
        i += 1
        j += 1
    
    return 0
```

---

## Example 5: Intersection of Two Arrays II (LC 350)

**Problem:** Return intersection (include duplicates).

```python
def intersect(nums1, nums2):
    nums1.sort()
    nums2.sort()
    
    i, j = 0, 0
    result = []
    
    while i < len(nums1) and j < len(nums2):
        if nums1[i] == nums2[j]:
            result.append(nums1[i])
            i += 1
            j += 1
        elif nums1[i] < nums2[j]:
            i += 1
        else:
            j += 1
    
    return result
```

---

## Merge Variations

```
┌─────────────────────────────────────────────────────────────┐
│   MERGE VARIATIONS                                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Standard merge:                                        │
│      Compare, take smaller, advance that pointer            │
│                                                             │
│   2. In-place merge (from end):                             │
│      Start from end to avoid overwriting                    │
│                                                             │
│   3. Interval intersection:                                 │
│      Advance pointer with smaller END time                  │
│                                                             │
│   4. Same elements (intersection):                          │
│      Only take when equal, advance both                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n + m) - each element processed once
Space: O(1) or O(n + m) depending on in-place or not
```

---

## Common Mistakes

```
❌ Mistake 1: In-place merge from start
   # This overwrites elements before using them!
   # Always merge from END for in-place

❌ Mistake 2: Forgetting remaining elements
   result.extend(arr1[i:])
   result.extend(arr2[j:])
   # One array might have leftover elements!

❌ Mistake 3: Wrong pointer advancement for intervals
   # Advance the one that ENDS first
   # Not the one that starts first!
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   MERGE ARRAYS PATTERN                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   i, j = 0, 0                                               │
│                                                             │
│   while i < len(arr1) and j < len(arr2):                    │
│       if COMPARE(arr1[i], arr2[j]):                         │
│           process(arr1[i])                                  │
│           i += 1                                            │
│       else:                                                 │
│           process(arr2[j])                                  │
│           j += 1                                            │
│                                                             │
│   # Handle remaining                                        │
│   process(arr1[i:])                                         │
│   process(arr2[j:])                                         │
│                                                             │
│   KEY: In-place merge → start from END                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


