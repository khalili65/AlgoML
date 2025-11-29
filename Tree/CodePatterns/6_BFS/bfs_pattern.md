# 🎬 Tutorial Script: Tree Code Pattern 6 — BFS (Level-by-Level Traversal)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Welcome back! Today we're learning the **sixth and final tree code pattern**: **BFS** (Breadth-First Search).

While all previous patterns used recursion (DFS), BFS uses a **queue** to process nodes **level by level**. This is perfect for problems that care about tree levels!

Let's see how it works!"

---

## Part 1 – From Graph to Tree: The Big Picture

**(Visual: Graph vs Tree traversal.)**

**Instructor:**
"If you've learned DFS and BFS on graphs, trees are just a simplified version! Let me show you the connection."

---

### STEP 1: DFS and BFS on GRAPH (Complete Code)

**Graph Example:**
```
    0 ─── 1
    │     │
    2 ─── 3 ─── 4

Adjacency list:
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2, 4],
    4: [3]
}
```

**DFS on Graph (using Stack):**
```python
def dfs_graph(graph, start):
    result = []
    visited = set()           # NEEDED! Graphs have cycles
    stack = []
    
    stack.append(start)       # 1. Initialize stack
    
    while stack:              # 2. While stack not empty
        node = stack.pop()    # 3. Pop top (LIFO)
        
        if node in visited:   # Skip if already visited
            continue
        
        visited.add(node)     # Mark as visited
        result.append(node)   # Process node
        
        # 4. Push unvisited neighbors
        for neighbor in graph[node]:
            if neighbor not in visited:
                stack.append(neighbor)
    
    return result

# DFS from node 0: [0, 2, 3, 4, 1] (order may vary)
```

**BFS on Graph (using List as Queue):**
```python
def bfs_graph(graph, start):
    result = []
    visited = set()           # NEEDED! Graphs have cycles
    queue = []
    
    queue.append(start)       # 1. Initialize queue
    visited.add(start)        # Mark start as visited
    
    while queue:              # 2. While queue not empty
        node = queue.pop(0)   # 3. Pop front (FIFO) - simple list!
        result.append(node)   # Process node
        
        # 4. Enqueue unvisited neighbors
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result

# BFS from node 0: [0, 1, 2, 3, 4]
```

**The Graph Magic Spells:**
```
┌─────────────────────────────────────────────────────────────┐
│   DFS on GRAPH:                                             │
│                                                             │
│   1. stack.append(start)                                    │
│   2. while stack:                                           │
│      3. node = stack.pop()          ← LIFO (Last In First Out)
│      4. if node in visited: continue                        │
│      5. visited.add(node)                                   │
│      6. process(node)                                       │
│      7. for neighbor in graph[node]:                        │
│            if neighbor not in visited:                      │
│                stack.append(neighbor)                       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   BFS on GRAPH:                                             │
│                                                             │
│   1. queue.append(start)                                    │
│   2. visited.add(start)                                     │
│   3. while queue:                                           │
│      4. node = queue.pop(0)         ← FIFO (First In First Out)
│      5. process(node)                                       │
│      6. for neighbor in graph[node]:                        │
│            if neighbor not in visited:                      │
│                visited.add(neighbor)                        │
│                queue.append(neighbor)                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### STEP 2: Simplify for TREE (No Visited Needed!)

**Why can we remove "visited" for trees?**
```
┌─────────────────────────────────────────────────────────────┐
│   GRAPH:                           TREE:                    │
│                                                             │
│       0 ─── 1                          1                    │
│       │     │     ← has cycle!        / \                   │
│       2 ─── 3                        2   3    ← no cycles!  │
│                                     / \                     │
│   Can visit same node              4   5                    │
│   multiple times!                                           │
│   NEED visited set.                Each node has ONE parent │
│                                    We only go DOWN (to children)
│                                    NO visited needed!       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**DFS on Tree (using Stack):**
```python
def dfs_tree(root):
    if not root:
        return []
    
    result = []
    stack = []
    # NO visited set needed!
    
    stack.append(root)           # 1. Initialize stack
    
    while stack:                 # 2. While stack not empty
        node = stack.pop()       # 3. Pop top (LIFO)
        result.append(node.val)  # 4. Process node
        
        # 5. Push children (RIGHT first, then LEFT)
        #    So LEFT is popped first (LIFO order)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    
    return result

# Tree:     1
#          / \
#         2   3
#        / \
#       4   5
#
# Stack trace:
# []              → append 1
# [1]             → pop 1, append 3, append 2
# [3, 2]          → pop 2, append 5, append 4
# [3, 5, 4]       → pop 4
# [3, 5]          → pop 5
# [3]             → pop 3
# []              → done!
#
# Result: [1, 2, 4, 5, 3] (preorder DFS!)
```

