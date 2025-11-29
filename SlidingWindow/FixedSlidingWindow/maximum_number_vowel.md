Here is the lecture script for **Lesson 3: Maximum Number of Vowels in a Substring**.

It follows the "Fixed Sliding Window" pattern exactly, showing students that the same template works on strings and with counting operations.

---

# 🎬 Tutorial Script: Maximum Number of Vowels (The "Counting" Variation)

---

## Part 0 – The Hook & The Challenge

**(Instructor on camera)**

**Instructor:**
"Welcome back! We've seen the Fixed Sliding Window pattern work for:
- Calculating sums (integers)
- Finding duplicates (sets)

Today, we're going to tackle a problem that works on **strings** instead of arrays, and we're going to **count** something instead of summing.

But guess what? It's still the **exact same 5-step pattern**!"

---

## Part 1 – The Scenario

**(Visual: The Problem Statement)**

```text
s = "abciiidef"
k = 3
```

**Instructor:**
"Here is the problem (LeetCode 1456):
Given a string `s` and an integer `k`, return the **maximum number of vowel letters** in any substring of `s` with length `k`.

Vowel letters in English are: `'a'`, `'e'`, `'i'`, `'o'`, and `'u'`.

In plain English?
We want to slide a window of size `k` across the string and find which window has the most vowels."

**(Visual: Example with highlighting)**

**Instructor:**
"Let's look at our example: `s = "abciiidef"`, `k = 3`.

- Window 1: `"abc"` → 1 vowel (`a`)
- Window 2: `"bci"` → 1 vowel (`i`)
- Window 3: `"cii"` → 2 vowels (`i`, `i`)
- Window 4: `"iii"` → **3 vowels** (`i`, `i`, `i`) ← This is the maximum!
- Window 5: `"iid"` → 2 vowels (`i`, `i`)
- And so on...

So the answer is **3**."

---

## Part 2 – Pattern Recognition

**Instructor:**
"Let's check our **3 Clues** to see if this is a Sliding Window problem."

**(Visual: The 3 Clues Checklist)**

1. **Linear Data Structure?**
   * "Yes, it's a **string** (which is just an array of characters)."

2. **Contiguous?**
   * "Yes. We're looking for a **substring** of length `k`. Substrings are contiguous by definition."

3. **Fixed Size k?**
   * "Yes, `k` is given explicitly: 'substring of length k'."

**Instructor:**
"All green lights. This is a **Fixed Sliding Window** problem."

---

## Part 3 – Adapting the Template (The "Operation" Swap)

**Instructor:**
"Now, here's the key insight.

In the 'Max Sum' problem, our window measured a **sum**.
We did: `current_sum += arr[i]` and `current_sum -= leaving`.

In *this* problem, our window measures a **count of vowels**.
We do: `if char is vowel: count += 1` and `if leaving is vowel: count -= 1`.

The data structure stays the same (an integer), but the **operation** changes from addition/subtraction to conditional counting."

**Instructor:**
"So we keep the **same sliding motion**, but instead of:
- `current_sum += entering`
- `current_sum -= leaving`

We do:
- `if entering is vowel: vowel_count += 1`
- `if leaving is vowel: vowel_count -= 1`"

---

## Part 4 – Walking Through the 5 Steps

**(Visual: The String `"abciiidef"`, `k = 3`)**

**Instructor:**
"Let's trace this step by step."

**Step 1: Compute the First Window**
"We look at the first `k` characters: `"abc"`.
- Count vowels: `a` is a vowel → count = 1
- Window: `"abc"`, vowel_count = 1"

**Step 2: Store Best**
"max_vowels = 1"

**Step 3: Slide the Window**
"Now we start our loop from index `k` (index 3)."

* **Iteration 1 (Index 3, Character 'i'):**
  * **Entering:** `'i'`. Is `'i'` a vowel? **Yes!**
  * **Add:** vowel_count += 1 → vowel_count = 2
  * **Leaving:** `'a'` (from index 0). Is `'a'` a vowel? **Yes!**
  * **Subtract:** vowel_count -= 1 → vowel_count = 1
  * Wait, that's wrong! Let me recalculate:
  * Window was `"abc"` (vowel_count = 1)
  * New window: `"bci"` (vowel_count = 1: `i` is vowel)
  * Actually: leaving `'a'` (-1), entering `'i'` (+1) → still 1
  * But wait, let me check: `"bci"` has `i` → 1 vowel. Correct.

* **Iteration 2 (Index 4, Character 'i'):**
  * **Entering:** `'i'`. Is `'i'` a vowel? **Yes!**
  * **Add:** vowel_count += 1 → vowel_count = 2
  * **Leaving:** `'b'` (from index 1). Is `'b'` a vowel? **No.**
  * **Subtract:** vowel_count -= 0 (no change)
  * Window: `"cii"`, vowel_count = 2
  * max_vowels = max(1, 2) = 2

