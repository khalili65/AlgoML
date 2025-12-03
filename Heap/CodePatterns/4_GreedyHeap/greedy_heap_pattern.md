# 🏔️ Pattern 4: Greedy + Heap

---

## When to Use

- Scheduling problems (meetings, tasks, jobs)
- Always need to pick the "best" next option
- Simulation problems with priorities
- Resource allocation

---

## The Core Idea

```
Greedy: Make the locally optimal choice at each step
Heap:   Quickly find/track the optimal choice

Common pattern:
1. Sort by some criteria (e.g., start time)
2. Use heap to track current state (e.g., end times)
3. At each step, update heap based on new information
```

---

## The Template

```python
import heapq

def greedyWithHeap(items):
    # Sort by some criteria
    items.sort(key=lambda x: x[0])
    
    heap = []
    result = 0
    
    for item in items:
        # Remove items that are no longer relevant
        while heap and EXPIRY_CONDITION(heap[0], item):
            heapq.heappop(heap)
        
        # Add current item to heap
        heapq.heappush(heap, PRIORITY_VALUE(item))
        
        # Update result based on heap state
        result = max(result, len(heap))  # or other logic
    
    return result
```

---

## Example 1: Meeting Rooms II (LC 253)

**Problem:** Find minimum number of meeting rooms required.

```python
def minMeetingRooms(intervals):
    if not intervals:
        return 0
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    # Heap stores end times of ongoing meetings
    end_times = []
    
    for start, end in intervals:
        # If earliest ending meeting is done, reuse that room
        if end_times and end_times[0] <= start:
            heapq.heappop(end_times)
        
        # Add current meeting's end time
        heapq.heappush(end_times, end)
    
    # Number of rooms = number of ongoing meetings at peak
    return len(end_times)
```

**Visual:**
```
Meetings: [[0,30], [5,10], [15,20]]

t=0:  Start [0,30]. heap = [30]. Rooms = 1
t=5:  Start [5,10]. 30 > 5, can't reuse room.
      heap = [10, 30]. Rooms = 2
t=15: Start [15,20]. 10 <= 15, reuse room!
      Pop 10, push 20. heap = [20, 30]. Rooms = 2

Answer: 2 rooms
```

**Why Greedy Works:**
```
We always try to reuse the room that becomes free EARLIEST.
If that room isn't free, no room is free → need new room.
This is optimal because we're maximizing room reuse.
```

---

## Example 2: Task Scheduler (LC 621)

**Problem:** Schedule tasks with cooling period n between same tasks.

```python
from collections import Counter

def leastInterval(tasks, n):
    count = Counter(tasks)
    max_heap = [-cnt for cnt in count.values()]
    heapq.heapify(max_heap)
    
    time = 0
    cooldown = []  # Queue of (available_time, remaining_count)
    
    while max_heap or cooldown:
        time += 1
        
        if max_heap:
            # Execute most frequent task
            cnt = heapq.heappop(max_heap) + 1  # +1 because negated
            if cnt < 0:  # Still has remaining
                cooldown.append((time + n, cnt))
        
        # Check if any task finished cooling
        if cooldown and cooldown[0][0] == time:
            heapq.heappush(max_heap, cooldown.pop(0)[1])
    
    return time
```

**Visual:**
```
Tasks: A, A, A, B, B, B, n = 2

max_heap: [-3, -3] (A:3, B:3)

Time 1: Execute A (most frequent). cooldown: [(3, -2)]
Time 2: Execute B. cooldown: [(3, -2), (4, -2)]
Time 3: A available again. Execute A. cooldown: [(4, -2), (5, -1)]
Time 4: B available. Execute B. cooldown: [(5, -1), (6, -1)]
Time 5: A available. Execute A. Done with A.
Time 6: B available. Execute B. Done with B.

Result: 6 time units
```

---

## Example 3: Reorganize String (LC 767)

**Problem:** Rearrange string so no two adjacent characters are the same.

