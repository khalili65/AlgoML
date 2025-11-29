# 🎬 Tutorial Script: LC 199 - Binary Tree Right Side View

---

## Part 0 – Problem Statement

**LeetCode 199: Binary Tree Right Side View**

```
Given the root of a binary tree, imagine yourself standing on the 
right side of it, return the values of the nodes you can see ordered 
from top to bottom.
```

**Example 1:**
```
Input:
        1      ← you see 1
       / \
      2   3    ← you see 3 (rightmost)
       \   \
        5   4  ← you see 4 (rightmost)

Output: [1, 3, 4]
```

**Example 2:**
```
Input:
        1      ← you see 1
       /
      2        ← you see 2 (only node at this level)

Output: [1, 2]
```

**Example 3:**
```
Input:
        1      ← you see 1
         \
          3    ← you see 3 (only node)

Output: [1, 3]
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
│   What does the problem ask for?                            │
│   → The RIGHTMOST node at each level                        │
│   → This is a level-based problem!                          │
│                                                             │
│   What's the key insight?                                   │
│   → At each level, we only want the LAST node               │
│                                                             │
│   Pattern: BFS ✓                                            │
│   (with modification: only keep last node per level)        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The Key Insight

**Instructor:**
"This is a variation of level order traversal where we only care about the LAST node:"

```
Standard Level Order:  [[1], [2, 3], [5, 4]]
Right Side View:       [1, 3, 4]  ← last element of each level!

The "last node in each level" when processing left-to-right
is the rightmost node visible from the right side!
```

---

## Part 3 – The Solution

```python
from collections import deque

class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        
        result = []
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            
            for i in range(level_size):
                node = queue.popleft()
                
                # KEY: Only add the LAST node of each level
                if i == level_size - 1:
                    result.append(node.val)
                
                # Add children (left first, then right)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return result
```

**Alternative: Just track the last node**
```python
class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        
        result = []
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            
            for i in range(level_size):
                node = queue.popleft()
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            # After processing level, 'node' is the last one!
            result.append(node.val)
        
        return result
```

---

## Part 4 – Walkthrough

```
Tree:       1
           / \
          2   3
           \   \
            5   4
```

```
STEP 0: Initialize
  queue = [1]
  result = []

STEP 1: Process Level 0
  level_size = 1
  
  i=0: Pop 1
       i == level_size - 1? 0 == 0? YES!
       result.append(1) → result = [1]
       Add children: queue = [2, 3]

STEP 2: Process Level 1
  level_size = 2
  
  i=0: Pop 2
       i == level_size - 1? 0 == 1? NO
       Add children: queue = [3, 5]
  
  i=1: Pop 3
       i == level_size - 1? 1 == 1? YES!
       result.append(3) → result = [1, 3]
       Add children: queue = [5, 4]

STEP 3: Process Level 2
  level_size = 2
  
  i=0: Pop 5
       i == level_size - 1? 0 == 1? NO
       No children
  
  i=1: Pop 4
       i == level_size - 1? 1 == 1? YES!
       result.append(4) → result = [1, 3, 4]
       No children

queue = []
DONE!

ANSWER: [1, 3, 4]
```

---

## Part 5 – Visual: What You See from Right

```
┌─────────────────────────────────────────────────────────────┐
│   VIEW FROM THE RIGHT SIDE:                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│           1      ←────── Level 0: See 1                     │
│          / \                                                │
│         2   3    ←────── Level 1: See 3 (blocks 2)          │
│          \   \                                              │
│           5   4  ←────── Level 2: See 4 (blocks 5)          │
│                                                             │
│   Standing on the right, you see: [1, 3, 4]                 │
│                                                             │
│   Note: 5 exists but 4 blocks it from the right!            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   BFS TEMPLATE                →    LC 199 MODIFICATION      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   for _ in range(level_size):      for i in range(...):     │
│       node = queue.popleft()           node = popleft()     │
│       level_values.append(val)                              │
│                                        if i == size - 1:    │
│                                            result.append()  │
│                                                             │
│   result.append(level_values)      (no level_values needed) │
│                                                             │
│   Difference: Instead of ALL values, just the LAST one      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 7 – Alternative: DFS Approach

**Instructor:**
"This can also be solved with DFS by tracking the maximum depth seen:"

```python
class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        result = []
        
        def dfs(node, depth):
            if not node:
                return
            
            # If this is the first node at this depth, add it
            if depth == len(result):
                result.append(node.val)
            
            # Visit right first! (so right nodes are seen first)
            dfs(node.right, depth + 1)
            dfs(node.left, depth + 1)
        
        dfs(root, 0)
        return result
```

**Key insight:** Visit right child FIRST. The first node we see at each depth is the rightmost!

---

## Part 8 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: []

# Edge Case 2: Single node
# root = TreeNode(1)
# Answer: [1]

# Edge Case 3: Only left children (skewed left)
#     1
#    /
#   2
#  /
# 3
# Answer: [1, 2, 3]  ← all visible! No right nodes to block

# Edge Case 4: Only right children (skewed right)
#     1
#      \
#       2
#        \
#         3
# Answer: [1, 2, 3]

# Edge Case 5: Left subtree deeper than right
#         1
#        / \
#       2   3
#      /
#     4
# Answer: [1, 3, 4]  ← 4 is visible because no node at level 2 on right!
```

---

## Part 9 – Tricky Case: Left Deeper than Right

```
Tree:       1
           / \
          2   3
         /
        4

Level 0: [1]        → see 1
Level 1: [2, 3]     → see 3
Level 2: [4]        → see 4 (only node at this level!)

Answer: [1, 3, 4]

Even though 4 is in the LEFT subtree, it's visible because
there's no node in the right subtree at level 2!
```

---

## Part 10 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each node: O(1) for queue operations

Space Complexity: O(w) where w = max width
  - Queue holds at most one level at a time
  - Result array holds at most O(h) values (one per level)
  - Overall: O(max(w, h)) = O(n) worst case
```

---

## Part 11 – Left Side View (Bonus)

**Instructor:**
"For left side view, just take the FIRST node of each level:"

```python
def leftSideView(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        
        for i in range(level_size):
            node = queue.popleft()
            
            if i == 0:  # FIRST node instead of last!
                result.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    
    return result
```

---

## Part 12 – Related Problems

| Problem | What to track per level |
|---------|-------------------------|
| LC 199 - Right Side View | Last node (this problem) |
| LC 102 - Level Order | All nodes |
| LC 515 - Largest in Row | Max value |
| LC 637 - Average of Levels | Sum / count |
| LC 513 - Bottom Left Value | Last level's first node |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 199 - BINARY TREE RIGHT SIDE VIEW SUMMARY              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: BFS (with modification)                          │
│                                                             │
│   Key Insight:                                              │
│     Right side view = last node at each level               │
│                                                             │
│   Algorithm:                                                │
│     Standard BFS, but only append when i == level_size - 1  │
│                                                             │
│   Alternative (DFS):                                        │
│     Visit right child first, add first node at each depth   │
│                                                             │
│   Gotcha:                                                   │
│     Left subtree nodes CAN be visible if right subtree      │
│     doesn't have nodes at that level!                       │
│                                                             │
│   Time: O(n), Space: O(w)                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


