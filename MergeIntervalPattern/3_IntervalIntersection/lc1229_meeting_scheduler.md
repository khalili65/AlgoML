# 🎬 Tutorial Script: LC 1229 — Meeting Scheduler

---

## Part 1 – Problem Introduction

**(Visual: LeetCode problem header.)**

**Instructor:**
"Let's solve **LeetCode 1229: Meeting Scheduler** — a practical variation of our Interval Intersection pattern where we need to find a **slot of a specific duration**!"

---

## Part 2 – Problem Statement

**(Visual: Problem description.)**

**Instructor:**
"Here's the problem:"

```
Given the availability time slots arrays slots1 and slots2 of two people 
and a meeting duration, return the earliest time slot that works for 
both of them and is of duration duration.

If there is no common time slot that satisfies the requirements, 
return an empty array.

The format of a time slot is an array of two elements [start, end] 
representing an inclusive time range from start to end.

It is guaranteed that no two availability slots of the same person 
intersect with each other. That is, for any two time slots [start1, end1] 
and [start2, end2] of the same person, either start1 > end2 or start2 > end1.
```

**(Visual: Examples.)**

```
Example 1:
Input: slots1 = [[10,50],[60,120],[140,210]]
       slots2 = [[0,15],[60,70]]
       duration = 8
Output: [60,68]
Explanation: Both persons are free [60,70]. 
             We need 8 minutes, so [60,68] works!

Example 2:
Input: slots1 = [[10,50],[60,120],[140,210]]
       slots2 = [[0,15],[60,70]]
       duration = 12
Output: []
Explanation: The common slot [60,70] is only 10 minutes long.
             We need 12 minutes, so no valid slot exists.
```

---

## Part 3 – Key Insight: Pattern Extension

**(Visual: Connecting to base pattern.)**

**Instructor:**
"This is an **extension** of our Interval Intersection pattern:

```
STANDARD INTERSECTION:
- Find ALL intersection intervals

MEETING SCHEDULER:
- Find intersections
- Check if intersection length >= duration
- Return the FIRST one that satisfies (earliest)
```

The key difference: we need the intersection to be **at least `duration` long**, and we return early once found!"

---

## Part 4 – Important Preprocessing: Sorting!

**(Visual: Sorting requirement.)**

**Instructor:**
"⚠️ **Critical difference from LC 986**: The input may NOT be sorted!

Look at the problem statement carefully — it only says slots don't overlap within a person, but **doesn't guarantee sorted order**."

```
We MUST sort both lists by start time first!

Why?
- Our two-pointer technique assumes sorted order
- Moving the pointer with smaller END only works if lists are sorted
```

```python
# Preprocessing step - don't forget!
slots1.sort(key=lambda x: x[0])
slots2.sort(key=lambda x: x[0])
```

---

## Part 5 – The Modified Pattern

**(Visual: Pattern for this problem.)**

**Instructor:**
"Here's our modified approach:"

```
┌─────────────────────────────────────────────────────────────┐
│           MEETING SCHEDULER PATTERN                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT both lists by start time                           │
│                                                             │
│  2. TWO POINTERS: i for slots1, j for slots2                │
│                                                             │
│  3. FOR EACH PAIR:                                          │
│     start = max(A.start, B.start)                           │
│     end = min(A.end, B.end)                                 │
│                                                             │
│  4. CHECK DURATION:                                         │
│     if end - start >= duration:                             │
│         return [start, start + duration]  ← FOUND!          │
│                                                             │
│  5. ADVANCE POINTER with smaller end                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Return EARLY when first valid slot is found!          │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Step-by-Step Walkthrough

**(Visual: Example walkthrough.)**

**Instructor:**
"Let's trace through Example 1:"

```
slots1 = [[10,50], [60,120], [140,210]]  (already sorted)
slots2 = [[0,15], [60,70]]               (already sorted)
duration = 8
```

### Iteration 1: i=0, j=0
```
A = [10, 50]
B = [0, 15]

Timeline:
[----B----]
      [------------A------------]
      |---∩---|

start = max(10, 0) = 10
end = min(50, 15) = 15
Intersection length = 15 - 10 = 5

5 >= 8? NO → Not long enough

A.end(50) > B.end(15) → j++
```

### Iteration 2: i=0, j=1
```
A = [10, 50]
B = [60, 70]

Timeline:
[------------A------------]
                              [--B--]
No overlap!

start = max(10, 60) = 60
end = min(50, 70) = 50
60 > 50? → No intersection

A.end(50) < B.end(70) → i++
```

### Iteration 3: i=1, j=1
```
A = [60, 120]
B = [60, 70]

Timeline:
[------------A------------]
[--B--]
|--∩--|

start = max(60, 60) = 60
end = min(120, 70) = 70
Intersection length = 70 - 60 = 10

10 >= 8? YES! ✓

Return [start, start + duration] = [60, 60 + 8] = [60, 68]
```

**Result:** `[60, 68]` ✓

---

## Part 7 – Complete Solution

**(Visual: Full code.)**

**Instructor:**
"Here's the complete solution:"

```python
def minAvailableDuration(slots1, slots2, duration):
    # Step 1: Sort both lists by start time
    slots1.sort(key=lambda x: x[0])
    slots2.sort(key=lambda x: x[0])
    
    # Step 2: Two pointers
    i, j = 0, 0
    
    while i < len(slots1) and j < len(slots2):
        # Get current slots
        A = slots1[i]
        B = slots2[j]
        
        # Find intersection
        start = max(A[0], B[0])
        end = min(A[1], B[1])
        
        # Check if intersection is long enough
        if end - start >= duration:
            return [start, start + duration]
        
        # Move pointer with smaller end
        if A[1] < B[1]:
            i += 1
        else:
            j += 1
    
    # No valid slot found
    return []
