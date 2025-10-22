# Pattern 7: Top 'K' Elements

## 🌍 Real World Case
Bayangkan Anda bekerja di **e-commerce** dan ingin menampilkan **5 produk terlaris** bulan ini dari jutaan produk. Tanpa Top K pattern, Anda harus sort semua produk (sangat lambat!). Dengan **Min Heap berukuran K**, Anda hanya perlu maintain 5 produk teratas saja - jauh lebih efisien!

Contoh lain: **Spotify** mencari "Top 10 lagu yang paling sering diputar minggu ini" dari miliaran streams.

## 📊 Visualisasi Konsep
```
Problem: Cari 3 angka terbesar dari [3, 2, 1, 5, 6, 4]

Naive approach (Sort semua): O(n log n)
[3, 2, 1, 5, 6, 4] → sort → [1, 2, 3, 4, 5, 6] → ambil 3 terakhir → [4, 5, 6]

Top K approach (Min Heap size K): O(n log k)
Min Heap (size 3):

Step 1: Add 3
Heap: [3]
       3

Step 2: Add 2
Heap: [2, 3]
       2
        \
         3

Step 3: Add 1
Heap: [1, 2, 3]
       1
      / \
     2   3

Step 4: Add 5 (heap full, check root)
5 > 1 (root), remove 1, add 5
Heap: [2, 3, 5]
       2
      / \
     3   5

Step 5: Add 6
6 > 2 (root), remove 2, add 6
Heap: [3, 5, 6]
       3
      / \
     5   6

Step 6: Add 4
4 > 3 (root), remove 3, add 4
Heap: [4, 5, 6]
       4
      / \
     5   6

Result: [4, 5, 6] adalah 3 terbesar!

Kenapa Min Heap?
- Min Heap size K menjaga K elemen terbesar
- Root (minimum) adalah "gatekeeper"
- Jika element baru > root, ganti root
- Root selalu adalah K-th largest element
```

## 💻 Code Python dengan Visualisasi

