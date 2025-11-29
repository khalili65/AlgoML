# 🎬 Tutorial Script: LC 257 — Binary Tree Paths

## Using: Code Pattern 1 (Void Return) with Backtracking

---

## Part 1 – Problem Statement

```
Given the root of a binary tree, return all root-to-leaf paths 
in any order.

A leaf is a node with no children.

Example:
Input: root = [1,2,3,null,5]
        1
       / \
      2   3
       \
        5

Output: ["1->2->5", "1->3"]
```

---

## Part 2 – Pattern Review

**Instructor:**
"This problem uses the **Void Return** pattern with an important addition: **Backtracking!**

When we're building paths, we need to:
1. Add node to current path
2. Explore children
3. **Remove node from path** (backtrack) before returning"

---

## Part 3 – The Backtracking Extension

**Instructor:**
"Here's how we extend the Void Return template for path problems:"

```python
def solve(root):
    result = []  # External collection
    
    def dfs(node, path):
        if not node:
            return
        
        # ADD current node to path
        path.append(node.val)
        
        # CHECK if we found what we want (leaf in this case)
        if not node.left and not node.right:
            result.append(path[:])  # COPY the path!
        
        # EXPLORE children
        dfs(node.left, path)
        dfs(node.right, path)
        
        # BACKTRACK: remove current node
        path.pop()
    
    dfs(root, [])
    return result
```

---

## Part 4 – Mapping Problem to Template

| Template Part | Our Problem |
|---------------|-------------|
| **result = []** | List of all paths |
| **path parameter** | Current path being built |
| **Condition to collect** | Node is a leaf |
| **What to collect** | Copy of current path |
| **Backtrack** | `path.pop()` |

---

## Part 5 – Why Backtracking?

**Instructor:**
"Why do we need `path.pop()`? Let me show you:"

```
Tree:       1
           / \
          2   3

WITHOUT backtracking:
  At 1: path = [1]
  At 2: path = [1, 2]  → collect [1, 2]
  At 3: path = [1, 2, 3]  → WRONG! Should be [1, 3]

WITH backtracking:
  At 1: path = [1]
  At 2: path = [1, 2]  → collect [1, 2]
  Back at 1: path.pop() → path = [1]
  At 3: path = [1, 3]  → collect [1, 3] ✓
```

**The path is SHARED across recursive calls, so we must undo our changes!**

---

## Part 6 – Why path[:] (Copy)?

**Instructor:**
"Why do we use `path[:]` instead of just `path`?"

```python
# WRONG: 
result.append(path)  # Appends REFERENCE to same list

# After backtracking, path changes, and so does result!

# CORRECT:
result.append(path[:])  # Appends a COPY of the list

# The copy is independent, won't change when we backtrack
```

---

## Part 7 – Complete Solution

```python
def binaryTreePaths(root):
    # Step 1: External collection for all paths
    result = []
    
    def dfs(node, path):
        # Step 2: Base case
        if not node:
            return
        
        # Step 3: Add current node to path
        path.append(str(node.val))
        
        # Step 4: Check if leaf (condition to collect)
        if not node.left and not node.right:
            result.append("->".join(path))  # Format as string
        
        # Step 5: Explore children
        dfs(node.left, path)
        dfs(node.right, path)
        
        # Step 6: BACKTRACK - remove current node
        path.pop()
    
    # Step 7: Start with empty path
    dfs(root, [])
    
    return result
```

---

## Part 8 – Walkthrough

```
Tree:       1
           / \
          2   3
           \
            5

result = []

dfs(1, []):
  path = ["1"]
  Not a leaf, explore children
  
  dfs(2, ["1"]):
    path = ["1", "2"]
    Not a leaf, explore children
    
    dfs(None, ["1", "2"]): return
    
    dfs(5, ["1", "2"]):
      path = ["1", "2", "5"]
      IS a leaf! result.append("1->2->5")
      result = ["1->2->5"]
      path.pop() → path = ["1", "2"]
    
    path.pop() → path = ["1"]
  
  dfs(3, ["1"]):
    path = ["1", "3"]
    IS a leaf! result.append("1->3")
    result = ["1->2->5", "1->3"]
    path.pop() → path = ["1"]
  
  path.pop() → path = []

Final: ["1->2->5", "1->3"] ✓
```

---

## Part 9 – The Backtracking Template

**Instructor:**
"Here's the general template for path problems with backtracking:"

```python
def solve_paths(root):
    result = []
    
    def dfs(node, path):
        if not node:
            return
        
        path.append(node.val)        # 1. CHOOSE
        
        if CONDITION(node):          # 2. CHECK
            result.append(path[:])   # (copy the path!)
        
        dfs(node.left, path)         # 3. EXPLORE
        dfs(node.right, path)
        
        path.pop()                   # 4. UN-CHOOSE (backtrack)
    
    dfs(root, [])
    return result
```

---

## Part 10 – Similar Problems

| Problem | Condition to Collect |
|---------|----------------------|
| LC 257 - Binary Tree Paths | `is_leaf` |
| LC 113 - Path Sum II | `is_leaf AND sum == target` |
| LC 988 - Smallest String | `is_leaf` (collect string) |
| LC 129 - Sum Root to Leaf | `is_leaf` (collect number) |

---

## Part 11 – Complexity

```
Time Complexity: O(n)
  - Visit each node exactly once

Space Complexity: O(h) for recursion + O(n * h) for paths
  - h = height of tree
  - In worst case, there can be O(n) paths of length O(h)
```

---

## Part 12 – Key Takeaways

```
┌─────────────────────────────────────────────────────────────┐
│           LC 257 - PATTERN APPLICATION                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PATTERN USED: Void Return + Backtracking                   │
│                                                             │
│  BACKTRACKING STEPS:                                        │
│    1. CHOOSE: path.append(node.val)                         │
│    2. CHECK: if leaf, collect path[:]                       │
│    3. EXPLORE: recurse on children                          │
│    4. UN-CHOOSE: path.pop()                                 │
│                                                             │
│  CRITICAL POINTS:                                           │
│    • Use path[:] to COPY the path                           │
│    • Always path.pop() after exploring                      │
│    • Pass path as PARAMETER (not global)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

