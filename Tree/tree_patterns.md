# 🌳 Tree Patterns - Complete Overview

This guide covers all tree-related patterns and their LeetCode problems, organized into 11 subpatterns.

> 📘 **Also see:** [CodePatterns/](CodePatterns/) — A template-based approach that categorizes problems by their **code structure** (like Fixed Sliding Window), with detailed pattern tutorials and LeetCode problem solutions.

---

## 📊 Quick Reference Table

| # | Subpattern | Core Idea | Key Problems |
|---|------------|-----------|--------------|
| 1 | Tree Traversal (DFS) | Pre/In/Post order | LC 94, 144, 145 |
| 2 | Tree Traversal (BFS) | Level order | LC 102, 103, 199 |
| 3 | Binary Search Tree | BST properties | LC 98, 700, 230 |
| 4 | Tree Depth/Height | Max/min depth | LC 104, 111, 110 |
| 5 | Tree Path Problems | Root-to-leaf paths | LC 112, 124, 437 |
| 6 | Lowest Common Ancestor | Find LCA | LC 236, 235 |
| 7 | Tree Construction | Build from traversals | LC 105, 106 |
| 8 | Tree Transformation | Modify structure | LC 226, 114 |
| 9 | Subtree Problems | Compare trees | LC 100, 101, 572 |
| 10 | Tree Serialization | Encode/decode | LC 297, 449 |
| 11 | Tree DP | DP on trees | LC 337, 96, 968 |

---

## 🔑 Essential Tree Concepts

### Tree Node Definition

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### Key Properties

```
        1          ← Root (depth 0, height 2)
       / \
      2   3        ← Internal nodes (depth 1)
     / \
    4   5          ← Leaf nodes (depth 2, height 0)

Height = longest path from node to leaf
Depth = path length from root to node
```

---

## 📂 Subpattern 1: Tree Traversal (DFS)

### Three Types of DFS Traversal

```
        1
       / \
      2   3
     / \
    4   5
```

**When do we PRINT vs TRAVERSE?**

```
┌─────────────────────────────────────────────────────────────┐
│  PREORDER (Root-Left-Right)                                 │
│  ─────────────────────────────────────────────────────────  │
│  1. PRINT the node  ← Process FIRST                         │
│  2. Traverse LEFT subtree                                   │
│  3. Traverse RIGHT subtree                                  │
│                                                             │
│  Result: 1 → 2 → 4 → 5 → 3                                  │
│  "I print myself, then explore my children"                 │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  INORDER (Left-Root-Right)                                  │
│  ─────────────────────────────────────────────────────────  │
│  1. Traverse LEFT subtree                                   │
│  2. PRINT the node  ← Process IN THE MIDDLE                 │
│  3. Traverse RIGHT subtree                                  │
│                                                             │
│  Result: 4 → 2 → 5 → 1 → 3                                  │
│  "I explore left, print myself, then explore right"         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  POSTORDER (Left-Right-Root)                                │
│  ─────────────────────────────────────────────────────────  │
│  1. Traverse LEFT subtree                                   │
│  2. Traverse RIGHT subtree                                  │
│  3. PRINT the node  ← Process LAST                          │
│                                                             │
│  Result: 4 → 5 → 2 → 3 → 1                                  │
│  "I explore all children first, then print myself"          │
└─────────────────────────────────────────────────────────────┘
```

**Visual Walkthrough for Preorder (1 → 2 → 4 → 5 → 3):**
```
At 1: PRINT 1, then go left
  At 2: PRINT 2, then go left
    At 4: PRINT 4, no children, return
  Back at 2: go right
    At 5: PRINT 5, no children, return
  Back at 2: done, return
Back at 1: go right
  At 3: PRINT 3, no children, return
Done!
```

**Visual Walkthrough for Inorder (4 → 2 → 5 → 1 → 3):**
```
At 1: go left first
  At 2: go left first
    At 4: go left (null), PRINT 4, go right (null), return
  Back at 2: PRINT 2, then go right
    At 5: go left (null), PRINT 5, go right (null), return
  Back at 2: done, return
Back at 1: PRINT 1, then go right
  At 3: go left (null), PRINT 3, go right (null), return
Done!
```

**Visual Walkthrough for Postorder (4 → 5 → 2 → 3 → 1):**
```
At 1: go left first
  At 2: go left first
    At 4: go left (null), go right (null), PRINT 4, return
  Back at 2: go right
    At 5: go left (null), go right (null), PRINT 5, return
  Back at 2: PRINT 2, return
Back at 1: go right
  At 3: go left (null), go right (null), PRINT 3, return
Back at 1: PRINT 1
Done!
```

### Templates