* **Iteration 3 (Index 5, Character 'i'):**
  * **Entering:** `'i'`. Is `'i'` a vowel? **Yes!**
  * **Add:** vowel_count += 1 → vowel_count = 3
  * **Leaving:** `'c'` (from index 2). Is `'c'` a vowel? **No.**
  * **Subtract:** vowel_count -= 0 (no change)
  * Window: `"iii"`, vowel_count = 3
  * max_vowels = max(2, 3) = **3** ← Maximum found!

* **Iteration 4 (Index 6, Character 'i'):**
  * **Entering:** `'d'`. Is `'d'` a vowel? **No.**
  * **Add:** vowel_count += 0 (no change)
  * **Leaving:** `'i'` (from index 3). Is `'i'` a vowel? **Yes!**
  * **Subtract:** vowel_count -= 1 → vowel_count = 2
  * Window: `"iid"`, vowel_count = 2

* **Iteration 5 (Index 7, Character 'e'):**
  * **Entering:** `'e'`. Is `'e'` a vowel? **Yes!**
  * **Add:** vowel_count += 1 → vowel_count = 3
  * **Leaving:** `'i'` (from index 4). Is `'i'` a vowel? **Yes!**
  * **Subtract:** vowel_count -= 1 → vowel_count = 2
  * Window: `"ide"`, vowel_count = 2

* **Iteration 6 (Index 8, Character 'f'):**
  * **Entering:** `'f'`. Is `'f'` a vowel? **No.**
  * **Add:** vowel_count += 0 (no change)
  * **Leaving:** `'i'` (from index 5). Is `'i'` a vowel? **Yes!**
  * **Subtract:** vowel_count -= 1 → vowel_count = 1
  * Window: `"def"`, vowel_count = 1

**Result:** max_vowels = 3

---

## Part 5 – The Python Solution

**Instructor:**
"Let's look at the code. Notice how similar it is to the Max Sum code!"

**(Visual: Python code.)**

```python
def max_vowels(s, k):
    """
    Return the maximum number of vowels in any substring of length k.
    Example: s = "abciiidef", k = 3  ->  3
    """
    vowels = {'a', 'e', 'i', 'o', 'u'}
    
    # Step 1: Compute the first window [0 .. k-1]
    vowel_count = 0
    for i in range(k):
        if s[i] in vowels:
            vowel_count += 1
    
    # Step 2: Store initial best
    max_vowels = vowel_count
    
    # Step 3: Slide the window across the rest of the string
    for i in range(k, len(s)):
        entering = s[i]        # character ENTERING on the right
        leaving = s[i - k]     # character LEAVING on the left
        
        # Step 4: Update the window count
        if entering in vowels:
            vowel_count += 1
        if leaving in vowels:
            vowel_count -= 1
        
        # Step 5: Update the best answer
        max_vowels = max(max_vowels, vowel_count)
    
    return max_vowels
```

**Instructor:**
"I wrote this to strictly follow our **5-step pattern**:"

1. **Step 1 (First Window):** We count vowels in the first `k` characters.
2. **Step 2 (Store Best):** We save the initial count as `max_vowels`.
3. **Step 3 (Slide Loop):** We start from index `k`.
4. **Step 4 (Update Window):** For each step:
   * If the **entering** character is a vowel → add 1
   * If the **leaving** character is a vowel → subtract 1
5. **Step 5 (Update Best):** We compare and update `max_vowels`.

"It's the exact same **Add/Remove** rhythm, just with conditional checks!"

---

## Part 6 – Connection to Template

**(Visual: Highlighting the mapping)**

**Instructor:**
"Let's map it to our 'Generic Template' concepts:"

1. **What are we measuring?**
   * Template: `window_value`
   * Here: `vowel_count` (Count of vowels)

2. **How do we update?**
   * Template: `+= entering`, `-= leaving`
   * Here: `if entering in vowels: += 1`, `if leaving in vowels: -= 1`

3. **What is "Best"?**
   * Template: `max(best, current)`
   * Here: `max(max_vowels, vowel_count)` (Same!)

**Instructor:**
"Notice that:
- The **data structure** is the same (integer)
- The **sliding motion** is identical
- Only the **operation** changes (conditional counting vs. direct addition)

This proves the pattern is flexible!"

---

## Part 7 – Key Takeaways

**Instructor:**
"This problem teaches us an important lesson:

The Fixed Sliding Window pattern works on:
- ✅ Arrays (numbers)
- ✅ Strings (characters)
- ✅ Any linear data structure

The operations can be:
- ✅ Direct addition/subtraction (`sum += x`)
- ✅ Conditional counting (`if x in set: count += 1`)
- ✅ Set operations (`.add()`, `.remove()`)
- ✅ Any operation that can be updated incrementally

**The core principle remains:**
> **Add the incoming element's contribution, remove the outgoing element's contribution.**"

---

## Part 8 – Wrap Up

**Instructor:**
"Today we solved a string problem using the exact same pattern we used for arrays.

**Homework:**
Try solving LeetCode 1456 on your own. Pay attention to:
- How we check if a character is a vowel
- How we update the count conditionally
- How the sliding motion stays the same

**Next Steps:**
Practice with more sliding window problems. The more you see the pattern in action, the easier it becomes to recognize and apply.

Thanks for watching, and I'll see you in the next lesson!"

---

