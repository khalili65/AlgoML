# 📊 Pattern 1: HashMap Counting (Frequency Map)

---

## When to Use

- Count occurrences of elements
- Find most/least frequent elements
- Group elements by some property
- Check if two things are "equal" (anagram, permutation)
- Frequency-based decisions

---

## The Template

```python
from collections import Counter

def countPattern(items):
    count = {}  # or Counter(items) for one-liner
    
    for item in items:
        count[item] = count.get(item, 0) + 1
    
    # Use the frequency map
    for item, freq in count.items():
        # process based on frequency...
        pass
```

---

## Python Counter Shortcuts

```python
from collections import Counter

nums = [1, 2, 2, 3, 3, 3]

# Create counter
count = Counter(nums)  # Counter({3: 3, 2: 2, 1: 1})

# Most common
count.most_common(2)   # [(3, 3), (2, 2)]

# Access count
count[3]               # 3
count[99]              # 0 (no KeyError!)

# Arithmetic
Counter([1,2]) + Counter([2,3])  # Counter({2: 2, 1: 1, 3: 1})
Counter([1,2,2]) - Counter([2])  # Counter({1: 1, 2: 1})
```

---

## Example 1: Valid Anagram (LC 242)

**Problem:** Check if two strings are anagrams.

```python
def isAnagram(s, t):
    if len(s) != len(t):
        return False
    
    count = {}
    
    # Count characters in s
    for c in s:
        count[c] = count.get(c, 0) + 1
    
    # Subtract characters in t
    for c in t:
        if c not in count or count[c] == 0:
            return False
        count[c] -= 1
    
    return True

# One-liner:
def isAnagram(s, t):
    return Counter(s) == Counter(t)
```

---

## Example 2: Group Anagrams (LC 49)

**Problem:** Group strings that are anagrams of each other.

```python
from collections import defaultdict

def groupAnagrams(strs):
    groups = defaultdict(list)
    
    for s in strs:
        # Anagrams have the same sorted form
        key = tuple(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```

**Alternative (count-based key):**
```python
def groupAnagrams(strs):
    groups = defaultdict(list)
    
    for s in strs:
        # Use character count as key
        count = [0] * 26
        for c in s:
            count[ord(c) - ord('a')] += 1
        groups[tuple(count)].append(s)
    
    return list(groups.values())
```

---

## Example 3: Top K Frequent Elements (LC 347)

**Problem:** Return k most frequent elements.

```python
def topKFrequent(nums, k):
    count = Counter(nums)
    
    # Bucket sort by frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, freq in count.items():
        buckets[freq].append(num)
    
    # Collect k most frequent
    result = []
    for i in range(len(buckets) - 1, -1, -1):
        for num in buckets[i]:
            result.append(num)
            if len(result) == k:
                return result
    
    return result
```

**Visual:**
```
nums = [1,1,1,2,2,3], k = 2

count = {1: 3, 2: 2, 3: 1}

buckets:
  index 0: []
  index 1: [3]
  index 2: [2]
  index 3: [1]

Traverse from high to low: [1, 2]
```

---

## Example 4: Single Number (LC 136)

**Problem:** Find the number that appears only once (others appear twice).

```python
def singleNumber(nums):
    count = Counter(nums)
    
    for num, freq in count.items():
        if freq == 1:
            return num

# Better: XOR approach (no extra space)
def singleNumber(nums):
    result = 0
    for num in nums:
        result ^= num
    return result
```

---

## Example 5: Majority Element (LC 169)

**Problem:** Find element that appears more than n/2 times.

```python
def majorityElement(nums):
    count = Counter(nums)
    
    for num, freq in count.items():
        if freq > len(nums) // 2:
            return num

# Better: Boyer-Moore Voting (O(1) space)
def majorityElement(nums):
    candidate = None
    count = 0
    
    for num in nums:
        if count == 0:
            candidate = num
        count += 1 if num == candidate else -1
    
    return candidate
```

---

## Example 6: Ransom Note (LC 383)

**Problem:** Can ransomNote be constructed from magazine?

```python
def canConstruct(ransomNote, magazine):
    mag_count = Counter(magazine)
    
    for c in ransomNote:
        if mag_count[c] <= 0:
            return False
        mag_count[c] -= 1
    
    return True

# One-liner:
def canConstruct(ransomNote, magazine):
    return not (Counter(ransomNote) - Counter(magazine))
```

---

## Common Counting Patterns

```
┌─────────────────────────────────────────────────────────────┐
│   COUNTING VARIATIONS                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Simple count:                                          │
│      count[x] = count.get(x, 0) + 1                         │
│                                                             │
│   2. Count and subtract:                                    │
│      count[x] -= 1  (for matching/anagram)                  │
│                                                             │
│   3. Count as key:                                          │
│      groups[tuple(sorted(s))].append(s)                     │
│                                                             │
│   4. Bucket by frequency:                                   │
│      buckets[freq].append(item)                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - single pass to count
Space: O(k) - where k is number of unique elements
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   HASHMAP COUNTING PATTERN                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   count = {}                                                │
│   for item in items:                                        │
│       count[item] = count.get(item, 0) + 1                  │
│                                                             │
│   # Or use Counter:                                         │
│   from collections import Counter                           │
│   count = Counter(items)                                    │
│                                                             │
│   USE FOR:                                                  │
│   • Frequency problems                                      │
│   • Anagram/permutation checks                              │
│   • Top K frequent                                          │
│   • Majority element                                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


