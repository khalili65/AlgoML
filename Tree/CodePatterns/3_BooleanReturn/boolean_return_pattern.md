# 🎬 Tutorial Script: Tree Code Pattern 3 — Boolean Return (Validate Condition)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Welcome back! Today we're learning the **third tree code pattern**: **Boolean Return**.

This pattern is all about **validation** — checking if a tree satisfies some condition. Does every node meet a requirement? Is the tree valid in some way?

Let's dive in with a concrete example!"

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: two binary trees.)**

```
Tree p:         Tree q:
    1               1
   / \             / \
  2   3           2   3

Problem: Are these two trees IDENTICAL?
(Same structure AND same values)

Expected output: True
```

**Instructor:**
"Here we have two trees. We want to check if they're exactly the same — same shape, same values at each position.

Looking at them, they ARE identical. But how do we check this systematically?"

---

## Part 1.5 – How This Pattern Differs from Others

**(Visual: Comparison.)**

**Instructor:**
"Let me show you how Boolean Return differs from the other patterns:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN COMPARISON:                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   VOID RETURN:                                              │
│   → Traverse and COLLECT values                             │
│   → Returns nothing, fills external list                    │
│                                                             │
│   SINGLE VALUE RETURN:                                      │
│   → COMPUTE a value (depth, sum, count)                     │
│   → Returns a number                                        │
│                                                             │
│   BOOLEAN RETURN:                                           │
│   → VALIDATE a condition across the tree                    │
│   → Returns True/False                                      │
│   → Usually: "Is EVERY node valid?" or "Do ALL match?"      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Key insight:**
```
Boolean Return has a special property:

If ANY node fails the condition → entire tree is False
ALL nodes must pass → tree is True

This is often expressed as: condition AND left AND right
```

---

## Part 2 – The "Aha!" Moment (Noticing the Pattern)

**Instructor:**
"For 'Same Tree', two trees are identical if:

1. Both roots are null (both empty) → True
2. One is null, other isn't → False
3. Both exist but values differ → False
4. Both exist, values same → check left AND right subtrees!"

```
┌─────────────────────────────────────────────────────────────┐
│   SAME TREE LOGIC:                                          │
│                                                             │
│   isSame(p, q) =                                            │
│       if both null     → True                               │
│       if one null      → False                              │
│       if p.val ≠ q.val → False                              │
│       else             → isSame(p.left, q.left) AND         │
│                          isSame(p.right, q.right)           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**The pattern:**
```
Boolean Return follows this structure:

1. Check BASE CASES (return True or False immediately)
2. Check CURRENT NODE condition (return False if fails)
3. RECURSIVELY check children with AND
```

---

## Part 2.5 – Do We Need a Helper Function?

**(Visual: Helper vs no helper.)**

**Instructor:**
"One thing you'll notice about Boolean Return: we often **don't need a helper function**. We just call the main function recursively!"

```
┌─────────────────────────────────────────────────────────────┐
│   WHEN DO YOU NEED A HELPER FUNCTION?                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   NO HELPER NEEDED:                                         │
│   When the function signature has all info you need         │
│   → Just call the function recursively on itself            │
│                                                             │
│   HELPER NEEDED:                                            │
│   When you need EXTRA PARAMETERS that aren't in the         │
│   original function signature                               │
│   → Create helper to pass additional state/constraints      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Example: Same Tree (NO helper needed)**
```python
# The function signature already has everything: (p, q)
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q or p.val != q.val:
        return False
    
    # Just call ITSELF recursively — no helper!
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**Example: Valid BST (HELPER needed)**
```python
# Original signature: isValidBST(root)
# But we NEED min/max constraints → create helper!

def isValidBST(root):
    def validate(node, min_val, max_val):  # ← Helper with extra params
        if not node:
            return True
        if not (min_val < node.val < max_val):
            return False
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))
    
    return validate(root, float('-inf'), float('inf'))
```

**Quick Reference:**
```
┌────────────────────┬─────────────────────┬────────────────┐
│ Problem            │ Extra Params?       │ Helper?        │
├────────────────────┼─────────────────────┼────────────────┤
│ Same Tree          │ No (just p, q)      │ ❌ No helper   │
│ Symmetric Tree     │ No (left, right)    │ ❌ No helper   │
│ Valid BST          │ Yes (min, max)      │ ✅ Need helper │
│ Balanced Tree      │ Maybe (track height)│ Depends        │
└────────────────────┴─────────────────────┴────────────────┘

SIMPLE RULE:
Need extra info down the recursion? → Helper function
Original signature has everything? → Call itself directly
```

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **Boolean Return** template:"

```python
def validate(node, ...params):
    # 1) BASE CASE(S): When to return True/False immediately?
    if not node:
        return True  # Usually True (empty tree is valid)
    
    # 2) CHECK CURRENT NODE: Does this node satisfy the condition?
    if not CONDITION(node, ...params):
        return False  # Fail fast!
    
    # 3) RECURSIVE CHECK: Do ALL children satisfy?
    left_valid = validate(node.left, ...updated_params)
    right_valid = validate(node.right, ...updated_params)
    
    # 4) COMBINE with AND: All must be valid!
    return left_valid and right_valid
