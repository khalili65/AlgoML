# 🎬 Tutorial Script: LC 337 - House Robber III

---

## Part 0 – Problem Statement

**LeetCode 337: House Robber III**

```
The thief has found himself a new place for his thievery again. 
There is only one entrance to this area, called root.

Besides the root, each house has one and only one parent house. 
After a tour, the smart thief realized that all houses in this place 
form a binary tree.

It will automatically contact the police if two directly-linked houses 
were broken into on the same night.

Given the root of the binary tree, return the maximum amount of money 
the thief can rob without alerting the police.
```

**Example 1:**
```
Input:
        3
       / \
      2   3
       \   \
        3   1

Output: 7

Explanation: 
Maximum = 3 + 3 + 1 = 7
Rob: root (3), and the two rightmost leaves (3 and 1)
```

**Example 2:**
```
Input:
        3
       / \
      4   5
     / \   \
    1   3   1

Output: 9

Explanation:
Maximum = 4 + 5 = 9
Rob: left child (4) and right child (5)
```

---

## Part 1 – Why This Problem is Tricky

**Instructor:**
"At first glance, you might think: just take alternating levels! But that doesn't always work:"

```
        3
       / \
      4   5
     / \   \
    1   3   1

Level 0: 3
Level 1: 4 + 5 = 9
Level 2: 1 + 3 + 1 = 5

Taking level 1 (9) is better than level 0 + level 2 (3 + 5 = 8)!
But levels aren't always the right way to think about it.
```

**The real question at each node:**
```
Should I ROB this house or SKIP it?

If I ROB it:  I get its value, but I CAN'T rob my children
If I SKIP it: I get nothing here, but my children are FREE to be robbed
```

---

## Part 2 – Pattern Recognition

**Instructor:**
"Let's identify the pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN RECOGNITION:                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   At each node, we have TWO CHOICES:                        │
│   1. Rob this node (constrains children)                    │
│   2. Skip this node (children are free)                     │
│                                                             │
│   Does parent need to know WHICH choice we made?            │
│   YES! Because it affects whether parent can rob.           │
│                                                             │
│   If child returns only "max money" = 10...                 │
│   Parent doesn't know if that includes the child!           │
│                                                             │
│   Solution: Return BOTH values!                             │
│   (max_if_rob_child, max_if_skip_child)                     │
│                                                             │
│   Pattern: TUPLE RETURN ✓                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – The Key Insight

**Instructor:**
"Each node returns a tuple: `(rob_me, skip_me)`"

```
rob_me  = What's the max if we ROB this node?
skip_me = What's the max if we SKIP this node?

For any node:

rob_this = node.val + skip_left + skip_right
           ↑ My value   ↑ Must skip children (can't rob adjacent)

skip_this = max(rob_left, skip_left) + max(rob_right, skip_right)
            ↑ Left can do whatever is best + Right can do whatever is best
```

---

## Part 4 – The Solution

```python
class Solution:
    def rob(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            # BASE CASE: Empty node
            if not node:
                return (0, 0)  # (rob_me, skip_me)
            
            # GET CHILDREN'S TUPLES
            left = dfs(node.left)    # (rob_left, skip_left)
            right = dfs(node.right)  # (rob_right, skip_right)
            
            # OPTION 1: Rob this node
            # If I rob myself, I MUST skip my children
            rob_this = node.val + left[1] + right[1]
            
            # OPTION 2: Skip this node
            # If I skip myself, children can do whatever is optimal
            skip_this = max(left[0], left[1]) + max(right[0], right[1])
            
            return (rob_this, skip_this)
        
        # FINAL: Take the better option at root
        result = dfs(root)
        return max(result[0], result[1])
```

**Cleaner version:**
```python
class Solution:
    def rob(self, root: Optional[TreeNode]) -> int:
        def dfs(node):
            if not node:
                return (0, 0)
            
            left = dfs(node.left)
            right = dfs(node.right)
            
            rob_this = node.val + left[1] + right[1]
            skip_this = max(left) + max(right)
            
            return (rob_this, skip_this)
        
        return max(dfs(root))
```

---

## Part 5 – Walkthrough: Example 1

```
Tree:       3
           / \
          2   3
           \   \
            3   1

Step-by-step (bottom up):
```

```
dfs(3 - leaf, left grandchild of root):
  left = (0, 0), right = (0, 0)
  rob_this = 3 + 0 + 0 = 3
  skip_this = max(0,0) + max(0,0) = 0
  return (3, 0)

dfs(1 - leaf):
  return (1, 0)

dfs(2):
  left = (0, 0)     ← no left child
  right = (3, 0)    ← from grandchild
  
  rob_this = 2 + 0 + 0 = 2     (rob 2, skip both children)
  skip_this = max(0,0) + max(3,0) = 0 + 3 = 3  (skip 2, best from right)
  
  return (2, 3)
  ↑ Note: skip (3) > rob (2) here because grandchild is worth more!

dfs(3 - right child of root):
  left = (0, 0)
  right = (1, 0)
  
  rob_this = 3 + 0 + 0 = 3
  skip_this = max(0,0) + max(1,0) = 0 + 1 = 1
  
  return (3, 1)

dfs(3 - root):
  left = (2, 3)
  right = (3, 1)
  
  rob_this = 3 + 3 + 1 = 7     (rob root, must use skip values)
                               = 3 + skip_left + skip_right
  
  skip_this = max(2,3) + max(3,1) = 3 + 3 = 6  (skip root, best from each)
  
  return (7, 6)

FINAL: max(7, 6) = 7

Which houses? Root (3) + the optimal from skipped children
             = 3 + 3 (grandchild) + 1 (grandchild) = 7 ✓
```

