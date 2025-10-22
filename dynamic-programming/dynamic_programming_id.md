# Pattern 11: Dynamic Programming (1D)

## 🌍 Real World Case
Bayangkan Anda sedang **naik tangga** dan ingin mencapai lantai paling atas. Anda bisa naik **1 atau 2 anak tangga** sekaligus. Ada berapa cara berbeda untuk mencapai atas? Tanpa DP, Anda akan menghitung ulang sub-masalah yang sama berkali-kali. Dengan **Dynamic Programming**, Anda simpan hasil perhitungan dan gunakan kembali - jauh lebih efisien!

Contoh lain: **Netflix** merekomendasikan film berdasarkan history (memoization), atau **investasi** memilih kapan beli/jual saham untuk profit maksimal.

## 📊 Visualisasi Konsep
```
Problem: Naik tangga 5 anak tangga, bisa naik 1 atau 2 step
Berapa banyak cara berbeda?

Without DP (Recursion - banyak duplicate calculation):
                    f(5)
                  /      \
              f(4)        f(3)
            /    \       /    \
         f(3)   f(2)  f(2)   f(1)
        /  \    / \    / \     |
      f(2) f(1)...    ...     ...
      
Total calls: BANYAK duplicate! ❌

With DP (Memoization - simpan hasil):
f(0) = 1  (0 step: 1 cara - sudah di atas)
f(1) = 1  (1 step: 1 cara)
f(2) = 2  (2 step: 2 cara - [1,1] atau [2])
f(3) = f(2) + f(1) = 2 + 1 = 3
f(4) = f(3) + f(2) = 3 + 2 = 5
f(5) = f(4) + f(3) = 5 + 3 = 8

Hanya 6 calculations! ✅

Pattern: f(n) = f(n-1) + f(n-2)

Visualization:
Step 0: [] → 1 way
Step 1: [1] → 1 way
Step 2: [1,1], [2] → 2 ways
Step 3: [1,1,1], [1,2], [2,1] → 3 ways
Step 4: [1,1,1,1], [1,1,2], [1,2,1], [2,1,1], [2,2] → 5 ways
Step 5: 8 ways

Key Insight:
- Overlapping subproblems → DP!
- Store results to avoid recalculation
- Build solution bottom-up or top-down
```

## 💻 Code Python dengan Visualisasi

