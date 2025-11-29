# 🎬 Tutorial Script: LC 102 - Binary Tree Level Order Traversal

---

## Part 0 – Problem Statement

**LeetCode 102: Binary Tree Level Order Traversal**

```
Given the root of a binary tree, return the level order traversal 
of its nodes' values. (i.e., from left to right, level by level).
```

**Example 1:**
```
Input:
        3
       / \
      9  20
        /  \
       15   7

Output: [[3], [9, 20], [15, 7]]
```

**Example 2:**
```
Input:
    1

Output: [[1]]
```

**Example 3:**
```
Input: None

Output: []
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
│   → Group nodes by LEVEL                                    │
│   → Process left to right within each level                 │
│                                                             │
│   Do we need level-by-level processing?                     │
│   → YES! Output is grouped by level: [[level0], [level1]]   │
│                                                             │
│   Pattern: BFS ✓                                            │
│                                                             │
│   Key: Use queue + level_size = len(queue)                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – Why Not DFS?

**Instructor:**
"Could we use DFS? Yes, but it's less natural:"

```python
# DFS approach (works but less intuitive)
def levelOrder(root):
    result = []
    
    def dfs(node, level):
        if not node:
            return
        if level >= len(result):
            result.append([])  # Create new level list
        result[level].append(node.val)
        dfs(node.left, level + 1)
        dfs(node.right, level + 1)
    
    dfs(root, 0)
    return result
```

**BFS is more natural for level-order problems because it processes level by level automatically!**

---

## Part 3 – The Solution

```python
from collections import deque

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        # Edge case
        if not root:
            return []
        
        result = []
        queue = deque([root])  # Initialize with root
        
        while queue:
            level_size = len(queue)  # Nodes in current level
            level_values = []
            
            # Process all nodes at current level
            for _ in range(level_size):
                node = queue.popleft()
                level_values.append(node.val)
                
                # Add children for next level
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            result.append(level_values)
        
        return result
```

---

## Part 4 – Walkthrough

```
Tree:       3
           / \
          9  20
            /  \
           15   7
```

```
STEP 0: Initialize
  queue = [3]
  result = []

STEP 1: Process Level 0
  level_size = 1
  level_values = []
  
  Pop 3:
    level_values = [3]
    Add children: queue = [9, 20]
  
  result = [[3]]

STEP 2: Process Level 1
  level_size = 2
  level_values = []
  
  Pop 9:
    level_values = [9]
    No children
  
  Pop 20:
    level_values = [9, 20]
    Add children: queue = [15, 7]
  
  result = [[3], [9, 20]]

STEP 3: Process Level 2
  level_size = 2
  level_values = []
  
  Pop 15:
    level_values = [15]
    No children
  
  Pop 7:
    level_values = [15, 7]
    No children
  
  queue = [] (empty)
  result = [[3], [9, 20], [15, 7]]

DONE!

ANSWER: [[3], [9, 20], [15, 7]]
```

---

## Part 5 – Visual: Queue at Each Step

```
┌─────────────────────────────────────────────────────────────┐
│   QUEUE STATE AT EACH WHILE ITERATION:                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Iteration 1: queue = [3]                                  │
│                        └─ level 0                           │
│                                                             │
│   Iteration 2: queue = [9, 20]                              │
│                        └────┘                               │
│                        level 1                              │
│                                                             │
│   Iteration 3: queue = [15, 7]                              │
│                        └─────┘                              │
│                        level 2                              │
│                                                             │
│   Iteration 4: queue = []                                   │
│                        empty → exit while loop              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   BFS TEMPLATE                →    LC 102 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not root: return []           (same)                   │
│                                                             │
│   queue = deque([root])            (same)                   │
│                                                             │
│   while queue:                     (same)                   │
│       level_size = len(queue)      (same)                   │
│                                                             │
│       for _ in range(level_size):  (same)                   │
│           node = queue.popleft()   (same)                   │
│           # process node           level_values.append(val) │
│           # add children           if left/right: append    │
│                                                             │
│       # save level                 result.append(level_vals)│
│                                                             │
└─────────────────────────────────────────────────────────────┘

This is the DIRECT application of the BFS template!
```

---

## Part 7 – Why level_size Matters

```
WITHOUT level_size (WRONG!):

while queue:
    node = queue.popleft()
    result.append(node.val)
    if node.left: queue.append(node.left)
    if node.right: queue.append(node.right)

This gives: [3, 9, 20, 15, 7]  ← Not grouped by level!

WITH level_size (CORRECT!):

while queue:
    level_size = len(queue)  # Capture before processing
    for _ in range(level_size):
        ...

This gives: [[3], [9, 20], [15, 7]]  ← Grouped by level! ✓
```

---

## Part 8 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: []

# Edge Case 2: Single node
# root = TreeNode(1)
# Answer: [[1]]

# Edge Case 3: Only left children (skewed)
#     1
#    /
#   2
#  /
# 3
# Answer: [[1], [2], [3]]

# Edge Case 4: Complete binary tree
#        1
#       / \
#      2   3
#     / \ / \
#    4  5 6  7
# Answer: [[1], [2, 3], [4, 5, 6, 7]]
```

---

## Part 9 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each node: O(1) for queue operations

Space Complexity: O(w) where w = max width
  - Queue holds at most one level at a time
  - Worst case (complete tree's last level): O(n/2) = O(n)
  - Plus O(n) for result array
```

---

## Part 10 – Related Problems

| Problem | Difference |
|---------|------------|
| LC 102 - Level Order | Basic (this problem) |
| LC 107 - Level Order II | Reverse the result |
| LC 103 - Zigzag Order | Alternate left/right |
| LC 199 - Right Side View | Only last node per level |
| LC 637 - Average of Levels | Compute average per level |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 102 - LEVEL ORDER TRAVERSAL SUMMARY                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: BFS                                              │
│                                                             │
│   Algorithm:                                                │
│     1. Initialize queue with root                           │
│     2. While queue not empty:                               │
│        a. Capture level_size = len(queue)                   │
│        b. Process level_size nodes                          │
│        c. Add their children to queue                       │
│        d. Append level values to result                     │
│                                                             │
│   Key Line: level_size = len(queue)                         │
│             This ensures we process one level at a time     │
│                                                             │
│   Time: O(n), Space: O(w) where w = max width               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