```python
import heapq
from typing import List

def find_kth_largest(nums: List[int], k: int) -> int:
    """
    Cari K-th largest element menggunakan Min Heap
    
    Visualisasi proses:
    nums = [3, 2, 1, 5, 6, 4], k = 2
    Goal: Cari 2nd largest element
    
    Min Heap (size = k = 2):
    
    Step 1: Add 3
    Heap: [3]
    
    Step 2: Add 2
    Heap: [2, 3] (min heap property)
    
    Step 3: Add 1
    Heap full! 1 < 2 (root), skip
    Heap: [2, 3]
    
    Step 4: Add 5
    5 > 2 (root), remove 2, add 5
    Heap: [3, 5]
    
    Step 5: Add 6
    6 > 3 (root), remove 3, add 6
    Heap: [5, 6]
    
    Step 6: Add 4
    4 < 5 (root), skip
    Heap: [5, 6]
    
    Root of heap = 5 = 2nd largest! ✅
    
    Kenapa ini bekerja?
    - Min heap size K berisi K largest elements
    - Root (minimum di heap) = K-th largest element
    
    Args:
        nums: Array of integers
        k: Find k-th largest element
    
    Returns:
        The k-th largest element
    """
    # Create min heap of size k
    min_heap = []
    
    for num in nums:
        if len(min_heap) < k:
            heapq.heappush(min_heap, num)
        elif num > min_heap[0]:
            heapq.heapreplace(min_heap, num)
    
    return min_heap[0]  # Root is k-th largest


def top_k_frequent(nums: List[int], k: int) -> List[int]:
    """
    Cari K most frequent elements
    
    Visualisasi:
    nums = [1,1,1,2,2,3], k = 2
    
    Step 1: Count frequency
    {1: 3, 2: 2, 3: 1}
    
    Step 2: Use Min Heap (size k) based on frequency
    
    Process 1 (freq=3):
    Heap: [(3, 1)]
    
    Process 2 (freq=2):
    Heap: [(2, 2), (3, 1)]
    
    Process 3 (freq=1):
    1 < 2 (root frequency), skip
    Heap: [(2, 2), (3, 1)]
    
    Result: [1, 2] (elements with top 2 frequencies)
    
    Args:
        nums: Array of integers
        k: Number of most frequent elements to return
    
    Returns:
        K most frequent elements
    """
    from collections import Counter
    
    # Count frequencies
    count = Counter(nums)
    
    # Min heap (size k) with (frequency, element)
    min_heap = []
    
    for num, freq in count.items():
        if len(min_heap) < k:
            heapq.heappush(min_heap, (freq, num))
        elif freq > min_heap[0][0]:
            heapq.heapreplace(min_heap, (freq, num))
    
    return [num for freq, num in min_heap]


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: FIND K-TH LARGEST ELEMENT")
    print("=" * 70)
    
    nums = [3, 2, 1, 5, 6, 4]
    k = 2
    
    print(f"\nArray: {nums}")
    print(f"Find: {k}-th largest element")
    
    print("\nProses dengan Min Heap (size k):\n")
    
    # Simulate step by step
    min_heap = []
    
    for i, num in enumerate(nums):
        print(f"Step {i + 1}: Process {num}")
        
        if len(min_heap) < k:
            heapq.heappush(min_heap, num)
            print(f"  Heap belum penuh, push {num}")
        elif num > min_heap[0]:
            old_root = min_heap[0]
            heapq.heapreplace(min_heap, num)
            print(f"  {num} > {old_root} (root), replace!")
        else:
            print(f"  {num} <= {min_heap[0]} (root), skip")
        
        print(f"  Heap: {sorted(min_heap)}")
        print(f"  Root (min): {min_heap[0] if min_heap else 'empty'}")
        print()
    
    result = find_kth_largest(nums, k)
    print(f"Result: {k}-th largest = {result}")
    print(f"\nVerifikasi: Sorted array = {sorted(nums, reverse=True)}")
    print(f"{k}-th element = {sorted(nums, reverse=True)[k-1]} ✅")
    
    print("\n" + "=" * 70)
    print("CASE 2: TOP K FREQUENT ELEMENTS")
    print("=" * 70)
    
    nums = [1, 1, 1, 2, 2, 3]
    k = 2
    
    print(f"\nArray: {nums}")
    print(f"Find: Top {k} most frequent elements")
    
    from collections import Counter
    count = Counter(nums)
    
    print(f"\nFrequency count: {dict(count)}")
    
    result = top_k_frequent(nums, k)
    print(f"\nTop {k} frequent elements: {result}")
    
    print("\n" + "=" * 70)
    print("CASE 3: TOP K LARGEST IN DATA STREAM")
    print("=" * 70)
    
    print("\nScenario: Real-time leaderboard (Top 3 scores)")
    print("Scores coming in: [10, 5, 15, 20, 8, 25, 12]")
    
    k = 3
    min_heap = []
    
    scores = [10, 5, 15, 20, 8, 25, 12]
    
    for i, score in enumerate(scores):
        if len(min_heap) < k:
            heapq.heappush(min_heap, score)
        elif score > min_heap[0]:
            heapq.heapreplace(min_heap, score)
        
        top_k = sorted(min_heap, reverse=True)
        print(f"Score {score} received → Top 3: {top_k}")
    
    print(f"\nFinal Top 3: {sorted(min_heap, reverse=True)}")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: E-COMMERCE TOP PRODUCTS")
    print("=" * 70)
    
    print("""
Scenario: Website dengan 1,000,000 produk
Goal: Tampilkan "Top 5 Best Sellers" di homepage

Method 1 - Naive (Sort semua):
- Sort 1 juta produk berdasarkan sales → O(n log n)
- Ambil 5 teratas
- Time: O(1,000,000 × log 1,000,000) ≈ 20 juta operasi 😱

Method 2 - Top K dengan Min Heap:
- Maintain heap size 5 saja
- Scan semua produk → O(n log k)
- Time: O(1,000,000 × log 5) ≈ 2.3 juta operasi 🚀
- 8-9x lebih cepat!

Use cases:
✅ Trending products
✅ Top rated items
✅ Most viewed articles
✅ Highest earning users
✅ Leaderboards
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Top K Pattern dengan Heap:

1. 🎯 Goal: Find K largest → Use MIN heap (size k)
   Goal: Find K smallest → Use MAX heap (size k)

2. 📏 Heap size = K (constant space relative to input)

3. 🔄 Algorithm:
   - Build heap dengan K elements pertama
   - Untuk setiap element sisanya:
     * Compare dengan root
     * Jika better, replace root
   
4. ⚡ Result: Root = K-th element, Heap = Top K elements

Time Complexity:
- Build initial heap: O(k)
- Process remaining (n-k) elements: O((n-k) log k)
- Total: O(n log k)

Space Complexity:
- O(k) - Only store k elements in heap

Kapan digunakan?
✅ "Find top/bottom K elements"
✅ "K-th largest/smallest"
✅ "K most frequent"
✅ Real-time ranking/leaderboard
    """)
```

## 📤 Output Ketika Dijalankan

