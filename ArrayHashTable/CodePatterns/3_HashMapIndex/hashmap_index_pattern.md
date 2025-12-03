# 📊 Pattern 3: HashMap Index Tracking

---

## When to Use

- Need to know WHERE we saw something
- Finding duplicates within distance k
- Track last/first occurrence
- Distance-based conditions

---

## The Template

```python
def indexTrackingPattern(nums):
    last_seen = {}  # value -> last index
    
    for i, num in enumerate(nums):
        if num in last_seen:
            prev_idx = last_seen[num]
            # Check distance: i - prev_idx
            # Process based on distance
        
        last_seen[num] = i  # Update to current index
```

---

## Example 1: Contains Duplicate II (LC 219)

**Problem:** Check if array has duplicates within distance k.

```python
def containsNearbyDuplicate(nums, k):
    last_seen = {}
    
    for i, num in enumerate(nums):
        if num in last_seen and i - last_seen[num] <= k:
            return True
        
        last_seen[num] = i
    
    return False
```

**Visual:**
```
nums = [1, 2, 3, 1], k = 3

i=0: 1 not seen, last_seen = {1: 0}
i=1: 2 not seen, last_seen = {1: 0, 2: 1}
i=2: 3 not seen, last_seen = {1: 0, 2: 1, 3: 2}
i=3: 1 seen at 0, distance = 3 - 0 = 3 ≤ k ✓

Return True
```

---

## Example 2: First Unique Character (LC 387)

**Problem:** Find index of first non-repeating character.

```python
def firstUniqChar(s):
    count = {}
    
    # First pass: count frequencies
    for c in s:
        count[c] = count.get(c, 0) + 1
    
    # Second pass: find first with count 1
    for i, c in enumerate(s):
        if count[c] == 1:
            return i
    
    return -1
```

---

## Example 3: Contains Duplicate III (LC 220)

**Problem:** Check if abs(nums[i] - nums[j]) <= t and abs(i - j) <= k.

```python
def containsNearbyAlmostDuplicate(nums, k, t):
    if t < 0:
        return False
    
    buckets = {}
    bucket_size = t + 1  # Avoid division by zero
    
    for i, num in enumerate(nums):
        bucket = num // bucket_size
        
        # Check same bucket
        if bucket in buckets:
            return True
        
        # Check adjacent buckets
        if bucket - 1 in buckets and abs(num - buckets[bucket - 1]) <= t:
            return True
        if bucket + 1 in buckets and abs(num - buckets[bucket + 1]) <= t:
            return True
        
        buckets[bucket] = num
        
        # Remove element outside window k
        if i >= k:
            old_bucket = nums[i - k] // bucket_size
            del buckets[old_bucket]
    
    return False
```

---

## Example 4: Longest Substring Without Repeating (LC 3)

**Problem:** Find length of longest substring without repeating characters.

```python
def lengthOfLongestSubstring(s):
    last_seen = {}
    max_len = 0
    start = 0
    
    for i, c in enumerate(s):
        if c in last_seen and last_seen[c] >= start:
            start = last_seen[c] + 1  # Move start past duplicate
        
        max_len = max(max_len, i - start + 1)
        last_seen[c] = i
    
    return max_len
```

**Visual:**
```
s = "abcabcbb"

i=0: 'a', start=0, last_seen={'a':0}, len=1
i=1: 'b', start=0, last_seen={'a':0,'b':1}, len=2
i=2: 'c', start=0, last_seen={'a':0,'b':1,'c':2}, len=3
i=3: 'a' seen at 0 >= start, start=1
     last_seen={'a':3,'b':1,'c':2}, len=3
i=4: 'b' seen at 1 >= start, start=2
     ...

Max length = 3 ("abc")
```

---

## Example 5: Minimum Window Substring (LC 76)

**Problem:** Find shortest substring containing all characters of t.

```python
from collections import Counter

def minWindow(s, t):
    if not t or not s:
        return ""
    
    t_count = Counter(t)
    required = len(t_count)
    
    window_count = {}
    formed = 0
    
    left = 0
    result = (float('inf'), None, None)  # (length, left, right)
    
    for right, c in enumerate(s):
        window_count[c] = window_count.get(c, 0) + 1
        
        if c in t_count and window_count[c] == t_count[c]:
            formed += 1
        
        # Contract window
        while formed == required:
            if right - left + 1 < result[0]:
                result = (right - left + 1, left, right)
            
            left_c = s[left]
            window_count[left_c] -= 1
            if left_c in t_count and window_count[left_c] < t_count[left_c]:
                formed -= 1
            left += 1
    
    return "" if result[0] == float('inf') else s[result[1]:result[2] + 1]
```

---

## Index Tracking Variations

```
┌─────────────────────────────────────────────────────────────┐
│   INDEX TRACKING VARIATIONS                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Last occurrence:                                       │
│      last_seen[num] = i  (always update)                    │
│                                                             │
│   2. First occurrence:                                      │
│      if num not in first_seen:                              │
│          first_seen[num] = i  (only store first)            │
│                                                             │
│   3. Distance check:                                        │
│      if num in last_seen and i - last_seen[num] <= k        │
│                                                             │
│   4. Window update:                                         │
│      if c in last_seen and last_seen[c] >= start            │
│          start = last_seen[c] + 1                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - single or two passes
Space: O(k) - where k is number of unique elements
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   HASHMAP INDEX PATTERN                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   last_seen = {}                                            │
│                                                             │
│   for i, num in enumerate(nums):                            │
│       if num in last_seen:                                  │
│           prev_idx = last_seen[num]                         │
│           distance = i - prev_idx                           │
│           # Process based on distance                       │
│                                                             │
│       last_seen[num] = i                                    │
│                                                             │
│   KEY: Track WHERE, not just IF                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


