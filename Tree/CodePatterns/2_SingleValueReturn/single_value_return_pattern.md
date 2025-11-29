# 🎬 Tutorial Script: Tree Code Pattern 2 — Single Value Return (Compute from Children)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Welcome back! Today we're learning the **second tree code pattern**: **Single Value Return**.

This is probably the **most common** tree pattern you'll encounter. Instead of collecting values into a list, we **compute a single answer** by combining results from our children.

Let's start with a concrete example!"

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: binary tree.)**

```
        1
       / \
      2   3
     / \
    4   5

Problem: Find the MAXIMUM DEPTH of this tree.
(How many levels? Count from root to deepest leaf)

Expected output: 3
```

**Instructor:**
"Here's a binary tree. We want to find its maximum depth — the longest path from root to any leaf.

Looking at the tree:
- Path 1→2→4 has depth 3
- Path 1→2→5 has depth 3  
- Path 1→3 has depth 2

So the maximum depth is **3**."

---

## Part 1.5 – Why This Pattern is Different from Void Return

**(Visual: Comparison.)**

**Instructor:**
"Before we dive in, let me explain how this differs from Pattern 1 (Void Return):"

```
┌─────────────────────────────────────────────────────────────┐
│   VOID RETURN (Pattern 1)        SINGLE VALUE (Pattern 2)   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   def dfs(node):                 def dfs(node):             │
│       if not node:                   if not node:           │
│           return                         return BASE        │
│       result.append(...)             left = dfs(node.left)  │
│       dfs(node.left)                 right = dfs(node.right)│
│       dfs(node.right)                return COMBINE(...)    │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Returns: NOTHING               Returns: A VALUE           │
│   Collects: Into external list   Computes: From children    │
│   Approach: Traverse & collect   Approach: Bottom-up        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Key insight:**
```
In Void Return:   We TRAVERSE and COLLECT
In Single Value:  We COMPUTE and RETURN

The answer bubbles UP from leaves to root!
```

---

## Part 2 – The "Aha!" Moment (Noticing the Pattern)

**Instructor:**
"Here's the key insight for maximum depth:

> My depth = 1 + max(left child's depth, right child's depth)

If I know how deep my children are, I just add 1 for myself!"

```
        1           depth = 1 + max(2, 1) = 3
       / \
      2   3         depth of 2 = 1 + max(1, 1) = 2
     / \            depth of 3 = 1 + max(0, 0) = 1
    4   5           depth of 4 = 1 + max(0, 0) = 1
                    depth of 5 = 1 + max(0, 0) = 1
```

**The pattern:**
```
┌─────────────────────────────────────────────────────────────┐
│   THE SINGLE VALUE RETURN PATTERN:                          │
│                                                             │
│   1. Ask children for their answers                         │
│   2. Combine children's answers with my own contribution    │
│   3. Return the result to my parent                         │
│                                                             │
│   Each node computes its answer based on its children!      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **Single Value Return** template:"

```python
def solve(root):
    def dfs(node):
        # 1) BASE CASE: What's the answer for an empty tree?
        if not node:
            return BASE_VALUE  # Often 0, -inf, inf, etc.
        
        # 2) RECURSIVE CASE: Ask children for their answers
        left_result = dfs(node.left)
        right_result = dfs(node.right)
        
        # 3) COMBINE: Compute MY answer from children's answers
        my_result = COMBINE(node.val, left_result, right_result)
        
        # 4) RETURN: Pass my answer up to my parent
        return my_result
    
    return dfs(root)
```

---

## Part 4 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the 4 steps:"

```
┌─────────────────────────────────────────────────────────────┐
│           SINGLE VALUE RETURN - 4 STEPS                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) BASE CASE: if not node: return BASE_VALUE               │
│     → What's the answer for "nothing"?                      │
│     → Usually: 0 (for counts/sums), -inf/inf (for max/min)  │
│                                                             │
│  2) ASK CHILDREN: left = dfs(node.left)                     │
│                   right = dfs(node.right)                   │
│     → Get answers from both subtrees                        │
│                                                             │
│  3) COMBINE: my_answer = f(node.val, left, right)           │
│     → Compute MY answer using children's results            │
│     → This is the "creative" part - problem specific!       │
│                                                             │
│  4) RETURN: return my_answer                                │
│     → Pass my answer up to whoever asked                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – The Key Question: What is BASE and COMBINE?

**(Visual: Different problems, different BASE and COMBINE.)**

**Instructor:**
"The template stays the same — what changes is **BASE** and **COMBINE**:"

```
┌────────────────────┬──────────────┬─────────────────────────┐
│ Problem            │ BASE         │ COMBINE                 │
├────────────────────┼──────────────┼─────────────────────────┤
│ Max Depth          │ 0            │ 1 + max(left, right)    │
├────────────────────┼──────────────┼─────────────────────────┤
│ Min Depth          │ 0            │ 1 + min(left, right)*   │
├────────────────────┼──────────────┼─────────────────────────┤
│ Sum of All Nodes   │ 0            │ node.val + left + right │
├────────────────────┼──────────────┼─────────────────────────┤
│ Count All Nodes    │ 0            │ 1 + left + right        │
├────────────────────┼──────────────┼─────────────────────────┤
│ Max Value in Tree  │ -infinity    │ max(node.val,left,right)│
├────────────────────┼──────────────┼─────────────────────────┤
│ Path Sum Exists?   │ False        │ (see LC 112)            │
└────────────────────┴──────────────┴─────────────────────────┘