```python
# Recursive Template
def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)

def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

def postorder(root):
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]
```

```python
# Iterative Preorder (using stack)
def preorder_iterative(root):
    if not root:
        return []
    result, stack = [], [root]
    while stack:
        node = stack.pop()
        result.append(node.val)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    return result
```

```python
# Iterative Inorder (using stack)
def inorder_iterative(root):
    result, stack = [], []
    current = root
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        result.append(current.val)
        current = current.right
    return result
```

### LeetCode Problems
- **LC 94 - Binary Tree Inorder Traversal**
- **LC 144 - Binary Tree Preorder Traversal**
- **LC 145 - Binary Tree Postorder Traversal**
- **LC 589 - N-ary Tree Preorder Traversal**
- **LC 590 - N-ary Tree Postorder Traversal**

---

## 📂 Subpattern 2: Tree Traversal (BFS)

### Level Order Traversal

```python
from collections import deque

def levelOrder(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level)
    
    return result
```

### Visual Example

```
        3
       / \
      9  20
        /  \
       15   7

Level 0: [3]
Level 1: [9, 20]
Level 2: [15, 7]

Output: [[3], [9, 20], [15, 7]]
```

### Variations

```python
# Zigzag Level Order
def zigzagLevelOrder(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    left_to_right = True
    
    while queue:
        level = deque()
        for _ in range(len(queue)):
            node = queue.popleft()
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(list(level))
        left_to_right = not left_to_right
    return result
```

### LeetCode Problems
- **LC 102 - Binary Tree Level Order Traversal**
- **LC 103 - Binary Tree Zigzag Level Order Traversal**
- **LC 107 - Binary Tree Level Order Traversal II** (bottom-up)
- **LC 199 - Binary Tree Right Side View**
- **LC 637 - Average of Levels in Binary Tree**
- **LC 429 - N-ary Tree Level Order Traversal**

---

## 📂 Subpattern 3: Binary Search Tree (BST)

### BST Property

```
For every node:
- All values in LEFT subtree < node.val
- All values in RIGHT subtree > node.val

        8
       / \
      3   10
     / \    \
    1   6   14
       / \  /
      4  7 13
```

### Key Operations

```python
# Search in BST - O(log n) average
def searchBST(root, val):
    if not root or root.val == val:
        return root
    if val < root.val:
        return searchBST(root.left, val)
    return searchBST(root.right, val)

# Insert into BST
def insertIntoBST(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insertIntoBST(root.left, val)
    else:
        root.right = insertIntoBST(root.right, val)
    return root

# Validate BST
def isValidBST(root, min_val=float('-inf'), max_val=float('inf')):
    if not root:
        return True
    if root.val <= min_val or root.val >= max_val:
        return False
    return (isValidBST(root.left, min_val, root.val) and
            isValidBST(root.right, root.val, max_val))
```

### Important: Inorder of BST = Sorted Order!

```python
# Kth Smallest in BST
def kthSmallest(root, k):
    stack = []
    current = root
    count = 0
    
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        current = stack.pop()
        count += 1
        if count == k:
            return current.val
        current = current.right
```

### LeetCode Problems
- **LC 98 - Validate Binary Search Tree**
- **LC 700 - Search in a Binary Search Tree**
- **LC 701 - Insert into a Binary Search Tree**
- **LC 450 - Delete Node in a BST**
- **LC 230 - Kth Smallest Element in a BST**
- **LC 235 - Lowest Common Ancestor of a BST**
- **LC 108 - Convert Sorted Array to BST**
- **LC 653 - Two Sum IV - Input is a BST**
- **LC 538 - Convert BST to Greater Tree**

---

## 📂 Subpattern 4: Tree Depth/Height

### Templates

```python
# Maximum Depth (Height)
def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))

# Minimum Depth (shortest path to leaf)
def minDepth(root):
    if not root:
        return 0
    if not root.left:
        return 1 + minDepth(root.right)
    if not root.right:
        return 1 + minDepth(root.left)
    return 1 + min(minDepth(root.left), minDepth(root.right))

# Balanced Tree Check
def isBalanced(root):
    def height(node):
        if not node:
            return 0
        left_h = height(node.left)
        right_h = height(node.right)
        if left_h == -1 or right_h == -1:
            return -1
        if abs(left_h - right_h) > 1:
            return -1
        return 1 + max(left_h, right_h)
    
    return height(root) != -1
```

### LeetCode Problems
- **LC 104 - Maximum Depth of Binary Tree**
- **LC 111 - Minimum Depth of Binary Tree**
- **LC 110 - Balanced Binary Tree**
- **LC 559 - Maximum Depth of N-ary Tree**
- **LC 543 - Diameter of Binary Tree**
- **LC 662 - Maximum Width of Binary Tree**

