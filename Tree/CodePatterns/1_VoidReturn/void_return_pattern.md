# 🎬 Tutorial Script: Tree Code Pattern 1 — Void Return (Traverse & Collect)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

Today we're learning the **first and most fundamental** tree code pattern: **Void Return**.

Instead of starting with a definition, I want to start with a concrete example, and then we'll turn it into a reusable template you can use for many tree problems."

---

## Part 1 – The Scenario (Concrete Example)

**(Visual appears: binary tree.)**

```
        1
       / \
      2   3
     / \
    4   5

Problem: Collect all values in INORDER (Left → Root → Right)
Expected output: [4, 2, 5, 1, 3]
```

**Instructor:**
"Here's our problem:

> We have this binary tree and we want to collect all node values in inorder traversal order.

Let's think about how we'd solve this."

---

## Part 1.5 – Don't Be Scared of Recursion! (For First-Time Learners)

**(Visual: Friendly explanation.)**

**Instructor:**
"Before we go further, let me demystify something. You might have heard scary words like 'recursion' or 'DFS'. Let me explain what they really mean in plain English."

### What is Recursion? (The Simple Truth)

```
Recursion is just: A FUNCTION THAT CALLS ITSELF

That's it. Nothing magical. Nothing scary.
```

**Think of it like this:**

```
Imagine you're standing in line, and someone asks:
"How many people are in front of you?"

You could:
1. Count everyone yourself (iterative/loop approach)

OR

2. Ask the person in front of you:
   "Hey, how many people are in front of YOU?"
   They ask the person in front of them...
   And so on until someone says "No one!"
   Then everyone passes the count back, adding 1 each time.
   
That's recursion! Each person does a TINY job and delegates the rest.
```

### What is DFS? (Just Recursion on Trees)

```
DFS = Depth First Search

It's just recursion applied to trees.
"Go as DEEP as possible first, then come back up."

     1
    / \
   2   3

DFS on this tree:
1. Start at 1
2. Go deep into 2 (left child first)
3. Can't go deeper? Come back up
4. Now try 3 (right child)
5. Done!
```

### The "dfs" Function is Just a Helper

```python
def dfs(node):    # "dfs" is just a name! You could call it "helper" or "visit"
    ...
    dfs(node.left)   # "Visit my left child the same way"
    dfs(node.right)  # "Visit my right child the same way"
```

**Key insight:**
```
When we write dfs(node.left), we're saying:
"Hey, do the SAME THING to my left child that you did to me."

The function doesn't need to know HOW to solve the whole tree.
It just needs to know HOW TO HANDLE ONE NODE.
Then it delegates the children to copies of itself!
```

### The Mental Model: You Are the Node

```
Imagine YOU are a node in the tree.
Your job is simple:

1. If you don't exist (you're None), say "I'm done" and leave
2. Otherwise:
   a. Maybe record your value somewhere
   b. Tell your left child to do the same thing
   c. Tell your right child to do the same thing
3. When both children are done, YOU are done

That's literally what the code does!
```

```python
def dfs(node):
    if not node:           # "If I don't exist..."
        return             # "...I'm done"
    
    result.append(node.val)  # "Record my value"
    dfs(node.left)           # "Left child, your turn!"
    dfs(node.right)          # "Right child, your turn!"
    # I'm done when my children are done
```

---

## Part 2 – The "Aha!" Moment (Noticing the Pattern)

**Instructor:**
"When we traverse a tree, we need to:

1. **Visit every node** (traverse the whole tree)
2. **Collect values** somewhere (store them in a list)
3. **Return the collected values** at the end

The key insight is:

> The recursive function doesn't need to RETURN anything.
> It just COLLECTS into an external list.

This is what makes it a **Void Return** pattern."

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **Void Return** template you can use for any 'collect values' tree problem:"

```python
def solve(root):
    result = []  # 1) Create EXTERNAL collection
    
    def dfs(node):
        # 2) BASE CASE: stop if null
        if not node:
            return
        
        # 3) PROCESS: Add to result at PRE/IN/POST position
        #    Choose ONE of these positions:
        
        # PREORDER position (before children)
        # result.append(node.val)
        
        dfs(node.left)   # 4) TRAVERSE left
        
        # INORDER position (between children)
        result.append(node.val)
        
        dfs(node.right)  # 5) TRAVERSE right
        
        # POSTORDER position (after children)
        # result.append(node.val)
        
        # 6) END OF FUNCTION: implicitly returns None
        #    Control goes back to WHOEVER CALLED this dfs()
    
    dfs(root)        # 7) Start traversal
    return result    # 8) Return collected values
```

---

## Part 3.5 – What Happens After dfs(node.right)? (The Unwinding)

**(Visual: Call stack diagram.)**

**Instructor:**
"Great question you might have: After `dfs(node.right)` finishes, what happens? There's no explicit `return` statement!"