* Min Depth has a special case (see later)
```

---

## Part 6 – Filled-In Example: Maximum Depth

**(Visual: Complete code.)**

**Instructor:**
"Let's fill in the template for Maximum Depth:"

```python
def maxDepth(root):
    def dfs(node):
        # BASE: Empty tree has depth 0
        if not node:
            return 0
        
        # ASK CHILDREN: How deep are you?
        left_depth = dfs(node.left)
        right_depth = dfs(node.right)
        
        # COMBINE: My depth = 1 + deeper child
        my_depth = 1 + max(left_depth, right_depth)
        
        # RETURN: Tell parent my depth
        return my_depth
    
    return dfs(root)

# Even shorter:
def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

---

## Part 7 – Walkthrough: How Values "Bubble Up"

**(Visual: Step-by-step trace.)**

```
Tree:       1
           / \
          2   3
         / \
        4   5

Let's trace how the depth bubbles up from leaves to root:
```

```
STEP 1: Start at node 1, but we need children's answers first...

STEP 2: Go to node 2, but we need its children's answers first...

STEP 3: Go to node 4
        dfs(4.left) = dfs(None) = 0
        dfs(4.right) = dfs(None) = 0
        return 1 + max(0, 0) = 1
        
        Node 4 returns: 1

STEP 4: Go to node 5
        dfs(5.left) = dfs(None) = 0
        dfs(5.right) = dfs(None) = 0
        return 1 + max(0, 0) = 1
        
        Node 5 returns: 1

STEP 5: Back at node 2
        left_depth = 1 (from node 4)
        right_depth = 1 (from node 5)
        return 1 + max(1, 1) = 2
        
        Node 2 returns: 2

STEP 6: Go to node 3
        dfs(3.left) = dfs(None) = 0
        dfs(3.right) = dfs(None) = 0
        return 1 + max(0, 0) = 1
        
        Node 3 returns: 1

STEP 7: Back at node 1
        left_depth = 2 (from node 2)
        right_depth = 1 (from node 3)
        return 1 + max(2, 1) = 3
        
        Node 1 returns: 3

FINAL ANSWER: 3
```

---

## Part 8 – Visual: The Bottom-Up Flow

**(Visual: Values bubbling up.)**

```
┌─────────────────────────────────────────────────────────────┐
│           HOW VALUES BUBBLE UP (BOTTOM-UP)                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│         ┌───┐                                               │
│         │ 1 │ ← receives 2 from left, 1 from right          │
│         └───┘   returns 1 + max(2,1) = 3                    │
│         /   \                                               │
│      ┌───┐ ┌───┐                                            │
│      │ 2 │ │ 3 │ ← node 3 returns 1                         │
│      └───┘ └───┘                                            │
│      /   \       ← node 2 receives 1 from each child        │
│   ┌───┐ ┌───┐      returns 1 + max(1,1) = 2                 │
│   │ 4 │ │ 5 │                                               │
│   └───┘ └───┘                                               │
│      ↑     ↑                                                │
│   returns returns                                           │
│      1       1     ← leaves return 1 (depth 1)              │
│                                                             │
│   The answer BUBBLES UP from leaves to root!                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Understanding Recursion in This Pattern

**(Visual: The trust in recursion.)**

**Instructor:**
"A common question: How do I think about this recursively?

The key is **trusting that your children will give you the right answer**."

```
┌─────────────────────────────────────────────────────────────┐
│   THE RECURSIVE LEAP OF FAITH:                              │
│                                                             │
│   "I don't need to know HOW my children compute their       │
│    answers. I just TRUST that they will give me the         │
│    correct answer for their subtrees."                      │
│                                                             │
│   My job is simple:                                         │
│   1. Ask children for their answers (trust them!)           │
│   2. Combine with my own contribution                       │
│   3. Return the result                                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Think of it like a company:**
```
CEO (root) asks:   "What's the total revenue?"
                    ↓
VP (left child):   "My department made $1M" (you trust this)
VP (right child):  "My department made $2M" (you trust this)
                    ↓
CEO computes:      "Total = $1M + $2M = $3M"
CEO reports:       "$3M total"

The CEO doesn't verify HOW each VP calculated their number.
The CEO TRUSTS the VPs and just combines the results!
```