```

---

## Part 4 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the 4 steps:"

```
┌─────────────────────────────────────────────────────────────┐
│           BOOLEAN RETURN PATTERN - 4 STEPS                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) BASE CASE: if not node: return True (usually)           │
│     → Empty tree/subtree is often "valid by default"        │
│     → Sometimes you need different base cases               │
│                                                             │
│  2) CHECK CURRENT NODE: if not condition: return False      │
│     → Check if THIS node satisfies the requirement          │
│     → Return False immediately if it fails (FAIL FAST)      │
│                                                             │
│  3) RECURSIVE CHECK: Check left and right subtrees          │
│     → Pass any updated constraints/parameters               │
│                                                             │
│  4) COMBINE WITH AND: return left AND right                 │
│     → ALL parts must be valid for whole tree to be valid    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – The Key: "Fail Fast" with AND

**(Visual: AND logic.)**

**Instructor:**
"The most important aspect of Boolean Return is the **AND logic**:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHY "AND"?                                                │
│                                                             │
│   For a tree to be VALID:                                   │
│   → Current node must be valid                              │
│   → AND left subtree must be valid                          │
│   → AND right subtree must be valid                         │
│                                                             │
│   If ANY part is False → whole thing is False               │
│                                                             │
│   True AND True AND True = True                             │
│   True AND False AND True = False  ← One failure kills it!  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Fail Fast Optimization:**
```python
# These are equivalent, but second is more efficient:

# Version 1: Check both, then AND
left = validate(node.left)
right = validate(node.right)
return left and right

# Version 2: Short-circuit (fail fast)
return validate(node.left) and validate(node.right)

# In Version 2, if left is False, we don't even check right!
# Python's "and" short-circuits automatically.
```

---

## Part 6 – Filled-In Example: Same Tree

**(Visual: Complete code.)**

**Instructor:**
"Let's fill in the template for Same Tree:"

```python
def isSameTree(p, q):
    # BASE CASE 1: Both empty → same!
    if not p and not q:
        return True
    
    # BASE CASE 2: One empty, one not → different!
    if not p or not q:
        return False
    
    # CHECK CURRENT: Values must match
    if p.val != q.val:
        return False
    
    # RECURSIVE: Both subtrees must be same
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**Even more compact:**
```python
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q or p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

---

## Part 7 – Walkthrough: Same Tree

**(Visual: Step-by-step trace.)**

```
Tree p:       Tree q:
    1             1
   / \           / \
  2   3         2   3

isSameTree(p=1, q=1)
│ Both exist, p.val(1) == q.val(1) ✓
│
├─ isSameTree(p=2, q=2)
│  │ Both exist, p.val(2) == q.val(2) ✓
│  │
│  ├─ isSameTree(None, None) → True (both empty)
│  └─ isSameTree(None, None) → True (both empty)
│  │
│  return True AND True = True
│
└─ isSameTree(p=3, q=3)
   │ Both exist, p.val(3) == q.val(3) ✓
   │
   ├─ isSameTree(None, None) → True
   └─ isSameTree(None, None) → True
   │
   return True AND True = True

return True AND True = True ✓

ANSWER: True (trees are identical)
```

---

## Part 8 – Walkthrough: Different Trees

```
Tree p:       Tree q:
    1             1
   / \           / \
  2   3         2   4   ← Different!

isSameTree(p=1, q=1)
│ Both exist, p.val(1) == q.val(1) ✓
│
├─ isSameTree(p=2, q=2) → True (subtrees match)
│
└─ isSameTree(p=3, q=4)
   │ Both exist, but p.val(3) ≠ q.val(4) ✗
   │ return False immediately!

return True AND False = False ✗

ANSWER: False (trees are different)
```

---

## Part 9 – Common Boolean Return Problems

**(Visual: Different problems, same pattern.)**

**Instructor:**
"Many problems follow this pattern with different conditions:"

```
┌────────────────────┬─────────────────────────────────────────┐
│ Problem            │ Condition to Check                      │
├────────────────────┼─────────────────────────────────────────┤
│ Same Tree          │ p.val == q.val                          │
│                    │ (compare two trees)                     │
├────────────────────┼─────────────────────────────────────────┤
│ Symmetric Tree     │ left.val == right.val                   │
│                    │ (compare mirror positions)              │
├────────────────────┼─────────────────────────────────────────┤
│ Valid BST          │ min < node.val < max                    │
│                    │ (value within valid range)              │
├────────────────────┼─────────────────────────────────────────┤
│ Balanced Tree      │ |left_height - right_height| <= 1      │
│                    │ (heights differ by at most 1)           │
├────────────────────┼─────────────────────────────────────────┤
│ Subtree of Another │ isSameTree(s, t) at some node           │
│                    │ (find matching subtree)                 │
└────────────────────┴─────────────────────────────────────────┘
```

---

## Part 10 – Passing Constraints Down

**(Visual: Parameters.)**

**Instructor:**
"Sometimes you need to pass constraints down the recursion:"