```python
from typing import List

def climb_stairs(n: int) -> int:
    """
    Hitung berapa cara berbeda naik n anak tangga
    Bisa naik 1 atau 2 step sekaligus
    
    Visualisasi untuk n=5:
    
    f(n) = f(n-1) + f(n-2)
    
    Step 0: dp[0] = 1
    Step 1: dp[1] = 1
    Step 2: dp[2] = dp[1] + dp[0] = 1 + 1 = 2
    Step 3: dp[3] = dp[2] + dp[1] = 2 + 1 = 3
    Step 4: dp[4] = dp[3] + dp[2] = 3 + 2 = 5
    Step 5: dp[5] = dp[4] + dp[3] = 5 + 3 = 8
    
    Table:
    i    | 0 | 1 | 2 | 3 | 4 | 5 |
    dp[i]| 1 | 1 | 2 | 3 | 5 | 8 |
    
    Why this works?
    - To reach step n, you can come from:
      * Step n-1 (then climb 1 step)
      * Step n-2 (then climb 2 steps)
    - Total ways = ways(n-1) + ways(n-2)
    
    Args:
        n: Number of stairs
    
    Returns:
        Number of distinct ways to climb
    """
    if n <= 2:
        return n
    
    # dp[i] = ways to reach step i
    dp = [0] * (n + 1)
    dp[0] = 1  # Base case: 1 way to stay at ground
    dp[1] = 1  # Base case: 1 way to reach step 1
    
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    
    return dp[n]


def rob_houses(nums: List[int]) -> int:
    """
    House Robber: Rob houses untuk dapat uang maksimal
    Tidak boleh rob 2 rumah bersebelahan
    
    Visualisasi untuk nums=[2, 7, 9, 3, 1]:
    
    Houses: [2, 7, 9, 3, 1]
    Index:   0  1  2  3  4
    
    Decision at each house:
    - Rob: Take money + max from i-2
    - Skip: Take max from i-1
    
    dp[i] = max(nums[i] + dp[i-2], dp[i-1])
    
    Step 0: dp[0] = 2 (rob house 0)
    Step 1: dp[1] = max(7, 2) = 7 (rob house 1, skip 0)
    Step 2: dp[2] = max(9+2, 7) = 11 (rob house 0 and 2)
    Step 3: dp[3] = max(3+7, 11) = 11 (skip house 3)
    Step 4: dp[4] = max(1+11, 11) = 12 (rob house 0,2,4)
    
    Table:
    i    | 0 | 1 | 2  | 3  | 4  |
    nums | 2 | 7 | 9  | 3  | 1  |
    dp   | 2 | 7 | 11 | 11 | 12 |
    
    Result: 12 (rob houses 0, 2, 4)
    
    Args:
        nums: Money in each house
    
    Returns:
        Maximum money can rob
    """
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    
    n = len(nums)
    dp = [0] * n
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    
    for i in range(2, n):
        # Rob current house + max from i-2
        rob = nums[i] + dp[i-2]
        # Skip current house, take max from i-1
        skip = dp[i-1]
        dp[i] = max(rob, skip)
    
    return dp[n-1]


def max_subarray_sum(nums: List[int]) -> int:
    """
    Kadane's Algorithm: Cari subarray dengan sum maksimal
    
    Visualisasi untuk nums=[-2, 1, -3, 4, -1, 2, 1, -5, 4]:
    
    Idea: At each position, decide:
    - Continue current subarray
    - Start new subarray from current element
    
    max_ending_here = max(nums[i], max_ending_here + nums[i])
    
    i=0: nums=-2
      max_ending_here = -2
      max_so_far = -2
      
    i=1: nums=1
      max_ending_here = max(1, -2+1) = 1
      max_so_far = 1
      
    i=2: nums=-3
      max_ending_here = max(-3, 1-3) = -2
      max_so_far = 1
      
    i=3: nums=4
      max_ending_here = max(4, -2+4) = 4
      max_so_far = 4
      
    i=4: nums=-1
      max_ending_here = max(-1, 4-1) = 3
      max_so_far = 4
      
    i=5: nums=2
      max_ending_here = max(2, 3+2) = 5
      max_so_far = 5
      
    i=6: nums=1
      max_ending_here = max(1, 5+1) = 6
      max_so_far = 6
      
    i=7: nums=-5
      max_ending_here = max(-5, 6-5) = 1
      max_so_far = 6
      
    i=8: nums=4
      max_ending_here = max(4, 1+4) = 5
      max_so_far = 6
    
    Best subarray: [4, -1, 2, 1] with sum = 6
    
    Args:
        nums: Array of integers
    
    Returns:
        Maximum subarray sum
    """
    if not nums:
        return 0
    
    max_ending_here = nums[0]
    max_so_far = nums[0]
    
    for i in range(1, len(nums)):
        # Either extend current subarray or start new
        max_ending_here = max(nums[i], max_ending_here + nums[i])
        max_so_far = max(max_so_far, max_ending_here)
    
    return max_so_far


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: CLIMBING STAIRS")
    print("=" * 70)
    
    n = 5
    
    print(f"\nNumber of stairs: {n}")
    print("Can climb 1 or 2 steps at a time")
    
    print("\nDP Table building:")
    print("i    | 0 | 1 | 2 | 3 | 4 | 5 |")
    print("-----|---|---|---|---|---|---|")
    
    # Simulate
    if n >= 0:
        dp = [0] * (n + 1)
        dp[0] = 1
        if n >= 1:
            dp[1] = 1
        
        values = [str(dp[i]) if i <= 1 else " " for i in range(n + 1)]
        print(f"dp[i]| {' | '.join(values)} |")
        
        for i in range(2, n + 1):
            dp[i] = dp[i-1] + dp[i-2]
            values = [str(dp[j]) for j in range(n + 1)]
            print(f"dp[i]| {' | '.join(values)} |")
            print(f"      dp[{i}] = dp[{i-1}] + dp[{i-2}] = {dp[i-1]} + {dp[i-2]} = {dp[i]}")
    
    result = climb_stairs(n)
    print(f"\nTotal ways to climb {n} stairs: {result}")
    
    print("\n" + "=" * 70)
    print("CASE 2: HOUSE ROBBER")
    print("=" * 70)
    
    houses = [2, 7, 9, 3, 1]
    
    print(f"\nMoney in houses: {houses}")
    print("Rule: Cannot rob adjacent houses")
    
    print("\nDP process:")
    n = len(houses)
    dp = [0] * n
    dp[0] = houses[0]
    print(f"dp[0] = {houses[0]} (rob house 0)")
    
    dp[1] = max(houses[0], houses[1])
    print(f"dp[1] = max({houses[0]}, {houses[1]}) = {dp[1]}")
    
    for i in range(2, n):
        rob = houses[i] + dp[i-2]
        skip = dp[i-1]
        dp[i] = max(rob, skip)
        print(f"dp[{i}] = max(rob={houses[i]}+{dp[i-2]}={rob}, skip={skip}) = {dp[i]}")
    
    result = rob_houses(houses)
    print(f"\nMaximum money: ${result}")
    print(f"Strategy: Rob houses at indices that give maximum")
    
    print("\n" + "=" * 70)
    print("CASE 3: MAXIMUM SUBARRAY (KADANE'S ALGORITHM)")
    print("=" * 70)
    
    nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
    
    print(f"\nArray: {nums}")
    
    print("\nKadane's Algorithm process:")
    print("i | nums | max_ending_here | max_so_far")
    print("--|------|-----------------|------------")
    
    max_ending_here = nums[0]
    max_so_far = nums[0]
    print(f"0 | {nums[0]:4} | {max_ending_here:15} | {max_so_far}")
    
    for i in range(1, len(nums)):
        max_ending_here = max(nums[i], max_ending_here + nums[i])
        max_so_far = max(max_so_far, max_ending_here)
        print(f"{i} | {nums[i]:4} | {max_ending_here:15} | {max_so_far}")
    
    result = max_subarray_sum(nums)
    print(f"\nMaximum subarray sum: {result}")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: INVESTMENT STRATEGY")
    print("=" * 70)
    
    print("""
Scenario: Stock trading - buy low, sell high

Stock prices: [7, 1, 5, 3, 6, 4]

DP Approach (similar to max subarray):
- Track minimum price seen so far
- Calculate max profit at each day

Day 0: price=7, min=7, profit=0
Day 1: price=1, min=1, profit=0
Day 2: price=5, min=1, profit=5-1=4 ✅
Day 3: price=3, min=1, profit=4
Day 4: price=6, min=1, profit=6-1=5 ✅
Day 5: price=4, min=1, profit=5

Best strategy: Buy at day 1 ($1), sell at day 4 ($6)
Max profit: $5

Other DP applications:
💰 Investment portfolio optimization
🎮 Game scoring strategies
📊 Resource allocation
🏃 Fitness training schedules
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
1D Dynamic Programming Pattern:

1. 🎯 Identify DP problem:
   - Overlapping subproblems
   - Optimal substructure
   - Can break into smaller subproblems

2. 🔑 DP approach:
   - Define state: dp[i] = ?
   - Find recurrence relation: dp[i] = f(dp[i-1], dp[i-2], ...)
   - Base cases: dp[0], dp[1], ...
   - Build table bottom-up

3. 📝 Template:
   dp = [0] * n
   dp[0] = base_case
   
   for i in range(1, n):
       dp[i] = some_function(dp[i-1], dp[i-2], ...)
   
   return dp[n-1]

4. ⚡ Optimization:
   - Space: Often can reduce to O(1) if only need prev values
   - Example: Fibonacci only needs last 2 values

Time Complexity: O(n)
Space Complexity: O(n) or O(1) with optimization

Kapan digunakan?
✅ Overlapping subproblems
✅ "Maximum/minimum", "count ways"
✅ Optimization problems
✅ Linear sequence decisions
    """)
```

