# 🎬 Tutorial Script: LC 105 - Construct Binary Tree from Preorder and Inorder

---

## Part 0 – Problem Statement

**LeetCode 105: Construct Binary Tree from Preorder and Inorder Traversal**

```
Given two integer arrays preorder and inorder where preorder is the 
preorder traversal of a binary tree and inorder is the inorder 
traversal of the same tree, construct and return the binary tree.
```

**Example:**
```
Input: 
  preorder = [3, 9, 20, 15, 7]
  inorder  = [9, 3, 15, 20, 7]

Output:
        3
       / \
      9  20
        /  \
       15   7
```

---

## Part 1 – Understanding the Traversals

**Instructor:**
"First, let's understand what preorder and inorder tell us:"

```
┌─────────────────────────────────────────────────────────────┐
│   PREORDER (Root-Left-Right):                               │
│   [3, 9, 20, 15, 7]                                         │
│    ↑                                                        │
│   FIRST element is always the ROOT!                         │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   INORDER (Left-Root-Right):                                │
│   [9, 3, 15, 20, 7]                                         │
│       ↑                                                     │
│   ROOT splits array into LEFT and RIGHT subtrees!           │
│   [9] | 3 | [15, 20, 7]                                     │
│   left  root  right                                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The Key Insight

**Instructor:**
"Here's the algorithm:"

```
1. First element of PREORDER = ROOT

2. Find root in INORDER:
   - Everything LEFT of root = left subtree
   - Everything RIGHT of root = right subtree

3. Use the SIZE of left subtree to split PREORDER:
   - Next (size) elements = left subtree's preorder
   - Remaining elements = right subtree's preorder

4. RECURSIVELY build left and right subtrees!
```

**Visual:**
```
preorder = [3, 9, 20, 15, 7]
            ↑  ↑   ↑-------↑
          root left   right

inorder  = [9, 3, 15, 20, 7]
            ↑  ↑  ↑-------↑
          left root  right

Root = 3
Left subtree: preorder=[9], inorder=[9]
Right subtree: preorder=[20,15,7], inorder=[15,20,7]
```

---

## Part 3 – Pattern Recognition

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   What does the problem ask?                                │
│   → BUILD a new tree from data                              │
│                                                             │
│   What do we return?                                        │
│   → A TreeNode (the root of constructed tree)               │
│                                                             │
│   Pattern: TREENODE RETURN (Build) ✓                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 4 – The Solution

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        # BASE CASE: No elements to build from
        if not preorder or not inorder:
            return None
        
        # STEP 1: First element of preorder is root
        root_val = preorder[0]
        root = TreeNode(root_val)  # CREATE new node!
        
        # STEP 2: Find root position in inorder
        mid = inorder.index(root_val)
        
        # STEP 3: Split arrays and build recursively
        # Left subtree: inorder[:mid], preorder[1:mid+1]
        # Right subtree: inorder[mid+1:], preorder[mid+1:]
        root.left = self.buildTree(preorder[1:mid+1], inorder[:mid])
        root.right = self.buildTree(preorder[mid+1:], inorder[mid+1:])
        
        # STEP 4: Return the built node
        return root
```

---

## Part 5 – Walkthrough

```
preorder = [3, 9, 20, 15, 7]
inorder  = [9, 3, 15, 20, 7]

buildTree([3,9,20,15,7], [9,3,15,20,7])
│
│ root_val = 3
│ Create node(3)
│ mid = inorder.index(3) = 1
│
│ Left:  preorder[1:2] = [9]
│        inorder[:1]   = [9]
│
│ Right: preorder[2:]  = [20,15,7]
│        inorder[2:]   = [15,20,7]
│
├─ buildTree([9], [9])
│  │ root_val = 9
│  │ Create node(9)
│  │ mid = 0
│  │
│  ├─ buildTree([], []) → None
│  └─ buildTree([], []) → None
│  │
│  return node(9)
│
└─ buildTree([20,15,7], [15,20,7])
   │ root_val = 20
   │ Create node(20)
   │ mid = inorder.index(20) = 1
   │
   ├─ buildTree([15], [15])
   │  │ Create node(15)
   │  │ return node(15)
   │
   └─ buildTree([7], [7])
      │ Create node(7)
      │ return node(7)
   │
   node(20).left = node(15)
   node(20).right = node(7)
   return node(20)

node(3).left = node(9)
node(3).right = node(20)
return node(3)

Result:
        3
       / \
      9  20
        /  \
       15   7
```