**Answer:** When a function reaches its end without a `return`, Python **implicitly returns `None`** and control goes back to the **caller**.

```
┌─────────────────────────────────────────────────────────────┐
│   WHAT HAPPENS AT THE END OF dfs()?                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   def dfs(node):                                            │
│       if not node:                                          │
│           return          ← Explicit return (base case)     │
│                                                             │
│       dfs(node.left)      ← Call child, WAIT for it         │
│       result.append(...)                                    │
│       dfs(node.right)     ← Call child, WAIT for it         │
│                                                             │
│       # ──────────────────────────────────────────────────  │
│       # END OF FUNCTION REACHED                             │
│       # Python does: return None (implicitly)               │
│       # Control goes BACK to whoever called this dfs()      │
│       # ──────────────────────────────────────────────────  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Visual: The Call Stack "Unwinding"**

```
Tree:       1
           / \
          2   3

GOING DOWN (building the call stack):
┌─────────────────────┐
│ dfs(1) starts       │ ← "I'll call dfs(2), wait for it"
│   dfs(2) starts     │ ← "I'll call dfs(None), wait for it"
│     dfs(None)       │ ← "Base case! return"
│   dfs(2) resumes    │ ← "dfs(None) is done, now append 2"
│     append(2)       │
│     dfs(None)       │ ← "Base case! return"
│   dfs(2) ENDS       │ ← "Nothing more to do, return None"
│ dfs(1) resumes      │ ← "dfs(2) is done, now append 1"
│   append(1)         │
│   dfs(3) starts     │ ← ... same process ...
└─────────────────────┘

COMING BACK UP (unwinding the call stack):
When dfs(2) ends → control returns to dfs(1)
When dfs(1) ends → control returns to solve()
```

**Key Insight:**
```
Each dfs() call WAITS for its child calls to complete.
When a child completes (returns), the parent RESUMES 
from where it left off, then eventually ends itself.

This is how recursion naturally "unwinds" back up the tree!
```

---

## Part 4 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the 7 steps of this pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           VOID RETURN PATTERN - 7 STEPS                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) CREATE external collection (result = [])                │
│     → This lives OUTSIDE the recursive function             │
│                                                             │
│  2) BASE CASE: if not node: return                          │
│     → Stop recursion when we hit null                       │
│                                                             │
│  3) PROCESS: result.append(node.val)                        │
│     → Add current node's value to result                    │
│     → Position determines traversal order!                  │
│                                                             │
│  4) TRAVERSE LEFT: dfs(node.left)                           │
│     → Recursively process left subtree                      │
│                                                             │
│  5) TRAVERSE RIGHT: dfs(node.right)                         │
│     → Recursively process right subtree                     │
│                                                             │
│  6) START: dfs(root)                                        │
│     → Kick off the traversal from root                      │
│                                                             │
│  7) RETURN: return result                                   │
│     → Return the collected values                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – The Key: Where You PROCESS Determines Order

**(Visual: Three positions.)**

**Instructor:**
"The most important thing to understand is:

> **WHERE you put `result.append(node.val)` determines the traversal order!**"

```python
def dfs(node):
    if not node:
        return
    
    # ═══════════════════════════════════════════
    # PREORDER: Process BEFORE children
    # result.append(node.val)  ← PUT HERE for preorder
    # ═══════════════════════════════════════════
    
    dfs(node.left)
    
    # ═══════════════════════════════════════════
    # INORDER: Process BETWEEN children
    # result.append(node.val)  ← PUT HERE for inorder
    # ═══════════════════════════════════════════
    
    dfs(node.right)
    
    # ═══════════════════════════════════════════
    # POSTORDER: Process AFTER children
    # result.append(node.val)  ← PUT HERE for postorder
    # ═══════════════════════════════════════════
```

**Same template, different position = different traversal order!**

---

## Part 6 – Filled-In Example: Inorder Traversal

**(Visual: Complete code.)**

**Instructor:**
"Let's fill in the template for our specific problem: Inorder Traversal."

```python
def inorderTraversal(root):
    result = []  # Step 1: External collection
    
    def dfs(node):
        if not node:  # Step 2: Base case
            return
        
        dfs(node.left)         # Step 4: Traverse left FIRST
        result.append(node.val)  # Step 3: Process IN THE MIDDLE
        dfs(node.right)        # Step 5: Traverse right LAST
    
    dfs(root)      # Step 6: Start traversal
    return result  # Step 7: Return result
```

**Instructor:**
"Notice how short and clean this is! The template structure stays the same — we just chose to put `append` in the INORDER position."

---

## Part 7 – Walkthrough with Our Example

**(Visual: Step-by-step trace.)**

```
Tree:       1
           / \
          2   3
         / \
        4   5

result = []  (starts empty)

