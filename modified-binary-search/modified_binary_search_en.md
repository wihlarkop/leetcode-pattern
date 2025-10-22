# Pattern 9: Modified Binary Search

## 🌍 Real World Case
Imagine you work at an **Amazon warehouse** with millions of products sorted by ID. When a customer orders a product with a specific ID, you need to find it **very quickly**. Binary Search allows you to find the product in **O(log n)** time - if there are 1 million products, it only takes a maximum of **20 steps**!

Another example: **Dictionary app** searching for words in alphabetical order, or **version control system** finding the commit that caused a bug (git bisect).

## 📊 Concept Visualization
```
Problem: Find number 7 in sorted array [1, 3, 5, 7, 9, 11, 13, 15]

Linear Search (O(n)):
Check: 1? No → 3? No → 5? No → 7? YES! ✅
Steps: 4 comparisons

Binary Search (O(log n)):
Array: [1, 3, 5, 7, 9, 11, 13, 15]
Index:  0  1  2  3  4   5   6   7

Step 1: Check middle
        L              M               R
Array: [1, 3, 5, 7, | 9, 11, 13, 15]
                    ↑
Middle = 9, Target = 7
7 < 9 → Search LEFT half

Step 2: Check middle of left half
        L      M      R
Array: [1, 3, 5, 7] | 9, 11, 13, 15
              ↑
Middle = 5, Target = 7
7 > 5 → Search RIGHT half

Step 3: Check middle of right half
              L  M/R
Array:  1, 3,[5, 7] | 9, 11, 13, 15
                 ↑
Middle = 7, Target = 7
FOUND! ✅

Steps: 3 comparisons (vs 4 in linear)
For 1 million items: 20 steps vs 1,000,000 steps! 🚀

Key Insight:
- Each step eliminates HALF of remaining elements
- log₂(n) steps needed
- Array MUST be sorted
```

## 💻 Python Code with Visualization