---

## 📂 Subpattern 5: Tree Path Problems

### Path Sum Template

```python
# Has Path Sum (root to leaf)
def hasPathSum(root, targetSum):
    if not root:
        return False
    if not root.left and not root.right:  # Leaf
        return root.val == targetSum
    return (hasPathSum(root.left, targetSum - root.val) or
            hasPathSum(root.right, targetSum - root.val))

# All Paths with Sum
def pathSum(root, targetSum):
    result = []
    
    def dfs(node, remaining, path):
        if not node:
            return
        path.append(node.val)
        if not node.left and not node.right and remaining == node.val:
            result.append(path[:])
        dfs(node.left, remaining - node.val, path)
        dfs(node.right, remaining - node.val, path)
        path.pop()  # Backtrack
    
    dfs(root, targetSum, [])
    return result
```

### Maximum Path Sum (Any to Any)

```python
def maxPathSum(root):
    max_sum = float('-inf')
    
    def dfs(node):
        nonlocal max_sum
        if not node:
            return 0
        
        # Max contribution from left/right (ignore if negative)
        left = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        
        # Path through current node
        current_path = node.val + left + right
        max_sum = max(max_sum, current_path)
        
        # Return max single-side path
        return node.val + max(left, right)
    
    dfs(root)
    return max_sum
```

### LeetCode Problems
- **LC 112 - Path Sum**
- **LC 113 - Path Sum II**
- **LC 437 - Path Sum III** (any starting point)
- **LC 124 - Binary Tree Maximum Path Sum** ⭐
- **LC 129 - Sum Root to Leaf Numbers**
- **LC 257 - Binary Tree Paths**
- **LC 988 - Smallest String Starting From Leaf**

---

## 📂 Subpattern 6: Lowest Common Ancestor (LCA)

### LCA in Binary Tree

```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root
    
    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)
    
    if left and right:
        return root  # p and q are in different subtrees
    return left or right
```

### LCA in BST (Optimized)

```python
def lowestCommonAncestorBST(root, p, q):
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
```

### Visual Example

```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4

LCA(5, 1) = 3
LCA(5, 4) = 5
LCA(6, 4) = 5
```

### LeetCode Problems
- **LC 236 - Lowest Common Ancestor of a Binary Tree** ⭐
- **LC 235 - Lowest Common Ancestor of a BST**
- **LC 1644 - LCA of Binary Tree II** (nodes may not exist)
- **LC 1650 - LCA of Binary Tree III** (with parent pointers)
- **LC 1676 - LCA of Binary Tree IV** (multiple nodes)

---

## 📂 Subpattern 7: Tree Construction

### From Preorder + Inorder

```python
def buildTree(preorder, inorder):
    if not preorder or not inorder:
        return None
    
    root = TreeNode(preorder[0])
    mid = inorder.index(preorder[0])
    
    root.left = buildTree(preorder[1:mid+1], inorder[:mid])
    root.right = buildTree(preorder[mid+1:], inorder[mid+1:])
    
    return root
```

### From Inorder + Postorder

```python
def buildTree(inorder, postorder):
    if not inorder or not postorder:
        return None
    
    root = TreeNode(postorder[-1])
    mid = inorder.index(postorder[-1])
    
    root.left = buildTree(inorder[:mid], postorder[:mid])
    root.right = buildTree(inorder[mid+1:], postorder[mid:-1])
    
    return root
```

### Key Insight

```
Preorder: [Root] [Left subtree] [Right subtree]
Inorder:  [Left subtree] [Root] [Right subtree]
Postorder: [Left subtree] [Right subtree] [Root]

Use root from pre/post to split inorder!
```

### LeetCode Problems
- **LC 105 - Construct Binary Tree from Preorder and Inorder** ⭐
- **LC 106 - Construct Binary Tree from Inorder and Postorder**
- **LC 889 - Construct Binary Tree from Preorder and Postorder**
- **LC 1008 - Construct BST from Preorder Traversal**

---

## 📂 Subpattern 8: Tree Transformation

### Invert Binary Tree

```python
def invertTree(root):
    if not root:
        return None
    root.left, root.right = invertTree(root.right), invertTree(root.left)
    return root
```

### Flatten to Linked List

```python
def flatten(root):
    if not root:
        return
    
    flatten(root.left)
    flatten(root.right)
    
    # Store right subtree
    right_subtree = root.right
    
    # Move left subtree to right
    root.right = root.left
    root.left = None
    
    # Find end of new right subtree
    current = root
    while current.right:
        current = current.right
    
    # Attach original right subtree
    current.right = right_subtree
```

