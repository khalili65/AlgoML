# 🎬 Tutorial Script: LC 543 - Diameter of Binary Tree

---

## Part 0 – Problem Statement

**LeetCode 543: Diameter of Binary Tree**

```
Given the root of a binary tree, return the length of the diameter 
of the tree.

The diameter of a binary tree is the length of the longest path 
between any two nodes in a tree. This path may or may not pass 
through the root.

The length of a path between two nodes is represented by the number 
of edges between them.
```

**Example 1:**
```
Input:
        1
       / \
      2   3
     / \
    4   5

Output: 3

Explanation: The longest path is [4, 2, 1, 3] or [5, 2, 1, 3]
             which has 3 edges.
```

**Example 2:**
```
Input:
    1
   /
  2

Output: 1

Explanation: The longest path is [2, 1] with 1 edge.
```

---

## Part 1 – Why This Problem is Tricky

**Instructor:**
"The tricky part is: the longest path might NOT go through the root!"

```
Example where diameter doesn't pass through root:

        1
       /
      2
     / \
    3   4
   /     \
  5       6

The diameter path is [5, 3, 2, 4, 6] = 4 edges
This path goes through node 2, not the root 1!
```

**So we can't just compute "height of left + height of right" at root!**

---

## Part 2 – The Key Insight

**Instructor:**
"At each node, we need to track TWO things:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHAT WE NEED TO TRACK:                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. DEPTH (for parent's calculation):                      │
│      "How deep is my subtree?"                              │
│      → Parent needs this to compute its diameter            │
│                                                             │
│   2. BEST DIAMETER SO FAR (global answer):                  │
│      "What's the longest path in my subtree?"               │
│      → The answer might be in a subtree, not at root!       │
│                                                             │
│   We return: (depth, max_diameter_so_far)                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**At each node:**
```
depth = 1 + max(left_depth, right_depth)
        (for parent to use)

diameter_through_me = left_depth + right_depth
                      (path that goes through THIS node)

best_diameter = max(diameter_through_me, left_best, right_best)
                (best of: through me, or entirely in a subtree)
```

---

## Part 3 – Pattern Recognition