dfs(1): go left first...
  dfs(2): go left first...
    dfs(4): go left first...
      dfs(None): return (base case)
    Back at 4: append 4 → result = [4]
    dfs(4): go right...
      dfs(None): return (base case)
  Back at 2: append 2 → result = [4, 2]
  dfs(2): go right...
    dfs(5): go left first...
      dfs(None): return (base case)
    Back at 5: append 5 → result = [4, 2, 5]
    dfs(5): go right...
      dfs(None): return (base case)
Back at 1: append 1 → result = [4, 2, 5, 1]
dfs(1): go right...
  dfs(3): go left first...
    dfs(None): return (base case)
  Back at 3: append 3 → result = [4, 2, 5, 1, 3]
  dfs(3): go right...
    dfs(None): return (base case)

Done! result = [4, 2, 5, 1, 3]
```

---

## Part 8 – When to Use This Pattern

**(Visual: Recognition clues.)**

**Instructor:**
"How do you know when to use the Void Return pattern? Look for these clues:"

```
USE VOID RETURN PATTERN WHEN:

✅ You need to COLLECT values from all/some nodes
✅ You need to TRAVERSE the entire tree
✅ You need to STORE results in a list/set/map
✅ The problem says "return all X" or "find all X"

EXAMPLES:
• "Return all node values in X order"
• "Find all root-to-leaf paths"
• "Collect all leaf nodes"
• "Find all nodes at depth K"
```

---

## Part 9 – Variations of This Pattern

**(Visual: Different customizations.)**

**Instructor:**
"The template can be customized in several ways:"

### Variation 1: Different Processing
```python
# Collect only leaves
if not node.left and not node.right:
    result.append(node.val)
```

### Variation 2: Collect with Conditions
```python
# Collect nodes greater than X
if node.val > X:
    result.append(node.val)
```

### Variation 3: Build Paths (with backtracking)
```python
def dfs(node, path):
    if not node:
        return
    path.append(node.val)
    
    if not node.left and not node.right:  # Leaf
        result.append(path[:])  # Copy the path!
    
    dfs(node.left, path)
    dfs(node.right, path)
    path.pop()  # BACKTRACK!
```

---

## Part 9.5 – Deep Dive: Backtracking Pattern (Collect Paths)

**(Visual: Backtracking as a pattern.)**

**Instructor:**
"Let me teach you backtracking step by step. This is actually a **sub-pattern** of Void Return — it's so common that it deserves its own template."

---

### Step 1: What is Backtracking? (The Goal)

```
┌─────────────────────────────────────────────────────────────┐
│   BACKTRACKING = Collecting PATHS, not just values          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Regular Void Return:                                      │
│   → Collect individual VALUES: [1, 2, 3, 4, 5]              │
│                                                             │
│   Backtracking:                                             │
│   → Collect complete PATHS: [[1,2,4], [1,2,5], [1,3]]       │
│                                                             │
│   A PATH = the journey from root to some destination        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**When do you need paths?**
```
✅ "Find all root-to-leaf paths"
✅ "Find paths that sum to X"
✅ "Return the path to a specific node"
✅ Any problem mentioning "path" or "route"
```

---

### Step 2: A Path Starts from Root — We Append as We Go

```
Tree:       1
           / \
          2   3
         /
        4

To collect the path [1, 2, 4], we need to BUILD IT as we traverse:
```

```python
def dfs(node, path):    # path = the journey so far
    path.append(node.val)  # Add THIS node to the journey
    
    # Now path contains the route from root to current node!
```

**How path builds up:**
```
dfs(1, [])     → path.append(1) → path = [1]
dfs(2, [1])    → path.append(2) → path = [1, 2]
dfs(4, [1,2])  → path.append(4) → path = [1, 2, 4]
```

**Key insight: We append FIRST, then explore children.**

---

### Step 3: We Save the Path Based on a Condition

```python
def dfs(node, path):
    path.append(node.val)
    
    # CONDITION: When do we save this path?
    if not node.left and not node.right:  # Example: reached a leaf
        result.append(path[:])  # Save a COPY of the path!
    
    dfs(node.left, path)
    dfs(node.right, path)
```

**Common conditions:**
```
• Reached a leaf:        if not node.left and not node.right
• Path sum equals target: if current_sum == target
• Found specific node:    if node.val == target
```

---

### Step 4: Python Lists Are SHARED (The Tricky Part!)

**This is where most people get confused.**

```
┌─────────────────────────────────────────────────────────────┐
│   PYTHON FACT: When you pass a list to a function,         │
│   you're passing a REFERENCE, not a copy.                  │
│                                                             │
│   This means: Changes in one place affect EVERYWHERE!       │
└─────────────────────────────────────────────────────────────┘
```

**Let's see what goes WRONG without handling this:**

```
Tree:       1
           / \
          2   3

We want: [[1, 2], [1, 3]]
```

