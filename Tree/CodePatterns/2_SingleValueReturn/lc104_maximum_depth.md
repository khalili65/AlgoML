# 🎬 Tutorial Script: LC 104 - Maximum Depth of Binary Tree

---

## Part 0 – Problem Statement

**LeetCode 104: Maximum Depth of Binary Tree**

```
Given the root of a binary tree, return its maximum depth.

A binary tree's maximum depth is the number of nodes along the longest 
path from the root node down to the farthest leaf node.
```

**Example:**
```
Input:
        3
       / \
      9  20
         / \
        15  7

Output: 3

Explanation: The longest path is 3 → 20 → 15 (or 3 → 20 → 7), 
             which has 3 nodes.
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
│   → A single number (the maximum depth)                     │
│                                                             │
│   Can we compute it from children's answers?                │
│   → YES! My depth = 1 + max(left's depth, right's depth)    │
│                                                             │
│   Pattern: SINGLE VALUE RETURN ✓                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – Apply the Template

**Instructor:**
"Let's recall our Single Value Return template and fill it in:"

```python
# TEMPLATE:
def dfs(node):
    if not node:
        return BASE          # What's depth of empty tree?
    
    left = dfs(node.left)    # Ask left child
    right = dfs(node.right)  # Ask right child
    
    return COMBINE(...)      # Compute my depth
```

**Filling in BASE and COMBINE:**

```
BASE = ?
→ What's the depth of an empty tree (None)?
→ Answer: 0 (no nodes = depth 0)

COMBINE = ?
→ If my left subtree has depth L and right subtree has depth R...
→ My depth = 1 (myself) + max(L, R) (deeper child)
→ Answer: 1 + max(left, right)
```

---

## Part 3 – The Solution

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            # BASE: Empty tree has depth 0
            if not node:
                return 0
            
            # ASK CHILDREN
            left_depth = dfs(node.left)
            right_depth = dfs(node.right)
            
            # COMBINE: My depth = 1 + deeper child
            return 1 + max(left_depth, right_depth)
        
        return dfs(root)
```

**Even shorter (inline recursion):**
```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

---

## Part 4 – Walkthrough

```
Tree:       3
           / \
          9  20
             / \
            15  7

Let's trace the recursion:

dfs(3)
├─ dfs(9)
│  ├─ dfs(None) → returns 0
│  └─ dfs(None) → returns 0
│  returns 1 + max(0, 0) = 1
│
└─ dfs(20)
   ├─ dfs(15)
   │  ├─ dfs(None) → returns 0
   │  └─ dfs(None) → returns 0
   │  returns 1 + max(0, 0) = 1
   │
   └─ dfs(7)
      ├─ dfs(None) → returns 0
      └─ dfs(None) → returns 0
      returns 1 + max(0, 0) = 1
   
   returns 1 + max(1, 1) = 2

returns 1 + max(1, 2) = 3

ANSWER: 3
```

---

## Part 5 – Visual: Values Bubbling Up

```
         ┌───┐
         │ 3 │ ← 1 + max(1, 2) = 3  ✓ ANSWER
         └───┘
         /   \
      ┌───┐ ┌───┐
      │ 9 │ │20 │ ← 1 + max(1, 1) = 2
      └───┘ └───┘
        ↑   /   \
        1 ┌───┐ ┌───┐
          │15 │ │ 7 │ ← both return 1
          └───┘ └───┘
            ↑     ↑
            1     1
```

---

## Part 6 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 104 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not node:             │
│       return BASE           →          return 0             │
│                                                             │
│   left = dfs(node.left)            left = dfs(node.left)    │
│   right = dfs(node.right)          right = dfs(node.right)  │
│                                                             │
│   return COMBINE(...)       →      return 1 + max(l, r)     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 7 – Why Does This Work?

**Instructor:**
"The key insight is that depth is a **recursive property**:"

```
The depth of a tree = 
    1 (for the root)
    + 
    the depth of its deeper subtree

This naturally leads to recursion!
```

**Each node asks:**
```
"How deep are you, left child?"   → left_depth
"How deep are you, right child?"  → right_depth
"Then I am 1 level deeper than the deeper one!"
```

---

## Part 8 – Complexity Analysis

```
Time Complexity: O(n)
  - We visit every node exactly once
  - Each visit does O(1) work

Space Complexity: O(h)
  - h = height of tree
  - Recursion stack depth = height of tree
  - Worst case (skewed tree): O(n)
  - Best case (balanced tree): O(log n)
```

---

## Part 9 – Iterative Solution (BFS Alternative)

**Instructor:**
"For completeness, here's an iterative BFS solution:"

```python
from collections import deque

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        queue = deque([root])
        depth = 0
        
        while queue:
            depth += 1
            level_size = len(queue)
            
            for _ in range(level_size):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return depth
```

**But the recursive Single Value Return solution is cleaner and more intuitive!**

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 104 - MAXIMUM DEPTH SUMMARY                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Single Value Return                              │
│   BASE: 0 (empty tree has depth 0)                          │
│   COMBINE: 1 + max(left_depth, right_depth)                 │
│                                                             │
│   Key Insight: My depth = 1 + deeper child's depth          │
│                                                             │
│   Code:                                                     │
│     if not root: return 0                                   │
│     return 1 + max(maxDepth(left), maxDepth(right))         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


