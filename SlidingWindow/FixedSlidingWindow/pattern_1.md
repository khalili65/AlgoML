Here’s a complete, ready-to-teach script that:

* Starts example-first (your style)
* Introduces the 5-step pattern
* Shows the **filled-in Python solution**
* Then shows the **reusable Python template** and maps them together

You can copy-paste this as your lecture script.

---

# 🎬 Tutorial Script: Fixed Sliding Window Pattern (Example → Pattern → Template)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back. I’m really glad you’re here.

Today we’re going to learn one of the most powerful patterns for array and string problems: the **fixed sliding window**.

Instead of starting with a definition, I want to start with a very concrete example, the way your brain naturally thinks, and then we’ll turn it into a reusable template you can use in many interview questions."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: array and k.)**

Show on screen:

```text
arr = [2, 5, 1, 8, 2, 9]
k = 3
```

**Instructor:**
"Here’s our problem:

> We have this list of numbers: `2, 5, 1, 8, 2, 9`, and we want to find the **maximum sum** of any **3 consecutive numbers**.

So we want all subarrays of size 3, and we want the largest sum."

**Instructor (thinking out loud, brute force way):**

"Let’s solve it the way we’d do it by hand first."

1. "First three numbers: `2 + 5 + 1 = 8`."
2. "Slide one step to the right: `5 + 1 + 8 = 14`. So 14 is better than 8."
3. "Slide again: `1 + 8 + 2 = 11`."
4. "Last one: `8 + 2 + 9 = 19`."

**Instructor:**
"So the answer is **19**, coming from the subarray `[8, 2, 9]`."

**(Short pause.)**

"Now I want you to notice something repetitive in what we just did."

---

## Part 2 – The “Aha!” Moment (Noticing the Waste)

**(Visual highlights the first two windows: `[2, 5, 1]` and `[5, 1, 8]`.)**

**Instructor:**
"Look carefully at the transition from the first window to the second window:

* First window: `[2, 5, 1]` → sum = 8
* Second window: `[5, 1, 8]` → sum = 14

To get 8, we did `2 + 5 + 1`.
To get 14, we did `5 + 1 + 8`."

**(Emphasize the repetition.)**

"We added `5` and `1` **twice**.

In this tiny example it’s fine. But imagine if `k` was 100, or 1,000. You’d be re-adding almost all of the same numbers again and again. That’s a lot of wasted computation."

**Instructor:**
"So here’s the key question:

> When we move the window one step to the right, can we update the sum *without* re-adding everything?"

---

## Part 3 – The Core Idea of the Sliding Window

**(Visual shows `[2, 5, 1]` sliding to `[5, 1, 8]` with arrows.)**

**Instructor:**
"Let’s look at that transition again:

We go from `[2, 5, 1]` to `[5, 1, 8]`.

What changed?"

* "We **lost** the `2` on the left."
* "We **gained** the `8` on the right."
* "The middle part (`5` and `1`) stayed the same."

"So instead of recomputing `5 + 1 + 8` from scratch, we can:

* **Start** from the previous sum (which was 8),
* **Subtract** the number that’s leaving (`2`),
* **Add** the number that’s entering (`8`)."

"So the new sum is:

> `previous_sum - leaving + entering = 8 - 2 + 8 = 14`."

**Instructor (reassuring):**
"If that feels a bit fast, don’t worry. We’re going to repeat this idea a few times.

The big picture is:

> We reuse the previous work instead of starting over."

---

## Part 4 – Turning It into a 5-Step Pattern

**(Visual: simple list of steps.)**

**Instructor:**
"Now that we've seen *why* the sliding window helps, let's turn it into a **5-step core pattern** you can follow.

This is a **pseudo-code template**. The exact implementation might change slightly depending on the problem, but these 5 steps will guide you through almost every fixed sliding window problem."

1. **Compute the first window.**
   "Manually compute the value for the first window. Here, that's the sum of the first `k` elements.
   
   *Note: Sometimes you might check conditions during this phase (like checking for duplicates), but the idea is the same—initialize your window state.*"

