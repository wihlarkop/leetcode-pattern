# Pattern 7: Top 'K' Elements

## 🌍 Real World Case
Imagine you work at an **e-commerce company** and want to display **5 best-selling products** this month from millions of products. Without the Top K pattern, you'd have to sort all products (very slow!). With a **Min Heap of size K**, you only need to maintain the top 5 products - much more efficient!

Another example: **Spotify** finding "Top 10 most played songs this week" from billions of streams.

## 📊 Concept Visualization
```
Problem: Find 3 largest numbers from [3, 2, 1, 5, 6, 4]

Naive approach (Sort all): O(n log n)
[3, 2, 1, 5, 6, 4] → sort → [1, 2, 3, 4, 5, 6] → take last 3 → [4, 5, 6]

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

Result: [4, 5, 6] are the 3 largest!

Why Min Heap?
- Min Heap of size K maintains K largest elements
- Root (minimum) is the "gatekeeper"
- If new element > root, replace root
- Root is always the K-th largest element
```

## 💻 Python Code with Visualization

```python
import heapq
from typing import List

def find_kth_largest(nums: List[int], k: int) -> int:
    """
    Find K-th largest element using Min Heap
    
    Process visualization:
    nums = [3, 2, 1, 5, 6, 4], k = 2
    Goal: Find 2nd largest element
    
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
    
    Why does this work?
    - Min heap of size K contains K largest elements
    - Root (minimum in heap) = K-th largest element
    
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
    Find K most frequent elements
    
    Visualization:
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
    
    print("\nProcess with Min Heap (size k):\n")
    
    # Simulate step by step
    min_heap = []
    
    for i, num in enumerate(nums):
        print(f"Step {i + 1}: Process {num}")
        
        if len(min_heap) < k:
            heapq.heappush(min_heap, num)
            print(f"  Heap not full, push {num}")
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
    print(f"\nVerification: Sorted array = {sorted(nums, reverse=True)}")
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
Scenario: Website with 1,000,000 products
Goal: Display "Top 5 Best Sellers" on homepage

Method 1 - Naive (Sort all):
- Sort 1 million products by sales → O(n log n)
- Take top 5
- Time: O(1,000,000 × log 1,000,000) ≈ 20 million operations 😱

Method 2 - Top K with Min Heap:
- Maintain heap of size 5 only
- Scan all products → O(n log k)
- Time: O(1,000,000 × log 5) ≈ 2.3 million operations 🚀
- 8-9x faster!

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
Top K Pattern with Heap:

1. 🎯 Goal: Find K largest → Use MIN heap (size k)
   Goal: Find K smallest → Use MAX heap (size k)

2. 📏 Heap size = K (constant space relative to input)

3. 🔄 Algorithm:
   - Build heap with first K elements
   - For each remaining element:
     * Compare with root
     * If better, replace root
   
4. ⚡ Result: Root = K-th element, Heap = Top K elements

Time Complexity:
- Build initial heap: O(k)
- Process remaining (n-k) elements: O((n-k) log k)
- Total: O(n log k)

Space Complexity:
- O(k) - Only store k elements in heap

When to use?
✅ "Find top/bottom K elements"
✅ "K-th largest/smallest"
✅ "K most frequent"
✅ Real-time ranking/leaderboard
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: FIND K-TH LARGEST ELEMENT
======================================================================

Array: [3, 2, 1, 5, 6, 4]
Find: 2-th largest element

Process with Min Heap (size k):

Step 1: Process 3
  Heap not full, push 3
  Heap: [3]
  Root (min): 3

Step 2: Process 2
  Heap not full, push 2
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

Verification: Sorted array = [6, 5, 4, 3, 2, 1]
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

Scenario: Website with 1,000,000 products
Goal: Display "Top 5 Best Sellers" on homepage

Method 1 - Naive (Sort all):
- Sort 1 million products by sales → O(n log n)
- Take top 5
- Time: O(1,000,000 × log 1,000,000) ≈ 20 million operations 😱

Method 2 - Top K with Min Heap:
- Maintain heap of size 5 only
- Scan all products → O(n log k)
- Time: O(1,000,000 × log 5) ≈ 2.3 million operations 🚀
- 8-9x faster!

Use cases:
✅ Trending products
✅ Top rated items
✅ Most viewed articles
✅ Highest earning users
✅ Leaderboards

======================================================================
KEY INSIGHTS
======================================================================

Top K Pattern with Heap:

1. 🎯 Goal: Find K largest → Use MIN heap (size k)
   Goal: Find K smallest → Use MAX heap (size k)

2. 📏 Heap size = K (constant space relative to input)

3. 🔄 Algorithm:
   - Build heap with first K elements
   - For each remaining element:
     * Compare with root
     * If better, replace root
   
4. ⚡ Result: Root = K-th element, Heap = Top K elements

Time Complexity:
- Build initial heap: O(k)
- Process remaining (n-k) elements: O((n-k) log k)
- Total: O(n log k)

Space Complexity:
- O(k) - Only store k elements in heap

When to use?
✅ "Find top/bottom K elements"
✅ "K-th largest/smallest"
✅ "K most frequent"
✅ Real-time ranking/leaderboard
```

## 🎯 When to Use This Pattern?

- ✅ Finding **K largest/smallest elements**
- ✅ Problems with keywords: "top K", "K-th largest", "K most frequent"
- ✅ **Streaming data** that needs to maintain top K in real-time
- ✅ Optimization from O(n log n) sort to O(n log k)

## ⏱️ Time & Space Complexity

- **Time**: O(n log k) - Much better than O(n log n) when k << n
- **Space**: O(k) - Only store k elements in heap
- **Advantage**: Very efficient for small k compared to large n

## 🔗 LeetCode Problems

1. [Kth Largest Element in an Array (LeetCode #215)](https://leetcode.com/problems/kth-largest-element-in-an-array/)
2. [Top K Frequent Elements (LeetCode #347)](https://leetcode.com/problems/top-k-frequent-elements/)
3. [K Closest Points to Origin (LeetCode #973)](https://leetcode.com/problems/k-closest-points-to-origin/)
4. [Find K Pairs with Smallest Sums (LeetCode #373)](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)

---

**Status**: ✅ REVIEWED & APPROVED