```python
# WRONG CODE (no backtracking)
def dfs(node, path):
    path.append(node.val)
    if is_leaf(node):
        result.append(path)  # Saving reference!
    dfs(node.left, path)
    dfs(node.right, path)
```

**What happens step by step:**
```
┌─────────────────────────────────────────────────────────────┐
│   dfs(1, [])                                                │
│   path.append(1) → path = [1]                               │
│                                                             │
│   ├─→ dfs(2, [1])                                           │
│   │   path.append(2) → path = [1, 2]                        │
│   │   Leaf! Save path → result = [[1, 2]]                   │
│   │   Return to parent...                                   │
│   │                                                         │
│   │   ⚠️ BUT path is STILL [1, 2]!                          │
│   │                                                         │
│   └─→ dfs(3, [1, 2])   ← WRONG! Should be [1]               │
│       path.append(3) → path = [1, 2, 3]                     │
│       Leaf! Save path → result = [[1,2,3], [1,2,3]]         │
│                                                             │
│   WRONG ANSWER!                                             │
└─────────────────────────────────────────────────────────────┘
```

**Why?** Because when we went left to node 2, we added 2 to path. When we came back and went right to node 3, that 2 was still there!

---

### Step 5: The Solution — Pop When Leaving (BACKTRACK!)

**The fix is simple: UNDO what you did when you LEAVE a node.**

```python
def dfs(node, path):
    path.append(node.val)   # ADD when ENTERING
    
    if is_leaf(node):
        result.append(path[:])
    
    dfs(node.left, path)
    dfs(node.right, path)
    
    path.pop()              # REMOVE when LEAVING ← THIS IS BACKTRACKING!
```

**Now what happens:**
```
┌─────────────────────────────────────────────────────────────┐
│   dfs(1, [])                                                │
│   path.append(1) → path = [1]                               │
│                                                             │
│   ├─→ dfs(2, [1])                                           │
│   │   path.append(2) → path = [1, 2]                        │
│   │   Leaf! Save copy → result = [[1, 2]]                   │
│   │   path.pop() → path = [1]  ← BACKTRACK!                 │
│   │   Return to parent...                                   │
│   │                                                         │
│   │   ✅ path is now [1] again!                             │
│   │                                                         │
│   └─→ dfs(3, [1])   ← CORRECT!                              │
│       path.append(3) → path = [1, 3]                        │
│       Leaf! Save copy → result = [[1, 2], [1, 3]]           │
│       path.pop() → path = [1]                               │
│                                                             │
│   path.pop() → path = []                                    │
│   CORRECT ANSWER! [[1, 2], [1, 3]]                          │
└─────────────────────────────────────────────────────────────┘
```

### Why Does pop() Work? (Understanding the Flow)

```
┌─────────────────────────────────────────────────────────────┐
│   THE RULE:                                                 │
│                                                             │
│   Every node that APPENDS must also POP                     │
│                                                             │
│   append() happens when we ENTER a node (going DOWN)        │
│   pop() happens when we LEAVE a node (going UP)             │
│                                                             │
│   Since we visit every node exactly once,                   │
│   every append has exactly one matching pop!                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### The Key Insight: What Happens When We Return to Parent?

```
┌─────────────────────────────────────────────────────────────┐
│   WHEN WE ENTER A NODE:                                     │
│   → We append the node's value to path                      │
│                                                             │
│   WHEN WE GO TO A CHILD:                                    │
│   → The child adds ITS value to the path                    │
│   → The path grows                                          │
│                                                             │
│   WHEN WE COME BACK FROM CHILD TO PARENT:                   │
│   → The child POPS what it added                            │
│   → The path is now EXACTLY as it was before we went down!  │
│                                                             │
│   WHEN WE GO TO THE OTHER CHILD:                            │
│   → The path is CLEAN (same as the first child received)    │
│   → The second child gets the same starting path!           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Visual example:**
```
Tree:       1
           / \
          2   3

We are at node 1, path = [1]
We want to explore both children (2 and 3)

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   At node 1:  path = [1]                                    │
│                                                             │
│   ┌─── Go to LEFT child (node 2) ───┐                       │
│   │                                 │                       │
│   │  Node 2 appends: path = [1, 2]  │                       │
│   │  (do work at node 2)            │                       │
│   │  Node 2 pops:    path = [1]     │ ← Child cleans up!    │
│   │                                 │                       │
│   └─────────────────────────────────┘                       │
│                                                             │
│   Back at node 1:  path = [1]  ← Same as before! ✓          │
│                                                             │
│   ┌─── Go to RIGHT child (node 3) ──┐                       │
│   │                                 │                       │
│   │  Node 3 appends: path = [1, 3]  │ ← Gets clean path!    │
│   │  (do work at node 3)            │                       │
│   │  Node 3 pops:    path = [1]     │                       │
│   │                                 │                       │
│   └─────────────────────────────────┘                       │
│                                                             │
│   Both children received the SAME starting path [1]!        │
│   Because each child cleans up after itself.                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**The golden rule:**
```
EACH CHILD IS RESPONSIBLE FOR UNDOING WHAT IT ADDED.

