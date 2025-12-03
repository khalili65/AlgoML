# 👆👆 Pattern 3: N-Sum Pattern

---

## When to Use

- Find pairs/triplets/quadruplets with target sum
- 3Sum, 4Sum, kSum variations
- Return all unique combinations

---

## The Core Idea

```
N-Sum = Fix (N-2) elements + Two Sum on remaining

3Sum: Fix 1, Two Sum on remaining 2
4Sum: Fix 2, Two Sum on remaining 2
kSum: Fix k-2, Two Sum on remaining 2

IMPORTANT: Sort first to enable two pointers and skip duplicates!
```

---

## The Template

```python
def threeSum(nums, target=0):
    nums.sort()  # MUST sort first!
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates for first element
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        # Two pointer for remaining elements
        left, right = i + 1, len(nums) - 1
        remaining_target = target - nums[i]
        
        while left < right:
            s = nums[left] + nums[right]
            
            if s == remaining_target:
                result.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                # Skip duplicates
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
                while left < right and nums[right] == nums[right + 1]:
                    right -= 1
            elif s < remaining_target:
                left += 1
            else:
                right -= 1
    
    return result
```

---

## Visual Understanding

```
Find triplets summing to 0 in [-1, 0, 1, 2, -1, -4]

Step 1: Sort
[-4, -1, -1, 0, 1, 2]

Step 2: Fix each element, two-pointer for rest

i=0: Fix -4, find two sum = 4 in [-1, -1, 0, 1, 2]
     L=-1, R=2: sum = 1 < 4, L++
     L=-1, R=2: sum = 1 < 4, L++
     L=0, R=2: sum = 2 < 4, L++
     L=1, R=2: sum = 3 < 4, L++
     L > R, no solution

i=1: Fix -1, find two sum = 1 in [-1, 0, 1, 2]
     L=-1, R=2: sum = 1 = 1 ✓ → [-1, -1, 2]
     Skip duplicates...
     L=0, R=1: sum = 1 = 1 ✓ → [-1, 0, 1]

i=2: nums[2]=-1 == nums[1], SKIP (duplicate)

i=3: Fix 0, find two sum = 0 in [1, 2]
     L=1, R=2: sum = 3 > 0, no solution

Result: [[-1, -1, 2], [-1, 0, 1]]
```

---

## Example 1: 3Sum (LC 15)

**Problem:** Find all unique triplets that sum to zero.

```python
def threeSum(nums):
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
                while left < right and nums[right] == nums[right + 1]:
                    right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1
    
    return result
```

---

## Example 2: 3Sum Closest (LC 16)

**Problem:** Find triplet with sum closest to target.

```python
def threeSumClosest(nums, target):
    nums.sort()
    closest = float('inf')
    
    for i in range(len(nums) - 2):
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            
            if abs(s - target) < abs(closest - target):
                closest = s
            
            if s < target:
                left += 1
            elif s > target:
                right -= 1
            else:
                return s  # Exact match
    
    return closest
```

---

## Example 3: 4Sum (LC 18)

**Problem:** Find all unique quadruplets that sum to target.

```python
def fourSum(nums, target):
    nums.sort()
    result = []
    n = len(nums)
    
    for i in range(n - 3):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        for j in range(i + 1, n - 2):
            if j > i + 1 and nums[j] == nums[j - 1]:
                continue
            
            left, right = j + 1, n - 1
            remaining = target - nums[i] - nums[j]
            
            while left < right:
                s = nums[left] + nums[right]
                
                if s == remaining:
                    result.append([nums[i], nums[j], nums[left], nums[right]])
                    left += 1
                    right -= 1
                    while left < right and nums[left] == nums[left - 1]:
                        left += 1
                    while left < right and nums[right] == nums[right + 1]:
                        right -= 1
                elif s < remaining:
                    left += 1
                else:
                    right -= 1
    
    return result
```

---

## Example 4: Generic kSum

**Problem:** Generalized solution for any k.

```python
def kSum(nums, target, k):
    nums.sort()
    result = []
    
    def twoSum(start, target):
        res = []
        left, right = start, len(nums) - 1
        
        while left < right:
            s = nums[left] + nums[right]
            if s == target:
                res.append([nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left - 1]:
                    left += 1
            elif s < target:
                left += 1
            else:
                right -= 1
        
        return res
    
    def kSumHelper(start, k, target):
        if k == 2:
            return twoSum(start, target)
        
        res = []
        for i in range(start, len(nums) - k + 1):
            if i > start and nums[i] == nums[i - 1]:
                continue
            
            for subset in kSumHelper(i + 1, k - 1, target - nums[i]):
                res.append([nums[i]] + subset)
        
        return res
    
    return kSumHelper(0, k, target)
```

---

## Example 5: 3Sum Smaller (LC 259)

**Problem:** Count triplets with sum less than target.

```python
def threeSumSmaller(nums, target):
    nums.sort()
    count = 0
    
    for i in range(len(nums) - 2):
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            
            if s < target:
                # All pairs between left and right work!
                count += right - left
                left += 1
            else:
                right -= 1
    
    return count
```

---

## Skipping Duplicates

```
┌─────────────────────────────────────────────────────────────┐
│   DUPLICATE SKIPPING RULES                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   For outer loop (fixed element):                           │
│   if i > 0 and nums[i] == nums[i-1]: continue               │
│   (Skip if same as PREVIOUS to avoid duplicate triplets)    │
│                                                             │
│   For inner pointers (after finding a match):               │
│   while left < right and nums[left] == nums[left-1]:        │
│       left += 1                                             │
│   while left < right and nums[right] == nums[right+1]:      │
│       right -= 1                                            │
│   (Skip duplicate values we've already used)                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
3Sum: O(n²) time, O(1) space (ignoring sort/output)
4Sum: O(n³) time, O(1) space
kSum: O(n^(k-1)) time
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to sort
   nums.sort()  # MUST do this first!

❌ Mistake 2: Wrong duplicate skip condition
   if nums[i] == nums[i-1]:  # WRONG - fails for i=0
   if i > 0 and nums[i] == nums[i-1]:  # RIGHT

❌ Mistake 3: Skipping duplicates before finding match
   # Skip duplicates AFTER finding a valid answer
   # Not before, or you might skip valid combinations
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   N-SUM PATTERN                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   nums.sort()  # ALWAYS sort first!                         │
│                                                             │
│   for i in range(len(nums) - 2):                            │
│       if i > 0 and nums[i] == nums[i-1]: continue           │
│                                                             │
│       left, right = i + 1, len(nums) - 1                    │
│       target_remaining = target - nums[i]                   │
│                                                             │
│       while left < right:                                   │
│           # Two pointer logic                               │
│                                                             │
│   KEY: Fix (N-2) elements + Two Sum for last 2              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


