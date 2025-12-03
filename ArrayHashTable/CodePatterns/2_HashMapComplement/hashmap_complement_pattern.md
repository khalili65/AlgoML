# 📊 Pattern 2: HashMap Complement (Target - Current)

---

## When to Use

- Find pairs that sum to target
- Find subarrays with target sum
- Two Sum variants
- "Have we seen the complement before?"

---

## The Template

```python
def complementPattern(nums, target):
    seen = {}  # value -> index (or count)
    
    for i, num in enumerate(nums):
        complement = target - num
        
        if complement in seen:
            return [seen[complement], i]  # Found!
        
        seen[num] = i  # Store AFTER checking
    
    return []
```

---

## Why Store AFTER Checking?

```python
# WRONG: Store first, then check
seen[num] = i
if complement in seen:  # Might find itself!

# RIGHT: Check first, then store
if complement in seen:
    # Won't find itself because we haven't stored it yet
seen[num] = i
```

**Example:**
```
nums = [3, 3], target = 6

WRONG (store first):
  i=0: seen = {3: 0}
       complement = 3, found at 0 → [0, 0] ❌ (same element!)

RIGHT (check first):
  i=0: complement = 3, not in seen
       seen = {3: 0}
  i=1: complement = 3, found at 0 → [0, 1] ✓
```

---

## Example 1: Two Sum (LC 1)

**Problem:** Find two numbers that add up to target.

```python
def twoSum(nums, target):
    seen = {}
    
    for i, num in enumerate(nums):
        complement = target - num
        
        if complement in seen:
            return [seen[complement], i]
        
        seen[num] = i
    
    return []
```

---

## Example 2: Subarray Sum Equals K (LC 560)

**Problem:** Count subarrays with sum equal to k.

```python
def subarraySum(nums, k):
    count = 0
    prefix_sum = 0
    seen = {0: 1}  # Important: empty prefix has sum 0
    
    for num in nums:
        prefix_sum += num
        complement = prefix_sum - k
        
        if complement in seen:
            count += seen[complement]
        
        seen[prefix_sum] = seen.get(prefix_sum, 0) + 1
    
    return count
```

**Why `{0: 1}`?**
```
If prefix_sum itself equals k, we need complement = 0.
The "empty prefix" (before any elements) has sum 0.

nums = [3], k = 3
prefix_sum = 3
complement = 3 - 3 = 0
Without {0: 1}, we'd miss this subarray!
```

**Visual:**
```
nums = [1, 2, 3], k = 3
seen = {0: 1}

i=0: prefix = 1, complement = -2, not found
     seen = {0: 1, 1: 1}

i=1: prefix = 3, complement = 0, found! count = 1
     seen = {0: 1, 1: 1, 3: 1}
     (subarray [1, 2] sums to 3)

i=2: prefix = 6, complement = 3, found! count = 2
     seen = {0: 1, 1: 1, 3: 1, 6: 1}
     (subarray [3] sums to 3)

Answer: 2
```

---

## Example 3: Contiguous Array (LC 525)

**Problem:** Find longest subarray with equal 0s and 1s.

```python
def findMaxLength(nums):
    # Treat 0 as -1, find longest subarray with sum 0
    seen = {0: -1}  # sum -> first index
    prefix_sum = 0
    max_len = 0
    
    for i, num in enumerate(nums):
        prefix_sum += 1 if num == 1 else -1
        
        if prefix_sum in seen:
            # Same prefix_sum means subarray between has sum 0
            max_len = max(max_len, i - seen[prefix_sum])
        else:
            seen[prefix_sum] = i  # Store FIRST occurrence only
    
    return max_len
```

**Why store FIRST occurrence?**
```
We want the LONGEST subarray.
If we see the same prefix_sum again, the subarray between has sum 0.
Storing the first occurrence maximizes the distance.

nums = [0, 1, 0, 1]
prefix (with 0→-1): -1, 0, -1, 0

seen[0] = -1 (before array starts)
i=1: prefix = 0, already in seen at -1
     length = 1 - (-1) = 2

i=3: prefix = 0, already in seen at -1
     length = 3 - (-1) = 4

If we updated seen[0] = 1, we'd get length 3-1 = 2 instead of 4!
```

---

## Example 4: Two Sum - Pairs Count (Variation)

**Problem:** Count pairs that sum to target (not indices).

```python
def countPairs(nums, target):
    count = 0
    seen = {}  # value -> count
    
    for num in nums:
        complement = target - num
        
        if complement in seen:
            count += seen[complement]
        
        seen[num] = seen.get(num, 0) + 1
    
    return count
```

---

## Example 5: 4Sum Count (LC 454)

**Problem:** Count tuples (i,j,k,l) where A[i]+B[j]+C[k]+D[l]=0.

```python
def fourSumCount(A, B, C, D):
    # Store all A+B sums
    ab_sums = {}
    for a in A:
        for b in B:
            s = a + b
            ab_sums[s] = ab_sums.get(s, 0) + 1
    
    # For each C+D, find complement in ab_sums
    count = 0
    for c in C:
        for d in D:
            complement = -(c + d)
            if complement in ab_sums:
                count += ab_sums[complement]
    
    return count
```

---

## The Complement Pattern Variations

```
┌─────────────────────────────────────────────────────────────┐
│   COMPLEMENT VARIATIONS                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Two Sum (find indices):                                │
│      seen[num] = index                                      │
│                                                             │
│   2. Count pairs:                                           │
│      seen[num] = count                                      │
│                                                             │
│   3. Subarray sum (with prefix):                            │
│      complement = prefix_sum - target                       │
│      seen = {0: 1} initially                                │
│                                                             │
│   4. Longest subarray:                                      │
│      seen[prefix_sum] = first_index                         │
│      (store first occurrence only!)                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - single pass
Space: O(n) - for the hashmap
```

---

## Common Mistakes

```
❌ Mistake 1: Storing before checking
   seen[num] = i  # WRONG order!
   if complement in seen: ...

❌ Mistake 2: Forgetting {0: 1} for subarray sum
   seen = {}  # Missing empty prefix!

❌ Mistake 3: Updating instead of keeping first occurrence
   # For "longest subarray" problems:
   if prefix_sum in seen:
       seen[prefix_sum] = i  # WRONG! Keep first occurrence
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   HASHMAP COMPLEMENT PATTERN                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   seen = {}                                                 │
│                                                             │
│   for i, num in enumerate(nums):                            │
│       complement = target - num                             │
│                                                             │
│       if complement in seen:                                │
│           # Found! Process it                               │
│                                                             │
│       seen[num] = i  # Store AFTER checking                 │
│                                                             │
│   KEY INSIGHT:                                              │
│   "Have I seen what I need to complete the target?"         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