When child finishes and returns to parent,
the path should be EXACTLY as the parent gave it.

This ensures the NEXT child starts with a clean slate!
```

---

### 🌟 THE UNIVERSAL BACKTRACKING PRINCIPLE

This rule applies to **ALL backtracking problems**, not just trees!

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   IN BACKTRACKING:                                          │
│                                                             │
│   Every node/element/choice is responsible for:             │
│                                                             │
│   1. ADDING its information when we ENTER/CHOOSE it         │
│   2. REMOVING its information when we LEAVE/UNCHOOSE it     │
│                                                             │
│   ADD → EXPLORE → REMOVE                                    │
│                                                             │
│   This is true for EVERY backtracking problem!              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Examples across different problem types:**

```
┌────────────────────┬──────────────────┬──────────────────┐
│ Problem Type       │ ADD              │ REMOVE (undo)    │
├────────────────────┼──────────────────┼──────────────────┤
│ Tree Paths         │ path.append(node)│ path.pop()       │
├────────────────────┼──────────────────┼──────────────────┤
│ Subsets            │ subset.append(x) │ subset.pop()     │
├────────────────────┼──────────────────┼──────────────────┤
│ Permutations       │ perm.append(x)   │ perm.pop()       │
│                    │ used[i] = True   │ used[i] = False  │
├────────────────────┼──────────────────┼──────────────────┤
│ Combinations       │ combo.append(x)  │ combo.pop()      │
├────────────────────┼──────────────────┼──────────────────┤
│ N-Queens           │ place queen      │ remove queen     │
│                    │ board[r][c] = 'Q'│ board[r][c] = '.'│
├────────────────────┼──────────────────┼──────────────────┤
│ Sudoku             │ grid[r][c] = num │ grid[r][c] = 0   │
├────────────────────┼──────────────────┼──────────────────┤
│ Word Search        │ visited[r][c]=T  │ visited[r][c]=F  │
└────────────────────┴──────────────────┴──────────────────┘
```

**The pattern is ALWAYS the same:**
```python
def backtrack(state):
    if <goal reached>:
        save result
        return
    
    for choice in choices:
        # 1. ADD: Make the choice
        state.add(choice)
        
        # 2. EXPLORE: Try this path
        backtrack(state)
        
        # 3. REMOVE: Undo the choice (so next choice starts clean!)
        state.remove(choice)
```

**Why this works:**
```
Choice A adds → Explore A → Choice A removes
                             ↓
                    State is back to original!
                             ↓
Choice B adds → Explore B → Choice B removes
                             ↓
                    State is back to original!
                             ↓
Choice C adds → ...

Each choice gets the SAME starting state!
```

**Think of it as entering and leaving a room:**
```
Enter node 1: path = [1]
  Enter node 2: path = [1, 2]
    (do work)
  Leave node 2: path = [1]      ← pop undoes the append
  Enter node 3: path = [1, 3]   ← starts with same [1] as node 2 did!
    (do work)
  Leave node 3: path = [1]      ← pop undoes the append
Leave node 1: path = []         ← pop undoes the append
```

---

### Bonus: Why path[:] Instead of path?

```python
result.append(path)    # ← WRONG: saves a REFERENCE
result.append(path[:]) # ← RIGHT: saves a COPY
```

**Demo:**
```python
path = [1, 2]
result.append(path)   # result[0] POINTS TO path
path.pop()            # path = [1], result = [[1]]  ← BOTH changed!

path = [1, 2]
result.append(path[:])  # result[0] is a COPY
path.pop()              # path = [1], result = [[1, 2]]  ← Only path changed!
```

**Visual:**
```
result.append(path)        result.append(path[:])

result[0] ────┐             result[0] → [1, 2] (copy)
              ↓
path ───────→ [1, 2]        path → [1, 2] (original)

Same memory!                Different memory!
```

---

## 🎯 TWO WAYS TO DO BACKTRACKING (Pick One!)

There are **two approaches** to backtracking. Both work! Choose the one you understand better.

---

### Approach 1: MUTABLE (with pop) — Modify & Undo

```python
def solve(root):
    result = []
    
    def dfs(node, path):
        if not node:
            return
        
        path.append(node.val)      # ← MODIFY the same list
        
        if <condition>:
            result.append(path[:])  # Save a COPY
        
        dfs(node.left, path)        # Pass SAME list
        dfs(node.right, path)       # Pass SAME list
        
        path.pop()                  # ← UNDO the modification
    
    dfs(root, [])
    return result
