# 🎬 Tutorial Script: LC 94 — Binary Tree Inorder Traversal

## Using: Code Pattern 1 (Void Return)

---

## Part 1 – Problem Statement

```
Given the root of a binary tree, return the inorder traversal 
of its nodes' values.

Example:
Input: root = [1,null,2,3]
        1
         \
          2
         /
        3

Output: [1, 3, 2]

Inorder = Left → Root → Right
```

---

## Part 2 – Pattern Review

**Instructor:**
"This problem is a direct application of the **Void Return** pattern. Let's recall the template:"

```python
def solve(root):
    result = []  # External collection
    
    def dfs(node):
        if not node:
            return
        
        # PREORDER position
        dfs(node.left)
        # INORDER position  ← We'll use THIS
        dfs(node.right)
        # POSTORDER position
    
    dfs(root)
    return result
```

---

## Part 3 – Mapping Problem to Template

**Instructor:**
"Let's map our problem to the template:"

| Template Part | Our Problem |
|---------------|-------------|
| **result = []** | List to collect node values |
| **Base case** | `if not node: return` |
| **Process position** | INORDER (between left and right) |
| **What to collect** | `node.val` |

---

## Part 4 – Complete Solution

```python
def inorderTraversal(root):
    # Step 1: Create external collection
    result = []
    
    def dfs(node):
        # Step 2: Base case
        if not node:
            return
        
        # Step 4: Traverse left FIRST
        dfs(node.left)
        
        # Step 3: Process IN THE MIDDLE (Inorder!)
        result.append(node.val)
        
        # Step 5: Traverse right LAST
        dfs(node.right)
    
    # Step 6: Start traversal
    dfs(root)
    
    # Step 7: Return result
    return result
```

---

## Part 5 – Walkthrough

```
Tree:   1
         \
          2
         /
        3

result = []

dfs(1):
  dfs(None): return (left is null)
  append 1 → result = [1]
  dfs(2):
    dfs(3):
      dfs(None): return (left is null)
      append 3 → result = [1, 3]
      dfs(None): return (right is null)
    append 2 → result = [1, 3, 2]
    dfs(None): return (right is null)

Final: [1, 3, 2] ✓
```

---

## Part 6 – Why This Pattern Works

**Instructor:**
"The Void Return pattern is perfect here because:

1. We need to **collect** all values → External list
2. We need to **traverse** the entire tree → DFS
3. We don't need to **compute** anything from children → Void return
4. Order matters → INORDER position"

---

## Part 7 – Preorder & Postorder (Same Pattern!)

**Instructor:**
"With the same template, just change the position of `append`:"

```python
# PREORDER (LC 144)
def preorderTraversal(root):
    result = []
    def dfs(node):
        if not node:
            return
        result.append(node.val)  # ← BEFORE children
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return result

# POSTORDER (LC 145)
def postorderTraversal(root):
    result = []
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        dfs(node.right)
        result.append(node.val)  # ← AFTER children
    dfs(root)
    return result
```

**Same template, different position = different traversal!**

---

## Part 8 – Complexity

```
Time Complexity: O(n)
  - Visit each node exactly once

Space Complexity: O(h) for recursion + O(n) for result
  - h = height of tree
  - Worst case: O(n) total
```

---

## Part 9 – Key Takeaways

```
┌─────────────────────────────────────────────────────────────┐
│           LC 94 - PATTERN APPLICATION                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PATTERN USED: Void Return (Pattern 1)                      │
│                                                             │
│  CUSTOMIZATIONS:                                            │
│    • Process position: INORDER (between children)           │
│    • What to collect: node.val                              │
│                                                             │
│  TEMPLATE STAYS SAME:                                       │
│    result = []                                              │
│    def dfs(node):                                           │
│        if not node: return                                  │
│        dfs(node.left)                                       │
│        result.append(node.val)  ← INORDER                   │
│        dfs(node.right)                                      │
│    dfs(root)                                                │
│    return result                                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

