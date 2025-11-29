# 🎬 Tutorial Script: Tree Code Pattern 4 — Tuple Return (Tree DP)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Welcome back! Today we're learning the **fourth tree code pattern**: **Tuple Return**, also known as **Tree DP**.

This is where things get interesting. Sometimes a single value isn't enough — we need to return **multiple values** from each node to make optimal decisions.

Let's start with a problem that makes this clear!"

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: binary tree with values.)**

```
        3
       / \
      2   3
       \   \
        3   1

Problem: House Robber III
- Each node has a value (money in that house)
- You CAN'T rob two adjacent houses (parent-child)
- Find the MAXIMUM money you can rob

Example paths:
- Rob nodes [3, 3, 1]: Can't! 3 (root) and 3 (right child) are adjacent
- Rob nodes [2, 3, 1]: Can't! 2 and 3 are adjacent  
- Rob nodes [3 (root)]: Get 3
- Rob nodes [2, 3 (left-right grandchildren), 1]: Can we do better?
```

**Instructor:**
"This problem is tricky because at each node, we have TWO choices:
1. **Rob this node** — then we CAN'T rob its children
2. **Don't rob this node** — then we CAN rob its children

To make the best decision, we need to know BOTH possibilities from each child!"

---

## Part 1.5 – Why Single Value Isn't Enough

**(Visual: Comparison.)**

**Instructor:**
"Let me show you why we need a tuple, not a single value:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHY SINGLE VALUE FAILS:                                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   If child just returns "max money from my subtree"...      │
│   We don't know if that max INCLUDES the child or not!      │
│                                                             │
│   Example:                                                  │
│       Parent (value=5)                                      │
│          |                                                  │
│       Child (value=10, returns max=10)                      │
│                                                             │
│   Can parent take 5? We don't know!                         │
│   - If child's 10 includes the child → Parent can't take 5  │
│   - If child's 10 skips the child → Parent CAN take 5       │
│                                                             │
│   SOLUTION: Child returns BOTH values!                      │
│   (rob_me, skip_me) = (10, 0) → Now parent knows!           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The "Aha!" Moment (Noticing the Pattern)

**Instructor:**
"The key insight is:

> Each node returns TWO values:
> 1. Max money if we ROB this node
> 2. Max money if we SKIP this node"

```
For each node, we calculate:

rob_this = node.val + skip_left + skip_right
           (If I rob myself, I must skip my children)

skip_this = max(rob_left, skip_left) + max(rob_right, skip_right)
            (If I skip myself, children can do whatever is optimal)

Return: (rob_this, skip_this)
```

**The pattern:**
```
┌─────────────────────────────────────────────────────────────┐
│   THE TUPLE RETURN PATTERN:                                 │
│                                                             │
│   1. Each node returns MULTIPLE values (a tuple)            │
│   2. These values represent DIFFERENT CHOICES/STATES        │
│   3. Parent uses child's tuple to compute its own tuple     │
│   4. Final answer combines the root's tuple                 │
│                                                             │
│   This is DYNAMIC PROGRAMMING on trees!                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **Tuple Return** template:"

```python
def solve(root):
    def dfs(node):
        # 1) BASE CASE: Return tuple of default values
        if not node:
            return (BASE_VAL_1, BASE_VAL_2)  # e.g., (0, 0)
        
        # 2) GET CHILDREN'S TUPLES
        left = dfs(node.left)    # Returns (val1, val2)
        right = dfs(node.right)  # Returns (val1, val2)
        
        # 3) COMPUTE THIS NODE'S TUPLE
        # Using children's values to calculate both options
        option1 = COMPUTE_OPTION1(node, left, right)
        option2 = COMPUTE_OPTION2(node, left, right)
        
        # 4) RETURN TUPLE
        return (option1, option2)
    
    # 5) FINAL ANSWER: Combine root's tuple
    result = dfs(root)
    return FINAL_COMBINE(result)  # Often max(result[0], result[1])
```

---

## Part 4 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the key parts:"

```
┌─────────────────────────────────────────────────────────────┐
│           TUPLE RETURN PATTERN - KEY STEPS                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) BASE CASE: Return tuple of zeros/defaults               │
│     if not node: return (0, 0)                              │
│     → Empty subtree contributes nothing                     │
│                                                             │
│  2) GET CHILDREN'S TUPLES:                                  │
│     left = dfs(node.left)   → (left_opt1, left_opt2)        │
│     right = dfs(node.right) → (right_opt1, right_opt2)      │
│     → Now we have 4 values to work with!                    │
│                                                             │
│  3) COMPUTE THIS NODE'S OPTIONS:                            │
│     option1 = ... (e.g., if we include this node)           │
│     option2 = ... (e.g., if we exclude this node)           │
│     → Use children's tuples in calculation                  │
│                                                             │
│  4) RETURN TUPLE:                                           │
│     return (option1, option2)                               │
│                                                             │
│  5) FINAL ANSWER:                                           │
│     Often max(root[0], root[1]) or some combination         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – The Key: What Goes in the Tuple?

**(Visual: Different problems, different tuples.)**