**BFS on Tree (using List as Queue):**
```python
def bfs_tree(root):
    if not root:
        return []
    
    result = []
    queue = []
    # NO visited set needed!
    
    queue.append(root)           # 1. Initialize queue
    
    while queue:                 # 2. While queue not empty
        node = queue.pop(0)      # 3. Pop front (FIFO) - simple list!
        result.append(node.val)  # 4. Process node
        
        # 5. Enqueue children (LEFT first, then RIGHT)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    
    return result

# Tree:     1
#          / \
#         2   3
#        / \
#       4   5
#
# Queue trace:
# []              → append 1
# [1]             → pop(0) → 1, append 2, append 3
# [2, 3]          → pop(0) → 2, append 4, append 5
# [3, 4, 5]       → pop(0) → 3
# [4, 5]          → pop(0) → 4
# [5]             → pop(0) → 5
# []              → done!
#
# Result: [1, 2, 3, 4, 5] (level order BFS!)
```

---

### STEP 3: Side-by-Side Comparison

```
┌─────────────────────────────────────────────────────────────┐
│                    GRAPH                 TREE               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   visited = set()              (not needed!)                │
│   visited.add(node)            (remove this line)           │
│   if node in visited:          (remove this check)          │
│                                                             │
│   for neighbor in graph[node]: if node.left:                │
│       stack.append(neighbor)       stack.append(node.left)  │
│                                if node.right:               │
│                                    stack.append(node.right) │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────┐
│           DFS (STACK)              BFS (QUEUE)              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   stack = []                       queue = []               │
│   stack.append(root)               queue.append(root)       │
│                                                             │
│   while stack:                     while queue:             │
│       node = stack.pop()               node = queue.pop(0)  │
│       process(node)                    process(node)        │
│                                                             │
│       # RIGHT first, then LEFT         # LEFT first         │
│       if node.right:                   if node.left:        │
│           stack.append(right)              queue.append(left)
│       if node.left:                    if node.right:       │
│           stack.append(left)               queue.append(right)
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   pop() = LIFO                     pop(0) = FIFO            │
│   Last In, First Out               First In, First Out      │
│   Goes DEEP first                  Goes WIDE first          │
│   Output: 1,2,4,5,3                Output: 1,2,3,4,5        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Visual: Stack vs Queue Step-by-Step

```
Tree:       1
           / \
          2   3
         / \
        4   5

STACK (DFS) - pop():             QUEUE (BFS) - pop(0):
────────────────────             ─────────────────────
Step 1: [1]                      Step 1: [1]
        ↓ pop() → 1                      ↓ pop(0) → 1
        append 3, append 2               append 2, append 3
        
Step 2: [3, 2]                   Step 2: [2, 3]
        ↓ pop() → 2 (last!)              ↓ pop(0) → 2 (first!)
        append 5, append 4               append 4, append 5
        
Step 3: [3, 5, 4]                Step 3: [3, 4, 5]
        ↓ pop() → 4                      ↓ pop(0) → 3
        
Step 4: [3, 5]                   Step 4: [4, 5]
        ↓ pop() → 5                      ↓ pop(0) → 4
        
Step 5: [3]                      Step 5: [5]
        ↓ pop() → 3                      ↓ pop(0) → 5

Output: 1→2→4→5→3                Output: 1→2→3→4→5
        (DEEP first)                     (WIDE first)
