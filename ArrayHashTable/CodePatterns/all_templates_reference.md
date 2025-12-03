# 📊 Array & HashTable - All Templates Reference

Quick reference for all 7 code patterns with examples.

---

## Pattern 1: HashMap Counting

**Template:**
```python
from collections import Counter

count = {}
for item in items:
    count[item] = count.get(item, 0) + 1

# Or simply:
count = Counter(items)
```

**Example - Valid Anagram (LC 242):**
```python
def isAnagram(s, t):
    return Counter(s) == Counter(t)
```

---

## Pattern 2: HashMap Complement

**Template:**
```python
seen = {}
for i, num in enumerate(nums):
    complement = target - num
    if complement in seen:
        return [seen[complement], i]
    seen[num] = i
```

**Example - Two Sum (LC 1):**
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        if target - num in seen:
            return [seen[target - num], i]
        seen[num] = i
    return []
```

---

## Pattern 3: HashMap Index

**Template:**
```python
last_seen = {}
for i, num in enumerate(nums):
    if num in last_seen:
        distance = i - last_seen[num]
        # process distance
    last_seen[num] = i
```

**Example - Contains Duplicate II (LC 219):**
```python
def containsNearbyDuplicate(nums, k):
    last_seen = {}
    for i, num in enumerate(nums):
        if num in last_seen and i - last_seen[num] <= k:
            return True
        last_seen[num] = i
    return False
```

---

## Pattern 4: HashSet Existence

**Template:**
```python
seen = set()
for num in nums:
    if num in seen:
        # found duplicate or match
        pass
    seen.add(num)
```

**Example - Longest Consecutive Sequence (LC 128):**
```python
def longestConsecutive(nums):
    num_set = set(nums)
    max_len = 0
    
    for num in num_set:
        if num - 1 not in num_set:  # Start of sequence
            length = 1
            while num + length in num_set:
                length += 1
            max_len = max(max_len, length)
    
    return max_len
```

---

## Pattern 5: Prefix Sum

**Template:**
```python
prefix = [0]
for num in nums:
    prefix.append(prefix[-1] + num)

# Sum from i to j (inclusive):
sum_i_to_j = prefix[j + 1] - prefix[i]
```

**Example - Range Sum Query (LC 303):**
```python
class NumArray:
    def __init__(self, nums):
        self.prefix = [0]
        for num in nums:
            self.prefix.append(self.prefix[-1] + num)
    
    def sumRange(self, left, right):
        return self.prefix[right + 1] - self.prefix[left]
```

---

## Pattern 6: In-place Modification

**Template:**
```python
write = 0
for read in range(len(nums)):
    if CONDITION(nums[read]):
        nums[write] = nums[read]
        write += 1
return write
```

**Example - Remove Duplicates (LC 26):**
```python
def removeDuplicates(nums):
    if not nums:
        return 0
    write = 1
    for read in range(1, len(nums)):
        if nums[read] != nums[read - 1]:
            nums[write] = nums[read]
            write += 1
    return write
```

---

## Pattern 7: Cyclic Sort

**Template:**
```python
i = 0
while i < len(nums):
    correct_idx = nums[i] - 1
    if nums[i] != nums[correct_idx]:
        nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
    else:
        i += 1
```

**Example - First Missing Positive (LC 41):**
```python
def firstMissingPositive(nums):
    n = len(nums)
    i = 0
    
    while i < n:
        correct_idx = nums[i] - 1
        if 0 < nums[i] <= n and nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    return n + 1
```

---

## 🎯 Quick Selection Guide

| Problem Type | Pattern |
|--------------|---------|
| Count frequencies | 1. HashMap Counting |
| Find pair summing to X | 2. HashMap Complement |
| Track positions/distances | 3. HashMap Index |
| Check existence | 4. HashSet |
| Range sum queries | 5. Prefix Sum |
| O(1) space modification | 6. In-place |
| Numbers in [1,n], missing? | 7. Cyclic Sort |

---

## 🔑 Key Differences

```
HashMap vs HashSet:
├─ HashMap: Need value (count, index, data)
└─ HashSet: Only need existence

Check first vs Store first:
├─ Two Sum: Check complement THEN store (avoid using same element)
└─ Counting: Store immediately

Prefix Sum formula:
└─ sum(i, j) = prefix[j+1] - prefix[i]

Cyclic Sort key insight:
└─ Number i belongs at index i-1
```