### LeetCode Problems
- **LC 226 - Invert Binary Tree** ⭐
- **LC 114 - Flatten Binary Tree to Linked List**
- **LC 116 - Populating Next Right Pointers in Each Node**
- **LC 117 - Populating Next Right Pointers II**
- **LC 654 - Maximum Binary Tree**
- **LC 617 - Merge Two Binary Trees**

---

## 📂 Subpattern 9: Subtree Problems

### Same Tree

```python
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q:
        return False
    return (p.val == q.val and 
            isSameTree(p.left, q.left) and 
            isSameTree(p.right, q.right))
```

### Symmetric Tree

```python
def isSymmetric(root):
    def isMirror(t1, t2):
        if not t1 and not t2:
            return True
        if not t1 or not t2:
            return False
        return (t1.val == t2.val and 
                isMirror(t1.left, t2.right) and 
                isMirror(t1.right, t2.left))
    
    return isMirror(root, root)
```

### Subtree of Another Tree

```python
def isSubtree(root, subRoot):
    if not root:
        return False
    if isSameTree(root, subRoot):
        return True
    return isSubtree(root.left, subRoot) or isSubtree(root.right, subRoot)
```

### LeetCode Problems
- **LC 100 - Same Tree**
- **LC 101 - Symmetric Tree**
- **LC 572 - Subtree of Another Tree**
- **LC 951 - Flip Equivalent Binary Trees**

---

## 📂 Subpattern 10: Tree Serialization

### Serialize/Deserialize (Preorder)

```python
class Codec:
    def serialize(self, root):
        result = []
        
        def dfs(node):
            if not node:
                result.append("null")
                return
            result.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        
        dfs(root)
        return ",".join(result)
    
    def deserialize(self, data):
        values = iter(data.split(","))
        
        def dfs():
            val = next(values)
            if val == "null":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        
        return dfs()
```

### LeetCode Problems
- **LC 297 - Serialize and Deserialize Binary Tree** ⭐
- **LC 449 - Serialize and Deserialize BST**
- **LC 428 - Serialize and Deserialize N-ary Tree**

---

## 📂 Subpattern 11: Tree DP

### House Robber III

```python
def rob(root):
    def dfs(node):
        if not node:
            return (0, 0)  # (rob this node, don't rob)
        
        left = dfs(node.left)
        right = dfs(node.right)
        
        # Rob this node: can't rob children
        rob_this = node.val + left[1] + right[1]
        
        # Don't rob this: take max from children
        skip_this = max(left) + max(right)
        
        return (rob_this, skip_this)
    
    return max(dfs(root))
```

### Count Unique BSTs

```python
def numTrees(n):
    # Catalan number
    dp = [0] * (n + 1)
    dp[0] = dp[1] = 1
    
    for i in range(2, n + 1):
        for j in range(1, i + 1):
            dp[i] += dp[j-1] * dp[i-j]
    
    return dp[n]
```

### LeetCode Problems
- **LC 337 - House Robber III** ⭐
- **LC 96 - Unique Binary Search Trees**
- **LC 95 - Unique Binary Search Trees II**
- **LC 968 - Binary Tree Cameras**
- **LC 979 - Distribute Coins in Binary Tree**
- **LC 834 - Sum of Distances in Tree**

---

## 🧠 Pattern Recognition Cheat Sheet

| If the problem asks... | Use this subpattern |
|------------------------|---------------------|
| "Traverse in X order" | Subpattern 1 (DFS) |
| "Level by level" | Subpattern 2 (BFS) |
| "Valid BST" or "Search/Insert" | Subpattern 3 (BST) |
| "Depth/Height/Balanced" | Subpattern 4 |
| "Path sum" or "Root to leaf" | Subpattern 5 |
| "Common ancestor" | Subpattern 6 (LCA) |
| "Build tree from traversals" | Subpattern 7 |
| "Modify/transform tree" | Subpattern 8 |
| "Compare two trees" | Subpattern 9 |
| "Encode/decode tree" | Subpattern 10 |
| "Optimize over tree structure" | Subpattern 11 (DP) |

---

## 📊 Time & Space Complexity Summary

| Subpattern | Time | Space |
|------------|------|-------|
| DFS Traversal | O(n) | O(h) |
| BFS Traversal | O(n) | O(w) |
| BST Operations | O(h) avg | O(h) |
| Depth/Height | O(n) | O(h) |
| Path Problems | O(n) | O(h) |
| LCA | O(n) | O(h) |
| Construction | O(n) | O(n) |
| Transformation | O(n) | O(h) |
| Subtree Compare | O(m*n) | O(h) |
| Serialization | O(n) | O(n) |
| Tree DP | O(n) | O(h) |

Where: n = nodes, h = height, w = max width