```

**How it works:**
```
One list, shared everywhere.
Append when entering → Explore → Pop when leaving.
Must pop() to undo changes!
```

---

### Approach 2: IMMUTABLE (no pop) — Create New Lists

```python
def solve(root):
    result = []
    
    def dfs(node, path):
        if not node:
            return
        
        path = path + [node.val]    # ← CREATE NEW list (doesn't modify original!)
        
        if <condition>:
            result.append(path)      # No need for [:] - it's already a new list!
        
        dfs(node.left, path)         # Pass the new list
        dfs(node.right, path)        # Pass the new list
        
        # NO POP NEEDED! We never modified the original list!
    
    dfs(root, [])
    return result
```

**How it works:**
```
Each call gets its OWN copy of the list.
path + [node.val] creates a NEW list, doesn't change the original.
No pop needed because nothing was modified!
```

---

### Why Does Approach 2 Work? (The Key Insight)

```
┌─────────────────────────────────────────────────────────────┐
│   THE DIFFERENCE: += vs +                                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   path.append(x)  →  Modifies THE SAME list                 │
│   path += [x]     →  Modifies THE SAME list                 │
│                                                             │
│   path = path + [x]  →  Creates a NEW list!                 │
│                         Original path is UNCHANGED!          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Demo in Python:**
```python
# MUTABLE (modifies original)
path = [1, 2]
path.append(3)      # path is now [1, 2, 3]

# MUTABLE (also modifies original)
path = [1, 2]
path += [3]         # path is now [1, 2, 3]

# IMMUTABLE (creates new list!)
path = [1, 2]
new_path = path + [3]  # new_path = [1, 2, 3], path is STILL [1, 2]!
```

---

### Visual: How Approach 2 Works (Larger Tree)

```
Tree:           1
              /   \
             2     3
            / \     \
           4   5     6
          /
         7

Expected paths (root to leaf):
  [1, 2, 4, 7]
  [1, 2, 5]
  [1, 3, 6]
```

**Step-by-step trace with Approach 2 (no pop):**

```
dfs(1, [])
│ path = [] + [1] = [1]              ← NEW list [1]
│
├─→ dfs(2, [1])                       (parent's path is [1])
│   │ path = [1] + [2] = [1, 2]       ← NEW list [1,2]
│   │
│   ├─→ dfs(4, [1, 2])                (parent's path is [1,2])
│   │   │ path = [1,2] + [4] = [1,2,4]  ← NEW list [1,2,4]
│   │   │
│   │   ├─→ dfs(7, [1, 2, 4])
│   │   │   │ path = [1,2,4] + [7] = [1,2,4,7]  ← NEW list
│   │   │   │ Leaf! Save [1, 2, 4, 7] ✓
│   │   │   │ Return
│   │   │   
│   │   │   Back at dfs(4), path is STILL [1,2,4]! ✓
│   │   │   (dfs(7) created its own list, didn't touch ours)
│   │   │
│   │   └─→ dfs(None) → return
│   │   
│   │   Back at dfs(2), path is STILL [1,2]! ✓
│   │
│   └─→ dfs(5, [1, 2])                (path is [1,2])
│       │ path = [1,2] + [5] = [1,2,5]  ← NEW list [1,2,5]
│       │ Leaf! Save [1, 2, 5] ✓
│       │ Return
│   
│   Back at dfs(1), path is STILL [1]! ✓
│
└─→ dfs(3, [1])                       (path is [1])
    │ path = [1] + [3] = [1, 3]       ← NEW list [1,3]
    │
    └─→ dfs(6, [1, 3])
        │ path = [1,3] + [6] = [1,3,6]  ← NEW list
        │ Leaf! Save [1, 3, 6] ✓
        │ Return

Result: [[1,2,4,7], [1,2,5], [1,3,6]] ✓
```

**THE KEY INSIGHT:**
```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Each dfs() call has its OWN path variable!                │
│                                                             │
│   dfs(1) has path = [1]                                     │
│   dfs(2) has path = [1, 2]         (different variable!)    │
│   dfs(4) has path = [1, 2, 4]      (different variable!)    │
│   dfs(7) has path = [1, 2, 4, 7]   (different variable!)    │
│                                                             │
│   When dfs(7) returns, dfs(4)'s path is still [1,2,4]       │
│   Because they are DIFFERENT LISTS in memory!               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Memory view:**
```
dfs(1):  path ──→ [1]
                    
dfs(2):  path ──→ [1, 2]         (NEW list, copied from parent + added 2)
                    
dfs(4):  path ──→ [1, 2, 4]      (NEW list, copied from parent + added 4)
                    
dfs(7):  path ──→ [1, 2, 4, 7]   (NEW list, copied from parent + added 7)