---

## Part 6 – Walkthrough: Example 2

```
Tree:       3
           / \
          4   5
         / \   \
        1   3   1

Step-by-step:
```

```
dfs(1 - left leaf):   return (1, 0)
dfs(3 - middle leaf): return (3, 0)
dfs(1 - right leaf):  return (1, 0)

dfs(4):
  left = (1, 0), right = (3, 0)
  
  rob_this = 4 + 0 + 0 = 4
  skip_this = max(1,0) + max(3,0) = 1 + 3 = 4
  
  return (4, 4)  ← Interesting! Both options equal!

dfs(5):
  left = (0, 0), right = (1, 0)
  
  rob_this = 5 + 0 + 0 = 5
  skip_this = max(0,0) + max(1,0) = 0 + 1 = 1
  
  return (5, 1)

dfs(3 - root):
  left = (4, 4), right = (5, 1)
  
  rob_this = 3 + 4 + 1 = 8     (rob root, must skip children)
  skip_this = max(4,4) + max(5,1) = 4 + 5 = 9  (skip root, best from each)
  
  return (8, 9)

FINAL: max(8, 9) = 9

Which houses? Skip root, take best from children
             = 4 (or its children 1+3) + 5 = 9 ✓
```

---

## Part 7 – Visual: The Tuple Meaning

```
┌─────────────────────────────────────────────────────────────┐
│   WHAT EACH TUPLE VALUE MEANS:                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   (rob_me, skip_me)                                         │
│                                                             │
│   rob_me:  "If parent CANNOT take me (adjacent rule),       │
│            what's the max from my subtree INCLUDING me?"    │
│                                                             │
│   skip_me: "If parent CAN take me but chooses not to,       │
│            what's the max from my subtree EXCLUDING me?"    │
│                                                             │
│   Parent uses these to decide its own best option!          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 8 – Template Mapping

```
┌─────────────────────────────────────────────────────────────┐
│   TEMPLATE                    →    LC 337 SOLUTION          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   if not node:                     if not node:             │
│       return (BASE1, BASE2)  →         return (0, 0)        │
│                                                             │
│   left = dfs(node.left)            left = dfs(node.left)    │
│   right = dfs(node.right)          right = dfs(node.right)  │
│                                                             │
│   option1 = COMPUTE1(...)    →     rob = val + l[1] + r[1]  │
│   option2 = COMPUTE2(...)    →     skip = max(l) + max(r)   │
│                                                             │
│   return (option1, option2)  →     return (rob, skip)       │
│                                                             │
│   return COMBINE(result)     →     return max(dfs(root))    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Why Not Single Value?

**Instructor:**
"Let me show why a single return value wouldn't work:"

```python
# WRONG APPROACH
def rob(root):
    def dfs(node):
        if not node:
            return 0
        return max(
            node.val + dfs(grandchildren...),  # Rob this
            dfs(node.left) + dfs(node.right)   # Skip this
        )
```

**Problems:**
```
1. Accessing grandchildren is messy and error-prone

2. We're recomputing subtrees multiple times!
   → Exponential time complexity

3. No way to tell parent whether we included ourselves
   → Parent can't make informed decision

TUPLE SOLUTION: O(n) time, clean code, parent has full info!
```

---

## Part 10 – Edge Cases

```python
# Edge Case 1: Empty tree
# root = None
# Answer: 0

# Edge Case 2: Single node
# root = TreeNode(5)
# Answer: 5 (rob the only house)

# Edge Case 3: All in one line (skewed)
#     1
#      \
#       2
#        \
#         3
# rob(1) + rob(3) = 4  OR  rob(2) = 2
# Answer: 4

# Edge Case 4: Negative values? 
# Problem states values are non-negative, so no issue
```

---

## Part 11 – Complexity Analysis

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work

Space Complexity: O(h)
  - h = height of tree (recursion stack)
  - Each stack frame stores a constant-size tuple
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
```

---

## Part 12 – Related Problems

| Problem | Similarity |
|---------|------------|
| LC 198 - House Robber | Linear version (array) |
| LC 213 - House Robber II | Circular array version |
| LC 337 - House Robber III | Tree version (this problem) |
| LC 968 - Binary Tree Cameras | Similar tuple states (covered, camera, etc.) |

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   LC 337 - HOUSE ROBBER III SUMMARY                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Pattern: Tuple Return (Tree DP)                           │
│                                                             │
│   Tuple: (rob_this, skip_this)                              │
│                                                             │
│   Transitions:                                              │
│     rob_this = node.val + skip_left + skip_right            │
│     skip_this = max(rob_left, skip_left) +                  │
│                 max(rob_right, skip_right)                  │
│                                                             │
│   Base Case: (0, 0) for empty node                          │
│   Final Answer: max(root's tuple)                           │
│                                                             │
│   Key Insight: Parent needs to know BOTH options            │
│                (rob and skip) from each child               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


