# 🎬 Tutorial Script: LC 226 - Invert Binary Tree

---

## Part 0 – Problem Statement

**LeetCode 226: Invert Binary Tree**

```
Given the root of a binary tree, invert the tree, and return its root.

(Invert = mirror the tree, swap left and right at every node)
```

**Example:**
```
Input:
        4
       / \
      2   7
     / \ / \
    1  3 6  9

Output:
        4
       / \
      7   2
     / \ / \
    9  6 3  1
```

---

## Part 1 – Pattern Recognition

**Instructor:**
"Let's identify the pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   What does the problem ask?                                │
│   → MODIFY the tree structure (swap children)               │
│                                                             │
│   Do we need to return something?                           │
│   → YES! Return the root of the modified tree               │
│                                                             │
│   What type of return?                                      │
│   → TreeNode (the modified root)                            │
│                                                             │
│   Pattern: TREENODE RETURN (Modify) ✓                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The Key Insight

**Instructor:**
"To invert a tree, at each node we need to:"

```
1. Invert the left subtree (recursively)
2. Invert the right subtree (recursively)
3. Swap left and right children
4. Return the node (so parent can use it)

The order matters! We need children inverted BEFORE we swap.
(Actually for this problem, order doesn't matter, but it's good practice)
```

---

## Part 3 – The Solution

```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # BASE CASE: Empty tree
        if not root:
            return None
        
        # RECURSIVE: Invert children first
        left_inverted = self.invertTree(root.left)
        right_inverted = self.invertTree(root.right)
        
        # MODIFY: Swap the children
        root.left = right_inverted
        root.right = left_inverted
        
        # RETURN: Return modified node
        return root
```

**Compact version:**
```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        
        root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
        return root
```

---

## Part 4 – Walkthrough

```
Tree:       4
           / \
          2   7
         / \
        1   3

Step-by-step (postorder - children first):
```

```
invertTree(4)
│
├─ invertTree(2)
│  ├─ invertTree(1)
│  │  ├─ invertTree(None) → None
│  │  └─ invertTree(None) → None
│  │  swap: (None, None) → (None, None)
│  │  return 1
│  │
│  └─ invertTree(3)
│     ├─ invertTree(None) → None
│     └─ invertTree(None) → None
│     return 3
│  
│  Now at node 2:
│  left_inverted = 1, right_inverted = 3
│  swap: root.left = 3, root.right = 1
│  return 2 (now has children 3, 1)
│
└─ invertTree(7)
   ├─ invertTree(None) → None
   └─ invertTree(None) → None
   return 7

Now at node 4:
left_inverted = 2 (with children 3, 1)
right_inverted = 7
swap: root.left = 7, root.right = 2

return 4

Result:     4
           / \
          7   2
             / \
            3   1
```

---

## Part 5 – Visual: The Swap at Each Level

```
┌─────────────────────────────────────────────────────────────┐
│   BEFORE:          AFTER:                                   │
│                                                             │
│       4               4                                     │
│      / \             / \                                    │
│     2   7    →      7   2      (swapped at root)            │
│    / \             / \                                      │
│   1   3           3   1        (swapped at node 2)          │
│                                                             │
│   Every node swaps its children!                            │
│   The recursion ensures ALL levels get swapped.             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Why Return the Node?

**Instructor:**
"You might wonder: why return the node if we're modifying in place?"

```
Two reasons:

1. PARENT NEEDS IT:
   root.left = invertTree(root.left)
   Parent must know what to assign to its left pointer!
   
2. THE ROOT:
   The caller needs to get the root back:
   result = invertTree(root)  # Returns the (modified) root

Even though we modify in place, returning allows the
recursive calls to "chain" properly.
```

---

## Part 7 – Alternative: Iterative (BFS)

```python
from collections import deque

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            
            # Swap children
            node.left, node.right = node.right, node.left
            
            # Add children to queue
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        return root
```

**Both work! Recursive is cleaner for this problem.**

---

## Part 8 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 226 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not root:             │
│       return None           →          return None          │
│                                                             │
│   left = modify(node.left)         left = invert(root.left) │
│   right = modify(node.right)       right = invert(root.right│
│                                                             │
│   # Modify node                    # Swap children          │
│   node.left = ...           →      root.left = right        │
│   node.right = ...                 root.right = left        │
│                                                             │
│   return node               →      return root              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work (swap)

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 10 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: None

# Edge Case 2: Single node
# root = TreeNode(1)
# Answer: TreeNode(1) (unchanged, no children to swap)

# Edge Case 3: Only left children
#     1
#    /
#   2
#  /
# 3
# Answer: All become right children
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 226 - INVERT BINARY TREE SUMMARY                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: TreeNode Return (Modify)                         │
│                                                             │
│   Algorithm:                                                │
│     1. Recursively invert left subtree                      │
│     2. Recursively invert right subtree                     │
│     3. Swap: root.left = right, root.right = left           │
│     4. Return root                                          │
│                                                             │
│   Key Code:                                                 │
│     root.left, root.right = invert(root.right),             │
│                             invert(root.left)               │
│     return root                                             │
│                                                             │
│   Time: O(n), Space: O(h)                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


