# 📊 Pattern 7: Cyclic Sort

---

## When to Use

- Array contains numbers in range [1, n] or [0, n-1]
- Find missing number(s)
- Find duplicate number(s)
- "Each number should be at its correct index"

---

## The Core Idea

```
If array has numbers 1 to n, each number i should be at index i-1.

nums = [3, 1, 5, 4, 2]

Number 1 should be at index 0
Number 2 should be at index 1
Number 3 should be at index 2
...

Goal: Place each number at its "correct" position by swapping.
```

---

## The Template

```python
def cyclicSort(nums):
    i = 0
    n = len(nums)
    
    while i < n:
        correct_idx = nums[i] - 1  # Where this number should be
        
        if nums[i] != nums[correct_idx]:  # Not in correct position
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
            # Don't increment i! Check the swapped element
        else:
            i += 1  # Already correct or duplicate, move on
    
    # Now each nums[i] should equal i+1 (unless missing/duplicate)
```

---

## Visual Understanding

```
nums = [3, 1, 5, 4, 2]  (should be [1, 2, 3, 4, 5])

i=0: nums[0]=3, correct_idx=2, swap with nums[2]
     [5, 1, 3, 4, 2]

i=0: nums[0]=5, correct_idx=4, swap with nums[4]
     [2, 1, 3, 4, 5]

i=0: nums[0]=2, correct_idx=1, swap with nums[1]
     [1, 2, 3, 4, 5]

i=0: nums[0]=1, correct_idx=0, already correct! i++
i=1: nums[1]=2, correct_idx=1, already correct! i++
i=2: nums[2]=3, correct_idx=2, already correct! i++
...

Final: [1, 2, 3, 4, 5] ✓
```

---

## Example 1: Find Missing Number (LC 268)

**Problem:** Array of n numbers in range [0, n], one missing.

```python
def missingNumber(nums):
    n = len(nums)
    i = 0
    
    while i < n:
        # Numbers are 0 to n, so correct index = nums[i] itself
        if nums[i] < n and nums[i] != nums[nums[i]]:
            nums[nums[i]], nums[i] = nums[i], nums[nums[i]]
        else:
            i += 1
    
    # Find which index has wrong number
    for i in range(n):
        if nums[i] != i:
            return i
    
    return n  # All 0 to n-1 present, so n is missing

# Math approach (simpler):
def missingNumber(nums):
    n = len(nums)
    return n * (n + 1) // 2 - sum(nums)
```

---

## Example 2: First Missing Positive (LC 41)

**Problem:** Find smallest missing positive integer.

```python
def firstMissingPositive(nums):
    n = len(nums)
    i = 0
    
    while i < n:
        correct_idx = nums[i] - 1
        
        # Only process if:
        # 1. Number is positive
        # 2. Number is in range [1, n]
        # 3. Not already in correct position
        if 0 < nums[i] <= n and nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    
    # Find first position where nums[i] != i+1
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    
    return n + 1
```

**Visual:**
```
nums = [3, 4, -1, 1]

i=0: 3 in [1,4], swap with nums[2] → [-1, 4, 3, 1]
i=0: -1 not in [1,4], skip, i++
i=1: 4 in [1,4], swap with nums[3] → [-1, 1, 3, 4]
i=1: 1 in [1,4], swap with nums[0] → [1, -1, 3, 4]
i=1: -1 not in [1,4], skip, i++
...

Final: [1, -1, 3, 4]
       index 1 has -1 instead of 2 → return 2
```

---

## Example 3: Find All Missing Numbers (LC 448)

**Problem:** Find all missing numbers in range [1, n].

```python
def findDisappearedNumbers(nums):
    n = len(nums)
    i = 0
    
    while i < n:
        correct_idx = nums[i] - 1
        
        if nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    
    missing = []
    for i in range(n):
        if nums[i] != i + 1:
            missing.append(i + 1)
    
    return missing
```

---

## Example 4: Find Duplicate Number (LC 287)

**Problem:** Array of n+1 numbers in range [1, n], one duplicate.

