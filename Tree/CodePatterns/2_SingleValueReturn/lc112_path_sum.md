# 🎬 Tutorial Script: LC 112 - Path Sum

---

## Part 0 – Problem Statement

**LeetCode 112: Path Sum**

```
Given the root of a binary tree and an integer targetSum, return true 
if the tree has a root-to-leaf path such that adding up all the values 
along the path equals targetSum.

A leaf is a node with no children.
```

**Example 1:**
```
Input:
        5
       / \
      4   8
     /   / \
    11  13  4
   /  \      \
  7    2      1

targetSum = 22

Output: true

Explanation: The path 5 → 4 → 11 → 2 = 22 exists.
```

**Example 2:**
```
Input:
        1
       / \
      2   3

targetSum = 5

Output: false

Explanation: No root-to-leaf path sums to 5.
```

---

## Part 1 – Pattern Recognition

**Instructor:**
"Let's figure out which pattern to use:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   What does the problem ask for?                            │
│   → A single answer: true or false                          │
│                                                             │
│   Can we compute it from children's answers?                │
│   → YES!                                                    │
│   → If my LEFT subtree has a path summing to (target - me)  │
│     OR my RIGHT subtree has a path summing to (target - me) │
│   → Then I can form a path!                                 │
│                                                             │
│   Pattern: SINGLE VALUE RETURN ✓                            │
│   (returning a boolean is still a "single value")           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The Key Insight

**Instructor:**
"Here's the trick: as we go down, we **subtract** the current node's value from the target."

```
Original Question: "Is there a path from root to leaf that sums to 22?"

At node 5:  "Is there a path from me to a leaf that sums to 22?"
            → Ask children: "Is there a path summing to 22 - 5 = 17?"

At node 4:  "Is there a path from me to a leaf that sums to 17?"
            → Ask children: "Is there a path summing to 17 - 4 = 13?"

At node 11: "Is there a path from me to a leaf that sums to 13?"
            → Ask children: "Is there a path summing to 13 - 11 = 2?"

At node 2:  "Is there a path from me to a leaf that sums to 2?"
            → I AM a leaf! Is 2 == 2? YES! Return True!
```

```
┌─────────────────────────────────────────────────────────────┐
│   THE TRANSFORMATION:                                       │
│                                                             │
│   Instead of tracking the SUM so far,                       │
│   we track the REMAINING target.                            │
│                                                             │
│   At each node: remaining = remaining - node.val            │
│   At a leaf: check if remaining == 0                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – Apply the Template

**Instructor:**
"Let's fill in our template:"

```python
# TEMPLATE:
def dfs(node, remaining):
    if not node:
        return BASE
    
    # Check if leaf and remaining == 0?
    # Or ask children with updated remaining
    
    left = dfs(node.left, remaining - node.val)
    right = dfs(node.right, remaining - node.val)
    
    return COMBINE(left, right)
```

**What should BASE and COMBINE be?**

```
BASE = ?
→ If node is None, should we return True or False?
→ None means we went past a leaf without finding the answer
→ Answer: False

But wait! The real BASE is checking at a LEAF:
→ If it's a leaf and remaining == node.val, return True!

COMBINE = ?
→ If EITHER left subtree OR right subtree found a valid path
→ Answer: left OR right
```

---

## Part 4 – The Solution

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        def dfs(node, remaining):
            # BASE: Empty tree - no path exists
            if not node:
                return False
            
            # BASE: Leaf node - check if path sum matches
            if not node.left and not node.right:
                return remaining == node.val
            
            # RECURSIVE: Update remaining and ask children
            new_remaining = remaining - node.val
            
            left_has_path = dfs(node.left, new_remaining)
            right_has_path = dfs(node.right, new_remaining)
            
            # COMBINE: Either subtree has valid path?
            return left_has_path or right_has_path
        
        return dfs(root, targetSum)
```

**Shorter version:**
```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        
        if not root.left and not root.right:
            return targetSum == root.val
        
        return (self.hasPathSum(root.left, targetSum - root.val) or
                self.hasPathSum(root.right, targetSum - root.val))
```

---

## Part 5 – Walkthrough

