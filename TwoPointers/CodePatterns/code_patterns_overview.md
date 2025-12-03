# 👆👆 Two Pointers Code Patterns - Template-Based Approach

Core patterns for Two Pointers problems (excluding Sliding Window, Binary Search, Linked List, and Array/HashTable patterns covered separately).

---

## 📁 Folder Structure

```
CodePatterns/
├── code_patterns_overview.md    ← You are here
├── all_templates_reference.md   ← Quick reference
├── 1_OppositeDirection/
│   └── opposite_direction_pattern.md
├── 2_SameDirection/
│   └── same_direction_pattern.md
├── 3_NSum/
│   └── nsum_pattern.md
├── 4_Partition/
│   └── partition_pattern.md
└── 5_MergeArrays/
    └── merge_arrays_pattern.md
```

---

## 📊 The 5 Core Code Patterns

| # | Pattern | Key Idea | Example Problems |
|---|---------|----------|------------------|
| 1 | Opposite Direction | Left & right converge to center | LC 167, 11, 42, 125 |
| 2 | Same Direction | Fast reads, slow writes | LC 26, 27, 80, 283 |
| 3 | N-Sum Pattern | Sort + fix + two pointers | LC 15, 16, 18 |
| 4 | Partition | Three-way partition | LC 75, 86, 215 |
| 5 | Merge Arrays | Process two arrays in parallel | LC 88, 977, 986 |

---

## 🧠 Decision Flowchart: Which Pattern to Use?

```
START: What does the problem ask for?
│
├─ "Sorted array, find pair with sum" ──────► Pattern 1 (Opposite)
│
├─ "Check if palindrome" ───────────────────► Pattern 1 (Opposite)
│
├─ "Container/water/area problems" ─────────► Pattern 1 (Opposite)
│
├─ "Remove/move elements in-place" ─────────► Pattern 2 (Same Dir)
│
├─ "Find triplets/quadruplets" ─────────────► Pattern 3 (N-Sum)
│
├─ "Sort array with limited values (0,1,2)" ► Pattern 4 (Partition)
│
├─ "Merge two sorted arrays" ───────────────► Pattern 5 (Merge)
│
└─ Not sure? Think: "Do I need two indices moving through data?"
```

---

## 📋 Quick Reference: All 5 Templates

```python
# PATTERN 1: Opposite Direction
left, right = 0, len(arr) - 1
while left < right:
    if CONDITION: left += 1
    else: right -= 1

# PATTERN 2: Same Direction
slow = 0
for fast in range(len(nums)):
    if KEEP(nums[fast]):
        nums[slow] = nums[fast]
        slow += 1

# PATTERN 3: N-Sum
nums.sort()
for i in range(len(nums) - 2):
    if i > 0 and nums[i] == nums[i-1]: continue
    left, right = i + 1, len(nums) - 1
    # two pointer on remaining

# PATTERN 4: Partition (Three-way)
low, mid, high = 0, 0, len(nums) - 1
while mid <= high:
    if nums[mid] < pivot: swap(low, mid); low++; mid++
    elif nums[mid] > pivot: swap(mid, high); high--
    else: mid++

# PATTERN 5: Merge Arrays
i, j = 0, 0
while i < len(a) and j < len(b):
    if a[i] < b[j]: use a[i]; i++
    else: use b[j]; j++
```

---

## 🔑 Golden Rules

```
┌─────────────────────────────────────────────────────────────┐
│   TWO POINTERS GOLDEN RULES:                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. Opposite Direction usually requires SORTED array       │
│                                                             │
│   2. Same Direction: slow = write position, fast = reader   │
│                                                             │
│   3. N-Sum: Always SORT first, SKIP DUPLICATES              │
│                                                             │
│   4. Partition: Don't increment mid after swapping with     │
│      high (the swapped element needs checking!)             │
│                                                             │
│   5. Merge from END when doing in-place merge               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary

| Pattern | Key Line | When to Use |
|---------|----------|-------------|
| 1. Opposite | `while left < right` | Sorted, find pair |
| 2. Same Dir | `nums[slow] = nums[fast]` | In-place modify |
| 3. N-Sum | `sort + fix i + two ptr` | Find triplets |
| 4. Partition | `low, mid, high` | Limited values |
| 5. Merge | `i++ or j++` | Merge sorted |

**Master these 5 templates, and you can solve most two pointers problems!**


