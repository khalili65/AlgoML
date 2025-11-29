# 🎬 Tutorial Script: Interval Intersection Pattern (Example → Pattern → Template)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

Today we're going to learn another powerful interval pattern: the **Interval Intersection** pattern.

This pattern is different from merging — instead of combining overlapping intervals, we're finding where two sets of intervals **overlap with each other**. Think of it like finding the **common time slots** when two people's schedules overlap."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: two lists of intervals.)**

Show on screen:

```text
firstList  = [[0,2], [5,10], [13,23], [24,25]]
secondList = [[1,5], [8,12], [15,24], [25,26]]
```

**Instructor:**
"Here's our problem:

> We have **two lists of intervals**, both sorted. We want to find all the **intersections** — places where an interval from the first list overlaps with an interval from the second list.

Think of it like this: Person A is busy during certain times, Person B is busy during other times. We want to find all the times when **both are busy simultaneously**."

**(Visual: two timelines showing both schedules.)**

```
Person A (firstList):
|██|     |█████|        |██████████|  |█|
0  2     5    10       13         23 24 25

Person B (secondList):
  |████|   |████|         |█████████||██|
  1    5   8   12        15        24 25 26

Intersections (both busy):
  |█|    |█|  |██|        |████████|  ||
  1 2    5 5  8 10       15       23 24 25
```

**Instructor:**
"Looking at this visually:
- When A is at [0,2] and B is at [1,5], they overlap at **[1,2]**
- When A is at [5,10] and B is at [1,5], they overlap at just **[5,5]** (a single point!)
- When A is at [5,10] and B is at [8,12], they overlap at **[8,10]**

And so on. Our goal is to find ALL these intersection points systematically."

---

## Part 2 – The "Aha!" Moment (Understanding Intersection)

**(Visual: Two overlapping intervals highlighted.)**

**Instructor:**
"Let me ask you: given two intervals, how do we find their intersection?

First, let's recall the **complete overlap condition**:"

```
┌─────────────────────────────────────────────────────────────┐
│           COMPLETE OVERLAP CONDITION                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Two intervals [A.start, A.end] and [B.start, B.end]       │
│   OVERLAP if and only if BOTH are true:                     │
│                                                             │
│     1. A.start <= B.end   (A starts before B ends)          │
│     2. B.start <= A.end   (B starts before A ends)          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Instructor:**
"But for intersection, we don't just need to know IF they overlap — we need to find the **exact overlap region**!"

**(Visual: Intersection formula.)**

```
If intervals A and B overlap, their intersection is:

    intersection_start = max(A.start, B.start)
    intersection_end   = min(A.end, B.end)

Why?
- The intersection can't start before EITHER interval starts → use max
- The intersection can't end after EITHER interval ends → use min
```

**(Visual: Example.)**

```
A = [5, 10]
B = [8, 12]

intersection_start = max(5, 8) = 8
intersection_end   = min(10, 12) = 10

Intersection = [8, 10] ✓

    [---A---]
        [---B---]
        |--|
        ^  ^
        8  10  ← The overlap region!
```

**Instructor:**
"Here's the clever part: if we compute `max(start)` and `min(end)`, we'll get a valid intersection **only if** `max(start) <= min(end)`. Otherwise, there's no overlap!"

```
A = [0, 2]
B = [5, 8]

intersection_start = max(0, 5) = 5
intersection_end   = min(2, 8) = 2

5 > 2 → No valid intersection! ✗

    [--A--]
              [--B--]
    No overlap!
```

---

## Part 3 – The Two-Pointer Approach

**(Visual: Two pointers on two lists.)**

**Instructor:**
"Now here's the key insight. Since both lists are **sorted by start time**, we can use a **two-pointer technique**!

We have pointer `i` for the first list and pointer `j` for the second list."

```
firstList:  [[0,2], [5,10], [13,23], [24,25]]
                ↑
                i

secondList: [[1,5], [8,12], [15,24], [25,26]]
                ↑
                j
```

**Instructor:**
"At each step:
1. Check if the two current intervals intersect
2. If yes, add the intersection to our result
3. **Move the pointer** whose interval ends first (it can't intersect with anything else from the other list)"

```
The Key Decision: Which pointer to move?

If firstList[i].end < secondList[j].end:
    → Move i (first interval ends sooner, exhausted its potential)
Else:
    → Move j (second interval ends sooner)
```

**(Visual: Why this works.)**

```
Example:
A = [0, 2]  ← ends at 2
B = [1, 5]  ← ends at 5

After finding intersection [1,2]:
- A ends at 2, which is BEFORE B ends (5)
- A can't possibly intersect with any FUTURE intervals in secondList
  (they all start after 1, and A already ends at 2)
