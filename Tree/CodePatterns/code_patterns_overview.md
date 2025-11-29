# 🌳 Tree Code Patterns - Template-Based Approach

Just like Fixed Sliding Window has a reusable 5-step template, **Tree problems have 6 core code patterns**. Master these templates and you can solve 95% of tree problems!

---

## 📁 Folder Structure

```
CodePatterns/
├── code_patterns_overview.md    ← You are here
├── 1_VoidReturn/
│   ├── void_return_pattern.md   ← Pattern explanation
│   ├── lc94_inorder_traversal.md
│   └── lc257_binary_tree_paths.md
├── 2_SingleValueReturn/
│   ├── single_value_return_pattern.md
│   ├── lc104_maximum_depth.md
│   └── lc112_path_sum.md
├── 3_BooleanReturn/
│   ├── boolean_return_pattern.md
│   ├── lc100_same_tree.md
│   └── lc98_validate_bst.md
├── 4_TupleReturn/
│   ├── tuple_return_pattern.md
│   ├── lc337_house_robber_iii.md
│   └── lc543_diameter_of_binary_tree.md
├── 5_TreeNodeReturn/
│   ├── treenode_return_pattern.md
│   ├── lc226_invert_binary_tree.md
│   └── lc105_construct_from_preorder_inorder.md
└── 6_BFS/
    ├── bfs_pattern.md
    ├── lc102_level_order_traversal.md
    └── lc199_right_side_view.md
```

---

## 📊 The 6 Core Tree Code Patterns

| Pattern | Return Type | Key Idea | Example Problems |
|---------|-------------|----------|------------------|
| 1 | `void` | Traverse & collect in external list | LC 94, 144, 145 |
| 2 | `single value` | Compute value from children | LC 104, 111, 112 |
| 3 | `boolean` | Validate condition | LC 98, 100, 101, 110 |
| 4 | `tuple` | Return multiple values (Tree DP) | LC 337, 543, 124 |
| 5 | `TreeNode` | Build/modify tree | LC 226, 105, 106 |
| 6 | BFS | Level-by-level with queue | LC 102, 199, 103 |

---

## 🧠 Decision Flowchart: Which Pattern to Use?

```
START: What does the problem ask for?
│
├─ "Traverse/collect all values" ────────────► Pattern 1 (Void)
│
├─ "Compute single value (height, sum)" ─────► Pattern 2 (Single Value)
│
├─ "Check if valid/same/symmetric" ──────────► Pattern 3 (Boolean)
│
├─ "Optimize with choices (rob or skip)" ────► Pattern 4 (Tuple/DP)
│
├─ "Build or modify tree" ───────────────────► Pattern 5 (TreeNode)
│
├─ "Level by level / shortest path" ─────────► Pattern 6 (BFS)
│
└─ Not sure? Start with Pattern 2 (most common)
```

---

## 📋 Quick Reference: All 6 Templates

```python
# PATTERN 1: Void (Traverse & Collect)
result = []
def dfs(node):
    if not node: return
    result.append(node.val)  # PRE/IN/POST
    dfs(node.left)
    dfs(node.right)

# PATTERN 2: Single Value
def dfs(node):
    if not node: return BASE
    left = dfs(node.left)
    right = dfs(node.right)
    return COMBINE(node, left, right)

# PATTERN 3: Boolean
def dfs(node, ...params):
    if not node: return True
    if not CONDITION: return False
    return dfs(left, ...) and dfs(right, ...)

# PATTERN 4: Tuple (Tree DP)
def dfs(node):
    if not node: return (BASE1, BASE2)
    left, right = dfs(node.left), dfs(node.right)
    val1 = COMPUTE1(node, left, right)
    val2 = COMPUTE2(node, left, right)
    return (val1, val2)

# PATTERN 5: TreeNode (Build/Modify)
def dfs(node):
    if not node: return None
    left = dfs(node.left)
    right = dfs(node.right)
    node.left, node.right = left, right  # or swap, modify
    return node

# PATTERN 6: BFS
queue = deque([root])
while queue:
    level_size = len(queue)  # KEY!
    for _ in range(level_size):
        node = queue.popleft()
        # process node
        # add children
```

---

## 🎯 Summary

| Pattern | Return | Template Core | Use When |
|---------|--------|---------------|----------|
| 1 | `void` | External list + traverse | Collect values |
| 2 | `int/value` | `return COMBINE(left, right)` | Compute from children |
| 3 | `bool` | `return condition AND left AND right` | Validate tree |
| 4 | `tuple` | `return (val1, val2)` | Multiple choices (DP) |
| 5 | `TreeNode` | `return node` | Build/modify tree |
| 6 | BFS | `level_size = len(queue)` | Level-by-level |

**Master these 6 templates, and you can solve any tree problem by identifying which pattern fits!**

