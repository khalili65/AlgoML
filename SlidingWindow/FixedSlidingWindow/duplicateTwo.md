Here is the lecture script for **Lesson 2: Contains Duplicate II**.

It is designed to follow your "Fixed Sliding Window" pattern exactly, proving to students that the template works even when the problem looks different (finding a duplicate vs. finding a sum).

---

# 🎬 Tutorial Script: Contains Duplicate II (The "Set" Variation)

---

## Part 0 – The Hook & The Challenge

**(Instructor on camera)**

**Instructor:**
"Welcome back! In our last lesson, we mastered the **Fixed Sliding Window** to calculate sums efficiently.

Today, I have a challenge for you.

I'm going to give you a problem that looks **completely different**.
It’s not asking for a sum. It’s not asking for a maximum or minimum.
It’s asking about *duplicates*.

But... I’m going to show you that we can solve it using the **exact same 5-step pattern** we built in the last video.

The logic doesn't change. Only the data structure changes."

---

## Part 1 – The Scenario

**(Visual: The Problem Statement)**

```text
nums = [1, 2, 3, 1]
k = 3
```

**Instructor:**
"Here is the problem (LeetCode 219):
Given an array of numbers and a number `k`, return `True` if there are **two distinct indices** `i` and `j` such that:

1.  The numbers are the same: `nums[i] == nums[j]`
2.  They are 'close' to each other: `abs(i - j) <= k`

In plain English?
We are looking for a duplicate number, but they have to be within distance `k` of each other."

**(Visual: A timeline or array)**

**Instructor:**
"Imagine you are walking down a street looking at house numbers. You have a short-term memory. You can only remember the last `k` houses you saw.

If you see a house number, and you say, 'Hey! I just saw that number 2 steps ago!' -> That’s a **True**.

If you saw it 100 steps ago, but your memory (`k`) is only 3... you forgot it. That doesn't count."

---

## Part 2 – Pattern Recognition

**Instructor:**
"Let's check our **3 Clues** from the last lesson to see if this is a Sliding Window problem."

**(Visual: The 3 Clues Checklist)**

1.  **Linear Data Structure?**
    *   "Yes, it's an array."
2.  **Contiguous?**
    *   "Yes. The condition `abs(i - j) <= k` literally defines a contiguous range (a window) of size `k`."
3.  **Fixed Size k?**
    *   "Yes, `k` is given explicitly."

**Instructor:**
"All green lights. This is a **Fixed Sliding Window** problem."

---

## Part 3 – Adapting the Template (The "Data Structure" Swap)

**Instructor:**
"Now, here is the most important concept for today.

In the 'Max Sum' problem, our window needed to hold a **Sum**.
So, our state was just an integer: `current_sum`.

In *this* problem, we need to know: **'Have we seen this number recently?'**

An integer can't help us there. We need a **Set** (or a Hash Map).

So, we are going to keep the **same sliding motion**, but instead of adding numbers to a `sum`, we will add them to a `set`."

---

## Part 4 – Walking Through the 5 Steps

**(Visual: The Array `[1, 2, 3, 1, 2, 3]`, `k = 2`)**

**Instructor:**
"Let's trace this with `k=2`. That means our window size is roughly `k` (checking the last 2 items)."

**(Visual Animation: A window frame sliding)**

**Step 1: Compute the First Window**
"We look at the first `k` items (indices 0 to 1).
Window: `[1, 2]`
Set: `{1, 2}`
No duplicates yet."

**Step 3: Slide the Window**
"Now we start our loop from index `2`."

*   **Iteration 1 (Index 2, Value 3):**
    *   **Entering:** `3`. Is `3` in our Set `{1, 2}`? No.
    *   **Add:** Add `3` to the set.
    *   **Leaving:** The window size can't exceed `k`. We need to remove the oldest guy.
    *   **Remove:** Remove `1` (from index 0).
    *   Set is now `{2, 3}`.