- So move pointer i to the next interval in firstList!
```

---

## Part 4 – The 3-Step Pattern

**(Visual: Pattern summary card.)**

**Instructor:**
"Let's crystallize this into our **3-step pattern**:"

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL INTERSECTION PATTERN                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. USE TWO POINTERS (i for list1, j for list2)             │
│                                                             │
│  2. FIND INTERSECTION at each step:                         │
│     start = max(A.start, B.start)                           │
│     end = min(A.end, B.end)                                 │
│     if start <= end: add [start, end] to result             │
│                                                             │
│  3. MOVE THE POINTER whose interval ends first:             │
│     if A.end < B.end: i++                                   │
│     else: j++                                               │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY INSIGHT: The interval that ends first can't intersect  │
│  with future intervals from the other list.                 │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – Step-by-Step Walkthrough

**(Visual: Full walkthrough with the example.)**

**Instructor:**
"Let's trace through our example step by step."

```
firstList  = [[0,2], [5,10], [13,23], [24,25]]
secondList = [[1,5], [8,12], [15,24], [25,26]]
result = []
```

### Step 1: i=0, j=0
```
A = [0, 2], B = [1, 5]

start = max(0, 1) = 1
end = min(2, 5) = 2
1 <= 2? YES → Add [1, 2]

A.end(2) < B.end(5)? YES → i++

result = [[1, 2]]
```

### Step 2: i=1, j=0
```
A = [5, 10], B = [1, 5]

start = max(5, 1) = 5
end = min(10, 5) = 5
5 <= 5? YES → Add [5, 5]

A.end(10) < B.end(5)? NO → j++

result = [[1, 2], [5, 5]]
```

### Step 3: i=1, j=1
```
A = [5, 10], B = [8, 12]

start = max(5, 8) = 8
end = min(10, 12) = 10
8 <= 10? YES → Add [8, 10]

A.end(10) < B.end(12)? YES → i++

result = [[1, 2], [5, 5], [8, 10]]
```

### Step 4: i=2, j=1
```
A = [13, 23], B = [8, 12]

start = max(13, 8) = 13
end = min(23, 12) = 12
13 <= 12? NO → No intersection

A.end(23) < B.end(12)? NO → j++

result = [[1, 2], [5, 5], [8, 10]]
```

### Step 5: i=2, j=2
```
A = [13, 23], B = [15, 24]

start = max(13, 15) = 15
end = min(23, 24) = 23
15 <= 23? YES → Add [15, 23]

A.end(23) < B.end(24)? YES → i++

result = [[1, 2], [5, 5], [8, 10], [15, 23]]
```

### Steps 6-7 (continuing...)
```
Final result: [[1,2], [5,5], [8,10], [15,23], [24,24], [25,25]]
```

---

## Part 6 – The Complete Python Solution

**(Visual: Code on screen.)**

**Instructor:**
"Here's the complete solution:"

```python
def intervalIntersection(firstList, secondList):
    result = []
    i, j = 0, 0
    
    while i < len(firstList) and j < len(secondList):
        # Get current intervals
        A = firstList[i]
        B = secondList[j]
        
        # Find potential intersection
        start = max(A[0], B[0])
        end = min(A[1], B[1])
        
        # Check if valid intersection exists
        if start <= end:
            result.append([start, end])
        
        # Move the pointer whose interval ends first
        if A[1] < B[1]:
            i += 1
        else:
            j += 1
    
    return result
```

---

## Part 7 – The Reusable Template

**(Visual: Template card.)**

**Instructor:**
"Here's your reusable template for interval intersection problems:"

```python
def interval_intersection(list1, list2):
    """
    Find all intersections between two sorted lists of intervals.
    
    Args:
        list1: First list of intervals [[start, end], ...]
        list2: Second list of intervals [[start, end], ...]
    
    Returns:
        List of intersection intervals
    """
    result = []
    i, j = 0, 0
    
    while i < len(list1) and j < len(list2):
        # Step 1: Get current intervals
        A = list1[i]
        B = list2[j]
        
        # Step 2: Calculate potential intersection
        #         (works for any overlap scenario)
        start = max(A[0], B[0])  # Latest start
        end = min(A[1], B[1])    # Earliest end
        
        # Step 3: Add if valid intersection
        if start <= end:
            result.append([start, end])
        
        # Step 4: Advance pointer with smaller end
        #         (that interval can't have more intersections)
        if A[1] < B[1]:
            i += 1
        else:
            j += 1
    
    return result
