# 👆👆 Pattern 1: Opposite Direction (Converging Pointers)

---

## When to Use

- Sorted array + find pair with condition
- Check if something is palindrome
- Container/trapping water problems
- "Find two elements that..."

---

## The Template

```python
def oppositeDirection(arr):
    left = 0
    right = len(arr) - 1
    
    while left < right:
        current = COMPUTE(arr[left], arr[right])
        
        if current == target:
            return [left, right]
        elif current < target:
            left += 1   # Need larger value
        else:
            right -= 1  # Need smaller value
    
    return []
```

---

## Visual Understanding

```
Sorted Array: [1, 2, 3, 4, 5, 6, 7], target = 9

Start:
[1, 2, 3, 4, 5, 6, 7]
 L                 R    sum = 1 + 7 = 8 < 9, move L

[1, 2, 3, 4, 5, 6, 7]
    L              R    sum = 2 + 7 = 9 = target ✓

Key insight: 
- Array is SORTED
- If sum too small → increase left (get larger number)
- If sum too big → decrease right (get smaller number)
```

---

## Example 1: Two Sum II - Sorted (LC 167)

**Problem:** Find two numbers that add up to target in sorted array.

```python
def twoSum(numbers, target):
    left, right = 0, len(numbers) - 1
    
    while left < right:
        current_sum = numbers[left] + numbers[right]
        
        if current_sum == target:
            return [left + 1, right + 1]  # 1-indexed
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []
```

---

## Example 2: Valid Palindrome (LC 125)

**Problem:** Check if string is palindrome (ignore non-alphanumeric).

```python
def isPalindrome(s):
    left, right = 0, len(s) - 1
    
    while left < right:
        # Skip non-alphanumeric characters
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True
```

**Visual:**
```
"A man, a plan, a canal: Panama"

L                              R
A                              a  ✓

 L                            R
 (space, skip)               m

  L                          R
  m                          a  
     ...
     
Compare from both ends, skip non-letters
```

---

## Example 3: Container With Most Water (LC 11)

**Problem:** Find two lines that form container with most water.

```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_water = 0
    
    while left < right:
        width = right - left
        h = min(height[left], height[right])
        max_water = max(max_water, width * h)
        
        # Move the shorter line (it's the bottleneck)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_water
```

**Why move the shorter line?**
```
Area = width × min(left_height, right_height)

If left is shorter:
- Moving left might find a taller line → area could increase
- Moving right will decrease width AND min stays same (left is still shorter)
  → area will definitely decrease

So always move the shorter one!
```

---

## Example 4: Trapping Rain Water (LC 42)

**Problem:** Calculate water trapped between bars.

```python
def trap(height):
    left, right = 0, len(height) - 1
    left_max, right_max = 0, 0
    water = 0
    
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    
    return water
```

**Visual:**
```
height = [0,1,0,2,1,0,1,3,2,1,2,1]

Water at each position = min(left_max, right_max) - height

Two pointers track left_max and right_max as they converge.
Process the smaller side (we know that's the limiting factor).
```

---

## Example 5: Valid Palindrome II (LC 680)

**Problem:** Check if string can be palindrome by removing at most one character.

```python
def validPalindrome(s):
    def isPalin(left, right):
        while left < right:
            if s[left] != s[right]:
                return False
            left += 1
            right -= 1
        return True
    
    left, right = 0, len(s) - 1
    
    while left < right:
        if s[left] != s[right]:
            # Try skipping left OR right
            return isPalin(left + 1, right) or isPalin(left, right - 1)
        left += 1
        right -= 1
    
    return True
```

---

## Complexity

```
Time:  O(n) - each pointer moves at most n times
Space: O(1) - only two pointers
```

---

## Common Mistakes

```
❌ Mistake 1: Using wrong comparison
   while left <= right:  # Usually wrong for pairs!
   while left < right:   # Correct - stops when they meet

❌ Mistake 2: Moving wrong pointer
   # For sum problems: move pointer that helps reach target
   # For palindrome: move both equally

❌ Mistake 3: Forgetting array must be sorted for sum problems
   # Two pointer for sum only works on SORTED arrays!
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   OPPOSITE DIRECTION PATTERN                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   left, right = 0, len(arr) - 1                             │
│                                                             │
│   while left < right:                                       │
│       if CONDITION:                                         │
│           left += 1                                         │
│       else:                                                 │
│           right -= 1                                        │
│                                                             │
│   KEY INSIGHTS:                                             │
│   • Sorted array: move based on comparison with target      │
│   • Palindrome: move both after comparing                   │
│   • Container: move the shorter one                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