```python
from typing import List

def binary_search(arr: List[int], target: int) -> int:
    """
    Standard binary search - find index of target
    
    Process visualization:
    arr = [1, 3, 5, 7, 9, 11, 13, 15]
    target = 7
    
    Step 1:
    L=0, R=7, M=3
    arr[3] = 7 = target ✅
    
    Return 3
    
    Key Pattern:
    1. Initialize left=0, right=len-1
    2. While left <= right:
       - Calculate mid
       - If arr[mid] == target: found!
       - If arr[mid] < target: search right half
       - If arr[mid] > target: search left half
    3. Return -1 if not found
    
    Args:
        arr: Sorted array
        target: Value to find
    
    Returns:
        Index of target, or -1 if not found
    """
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2  # Avoid overflow
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1  # Search right half
        else:
            right = mid - 1  # Search left half
    
    return -1  # Not found


def search_rotated_array(arr: List[int], target: int) -> int:
    """
    Binary search on rotated sorted array
    
    Visualization:
    arr = [4, 5, 6, 7, 0, 1, 2]
    target = 0
    
    Original sorted: [0, 1, 2, 3, 4, 5, 6, 7]
    After rotation:  [4, 5, 6, 7, | 0, 1, 2]
                                  ↑ pivot
    
    Step 1: Find which half is sorted
    L=0, R=6, M=3
    arr[M] = 7
    arr[L] = 4 ≤ arr[M] = 7 → LEFT half is sorted
    
    Step 2: Check if target in sorted half
    target = 0
    4 ≤ 0 ≤ 7? NO → Search RIGHT half
    
    Step 3: Search right half
    L=4, R=6, M=5
    arr[M] = 1
    arr[L] = 0 ≤ arr[M] = 1 → LEFT half is sorted
    0 ≤ 0 ≤ 1? YES → Search LEFT half
    
    Step 4:
    L=4, R=4, M=4
    arr[M] = 0 = target ✅
    
    Args:
        arr: Rotated sorted array
        target: Value to find
    
    Returns:
        Index of target, or -1 if not found
    """
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if arr[mid] == target:
            return mid
        
        # Determine which half is sorted
        if arr[left] <= arr[mid]:
            # Left half is sorted
            if arr[left] <= target < arr[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            # Right half is sorted
            if arr[mid] < target <= arr[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1


def find_peak_element(arr: List[int]) -> int:
    """
    Find peak element (element greater than neighbors)
    
    Visualization:
    arr = [1, 2, 3, 1]
    
    Peak is element where arr[i] > arr[i-1] and arr[i] > arr[i+1]
    Index 2 (value 3) is the peak
    
    Step 1:
    L=0, R=3, M=1
    arr[M] = 2
    arr[M+1] = 3
    2 < 3 → Peak is on RIGHT
    
    Step 2:
    L=2, R=3, M=2
    arr[M] = 3
    arr[M+1] = 1
    3 > 1 → This is peak! ✅
    
    Args:
        arr: Array of integers
    
    Returns:
        Index of any peak element
    """
    left, right = 0, len(arr) - 1
    
    while left < right:
        mid = left + (right - left) // 2
        
        if arr[mid] > arr[mid + 1]:
            # Peak is on left (including mid)
            right = mid
        else:
            # Peak is on right
            left = mid + 1
    
    return left


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: STANDARD BINARY SEARCH")
    print("=" * 70)
    
    arr = [1, 3, 5, 7, 9, 11, 13, 15]
    target = 7
    
    print(f"\nArray: {arr}")
    print(f"Target: {target}")
    
    print("\nBinary Search Process:\n")
    
    # Simulate step by step
    left, right = 0, len(arr) - 1
    step = 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        print(f"Step {step}:")
        print(f"  Range: [{left}, {right}]")
        print(f"  Middle index: {mid}")
        print(f"  Middle value: {arr[mid]}")
        
        if arr[mid] == target:
            print(f"  {arr[mid]} == {target} → FOUND at index {mid}! ✅")
            break
        elif arr[mid] < target:
            print(f"  {arr[mid]} < {target} → Search RIGHT half")
            left = mid + 1
        else:
            print(f"  {arr[mid]} > {target} → Search LEFT half")
            right = mid - 1
        
        print()
        step += 1
    
    result = binary_search(arr, target)
    print(f"\nResult: Found at index {result}")
    
    print("\n" + "=" * 70)
    print("CASE 2: SEARCH IN ROTATED SORTED ARRAY")
    print("=" * 70)
    
    arr = [4, 5, 6, 7, 0, 1, 2]
    target = 0
    
    print(f"\nRotated Array: {arr}")
    print(f"Target: {target}")
    
    print("\nVisualization:")
    print("Original: [0, 1, 2, 3, 4, 5, 6, 7]")
    print("Rotated:  [4, 5, 6, 7, | 0, 1, 2]")
    print("                      ↑ pivot point")
    
    print("\nSearch Process:\n")
    
    left, right = 0, len(arr) - 1
    step = 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        print(f"Step {step}:")
        print(f"  Range: [{left}, {right}]")
        print(f"  Middle index: {mid}, value: {arr[mid]}")
        
        if arr[mid] == target:
            print(f"  Found target {target} at index {mid}! ✅")
            break
        
        if arr[left] <= arr[mid]:
            print(f"  Left half [{left}:{mid}] is sorted")
            if arr[left] <= target < arr[mid]:
                print(f"  Target {target} in sorted left half")
                right = mid - 1
            else:
                print(f"  Target {target} in right half")
                left = mid + 1
        else:
            print(f"  Right half [{mid}:{right}] is sorted")
            if arr[mid] < target <= arr[right]:
                print(f"  Target {target} in sorted right half")
                left = mid + 1
            else:
                print(f"  Target {target} in left half")
                right = mid - 1
        
        print()
        step += 1
    
    result = search_rotated_array(arr, target)
    print(f"Result: Found at index {result}")
    
    print("\n" + "=" * 70)
    print("CASE 3: FIND PEAK ELEMENT")
    print("=" * 70)
    
    arr = [1, 2, 3, 1]
    
    print(f"\nArray: {arr}")
    print("Peak = element greater than both neighbors")
    
    print("\nVisualization:")
    print("    3")
    print("   / \\")
    print("  2   1")
    print(" /")
    print("1")
    print("\nIndex 2 (value 3) is the peak")
    
    result = find_peak_element(arr)
    print(f"\nPeak found at index {result} with value {arr[result]}")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: AMAZON WAREHOUSE")
    print("=" * 70)
    
    print("""
Scenario: Finding product in warehouse with 1,000,000 items

Linear Search:
- Check item 1, 2, 3, ..., until found
- Average: 500,000 comparisons
- Worst: 1,000,000 comparisons
- Time: ~5 minutes (assuming 100 items/sec)

Binary Search:
- Divide and conquer
- Maximum: log₂(1,000,000) = 20 comparisons
- Time: ~0.2 seconds
- 1,500x FASTER! 🚀

Real applications:
📦 Warehouse inventory lookup
📚 Library book search
🗂️  Database indexing
🔍 Dictionary/phonebook lookup
🎮 Game leaderboard ranking
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Modified Binary Search Pattern:

1. 🔑 Requirements:
   - Array must be SORTED (or have sortedness property)
   - Need O(log n) time complexity
   
2. 🎯 Core Algorithm:
   while left <= right:
       mid = left + (right - left) // 2
       if condition_met:
           return mid
       elif search_left:
           right = mid - 1
       else:
           left = mid + 1

3. 🔧 Modifications:
   - Rotated array: Check which half is sorted
   - Peak finding: Compare with neighbors
   - First/last occurrence: Continue after finding
   - Search range: Find boundaries

4. ⚡ Complexity:
   Time: O(log n) - Eliminate half each step
   Space: O(1) - Only use pointers

When to use?
✅ Sorted array (or rotated/modified sorted)
✅ "Search", "Find", "First", "Last"
✅ Need better than O(n) time
✅ Can eliminate half of search space
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: STANDARD BINARY SEARCH
======================================================================

Array: [1, 3, 5, 7, 9, 11, 13, 15]
Target: 7

Binary Search Process:

Step 1:
  Range: [0, 7]
  Middle index: 3
  Middle value: 7
  7 == 7 → FOUND at index 3! ✅

Result: Found at index 3

======================================================================
CASE 2: SEARCH IN ROTATED SORTED ARRAY
======================================================================

Rotated Array: [4, 5, 6, 7, 0, 1, 2]
Target: 0

Visualization:
Original: [0, 1, 2, 3, 4, 5, 6, 7]
Rotated:  [4, 5, 6, 7, | 0, 1, 2]
                      ↑ pivot point

Search Process:

Step 1:
  Range: [0, 6]
  Middle index: 3, value: 7
  Left half [0:3] is sorted
  Target 0 in right half

Step 2:
  Range: [4, 6]
  Middle index: 5, value: 1
  Left half [4:5] is sorted
  Target 0 in sorted left half

Step 3:
  Range: [4, 4]
  Middle index: 4, value: 0
  Found target 0 at index 4! ✅

Result: Found at index 4

======================================================================
CASE 3: FIND PEAK ELEMENT
======================================================================

Array: [1, 2, 3, 1]
Peak = element greater than both neighbors

Visualization:
    3
   / \
  2   1
 /
1

Index 2 (value 3) is the peak

Peak found at index 2 with value 3

======================================================================
REAL WORLD ANALOGY: AMAZON WAREHOUSE
======================================================================

Scenario: Finding product in warehouse with 1,000,000 items

Linear Search:
- Check item 1, 2, 3, ..., until found
- Average: 500,000 comparisons
- Worst: 1,000,000 comparisons
- Time: ~5 minutes (assuming 100 items/sec)

Binary Search:
- Divide and conquer
- Maximum: log₂(1,000,000) = 20 comparisons
- Time: ~0.2 seconds
- 1,500x FASTER! 🚀

Real applications:
📦 Warehouse inventory lookup
📚 Library book search
🗂️  Database indexing
🔍 Dictionary/phonebook lookup
🎮 Game leaderboard ranking

======================================================================
KEY INSIGHTS
======================================================================

Modified Binary Search Pattern:

1. 🔑 Requirements:
   - Array must be SORTED (or have sortedness property)
   - Need O(log n) time complexity
   
2. 🎯 Core Algorithm:
   while left <= right:
       mid = left + (right - left) // 2
       if condition_met:
           return mid
       elif search_left:
           right = mid - 1
       else:
           left = mid + 1

3. 🔧 Modifications:
   - Rotated array: Check which half is sorted
   - Peak finding: Compare with neighbors
   - First/last occurrence: Continue after finding
   - Search range: Find boundaries

4. ⚡ Complexity:
   Time: O(log n) - Eliminate half each step
   Space: O(1) - Only use pointers

When to use?
✅ Sorted array (or rotated/modified sorted)
✅ "Search", "Find", "First", "Last"
✅ Need better than O(n) time
✅ Can eliminate half of search space
```

## 🎯 When to Use This Pattern?

- ✅ Array is **sorted** or has sorted property
- ✅ Keywords: "search", "find first", "find last", "peak"
- ✅ Need **O(log n)** time complexity
- ✅ Can **eliminate half** of search space each step

## ⏱️ Time & Space Complexity

- **Time**: O(log n) - Eliminate half each iteration
- **Space**: O(1) - Only use constant pointers
- **Advantage**: Exponentially faster than linear O(n) for large datasets

## 🔗 LeetCode Problems

1. [Binary Search (LeetCode #704)](https://leetcode.com/problems/binary-search/)
2. [Search in Rotated Sorted Array (LeetCode #33)](https://leetcode.com/problems/search-in-rotated-sorted-array/)
3. [Find Peak Element (LeetCode #162)](https://leetcode.com/problems/find-peak-element/)
4. [Find First and Last Position (LeetCode #34)](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
5. [Search a 2D Matrix (LeetCode #74)](https://leetcode.com/problems/search-a-2d-matrix/)

---

**Status**: ✅ REVIEWED & APPROVED
