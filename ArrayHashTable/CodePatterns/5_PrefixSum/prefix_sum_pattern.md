# 📊 Pattern 5: Prefix Sum

---

## When to Use

- Range sum queries
- Subarray sum problems
- "Sum between index i and j"
- Product of array (prefix product)
- Running totals

---

## The Template

```python
def prefixSumPattern(nums):
    n = len(nums)
    prefix = [0] * (n + 1)  # prefix[i] = sum of nums[0..i-1]
    
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    
    # Sum from index i to j (inclusive):
    # sum(i, j) = prefix[j + 1] - prefix[i]
```

---

## Visual Understanding

```
nums   = [1, 2, 3, 4, 5]
index  =  0  1  2  3  4

prefix = [0, 1, 3, 6, 10, 15]
index  =  0  1  2  3   4   5

prefix[0] = 0           (sum of nothing)
prefix[1] = 1           (sum of nums[0])
prefix[2] = 1+2 = 3     (sum of nums[0..1])
prefix[3] = 1+2+3 = 6   (sum of nums[0..2])
...

Sum from index 1 to 3 (nums[1] + nums[2] + nums[3]):
= prefix[4] - prefix[1]
= 10 - 1
= 9
= 2 + 3 + 4 ✓
```

**Why `prefix[j+1] - prefix[i]` and not `prefix[j] - prefix[i-1]`?**
```
With n+1 size prefix array starting at 0:
- No bounds checking needed
- prefix[0] = 0 handles edge case naturally
- sum(0, j) = prefix[j+1] - prefix[0] = prefix[j+1]
```

---

## Example 1: Range Sum Query (LC 303)

**Problem:** Answer multiple range sum queries efficiently.

```python
class NumArray:
    def __init__(self, nums):
        self.prefix = [0]
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)
    
    def sumRange(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]
```

**Usage:**
```python
arr = NumArray([1, 2, 3, 4, 5])
arr.sumRange(1, 3)  # 2 + 3 + 4 = 9
arr.sumRange(0, 4)  # 1 + 2 + 3 + 4 + 5 = 15
```

---

## Example 2: Subarray Sum Equals K (LC 560)

**Problem:** Count subarrays with sum equal to k.

```python
def subarraySum(nums, k):
    count = 0
    prefix_sum = 0
    seen = {0: 1}  # sum -> count
    
    for num in nums:
        prefix_sum += num
        
        # If (prefix_sum - k) exists, those subarrays sum to k
        if prefix_sum - k in seen:
            count += seen[prefix_sum - k]
        
        seen[prefix_sum] = seen.get(prefix_sum, 0) + 1
    
    return count
```

**Why this works:**
```
If prefix[j] - prefix[i] = k
Then subarray from i to j-1 has sum k

So for each prefix_sum, we look for (prefix_sum - k)
```

---

## Example 3: Product of Array Except Self (LC 238)

**Problem:** Return array where result[i] = product of all elements except nums[i].

```python
def productExceptSelf(nums):
    n = len(nums)
    result = [1] * n
    
    # Left products: result[i] = product of nums[0..i-1]
    left = 1
    for i in range(n):
        result[i] = left
        left *= nums[i]
    
    # Right products: multiply by product of nums[i+1..n-1]
    right = 1
    for i in range(n - 1, -1, -1):
        result[i] *= right
        right *= nums[i]
    
    return result
```

**Visual:**
```
nums = [1, 2, 3, 4]

Left pass:
result = [1, 1, 2, 6]
         (nothing, 1, 1*2, 1*2*3)

Right pass:
result = [24, 12, 8, 6]
         (1*24, 1*12, 2*4, 6*1)
         (2*3*4, 1*3*4, 1*2*4, 1*2*3)
```

---

## Example 4: 2D Prefix Sum (LC 304)

**Problem:** Range sum query on 2D matrix.

```python
class NumMatrix:
    def __init__(self, matrix):
        if not matrix:
            return
        
        m, n = len(matrix), len(matrix[0])
        self.prefix = [[0] * (n + 1) for _ in range(m + 1)]
        
        for i in range(m):
            for j in range(n):
                self.prefix[i+1][j+1] = (
                    matrix[i][j]
                    + self.prefix[i][j+1]
                    + self.prefix[i+1][j]
                    - self.prefix[i][j]
                )
    
    def sumRegion(self, r1, c1, r2, c2):
        return (
            self.prefix[r2+1][c2+1]
            - self.prefix[r1][c2+1]
            - self.prefix[r2+1][c1]
            + self.prefix[r1][c1]
        )
```

**Visual (Inclusion-Exclusion):**
```
To get sum of rectangle (r1,c1) to (r2,c2):

+---+---+---+
| A | B |   |
+---+---+---+
| C | D |   |  D is our target rectangle
+---+---+---+

prefix[r2+1][c2+1] = A + B + C + D
prefix[r1][c2+1]   = A + B
prefix[r2+1][c1]   = A + C
prefix[r1][c1]     = A

D = (A+B+C+D) - (A+B) - (A+C) + A
```

---

## Example 5: Pivot Index (LC 724)

**Problem:** Find index where left sum equals right sum.

```python
def pivotIndex(nums):
    total = sum(nums)
    left_sum = 0
    
    for i, num in enumerate(nums):
        # right_sum = total - left_sum - nums[i]
        if left_sum == total - left_sum - num:
            return i
        left_sum += num
    
    return -1
```

---

## Example 6: Subarray Sum Divisible by K (LC 974)

**Problem:** Count subarrays with sum divisible by k.

```python
def subarraysDivByK(nums, k):
    count = 0
    prefix_sum = 0
    remainder_count = {0: 1}
    
    for num in nums:
        prefix_sum += num
        remainder = prefix_sum % k
        
        # Handle negative remainders
        if remainder < 0:
            remainder += k
        
        if remainder in remainder_count:
            count += remainder_count[remainder]
        
        remainder_count[remainder] = remainder_count.get(remainder, 0) + 1
    
    return count
```

**Why remainders work:**
```
If prefix[j] % k == prefix[i] % k
Then (prefix[j] - prefix[i]) % k == 0
So subarray from i to j-1 is divisible by k!
```

---

## Prefix Sum Variations

```
┌─────────────────────────────────────────────────────────────┐
│   PREFIX SUM VARIATIONS                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Basic range sum:                                       │
│      prefix[j+1] - prefix[i]                                │
│                                                             │
│   2. With hashmap (subarray sum = k):                       │
│      if prefix_sum - k in seen                              │
│                                                             │
│   3. Prefix product:                                        │
│      Same concept with multiplication                       │
│                                                             │
│   4. 2D prefix sum:                                         │
│      Inclusion-exclusion principle                          │
│                                                             │
│   5. Remainder-based:                                       │
│      Track prefix_sum % k                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Preprocessing: O(n) or O(m*n) for 2D
Query:         O(1)
Space:         O(n) or O(m*n) for 2D
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   PREFIX SUM PATTERN                                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   prefix = [0]                                              │
│   for num in nums:                                          │
│       prefix.append(prefix[-1] + num)                       │
│                                                             │
│   # Range sum from index i to j:                            │
│   sum(i, j) = prefix[j + 1] - prefix[i]                     │
│                                                             │
│   KEY INSIGHT:                                              │
│   • Precompute O(n), query O(1)                             │
│   • sum(i,j) = total_up_to_j - total_up_to_(i-1)            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