```python
def findDuplicate(nums):
    i = 0
    
    while i < len(nums):
        correct_idx = nums[i] - 1
        
        if nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            if i != correct_idx:
                return nums[i]  # Found duplicate!
            i += 1
    
    return -1  # Should never reach here

# Floyd's cycle detection (O(1) space, doesn't modify array):
def findDuplicate(nums):
    slow = fast = nums[0]
    
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break
    
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]
    
    return slow
```

---

## Example 5: Find All Duplicates (LC 442)

**Problem:** Find all duplicates (appear twice).

```python
def findDuplicates(nums):
    result = []
    
    # Marking approach (in-place, clever!)
    for num in nums:
        idx = abs(num) - 1
        
        if nums[idx] < 0:
            result.append(abs(num))  # Already visited = duplicate
        else:
            nums[idx] = -nums[idx]  # Mark as visited
    
    return result
```

**Why this works:**
```
Use the sign as a "visited" marker!

nums = [4, 3, 2, 7, 8, 2, 3, 1]

num=4: idx=3, nums[3]=7>0, mark → [4, 3, 2, -7, 8, 2, 3, 1]
num=3: idx=2, nums[2]=2>0, mark → [4, 3, -2, -7, 8, 2, 3, 1]
num=-2: idx=1, nums[1]=3>0, mark → [4, -3, -2, -7, 8, 2, 3, 1]
num=-7: idx=6, nums[6]=3>0, mark → [4, -3, -2, -7, 8, 2, -3, 1]
num=8: idx=7, nums[7]=1>0, mark → [4, -3, -2, -7, 8, 2, -3, -1]
num=2: idx=1, nums[1]=-3<0, DUPLICATE! → append 2
num=-3: idx=2, nums[2]=-2<0, DUPLICATE! → append 3
num=-1: idx=0, nums[0]=4>0, mark → [-4, -3, -2, -7, 8, 2, -3, -1]

Result: [2, 3]
```

---

## Example 6: Set Mismatch (LC 645)

**Problem:** One number is missing, one is duplicated.

```python
def findErrorNums(nums):
    n = len(nums)
    i = 0
    
    while i < n:
        correct_idx = nums[i] - 1
        
        if nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    
    for i in range(n):
        if nums[i] != i + 1:
            return [nums[i], i + 1]  # [duplicate, missing]
    
    return []
```

---

## Cyclic Sort Variations

```
┌─────────────────────────────────────────────────────────────┐
│   CYCLIC SORT VARIATIONS                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Range [1, n]:                                          │
│      correct_idx = nums[i] - 1                              │
│                                                             │
│   2. Range [0, n-1]:                                        │
│      correct_idx = nums[i]                                  │
│                                                             │
│   3. With out-of-range values:                              │
│      if 0 < nums[i] <= n and ...                            │
│                                                             │
│   4. Using sign as marker:                                  │
│      nums[idx] = -nums[idx]  (mark visited)                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - each element is swapped at most once
Space: O(1) - in-place modification
```

---

## Common Mistakes

```
❌ Mistake 1: Incrementing i after swap
   # DON'T increment i after swap!
   # Need to check the new element at position i

❌ Mistake 2: Forgetting to handle out-of-range
   if 0 < nums[i] <= n:  # Must check range!

❌ Mistake 3: Infinite loop with duplicates
   if nums[i] != nums[correct_idx]:  # Not just i != correct_idx
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   CYCLIC SORT PATTERN                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   i = 0                                                     │
│   while i < n:                                              │
│       correct_idx = nums[i] - 1                             │
│                                                             │
│       if nums[i] != nums[correct_idx]:                      │
│           swap(nums[i], nums[correct_idx])                  │
│       else:                                                 │
│           i += 1                                            │
│                                                             │
│   # After: nums[i] = i+1 unless missing/duplicate           │
│                                                             │
│   USE FOR:                                                  │
│   • Numbers in range [1, n]                                 │
│   • Find missing numbers                                    │
│   • Find duplicate numbers                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


