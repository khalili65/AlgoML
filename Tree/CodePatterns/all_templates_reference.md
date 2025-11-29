# 🌳 Tree Code Patterns - All Templates with Examples

---

## Pattern 1: Void Return (Traverse & Collect)

**Template:**
```python
result = []
def dfs(node):
    if not node:
        return
    # PREORDER: result.append(node.val)
    dfs(node.left)
    # INORDER: result.append(node.val)
    dfs(node.right)
    # POSTORDER: result.append(node.val)
dfs(root)
return result
```

**Example: Inorder Traversal (LC 94)**
```python
result = []
def dfs(node):
    if not node:
        return
    dfs(node.left)
    result.append(node.val)  # INORDER position
    dfs(node.right)
dfs(root)
return result
```

---

## Pattern 1B: Void Return + Backtracking

**Template:**
```python
result = []
def dfs(node, path):
    if not node:
        return
    path.append(node.val)          # ADD
    if CONDITION:
        result.append(path[:])     # SAVE copy
    dfs(node.left, path)           # EXPLORE
    dfs(node.right, path)
    path.pop()                     # REMOVE (backtrack)
dfs(root, [])
return result
```

**Example: Binary Tree Paths (LC 257)**
```python
result = []
def dfs(node, path):
    if not node:
        return
    path.append(node.val)
    if not node.left and not node.right:  # leaf
        result.append(path[:])
    dfs(node.left, path)
    dfs(node.right, path)
    path.pop()
dfs(root, [])
return result
```

---

## Pattern 2: Single Value Return

**Template:**
```python
def dfs(node):
    if not node:
        return BASE
    left = dfs(node.left)
    right = dfs(node.right)
    return COMBINE(node, left, right)
```

**Example: Max Depth (LC 104)**
```python
def maxDepth(root):
    if not root:
        return 0
    left = maxDepth(root.left)
    right = maxDepth(root.right)
    return 1 + max(left, right)
```

**Example: Path Sum (LC 112)**
```python
def hasPathSum(root, target):
    if not root:
        return False
    if not root.left and not root.right:
        return target == root.val
    return hasPathSum(root.left, target - root.val) or \
           hasPathSum(root.right, target - root.val)
```

---

## Pattern 3: Boolean Return

**Template:**
```python
def dfs(node, ...params):
    if not node:
        return True
    if not CONDITION:
        return False
    return dfs(node.left, ...) and dfs(node.right, ...)
```

**Example: Same Tree (LC 100)**
```python
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q:
        return False
    if p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**Example: Valid BST (LC 98)**
```python
def isValidBST(root, lo=float('-inf'), hi=float('inf')):
    if not root:
        return True
    if not (lo < root.val < hi):
        return False
    return isValidBST(root.left, lo, root.val) and \
           isValidBST(root.right, root.val, hi)
```

---

## Pattern 4: Tuple Return (Tree DP)

**Template:**
```python
def dfs(node):
    if not node:
        return (BASE1, BASE2)
    left = dfs(node.left)
    right = dfs(node.right)
    opt1 = COMPUTE1(node, left, right)
    opt2 = COMPUTE2(node, left, right)
    return (opt1, opt2)
return FINAL(dfs(root))
```

**Example: House Robber III (LC 337)**
```python
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

**Example: Diameter (LC 543)**
```python
def diameterOfBinaryTree(root):
    def dfs(node):
        if not node:
            return (0, 0)  # (depth, max_diameter)
        left = dfs(node.left)
        right = dfs(node.right)
        depth = 1 + max(left[0], right[0])
        diameter = max(left[0] + right[0], left[1], right[1])
        return (depth, diameter)
    return dfs(root)[1]
```

---

## Pattern 5: TreeNode Return (Build/Modify)

**Template (Modify):**
```python
def dfs(node):
    if not node:
        return None
    left = dfs(node.left)
    right = dfs(node.right)
    node.left = ...   # modify
    node.right = ...  # modify
    return node
```

**Example: Invert Tree (LC 226)**
```python
def invertTree(root):
    if not root:
        return None
    left = invertTree(root.left)
    right = invertTree(root.right)
    root.left = right
    root.right = left
    return root
```

**Template (Build):**
```python
def build(data):
    if not data:
        return None
    node = TreeNode(VALUE)
    node.left = build(LEFT_DATA)
    node.right = build(RIGHT_DATA)
    return node
```

**Example: Build from Preorder & Inorder (LC 105)**
```python
def buildTree(preorder, inorder):
    if not preorder:
        return None
    root = TreeNode(preorder[0])
    mid = inorder.index(preorder[0])
    root.left = buildTree(preorder[1:mid+1], inorder[:mid])
    root.right = buildTree(preorder[mid+1:], inorder[mid+1:])
    return root
```

---

## Pattern 6: BFS (Level-by-Level)

**Template:**
```python
def bfs(root):
    if not root:
        return []
    result = []
    queue = [root]
    while queue:
        level_size = len(queue)  # KEY LINE!
        level = []
        for _ in range(level_size):
            node = queue.pop(0)
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

**Example: Level Order Traversal (LC 102)**
```python
def levelOrder(root):
    if not root:
        return []
    result = []
    queue = [root]
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.pop(0)
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

**Example: Right Side View (LC 199)**
```python
def rightSideView(root):
    if not root:
        return []
    result = []
    queue = [root]
    while queue:
        level_size = len(queue)
        for i in range(level_size):
            node = queue.pop(0)
            if i == level_size - 1:  # last node in level
                result.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return result
```

---

## 📊 Quick Lookup Table

| # | Pattern | Return | Key Line | When to Use |
|---|---------|--------|----------|-------------|
| 1 | Void | None | `result.append(node.val)` | Collect values |
| 1B | Backtracking | None | `path.append(); path.pop()` | Collect paths |
| 2 | Single Value | int | `return COMBINE(left, right)` | Compute from children |
| 3 | Boolean | bool | `return left and right` | Validate condition |
| 4 | Tuple | tuple | `return (opt1, opt2)` | Tree DP / choices |
| 5 | TreeNode | node | `return node` | Build/modify tree |
| 6 | BFS | list | `level_size = len(queue)` | Level-by-level |

---

## 🎯 Pattern Selection Cheat Sheet

```
"Collect all values"           → Pattern 1 (Void)
"Collect all paths"            → Pattern 1B (Backtracking)
"Find max/min/sum/count"       → Pattern 2 (Single Value)
"Is tree valid/same/balanced?" → Pattern 3 (Boolean)
"Choose best option at node"   → Pattern 4 (Tuple)
"Build or modify tree"         → Pattern 5 (TreeNode)
"Process by level"             → Pattern 6 (BFS)
```

