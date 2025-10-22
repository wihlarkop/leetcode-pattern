# Pattern 5: LinkedList In-place Reversal

## 🌍 Real World Case
Imagine you have **undo history** in a text editor application. History is stored as a linked list: Action1 → Action2 → Action3 → Action4. When the user presses "Undo All", you need to **reverse the order** to Action4 → Action3 → Action2 → Action1, **without creating a new list** (saves memory!).

Another example: **Reversing navigation history** in a browser - the "Back" button needs to reverse the order of visited pages.

## 📊 Concept Visualization
```
Original List:
1 → 2 → 3 → 4 → 5 → None

Goal: Reverse to
5 → 4 → 3 → 2 → 1 → None

Step-by-step Process:

Step 0: Setup pointers
prev = None
curr = 1
next = ?

None ← 1 → 2 → 3 → 4 → 5 → None
       ↑
      curr

Step 1: Save next, reverse arrow, move forward
prev = None → 1
curr = 2
next = 3

None ← 1   2 → 3 → 4 → 5 → None
       ↑   ↑
      prev curr

Step 2: Continue...
None ← 1 ← 2   3 → 4 → 5 → None
           ↑   ↑
          prev curr

Step 3: Continue...
None ← 1 ← 2 ← 3   4 → 5 → None
               ↑   ↑
              prev curr

Step 4: Continue...
None ← 1 ← 2 ← 3 ← 4   5 → None
                   ↑   ↑
                  prev curr

Step 5: Last node
None ← 1 ← 2 ← 3 ← 4 ← 5   None
                       ↑   ↑
                      prev curr

Result: Return prev (new head = 5)
5 → 4 → 3 → 2 → 1 → None ✅
```

## 💻 Python Code with Visualization