---

## Part 6 – Visual: How Arrays Split

```
┌─────────────────────────────────────────────────────────────┐
│   HOW ARRAYS SPLIT AT EACH STEP:                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Original:                                                 │
│   preorder = [3, 9, 20, 15, 7]                              │
│   inorder  = [9, 3, 15, 20, 7]                              │
│                                                             │
│   Root = 3, mid = 1                                         │
│                                                             │
│   Split for LEFT subtree:                                   │
│   preorder[1:mid+1] = preorder[1:2] = [9]                   │
│   inorder[:mid]     = inorder[:1]   = [9]                   │
│                                                             │
│   Split for RIGHT subtree:                                  │
│   preorder[mid+1:]  = preorder[2:]  = [20, 15, 7]           │
│   inorder[mid+1:]   = inorder[2:]   = [15, 20, 7]           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 7 – Optimized Solution (with HashMap)

**Instructor:**
"The basic solution calls `index()` which is O(n). We can optimize:"

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        # Build hashmap for O(1) index lookup
        inorder_map = {val: idx for idx, val in enumerate(inorder)}
        
        def build(pre_left, pre_right, in_left, in_right):
            if pre_left > pre_right:
                return None
            
            # Root is first element of preorder range
            root_val = preorder[pre_left]
            root = TreeNode(root_val)
            
            # Find root in inorder (O(1) with hashmap!)
            mid = inorder_map[root_val]
            
            # Size of left subtree
            left_size = mid - in_left
            
            # Build children
            root.left = build(pre_left + 1, pre_left + left_size,
                            in_left, mid - 1)
            root.right = build(pre_left + left_size + 1, pre_right,
                             mid + 1, in_right)
            
            return root
        
        return build(0, len(preorder) - 1, 0, len(inorder) - 1)
```

**This optimizes from O(n²) to O(n)!**

---

## Part 8 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE (Build)            →    LC 105 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not data:                     if not preorder:         │
│       return None           →          return None          │
│                                                             │
│   node = TreeNode(value)           root = TreeNode(pre[0])  │
│                             →                               │
│                                                             │
│   node.left = build(left_data)     root.left = build(...)   │
│   node.right = build(right_data) → root.right = build(...)  │
│                                                             │
│   return node               →      return root              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Why This Works

```
┌─────────────────────────────────────────────────────────────┐
│   THE INSIGHT:                                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   PREORDER gives us: ROOT, then left, then right            │
│   INORDER gives us:  left, then ROOT, then right            │
│                                                             │
│   Together they uniquely identify the tree!                 │
│                                                             │
│   Preorder tells us WHO is root                             │
│   Inorder tells us WHAT belongs to left/right               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – Edge Cases

```python
# Edge Case 1: Empty arrays
# preorder = [], inorder = []
# Answer: None

# Edge Case 2: Single node
# preorder = [1], inorder = [1]
# Answer: TreeNode(1)

# Edge Case 3: All left (skewed)
# preorder = [3, 2, 1], inorder = [1, 2, 3]
# Answer: 3 → 2 → 1 (all left children)
```

---

## Part 11 – Complexity Analysis

```
Basic Solution:
  Time: O(n²) - index() is O(n), called n times
  Space: O(n²) - array slicing creates new arrays

Optimized Solution:
  Time: O(n) - hashmap gives O(1) lookup
  Space: O(n) - hashmap + O(h) recursion stack
```

---

## Part 12 – Related Problems

| Problem | Difference |
|---------|------------|
| LC 105 - Preorder + Inorder | This problem |
| LC 106 - Inorder + Postorder | Root is LAST in postorder |
| LC 889 - Preorder + Postorder | Different split logic |
| LC 108 - Sorted Array to BST | Simpler: middle is root |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 105 - CONSTRUCT FROM PREORDER & INORDER SUMMARY        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: TreeNode Return (Build)                          │
│                                                             │
│   Algorithm:                                                │
│     1. preorder[0] = root                                   │
│     2. Find root in inorder → splits left/right             │
│     3. Recursively build left and right subtrees            │
│     4. Return the constructed node                          │
│                                                             │
│   Key Insight:                                              │
│     Preorder tells us WHO is root                           │
│     Inorder tells us WHAT goes left/right                   │
│                                                             │
│   Optimization: Use hashmap for O(1) index lookup           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


