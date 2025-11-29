# 🎬 Tutorial Script: LC 45 — Jump Game II

---

## Part 1 – Problem Introduction

**(Visual: LeetCode problem header.)**

**Instructor:**
"Let's solve **LeetCode 45: Jump Game II** — this is actually the **origin** of our Interval Coverage pattern! Understanding this problem deeply will help you master the entire pattern family."

---

## Part 2 – Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:"

```
You are given a 0-indexed array of integers nums of length n.
You are initially positioned at nums[0].

Each element nums[i] represents the maximum length of a forward 
jump from index i. In other words, if you are at nums[i], you 
can jump to any nums[i + j] where:
  - 0 <= j <= nums[i]
  - i + j < n

Return the minimum number of jumps to reach nums[n - 1].
The test cases are generated such that you can reach nums[n - 1].
```

**(Visual: Examples.)**

```
Example 1:
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: Jump 1 step from index 0 to 1, then 3 steps to end.
             Path: 0 → 1 → 4 (2 jumps)

Example 2:
Input: nums = [2,3,0,1,4]
Output: 2
Explanation: Same path works: 0 → 1 → 4
```

---

## Part 3 – Connecting to Interval Coverage

**(Visual: Interval mapping.)**

**Instructor:**
"Here's the beautiful insight — Jump Game II IS an interval coverage problem!"

```
MAPPING:
- Position i with value nums[i] creates an "interval" [i, i + nums[i]]
- Starting at position 0, we need to reach position n-1
- Each jump "covers" from current position to a new position

Example: nums = [2,3,1,1,4]
Position 0: value 2 → can reach [0, 2]
Position 1: value 3 → can reach [1, 4]
Position 2: value 1 → can reach [2, 3]
Position 3: value 1 → can reach [3, 4]
Position 4: value 4 → can reach [4, 8]

Target: reach position 4 (index n-1)
```

---

## Part 4 – The Greedy Strategy

**(Visual: Strategy explanation.)**

**Instructor:**
"The greedy insight is the same:"

```
┌─────────────────────────────────────────────────────────────┐
│           JUMP GAME II GREEDY STRATEGY                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  At each position, we can jump to multiple places.          │
│  GREEDY CHOICE: Jump to the position that lets us reach     │
│  the FARTHEST in the NEXT jump!                             │
│                                                             │
│  Variables:                                                 │
│  - current_end: farthest we can reach with current jumps    │
│  - farthest: farthest we can reach with one more jump       │
│  - jumps: number of jumps made                              │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: When we reach current_end, we MUST jump!              │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – Step-by-Step Walkthrough

**(Visual: Example 1 walkthrough.)**

**Instructor:**
"Let's trace through Example 1."

```
nums = [2, 3, 1, 1, 4]
         0  1  2  3  4

Initialize:
  jumps = 0
  current_end = 0  (can reach index 0 without jumping)
  farthest = 0
```

### Position 0: nums[0] = 2
```
farthest = max(0, 0 + 2) = 2

i (0) == current_end (0) → Must jump!
  jumps = 1
  current_end = farthest = 2

Now we can reach up to index 2
```

### Position 1: nums[1] = 3
```
farthest = max(2, 1 + 3) = 4

i (1) != current_end (2) → Don't jump yet
Continue exploring...
```

### Position 2: nums[2] = 1
```
farthest = max(4, 2 + 1) = 4

i (2) == current_end (2) → Must jump!
  jumps = 2
  current_end = farthest = 4

Now we can reach index 4 (the end!)
```

### Done!
```
current_end (4) >= n-1 (4) → We can reach the end!
Return jumps = 2
```

---

## Part 6 – Visual Walkthrough

**(Visual: Timeline.)**

**Instructor:**
"Visualizing the jumps:"

```
Index:    0     1     2     3     4
nums:    [2]   [3]   [1]   [1]   [4]
          |     |     |     |     |
          └──┬──┘     |     |     |
             |        |     |     |
Range from 0: [0,2]   |     |     |
                      |     |     |
          └─────────┬─┘     |     |
                    |       |     |
Range from 1: [1,4] (reaches end!)
                    |       |     |
          └──────────────┬──┘     |
                         |        |
                         └────────┘

Jump 1: 0 → 1 (best choice from position 0)
Jump 2: 1 → 4 (reaches the end!)

Total: 2 jumps
```

---

## Part 7 – Complete Solution

**(Visual: Full code.)**

**Instructor:**
"Here's the elegant O(n) solution:"

```python
def jump(nums):
    n = len(nums)
    if n <= 1:
        return 0
    
    jumps = 0
    current_end = 0    # Farthest reachable with 'jumps' jumps
    farthest = 0       # Farthest reachable with 'jumps + 1' jumps
    
    for i in range(n - 1):  # Don't need to process last index
        # Update farthest reachable from position i
        farthest = max(farthest, i + nums[i])
        
        # If we've reached the boundary of current jump
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            # Early termination: can already reach the end
            if current_end >= n - 1:
                break
    
    return jumps
```

---

## Part 8 – Why `range(n - 1)`?

**(Visual: Edge case explanation.)**

**Instructor:**
"A common question: Why iterate only to `n - 1`?"

```
If we iterate to n-1 (inclusive), and we're at the last position:
- i == current_end might be true
- We'd count an extra jump we don't need!

