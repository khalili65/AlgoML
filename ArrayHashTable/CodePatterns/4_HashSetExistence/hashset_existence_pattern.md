# 📊 Pattern 4: HashSet Existence

---

## When to Use

- Check if element exists (O(1) lookup)
- Find unique elements
- Intersection/union of sets
- Detect duplicates
- "Is X in the collection?"

---

## The Template

```python
def setExistencePattern(nums):
    seen = set()  # or set(nums) to build immediately
    
    for num in nums:
        if num in seen:  # O(1) lookup!
            # Found duplicate or match
            pass
        seen.add(num)
```

---

## Set vs HashMap

```
Use HashSet when:
  - Only need to know IF element exists
  - Don't need count or index
  
Use HashMap when:
  - Need count: {element: frequency}
  - Need index: {element: index}
  - Need additional data: {element: data}
```

---

## Example 1: Contains Duplicate (LC 217)

**Problem:** Return true if array has duplicates.

```python
def containsDuplicate(nums):
    seen = set()
    
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    
    return False

# One-liner:
def containsDuplicate(nums):
    return len(nums) != len(set(nums))
```

---

## Example 2: Longest Consecutive Sequence (LC 128)

**Problem:** Find length of longest consecutive sequence.

```python
def longestConsecutive(nums):
    num_set = set(nums)
    max_len = 0
    
    for num in num_set:
        # Only start counting if this is the START of a sequence
        if num - 1 not in num_set:
            current = num
            length = 1
            
            while current + 1 in num_set:
                current += 1
                length += 1
            
            max_len = max(max_len, length)
    
    return max_len
```

**Why check `num - 1 not in num_set`?**
```
nums = [100, 4, 200, 1, 3, 2]
set = {1, 2, 3, 4, 100, 200}

Without check: We'd count 1,2,3,4 starting from each of 1, 2, 3, 4
               O(n²) in worst case!

With check: 
  - 1: 1-1=0 not in set → START! Count 1,2,3,4 → length 4
  - 2: 2-1=1 in set → SKIP (not a start)
  - 3: 3-1=2 in set → SKIP
  - 4: 4-1=3 in set → SKIP
  - 100: 99 not in set → START! Count 100 → length 1
  - 200: 199 not in set → START! Count 200 → length 1

O(n) because each number is visited at most twice!
```

---

## Example 3: Intersection of Two Arrays (LC 349)

**Problem:** Return intersection (unique elements in both).

```python
def intersection(nums1, nums2):
    return list(set(nums1) & set(nums2))

# Or explicit:
def intersection(nums1, nums2):
    set1 = set(nums1)
    result = set()
    
    for num in nums2:
        if num in set1:
            result.add(num)
    
    return list(result)
```

---

## Example 4: Happy Number (LC 202)

**Problem:** Check if number eventually reaches 1 through digit square sum.

```python
def isHappy(n):
    seen = set()
    
    def get_next(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit * digit
            num //= 10
        return total
    
    while n != 1:
        if n in seen:
            return False  # Cycle detected!
        seen.add(n)
        n = get_next(n)
    
    return True
```

---

## Example 5: Valid Sudoku (LC 36)

**Problem:** Check if Sudoku board is valid.

```python
def isValidSudoku(board):
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    
    for i in range(9):
        for j in range(9):
            num = board[i][j]
            if num == '.':
                continue
            
            box_idx = (i // 3) * 3 + (j // 3)
            
            if num in rows[i] or num in cols[j] or num in boxes[box_idx]:
                return False
            
            rows[i].add(num)
            cols[j].add(num)
            boxes[box_idx].add(num)
    
    return True
```

---

## Example 6: Set Operations

**Problem:** Various set operations.

```python
a = {1, 2, 3}
b = {2, 3, 4}

# Union (elements in either)
a | b  # {1, 2, 3, 4}
a.union(b)

# Intersection (elements in both)
a & b  # {2, 3}
a.intersection(b)

# Difference (elements in a but not b)
a - b  # {1}
a.difference(b)

# Symmetric difference (elements in either but not both)
a ^ b  # {1, 4}
a.symmetric_difference(b)

# Subset check
{1, 2} <= {1, 2, 3}  # True
{1, 2}.issubset({1, 2, 3})
```

---

## Set Patterns

```
┌─────────────────────────────────────────────────────────────┐
│   SET OPERATION PATTERNS                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Duplicate check:                                       │
│      if num in seen: return True                            │
│                                                             │
│   2. Cycle detection:                                       │
│      while state not in seen: seen.add(state)               │
│                                                             │
│   3. Sequence start check:                                  │
│      if num - 1 not in num_set: # start here                │
│                                                             │
│   4. Intersection:                                          │
│      set1 & set2                                            │
│                                                             │
│   5. Unique count:                                          │
│      len(set(nums))                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(1) average for add, remove, lookup
       O(n) for set operations on n elements
       
Space: O(n) - storing n unique elements
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   HASHSET EXISTENCE PATTERN                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   seen = set()                                              │
│                                                             │
│   for num in nums:                                          │
│       if num in seen:  # O(1) lookup                        │
│           # Found!                                          │
│       seen.add(num)                                         │
│                                                             │
│   USE FOR:                                                  │
│   • Duplicate detection                                     │
│   • Cycle detection                                         │
│   • Membership testing                                      │
│   • Set operations (union, intersection)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