```python
# VALID BST: Each node must be within a valid range

def isValidBST(root, min_val=-inf, max_val=inf):
    if not root:
        return True
    
    # CHECK: Is current node in valid range?
    if not (min_val < root.val < max_val):
        return False
    
    # RECURSIVE: Update constraints for children
    # Left child must be < current value
    # Right child must be > current value
    return (isValidBST(root.left, min_val, root.val) and
            isValidBST(root.right, root.val, max_val))
```

**The pattern with constraints:**
```
┌─────────────────────────────────────────────────────────────┐
│   PASSING CONSTRAINTS:                                      │
│                                                             │
│   validate(node, constraint)                                │
│       if not node: return True                              │
│       if not valid(node, constraint): return False          │
│       return validate(left, updated_constraint) AND         │
│              validate(right, updated_constraint)            │
│                                                             │
│   The constraint UPDATES as you go down!                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 11 – Boolean Return vs. Single Value Return

**(Visual: When to use which.)**

**Instructor:**
"Sometimes the line between Boolean and Single Value is blurry:"

```
┌─────────────────────────────────────────────────────────────┐
│   BOOLEAN RETURN                SINGLE VALUE RETURN         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   "Is the tree X?"              "What is the X of tree?"    │
│   "Is it valid?"                "Compute a value"           │
│   "Do all nodes satisfy?"       "Aggregate from children"   │
│                                                             │
│   Returns: True/False           Returns: number             │
│   Combines with: AND            Combines with: +, max, etc  │
│                                                             │
│   Example:                      Example:                    │
│   "Is tree balanced?"           "What is max depth?"        │
│   "Is tree a valid BST?"        "What is sum of nodes?"     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Note:** Some problems like "Is Balanced?" can be solved either way:
- Boolean Return: Check at each node
- Single Value Return: Compute heights and check difference

---

## Part 12 – When to Use This Pattern

**(Visual: Recognition clues.)**

**Instructor:**
"How do you recognize a Boolean Return problem?"

```
USE BOOLEAN RETURN WHEN:

✅ Problem asks "Is the tree...?" or "Are the trees...?"
✅ You need to VALIDATE a condition across ALL nodes
✅ Keywords:
   • "valid" / "validate"
   • "same" / "identical"
   • "symmetric" / "mirror"
   • "balanced"
   • "is X?"

THE KEY PATTERN:
→ If ANY node fails → return False
→ Only if ALL nodes pass → return True
→ Use AND to combine results
```

---

## Part 13 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node at most once
  - Might exit early if condition fails (fail fast)

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 14 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Using OR instead of AND
   return isValid(left) or isValid(right)  ← WRONG!
   return isValid(left) and isValid(right) ← RIGHT!
   
   All parts must be valid, not just one!

❌ Mistake 2: Forgetting base cases
   Two trees comparison needs multiple base cases:
   - Both null → True
   - One null → False
   - Both exist → check values

❌ Mistake 3: Not passing updated constraints
   For Valid BST, must update min/max for each child!

❌ Mistake 4: Checking only current node
   Must recursively check ALL descendants!
```

---

## Part 15 – LeetCode Problems Using This Pattern

| Problem | Condition | Special Notes |
|---------|-----------|---------------|
| LC 100 - Same Tree | p.val == q.val | Compare two trees |
| LC 101 - Symmetric Tree | left.val == right.val | Mirror comparison |
| LC 98 - Valid BST | min < val < max | Pass range constraints |
| LC 110 - Balanced Tree | height diff <= 1 | Can also use Single Value |
| LC 572 - Subtree of Another | isSameTree somewhere | Combine with search |

---

## Part 16 – Summary

**Instructor:**
"Let's recap the Boolean Return pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           BOOLEAN RETURN PATTERN SUMMARY                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    def validate(node, ...constraints):                      │
│        if not node: return True                             │
│        if not CONDITION: return False                       │
│        return validate(left, ...) AND validate(right, ...)  │
│                                                             │
│  KEY INSIGHT:                                               │
│    ALL nodes must pass for True                             │
│    ANY node failing means False                             │
│    Use AND to combine (fail fast!)                          │
│                                                             │
│  USE WHEN:                                                  │
│    "Is the tree valid/same/symmetric/balanced?"             │
│    Need to validate a condition across all nodes            │
│                                                             │
│  TEMPLATE:                                                  │
│    1. BASE: Empty usually returns True                      │
│    2. CHECK: Current node condition                         │
│    3. RECURSE: Check both children                          │
│    4. COMBINE: left AND right                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
# BOOLEAN RETURN TEMPLATE
def validate(node, ...params):
    # Base case
    if not node:
        return True
    
    # Check current node
    if not CONDITION(node):
        return False
    
    # Recursively check children with AND
    return validate(node.left, ...) and validate(node.right, ...)


# EXAMPLE: Same Tree
def isSameTree(p, q):
    if not p and not q: return True
    if not p or not q: return False
    if p.val != q.val: return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)


# EXAMPLE: Valid BST (with constraints)
def isValidBST(root, min_val=-inf, max_val=inf):
    if not root: return True
    if not (min_val < root.val < max_val): return False
    return (isValidBST(root.left, min_val, root.val) and
            isValidBST(root.right, root.val, max_val))
```