Four SEPARATE lists in memory! Changes to one don't affect others!
```

---

### Compare: Same Tree with Approach 1 (with pop)

```
dfs(1, [])
│ path.append(1) → path = [1]        ← SAME list everywhere!
│
├─→ dfs(2, path)                      path = [1]
│   │ path.append(2) → path = [1, 2]
│   │
│   ├─→ dfs(4, path)                  path = [1, 2]
│   │   │ path.append(4) → path = [1, 2, 4]
│   │   │
│   │   ├─→ dfs(7, path)
│   │   │   │ path.append(7) → path = [1, 2, 4, 7]
│   │   │   │ Leaf! Save path[:] = [1, 2, 4, 7] ✓
│   │   │   │ path.pop() → path = [1, 2, 4]  ← UNDO!
│   │   │   │ Return
│   │   │
│   │   │ path.pop() → path = [1, 2]  ← UNDO!
│   │
│   └─→ dfs(5, path)                  path = [1, 2]
│       │ path.append(5) → path = [1, 2, 5]
│       │ Leaf! Save path[:] = [1, 2, 5] ✓
│       │ path.pop() → path = [1, 2]  ← UNDO!
│   
│   path.pop() → path = [1]  ← UNDO!
│
└─→ dfs(3, path)                      path = [1]
    │ path.append(3) → path = [1, 3]
    │
    └─→ dfs(6, path)
        │ path.append(6) → path = [1, 3, 6]
        │ Leaf! Save path[:] = [1, 3, 6] ✓
        │ path.pop() → path = [1, 3]  ← UNDO!
    
    path.pop() → path = [1]  ← UNDO!

path.pop() → path = []  ← UNDO!

Result: [[1,2,4,7], [1,2,5], [1,3,6]] ✓
```

**Memory view:**
```
ALL dfs() calls share the SAME list:

path ──→ [1, 2, 4, 7]   (ONE list, constantly modified)

Everyone modifies it, everyone must clean up after themselves!
That's why pop() is required!
```

---

### Summary: The Core Difference

```
┌─────────────────────────────────────────────────────────────┐
│   APPROACH 1 (MUTABLE)          APPROACH 2 (IMMUTABLE)      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ONE list, SHARED              MANY lists, INDEPENDENT     │
│                                                             │
│   path ──→ [...]                dfs(1): path ──→ [1]        │
│      ↑                          dfs(2): path ──→ [1,2]      │
│   everyone uses this!           dfs(4): path ──→ [1,2,4]    │
│                                                             │
│   Must UNDO changes (pop)       No cleanup needed           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Side-by-Side Comparison

```
┌─────────────────────────────────────────────────────────────┐
│   APPROACH 1 (MUTABLE)          APPROACH 2 (IMMUTABLE)      │
│   with pop()                    no pop()                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   path.append(node.val)         path = path + [node.val]    │
│   ...                           ...                         │
│   dfs(left, path)               dfs(left, path)             │
│   dfs(right, path)              dfs(right, path)            │
│   path.pop()                    (nothing!)                  │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   result.append(path[:])        result.append(path)         │
│   (need copy!)                  (already a new list)        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Memory: O(h) for path         Memory: O(h²) total         │
│   (reuses one list)             (creates many lists)        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Speed: Faster                 Speed: Slightly slower      │
│   (no list creation)            (creates new lists)         │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Easier to mess up             Easier to understand        │
│   (forget pop = bug!)           (no cleanup needed)         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Which One Should You Use?

```
For LEARNING:     Use Approach 2 (Immutable) - easier to understand
For INTERVIEWS:   Either works - use what you're comfortable with
For PERFORMANCE:  Use Approach 1 (Mutable) - more memory efficient

Most interviewers don't care which you use, as long as it's correct!
```

---

### Your Code Example Analyzed

Looking at your code:
```python
l = l + [nums[index]]    # ← Creates NEW list (Approach 2!)
dfs(index+1, l[:])       # ← Passing a copy
l.pop(-1)                # ← This is actually unnecessary with "l = l + ..."!
dfs(index+1, l[:])
```

You're **mixing both approaches**! The `l = l + [...]` already creates a new list, so the `pop(-1)` isn't actually needed for that part. But the logic still works because you're exploring "include this element" vs "don't include this element".

---

## 🎯 BACKTRACKING TEMPLATES (Choose One!)

### Template A: Mutable (with pop)
```python
def solve(root):
    result = []
    
    def dfs(node, path):
        if not node:
            return
        
        path.append(node.val)       # MODIFY
        
        if <condition>:
            result.append(path[:])  # COPY needed!
        
        dfs(node.left, path)
        dfs(node.right, path)
        
        path.pop()                  # UNDO
    
    dfs(root, [])
    return result