```
Tree:       5
           / \
          4   8
         /   / \
        11  13  4
       /  \      \
      7    2      1

targetSum = 22

dfs(5, 22)
├─ dfs(4, 22-5=17)
│  ├─ dfs(11, 17-4=13)
│  │  ├─ dfs(7, 13-11=2)
│  │  │  └─ Leaf! Is 2 == 7? NO → return False
│  │  │
│  │  └─ dfs(2, 13-11=2)
│  │     └─ Leaf! Is 2 == 2? YES → return True ✓
│  │
│  │  return False OR True = True ✓
│  │
│  └─ (no right child)
│  
│  return True ✓
│
└─ (we already found True, but let's trace anyway)
   dfs(8, 22-5=17)
   ... would return False (no path sums to 17 from here)

return True OR False = True ✓

ANSWER: True (path 5→4→11→2 = 22)
```

---

## Part 6 – Visual: The Remaining Value

```
              ┌───┐
              │ 5 │ remaining = 22
              └───┘
              /   \
           ┌───┐ ┌───┐
    22-5=17│ 4 │ │ 8 │ 22-5=17
           └───┘ └───┘
           /       / \
        ┌───┐   ┌───┐ ┌───┐
 17-4=13│11 │   │13 │ │ 4 │
        └───┘   └───┘ └───┘
        /   \           \
     ┌───┐ ┌───┐       ┌───┐
13-11│ 7 │ │ 2 │       │ 1 │
  =2 └───┘ └───┘       └───┘
       ↓     ↓
    2≠7?   2==2? ✓
    False  True!
```

---

## Part 7 – Why Check Leaf Separately?

**Instructor:**
"You might wonder why we need a special check for leaves:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHY LEAF CHECK IS NECESSARY:                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   The problem says: "root-to-LEAF path"                     │
│                                                             │
│   We can only say "found it!" when we're at a LEAF.         │
│   Not at an intermediate node!                              │
│                                                             │
│   Example: target = 5                                       │
│        5                                                    │
│       /                                                     │
│      3                                                      │
│                                                             │
│   At node 5: remaining = 5, and 5 == 5, but...              │
│   Node 5 is NOT a leaf! We must continue to node 3.         │
│   At node 3: remaining = 5-5 = 0, and 0 ≠ 3 → False         │
│                                                             │
│   So we can't just check "remaining == 0" at any node!      │
│   We must check "remaining == node.val" only at LEAVES.     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 8 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 112 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not node:             │
│       return BASE           →          return False         │
│                                                             │
│   (special leaf check)             if is_leaf:              │
│                             →          return rem == val    │
│                                                             │
│   left = dfs(node.left, ...)       left = dfs(left, rem-val)│
│   right = dfs(node.right, ...)     right = dfs(right,rem-val│
│                                                             │
│   return COMBINE(...)       →      return left OR right     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None, targetSum = anything
# Answer: False (no path exists)

# Edge Case 2: Single node (root is a leaf)
# root = TreeNode(5), targetSum = 5
# Answer: True (the root itself is the path)

# Edge Case 3: Single node, wrong sum
# root = TreeNode(5), targetSum = 3
# Answer: False

# Edge Case 4: Negative values
# The tree can have negative values!
# The algorithm still works because we're just doing subtraction.
```

---

## Part 10 – Alternative: Track Sum Going Down

**Instructor:**
"There's another way to think about this: track the sum as you go down."

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        def dfs(node, current_sum):
            if not node:
                return False
            
            current_sum += node.val
            
            # At leaf: check if sum matches target
            if not node.left and not node.right:
                return current_sum == targetSum
            
            return dfs(node.left, current_sum) or dfs(node.right, current_sum)
        
        return dfs(root, 0)
```

**Both approaches work! Choose the one you find more intuitive:**
- Subtract from target (remaining approach)
- Add to sum (accumulating approach)

---

## Part 11 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit every node at most once
  - Each visit does O(1) work

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 12 – Related Problems

| Problem | Difference |
|---------|------------|
| LC 112 - Path Sum | Return true/false (this problem) |
| LC 113 - Path Sum II | Return ALL paths (use backtracking!) |
| LC 437 - Path Sum III | Path can start/end anywhere |
| LC 129 - Sum Root to Leaf Numbers | Interpret path as a number |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 112 - PATH SUM SUMMARY                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Single Value Return                              │
│                                                             │
│   Key Insight: Subtract node value from target as we go     │
│                At leaf: check if remaining == node.val      │
│                                                             │
│   BASE: False (empty tree has no path)                      │
│   LEAF CHECK: remaining == node.val                         │
│   COMBINE: left OR right (either path works)                │
│                                                             │
│   Code:                                                     │
│     if not node: return False                               │
│     if is_leaf: return targetSum == node.val                │
│     return hasPathSum(left, target-val) OR                  │
│            hasPathSum(right, target-val)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