2. **Store the best answer so far.**
   "Say `max_sum = current_sum`.
   
   *Note: Some problems might skip this step if you're doing early returns, but the concept of tracking your 'answer' remains.*"

3. **Slide the window across the array.**
   "Start a loop from index `k` to the end.
   
   *Note: The loop might start slightly earlier or later depending on the problem, but the sliding motion is always there.*"

4. **Update the window using just the edges.**
   "For each step:

   * Add the element that **enters** on the right.
   * Subtract the element that **leaves** on the left.
   
   *Note: 'Add' and 'Subtract' might mean different operations (like `.add()` and `.remove()` for sets, or updating a frequency map), but the concept of 'update with entering, remove leaving' is universal.*"

5. **Update the best answer.**
   "If the new window is better (larger, smaller, etc.), update your best answer.
   
   *Note: Some problems might return early (like finding a duplicate), but you're still evaluating whether the current window satisfies the condition.*"

**Instructor (summary line):**
"The heart of the sliding window is step 4:

> **Add the incoming element, subtract the outgoing element.**

Remember: This is a **core pattern**. You might adjust the order, combine steps, or use different data structures, but following these 5 steps will help you solve any fixed sliding window problem."

---

## Part 5 – Python Code for Our Example (Filled-In Version)

**Instructor:**
"Let’s write this out in Python for our specific problem:

> 'Maximum sum of any contiguous subarray of size k.'"

**(Visual: Python code.)**

```python
def max_sum_subarray(arr, k):
    """
    Return the maximum sum of any contiguous subarray of size k.
    Example: arr = [2, 5, 1, 8, 2, 9], k = 3  ->  19
    """

    # Step 1: Compute the first window [0 .. k-1]
    current_sum = 0
    for i in range(k):
        current_sum += arr[i]

    # Step 2: Store initial best
    max_sum = current_sum

    # Step 3: Slide the window across the rest of the array
    for i in range(k, len(arr)):
        entering = arr[i]        # new element coming into the window
        leaving = arr[i - k]     # element leaving the window

        # Step 4: Update the window sum
        current_sum += entering
        current_sum -= leaving

        # Step 5: Update the best answer
        if current_sum > max_sum:
            max_sum = current_sum

    return max_sum
```

**Instructor:**
"Notice how short the core logic is. The loop doesn’t recompute the entire sum every time.

Each step just adjusts the sum using the new number coming in and the old number going out."

**Quick check-in question:**
"If I slide one more step to the right, what do I add, and what do I subtract?

If you can answer that, you understand the core of the sliding window."

---

## Part 6 – The Reusable Python Template (Pattern You Can Reuse)

**Instructor:**
"Now I want to do something very important.

I don’t want you to memorize **just** this solution. I want you to have a **template** you can reuse for many fixed-window problems."

**(Visual: title “Fixed Sliding Window – Python Template”)**

**Instructor:**
"Here is a generic Python template for any **fixed-size sliding window** problem.

This is a **core pseudo-code pattern**. For new problems, you will:

* Keep this structure the same,
* But customize what the window is measuring and how you compare answers.
* You might need to adjust the order of operations slightly (e.g., check conditions before adding, or return early).
* The data structure might change (set, map, integer, etc.), but the **sliding motion** stays the same.

The key is: **Follow these 5 steps as your mental framework**, then adapt the implementation details to fit the problem."

**(Visual: Python template code.)**

