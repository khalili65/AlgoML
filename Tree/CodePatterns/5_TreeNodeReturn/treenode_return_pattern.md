# 🎬 Tutorial Script: Tree Code Pattern 5 — TreeNode Return (Build/Modify)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Welcome back! Today we're learning the **fifth tree code pattern**: **TreeNode Return**.

This pattern is about **building** new trees or **modifying** existing ones. Instead of returning numbers or booleans, we return actual TreeNode objects!

Let's see what this looks like!"

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: binary tree transformation.)**

```
Problem: Invert a Binary Tree (Mirror it)

Before:              After:
      4                  4
     / \                / \
    2   7      →       7   2
   / \ / \            / \ / \
  1  3 6  9          9  6 3  1
```

**Instructor:**
"We want to flip the tree — every left child becomes right, and every right becomes left.

The key insight: we need to **return the modified node** so the parent can update its pointers!"

---

## Part 1.5 – Why This Pattern is Different

**(Visual: Comparison.)**

**Instructor:**
"Let me show you how this differs from other patterns:"

```
┌─────────────────────────────────────────────────────────────┐
│   PATTERN COMPARISON:                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   SINGLE VALUE RETURN:                                      │
│   → Returns a NUMBER (depth, sum, count)                    │
│   → Tree structure unchanged                                │
│                                                             │
│   BOOLEAN RETURN:                                           │
│   → Returns TRUE/FALSE                                      │
│   → Tree structure unchanged                                │
│                                                             │
│   TREENODE RETURN:                                          │
│   → Returns a TREENODE                                      │
│   → Tree structure IS CHANGED (built or modified)           │
│   → Parent uses returned node to update its children        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Key insight:**
```
When we return a TreeNode:
- We might return the SAME node (modified)
- We might return a NEW node (constructed)
- We might return a DIFFERENT node (deleted/rearranged)
- We might return None (node removed)

The parent MUST use this returned value!
```

---

## Part 2 – Two Types of TreeNode Return Problems

**Instructor:**
"There are two main categories:"

```
┌─────────────────────────────────────────────────────────────┐
│   TYPE 1: MODIFY EXISTING TREE                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   - Invert Binary Tree (swap children)                      │
│   - Flatten Binary Tree to Linked List                      │
│   - Delete Node in BST                                      │
│   - Prune Binary Tree                                       │
│                                                             │
│   You RECEIVE a tree and CHANGE it                          │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   TYPE 2: BUILD NEW TREE                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   - Construct from Preorder/Inorder                         │
│   - Construct from Inorder/Postorder                        │
│   - Convert Sorted Array to BST                             │
│   - Clone a Tree                                            │
│                                                             │
│   You CREATE a tree from some input                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **TreeNode Return** template:"

```python
def buildOrModify(node):
    # 1) BASE CASE: What to return for empty?
    if not node:
        return None  # Usually None, sometimes a new node
    
    # 2) RECURSIVE: Process children (they return TreeNodes!)
    left_result = buildOrModify(node.left)
    right_result = buildOrModify(node.right)
    
    # 3) MODIFY/BUILD: Update current node or create new
    node.left = left_result   # Reassign children!
    node.right = right_result
    # ... any other modifications ...
    
    # 4) RETURN: Return the (modified) node
    return node
```

---

## Part 4 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the key parts:"

```
┌─────────────────────────────────────────────────────────────┐
│           TREENODE RETURN PATTERN - KEY STEPS               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) BASE CASE: if not node: return None                     │
│     → Empty subtree returns None                            │
│     → For construction: might return a new node instead     │
│                                                             │
│  2) RECURSIVE CALLS: Get modified/built children            │
│     left = buildOrModify(node.left)                         │
│     right = buildOrModify(node.right)                       │
│     → These return TreeNodes (or None)!                     │
│                                                             │
│  3) MODIFY/BUILD:                                           │
│     → For modify: update node.left, node.right, etc.        │
│     → For build: create new TreeNode with children          │
│                                                             │
│  4) RETURN THE NODE:                                        │
│     return node  (or return new_node)                       │
│     → Parent will use this to set ITS children!             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – The Critical Step: REASSIGNING Children

**(Visual: Why reassignment matters.)**

**Instructor:**
"The most important thing in this pattern is **reassigning children**:"

```python
# THIS IS CRITICAL!
node.left = buildOrModify(node.left)
node.right = buildOrModify(node.right)
```

**Why?**
```
┌─────────────────────────────────────────────────────────────┐
│   WHY WE MUST REASSIGN:                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Scenario: Deleting a node                                 │
│                                                             │
│   Before:    Parent → Child → Grandchild                    │
│                                                             │
│   If we delete Child:                                       │
│   - buildOrModify(Child) returns Grandchild                 │
│   - Parent MUST update: parent.left = Grandchild            │
│                                                             │
│   Without reassignment:                                     │
│   - Parent still points to deleted Child!                   │
│   - Tree structure is BROKEN                                │
│                                                             │
│   RULE: Always do node.left = recursive(node.left)          │
│         Always do node.right = recursive(node.right)        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Example 1: Invert Binary Tree (Modify)