```

---

## Part 8 – Code Walkthrough

**(Visual: Annotated code.)**

**Instructor:**
"Let's break it down:"

```python
def minAvailableDuration(slots1, slots2, duration):
    # IMPORTANT: Must sort first (not guaranteed to be sorted!)
    slots1.sort(key=lambda x: x[0])
    slots2.sort(key=lambda x: x[0])
    
    i, j = 0, 0
    
    while i < len(slots1) and j < len(slots2):
        A = slots1[i]
        B = slots2[j]
        
        # Same intersection formula as LC 986
        start = max(A[0], B[0])
        end = min(A[1], B[1])
        
        # NEW: Check if intersection meets duration requirement
        # We return [start, start + duration] — not [start, end]!
        # This gives us the EARLIEST start with exactly 'duration' length
        if end - start >= duration:
            return [start, start + duration]
        
        # Same pointer advancement logic
        if A[1] < B[1]:
            i += 1
        else:
            j += 1
    
    return []  # No valid slot found
```

---

## Part 9 – Why Return `[start, start + duration]`?

**(Visual: Explanation.)**

**Instructor:**
"You might wonder: why not just return `[start, end]`?

```
Example:
Intersection = [60, 70]  (10 minutes available)
Duration = 8

Option 1: Return [60, 70]  ❌
→ Gives 10 minutes, but we only need 8!

Option 2: Return [60, 68]  ✓
→ Gives exactly 8 minutes, starting as early as possible

The problem asks for a slot OF duration, not the entire intersection!
```

**Key insight:** We want the **earliest possible start** with **exactly the required duration**."

---

## Part 10 – Edge Cases

**(Visual: Edge cases.)**

**Instructor:**
"Let's cover the edge cases:"

### Case 1: No Common Slot Long Enough
```python
slots1 = [[10,50]]
slots2 = [[40,60]]
duration = 20

Intersection = [40, 50] → length 10
10 < 20 → Return []
```

### Case 2: Exact Duration Match
```python
slots1 = [[10,20]]
slots2 = [[15,25]]
duration = 5

Intersection = [15, 20] → length 5
5 >= 5 ✓ → Return [15, 20]
```

### Case 3: Empty Input
```python
slots1 = []
slots2 = [[10,20]]
# Output: []
# Loop never executes
```

### Case 4: Unsorted Input
```python
slots1 = [[60,120], [10,50]]  # NOT sorted!
slots2 = [[0,15], [60,70]]

# After sorting:
slots1 = [[10,50], [60,120]]
slots2 = [[0,15], [60,70]]
# Now we can process correctly
```

---

## Part 11 – Complexity Analysis

**Instructor:**
"Let's analyze:"

```
Time Complexity: O(m log m + n log n)
- Sorting slots1: O(m log m)
- Sorting slots2: O(n log n)
- Two-pointer traversal: O(m + n)
- Total: O(m log m + n log n)

Note: If input was guaranteed sorted, it would be O(m + n)

Space Complexity: O(1) or O(log m + log n)
- O(1) extra space for pointers
- O(log n) if counting sorting's stack space
```

---

## Part 12 – Comparison: LC 986 vs LC 1229

**(Visual: Side-by-side comparison.)**

**Instructor:**
"Let's compare the two problems:"

| Aspect | LC 986 | LC 1229 |
|--------|--------|---------|
| **Input** | Sorted | May be unsorted |
| **Goal** | Find ALL intersections | Find FIRST valid slot |
| **Constraint** | None | Minimum duration |
| **Output** | List of all | Single slot or empty |
| **Termination** | Exhaust both lists | Early return when found |

```python
# LC 986: Collect all intersections
if start <= end:
    result.append([start, end])
return result

# LC 1229: Return first valid one
if end - start >= duration:
    return [start, start + duration]
return []
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**
"Watch out for these:"

```
❌ Mistake 1: Forgetting to sort
   The input is NOT guaranteed to be sorted!
   Always sort by start time first.

❌ Mistake 2: Returning [start, end] instead of [start, start + duration]
   Wrong: return [start, end]  → May be longer than needed
   Right: return [start, start + duration]  → Exactly the duration

❌ Mistake 3: Using `end - start > duration` instead of `>=`
   We need AT LEAST duration, so >= is correct!

❌ Mistake 4: Continuing after finding a valid slot
   We want the EARLIEST slot, so return immediately!
```

---

## Part 14 – Summary

**Instructor:**
"That's LC 1229! Key takeaways:

1. **Sort first** — input may not be sorted!
2. Same **two-pointer + intersection formula** as LC 986
3. Check if intersection **length >= duration**
4. Return `[start, start + duration]` for exact slot
5. **Return early** when first valid slot is found

This shows how the base pattern adapts for real-world scheduling problems!"

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────────┐
│              LC 1229 SOLUTION TEMPLATE                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  # Don't forget to sort!                                    │
│  slots1.sort(key=lambda x: x[0])                            │
│  slots2.sort(key=lambda x: x[0])                            │
│                                                             │
│  i, j = 0, 0                                                │
│  while i < len(slots1) and j < len(slots2):                 │
│      start = max(slots1[i][0], slots2[j][0])                │
│      end = min(slots1[i][1], slots2[j][1])                  │
│                                                             │
│      if end - start >= duration:                            │
│          return [start, start + duration]  # FOUND!         │
│                                                             │
│      if slots1[i][1] < slots2[j][1]:                        │
│          i += 1                                             │
│      else:                                                  │
│          j += 1                                             │
│                                                             │
│  return []  # No valid slot                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

