# Pattern 1: Prefix Sum

## 🌍 Real World Case
Imagine you work at an e-commerce company and want to calculate **total sales within a specific period** (for example, sales from day 5 to day 10 in a month). Without prefix sum, you'd have to manually sum each day. With prefix sum, you can instantly calculate using the formula `P[j] - P[i-1]`.

## 📊 Concept Visualization
```
Daily Sales:          [100, 200, 150, 300, 250, 400]
                       Day0 Day1 Day2 Day3 Day4 Day5

Prefix Sum Array:     [100, 300, 450, 750, 1000, 1400]
                       ↑    ↑    ↑    ↑     ↑     ↑
                      Sum  Sum  Sum  Sum   Sum   Sum
                      0-0  0-1  0-2  0-3   0-4   0-5

Query: Total sales from Day 2 to Day 4?
Answer: P[4] - P[1] = 1000 - 300 = 700 ✅
       (150 + 300 + 250 = 700)
```

## 💻 Python Code with Visualization

```python
from typing import List

def build_prefix_sum(sales: List[int]) -> List[int]:
    """
    Build prefix sum array from daily sales
    
    Process visualization:
    sales  = [100, 200, 150, 300, 250, 400]
    
    Step 0: prefix[0] = sales[0] = 100
    prefix = [100, ?, ?, ?, ?, ?]
    
    Step 1: prefix[1] = prefix[0] + sales[1] = 100 + 200 = 300
    prefix = [100, 300, ?, ?, ?, ?]
    
    Step 2: prefix[2] = prefix[1] + sales[2] = 300 + 150 = 450
    prefix = [100, 300, 450, ?, ?, ?]
    
    ... and so on
    """
    prefix = [0] * len(sales)
    prefix[0] = sales[0]
    
    for i in range(1, len(sales)):
        prefix[i] = prefix[i-1] + sales[i]
    
    return prefix


def range_sum(prefix: List[int], start: int, end: int) -> int:
    """
    Calculate total sales from start to end index
    
    Visualization:
    
    Case 1 - Starting from 0:
    -------------------------
    range_sum(prefix, 0, 3)
    
    prefix = [100, 300, 450, 750, ...]
              ↑              ↑
            start          end
    
    Return: prefix[3] = 750
    
    
    Case 2 - Starting from middle:
    ------------------------------
    range_sum(prefix, 2, 4)
    
    sales  = [100, 200, 150, 300, 250, 400]
                        ↑    ↑    ↑
                      start      end
    
    prefix = [100, 300, 450, 750, 1000, 1400]
                   ↑              ↑
                 start-1         end
    
    Formula: prefix[end] - prefix[start-1]
           = prefix[4]   - prefix[1]
           = 1000        - 300
           = 700 ✅
    
    This equals: 150 + 300 + 250 = 700
    """
    if start == 0:
        return prefix[end]
    return prefix[end] - prefix[start - 1]


# ==================== DEMO ====================
if __name__ == "__main__":
    # Daily sales data (in thousands)
    sales = [100, 200, 150, 300, 250, 400]
    
    print("=" * 50)
    print("DAILY SALES:")
    print("=" * 50)
    for i, sale in enumerate(sales):
        print(f"Day {i}: ${sale},000")
    
    # Build prefix sum
    prefix = build_prefix_sum(sales)
    
    print("\n" + "=" * 50)
    print("PREFIX SUM (Cumulative Total):")
    print("=" * 50)
    for i, total in enumerate(prefix):
        print(f"Day 0 - Day {i}: ${total},000")
    
    print("\n" + "=" * 50)
    print("QUERY EXAMPLES:")
    print("=" * 50)
    
    # Query 1: Total sales from beginning (Day 0) to Day 3
    result1 = range_sum(prefix, 0, 3)
    print(f"\n1. Total sales Day 0-3: ${result1},000")
    print(f"   Visualization: [100, 200, 150, 300]")
    print(f"   Manual check: 100 + 200 + 150 + 300 = {sum(sales[0:4])}")
    
    # Query 2: Total sales from Day 2 to Day 4
    result2 = range_sum(prefix, 2, 4)
    print(f"\n2. Total sales Day 2-4: ${result2},000")
    print(f"   Visualization: [150, 300, 250]")
    print(f"   Manual check: 150 + 300 + 250 = {sum(sales[2:5])}")
    print(f"   Calculation: prefix[4] - prefix[1] = {prefix[4]} - {prefix[1]} = {result2}")
    
    # Query 3: Total of all sales
    result3 = range_sum(prefix, 0, 5)
    print(f"\n3. Total all sales: ${result3},000")
    print(f"   Visualization: [100, 200, 150, 300, 250, 400]")
    print(f"   Manual check: {' + '.join(map(str, sales))} = {sum(sales)}")
```

## 📤 Output When Executed

```
==================================================
DAILY SALES:
==================================================
Day 0: $100,000
Day 1: $200,000
Day 2: $150,000
Day 3: $300,000
Day 4: $250,000
Day 5: $400,000

==================================================
PREFIX SUM (Cumulative Total):
==================================================
Day 0 - Day 0: $100,000
Day 0 - Day 1: $300,000
Day 0 - Day 2: $450,000
Day 0 - Day 3: $750,000
Day 0 - Day 4: $1000,000
Day 0 - Day 5: $1400,000

==================================================
QUERY EXAMPLES:
==================================================

1. Total sales Day 0-3: $750,000
   Visualization: [100, 200, 150, 300]
   Manual check: 100 + 200 + 150 + 300 = 750

2. Total sales Day 2-4: $700,000
   Visualization: [150, 300, 250]
   Manual check: 150 + 300 + 250 = 700
   Calculation: prefix[4] - prefix[1] = 1000 - 300 = 700

3. Total all sales: $1400,000
   Visualization: [100, 200, 150, 300, 250, 400]
   Manual check: 100 + 200 + 150 + 300 + 250 + 400 = 1400
```

## 🎯 When to Use This Pattern?

- ✅ When you need to calculate sum of subarrays multiple times
- ✅ When there are multiple queries for range sum
- ✅ To optimize from O(n) per query to O(1) per query
- ✅ Problems involving cumulative sum or running total

## ⏱️ Time & Space Complexity

- **Build Prefix Sum**: O(n)
- **Range Query**: O(1)
- **Space**: O(n)

## 🔗 LeetCode Problems

1. [Range Sum Query - Immutable (LeetCode #303)](https://leetcode.com/problems/range-sum-query-immutable/)
2. [Find Pivot Index (LeetCode #724)](https://leetcode.com/problems/find-pivot-index/)
3. [Subarray Sum Equals K (LeetCode #560)](https://leetcode.com/problems/subarray-sum-equals-k/)
4. [Contiguous Array (LeetCode #525)](https://leetcode.com/problems/contiguous-array/)

---

**Status**: ✅ REVIEWED & APPROVED