```

---

### Summary: Graph → Tree Transformation

```
┌─────────────────────────────────────────────────────────────┐
│   TO CONVERT GRAPH DFS/BFS TO TREE:                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. REMOVE visited set (trees have no cycles)              │
│                                                             │
│   2. CHANGE "for neighbor in graph[node]"                   │
│      TO "if node.left / if node.right"                      │
│                                                             │
│   3. EVERYTHING ELSE stays the same!                        │
│      - stack.pop() for DFS                                  │
│      - queue.pop(0) for BFS                                 │
│      - Same while loop structure                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Note on Performance:**
```
queue.pop(0) is O(n) - fine for learning!
deque.popleft() is O(1) - better for production code

For interviews, both are acceptable. Just know the difference!
```

---

## Part 1.5 – DFS vs BFS: The Key Difference

**(Visual: Two traversal orders.)**

```
Tree:       1
           / \
          2   3
         / \   \
        4   5   6

DFS (Depth-First): Goes DEEP first
  Preorder:  1 → 2 → 4 → 5 → 3 → 6
  
BFS (Breadth-First): Goes WIDE first (level by level)
  Level 0:   1
  Level 1:   2, 3
  Level 2:   4, 5, 6
  Order:     1 → 2 → 3 → 4 → 5 → 6
```

```
┌─────────────────────────────────────────────────────────────┐
│   DFS: Uses RECURSION (or stack)                            │
│        Goes as DEEP as possible first                       │
│                                                             │
│   BFS: Uses QUEUE                                           │
│        Processes all nodes at current LEVEL before moving on│
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2 – The Scenario (Concrete Example)

**(Visual appears: binary tree.)**

```
        3
       / \
      9  20
        /  \
       15   7

Problem: Return level order traversal (values grouped by level)

Expected output: [[3], [9, 20], [15, 7]]

Level 0: [3]
Level 1: [9, 20]
Level 2: [15, 7]
```

**Instructor:**
"We need to group nodes by their level. DFS doesn't naturally give us levels — BFS does!"

---

## Part 3 – The Core Template

**(Visual: The template.)**

**Instructor:**
"Here's the **BFS** template. The KEY is knowing how many nodes are in the current level!"

```python
from collections import deque

def bfs(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])  # 1) Initialize queue with root
    
    while queue:           # 2) While there are nodes to process
        level_size = len(queue)  # 3) KEY! Nodes in current level
        level_values = []
        
        for _ in range(level_size):  # 4) Process entire level
            node = queue.popleft()
            level_values.append(node.val)
            
            # 5) Add children for NEXT level
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level_values)  # 6) Save this level
    
    return result
```

---

## Part 4 – The KEY Insight: level_size = len(queue)

**(Visual: Why this is crucial.)**

**Instructor:**
"The **magic line** is `level_size = len(queue)`. Let me explain why:"

```
┌─────────────────────────────────────────────────────────────┐
│   WHY level_size = len(queue) IS THE KEY:                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   At the START of each while loop iteration:                │
│   - Queue contains EXACTLY all nodes at current level!      │
│   - We capture this count BEFORE adding children            │
│                                                             │
│   Example:                                                  │
│       3                                                     │
│      / \                                                    │
│     9  20                                                   │
│                                                             │
│   Initial: queue = [3], level_size = 1                      │
│   Process 3, add 9 and 20                                   │
│   After: queue = [9, 20]                                    │
│                                                             │
│   Next iteration: queue = [9, 20], level_size = 2           │
│   Process 9 and 20 (exactly 2 nodes!)                       │
│                                                             │
│   If we didn't capture level_size, we'd mix levels!         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 5 – Understanding the Template Steps

**(Visual: Numbered steps.)**

**Instructor:**
"Let me break down the 6 steps:"

