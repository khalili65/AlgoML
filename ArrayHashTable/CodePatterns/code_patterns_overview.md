# 📊 Array & HashTable Code Patterns - Template-Based Approach

Core patterns for Array and HashTable problems (excluding Sliding Window, Two Pointers, Stack, Binary Search, and Linked List which are covered separately).

---

## 📁 Folder Structure

```
CodePatterns/
├── code_patterns_overview.md    ← You are here
├── all_templates_reference.md   ← Quick reference
├── 1_HashMapCounting/
│   └── hashmap_counting_pattern.md
├── 2_HashMapComplement/
│   └── hashmap_complement_pattern.md
├── 3_HashMapIndex/
│   └── hashmap_index_pattern.md
├── 4_HashSetExistence/
│   └── hashset_existence_pattern.md
├── 5_PrefixSum/
│   └── prefix_sum_pattern.md
├── 6_InPlace/
│   └── inplace_pattern.md
└── 7_CyclicSort/
    └── cyclic_sort_pattern.md
```

---

## 📊 The 7 Core Code Patterns

| # | Pattern | Key Idea | Example Problems |
|---|---------|----------|------------------|
| 1 | HashMap Counting | Count frequencies | LC 1, 49, 347, 242 |
| 2 | HashMap Complement | Find target - current | LC 1, 560, 525 |
| 3 | HashMap Index | Store index for lookup | LC 1, 217, 219 |
| 4 | HashSet Existence | O(1) membership check | LC 217, 128, 349 |
| 5 | Prefix Sum | Range sum queries | LC 303, 560, 238 |
| 6 | In-place Modification | Swap/overwrite in array | LC 26, 27, 283, 41 |
| 7 | Cyclic Sort | Place num at index num-1 | LC 41, 268, 287, 442 |

---

## 🧠 Decision Flowchart: Which Pattern to Use?

```
START: What does the problem ask for?
│
├─ "Count occurrences / frequency" ─────────► Pattern 1 (Counting)
│
├─ "Find pair that sums to X" ──────────────► Pattern 2 (Complement)
│
├─ "Where did I see this before?" ──────────► Pattern 3 (Index)
│
├─ "Does X exist?" ─────────────────────────► Pattern 4 (HashSet)
│
├─ "Sum of range / subarray sum" ───────────► Pattern 5 (Prefix Sum)
│
├─ "Modify in-place, O(1) space" ───────────► Pattern 6 (In-place)
│
├─ "Numbers in range [1,n], missing?" ──────► Pattern 7 (Cyclic Sort)
│
└─ Not sure? Think: "Do I need fast lookup?" → HashMap/HashSet
```

---

## 📋 Quick Reference: All 7 Templates

```python
# PATTERN 1: HashMap Counting
count = {}
for item in items:
    count[item] = count.get(item, 0) + 1

# PATTERN 2: HashMap Complement
seen = {}
for i, num in enumerate(nums):
    if target - num in seen:
        return [seen[target - num], i]
    seen[num] = i

# PATTERN 3: HashMap Index
last_seen = {}
for i, num in enumerate(nums):
    if num in last_seen:
        # check distance: i - last_seen[num]
    last_seen[num] = i

# PATTERN 4: HashSet Existence
seen = set()
for num in nums:
    if num in seen:
        return True
    seen.add(num)

# PATTERN 5: Prefix Sum
prefix = [0]
for num in nums:
    prefix.append(prefix[-1] + num)
# sum(i, j) = prefix[j+1] - prefix[i]

# PATTERN 6: In-place (Read/Write Pointers)
write = 0
for read in range(len(nums)):
    if CONDITION:
        nums[write] = nums[read]
        write += 1

# PATTERN 7: Cyclic Sort
i = 0
while i < n:
    correct_idx = nums[i] - 1
    if nums[i] != nums[correct_idx]:
        nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
    else:
        i += 1
```

---

## 🔑 Golden Rules

```
┌─────────────────────────────────────────────────────────────┐
│   ARRAY & HASHTABLE GOLDEN RULES:                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. HashMap = O(1) lookup by KEY                           │
│      When you need fast lookup, think HashMap!              │
│                                                             │
│   2. HashSet = O(1) existence check                         │
│      When you only need "is it there?"                      │
│                                                             │
│   3. Prefix Sum = O(1) range queries after O(n) preprocess  │
│      sum(i,j) = prefix[j+1] - prefix[i]                     │
│                                                             │
│   4. In-place often means Two Pointers (read/write)         │
│      Write pointer trails behind read pointer               │
│                                                             │
│   5. Cyclic Sort works when nums are in range [1,n]         │
│      Each number goes to index (number - 1)                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary

| Pattern | Key Line | When to Use |
|---------|----------|-------------|
| 1. Counting | `count[x] += 1` | Frequency problems |
| 2. Complement | `if target - num in seen` | Find pairs |
| 3. Index | `seen[num] = i` | Track positions |
| 4. HashSet | `if num in seen` | Existence check |
| 5. Prefix Sum | `prefix[j+1] - prefix[i]` | Range sums |
| 6. In-place | `nums[write] = nums[read]` | O(1) space |
| 7. Cyclic Sort | `swap to correct_idx` | Missing in [1,n] |

**Master these 7 templates, and you can solve most array & hashtable problems!**