```
======================================================================
CASE 1: FIND K-TH LARGEST ELEMENT
======================================================================

Array: [3, 2, 1, 5, 6, 4]
Find: 2-th largest element

Proses dengan Min Heap (size k):

Step 1: Process 3
  Heap belum penuh, push 3
  Heap: [3]
  Root (min): 3

Step 2: Process 2
  Heap belum penuh, push 2
  Heap: [2, 3]
  Root (min): 2

Step 3: Process 1
  1 <= 2 (root), skip
  Heap: [2, 3]
  Root (min): 2

Step 4: Process 5
  5 > 2 (root), replace!
  Heap: [3, 5]
  Root (min): 3

Step 5: Process 6
  6 > 3 (root), replace!
  Heap: [5, 6]
  Root (min): 5

Step 6: Process 4
  4 <= 5 (root), skip
  Heap: [5, 6]
  Root (min): 5

Result: 2-th largest = 5

Verifikasi: Sorted array = [6, 5, 4, 3, 2, 1]
2-th element = 5 ✅

======================================================================
CASE 2: TOP K FREQUENT ELEMENTS
======================================================================

Array: [1, 1, 1, 2, 2, 3]
Find: Top 2 most frequent elements

Frequency count: {1: 3, 2: 2, 3: 1}

Top 2 frequent elements: [2, 1]

======================================================================
CASE 3: TOP K LARGEST IN DATA STREAM
======================================================================

Scenario: Real-time leaderboard (Top 3 scores)
Scores coming in: [10, 5, 15, 20, 8, 25, 12]

Score 10 received → Top 3: [10]
Score 5 received → Top 3: [10, 5]
Score 15 received → Top 3: [15, 10, 5]
Score 20 received → Top 3: [20, 15, 10]
Score 8 received → Top 3: [20, 15, 10]
Score 25 received → Top 3: [25, 20, 15]
Score 12 received → Top 3: [25, 20, 15]

Final Top 3: [25, 20, 15]

======================================================================
REAL WORLD ANALOGY: E-COMMERCE TOP PRODUCTS
======================================================================

Scenario: Website dengan 1,000,000 produk
Goal: Tampilkan "Top 5 Best Sellers" di homepage

Method 1 - Naive (Sort semua):
- Sort 1 juta produk berdasarkan sales → O(n log n)
- Ambil 5 teratas
- Time: O(1,000,000 × log 1,000,000) ≈ 20 juta operasi 😱

Method 2 - Top K dengan Min Heap:
- Maintain heap size 5 saja
- Scan semua produk → O(n log k)
- Time: O(1,000,000 × log 5) ≈ 2.3 juta operasi 🚀
- 8-9x lebih cepat!

Use cases:
✅ Trending products
✅ Top rated items
✅ Most viewed articles
✅ Highest earning users
✅ Leaderboards

======================================================================
KEY INSIGHTS
======================================================================

Top K Pattern dengan Heap:

1. 🎯 Goal: Find K largest → Use MIN heap (size k)
   Goal: Find K smallest → Use MAX heap (size k)

2. 📏 Heap size = K (constant space relative to input)

3. 🔄 Algorithm:
   - Build heap dengan K elements pertama
   - Untuk setiap element sisanya:
     * Compare dengan root
     * Jika better, replace root
   
4. ⚡ Result: Root = K-th element, Heap = Top K elements

Time Complexity:
- Build initial heap: O(k)
- Process remaining (n-k) elements: O((n-k) log k)
- Total: O(n log k)

Space Complexity:
- O(k) - Only store k elements in heap

Kapan digunakan?
✅ "Find top/bottom K elements"
✅ "K-th largest/smallest"
✅ "K most frequent"
✅ Real-time ranking/leaderboard
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Mencari **K largest/smallest elements**
- ✅ Problem dengan kata kunci: "top K", "K-th largest", "K most frequent"
- ✅ **Streaming data** yang perlu maintain top K real-time
- ✅ Optimasi dari O(n log n) sort menjadi O(n log k)

## ⏱️ Time & Space Complexity

- **Time**: O(n log k) - Much better than O(n log n) when k << n
- **Space**: O(k) - Only store k elements in heap
- **Keunggulan**: Sangat efisien untuk k kecil dibanding n besar

## 🔗 LeetCode Problems

1. [Kth Largest Element in an Array (LeetCode #215)](https://leetcode.com/problems/kth-largest-element-in-an-array/)
2. [Top K Frequent Elements (LeetCode #347)](https://leetcode.com/problems/top-k-frequent-elements/)
3. [K Closest Points to Origin (LeetCode #973)](https://leetcode.com/problems/k-closest-points-to-origin/)
4. [Find K Pairs with Smallest Sums (LeetCode #373)](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)

---

**Status**: ✅ REVIEWED & APPROVED