```
┌─────────────────────────────────────────────────────────────┐
│           BFS PATTERN - 6 STEPS                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1) INITIALIZE: queue = deque([root])                       │
│     → Start with root in queue                              │
│                                                             │
│  2) WHILE LOOP: while queue:                                │
│     → Continue until all levels processed                   │
│                                                             │
│  3) CAPTURE LEVEL SIZE: level_size = len(queue)             │
│     → This tells us how many nodes in current level         │
│     → MUST capture BEFORE processing!                       │
│                                                             │
│  4) PROCESS LEVEL: for _ in range(level_size):              │
│     → Process exactly level_size nodes                      │
│     → Use popleft() to get FIFO order                       │
│                                                             │
│  5) ADD CHILDREN: queue.append(child)                       │
│     → Children go to BACK of queue (next level)             │
│                                                             │
│  6) SAVE LEVEL: result.append(level_values)                 │
│     → Store this level's results                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 6 – Walkthrough: Level Order Traversal

**(Visual: Step-by-step trace.)**

```
Tree:       3
           / \
          9  20
            /  \
           15   7
```

```
INITIALIZATION:
  queue = [3]
  result = []

ITERATION 1 (Level 0):
  level_size = len([3]) = 1
  level_values = []
  
  Process node 3:
    - popleft() → 3
    - level_values = [3]
    - Add children: queue = [9, 20]
  
  result = [[3]]
  
ITERATION 2 (Level 1):
  level_size = len([9, 20]) = 2
  level_values = []
  
  Process node 9:
    - popleft() → 9
    - level_values = [9]
    - No children
  
  Process node 20:
    - popleft() → 20
    - level_values = [9, 20]
    - Add children: queue = [15, 7]
  
  result = [[3], [9, 20]]

ITERATION 3 (Level 2):
  level_size = len([15, 7]) = 2
  level_values = []
  
  Process node 15:
    - popleft() → 15
    - level_values = [15]
    - No children
  
  Process node 7:
    - popleft() → 7
    - level_values = [15, 7]
    - No children
  
  queue = [] (empty!)
  result = [[3], [9, 20], [15, 7]]

DONE! queue is empty.

ANSWER: [[3], [9, 20], [15, 7]]
```

---

## Part 7 – Visual: Queue State at Each Level

```
┌─────────────────────────────────────────────────────────────┐
│           QUEUE STATE AT EACH ITERATION                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Start:     [3]            ← Level 0 nodes                 │
│              ───                                            │
│   After L0:  [9, 20]        ← Level 1 nodes                 │
│              ──────                                         │
│   After L1:  [15, 7]        ← Level 2 nodes                 │
│              ───────                                        │
│   After L2:  []             ← Done!                         │
│                                                             │
│   The queue always contains EXACTLY one level's nodes       │
│   at the start of each while iteration!                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 8 – When to Use BFS vs DFS

**(Visual: Decision guide.)**

**Instructor:**
"How do you know when to use BFS?"

```
┌─────────────────────────────────────────────────────────────┐
│   USE BFS WHEN:                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ✅ Problem mentions "level" or "depth"                    │
│   ✅ Need to process nodes level by level                   │
│   ✅ Finding SHORTEST path in unweighted graph/tree         │
│   ✅ Need all nodes at a certain distance from root         │
│   ✅ Right side view / left side view                       │
│   ✅ Zigzag level order                                     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│   USE DFS WHEN:                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ✅ Path problems (root to leaf)                           │
│   ✅ Need to explore entire subtree before sibling          │
│   ✅ Backtracking problems                                  │
│   ✅ Tree DP problems                                       │
│   ✅ Checking tree properties (valid BST, balanced)         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 9 – Variations of BFS

**(Visual: Different BFS patterns.)**

**Instructor:**
"The template can be adapted for different problems:"

### Variation 1: Just Get Last Node per Level (Right Side View)
```python
for _ in range(level_size):
    node = queue.popleft()
    if _ == level_size - 1:  # Last node in level
        result.append(node.val)
    # ... add children
```

### Variation 2: Zigzag Order
```python
for i, _ in enumerate(range(level_size)):
    node = queue.popleft()
    if i % 2 == 0:
        level_values.append(node.val)
    else:
        level_values.insert(0, node.val)  # Reverse for odd levels
```

### Variation 3: Average per Level
```python
level_sum = 0
for _ in range(level_size):
    node = queue.popleft()
    level_sum += node.val
    # ... add children