**Instructor:**
"The tuple values depend on the problem. Here are common examples:"

```
┌────────────────────┬────────────────────────────────────────┐
│ Problem            │ Tuple Meaning                          │
├────────────────────┼────────────────────────────────────────┤
│ House Robber III   │ (rob_this_node, skip_this_node)        │
│ LC 337             │ max money if we rob/skip current       │
├────────────────────┼────────────────────────────────────────┤
│ Diameter of Tree   │ (max_depth, max_diameter_so_far)       │
│ LC 543             │ depth for parent + global diameter     │
├────────────────────┼────────────────────────────────────────┤
│ Max Path Sum       │ (max_one_side, max_path_anywhere)      │
│ LC 124             │ extendable path + global best          │
├────────────────────┼────────────────────────────────────────┤
│ Binary Tree Cameras│ (has_camera, is_covered, min_cameras)  │
│ LC 968             │ multiple states for coverage           │
└────────────────────┴────────────────────────────────────────┘
```

---

## Part 6 – Filled-In Example: House Robber III

**(Visual: Complete code.)**

**Instructor:**
"Let's fill in the template for House Robber III:"

```python
def rob(root):
    def dfs(node):
        # BASE: Empty node contributes nothing
        if not node:
            return (0, 0)  # (rob_me, skip_me)
        
        # GET CHILDREN'S OPTIONS
        left = dfs(node.left)    # (rob_left, skip_left)
        right = dfs(node.right)  # (rob_right, skip_right)
        
        # COMPUTE THIS NODE'S OPTIONS
        # If I rob myself: can't rob children
        rob_this = node.val + left[1] + right[1]
        
        # If I skip myself: children can do whatever is best
        skip_this = max(left) + max(right)
        
        return (rob_this, skip_this)
    
    # FINAL: Take the better option at root
    return max(dfs(root))
```

---

## Part 7 – Walkthrough: House Robber III

**(Visual: Step-by-step trace.)**

```
Tree:       3
           / \
          2   3
           \   \
            3   1

Let's trace bottom-up:
```

```
dfs(3 - left leaf)   → rob=3, skip=0  → return (3, 0)
dfs(1 - right leaf)  → rob=1, skip=0  → return (1, 0)

dfs(2):
  left = dfs(None) = (0, 0)
  right = dfs(3) = (3, 0)
  
  rob_this = 2 + 0 + 0 = 2      (rob 2, must skip children)
  skip_this = max(0,0) + max(3,0) = 3  (skip 2, best from children)
  
  return (2, 3)

dfs(3 - right child of root):
  left = dfs(None) = (0, 0)
  right = dfs(1) = (1, 0)
  
  rob_this = 3 + 0 + 0 = 3
  skip_this = max(0,0) + max(1,0) = 1
  
  return (3, 1)

dfs(3 - root):
  left = (2, 3)   from node 2
  right = (3, 1)  from node 3
  
  rob_this = 3 + 3 + 1 = 7      (rob root, must skip children)
           = node.val + skip_left + skip_right
  
  skip_this = max(2,3) + max(3,1) = 3 + 3 = 6  (skip root, best from children)
  
  return (7, 6)

FINAL: max(7, 6) = 7

ANSWER: 7 (rob root=3, and grandchildren 3+1=4, but wait...)

Actually: rob root(3) + skip_left's best(3 from grandchild) + skip_right's best(1)
        = 3 + 3 + 1 = 7 ✓
```

---

## Part 8 – Visual: How Tuples Bubble Up

```
┌─────────────────────────────────────────────────────────────┐
│           HOW TUPLES BUBBLE UP                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│              ┌───┐                                          │
│              │ 3 │ → returns (7, 6)                         │
│              └───┘   rob=7 beats skip=6, answer=7           │
│              /   \                                          │
│           ┌───┐ ┌───┐                                       │
│           │ 2 │ │ 3 │                                       │
│           └───┘ └───┘                                       │
│          (2,3)  (3,1)                                       │
│              \     \                                        │
│            ┌───┐ ┌───┐                                      │
│            │ 3 │ │ 1 │                                      │
│            └───┘ └───┘                                      │
│           (3,0)  (1,0)                                      │
│                                                             │
│   Each node passes UP both options: (rob_me, skip_me)       │
│   Parent uses these to compute ITS options!                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Why This is Dynamic Programming

**(Visual: DP explanation.)**

**Instructor:**
"This is called **Tree DP** because:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHY IT'S DYNAMIC PROGRAMMING:                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. OPTIMAL SUBSTRUCTURE:                                  │
│      The optimal solution at a node depends on              │
│      optimal solutions at its children.                     │
│                                                             │
│   2. OVERLAPPING SUBPROBLEMS:                               │
│      Without memoization, we'd recompute subtrees.          │
│      (Though in trees, recursion naturally avoids this)     │
│                                                             │
│   3. STATE TRANSITIONS:                                     │
│      rob_this = node.val + skip_left + skip_right           │
│      skip_this = max(rob_left, skip_left) + ...             │
│      ↑ These are DP transition equations!                   │
│                                                             │
│   4. MULTIPLE STATES:                                       │
│      The tuple represents different "states" at each node   │
│      (e.g., "did we rob this node or not?")                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – When to Use This Pattern

**(Visual: Recognition clues.)**

**Instructor:**
"How do you recognize a Tuple Return / Tree DP problem?"

```
USE TUPLE RETURN WHEN:

