# 🎬 Tutorial Script: LC 98 - Validate Binary Search Tree

---

## Part 0 – Problem Statement

**LeetCode 98: Validate Binary Search Tree**

```
Given the root of a binary tree, determine if it is a valid binary 
search tree (BST).

A valid BST is defined as follows:
- The left subtree of a node contains only nodes with keys LESS THAN 
  the node's key.
- The right subtree of a node contains only nodes with keys GREATER THAN 
  the node's key.
- Both the left and right subtrees must also be binary search trees.
```

**Example 1:**
```
Input:
    2
   / \
  1   3

Output: true
```

**Example 2:**
```
Input:
        5
       / \
      1   4
         / \
        3   6

Output: false

Explanation: The root's right child is 4, which is less than 5.
             Also, 3 is in the right subtree of 5 but 3 < 5!
```

---

## Part 1 – The Common Mistake

**Instructor:**
"Before we solve this, let me show you a common mistake:"

```python
# WRONG APPROACH:
def isValidBST(root):
    if not root:
        return True
    
    # Only check immediate children
    if root.left and root.left.val >= root.val:
        return False
    if root.right and root.right.val <= root.val:
        return False
    
    return isValidBST(root.left) and isValidBST(root.right)
```

**Why is this wrong?**
```
        5
       / \
      1   6
         / \
        3   7   ← 3 is in RIGHT subtree of 5, but 3 < 5!
        
The wrong approach only checks parent-child.
It misses that 3 violates the BST property relative to 5!
```

---

## Part 2 – The Key Insight

**Instructor:**
"The correct insight is:

> Every node must be within a valid RANGE, not just compared to its parent."

```
┌─────────────────────────────────────────────────────────────┐
│   THE INSIGHT: VALID RANGE                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Each node has a valid range: (min, max)                   │
│   The node's value must be: min < value < max               │
│                                                             │
│   When we go LEFT:  new range is (min, current_value)       │
│   When we go RIGHT: new range is (current_value, max)       │
│                                                             │
│   Example:                                                  │
│        5           (−∞, +∞)     5 is valid (between -∞,+∞)  │
│       / \                                                   │
│      3   7         3: (−∞, 5)   7: (5, +∞)                  │
│     / \                                                     │
│    1   4           1: (−∞, 3)   4: (3, 5)                   │
│                                                             │
│   Node 4 must be > 3 (parent) AND < 5 (grandparent)!        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – Pattern Recognition

**Instructor:**
"This is a Boolean Return problem with **constraints passed down**:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   What does the problem ask for?                            │
│   → Is this tree a valid BST? (yes/no)                      │
│                                                             │
│   What condition must EVERY node satisfy?                   │
│   → min < node.val < max (within valid range)               │
│                                                             │
│   How do we pass information down?                          │
│   → Update the valid range for each child                   │
│                                                             │
│   Pattern: BOOLEAN RETURN with CONSTRAINTS ✓                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 4 – Apply the Template

**Instructor:**
"Let's apply our Boolean Return template with constraints:"

```python
# TEMPLATE with constraints:
def validate(node, constraint):
    if not node:
        return True
    if not valid(node, constraint):
        return False
    return validate(left, updated_constraint) and \
           validate(right, updated_constraint)

# FOR VALID BST:
def isValidBST(node, min_val, max_val):
    if not node:
        return True
    if not (min_val < node.val < max_val):
        return False
    return isValidBST(node.left, min_val, node.val) and \
           isValidBST(node.right, node.val, max_val)
```

---

## Part 5 – The Solution

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate(node, min_val, max_val):
            # BASE CASE: Empty tree is valid
            if not node:
                return True
            
            # CHECK: Is current node within valid range?
            if not (min_val < node.val < max_val):
                return False
            
            # RECURSIVE: Check children with updated ranges
            # Left child: must be less than current → new max is current
            # Right child: must be greater than current → new min is current
            left_valid = validate(node.left, min_val, node.val)
            right_valid = validate(node.right, node.val, max_val)
            
            return left_valid and right_valid
        
        # Start with infinite range
        return validate(root, float('-inf'), float('inf'))
```

**Compact version:**
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def validate(node, min_val=float('-inf'), max_val=float('inf')):
            if not node:
                return True
            if not (min_val < node.val < max_val):
                return False
            return (validate(node.left, min_val, node.val) and
                    validate(node.right, node.val, max_val))
        
        return validate(root)
```

---

## Part 6 – Walkthrough: Valid BST

```
Tree:
        5
       / \
      3   7
     / \
    1   4

validate(5, -∞, +∞)
│ Is -∞ < 5 < +∞? YES ✓
│
├─ validate(3, -∞, 5)        ← Left child: max becomes 5
│  │ Is -∞ < 3 < 5? YES ✓
│  │
│  ├─ validate(1, -∞, 3)     ← Left: max becomes 3
│  │  │ Is -∞ < 1 < 3? YES ✓
│  │  │
│  │  ├─ validate(None, ...) → True
│  │  └─ validate(None, ...) → True
│  │  return True
│  │
│  └─ validate(4, 3, 5)      ← Right: min becomes 3
│     │ Is 3 < 4 < 5? YES ✓
│     │
│     ├─ validate(None, ...) → True
│     └─ validate(None, ...) → True
│     return True
│  
│  return True AND True = True
│
└─ validate(7, 5, +∞)        ← Right child: min becomes 5
   │ Is 5 < 7 < +∞? YES ✓
   │
   ├─ validate(None, ...) → True
   └─ validate(None, ...) → True
   return True

return True AND True = True ✓