```python
def solve_with_fixed_window(arr, k):
    """
    Generic fixed-size sliding window template.

    You customize:
      - how you compute window_value for the first window
      - how you update window_value when elements enter/leave
      - how you compare to get best_answer
    """

    # 1) Compute the value of the FIRST window [0 .. k-1]
    window_value = 0
    for i in range(k):
        # VALUE_OF(arr[i]) — define what the window is measuring
        value = arr[i]          # e.g. just arr[i] for sums
        window_value += value

    # 2) Store the best answer so far
    best_answer = window_value

    # 3) Slide the window across the rest of the array
    for i in range(k, len(arr)):

        # Elements entering and leaving the window
        entering = arr[i]        # element ENTERING on the right
        leaving = arr[i - k]     # element LEAVING on the left

        # 4) Update window_value based on entering and leaving elements
        # CHANGE_FROM_ENTERING / CHANGE_FROM_LEAVING
        window_value += entering   # often: + entering
        window_value -= leaving    # often: - leaving

        # 5) Update the best answer if needed
        # BETTER_OF(best_answer, window_value)
        best_answer = max(best_answer, window_value)

    return best_answer
```

**Instructor (explaining the template):**
"When you face a new problem, you **don’t** rewrite everything from scratch.

You just answer three questions:"

1. **What is my window measuring?**

   * That’s how you set `value` for the first window.
   * Sometimes it’s just the number itself (for sums).
   * Sometimes it’s `1` or `0` (e.g., counting something).
   * Sometimes it’s something like length, frequency, etc.

2. **How does the window change when one element enters and one leaves?**

   * That’s how you update `window_value` with `entering` and `leaving`.
   * In our example, it was simply: `+ entering` and `- leaving`.

3. **What does ‘best’ mean?**

   * Are we looking for the **maximum**? Then we use `max(...)`.
   * Are we looking for the **minimum**? Then we use `min(...)`.
   * Or some other comparison depending on the problem.

---

## Part 7 – Connecting the Example to the Template

**Instructor:**
"Let’s connect our concrete example back to this template, so you clearly see how they match."

"For our **max sum subarray of size k** problem, we made the following choices:"

* `value = arr[i]`
  → *The window is just measuring the sum of its elements.*

* `window_value += entering`

* `window_value -= leaving`
  → *We update the sum by adding the new element and subtracting the old one.*

* `best_answer = max(best_answer, window_value)`
  → *We want the maximum sum, so we use `max`.*

**(Visual: side-by-side snippet – template on the left, `max_sum_subarray` on the right.)**

**Instructor:**
"So the **structure** of the code stays the same, but the **meaning** of the window and the way we compare answers depends on the problem."

---

## Part 8 – When Should You Use This Pattern?

**Instructor:**
"Now you might be wondering:

> 'How do I know when to use the fixed sliding window in an interview?'"

"Here are three big clues:"

1. **Linear data structure**

   * The input is usually an **array** or a **string**.

2. **Contiguous part of it**

   * The problem talks about a **subarray** or **substring**.
   * Often they say ‘**contiguous**’. That’s your keyword.

3. **Fixed size k**

   * They give you a specific size:
     ‘subarray of size 3’, ‘substring of length k’, etc.

**Instructor:**
"If you see:

* array or string
* contiguous
* fixed size **k**

then that’s a big green light to use this **fixed sliding window pattern**."

---

## Part 9 – Wrap-Up

**Instructor:**
"Let’s recap what we did today:"

1. "We started with a concrete problem:
   **Maximum sum of any subarray of size k**."

2. "We noticed that recomputing the whole sum every time is wasteful."

3. "We discovered the sliding window trick:

   > **New sum = previous sum − outgoing + incoming**."

4. "We turned that into a 5-step pattern and wrote the actual Python solution."

5. "Then we generalized it into a **Python template** that you can reuse for many fixed-window problems."

**Instructor (closing):**
"If parts of the index logic like `i - k` still feel a bit shaky, that’s completely normal.

In the next lesson, we’ll take a brand-new problem and walk through it step-by-step using this same template, so you can see how to plug any question into this pattern.

Thanks for watching, and I’ll see you in the next video."

---

If you want, next we can write **Lesson 2**: pick a different problem (like “maximum number of 1s in any subarray of size k”) and create a full script that reuses this exact template so your students see the pattern in action on multiple problems.
