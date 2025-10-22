# Pattern 4: Fast & Slow Pointers (Tortoise and Hare)

## 🌍 Real World Case
Bayangkan Anda sedang **mengaudit playlist musik** di aplikasi streaming. Ada kemungkinan playlist memiliki **cycle** (lagu A → lagu B → lagu C → kembali ke lagu B, loop terus). Dengan Fast & Slow Pointers, Anda bisa **mendeteksi loop** tanpa perlu menyimpan semua lagu yang sudah dilewati!

Contoh lain: **Deteksi infinite redirect** di web crawler - jika URL A redirect ke B, B ke C, C kembali ke A (loop), crawler bisa stuck. Fast & Slow pointers mendeteksi ini.

## 📊 Visualisasi Konsep
```
Linked List dengan Cycle:
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
                S,F (BERTEMU!)
                ↓
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←

Result: CYCLE DETECTED! ✅
```

## 💻 Code Python dengan Visualisasi

```python
from typing import Optional

class ListNode:
    """Node untuk linked list"""
    def __init__(self, val: int = 0, next: Optional['ListNode'] = None):
        self.val = val
        self.next = next

def has_cycle(head: Optional[ListNode]) -> bool:
    """
    Deteksi apakah linked list memiliki cycle
    Menggunakan Fast & Slow Pointers (Floyd's Cycle Detection)
    
    Visualisasi konsep:
    
    No Cycle:
    1 → 2 → 3 → 4 → 5 → None
    
    Slow: 1 → 2 → 3 → 4 → 5 → None
    Fast: 1 → 3 → 5 → None
    Fast mencapai None = NO CYCLE ✅
    
    
    With Cycle:
    1 → 2 → 3 → 4 → 5
            ↑       ↓
            ←←←←←←←←
    
    Round 1: S=1, F=1
    Round 2: S=2, F=3
    Round 3: S=3, F=5
    Round 4: S=4, F=4 → BERTEMU! CYCLE DETECTED! ✅
    
    
    Kenapa ini bekerja?
    - Jika ada cycle, fast & slow pasti bertemu di dalam cycle
    - Analoginya seperti 2 pelari di track melingkar:
      Pelari cepat akan menyusul pelari lambat
    
    Args:
        head: Head node dari linked list
    
    Returns:
        True jika ada cycle, False jika tidak
    """
    if not head or not head.next:
        return False
    
    slow = head
    fast = head
    
    while fast and fast.next:
        slow = slow.next        # Gerak 1 langkah
        fast = fast.next.next   # Gerak 2 langkah
        
        if slow == fast:
            return True  # Bertemu = ada cycle!
    
    return False  # Fast mencapai None = tidak ada cycle


def find_cycle_start(head: Optional[ListNode]) -> Optional[ListNode]:
    """
    Menemukan node awal dari cycle (jika ada)
    
    Visualisasi:
    1 → 2 → 3 → 4 → 5
            ↑       ↓
            ←←←←←←←←
            
    Phase 1: Deteksi cycle (slow & fast bertemu di node X)
    Phase 2: Reset slow ke head, kedua pointer gerak 1 langkah
            Mereka akan bertemu di START of cycle (node 3)
    
    Args:
        head: Head node dari linked list
    
    Returns:
        Node awal dari cycle, atau None jika tidak ada cycle
    """
    if not head or not head.next:
        return None
    
    slow = head
    fast = head
    
    # Phase 1: Deteksi cycle
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
    Menemukan middle node dari linked list
    
    Visualisasi:
    
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
        head: Head node dari linked list
    
    Returns:
        Middle node dari linked list
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
    print("CASE 1: DETEKSI CYCLE DALAM LINKED LIST")
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
    
    print("\nLinked List struktur:")
    print("1 → 2 → 3 → 4 → 5")
    print("        ↑       ↓")
    print("        ←←←←←←←←")
    print("(node 5 menunjuk kembali ke node 3)\n")
    
    # Simulate step by step
    print("Simulasi Fast & Slow Pointers:\n")
    slow = node1
    fast = node1
    step = 0
    
    while step < 10:  # Limit iterations for demo
        print(f"Step {step}:")
        print(f"  Slow pointer at node: {slow.val}")
        print(f"  Fast pointer at node: {fast.val}")
        
        if step > 0 and slow == fast:
            print(f"  ✅ BERTEMU! Cycle terdeteksi!\n")
            break
        
        slow = slow.next
        fast = fast.next.next
        step += 1
        print()
    
    # Test dengan function
    result = has_cycle(node1)
    print(f"has_cycle() result: {result}")
    
    cycle_start = find_cycle_start(node1)
    if cycle_start:
        print(f"Cycle dimulai dari node: {cycle_start.val}")
    
    print("\n" + "=" * 70)
    print("CASE 2: LINKED LIST TANPA CYCLE")
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
    
    print("\nLinked List struktur:")
    print("1 → 2 → 3 → 4 → 5 → None\n")
    
    result = has_cycle(node_a)
    print(f"has_cycle() result: {result}")
    print("Fast pointer akan mencapai None = tidak ada cycle ✅")
    
    print("\n" + "=" * 70)
    print("CASE 3: MENEMUKAN MIDDLE NODE")
    print("=" * 70)
    
    print("\nLinked List: 1 → 2 → 3 → 4 → 5 → None")
    middle = find_middle_node(node_a)
    if middle:
        print(f"Middle node: {middle.val}")
        print("\nVisualisasi:")
        print("1 → 2 → 3 → 4 → 5 → None")
        print("        ↑")
        print("      middle")
    
    # Test dengan even length
    print("\n" + "=" * 70)
    print("CASE 4: MIDDLE NODE (EVEN LENGTH)")
    print("=" * 70)
    
    node_f = ListNode(6)
    node_e.next = node_f
    
    print("\nLinked List: 1 → 2 → 3 → 4 → 5 → 6 → None")
    middle = find_middle_node(node_a)
    if middle:
        print(f"Middle node: {middle.val}")
        print("\nVisualisasi:")
        print("1 → 2 → 3 → 4 → 5 → 6 → None")
        print("            ↑")
        print("          middle")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: PELARI DI TRACK")
    print("=" * 70)
    
    print("""
Bayangkan 2 pelari di track melingkar:
- Pelari LAMBAT: lari 1 putaran per menit
- Pelari CEPAT: lari 2 putaran per menit

Jika track melingkar (ada cycle):
→ Pelari cepat akan MENYUSUL pelari lambat
→ Mereka pasti BERTEMU di suatu titik

Jika track lurus (tidak ada cycle):
→ Pelari cepat akan FINISH duluan
→ Mereka TIDAK BERTEMU

Ini persis cara kerja Fast & Slow Pointers! 🏃‍♂️💨
    """)
```

