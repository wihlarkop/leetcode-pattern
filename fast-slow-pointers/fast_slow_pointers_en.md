# Pattern 4: Fast & Slow Pointers (Tortoise and Hare)

## 🌍 Real World Case
Imagine you're **auditing a music playlist** in a streaming app. There's a possibility the playlist has a **cycle** (song A → song B → song C → back to song B, looping forever). With Fast & Slow Pointers, you can **detect the loop** without having to store all the songs you've visited!

Another example: **Detecting infinite redirects** in a web crawler - if URL A redirects to B, B to C, C back to A (loop), the crawler can get stuck. Fast & Slow pointers detect this.

## 📊 Concept Visualization
```
Linked List with Cycle:
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Step 1: Start (both at head)
S,F
↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Step 2: Move (slow +1, fast +2)
    S       F
    ↓       ↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Step 3: Move again
        S           F
        ↓           ↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Step 4: Fast loops back
            S   F
            ↓   ↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Step 5: Keep moving...
                S,F (MEET!)
                ↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Result: CYCLE DETECTED! ✅
```

## 💻 Python Code with Visualization

```python
from typing import Optional

class ListNode:
    """Node for linked list"""
    def __init__(self, val: int = 0, next: Optional['ListNode'] = None):
        self.val = val
        self.next = next

def has_cycle(head: Optional[ListNode]) -> bool:
    """
    Detect if linked list has a cycle
    Using Fast & Slow Pointers (Floyd's Cycle Detection)
    
    Concept visualization:
    
    No Cycle:
    1 → 2 → 3 → 4 → 5 → None
    
    Slow: 1 → 2 → 3 → 4 → 5 → None
    Fast: 1 → 3 → 5 → None
    Fast reaches None = NO CYCLE ✅
    
    
    With Cycle:
    1 → 2 → 3 → 4 → 5
            ↑       ↓
            ←←←←←←←←
    
    Round 1: S=1, F=1
    Round 2: S=2, F=3
    Round 3: S=3, F=5
    Round 4: S=4, F=4 → MEET! CYCLE DETECTED! ✅
    
    
    Why does this work?
    - If there's a cycle, fast & slow will eventually meet inside the cycle
    - Analogy: Like 2 runners on a circular track:
      The fast runner will catch up to the slow runner
    
    Args:
        head: Head node of the linked list
    
    Returns:
        True if cycle exists, False otherwise
    """
    if not head or not head.next:
        return False
    
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next        # Move 1 step
        fast = fast.next.next   # Move 2 steps
        
        if slow == fast:
            return True  # They meet = cycle exists!
    
    return False  # Fast reaches None = no cycle


def find_cycle_start(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Find the starting node of the cycle (if exists)
    
    Visualization:
    1 → 2 → 3 → 4 → 5
            ↑       ↓
            ←←←←←←←←
            
    Phase 1: Detect cycle (slow & fast meet at node X)
    Phase 2: Reset slow to head, both pointers move 1 step
            They will meet at START of cycle (node 3)
    
    Args:
        head: Head node of the linked list
    
    Returns:
        Starting node of the cycle, or None if no cycle
    """
    if not head or not head.next:
        return None
    
    slow = head
    fast = head
    
    # Phase 1: Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        
        if slow == fast:
            # Cycle detected! Now find the start
            slow = head
            
            # Phase 2: Find cycle start
            while slow != fast:
                slow = slow.next
                fast = fast.next
            
            return slow  # Start of cycle
    
    return None  # No cycle


def find_middle_node(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Find the middle node of the linked list
    
    Visualization:
    
    Odd length (5 nodes):
    1 → 2 → 3 → 4 → 5 → None
            ↑
         middle
    
    Step by step:
    Initial: S=1, F=1
    Step 1:  S=2, F=3
    Step 2:  S=3, F=5
    Step 3:  S still at 3, F=None (stop)
    Return S (node 3)
    
    
    Even length (6 nodes):
    1 → 2 → 3 → 4 → 5 → 6 → None
                ↑
             middle (second middle)
    
    Args:
        head: Head node of the linked list
    
    Returns:
        Middle node of the linked list
    """
    if not head:
        return None
    
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: DETECTING CYCLE IN LINKED LIST")
    print("=" * 70)
    
    # Create linked list WITH cycle: 1 → 2 → 3 → 4 → 5 → (back to 3)
    node1 = ListNode(1)
    node2 = ListNode(2)
    node3 = ListNode(3)
    node4 = ListNode(4)
    node5 = ListNode(5)
    
    node1.next = node2
    node2.next = node3
    node3.next = node4
    node4.next = node5
    node5.next = node3  # Create cycle back to node 3
    
    print("\nLinked List structure:")
    print("1 → 2 → 3 → 4 → 5")
    print("        ↑       ↓")
    print("        ←←←←←←←←")
    print("(node 5 points back to node 3)\n")
    
    # Simulate step by step
    print("Fast & Slow Pointers Simulation:\n")
    slow = node1
    fast = node1
    step = 0
    
    while step < 10:  # Limit iterations for demo
        print(f"Step {step}:")
        print(f"  Slow pointer at node: {slow.val}")
        print(f"  Fast pointer at node: {fast.val}")
        
        if step > 0 and slow == fast:
            print(f"  ✅ MEET! Cycle detected!\n")
            break
        
        slow = slow.next
        fast = fast.next.next
        step += 1
        print()
    
    # Test with function
    result = has_cycle(node1)
    print(f"has_cycle() result: {result}")
    
    cycle_start = find_cycle_start(node1)
    if cycle_start:
        print(f"Cycle starts at node: {cycle_start.val}")
    
    print("\n" + "=" * 70)
    print("CASE 2: LINKED LIST WITHOUT CYCLE")
    print("=" * 70)
    
    # Create linked list WITHOUT cycle: 1 → 2 → 3 → 4 → 5 → None
    node_a = ListNode(1)
    node_b = ListNode(2)
    node_c = ListNode(3)
    node_d = ListNode(4)
    node_e = ListNode(5)
    
    node_a.next = node_b
    node_b.next = node_c
    node_c.next = node_d
    node_d.next = node_e
    # node_e.next = None (no cycle)
    
    print("\nLinked List structure:")
    print("1 → 2 → 3 → 4 → 5 → None\n")
    
    result = has_cycle(node_a)
    print(f"has_cycle() result: {result}")
    print("Fast pointer reaches None = no cycle ✅")
    
    print("\n" + "=" * 70)
    print("CASE 3: FINDING MIDDLE NODE")
    print("=" * 70)
    
    print("\nLinked List: 1 → 2 → 3 → 4 → 5 → None")
    middle = find_middle_node(node_a)
    if middle:
        print(f"Middle node: {middle.val}")
        print("\nVisualization:")
        print("1 → 2 → 3 → 4 → 5 → None")
        print("        ↑")
        print("      middle")
    
    # Test with even length
    print("\n" + "=" * 70)
    print("CASE 4: MIDDLE NODE (EVEN LENGTH)")
    print("=" * 70)
    
    node_f = ListNode(6)
    node_e.next = node_f
    
    print("\nLinked List: 1 → 2 → 3 → 4 → 5 → 6 → None")
    middle = find_middle_node(node_a)
    if middle:
        print(f"Middle node: {middle.val}")
        print("\nVisualization:")
        print("1 → 2 → 3 → 4 → 5 → 6 → None")
        print("            ↑")
        print("          middle")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: RUNNERS ON A TRACK")
    print("=" * 70)
    
    print("""
Imagine 2 runners on a circular track:
- SLOW runner: runs 1 lap per minute
- FAST runner: runs 2 laps per minute

If the track is circular (has a cycle):
→ Fast runner will CATCH UP to slow runner
→ They will MEET at some point

If the track is straight (no cycle):
→ Fast runner will FINISH first
→ They will NOT MEET

This is exactly how Fast & Slow Pointers work! 🏃‍♂️💨
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: DETECTING CYCLE IN LINKED LIST
======================================================================

Linked List structure:
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←
(node 5 points back to node 3)

Fast & Slow Pointers Simulation:

Step 0:
  Slow pointer at node: 1
  Fast pointer at node: 1

Step 1:
  Slow pointer at node: 2
  Fast pointer at node: 3

Step 2:
  Slow pointer at node: 3
  Fast pointer at node: 5

Step 3:
  Slow pointer at node: 4
  Fast pointer at node: 4
  ✅ MEET! Cycle detected!

has_cycle() result: True
Cycle starts at node: 3

======================================================================
CASE 2: LINKED LIST WITHOUT CYCLE
======================================================================

Linked List structure:
1 → 2 → 3 → 4 → 5 → None

has_cycle() result: False
Fast pointer reaches None = no cycle ✅

======================================================================
CASE 3: FINDING MIDDLE NODE
======================================================================

Linked List: 1 → 2 → 3 → 4 → 5 → None
Middle node: 3

Visualization:
1 → 2 → 3 → 4 → 5 → None
        ↑
      middle

======================================================================
CASE 4: MIDDLE NODE (EVEN LENGTH)
======================================================================

Linked List: 1 → 2 → 3 → 4 → 5 → 6 → None
Middle node: 4

Visualization:
1 → 2 → 3 → 4 → 5 → 6 → None
            ↑
          middle

======================================================================
REAL WORLD ANALOGY: RUNNERS ON A TRACK
======================================================================

Imagine 2 runners on a circular track:
- SLOW runner: runs 1 lap per minute
- FAST runner: runs 2 laps per minute

If the track is circular (has a cycle):
→ Fast runner will CATCH UP to slow runner
→ They will MEET at some point

If the track is straight (no cycle):
→ Fast runner will FINISH first
→ They will NOT MEET

This is exactly how Fast & Slow Pointers work! 🏃‍♂️💨
```

## 🎯 When to Use This Pattern?

- ✅ Detecting **cycles** in linked lists or graphs
- ✅ Finding **middle element** of linked list
- ✅ Finding **k-th element from end** in linked list
- ✅ Problems involving **linked list traversal**

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Worst case traverse entire list
- **Space**: O(1) - Only need 2 pointer variables
- **Advantage**: No extra space needed for tracking (like HashSet)

## 🔗 LeetCode Problems

1. [Linked List Cycle (LeetCode #141)](https://leetcode.com/problems/linked-list-cycle/)
2. [Linked List Cycle II (LeetCode #142)](https://leetcode.com/problems/linked-list-cycle-ii/)
3. [Middle of the Linked List (LeetCode #876)](https://leetcode.com/problems/middle-of-the-linked-list/)
4. [Happy Number (LeetCode #202)](https://leetcode.com/problems/happy-number/)
5. [Find the Duplicate Number (LeetCode #287)](https://leetcode.com/problems/find-the-duplicate-number/)

---

**Status**: ✅ REVIEWED & APPROVED