**(Visual: Complete code.)**

**Instructor:**
"Let's fill in the template for Invert Binary Tree:"

```python
def invertTree(root):
    # BASE CASE
    if not root:
        return None
    
    # RECURSIVE: Get inverted children
    left_inverted = invertTree(root.left)
    right_inverted = invertTree(root.right)
    
    # MODIFY: Swap children!
    root.left = right_inverted   # Left becomes (inverted) right
    root.right = left_inverted   # Right becomes (inverted) left
    
    # RETURN: Return modified node
    return root
```

**Even shorter:**
```python
def invertTree(root):
    if not root:
        return None
    
    root.left, root.right = invertTree(root.right), invertTree(root.left)
    return root
```

---

## Part 7 – Example 2: Construct Tree (Build)

**(Visual: Construction code.)**

**Instructor:**
"For building a tree from traversals:"

```python
def buildTree(preorder, inorder):
    if not preorder:
        return None
    
    # Root is first element of preorder
    root_val = preorder[0]
    root = TreeNode(root_val)  # CREATE new node!
    
    # Find root in inorder to split left/right
    mid = inorder.index(root_val)
    
    # BUILD children recursively
    root.left = buildTree(preorder[1:mid+1], inorder[:mid])
    root.right = buildTree(preorder[mid+1:], inorder[mid+1:])
    
    # RETURN the built node
    return root
```

**Key difference from modification:**
```
MODIFY: Work with existing nodes
        root.left = invertTree(root.left)
        
BUILD:  Create new nodes
        root = TreeNode(val)
        root.left = buildTree(...)
```

---

## Part 8 – Walkthrough: Invert Binary Tree

**(Visual: Step-by-step trace.)**

```
Tree:       4
           / \
          2   7
         / \
        1   3

Let's trace the inversion:
```

```
invertTree(4)
│ Need inverted children first...
│
├─ invertTree(2)
│  ├─ invertTree(1)
│  │  ├─ invertTree(None) → None
│  │  └─ invertTree(None) → None
│  │  swap: left=None, right=None
│  │  return node 1
│  │
│  └─ invertTree(3)
│     ├─ invertTree(None) → None
│     └─ invertTree(None) → None
│     return node 3
│  
│  swap: left = 3, right = 1  (was left=1, right=3)
│  return node 2 (now has children swapped)
│
└─ invertTree(7)
   ├─ invertTree(None) → None
   └─ invertTree(None) → None
   swap: left=None, right=None
   return node 7

Back at node 4:
  left_inverted = node 2 (with swapped children)
  right_inverted = node 7
  
  swap: left = 7, right = 2  (was left=2, right=7)
  return node 4

Result:     4
           / \
          7   2
             / \
            3   1
```

---

## Part 9 – Visual: How Nodes Flow Up

```
┌─────────────────────────────────────────────────────────────┐
│           HOW TREENODES FLOW BACK UP                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   invertTree(4) ← receives nodes 7 and 2 from children      │
│        │           swaps them: left=7, right=2              │
│        │           returns node 4                           │
│       / \                                                   │
│      ↑   ↑                                                  │
│     /     \                                                 │
│   invertTree(2)    invertTree(7)                            │
│   returns node 2   returns node 7                           │
│   (with 3,1 swapped)                                        │
│                                                             │
│   Each node RETURNS ITSELF after modification               │
│   Parent RECEIVES children and can reassign them            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – When to Use This Pattern

**(Visual: Recognition clues.)**

**Instructor:**
"How do you recognize a TreeNode Return problem?"

```
USE TREENODE RETURN WHEN:

✅ You need to MODIFY tree structure
✅ You need to BUILD a new tree
✅ Keywords:
   • "invert" / "mirror" / "flip"
   • "construct" / "build"
   • "delete" / "remove"
   • "flatten" / "convert"
   • "prune" / "trim"

