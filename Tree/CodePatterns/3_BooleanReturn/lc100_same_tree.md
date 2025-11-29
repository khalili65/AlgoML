# 🎬 Tutorial Script: LC 100 - Same Tree

---

## Part 0 – Problem Statement

**LeetCode 100: Same Tree**

```
Given the roots of two binary trees p and q, write a function to check 
if they are the same or not.

Two binary trees are considered the same if they are structurally 
identical, and the nodes have the same value.
```

**Example 1:**
```
Input: p = [1,2,3], q = [1,2,3]

    p:        q:
    1         1
   / \       / \
  2   3     2   3

Output: true
```

**Example 2:**
```
Input: p = [1,2], q = [1,null,2]

    p:        q:
    1         1
   /           \
  2             2

Output: false (different structure!)
```

**Example 3:**
```
Input: p = [1,2,1], q = [1,1,2]

    p:        q:
    1         1
   / \       / \
  2   1     1   2

Output: false (different values at same positions!)
```

---

## Part 1 – Pattern Recognition

**Instructor:**
"Let's identify which pattern to use:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   What does the problem ask for?                            │
│   → A yes/no answer (are they the same?)                    │
│                                                             │
│   What condition needs to be satisfied?                     │
│   → EVERY corresponding node must:                          │
│     - Both exist (or both not exist)                        │
│     - Have the same value                                   │
│                                                             │
│   How do we combine results?                                │
│   → ALL must match: left must match AND right must match    │
│                                                             │
│   Pattern: BOOLEAN RETURN ✓                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – Breaking Down the Logic

**Instructor:**
"Two trees are the same if they match at every position. Let's think about what can happen at each pair of nodes:"

```
┌─────────────────────────────────────────────────────────────┐
│   CASE ANALYSIS:                                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   CASE 1: Both p and q are None                             │
│   → Return True (two empty trees are the same!)             │
│                                                             │
│   CASE 2: One is None, other is not                         │
│   → Return False (different structure!)                     │
│                                                             │
│   CASE 3: Both exist but values differ                      │
│   → Return False (different values!)                        │
│                                                             │
│   CASE 4: Both exist and values match                       │
│   → Check if left subtrees match AND right subtrees match   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – Apply the Template

**Instructor:**
"Let's fill in our Boolean Return template:"

```python
# TEMPLATE:
def validate(node, ...):
    if not node:
        return True
    if not CONDITION:
        return False
    return validate(left) and validate(right)

# ADAPTED FOR TWO TREES:
def isSameTree(p, q):
    # Base cases
    if <both null>: return True
    if <one null>: return False
    
    # Check condition
    if <values differ>: return False
    
    # Recursive check with AND
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

---

## Part 4 – The Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        # CASE 1: Both empty → same!
        if not p and not q:
            return True
        
        # CASE 2: One empty, one not → different!
        if not p or not q:
            return False
        
        # CASE 3: Both exist, check values
        if p.val != q.val:
            return False
        
        # CASE 4: Values match, check subtrees
        left_same = self.isSameTree(p.left, q.left)
        right_same = self.isSameTree(p.right, q.right)
        
        return left_same and right_same
```

**Compact version:**
```python
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if not p and not q:
            return True
        if not p or not q or p.val != q.val:
            return False
        return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
```

---

## Part 5 – Walkthrough: Same Trees

```
Tree p:       Tree q:
    1             1
   / \           / \
  2   3         2   3

isSameTree(p=1, q=1)
│
│ Both exist? ✓
│ p.val(1) == q.val(1)? ✓
│
├─ isSameTree(p.left=2, q.left=2)
│  │ Both exist? ✓
│  │ p.val(2) == q.val(2)? ✓
│  │
│  ├─ isSameTree(None, None) → True
│  └─ isSameTree(None, None) → True
│  │
│  return True AND True = True ✓
│
└─ isSameTree(p.right=3, q.right=3)
   │ Both exist? ✓
   │ p.val(3) == q.val(3)? ✓
   │
   ├─ isSameTree(None, None) → True
   └─ isSameTree(None, None) → True
   │
   return True AND True = True ✓

return True AND True = True ✓

ANSWER: True
```

---

## Part 6 – Walkthrough: Different Trees (Structure)

```
Tree p:       Tree q:
    1             1
   /               \
  2                 2

