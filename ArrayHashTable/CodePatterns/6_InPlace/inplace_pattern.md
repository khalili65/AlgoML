# 📊 Pattern 6: In-place Modification

---

## When to Use

- "Modify array without extra space"
- Remove elements
- Move elements (zeros, duplicates)
- "Do it in O(1) space"

---

## The Template (Read/Write Pointers)

```python
def inPlacePattern(nums):
    write = 0  # Position to write next valid element
    
    for read in range(len(nums)):
        if CONDITION(nums[read]):  # Keep this element?
            nums[write] = nums[read]
            write += 1
    
    return write  # New length of valid portion
```

---

## Visual Understanding

```
Remove element 2 from [1, 2, 3, 2, 4]

Initial:
[1, 2, 3, 2, 4]
 w
 r

Step 1: nums[0]=1 ≠ 2, keep it
[1, 2, 3, 2, 4]
    w
    r

Step 2: nums[1]=2 = 2, skip it
[1, 2, 3, 2, 4]
    w
       r

Step 3: nums[2]=3 ≠ 2, write to position 1
[1, 3, 3, 2, 4]
       w
          r

Step 4: nums[3]=2 = 2, skip it
[1, 3, 3, 2, 4]
       w
             r

Step 5: nums[4]=4 ≠ 2, write to position 2
[1, 3, 4, 2, 4]
          w

Return 3 (first 3 elements are valid: [1, 3, 4])
```

---

## Example 1: Remove Duplicates from Sorted Array (LC 26)

**Problem:** Remove duplicates in-place, return new length.

```python
def removeDuplicates(nums):
    if not nums:
        return 0
    
    write = 1  # First element always stays
    
    for read in range(1, len(nums)):
        if nums[read] != nums[read - 1]:  # Different from previous
            nums[write] = nums[read]
            write += 1
    
    return write
```

**Visual:**
```
[1, 1, 2, 2, 3] → [1, 2, 3, _, _] return 3
```

---

## Example 2: Remove Element (LC 27)

**Problem:** Remove all occurrences of val in-place.

```python
def removeElement(nums, val):
    write = 0
    
    for read in range(len(nums)):
        if nums[read] != val:
            nums[write] = nums[read]
            write += 1
    
    return write
```

---

## Example 3: Move Zeroes (LC 283)

**Problem:** Move all zeros to end, maintaining order of non-zeros.

```python
def moveZeroes(nums):
    write = 0
    
    for read in range(len(nums)):
        if nums[read] != 0:
            nums[write], nums[read] = nums[read], nums[write]
            write += 1
```

**Why swap instead of just write?**
```
Swapping preserves zeros at the end automatically!

[0, 1, 0, 3, 12]

read=0: 0, skip
read=1: 1≠0, swap nums[0] and nums[1] → [1, 0, 0, 3, 12]
read=2: 0, skip
read=3: 3≠0, swap nums[1] and nums[3] → [1, 3, 0, 0, 12]
read=4: 12≠0, swap nums[2] and nums[4] → [1, 3, 12, 0, 0]
```

---

## Example 4: Remove Duplicates II - Allow 2 (LC 80)

**Problem:** Remove duplicates so each appears at most twice.

```python
def removeDuplicates(nums):
    if len(nums) <= 2:
        return len(nums)
    
    write = 2  # First two elements always stay
    
    for read in range(2, len(nums)):
        if nums[read] != nums[write - 2]:  # Compare with 2 back
            nums[write] = nums[read]
            write += 1
    
    return write
```

**Why compare with `write - 2`?**
```
[1, 1, 1, 2, 2, 3]

write=2, read=2: nums[2]=1, nums[0]=1, same → skip
write=2, read=3: nums[3]=2, nums[0]=1, diff → write
write=3, read=4: nums[4]=2, nums[1]=1, diff → write
write=4, read=5: nums[5]=3, nums[2]=1, diff → write

Result: [1, 1, 2, 2, 3, _]
```

---

## Example 5: Sort Colors (LC 75) - Dutch National Flag

**Problem:** Sort array with only 0, 1, 2 in-place.

```python
def sortColors(nums):
    low = 0      # Boundary for 0s
    mid = 0      # Current element
    high = len(nums) - 1  # Boundary for 2s
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
            # Don't increment mid! Need to check swapped element
```

**Visual:**
```
[2, 0, 2, 1, 1, 0]
 L        
 M                 
             H

Invariants:
- All elements before L are 0
- All elements after H are 2
- Elements between L and M are 1
```

---

## Example 6: Rotate Array (LC 189)

**Problem:** Rotate array right by k steps.

```python
def rotate(nums, k):
    n = len(nums)
    k = k % n  # Handle k > n
    
    def reverse(start, end):
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1
    
    # Reverse entire array
    reverse(0, n - 1)
    # Reverse first k elements
    reverse(0, k - 1)
    # Reverse remaining elements
    reverse(k, n - 1)
```

**Visual:**
```
[1, 2, 3, 4, 5, 6, 7], k = 3

Reverse all:    [7, 6, 5, 4, 3, 2, 1]
Reverse 0..2:   [5, 6, 7, 4, 3, 2, 1]
Reverse 3..6:   [5, 6, 7, 1, 2, 3, 4]
```

---

## In-Place Patterns

```
┌─────────────────────────────────────────────────────────────┐
│   IN-PLACE PATTERNS                                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Read/Write pointers:                                   │
│      write trails behind read                               │
│                                                             │
│   2. Swap-based:                                            │
│      Swap elements to their correct positions               │
│                                                             │
│   3. Three-way partition:                                   │
│      low, mid, high for 3 categories                        │
│                                                             │
│   4. Reverse-based:                                         │
│      Multiple reverses to achieve rotation/movement         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - single or constant passes
Space: O(1) - in-place modification
```

---

## Common Mistakes

```
❌ Mistake 1: Not handling empty array
   if not nums: return 0

❌ Mistake 2: Incrementing write when should skip
   # Only increment write when actually writing

❌ Mistake 3: Wrong comparison for duplicates
   # Compare with nums[read-1] for sorted array
   # Compare with nums[write-1] for result array
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   IN-PLACE MODIFICATION PATTERN                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   write = 0                                                 │
│                                                             │
│   for read in range(len(nums)):                             │
│       if CONDITION(nums[read]):                             │
│           nums[write] = nums[read]                          │
│           write += 1                                        │
│                                                             │
│   return write                                              │
│                                                             │
│   KEY: Write pointer only moves when we keep an element     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