result.append(level_sum / level_size)
```

---

## Part 10 – BFS Without Level Grouping

**(Visual: Simpler BFS.)**

**Instructor:**
"If you don't need to group by level, BFS is even simpler:"

```python
def simpleBFS(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        node = queue.popleft()
        result.append(node.val)  # Just process each node
        
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    
    return result  # Returns: [3, 9, 20, 15, 7]
```

**No level_size needed if you don't care about level grouping!**

---

## Part 11 – Why Use deque?

**(Visual: deque vs list.)**

**Instructor:**
"Why do we use `deque` instead of a regular list?"

```
┌─────────────────────────────────────────────────────────────┐
│   LIST vs DEQUE:                                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   list.pop(0)     → O(n) - shifts all elements!             │
│   deque.popleft() → O(1) - efficient removal from front     │
│                                                             │
│   For BFS with n nodes:                                     │
│   - With list: O(n²) total time                             │
│   - With deque: O(n) total time                             │
│                                                             │
│   ALWAYS use deque for BFS!                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘

from collections import deque
queue = deque([root])  # ← Always use this!
```

---

## Part 12 – Complexity Analysis

**Instructor:**

```
Time Complexity: O(n)
  - Visit each node exactly once
  - Each node: O(1) for popleft and append

Space Complexity: O(w)
  - w = maximum width of tree (max nodes at any level)
  - Worst case (complete tree): O(n/2) = O(n) at last level
  - Worst case (skewed): O(1)
```

---

## Part 13 – Common Mistakes

**(Visual: Pitfalls.)**

**Instructor:**

```
❌ Mistake 1: Forgetting to capture level_size
   while queue:
       for node in queue:  # WRONG! Queue changes during loop!
           ...
   
   while queue:
       level_size = len(queue)  # RIGHT! Capture first
       for _ in range(level_size):
           ...

❌ Mistake 2: Using list instead of deque
   queue.pop(0)     # O(n) - SLOW!
   queue.popleft()  # O(1) - FAST!

❌ Mistake 3: Forgetting to check if root is None
   if not root:
       return []  # Don't forget!

❌ Mistake 4: Adding None children to queue
   if node.left:   # Check first!
       queue.append(node.left)
```

---

## Part 14 – LeetCode Problems Using BFS

| Problem | What to Track per Level |
|---------|-------------------------|
| LC 102 - Level Order Traversal | All values |
| LC 107 - Level Order II | All values (reversed) |
| LC 103 - Zigzag Level Order | Alternating direction |
| LC 199 - Right Side View | Last node only |
| LC 637 - Average of Levels | Sum / count |
| LC 515 - Largest Value per Level | Max value |
| LC 111 - Minimum Depth | Stop at first leaf |

---

## Part 15 – Summary

**Instructor:**
"Let's recap the BFS pattern:"

```
┌─────────────────────────────────────────────────────────────┐
│           BFS PATTERN SUMMARY                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  STRUCTURE:                                                 │
│    queue = deque([root])                                    │
│    while queue:                                             │
│        level_size = len(queue)  # KEY!                      │
│        for _ in range(level_size):                          │
│            node = queue.popleft()                           │
│            # process node                                   │
│            # add children to queue                          │
│                                                             │
│  KEY INSIGHT:                                               │
│    level_size = len(queue) captures nodes in current level  │
│    Process exactly that many before moving to next level    │
│                                                             │
│  USE WHEN:                                                  │
│    - Level-by-level processing needed                       │
│    - Shortest path problems                                 │
│    - "Level" or "depth" mentioned in problem                │
│                                                             │
│  WHY DEQUE:                                                 │
│    O(1) popleft() vs O(n) list.pop(0)                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Quick Reference Card

```python
from collections import deque

# BFS TEMPLATE (with level grouping)
def bfs(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)  # KEY LINE!
        level_values = []
        
        for _ in range(level_size):
            node = queue.popleft()
            level_values.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(level_values)
    
    return result


# BFS TEMPLATE (simple, no level grouping)
def simpleBFS(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        node = queue.popleft()
        result.append(node.val)
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)
    
    return result
```


