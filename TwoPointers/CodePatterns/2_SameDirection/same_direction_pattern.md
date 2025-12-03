# 👆👆 Pattern 2: Same Direction (Fast & Slow on Arrays)

---

## When to Use

- Remove elements in-place
- Keep certain elements
- Compress/deduplicate array
- "Do it in O(1) extra space"

---

## The Template

```python
def sameDirection(nums):
    slow = 0  # Write pointer (where to put next valid element)
    
    for fast in range(len(nums)):  # Read pointer
        if CONDITION(nums[fast]):  # Should we keep this element?
            nums[slow] = nums[fast]
            slow += 1
    
    return slow  # New length of valid portion
```

---

## Visual Understanding

```
Remove value 3 from [3, 2, 2, 3, 4]

slow
fast
[3, 2, 2, 3, 4]    nums[0]=3 == 3, skip (don't write)

slow
   fast
[3, 2, 2, 3, 4]    nums[1]=2 ≠ 3, write to slow, slow++

   slow
      fast
[2, 2, 2, 3, 4]    nums[2]=2 ≠ 3, write to slow, slow++

      slow
         fast
[2, 2, 2, 3, 4]    nums[3]=3 == 3, skip

      slow
            fast
[2, 2, 2, 3, 4]    nums[4]=4 ≠ 3, write to slow, slow++

         slow
[2, 2, 4, 3, 4]    return 3 (first 3 elements are valid: [2, 2, 4])
```

---

## Example 1: Remove Element (LC 27)

**Problem:** Remove all occurrences of val in-place.

```python
def removeElement(nums, val):
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] != val:
            nums[slow] = nums[fast]
            slow += 1
    
    return slow
```

---

## Example 2: Remove Duplicates from Sorted Array (LC 26)

**Problem:** Remove duplicates in-place from sorted array.

```python
def removeDuplicates(nums):
    if not nums:
        return 0
    
    slow = 1  # First element always stays
    
    for fast in range(1, len(nums)):
        if nums[fast] != nums[fast - 1]:  # Different from previous
            nums[slow] = nums[fast]
            slow += 1
    
    return slow
```

**Visual:**
```
[1, 1, 2, 2, 3]

slow=1, fast=1: 1 == 1, skip
slow=1, fast=2: 2 ≠ 1, write → [1, 2, 2, 2, 3], slow=2
slow=2, fast=3: 2 == 2, skip
slow=2, fast=4: 3 ≠ 2, write → [1, 2, 3, 2, 3], slow=3

Return 3: [1, 2, 3, _, _]
```

---

## Example 3: Remove Duplicates II - Allow 2 (LC 80)

**Problem:** Allow at most 2 duplicates of each element.

```python
def removeDuplicates(nums):
    if len(nums) <= 2:
        return len(nums)
    
    slow = 2  # First two elements always stay
    
    for fast in range(2, len(nums)):
        if nums[fast] != nums[slow - 2]:  # Compare with 2 back
            nums[slow] = nums[fast]
            slow += 1
    
    return slow
```

**Why compare with `slow - 2`?**
```
We want at most 2 of each value.
If nums[fast] == nums[slow-2], we already have 2 of this value.

[1, 1, 1, 2, 2, 3]
       slow
          fast

nums[fast]=1, nums[slow-2]=nums[0]=1, same → skip!
(We already have two 1s at positions 0 and 1)
```

---

## Example 4: Move Zeroes (LC 283)

**Problem:** Move all zeros to end, maintain order of non-zeros.

```python
def moveZeroes(nums):
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] != 0:
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1
```

**Why swap instead of just write?**
```
Swapping ensures zeros move to the back automatically!

[0, 1, 0, 3, 12]
 s
 f     → 0, skip

[0, 1, 0, 3, 12]
 s
    f  → 1≠0, swap → [1, 0, 0, 3, 12]

[1, 0, 0, 3, 12]
    s
       f  → 0, skip

[1, 0, 0, 3, 12]
    s
          f  → 3≠0, swap → [1, 3, 0, 0, 12]

[1, 3, 0, 0, 12]
       s
             f  → 12≠0, swap → [1, 3, 12, 0, 0]
```

---

## Example 5: Remove Linked List Duplicates (LC 83)

**Problem:** Remove duplicates from sorted linked list.

```python
def deleteDuplicates(head):
    if not head:
        return head
    
    slow = head
    fast = head.next
    
    while fast:
        if fast.val != slow.val:
            slow.next = fast
            slow = slow.next
        fast = fast.next
    
    slow.next = None  # Important! Cut off remaining
    return head
```

---

## Two Variations

```
┌─────────────────────────────────────────────────────────────┐
│   VARIATION 1: Write only (overwrite)                       │
├─────────────────────────────────────────────────────────────┤
│   for fast in range(len(nums)):                             │
│       if KEEP(nums[fast]):                                  │
│           nums[slow] = nums[fast]  # Overwrite              │
│           slow += 1                                         │
│                                                             │
│   Use when: Elements to remove are scattered                │
├─────────────────────────────────────────────────────────────┤
│   VARIATION 2: Swap                                         │
├─────────────────────────────────────────────────────────────┤
│   for fast in range(len(nums)):                             │
│       if KEEP(nums[fast]):                                  │
│           nums[slow], nums[fast] = nums[fast], nums[slow]   │
│           slow += 1                                         │
│                                                             │
│   Use when: Need to preserve removed elements (at end)      │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n) - single pass
Space: O(1) - in-place modification
```

---

## Common Mistakes

```
❌ Mistake 1: Wrong starting position
   slow = 0  # For remove element
   slow = 1  # For remove duplicates (first element stays)

❌ Mistake 2: Wrong comparison
   nums[fast] != nums[fast-1]   # Compare with previous (sorted)
   nums[fast] != nums[slow-1]   # Compare with last written
   
❌ Mistake 3: Forgetting edge cases
   if not nums: return 0
   if len(nums) <= 2: return len(nums)
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   SAME DIRECTION PATTERN                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   slow = 0  # or 1 for duplicates                           │
│                                                             │
│   for fast in range(len(nums)):                             │
│       if CONDITION(nums[fast]):                             │
│           nums[slow] = nums[fast]  # or swap                │
│           slow += 1                                         │
│                                                             │
│   return slow                                               │
│                                                             │
│   KEY: slow = write position, fast = read position          │
│        slow only moves when we keep an element              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