✅ You need to make CHOICES at each node
✅ The choice affects what children/parent can do
✅ You need to track MULTIPLE VALUES per node
✅ Keywords:
   • "maximum/minimum with constraints"
   • "can't choose adjacent"
   • "path sum" (might need best ending at node vs best anywhere)
   • "with or without" scenarios

THE KEY QUESTION:
"Does returning a single value give parent enough info?"
If NO → Use tuple to return multiple values!
```

---

## Part 11 – Common Tuple Patterns

**(Visual: Common patterns.)**

**Instructor:**
"Here are the most common tuple patterns:"

```
┌─────────────────────────────────────────────────────────────┐
│   COMMON TUPLE PATTERNS:                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   PATTERN A: Include/Exclude                                │
│   (include_this, exclude_this)                              │
│   Example: House Robber III                                 │
│                                                             │
│   PATTERN B: Extendable/Global                              │
│   (best_ending_here, best_anywhere)                         │
│   Example: Max Path Sum, Diameter                           │
│                                                             │
│   PATTERN C: Multiple States                                │
│   (state_0, state_1, state_2, ...)                          │
│   Example: Binary Tree Cameras (not covered, covered, has)  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 12 – Comparison with Single Value Return

**(Visual: When to use which.)**

**Instructor:**
"When should you use Tuple vs Single Value?"

```
┌─────────────────────────────────────────────────────────────┐
│   SINGLE VALUE              vs       TUPLE RETURN           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Max Depth                          House Robber III       │
│   → Just need one number             → Need rob AND skip    │
│                                                             │
│   Sum of All Nodes                   Diameter of Tree       │
│   → Just need total                  → Need depth AND best  │
│                                                             │
│   Count Nodes                        Max Path Sum           │
│   → Just need count                  → Need extendable AND  │
│                                         global best         │
│                                                             │
│   RULE: If choice at node affects    Use TUPLE!             │
│         what parent can do...                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 13 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work (tuple operations)

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Tuple size is constant (usually 2-3 values)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 14 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Forgetting what each tuple value means
   Keep clear comments! (rob_me, skip_me) or (include, exclude)

❌ Mistake 2: Wrong base case tuple
   Empty node should return (0, 0) for most sum problems
   Think: "What does empty subtree contribute?"

❌ Mistake 3: Mixing up tuple indices
   left[0] vs left[1] — be consistent!
   Consider using named tuples or unpacking for clarity

❌ Mistake 4: Forgetting the final combination
   The root returns a tuple — you need to combine it!
   Often: max(dfs(root)) or dfs(root)[0] + dfs(root)[1]
```

---

## Part 15 – LeetCode Problems Using This Pattern

| Problem | Tuple Meaning | Final Answer |
|---------|---------------|--------------|
| LC 337 - House Robber III | (rob, skip) | max(rob, skip) |
| LC 543 - Diameter | (depth, diameter) | diameter |
| LC 124 - Max Path Sum | (extendable, global) | global |
| LC 968 - Binary Tree Cameras | (state0, state1, state2) | min cameras |

---

## Part 16 – Summary

**Instructor:**
"Let's recap the Tuple Return / Tree DP pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           TUPLE RETURN PATTERN SUMMARY                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    def dfs(node):                                           │
│        if not node: return (BASE1, BASE2)                   │
│        left, right = dfs(node.left), dfs(node.right)        │
│        option1 = COMPUTE1(node, left, right)                │
│        option2 = COMPUTE2(node, left, right)                │
│        return (option1, option2)                            │
│    return COMBINE(dfs(root))                                │
│                                                             │
│  KEY INSIGHT:                                               │
│    Return MULTIPLE values when parent needs to know         │
│    about DIFFERENT CHOICES/STATES at child                  │
│                                                             │
│  USE WHEN:                                                  │
│    - Choices at node affect parent's options                │
│    - "Include or exclude" scenarios                         │
│    - Need to track "best ending here" vs "best anywhere"    │
│                                                             │
│  THIS IS TREE DP!                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
# TUPLE RETURN / TREE DP TEMPLATE
def solve(root):
    def dfs(node):
        if not node:
            return (0, 0)  # Base case tuple
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # Compute both options using children's tuples
        option1 = ...  # e.g., include this node
        option2 = ...  # e.g., exclude this node
        
        return (option1, option2)
    
    result = dfs(root)
    return max(result)  # or other combination


# EXAMPLE: House Robber III
def rob(root):
    def dfs(node):
        if not node:
            return (0, 0)  # (rob, skip)
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        rob_this = node.val + left[1] + right[1]
        skip_this = max(left) + max(right)
        
        return (rob_this, skip_this)
    
    return max(dfs(root))
```


