# Pattern 11: Dynamic Programming (1D)

## 🌍 Real World Case
Imagine you are **climbing stairs** and want to reach the top floor. You can climb **1 or 2 steps** at a time. How many different ways are there to reach the top? Without DP, you would recalculate the same subproblems many times. With **Dynamic Programming**, you store calculation results and reuse them - much more efficient!

Another example: **Netflix** recommending movies based on history (memoization), or **investments** choosing when to buy/sell stocks for maximum profit.

## 📊 Concept Visualization
```
Problem: Climb 5 stairs, can climb 1 or 2 steps
How many different ways?

Without DP (Recursion - many duplicate calculations):
                    f(5)
                  /      \
              f(4)        f(3)
            /    \       /    \
         f(3)   f(2)  f(2)   f(1)
        /  \    / \    / \     |
      f(2) f(1)...    ...     ...
      
Total calls: MANY duplicates! ❌

With DP (Memoization - store results):
f(0) = 1  (0 steps: 1 way - already at top)
f(1) = 1  (1 step: 1 way)
f(2) = 2  (2 steps: 2 ways - [1,1] or [2])
f(3) = f(2) + f(1) = 2 + 1 = 3
f(4) = f(3) + f(2) = 3 + 2 = 5
f(5) = f(4) + f(3) = 5 + 3 = 8

Only 6 calculations! ✅

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

## 💻 Python Code with Visualization

```python
from typing import List

def climb_stairs(n: int) -> int:
    """
    Count different ways to climb n stairs
    Can climb 1 or 2 steps at a time
    
    Visualization for n=5:
    
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
    House Robber: Rob houses to get maximum money
    Cannot rob 2 adjacent houses
    
    Visualization for nums=[2, 7, 9, 3, 1]:
    
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
    Kadane's Algorithm: Find subarray with maximum sum
    
    Visualization for nums=[-2, 1, -3, 4, -1, 2, 1, -5, 4]:
    
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
```

## 🎯 When to Use This Pattern?

- ✅ Problems with **overlapping subproblems**
- ✅ Keywords: "maximum/minimum", "count ways", "optimize"
- ✅ **Linear sequence** of decisions
- ✅ Can break problem into smaller similar subproblems

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Process each element once
- **Space**: O(n) for DP array, can optimize to O(1) if only need previous values
- **Advantage**: Convert exponential recursion to linear time!

## 🔗 LeetCode Problems

1. [Climbing Stairs (LeetCode #70)](https://leetcode.com/problems/climbing-stairs/)
2. [House Robber (LeetCode #198)](https://leetcode.com/problems/house-robber/)
3. [Maximum Subarray (LeetCode #53)](https://leetcode.com/problems/maximum-subarray/)
4. [Coin Change (LeetCode #322)](https://leetcode.com/problems/coin-change/)
5. [Longest Increasing Subsequence (LeetCode #300)](https://leetcode.com/problems/longest-increasing-subsequence/)

---

**Status**: ✅ REVIEWED & APPROVED