# 🎬 Tutorial Script: Meeting Rooms Pattern (Scheduling Problems)

---

## Part 0 – Opening & Hook

**(Instructor on camera, no slides yet.)**

**Instructor:**
"Hey everyone, welcome back!

Today we're learning the **Meeting Rooms** pattern — one of the most practical interval patterns! This is actually TWO related problems:

1. **Can one person attend all meetings?** (Conflict detection)
2. **How many rooms do we need?** (Resource allocation)

These are classic scheduling problems that appear in interviews at companies like Google, Microsoft, and Amazon. Let's dive in!"

---

## Part 1 – Problem 5a: Can Attend All Meetings?

**(Visual: Meeting schedule.)**

**Instructor:**
"Let's start with the simpler problem:"

```
Given an array of meeting time intervals where intervals[i] = [start, end],
determine if a person could attend ALL meetings.

Example 1:
Input: [[0,30], [5,10], [15,20]]
Output: false
(Meetings [0,30] and [5,10] overlap!)

Example 2:
Input: [[7,10], [2,4]]
Output: true
(After sorting: [[2,4], [7,10]] — no overlap!)
```

---

## Part 2 – The Simple Approach: Detect Any Overlap

**(Visual: Overlap detection logic.)**

**Instructor:**
"This is essentially asking: **are there any overlapping intervals?**

The approach is simple:
1. **Sort by start time**
2. **Check each pair** of adjacent intervals
3. If any `current.start < previous.end` → **overlap exists!**"

```
┌─────────────────────────────────────────────────────────────┐
│           MEETING ROOMS I PATTERN                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT by start time                                      │
│                                                             │
│  2. FOR each pair of adjacent meetings:                     │
│     if current.start < previous.end → OVERLAP!              │
│                                                             │
│  3. Return True if no overlaps found                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: After sorting, only need to check adjacent pairs!     │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3 – Walkthrough: Can Attend All?

**(Visual: Example walkthrough.)**

```
Input: [[0,30], [5,10], [15,20]]
After sorting: [[0,30], [5,10], [15,20]]

Check [5,10] vs [0,30]:
  5 < 30? YES → OVERLAP!
  Return False immediately.
```

```
Input: [[7,10], [2,4]]
After sorting: [[2,4], [7,10]]

Check [7,10] vs [2,4]:
  7 < 4? NO → No overlap
  
All meetings checked, no overlaps.
Return True!
```

---

## Part 4 – Solution for Meeting Rooms I

**(Visual: Code.)**

```python
def canAttendMeetings(intervals):
    if not intervals:
        return True
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    # Check for overlaps between adjacent meetings
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False  # Overlap found!
    
    return True  # No overlaps
```

**Time:** O(n log n) for sorting
**Space:** O(1) extra

---

## Part 5 – Problem 5b: Minimum Meeting Rooms

**(Visual: More complex scenario.)**

**Instructor:**
"Now let's tackle the harder problem:"

```
Given an array of meeting time intervals, return the MINIMUM 
number of conference rooms required.

Example:
Input: [[0,30], [5,10], [15,20]]
Output: 2

Timeline:
0----5----10---15---20---25---30
|=========================|       Room 1: [0,30]
     |====|                       Room 2: [5,10]
               |=====|            Room 2: [15,20] (reused!)

At time 5-10, we need 2 rooms simultaneously!
```

---

## Part 6 – Approach 1: Min-Heap Solution

**(Visual: Heap approach.)**

**Instructor:**
"The first approach uses a **min-heap** to track when rooms become free:"

```
┌─────────────────────────────────────────────────────────────┐
│           MEETING ROOMS II - HEAP APPROACH                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. SORT meetings by start time                             │
│                                                             │
│  2. Use MIN-HEAP to track room end times                    │
│                                                             │
│  3. FOR each meeting:                                       │
│     - If earliest ending room is free (heap[0] <= start):   │
│         Pop it (room becomes available)                     │
│     - Push current meeting's end time (room is now busy)    │
│                                                             │
│  4. RETURN heap size (= rooms in use)                       │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  KEY: Heap always contains end times of ongoing meetings    │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 7 – Heap Walkthrough

**(Visual: Step-by-step heap.)**

```
Meetings sorted: [[0,30], [5,10], [15,20]]
Heap = []

Meeting [0,30]:
  Heap empty, can't reuse
  Push 30 → Heap = [30]

Meeting [5,10]:
  heap[0] = 30, but 30 > 5 → Room not free
  Push 10 → Heap = [10, 30]  (need new room!)

Meeting [15,20]:
  heap[0] = 10, and 10 <= 15 → Room is free!
  Pop 10 → Heap = [30]
  Push 20 → Heap = [20, 30]  (reused room!)

Final heap size = 2 → Need 2 rooms!
```

---

## Part 8 – Heap Solution Code

**(Visual: Code.)**

```python
import heapq

def minMeetingRooms(intervals):
    if not intervals:
        return 0
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    # Min-heap to track end times of ongoing meetings
    heap = []
    
    for start, end in intervals:
        # If earliest ending meeting is done, reuse that room
        if heap and heap[0] <= start:
            heapq.heappop(heap)
        
        # Allocate room for current meeting
        heapq.heappush(heap, end)
    
    # Heap size = number of rooms needed
    return len(heap)
```

**Time:** O(n log n)
**Space:** O(n) for heap

---

## Part 9 – Approach 2: Sweep Line Algorithm

**(Visual: Sweep line concept.)**

**Instructor:**
"There's an elegant alternative called the **Sweep Line** algorithm:"