*   **Iteration 2 (Index 3, Value 1):**
    *   **Entering:** `1`. Is `1` in our Set `{2, 3}`? No.
    *   **Add:** Add `1`.
    *   **Leaving:** Remove `2` (from index 1).
    *   Set is now `{3, 1}`.

*   **Iteration 3 (Index 4, Value 2):**
    *   **Entering:** `2`. Is `2` in our Set `{3, 1}`? No.
    *   **Add:** Add `2`.
    *   **Leaving:** Remove `3`.
    *   Set is now `{1, 2}`.

*   **Iteration 4 (Index 5, Value 2):**
    *   **Entering:** `2`.
    *   **Check:** Is `2` in our Set `{1, 2}`? **YES!**
    *   **Result:** Return `True` immediately!

---

## Part 5 – The Python Solution

**Instructor:**
"Let's look at the code. You'll be shocked at how similar it is to the Max Sum code."

**(Visual: Split screen. Template on Left, New Code on Right)**

```python
def contains_nearby_duplicate(nums, k):
    # Step 1: Compute the first window [0 ... k-1]
    # (Or smaller if the array is shorter than k)
    window_set = set()
    
    # We only go up to 'k' initially (exclusive) because index 'k' is the start of the SLIDE.
    # But wait! We must check for duplicates even in the first window!
    for i in range(min(k, len(nums))): 
        if nums[i] in window_set:
            return True
        window_set.add(nums[i])

    # Step 2: Store initial best (Not needed here, we just return True if found)

    # Step 3: Slide the window across the rest
    for i in range(min(k, len(nums)), len(nums)):
        entering = nums[i]
        
        # leaving is the element that was at index (i - k - 1) 
        # because the window size effectively is 'k' (checking 'k' previous items)
        # Wait, simpler view: we want to maintain a set of size 'k'.
        # When we are at index 'i', we remove (i-k-1) is confusing?
        # Let's stick to the pure pattern: 
        
        # Logic: Window should hold at most 'k' items BEFORE checking current.
        # So when we add the (k+1)-th item, we remove the 1st item.
        
        leaving_index = i - k
        if leaving_index >= 0:
             leaving = nums[leaving_index]
             # Step 4: Update window (Remove Leaving)
             window_set.remove(leaving)

        # Step 4 (continued): Check Entering
        if entering in window_set:
            return True
            
        # Step 4 (continued): Add Entering
        window_set.add(entering)

    return False
```

**Instructor:**
"I wrote this to strictly follow our **2-phase pattern**:"

1.  **Phase 1 (First Window):** We fill the set with the first `k` numbers. If we find a duplicate already? Boom, return True.
2.  **Phase 2 (The Slide):** We start from index `k`.
    *   We **remove** the element that is falling out of our short-term memory (index `i - k`).
    *   We **check** the new element coming in (`nums[i]`).
    *   We **add** the new element.

"It is the exact same **Add/Remove** rhythm, just with a Set instead of a Sum."

---

## Part 6 – Connection to Template

**(Visual: Highlighting the mapping)**

**Instructor:**
"Let's map it to our 'Generic Template' concepts:"

1.  **What are we measuring?**
    *   Template: `window_value`
    *   Here: `window_set` (Existence)

2.  **How do we update?**
    *   Template: `+= entering`, `-= leaving`
    *   Here: `.add(entering)`, `.remove(leaving)`

3.  **What is "Best"?**
    *   Template: `max(best, current)`
    *   Here: `if nums[i] in set: return True` (Early Exit)

---

## Part 7 – Wrap Up

**Instructor:**
"This problem is a classic example of why learning **Patterns** is better than memorizing code.

Because we understood the **Sliding Window** (Add Incoming, Remove Outgoing), we solved a 'Duplicate' problem just as easily as a 'Sum' problem.

**Homework:**
Try to solve this problem on LeetCode (Problem 219).
Pay attention to the line where you check `if i > k`. That is the moment your window starts sliding!

See you in the next lesson."