## 📤 Output Ketika Dijalankan

```
======================================================================
CASE 1: DETEKSI CYCLE DALAM LINKED LIST
======================================================================

Linked List struktur:
1 → 2 → 3 → 4 → 5
        ↑       ↓
        ←←←←←←←←
(node 5 menunjuk kembali ke node 3)

Simulasi Fast & Slow Pointers:

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
  ✅ BERTEMU! Cycle terdeteksi!

has_cycle() result: True
Cycle dimulai dari node: 3

======================================================================
CASE 2: LINKED LIST TANPA CYCLE
======================================================================

Linked List struktur:
1 → 2 → 3 → 4 → 5 → None

has_cycle() result: False
Fast pointer akan mencapai None = tidak ada cycle ✅

======================================================================
CASE 3: MENEMUKAN MIDDLE NODE
======================================================================

Linked List: 1 → 2 → 3 → 4 → 5 → None
Middle node: 3

Visualisasi:
1 → 2 → 3 → 4 → 5 → None
        ↑
      middle

======================================================================
CASE 4: MIDDLE NODE (EVEN LENGTH)
======================================================================

Linked List: 1 → 2 → 3 → 4 → 5 → 6 → None
Middle node: 4

Visualisasi:
1 → 2 → 3 → 4 → 5 → 6 → None
            ↑
          middle

======================================================================
REAL WORLD ANALOGY: PELARI DI TRACK
======================================================================

Bayangkan 2 pelari di track melingkar:
- Pelari LAMBAT: lari 1 putaran per menit
- Pelari CEPAT: lari 2 putaran per menit

Jika track melingkar (ada cycle):
→ Pelari cepat akan MENYUSUL pelari lambat
→ Mereka pasti BERTEMU di suatu titik

Jika track lurus (tidak ada cycle):
→ Pelari cepat akan FINISH duluan
→ Mereka TIDAK BERTEMU

Ini persis cara kerja Fast & Slow Pointers! 🏃‍♂️💨
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Deteksi **cycle** dalam linked list atau graph
- ✅ Mencari **middle element** dari linked list
- ✅ Mencari **k-th element from end** dalam linked list
- ✅ Problem yang melibatkan **linked list traversal**

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Worst case traverse seluruh list
- **Space**: O(1) - Hanya butuh 2 pointer variables
- **Keunggulan**: Tidak perlu extra space untuk tracking (seperti HashSet)

## 🔗 LeetCode Problems

1. [Linked List Cycle (LeetCode #141)](https://leetcode.com/problems/linked-list-cycle/)
2. [Linked List Cycle II (LeetCode #142)](https://leetcode.com/problems/linked-list-cycle-ii/)
3. [Middle of the Linked List (LeetCode #876)](https://leetcode.com/problems/middle-of-the-linked-list/)
4. [Happy Number (LeetCode #202)](https://leetcode.com/problems/happy-number/)
5. [Find the Duplicate Number (LeetCode #287)](https://leetcode.com/problems/find-the-duplicate-number/)

---

**Status**: ✅ REVIEWED & APPROVED