```
┌─────────────────────────────────────────────────────────────┐
│           MEETING ROOMS II - SWEEP LINE                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  IDEA: Track when rooms are needed/freed                    │
│                                                             │
│  1. Create EVENTS:                                          │
│     - Meeting starts → +1 room needed                       │
│     - Meeting ends → -1 room freed                          │
│                                                             │
│  2. SORT events by time                                     │
│                                                             │
│  3. SWEEP through events:                                   │
│     - Add/subtract rooms as events occur                    │
│     - Track maximum rooms needed at any point               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 10 – Sweep Line Walkthrough

**(Visual: Event processing.)**

```
Meetings: [[0,30], [5,10], [15,20]]

Step 1: Create events
  [0, +1]  → Meeting starts at 0
  [30, -1] → Meeting ends at 30
  [5, +1]  → Meeting starts at 5
  [10, -1] → Meeting ends at 10
  [15, +1] → Meeting starts at 15
  [20, -1] → Meeting ends at 20

Step 2: Sort by time
  [(0,+1), (5,+1), (10,-1), (15,+1), (20,-1), (30,-1)]

Step 3: Sweep through
  Time 0:  rooms = 0 + 1 = 1, max = 1
  Time 5:  rooms = 1 + 1 = 2, max = 2  ← Peak!
  Time 10: rooms = 2 - 1 = 1
  Time 15: rooms = 1 + 1 = 2, max = 2
  Time 20: rooms = 2 - 1 = 1
  Time 30: rooms = 1 - 1 = 0

Answer: max = 2 rooms
```

---

## Part 11 – Sweep Line Solution Code

**(Visual: Code.)**

```python
def minMeetingRooms(intervals):
    if not intervals:
        return 0
    
    # Create events: (time, delta)
    events = []
    for start, end in intervals:
        events.append((start, 1))   # Meeting starts: +1 room
        events.append((end, -1))    # Meeting ends: -1 room
    
    # Sort by time (if tie, process ends before starts)
    events.sort(key=lambda x: (x[0], x[1]))
    
    # Sweep through events
    rooms = 0
    max_rooms = 0
    
    for time, delta in events:
        rooms += delta
        max_rooms = max(max_rooms, rooms)
    
    return max_rooms
```

**Time:** O(n log n)
**Space:** O(n) for events

---

## Part 12 – Heap vs Sweep Line

**(Visual: Comparison.)**

**Instructor:**
"Both approaches work! Here's when to use each:"

| Aspect | Heap Approach | Sweep Line |
|--------|---------------|------------|
| **Intuition** | Track room availability | Count concurrent events |
| **Best for** | Room assignment | Counting overlaps |
| **Extension** | Which room for each meeting? | Peak load analysis |
| **Complexity** | O(n log n) | O(n log n) |

**Tip:** Sweep line is more versatile for "maximum concurrent events" problems!

---

## 💡 Tip for Students

**Instructor:**
"Here's how to choose between Meeting Rooms I and II patterns:

**Meeting Rooms I (Overlap Detection):**
- 'Can X attend all?' → Check if ANY overlap exists
- Sort by start, check adjacent pairs
- Simple O(n log n) solution

**Meeting Rooms II (Resource Counting):**
- 'How many rooms/resources needed?' → Count max concurrent
- Use Heap OR Sweep Line
- Both O(n log n) solutions

**Key insight for Sweep Line:**
> Start event = +1, End event = -1
> Max sum at any point = max concurrent events!"

---

## Part 13 – Other Problems Using This Pattern

**(Visual: Related problems.)**

| Problem | Type |
|---------|------|
| **LC 252 - Meeting Rooms** | Overlap detection |
| **LC 253 - Meeting Rooms II** | Min rooms (heap/sweep) |
| **LC 1094 - Car Pooling** | Sweep line with capacity |
| **LC 731 - My Calendar II** | Max overlaps |
| **LC 732 - My Calendar III** | K-booking count |

---

## Part 14 – Pattern Recognition

**(Visual: When to use each approach.)**

```
USE MEETING ROOMS I when:
✅ Checking if events conflict
✅ "Can attend all?" questions
✅ Simple yes/no overlap detection

USE MEETING ROOMS II when:
✅ "How many resources needed?"
✅ "Maximum concurrent events?"
✅ Capacity/load planning problems
```

---

## Part 15 – Complexity Summary

```
Meeting Rooms I:
  Time:  O(n log n) - sorting dominates
  Space: O(1) extra

Meeting Rooms II (Heap):
  Time:  O(n log n) - sorting + heap operations
  Space: O(n) - heap storage

Meeting Rooms II (Sweep Line):
  Time:  O(n log n) - sorting events
  Space: O(n) - event storage
```

---

## Part 16 – Closing

**Instructor:**
"And that's the Meeting Rooms pattern!

**Key takeaways:**
1. **Meeting Rooms I**: Sort + check adjacent for overlaps
2. **Meeting Rooms II**: Heap tracks room availability
3. **Sweep Line**: Events (+1/-1) count concurrent usage
4. Both II approaches are O(n log n)

Practice with LC 252, LC 253, and LC 1094 to master scheduling problems!"

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│           MEETING ROOMS CHEAT SHEET                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  MEETING ROOMS I (Can attend all?):                         │
│    Sort by start                                            │
│    Check: current.start < prev.end → OVERLAP                │
│                                                             │
│  MEETING ROOMS II (Min rooms):                              │
│                                                             │
│  HEAP APPROACH:                                             │
│    Sort by start                                            │
│    heap[0] <= start → pop (room free)                       │
│    push(end) → allocate room                                │
│    return len(heap)                                         │
│                                                             │
│  SWEEP LINE:                                                │
│    events = [(start, +1), (end, -1)]                        │
│    sort events                                              │
│    rooms += delta, track max                                │
│                                                             │
│  TIME: O(n log n)    SPACE: O(n)                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