**Instructor:**
"This is a Tuple Return problem because:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Can we return just ONE value?                             │
│                                                             │
│   If we return only depth:                                  │
│   → We lose track of the best diameter in subtrees!         │
│                                                             │
│   If we return only diameter:                               │
│   → Parent can't compute diameter through itself!           │
│     (needs children's depths)                               │
│                                                             │
│   Solution: Return BOTH!                                    │
│   (depth, max_diameter)                                     │
│                                                             │
│   Pattern: TUPLE RETURN ✓                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 4 – The Solution

```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            # BASE CASE: Empty node
            if not node:
                return (0, 0)  # (depth, max_diameter)
            
            # GET CHILDREN'S TUPLES
            left = dfs(node.left)    # (left_depth, left_max_diameter)
            right = dfs(node.right)  # (right_depth, right_max_diameter)
            
            # COMPUTE THIS NODE'S DEPTH (for parent)
            depth = 1 + max(left[0], right[0])
            
            # COMPUTE DIAMETER THROUGH THIS NODE
            diameter_through_me = left[0] + right[0]
            
            # COMPUTE BEST DIAMETER (max of all options)
            max_diameter = max(diameter_through_me, left[1], right[1])
            
            return (depth, max_diameter)
        
        # FINAL: Return the diameter (second element)
        return dfs(root)[1]
```

---

## Part 5 – Alternative: Using Instance Variable

**Instructor:**
"Some people prefer using an instance variable instead of tuple. Both work!"

```python
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        self.max_diameter = 0  # Global tracker
        
        def depth(node):
            if not node:
                return 0
            
            left_depth = depth(node.left)
            right_depth = depth(node.right)
            
            # Update global max
            self.max_diameter = max(self.max_diameter, 
                                    left_depth + right_depth)
            
            # Return depth for parent
            return 1 + max(left_depth, right_depth)
        
        depth(root)
        return self.max_diameter
```

**Comparison:**
```
TUPLE APPROACH:           GLOBAL VARIABLE APPROACH:
- Pure functional         - Uses side effect
- Returns (depth, diam)   - Returns only depth
- No external state       - Modifies self.max_diameter
- All info in return      - Info split between return and global

Both are valid! Tuple is "cleaner", global is "simpler to read".
```

---

## Part 6 – Walkthrough

```
Tree:       1
           / \
          2   3
         / \
        4   5

Step-by-step (bottom up):
```

```
dfs(4 - leaf):
  left = (0, 0), right = (0, 0)
  depth = 1 + max(0, 0) = 1
  diameter_through_me = 0 + 0 = 0
  max_diameter = max(0, 0, 0) = 0
  return (1, 0)

dfs(5 - leaf):
  return (1, 0)  [same logic]

dfs(3 - leaf):
  return (1, 0)  [same logic]

dfs(2):
  left = (1, 0)   ← from node 4
  right = (1, 0)  ← from node 5
  
  depth = 1 + max(1, 1) = 2
  diameter_through_me = 1 + 1 = 2  ← path [4, 2, 5]
  max_diameter = max(2, 0, 0) = 2
  
  return (2, 2)

dfs(1 - root):
  left = (2, 2)   ← from node 2
  right = (1, 0)  ← from node 3
  
  depth = 1 + max(2, 1) = 3
  diameter_through_me = 2 + 1 = 3  ← path [4, 2, 1, 3] or [5, 2, 1, 3]
  max_diameter = max(3, 2, 0) = 3
  
  return (3, 3)

FINAL: return dfs(root)[1] = 3

ANSWER: 3 edges
```

---

## Part 7 – Visual: Path Through a Node

```
┌─────────────────────────────────────────────────────────────┐
│   UNDERSTANDING DIAMETER THROUGH A NODE:                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│              ┌───┐                                          │
│              │ 2 │  ← diameter_through_me = left + right    │
│              └───┘                                          │
│             /     \                                         │
│          ┌───┐   ┌───┐                                      │
│   depth=1│ 4 │   │ 5 │depth=1                               │
│          └───┘   └───┘                                      │
│                                                             │
│   Path through node 2: [4] → [2] → [5]                      │
│   Number of edges = left_depth + right_depth = 1 + 1 = 2    │
│                                                             │
│   This is the diameter if it's the longest path!            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 8 – Why Track Max Diameter?

**Instructor:**
"Why can't we just compute diameter at the root?"

```
Counter-example:

            1
           /
          2
         / \
        3   4
       /     \
      5       6

At root (1):
  left_depth = 4, right_depth = 0
  diameter_through_root = 4 + 0 = 4

But the TRUE diameter is at node 2:
  left_depth = 2 (going through 3→5)
  right_depth = 2 (going through 4→6)
  diameter_through_2 = 2 + 2 = 4

Both are 4 in this case, but consider:

            1
           /
          2
         / \
        3   4
       / \ / \
      5  6 7  8

diameter_through_2 = 3 + 3 = 6
diameter_through_root = 6 + 0 = 6

Still same? But what if we add more to subtree of 2?
The point is: we MUST track the best seen so far!
```

---

## Part 9 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 543 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not node:             │
│       return (BASE1, BASE2)  →         return (0, 0)        │
│                                                             │
│   left = dfs(node.left)            left = dfs(node.left)    │
│   right = dfs(node.right)          right = dfs(node.right)  │
│                                                             │
│   option1 = COMPUTE1(...)    →     depth = 1 + max(l[0],r[0])│
│   option2 = COMPUTE2(...)    →     diam = max(l[0]+r[0],    │
│                                              l[1], r[1])    │
│                                                             │
│   return (option1, option2)  →     return (depth, diam)     │
│                                                             │
│   return result[?]           →     return dfs(root)[1]      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: 0

# Edge Case 2: Single node
# root = TreeNode(1)
# Answer: 0 (no edges)

# Edge Case 3: Two nodes
#     1
#    /
#   2
# Answer: 1 (one edge)

# Edge Case 4: Linear tree (all left or all right)
#     1
#      \
#       2
#        \
#         3
# Answer: 2 (path is 1→2→3)
```

---

## Part 11 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 12 – Common Mistakes

```
❌ Mistake 1: Confusing depth with edges
   depth = number of nodes
   edges = depth - 1 (but our depth starts at 0 for null)
   
   Actually in our solution:
   - leaf returns depth=1
   - diameter_through_node = left_depth + right_depth (gives edges!)
   
❌ Mistake 2: Only checking diameter at root
   The longest path might be entirely in a subtree!
   
❌ Mistake 3: Forgetting to track max_diameter
   Must carry the best seen so far up the tree

❌ Mistake 4: Returning depth instead of diameter
   return dfs(root)[1]  ← diameter is second element!
```

---

## Part 13 – Related Problems

| Problem | Similarity |
|---------|------------|
| LC 543 - Diameter | Longest path, any two nodes (this problem) |
| LC 124 - Max Path Sum | Similar but with values, can be negative |
| LC 687 - Longest Univalue Path | Diameter but values must be same |
| LC 104 - Max Depth | Just depth, no diameter tracking |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 543 - DIAMETER OF BINARY TREE SUMMARY                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Tuple Return                                     │
│                                                             │
│   Tuple: (depth, max_diameter)                              │
│                                                             │
│   Computations:                                             │
│     depth = 1 + max(left_depth, right_depth)                │
│     diameter_through_me = left_depth + right_depth          │
│     max_diameter = max(through_me, left_max, right_max)     │
│                                                             │
│   Base Case: (0, 0) for empty node                          │
│   Final Answer: dfs(root)[1] (the max_diameter)             │
│                                                             │
│   Key Insight: The longest path might not go through root!  │
│                Track the best diameter seen in any subtree. │
│                                                             │
│   Alternative: Use global variable instead of tuple         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