ANSWER: True (Valid BST)
```

---

## Part 7 – Walkthrough: Invalid BST

```
Tree:
        5
       / \
      1   6
         / \
        3   7    ← 3 is in right subtree of 5, but 3 < 5!

validate(5, -∞, +∞)
│ Is -∞ < 5 < +∞? YES ✓
│
├─ validate(1, -∞, 5)
│  │ Is -∞ < 1 < 5? YES ✓
│  └─ ... returns True
│
└─ validate(6, 5, +∞)        ← Right: min becomes 5
   │ Is 5 < 6 < +∞? YES ✓
   │
   ├─ validate(3, 5, 6)      ← Left of 6: range is (5, 6)
   │  │ Is 5 < 3 < 6? NO! ✗  ← 3 is not > 5!
   │  │ return False immediately!
   │
   └─ (short-circuit: don't check 7)
   
   return False

return True AND False = False ✗

ANSWER: False (Invalid BST - node 3 violates the range)
```

---

## Part 8 – Visual: How Ranges Propagate

```
┌─────────────────────────────────────────────────────────────┐
│   HOW RANGES PROPAGATE DOWN THE TREE:                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│              5                                              │
│         (−∞, +∞)       Root: can be anything                │
│            / \                                              │
│           /   \                                             │
│          3     7                                            │
│      (−∞, 5) (5, +∞)   Left must be < 5, Right must be > 5  │
│         / \                                                 │
│        /   \                                                │
│       1     4                                               │
│   (−∞, 3) (3, 5)       1 must be < 3, 4 must be > 3 AND < 5 │
│                                                             │
│   Notice: 4's range is (3, 5)                               │
│   It must be greater than its parent (3)                    │
│   AND less than its grandparent (5)!                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – The Range Update Rules

**Instructor:**
"Here are the rules for updating ranges:"

```
┌─────────────────────────────────────────────────────────────┐
│   RANGE UPDATE RULES:                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Current node has range: (min, max)                        │
│                                                             │
│   LEFT CHILD:                                               │
│   → Must be LESS than current node                          │
│   → New range: (min, current.val)                           │
│   → Max is updated to current.val                           │
│                                                             │
│   RIGHT CHILD:                                              │
│   → Must be GREATER than current node                       │
│   → New range: (current.val, max)                           │
│   → Min is updated to current.val                           │
│                                                             │
│   validate(node.left, min, node.val)  ← max shrinks         │
│   validate(node.right, node.val, max) ← min grows           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 98 SOLUTION           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not node:             │
│       return True           →          return True          │
│                                                             │
│   if not CONDITION:                if not (min < val < max):│
│       return False          →          return False         │
│                                                             │
│   return validate(left, ...)       return validate(left,    │
│          and validate(right,...)       min, node.val) and   │
│                             →        validate(right,        │
│                                        node.val, max)       │
│                                                             │
│   Constraints: (min_val, max_val) = valid range             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 11 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: True (empty tree is a valid BST)

# Edge Case 2: Single node
# root = TreeNode(1)
# Answer: True (single node is always valid)

# Edge Case 3: Equal values
# BST definition: strictly less than (<), strictly greater than (>)
# Equal values are NOT allowed!
#     2
#    /
#   2     ← Invalid! Left must be < root, not ==
# Answer: False

# Edge Case 4: Integer limits
# What if node.val = -2^31 or 2^31 - 1?
# Use float('-inf') and float('inf') to avoid issues
```

---

## Part 12 – Alternative: Inorder Traversal

**Instructor:**
"There's another elegant approach using the BST property:"

```
BST Property: Inorder traversal gives sorted (increasing) order!

If we do inorder traversal and the values aren't strictly increasing,
the BST is invalid.
```

```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        self.prev = float('-inf')
        
        def inorder(node):
            if not node:
                return True
            
            # Check left subtree
            if not inorder(node.left):
                return False
            
            # Check current: must be greater than previous
            if node.val <= self.prev:
                return False
            self.prev = node.val
            
            # Check right subtree
            return inorder(node.right)
        
        return inorder(root)
```

**Both approaches work! Range-checking is more intuitive for Boolean Return pattern.**

---

## Part 13 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 14 – Common Mistakes

```
❌ Mistake 1: Only checking parent-child relationship
   if root.left.val >= root.val: return False  ← NOT ENOUGH!
   Must check against ALL ancestors!

❌ Mistake 2: Using <= instead of <
   BST requires STRICTLY less than and greater than
   Equal values are not allowed in standard BST

❌ Mistake 3: Not handling integer overflow
   Use float('-inf') and float('inf') for safety
   
❌ Mistake 4: Forgetting to update both bounds
   Left child: update MAX to current value
   Right child: update MIN to current value
```

---

## Part 15 – Related Problems

| Problem | How it relates |
|---------|----------------|
| LC 98 - Validate BST | Range validation (this problem) |
| LC 230 - Kth Smallest | Use inorder traversal on BST |
| LC 99 - Recover BST | Find two swapped nodes |
| LC 700 - Search in BST | Basic BST search |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 98 - VALIDATE BST SUMMARY                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Boolean Return with CONSTRAINTS                  │
│                                                             │
│   Key Insight: Each node has a valid RANGE (min, max)       │
│                Must satisfy: min < node.val < max           │
│                                                             │
│   Base Case: Empty tree → True                              │
│   Condition: min < node.val < max                           │
│   Constraint Update:                                        │
│     - Left child: (min, node.val) - max shrinks             │
│     - Right child: (node.val, max) - min grows              │
│                                                             │
│   Code:                                                     │
│     def validate(node, min_val, max_val):                   │
│         if not node: return True                            │
│         if not (min_val < node.val < max_val): return False │
│         return validate(left, min, val) and                 │
│                validate(right, val, max)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