```

### Template B: Immutable (no pop)
```python
def solve(root):
    result = []
    
    def dfs(node, path):
        if not node:
            return
        
        path = path + [node.val]    # NEW LIST
        
        if <condition>:
            result.append(path)     # No copy needed!
        
        dfs(node.left, path)
        dfs(node.right, path)
        
        # No pop!
    
    dfs(root, [])
    return result
```

**The 7 Steps:**
```
┌─────────────────────────────────────────────────────────────┐
│           BACKTRACKING TEMPLATE - 7 STEPS                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) result = []         → Store all valid paths             │
│  2) if not node: return → Base case                         │
│  3) path.append(...)    → Add node when ENTERING            │
│  4) if <condition>      → Check if path is valid            │
│     result.append(path[:]) → Save a COPY                    │
│  5) dfs(children)       → Explore all options               │
│  6) path.pop()          → Remove node when LEAVING          │
│  7) dfs(root, [])       → Start with empty path             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Quick Comparison: Void Return vs Backtracking

```
┌─────────────────────────────────────────────────────────────┐
│   VOID RETURN (Simple)        BACKTRACKING (Path-based)     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   def dfs(node):              def dfs(node, path):          │
│       if not node:                if not node:              │
│           return                      return                │
│                                                             │
│       result.append(node.val)     path.append(node.val)     │
│                                                             │
│       dfs(node.left)              if <condition>:           │
│       dfs(node.right)                 result.append(path[:])│
│                                                             │
│                                   dfs(node.left, path)      │
│                                   dfs(node.right, path)     │
│                                                             │
│                                   path.pop()  ← BACKTRACK   │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   Collects: VALUES              Collects: PATHS             │
│   Output: [1, 2, 3, 4]          Output: [[1,2], [1,3]]      │
│   No path tracking              Tracks journey from root    │
│   No pop needed                 pop() required!             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### LeetCode Problems Using Backtracking Template

| Problem | Condition to Save Path |
|---------|------------------------|
| LC 257 - Binary Tree Paths | `if not node.left and not node.right` (leaf) |
| LC 113 - Path Sum II | `if not node.left and not node.right and sum == target` |
| LC 129 - Sum Root to Leaf | Compute number from path at leaf |
| LC 1022 - Sum of Root to Leaf Binary | Similar to LC 129 |

---

### Mental Model: The Breadcrumb Trail

```
Think of path as a TRAIL OF BREADCRUMBS:

          1           You're at node 1
         /           path = [1]
        2             Drop a breadcrumb at 1
       /              Go to 2, drop breadcrumb → path = [1, 2]
      4               Go to 4, drop breadcrumb → path = [1, 2, 4]
                      Dead end! Record the path
                      Pick up breadcrumb at 4 → path = [1, 2]
                      Pick up breadcrumb at 2 → path = [1]
                      Go to 3, drop breadcrumb → path = [1, 3]
                      ...

ENTERING a node: drop a breadcrumb (append)
LEAVING a node:  pick it up (pop)
```

---

## Part 10 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node exactly once
  - n = number of nodes

Space Complexity: O(h) for recursion stack
  - h = height of tree
  - Worst case (skewed): O(n)
  - Best case (balanced): O(log n)
  
  Plus O(n) for the result array
```

---

## Part 11 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Forgetting base case
   if not node: return  ← ALWAYS need this!

❌ Mistake 2: Returning value from dfs
   The function returns NOTHING (void)
   Results go into the EXTERNAL list

❌ Mistake 3: Wrong position for processing
   Pre/In/Post order depends on WHERE you append

❌ Mistake 4: Not copying path in backtracking
   result.append(path[:])  ← Need to copy!
   result.append(path)     ← WRONG! Same reference
```

---

## Part 12 – LeetCode Problems Using This Pattern

| Problem | Customization |
|---------|---------------|
| LC 94 - Inorder Traversal | Process in INORDER position |
| LC 144 - Preorder Traversal | Process in PREORDER position |
| LC 145 - Postorder Traversal | Process in POSTORDER position |
| LC 257 - Binary Tree Paths | Build paths with backtracking |
| LC 113 - Path Sum II | Paths with sum condition |
| LC 872 - Leaf-Similar Trees | Collect only leaves |

---

## Part 13 – Summary

**Instructor:**
"Let's recap the Void Return pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           VOID RETURN PATTERN SUMMARY                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    result = []                                              │
│    def dfs(node):                                           │
│        if not node: return                                  │
│        # process at PRE/IN/POST position                    │
│        dfs(node.left)                                       │
│        dfs(node.right)                                      │
│    dfs(root)                                                │
│    return result                                            │
│                                                             │
│  KEY INSIGHT:                                               │
│    Function returns NOTHING                                 │
│    Results collected in EXTERNAL variable                   │
│    Position of processing = traversal order                 │
│                                                             │
│  USE WHEN:                                                  │
│    "Collect all X" or "Find all X"                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
# VOID RETURN TEMPLATE
def solve(root):
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