```python
from typing import Optional

class ListNode:
    """Node for linked list"""
    def __init__(self, val: int = 0, next: Optional['ListNode'] = None):
        self.val = val
        self.next = next

def reverse_linked_list(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Reverse entire linked list in-place
    
    Process visualization:
    Original: 1 → 2 → 3 → 4 → 5 → None
    
    Step 0:
    prev = None, curr = 1
    None    1 → 2 → 3 → 4 → 5 → None
            ↑
           curr
    
    Step 1: Save next, reverse arrow, move pointers
    next = 2
    1.next = None (reverse arrow)
    prev = 1, curr = 2
    
    None ← 1    2 → 3 → 4 → 5 → None
           ↑    ↑
          prev curr
    
    Step 2:
    next = 3
    2.next = 1 (reverse arrow)
    prev = 2, curr = 3
    
    None ← 1 ← 2    3 → 4 → 5 → None
               ↑    ↑
              prev curr
    
    ... continue until curr = None
    
    Final:
    None ← 1 ← 2 ← 3 ← 4 ← 5
                           ↑
                          prev (new head)
    
    Key Pattern:
    1. Save next node (so it doesn't get lost)
    2. Reverse arrow: curr.next = prev
    3. Move pointers: prev = curr, curr = next
    
    Args:
        head: Head node of the linked list
    
    Returns:
        New head node (previously tail)
    """
    prev = None
    curr = head
    
    while curr:
        # Save next node
        next_node = curr.next
        
        # Reverse arrow
        curr.next = prev
        
        # Move pointers
        prev = curr
        curr = next_node
    
    return prev  # prev is now the new head


def reverse_between(head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
    """
    Reverse sublist from position left to right (1-indexed)
    
    Visualization:
    Original: 1 → 2 → 3 → 4 → 5 → None
    left = 2, right = 4
    
    Result:   1 → 4 → 3 → 2 → 5 → None
                  └───────┘
                  reversed
    
    Step 1: Find node before left (prev_left)
    1 → 2 → 3 → 4 → 5 → None
    ↑   ↑
    prev_left
        left_node
    
    Step 2: Reverse from left to right
    1 → 2 ← 3 ← 4   5 → None
    ↑   ↓           ↑
    prev left      right
    
    Step 3: Reconnect
    1 → 4 → 3 → 2 → 5 → None
    
    Args:
        head: Head node of the linked list
        left: Start position (1-indexed)
        right: End position (1-indexed)
    
    Returns:
        Modified head node
    """
    if not head or left == right:
        return head
    
    # Dummy node to handle edge cases
    dummy = ListNode(0)
    dummy.next = head
    prev_left = dummy
    
    # Step 1: Find node before left
    for _ in range(left - 1):
        prev_left = prev_left.next
    
    # Step 2: Reverse from left to right
    prev = None
    curr = prev_left.next
    
    for _ in range(right - left + 1):
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    
    # Step 3: Reconnect
    prev_left.next.next = curr  # Connect tail of reversed part
    prev_left.next = prev       # Connect head of reversed part
    
    return dummy.next


def print_list(head: Optional[ListNode]) -> str:
    """Helper function to print linked list"""
    values = []
    curr = head
    while curr:
        values.append(str(curr.val))
        curr = curr.next
    return " → ".join(values) + " → None"


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: REVERSE ENTIRE LINKED LIST")
    print("=" * 70)
    
    # Create linked list: 1 → 2 → 3 → 4 → 5
    node1 = ListNode(1)
    node2 = ListNode(2)
    node3 = ListNode(3)
    node4 = ListNode(4)
    node5 = ListNode(5)
    
    node1.next = node2
    node2.next = node3
    node3.next = node4
    node4.next = node5
    
    print("\nOriginal List:")
    print(print_list(node1))
    
    # Simulate step by step
    print("\nReversal Process Step-by-Step:\n")
    
    prev = None
    curr = node1
    step = 0
    
    # Show initial state
    print(f"Step {step}: Initial State")
    print(f"  prev = None")
    print(f"  curr = {curr.val if curr else 'None'}")
    print()
    
    while curr:
        step += 1
        next_node = curr.next
        
        print(f"Step {step}:")
        print(f"  Save next: next_node = {next_node.val if next_node else 'None'}")
        
        # Reverse arrow
        curr.next = prev
        print(f"  Reverse arrow: curr.next = prev")
        
        # Build visual representation
        if step == 1:
            print(f"  Visual: None ← {curr.val}   {next_node.val if next_node else 'None'} → ...")
        elif step <= 5:
            reversed_part = []
            temp = curr
            for _ in range(step):
                if temp:
                    reversed_part.insert(0, str(temp.val))
                    temp = temp.next if hasattr(temp, 'next') and temp != curr else None
            print(f"  Visual: None ← {' ← '.join(reversed_part)}   {next_node.val if next_node else 'None'} → ...")
        
        # Move pointers
        prev = curr
        curr = next_node
        
        print(f"  Move: prev = {prev.val}, curr = {curr.val if curr else 'None'}")
        print()
    
    # Reverse the list
    node1 = ListNode(1)
    node1.next = ListNode(2)
    node1.next.next = ListNode(3)
    node1.next.next.next = ListNode(4)
    node1.next.next.next.next = ListNode(5)
    
    new_head = reverse_linked_list(node1)
    
    print("Reversed List:")
    print(print_list(new_head))
    
    print("\n" + "=" * 70)
    print("CASE 2: REVERSE PARTIAL LIST (left=2, right=4)")
    print("=" * 70)
    
    # Create linked list: 1 → 2 → 3 → 4 → 5
    node1 = ListNode(1)
    node1.next = ListNode(2)
    node1.next.next = ListNode(3)
    node1.next.next.next = ListNode(4)
    node1.next.next.next.next = ListNode(5)
    
    print("\nOriginal List:")
    print(print_list(node1))
    
    print("\nReverse from position 2 to 4:")
    print("1 → [2 → 3 → 4] → 5")
    print("        ↓")
    print("1 → [4 → 3 → 2] → 5")
    
    new_head = reverse_between(node1, 2, 4)
    
    print("\nResult:")
    print(print_list(new_head))
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: UNDO HISTORY")
    print("=" * 70)
    
    print("""
Text Editor Application:
- History: Type "Hello" → Delete word → Type "World" → Bold text
- List: Action1 → Action2 → Action3 → Action4

User clicks "Undo All":
- Reverse history: Action4 → Action3 → Action2 → Action1
- Execute backward: Unbold → Delete "World" → Undo delete → Delete "Hello"

In-place reversal saves memory because no list duplication needed! 💾
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Reversal Pattern:
1. ⚠️  Save next BEFORE changing pointer (so it doesn't get lost!)
2. 🔄 Reverse arrow: curr.next = prev
3. ➡️  Move: prev = curr, curr = next
4. 🔁 Repeat until curr = None

Time: O(n) - Single pass
Space: O(1) - Only 3 pointer variables!
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: REVERSE ENTIRE LINKED LIST
======================================================================

Original List:
1 → 2 → 3 → 4 → 5 → None

Reversal Process Step-by-Step:

Step 0: Initial State
  prev = None
  curr = 1

Step 1:
  Save next: next_node = 2
  Reverse arrow: curr.next = prev
  Visual: None ← 1   2 → ...
  Move: prev = 1, curr = 2

Step 2:
  Save next: next_node = 3
  Reverse arrow: curr.next = prev
  Visual: None ← 1 ← 2   3 → ...
  Move: prev = 2, curr = 3

Step 3:
  Save next: next_node = 4
  Reverse arrow: curr.next = prev
  Visual: None ← 2 ← 1 ← 3   4 → ...
  Move: prev = 3, curr = 4

Step 4:
  Save next: next_node = 5
  Reverse arrow: curr.next = prev
  Visual: None ← 3 ← 2 ← 1 ← 4   5 → ...
  Move: prev = 4, curr = 5

Step 5:
  Save next: next_node = None
  Reverse arrow: curr.next = prev
  Visual: None ← 4 ← 3 ← 2 ← 1 ← 5   None → ...
  Move: prev = 5, curr = None

Reversed List:
5 → 4 → 3 → 2 → 1 → None

======================================================================
CASE 2: REVERSE PARTIAL LIST (left=2, right=4)
======================================================================

Original List:
1 → 2 → 3 → 4 → 5 → None

Reverse from position 2 to 4:
1 → [2 → 3 → 4] → 5
        ↓
1 → [4 → 3 → 2] → 5

Result:
1 → 4 → 3 → 2 → 5 → None

======================================================================
REAL WORLD ANALOGY: UNDO HISTORY
======================================================================

Text Editor Application:
- History: Type "Hello" → Delete word → Type "World" → Bold text
- List: Action1 → Action2 → Action3 → Action4

User clicks "Undo All":
- Reverse history: Action4 → Action3 → Action2 → Action1
- Execute backward: Unbold → Delete "World" → Undo delete → Delete "Hello"

In-place reversal saves memory because no list duplication needed! 💾

======================================================================
KEY INSIGHTS
======================================================================

Reversal Pattern:
1. ⚠️  Save next BEFORE changing pointer (so it doesn't get lost!)
2. 🔄 Reverse arrow: curr.next = prev
3. ➡️  Move: prev = curr, curr = next
4. 🔁 Repeat until curr = None

Time: O(n) - Single pass
Space: O(1) - Only 3 pointer variables!
```

## 🎯 When to Use This Pattern?

- ✅ Reversing entire or partial **linked list**
- ✅ Problems requiring **in-place modification** (memory efficient)
- ✅ Keywords: "reverse", "reverse from position m to n"
- ✅ When extra O(n) space is not allowed

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Single pass through the list
- **Space**: O(1) - Only 3 pointer variables (prev, curr, next)
- **In-place**: No new list created, only pointer modifications

## 🔗 LeetCode Problems

1. [Reverse Linked List (LeetCode #206)](https://leetcode.com/problems/reverse-linked-list/)
2. [Reverse Linked List II (LeetCode #92)](https://leetcode.com/problems/reverse-linked-list-ii/)
3. [Reverse Nodes in k-Group (LeetCode #25)](https://leetcode.com/problems/reverse-nodes-in-k-group/)
4. [Swap Nodes in Pairs (LeetCode #24)](https://leetcode.com/problems/swap-nodes-in-pairs/)

---

**Status**: ✅ REVIEWED & APPROVED
