# 🔗 Pattern 1: Fast & Slow Pointers (Floyd's Algorithm)

---

## When to Use

- Detect if a cycle exists
- Find the middle of a linked list
- Find the start of a cycle
- Find if list has certain length properties

---

## The Template

```python
def fastSlow(head):
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next        # Move 1 step
        fast = fast.next.next   # Move 2 steps
        
        if CONDITION:           # e.g., slow == fast
            return RESULT
    
    return slow  # slow is at middle when fast reaches end
```

---

## Why It Works

```
Fast moves 2x speed of slow.

FOR MIDDLE:
When fast reaches end, slow is at middle.

List: 1 → 2 → 3 → 4 → 5
      s       
      f
      
      1 → 2 → 3 → 4 → 5
          s       
              f
              
      1 → 2 → 3 → 4 → 5
              s       
                      f (end)
                      
Slow is at middle (3)!

FOR CYCLE:
If cycle exists, fast will eventually catch up to slow.
Like runners on a circular track - faster one laps the slower.
```

---

## Example 1: Detect Cycle (LC 141)

**Problem:** Return true if linked list has a cycle.

```python
def hasCycle(head):
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:  # They meet = cycle!
            return True
    
    return False  # fast reached end = no cycle
```

---

## Example 2: Find Middle (LC 876)

**Problem:** Return the middle node of the linked list.

```python
def middleNode(head):
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow  # slow is at middle
```

**Note:** For even-length lists, this returns the second middle node.

---

## Example 3: Find Cycle Start (LC 142)

**Problem:** Return the node where the cycle begins.

```python
def detectCycle(head):
    slow = head
    fast = head
    
    # Phase 1: Detect cycle (find meeting point)
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # No cycle
    
    # Phase 2: Find cycle start
    # Reset one pointer to head, move both at same speed
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow  # This is the cycle start
```

**Why Phase 2 works:**
```
Let:
- x = distance from head to cycle start
- y = distance from cycle start to meeting point
- c = cycle length

At meeting point:
- slow traveled: x + y
- fast traveled: x + y + n*c (went around cycle n times)

Since fast = 2 * slow:
x + y + n*c = 2(x + y)
n*c = x + y
x = n*c - y

So if we reset slow to head and move both at same speed,
they'll meet at cycle start!
```

---

## Example 4: Linked List Cycle Length

**Problem:** If cycle exists, find its length.

```python
def cycleLength(head):
    slow = head
    fast = head
    
    # Find meeting point
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            # Count cycle length
            count = 1
            curr = slow.next
            while curr != slow:
                count += 1
                curr = curr.next
            return count
    
    return 0  # No cycle
```

---

## Example 5: Happy Number (LC 202)

**Problem:** A number is happy if repeatedly summing squares of digits eventually equals 1.

```python
def isHappy(n):
    def get_next(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit ** 2
            num //= 10
        return total
    
    slow = n
    fast = get_next(n)
    
    while fast != 1 and slow != fast:
        slow = get_next(slow)
        fast = get_next(get_next(fast))
    
    return fast == 1
```

---

## Complexity

```
Time:  O(n) - each pointer visits each node at most twice
Space: O(1) - only two pointers
```

---

## Common Mistakes

```
❌ Mistake 1: Not checking fast.next
   while fast:  # WRONG - fast.next.next will crash!
   while fast and fast.next:  # RIGHT

❌ Mistake 2: Starting pointers at different positions
   slow = head
   fast = head.next  # WRONG for some problems
   
❌ Mistake 3: Forgetting the else clause for no-cycle case
   # Use while-else to handle no cycle found
```

---

## Summary

```
┌─────────────────────────────────────────────────────────────┐
│   FAST & SLOW PATTERN                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   slow = fast = head                                        │
│   while fast and fast.next:                                 │
│       slow = slow.next                                      │
│       fast = fast.next.next                                 │
│                                                             │
│   USE FOR:                                                  │
│   • Cycle detection: if slow == fast → cycle exists         │
│   • Find middle: when loop ends, slow is at middle          │
│   • Cycle start: reset one to head, move both at same speed │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```