## 📤 Output Ketika Dijalankan

```
======================================================================
CASE 1: CLIMBING STAIRS
======================================================================

Number of stairs: 5
Can climb 1 or 2 steps at a time

DP Table building:
i    | 0 | 1 | 2 | 3 | 4 | 5 |
-----|---|---|---|---|---|---|
dp[i]| 1 | 1 |   |   |   |   |
dp[i]| 1 | 1 | 2 |   |   |   |
      dp[2] = dp[1] + dp[0] = 1 + 1 = 2
dp[i]| 1 | 1 | 2 | 3 |   |   |
      dp[3] = dp[2] + dp[1] = 2 + 1 = 3
dp[i]| 1 | 1 | 2 | 3 | 5 |   |
      dp[4] = dp[3] + dp[2] = 3 + 2 = 5
dp[i]| 1 | 1 | 2 | 3 | 5 | 8 |
      dp[5] = dp[4] + dp[3] = 5 + 3 = 8

Total ways to climb 5 stairs: 8

======================================================================
CASE 2: HOUSE ROBBER
======================================================================

Money in houses: [2, 7, 9, 3, 1]
Rule: Cannot rob adjacent houses

DP process:
dp[0] = 2 (rob house 0)
dp[1] = max(2, 7) = 7
dp[2] = max(rob=9+2=11, skip=7) = 11
dp[3] = max(rob=3+7=10, skip=11) = 11
dp[4] = max(rob=1+11=12, skip=11) = 12

Maximum money: $12
Strategy: Rob houses at indices that give maximum

======================================================================
CASE 3: MAXIMUM SUBARRAY (KADANE'S ALGORITHM)
======================================================================

Array: [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Kadane's Algorithm process:
i | nums | max_ending_here | max_so_far
--|------|-----------------|------------
0 |   -2 |              -2 | -2
1 |    1 |               1 | 1
2 |   -3 |              -2 | 1
3 |    4 |               4 | 4
4 |   -1 |               3 | 4
5 |    2 |               5 | 5
6 |    1 |               6 | 6
7 |   -5 |               1 | 6
8 |    4 |               5 | 6

Maximum subarray sum: 6

======================================================================
REAL WORLD ANALOGY: INVESTMENT STRATEGY
======================================================================

Scenario: Stock trading - buy low, sell high

Stock prices: [7, 1, 5, 3, 6, 4]

DP Approach (similar to max subarray):
- Track minimum price seen so far
- Calculate max profit at each day

Day 0: price=7, min=7, profit=0
Day 1: price=1, min=1, profit=0
Day 2: price=5, min=1, profit=5-1=4 ✅
Day 3: price=3, min=1, profit=4
Day 4: price=6, min=1, profit=6-1=5 ✅
Day 5: price=4, min=1, profit=5

Best strategy: Buy at day 1 ($1), sell at day 4 ($6)
Max profit: $5

Other DP applications:
💰 Investment portfolio optimization
🎮 Game scoring strategies
📊 Resource allocation
🏃 Fitness training schedules

======================================================================
KEY INSIGHTS
======================================================================

1D Dynamic Programming Pattern:

1. 🎯 Identify DP problem:
   - Overlapping subproblems
   - Optimal substructure
   - Can break into smaller subproblems

2. 🔑 DP approach:
   - Define state: dp[i] = ?
   - Find recurrence relation: dp[i] = f(dp[i-1], dp[i-2], ...)
   - Base cases: dp[0], dp[1], ...
   - Build table bottom-up

3. 📝 Template:
   dp = [0] * n
   dp[0] = base_case
   
   for i in range(1, n):
       dp[i] = some_function(dp[i-1], dp[i-2], ...)
   
   return dp[n-1]

4. ⚡ Optimization:
   - Space: Often can reduce to O(1) if only need prev values
   - Example: Fibonacci only needs last 2 values

Time Complexity: O(n)
Space Complexity: O(n) or O(1) with optimization

Kapan digunakan?
✅ Overlapping subproblems
✅ "Maximum/minimum", "count ways"
✅ Optimization problems
✅ Linear sequence decisions
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Problem dengan **overlapping subproblems**
- ✅ Kata kunci: "maximum/minimum", "count ways", "optimize"
- ✅ **Linear sequence** of decisions
- ✅ Can break problem into smaller similar subproblems

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Process each element once
- **Space**: O(n) for DP array, can optimize to O(1) if only need previous values
- **Keunggulan**: Convert exponential recursion to linear time!

## 🔗 LeetCode Problems

1. [Climbing Stairs (LeetCode #70)](https://leetcode.com/problems/climbing-stairs/)
2. [House Robber (LeetCode #198)](https://leetcode.com/problems/house-robber/)
3. [Maximum Subarray (LeetCode #53)](https://leetcode.com/problems/maximum-subarray/)
4. [Coin Change (LeetCode #322)](https://leetcode.com/problems/coin-change/)
5. [Longest Increasing Subsequence (LeetCode #300)](https://leetcode.com/problems/longest-increasing-subsequence/)

---

**Status**: ✅ REVIEWED & APPROVED
