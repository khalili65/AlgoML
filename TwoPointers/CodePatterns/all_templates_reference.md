# 👆👆 Two Pointers - All Templates Reference

Quick reference for all 5 two pointers code patterns with examples.

---

## Pattern 1: Opposite Direction

**Template:**
```python
left, right = 0, len(arr) - 1

while left < right:
    current = arr[left] + arr[right]
    
    if current == target:
        return [left, right]
    elif current < target:
        left += 1
    else:
        right -= 1
```

**Example - Two Sum II Sorted (LC 167):**
```python
def twoSum(numbers, target):
    left, right = 0, len(numbers) - 1
    
    while left < right:
        s = numbers[left] + numbers[right]
        if s == target:
            return [left + 1, right + 1]
        elif s < target:
            left += 1
        else:
            right -= 1
    return []
```

---

## Pattern 2: Same Direction

**Template:**
```python
slow = 0

for fast in range(len(nums)):
    if CONDITION(nums[fast]):
        nums[slow] = nums[fast]
        slow += 1

return slow
```

**Example - Remove Duplicates (LC 26):**
```python
def removeDuplicates(nums):
    if not nums:
        return 0
    slow = 1
    for fast in range(1, len(nums)):
        if nums[fast] != nums[fast - 1]:
            nums[slow] = nums[fast]
            slow += 1
    return slow
```

---

## Pattern 3: N-Sum

**Template:**
```python
nums.sort()

for i in range(len(nums) - 2):
    if i > 0 and nums[i] == nums[i-1]:
        continue
    
    left, right = i + 1, len(nums) - 1
    
    while left < right:
        s = nums[i] + nums[left] + nums[right]
        if s == target:
            # found, skip duplicates
        elif s < target:
            left += 1
        else:
            right -= 1
```

**Example - 3Sum (LC 15):**
```python
def threeSum(nums):
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i-1]:
            continue
        
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                left += 1
                right -= 1
                while left < right and nums[left] == nums[left-1]:
                    left += 1
            elif s < 0:
                left += 1
            else:
                right -= 1
    
    return result
```

---

## Pattern 4: Partition

**Template:**
```python
low, mid, high = 0, 0, len(nums) - 1

while mid <= high:
    if nums[mid] < pivot:
        nums[low], nums[mid] = nums[mid], nums[low]
        low += 1
        mid += 1
    elif nums[mid] > pivot:
        nums[mid], nums[high] = nums[high], nums[mid]
        high -= 1
        # Don't increment mid!
    else:
        mid += 1
```

**Example - Sort Colors (LC 75):**
```python
def sortColors(nums):
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 2:
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
        else:
            mid += 1
```

---

## Pattern 5: Merge Arrays

**Template:**
```python
i, j = 0, 0

while i < len(arr1) and j < len(arr2):
    if arr1[i] < arr2[j]:
        result.append(arr1[i])
        i += 1
    else:
        result.append(arr2[j])
        j += 1

result.extend(arr1[i:])
result.extend(arr2[j:])
```

**Example - Merge Sorted Array In-Place (LC 88):**
```python
def merge(nums1, m, nums2, n):
    p1, p2, p = m - 1, n - 1, m + n - 1
    
    while p2 >= 0:
        if p1 >= 0 and nums1[p1] > nums2[p2]:
            nums1[p] = nums1[p1]
            p1 -= 1
        else:
            nums1[p] = nums2[p2]
            p2 -= 1
        p -= 1
```

---

## 🎯 Quick Selection Guide

| Problem Type | Pattern |
|--------------|---------|
| Sorted array, find pair | 1. Opposite Direction |
| Check palindrome | 1. Opposite Direction |
| Container/water problems | 1. Opposite Direction |
| Remove/move elements | 2. Same Direction |
| Find triplets/quadruplets | 3. N-Sum |
| Sort limited values (0,1,2) | 4. Partition |
| Merge sorted arrays | 5. Merge Arrays |

---

## 🔑 Key Insights

```
Pattern 1: Requires SORTED array (usually)
Pattern 2: slow = write, fast = read
Pattern 3: SORT first, SKIP duplicates
Pattern 4: Don't increment mid after swap with high
Pattern 5: Merge from END for in-place
```


