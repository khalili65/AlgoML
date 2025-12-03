# 👆👆 Pattern 4: Partition (Three-way)

---

## When to Use

- Sort array with limited values (0, 1, 2)
- Partition around pivot (quicksort)
- Dutch National Flag problem
- Separate array into categories

---

## The Core Idea

```
Three pointers divide array into regions:

[0, 0, 0, 1, 1, 1, ?, ?, ?, 2, 2, 2]
         ↑        ↑        ↑
        low      mid      high

Region 0..low-1:   All 0s (done)
Region low..mid-1: All 1s (done)
Region mid..high:  Unknown (to process)
Region high+1..n:  All 2s (done)
```

---

## The Template

```python
def threeWayPartition(nums, pivot=1):
    low = 0      # Boundary for < pivot
    mid = 0      # Current element to process
    high = len(nums) - 1  # Boundary for > pivot
    
    while mid <= high:
        if nums[mid] < pivot:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] > pivot:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
            # DON'T increment mid! Need to check swapped element
        else:
            mid += 1
```

---

## Visual Understanding

```
Sort [2, 0, 2, 1, 1, 0] (values 0, 1, 2)

L
M           H
[2, 0, 2, 1, 1, 0]    mid=2 > 1, swap with high, high--

L
M        H
[0, 0, 2, 1, 1, 2]    mid=0 < 1, swap with low, low++, mid++

   L
   M     H
[0, 0, 2, 1, 1, 2]    mid=0 < 1, swap with low, low++, mid++

      L
      M  H
[0, 0, 2, 1, 1, 2]    mid=2 > 1, swap with high, high--

      L
      M
         H
[0, 0, 1, 1, 2, 2]    mid=1 = 1, mid++

      L
         M
         H
[0, 0, 1, 1, 2, 2]    mid=1 = 1, mid++

      L
            M
         H
[0, 0, 1, 1, 2, 2]    mid > high, DONE!
```

---

## Example 1: Sort Colors (LC 75)

**Problem:** Sort array containing only 0, 1, 2.

```python
def sortColors(nums):
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 2:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
        else:  # nums[mid] == 1
            mid += 1
```

---

## Example 2: Move Zeros to End (Variation)

**Problem:** Move all zeros to end (two-way partition).

```python
def moveZeroes(nums):
    low = 0  # Boundary for non-zeros
    
    for mid in range(len(nums)):
        if nums[mid] != 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
```

---

## Example 3: Partition Array by Odd/Even

**Problem:** Move all odd numbers before even numbers.

```python
def partitionOddEven(nums):
    low = 0
    high = len(nums) - 1
    
    while low < high:
        while low < high and nums[low] % 2 == 1:  # Odd
            low += 1
        while low < high and nums[high] % 2 == 0:  # Even
            high -= 1
        
        if low < high:
            nums[low], nums[high] = nums[high], nums[low]
            low += 1
            high -= 1
```

---

## Example 4: Quick Select (Kth Largest)

**Problem:** Find kth largest element using partition.

```python
def findKthLargest(nums, k):
    k = len(nums) - k  # Convert to kth smallest
    
    def partition(left, right):
        pivot = nums[right]
        store = left
        
        for i in range(left, right):
            if nums[i] < pivot:
                nums[store], nums[i] = nums[i], nums[store]
                store += 1
        
        nums[store], nums[right] = nums[right], nums[store]
        return store
    
    left, right = 0, len(nums) - 1
    
    while True:
        pivot_idx = partition(left, right)
        
        if pivot_idx == k:
            return nums[k]
        elif pivot_idx < k:
            left = pivot_idx + 1
        else:
            right = pivot_idx - 1
```

---

## Example 5: Wiggle Sort

**Problem:** Rearrange to nums[0] < nums[1] > nums[2] < nums[3]...

```python
def wiggleSort(nums):
    nums.sort()
    half = (len(nums) + 1) // 2
    
    # Interleave smaller and larger halves
    nums[::2], nums[1::2] = nums[:half][::-1], nums[half:][::-1]
```

---

## Why NOT Increment Mid After Swap with High?

```
When we swap nums[mid] with nums[high]:
- nums[high] was unknown (could be 0, 1, or 2)
- After swap, nums[mid] is now that unknown value
- We MUST check it before moving forward!

Example:
[2, 0, 1]
 M     H

Swap mid and high:
[1, 0, 2]
 M  H

If we incremented mid, we'd skip the 1!
So we decrement high but keep mid the same.
```

---

## Complexity

```
Time:  O(n) - single pass
Space: O(1) - in-place
```

---

## Common Mistakes

```
❌ Mistake 1: Incrementing mid after swap with high
   nums[mid], nums[high] = nums[high], nums[mid]
   high -= 1
   mid += 1  # WRONG! Don't do this!

❌ Mistake 2: Wrong loop condition
   while mid < high:   # WRONG
   while mid <= high:  # RIGHT (need to check when mid == high)

❌ Mistake 3: Forgetting to increment mid after swap with low
   # When swapping with low, the element came from the left
   # It's already processed, so increment both low and mid
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   PARTITION PATTERN (THREE-WAY)                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   low, mid, high = 0, 0, len(nums) - 1                      │
│                                                             │
│   while mid <= high:                                        │
│       if nums[mid] < pivot:                                 │
│           swap(low, mid)                                    │
│           low++, mid++                                      │
│       elif nums[mid] > pivot:                               │
│           swap(mid, high)                                   │
│           high--                                            │
│           # DON'T increment mid!                            │
│       else:                                                 │
│           mid++                                             │
│                                                             │
│   KEY: Three regions - less, equal, greater                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