---

## Part 10 – When to Use This Pattern

**(Visual: Recognition clues.)**

**Instructor:**
"How do you recognize a Single Value Return problem?"

```
USE SINGLE VALUE RETURN WHEN:

✅ The problem asks for ONE answer (a number, a boolean)
✅ The answer can be computed FROM children's answers
✅ You see keywords like:
   • "height" / "depth"
   • "sum" / "count"
   • "maximum" / "minimum"
   • "exists" / "has path"

EXAMPLES:
• "Find the maximum depth"           → COMBINE: 1 + max(left, right)
• "Sum all node values"              → COMBINE: node.val + left + right
• "Does a path with sum X exist?"    → COMBINE: check if any path works
• "Count number of nodes"            → COMBINE: 1 + left + right
```

---

## Part 11 – Common BASE Values

**(Visual: Choosing BASE.)**

**Instructor:**
"Choosing the right BASE value is crucial:"

```
┌─────────────────────────────────────────────────────────────┐
│   HOW TO CHOOSE BASE VALUE:                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   For COUNTING/SUMMING problems:                            │
│   → BASE = 0                                                │
│   "An empty tree has count/sum of 0"                        │
│                                                             │
│   For MAXIMUM problems:                                     │
│   → BASE = 0 or -infinity                                   │
│   "Don't let empty subtrees affect the max"                 │
│                                                             │
│   For MINIMUM problems:                                     │
│   → BASE = infinity (or needs special handling)             │
│   "Don't let empty subtrees give fake minimum"              │
│                                                             │
│   For BOOLEAN (exists) problems:                            │
│   → BASE = False (usually)                                  │
│   "An empty tree doesn't satisfy the condition"             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 12 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node exactly once
  - n = number of nodes

Space Complexity: O(h) for recursion stack
  - h = height of tree
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Wrong BASE value
   For max depth, BASE = 0, not -infinity!
   (Empty tree has depth 0)

❌ Mistake 2: Forgetting to combine with current node
   return max(left, right)           ← WRONG!
   return 1 + max(left, right)       ← RIGHT! (add myself)

❌ Mistake 3: Not handling special cases
   Min depth needs special handling for nodes with only one child

❌ Mistake 4: Confusing void return with single value return
   This pattern RETURNS a value, doesn't collect into a list!
```

---

## Part 14 – LeetCode Problems Using This Pattern

| Problem | BASE | COMBINE |
|---------|------|---------|
| LC 104 - Max Depth | `0` | `1 + max(left, right)` |
| LC 111 - Min Depth | `0` | `1 + min(left, right)` + special case |
| LC 112 - Path Sum | `False` | Check if any path sums to target |
| LC 129 - Sum Root to Leaf Numbers | Build number along path |
| LC 250 - Count Univalue Subtrees | Count + check condition |

---

## Part 15 – Summary

**Instructor:**
"Let's recap the Single Value Return pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           SINGLE VALUE RETURN PATTERN SUMMARY               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    def dfs(node):                                           │
│        if not node: return BASE                             │
│        left = dfs(node.left)                                │
│        right = dfs(node.right)                              │
│        return COMBINE(node, left, right)                    │
│                                                             │
│  KEY INSIGHT:                                               │
│    Values BUBBLE UP from leaves to root                     │
│    Each node computes its answer from children's answers    │
│                                                             │
│  USE WHEN:                                                  │
│    Problem asks for ONE value (depth, sum, count, etc.)     │
│    Answer can be computed from children's answers           │
│                                                             │
│  TEMPLATE:                                                  │
│    1. BASE: What's the answer for empty tree?               │
│    2. ASK: Get answers from children                        │
│    3. COMBINE: Compute my answer from children's            │
│    4. RETURN: Pass my answer to parent                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
# SINGLE VALUE RETURN TEMPLATE
def solve(root):
    def dfs(node):
        if not node:
            return BASE  # 0, -inf, inf, False, etc.
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        return COMBINE(node.val, left, right)
    
    return dfs(root)

# EXAMPLE: Max Depth
def maxDepth(root):
    if not root:
        return 0  # BASE
    return 1 + max(maxDepth(root.left), maxDepth(root.right))  # COMBINE
```