```

---

## Part 8 – Understanding the Intersection Condition

**(Visual: Why max/min works.)**

**Instructor:**
"Let's understand why `max(start)` and `min(end)` gives us the intersection:"

```
There are 6 ways two intervals can be positioned:

1. A completely before B (no overlap):
   [--A--]
              [--B--]
   max(start) = B.start, min(end) = A.end
   B.start > A.end → No intersection!

2. A overlaps start of B:
   [----A----]
        [----B----]
   Intersection: [B.start, A.end]

3. A completely contains B:
   [--------A--------]
       [--B--]
   Intersection: [B.start, B.end] = B itself!

4. B completely contains A:
       [--A--]
   [--------B--------]
   Intersection: [A.start, A.end] = A itself!

5. A overlaps end of B:
        [----A----]
   [----B----]
   Intersection: [A.start, B.end]

6. A completely after B (no overlap):
              [--A--]
   [--B--]
   max(start) = A.start, min(end) = B.end
   A.start > B.end → No intersection!
```

**Instructor:**
"The beauty of `max(start), min(end)` is that it handles ALL these cases automatically! We just check if `start <= end` to know if there's a valid intersection."

---

## 💡 Tip for Students

**Instructor:**
"The intersection formula `max(A.start, B.start), min(A.end, B.end)` with the check `start <= end` is simple and handles all overlap scenarios. 

If you're ever unsure, just remember:
- **max of starts**: The intersection can't start before either interval
- **min of ends**: The intersection can't extend past either interval
- **start <= end**: This tells you if there's actually an overlap!"

---

## Part 9 – Other Problems Using This Pattern

**(Visual: Related problems.)**

**Instructor:**
"Here are other problems that use the Interval Intersection pattern:"

| Problem | Description |
|---------|-------------|
| **LC 986 - Interval List Intersections** | The classic problem — find all intersections |
| **LC 1229 - Meeting Scheduler** | Find common free time slots between two people |
| **Finding overlapping events** | Any problem with two sorted event lists |

---

## Part 10 – Pattern Recognition Clues

**(Visual: When to use this pattern.)**

**Instructor:**
"Use this pattern when you see:

✅ **Two sorted lists** of intervals
✅ Need to find **common/shared** time periods
✅ Both lists are **already sorted** by start time
✅ Looking for **where intervals from different lists overlap**

**Key difference from Merge Overlapping:**
- Merge: Combine intervals within ONE list
- Intersection: Find overlaps BETWEEN TWO lists"

---

## Part 11 – Complexity Analysis

**Instructor:**
"Let's analyze the complexity:"

```
Time Complexity: O(m + n)
- m = length of firstList
- n = length of secondList
- Each pointer moves forward exactly once per interval
- No nested loops!

Space Complexity: O(1) extra space
- We only use two pointers
- Result space is O(min(m, n)) for the intersections
```

---

## Part 12 – Common Mistakes

**(Visual: Pitfalls to avoid.)**

**Instructor:**
"Watch out for these common mistakes:"

```
❌ Mistake 1: Forgetting that single-point intersections are valid
   [1, 5] ∩ [5, 10] = [5, 5] ✓ (start == end is valid!)

❌ Mistake 2: Wrong pointer advancement
   Always move the pointer whose interval ENDS first, not STARTS first!

❌ Mistake 3: Using <= instead of < for pointer comparison
   if A[1] < B[1]:   ✓ Correct
   if A[1] <= B[1]:  Also works (just need consistency)

❌ Mistake 4: Assuming intervals can't touch
   Touching intervals DO intersect at a single point!
```

---

## Part 13 – Closing

**Instructor:**
"And that's the Interval Intersection pattern!

**Key takeaways:**
1. Use **two pointers** for two sorted lists
2. **max(starts), min(ends)** gives the intersection
3. **Move the pointer** whose interval ends first
4. Check `start <= end` to validate intersection

Practice with LC 986 and LC 1229, and you'll master this pattern!"

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│           INTERVAL INTERSECTION CHEAT SHEET                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  FORMULA:                                                   │
│    start = max(A.start, B.start)                            │
│    end = min(A.end, B.end)                                  │
│    if start <= end → valid intersection!                    │
│                                                             │
│  ALGORITHM:                                                 │
│    i, j = 0, 0                                              │
│    while i < len(list1) and j < len(list2):                 │
│        find intersection                                    │
│        if valid: add to result                              │
│        if list1[i].end < list2[j].end: i++                  │
│        else: j++                                            │
│                                                             │
│  TIME: O(m + n)    SPACE: O(1)                              │
│                                                             │
│  KEY: Move pointer whose interval ends first!               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