THE KEY QUESTION:
"Am I changing the tree structure or just reading it?"
If CHANGING → Use TreeNode Return!
```

---

## Part 11 – Modify vs Build: The Difference

**(Visual: Side by side.)**

**Instructor:**
"Let me clarify the two subtypes:"

```
┌─────────────────────────────────────────────────────────────┐
│   MODIFY (change existing)      BUILD (create new)          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   def modify(node):             def build(data):            │
│       if not node:                  if not data:            │
│           return None                   return None         │
│                                                             │
│       # Work with existing          # Create new node       │
│       node.left = modify(...)       node = TreeNode(val)    │
│       node.right = modify(...)      node.left = build(...)  │
│                                     node.right = build(...) │
│                                                             │
│       # Maybe swap, delete, etc     # Assign built children │
│       return node                   return node             │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Examples:                     Examples:                   │
│   - Invert Tree                 - Build from traversals     │
│   - Delete Node                 - Sorted Array to BST       │
│   - Flatten Tree                - Clone Tree                │
│   - Prune Tree                  - Merge Two Trees           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 12 – Special Cases: Returning Different Nodes

**(Visual: Different return scenarios.)**

**Instructor:**
"Sometimes we don't return the same node:"

```python
# SCENARIO 1: Delete a node - return its child
def deleteNode(root, key):
    if root.val == key:
        if not root.left:
            return root.right  # Return right child (skip current)
        if not root.right:
            return root.left   # Return left child (skip current)
        # ... handle two children case

# SCENARIO 2: Prune - return None to remove
def pruneTree(root):
    if should_remove(root):
        return None  # Remove this subtree!
    
    root.left = pruneTree(root.left)
    root.right = pruneTree(root.right)
    return root

# SCENARIO 3: Merge - return new combined node
def mergeTrees(t1, t2):
    if not t1: return t2
    if not t2: return t1
    
    merged = TreeNode(t1.val + t2.val)  # New node!
    merged.left = mergeTrees(t1.left, t2.left)
    merged.right = mergeTrees(t1.right, t2.right)
    return merged
```

---

## Part 13 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each visit does O(1) work (for most problems)
  - Construction from traversals might have O(n) per call 
    for index finding → can be optimized to O(n) total

Space Complexity: O(h) for recursion + O(n) for new nodes
  - h = height of tree (recursion stack)
  - If building new tree: O(n) for new nodes
  - If modifying in place: just O(h) for stack
```

---

## Part 14 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Forgetting to reassign children
   invertTree(node.left)          ← WRONG! Result lost
   node.left = invertTree(node.left)  ← RIGHT!

❌ Mistake 2: Not returning the node
   def invert(node):
       node.left, node.right = node.right, node.left
       invert(node.left)
       invert(node.right)
       # Missing: return node!

❌ Mistake 3: Modifying before recursive calls (when order matters)
   # For flatten to linked list:
   # Must save right BEFORE modifying!
   right = node.right  # Save first!
   node.right = flatten(node.left)
   
❌ Mistake 4: Forgetting base case returns None
   if not node:
       return None  # Don't forget!
```

---

## Part 15 – LeetCode Problems Using This Pattern

| Problem | Type | Key Operation |
|---------|------|---------------|
| LC 226 - Invert Binary Tree | Modify | Swap children |
| LC 105 - Build from Pre+In | Build | Create from traversals |
| LC 106 - Build from In+Post | Build | Create from traversals |
| LC 114 - Flatten to Linked List | Modify | Rewire pointers |
| LC 450 - Delete Node in BST | Modify | Remove and reconnect |
| LC 617 - Merge Two Trees | Build | Combine nodes |
| LC 108 - Sorted Array to BST | Build | Create balanced BST |

---

## Part 16 – Summary

**Instructor:**
"Let's recap the TreeNode Return pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           TREENODE RETURN PATTERN SUMMARY                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    def buildOrModify(node):                                 │
│        if not node: return None                             │
│        node.left = buildOrModify(node.left)   # REASSIGN!   │
│        node.right = buildOrModify(node.right) # REASSIGN!   │
│        # ... modify or build ...                            │
│        return node                                          │
│                                                             │
│  KEY INSIGHT:                                               │
│    We RETURN TreeNodes so parents can update their pointers │
│    Always reassign: node.left = recursive(node.left)        │
│                                                             │
│  TWO TYPES:                                                 │
│    MODIFY: Change existing tree structure                   │
│    BUILD: Create new tree from input                        │
│                                                             │
│  USE WHEN:                                                  │
│    Invert, construct, delete, flatten, prune, merge         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
# TREENODE RETURN TEMPLATE (Modify)
def modify(node):
    if not node:
        return None
    
    node.left = modify(node.left)    # Reassign!
    node.right = modify(node.right)  # Reassign!
    
    # Do modifications (swap, delete, etc.)
    
    return node  # Return (possibly modified) node


# TREENODE RETURN TEMPLATE (Build)
def build(data):
    if not data:
        return None
    
    node = TreeNode(value)  # Create new node
    node.left = build(left_data)
    node.right = build(right_data)
    
    return node  # Return newly built node


# EXAMPLE: Invert Tree
def invertTree(root):
    if not root:
        return None
    root.left, root.right = invertTree(root.right), invertTree(root.left)
    return root
```


