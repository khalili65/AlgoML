# 🔗 Linked List Code Patterns - Template-Based Approach

Just like Trees have 6 core patterns, **Linked List problems have 6 core code patterns**. Master these templates!

---

## 📁 Folder Structure

```
CodePatterns/
├── code_patterns_overview.md    ← You are here
├── 1_FastSlow/
│   └── fast_slow_pattern.md
├── 2_GapPointers/
│   └── gap_pointers_pattern.md
├── 3_DummyHead/
│   └── dummy_head_pattern.md
├── 4_Reverse/
│   └── reverse_pattern.md
├── 5_Merge/
│   └── merge_pattern.md
└── 6_HashMapCopy/
    └── hashmap_copy_pattern.md
```

---

## 📊 The 6 Core Linked List Code Patterns

| # | Pattern | Key Technique | Example Problems |
|---|---------|---------------|------------------|
| 1 | Fast & Slow | `slow++, fast+=2` | LC 141, 142, 876 |
| 2 | Gap Pointers | First n ahead of second | LC 19, 61 |
| 3 | Dummy Head | `dummy.next = head` | LC 203, 82, 83 |
| 4 | Reverse | `prev, curr, next` swap | LC 206, 92, 25 |
| 5 | Merge | Compare and attach | LC 21, 23, 148 |
| 6 | HashMap Copy | `old -> new` mapping | LC 138 |

---

## 🧠 Decision Flowchart: Which Pattern to Use?

```
START: What does the problem ask for?
│
├─ "Detect cycle / find middle" ────────────► Pattern 1 (Fast & Slow)
│
├─ "Find nth from end / rotate" ────────────► Pattern 2 (Gap Pointers)
│
├─ "Delete/insert at head area" ────────────► Pattern 3 (Dummy Head)
│
├─ "Reverse all or part of list" ───────────► Pattern 4 (Reverse)
│
├─ "Merge/combine sorted lists" ────────────► Pattern 5 (Merge)
│
├─ "Clone list with extra pointers" ────────► Pattern 6 (HashMap Copy)
│
└─ Not sure? Check if head might change → Use Dummy Head
```

---

## 📋 Quick Reference: All 6 Templates

```python
# PATTERN 1: Fast & Slow
slow = fast = head
while fast and fast.next:
    slow = slow.next
    fast = fast.next.next

# PATTERN 2: Gap Pointers
first = second = head
for _ in range(n):
    first = first.next
while first:
    first = first.next
    second = second.next

# PATTERN 3: Dummy Head
dummy = ListNode(0)
dummy.next = head
curr = dummy
# ... modify list ...
return dummy.next

# PATTERN 4: Reverse
prev = None
curr = head
while curr:
    next_temp = curr.next
    curr.next = prev
    prev = curr
    curr = next_temp
return prev

# PATTERN 5: Merge
dummy = ListNode(0)
curr = dummy
while l1 and l2:
    if l1.val <= l2.val:
        curr.next = l1
        l1 = l1.next
    else:
        curr.next = l2
        l2 = l2.next
    curr = curr.next
curr.next = l1 or l2
return dummy.next

# PATTERN 6: HashMap Copy
old_to_new = {}
curr = head
while curr:
    old_to_new[curr] = Node(curr.val)
    curr = curr.next
curr = head
while curr:
    old_to_new[curr].next = old_to_new.get(curr.next)
    old_to_new[curr].random = old_to_new.get(curr.random)
    curr = curr.next
return old_to_new[head]
```

---

## 🔑 Golden Rules for Linked Lists

```
┌─────────────────────────────────────────────────────────────┐
│   LINKED LIST GOLDEN RULES:                                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Use DUMMY HEAD when head might change                  │
│      dummy = ListNode(0)                                    │
│      dummy.next = head                                      │
│      return dummy.next                                      │
│                                                             │
│   2. Check BOTH node and node.next before accessing         │
│      while fast and fast.next:  # not just "while fast"     │
│                                                             │
│   3. SAVE next before modifying pointers                    │
│      next_temp = curr.next                                  │
│      curr.next = prev  # now safe to modify                 │
│                                                             │
│   4. Draw it out! Linked list bugs are pointer bugs         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary

| Pattern | Key Line | When to Use |
|---------|----------|-------------|
| 1. Fast & Slow | `fast = fast.next.next` | Cycle, middle, nth from end |
| 2. Gap Pointers | `for _ in range(n): first = first.next` | Nth from end, rotate |
| 3. Dummy Head | `dummy.next = head; return dummy.next` | Head might change |
| 4. Reverse | `curr.next = prev` | Flip pointers |
| 5. Merge | `curr.next = smaller; curr = curr.next` | Combine sorted lists |
| 6. HashMap | `old_to_new[node] = Node(node.val)` | Deep copy |

**Master these 6 templates, and you can solve most linked list problems!**