Example: nums = [1, 2]
- Position 0: farthest = 1, i == current_end → jumps = 1, current_end = 1
- Position 1: farthest = 3, i == current_end → jumps = 2 (WRONG!)

We're already AT the destination, we don't need to jump FROM it!
```

---

## Part 9 – Alternative: Explicit Interval Approach

**(Visual: Interval-style solution.)**

**Instructor:**
"Here's the same logic written to match our Interval Coverage template exactly:"

```python
def jump(nums):
    n = len(nums)
    if n <= 1:
        return 0
    
    # Convert to "intervals": position i can reach [i, i + nums[i]]
    # But we don't need to explicitly create intervals!
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    # Process each position
    for i in range(n - 1):
        # This position can reach up to i + nums[i]
        farthest = max(farthest, i + nums[i])
        
        # When we've explored all positions in current "level"
        if i == current_end:
            jumps += 1           # Make the jump
            current_end = farthest  # Update boundary
            
            if current_end >= n - 1:
                return jumps
    
    return jumps
```

---

## Part 10 – BFS Perspective

**(Visual: BFS interpretation.)**

**Instructor:**
"Another way to think about it: this is **level-order BFS**!"

```
nums = [2, 3, 1, 1, 4]

Level 0: [0]           (start)
Level 1: [1, 2]        (reachable from 0)
Level 2: [3, 4]        (reachable from level 1)
                       (4 is the end!)

Number of levels to reach end = 2 jumps!
```

```
The greedy solution simulates BFS without a queue:
- current_end = boundary of current level
- farthest = boundary of next level
- When we cross current_end, we move to the next level
```

---

## Part 11 – Edge Cases

**(Visual: Edge cases.)**

**Instructor:**
"Let's handle edge cases:"

### Case 1: Already at destination
```python
nums = [0]
n = 1

n <= 1 → Return 0 (no jumps needed)
```

### Case 2: Can reach in one jump
```python
nums = [5, 0, 0, 0, 0]
n = 5

i = 0: farthest = 5, i == current_end
       jumps = 1, current_end = 5
       5 >= 4 → Return 1
```

### Case 3: Must use every position
```python
nums = [1, 1, 1, 1]
n = 4

i = 0: farthest = 1, jumps = 1, current_end = 1
i = 1: farthest = 2, jumps = 2, current_end = 2
i = 2: farthest = 3, jumps = 3, current_end = 3
3 >= 3 → Return 3
```

---

## Part 12 – Complexity Analysis

**Instructor:**
"This solution is beautifully efficient:"

```
Time Complexity: O(n)
- Single pass through the array
- Each element visited exactly once

Space Complexity: O(1)
- Only three variables: jumps, current_end, farthest
- No extra data structures!
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**
"Watch out for these:"

```
❌ Mistake 1: Using BFS with queue (O(n²) worst case)
   The greedy O(n) solution is much better!

❌ Mistake 2: Iterating to n instead of n-1
   Causes extra jump count at the end

❌ Mistake 3: Not handling n <= 1
   If we're already at the destination, return 0

❌ Mistake 4: Using max incorrectly
   farthest = max(farthest, i + nums[i]), not just i + nums[i]

❌ Mistake 5: Checking >= n instead of >= n-1
   We need to reach index n-1, not go beyond
```

---

## Part 14 – Comparison: Jump Game I vs II

**(Visual: Comparison table.)**

**Instructor:**
"Don't confuse Jump Game I and II:"

| Aspect | Jump Game I (LC 55) | Jump Game II (LC 45) |
|--------|---------------------|----------------------|
| **Question** | CAN you reach the end? | MINIMUM jumps to reach end? |
| **Return** | Boolean | Integer |
| **Approach** | Track farthest reachable | Greedy coverage |
| **Guarantee** | May be impossible | Always possible (per problem) |

```python
# Jump Game I: Just check if farthest reaches end
def canJump(nums):
    farthest = 0
    for i, jump in enumerate(nums):
        if i > farthest:
            return False
        farthest = max(farthest, i + jump)
    return True
```

---

## Part 15 – Summary

**Instructor:**
"That's LC 45! Key takeaways:

1. **Greedy interval coverage** in disguise
2. **O(n) time, O(1) space** — no BFS queue needed!
3. **Two pointers**: current_end and farthest
4. **Jump when reaching boundary** (`i == current_end`)
5. **Stop at n-1**, don't process the last element

Master this pattern and you've mastered interval coverage!"

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│              LC 45 SOLUTION TEMPLATE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  def jump(nums):                                            │
│      n = len(nums)                                          │
│      if n <= 1:                                             │
│          return 0                                           │
│                                                             │
│      jumps = 0                                              │
│      current_end = 0                                        │
│      farthest = 0                                           │
│                                                             │
│      for i in range(n - 1):  # NOT n!                       │
│          farthest = max(farthest, i + nums[i])              │
│                                                             │
│          if i == current_end:                               │
│              jumps += 1                                     │
│              current_end = farthest                         │
│                                                             │
│              if current_end >= n - 1:                       │
│                  break                                      │
│                                                             │
│      return jumps                                           │
│                                                             │
│  TIME: O(n)    SPACE: O(1)                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