isSameTree(p=1, q=1)
│
│ Both exist? ✓
│ p.val(1) == q.val(1)? ✓
│
├─ isSameTree(p.left=2, q.left=None)
│  │ p exists but q is None!
│  │ return False immediately! ✗
│
└─ (short-circuit: we don't even check right!)

return False ✗

ANSWER: False (different structure)
```

---

## Part 7 – Walkthrough: Different Trees (Values)

```
Tree p:       Tree q:
    1             1
   / \           / \
  2   1         1   2

isSameTree(p=1, q=1)
│
│ Both exist? ✓
│ p.val(1) == q.val(1)? ✓
│
├─ isSameTree(p.left=2, q.left=1)
│  │ Both exist? ✓
│  │ p.val(2) != q.val(1)? ✗
│  │ return False immediately!
│
└─ (short-circuit: we don't check right!)

return False ✗

ANSWER: False (different values)
```

---

## Part 8 – Visual: The Comparison

```
┌─────────────────────────────────────────────────────────────┐
│   HOW WE COMPARE TWO TREES:                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│     Tree p:        Tree q:        Compare:                  │
│                                                             │
│        1              1           1 == 1? ✓                 │
│       / \            / \                                    │
│      2   3          2   3         2 == 2? ✓  3 == 3? ✓      │
│     / \ / \        / \ / \                                  │
│    N  N N  N      N  N N  N       All nulls match? ✓        │
│                                                             │
│   We compare CORRESPONDING positions in both trees!         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Why AND is Crucial

**Instructor:**
"Notice we use AND to combine results. Here's why:"

```
For two trees to be the same:
- The left subtrees must be the same  → left_same
- AND the right subtrees must be the same → right_same

If EITHER is different, the whole tree is different!

┌─────────────────────────────────────────────────────────────┐
│   left_same    right_same    Result                         │
├─────────────────────────────────────────────────────────────┤
│   True         True          True (both match!)             │
│   True         False         False (right differs)          │
│   False        True          False (left differs)           │
│   False        False         False (both differ)            │
└─────────────────────────────────────────────────────────────┘

Only True AND True = True!
```

---

## Part 10 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 100 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not p and not q:      │
│       return True           →          return True          │
│                                    if not p or not q:       │
│                                        return False         │
│                                                             │
│   if not CONDITION:                if p.val != q.val:       │
│       return False          →          return False         │
│                                                             │
│   return validate(left)            return isSameTree(p.left,│
│          and validate(right) →            q.left) and       │
│                                        isSameTree(p.right,  │
│                                            q.right)         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 11 – Edge Cases

```python
# Edge Case 1: Both empty
# p = None, q = None
# Answer: True (two empty trees are the same)

# Edge Case 2: One empty
# p = TreeNode(1), q = None
# Answer: False

# Edge Case 3: Single nodes, same value
# p = TreeNode(1), q = TreeNode(1)
# Answer: True

# Edge Case 4: Single nodes, different value
# p = TreeNode(1), q = TreeNode(2)
# Answer: False
```

---

## Part 12 – Complexity Analysis

```
Time Complexity: O(min(n, m))
  - n = nodes in tree p
  - m = nodes in tree q
  - We visit each node until we find a difference
  - At worst, visit all nodes in smaller tree

Space Complexity: O(min(h_p, h_q))
  - h_p = height of tree p
  - h_q = height of tree q
  - Recursion stack depth
  - Worst case (skewed): O(min(n, m))
  - Best case (balanced): O(log(min(n, m)))
```

---

## Part 13 – Alternative: Iterative with Stack

**Instructor:**
"For completeness, here's an iterative approach:"

```python
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        stack = [(p, q)]
        
        while stack:
            node1, node2 = stack.pop()
            
            if not node1 and not node2:
                continue
            if not node1 or not node2:
                return False
            if node1.val != node2.val:
                return False
            
            stack.append((node1.left, node2.left))
            stack.append((node1.right, node2.right))
        
        return True
```

**But the recursive Boolean Return solution is cleaner!**

---

## Part 14 – Related Problems

| Problem | How it relates |
|---------|----------------|
| LC 100 - Same Tree | Direct comparison (this problem) |
| LC 101 - Symmetric Tree | Compare tree with its mirror |
| LC 572 - Subtree of Another | Find if one tree is a subtree |
| LC 951 - Flip Equivalent | Same after some flips |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 100 - SAME TREE SUMMARY                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Boolean Return                                   │
│                                                             │
│   Base Cases:                                               │
│     - Both None → True                                      │
│     - One None → False                                      │
│                                                             │
│   Condition: p.val == q.val                                 │
│                                                             │
│   Combine: isSameTree(p.left, q.left) AND                   │
│            isSameTree(p.right, q.right)                     │
│                                                             │
│   Key Insight: Compare corresponding positions              │
│                Use AND because ALL must match               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