```python
from collections import Counter

def reorganizeString(s):
    count = Counter(s)
    max_heap = [(-cnt, char) for char, cnt in count.items()]
    heapq.heapify(max_heap)
    
    result = []
    prev = (0, '')  # Previous character's (count, char)
    
    while max_heap or prev[0] < 0:
        if not max_heap:
            return ""  # Impossible to reorganize
        
        cnt, char = heapq.heappop(max_heap)
        result.append(char)
        
        # Put previous character back (if it has remaining)
        if prev[0] < 0:
            heapq.heappush(max_heap, prev)
        
        # Update prev for next iteration
        prev = (cnt + 1, char)  # +1 because we used one
    
    return ''.join(result)
```

---

## Example 4: Car Pooling (LC 1094)

**Problem:** Can a car with capacity pick up all passengers?

```python
def carPooling(trips, capacity):
    # Sort by pickup location
    trips.sort(key=lambda x: x[1])
    
    # Heap: (drop_off_location, num_passengers)
    heap = []
    current_passengers = 0
    
    for num, start, end in trips:
        # Drop off passengers whose destination we've passed
        while heap and heap[0][0] <= start:
            current_passengers -= heapq.heappop(heap)[1]
        
        # Pick up new passengers
        current_passengers += num
        heapq.heappush(heap, (end, num))
        
        if current_passengers > capacity:
            return False
    
    return True
```

---

## Example 5: Single-Threaded CPU (LC 1834)

**Problem:** Process tasks one at a time, always picking shortest available.

```python
def getOrder(tasks):
    # Add original indices
    tasks = [(t[0], t[1], i) for i, t in enumerate(tasks)]
    tasks.sort()  # Sort by enqueue time
    
    result = []
    heap = []  # (processing_time, original_index)
    time = 0
    i = 0
    
    while i < len(tasks) or heap:
        # If heap is empty, jump to next task's enqueue time
        if not heap:
            time = max(time, tasks[i][0])
        
        # Add all tasks that have arrived
        while i < len(tasks) and tasks[i][0] <= time:
            heapq.heappush(heap, (tasks[i][1], tasks[i][2]))
            i += 1
        
        # Process the shortest task
        proc_time, idx = heapq.heappop(heap)
        result.append(idx)
        time += proc_time
    
    return result
```

---

## Common Greedy + Heap Patterns

```
┌─────────────────────────────────────────────────────────────┐
│   COMMON PATTERNS                                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   1. SCHEDULING:                                            │
│      Sort by start time                                     │
│      Heap tracks end times                                  │
│      Reuse resources when possible                          │
│                                                             │
│   2. FREQUENCY-BASED:                                       │
│      Heap tracks remaining counts                           │
│      Always pick most/least frequent                        │
│      Use cooldown for constraints                           │
│                                                             │
│   3. SIMULATION:                                            │
│      Process events in order                                │
│      Heap tracks current state                              │
│      Update state based on events                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Complexity

```
Time:  O(n log n) - sorting + heap operations
Space: O(n) - heap size in worst case
```

---

## Common Mistakes

```
❌ Mistake 1: Forgetting to sort first
   Most greedy problems require sorted input!

❌ Mistake 2: Wrong heap comparison
   # For meetings: heap should have END times
   # We care about which meeting ENDS earliest

❌ Mistake 3: Not handling empty heap
   if heap:  # Always check before accessing heap[0]!

❌ Mistake 4: Using > instead of >= (or vice versa)
   if end_times[0] <= start:  # Can we reuse this room?
   # If meeting ends AT start time, room is available
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   GREEDY + HEAP PATTERN                                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   # 1. Sort by relevant criteria                            │
│   items.sort(key=...)                                       │
│                                                             │
│   # 2. Process each item                                    │
│   for item in items:                                        │
│       # Remove expired/irrelevant entries                   │
│       while heap and CONDITION:                             │
│           heappop(heap)                                     │
│                                                             │
│       # Add current item                                    │
│       heappush(heap, item)                                  │
│                                                             │
│       # Update result                                       │
│       result = f(heap)                                      │
│                                                             │
│   KEY: Sort first, heap tracks current state                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


