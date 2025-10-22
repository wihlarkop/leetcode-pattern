# Pattern 6: Monotonic Stack

## 🌍 Real World Case
Imagine you work at a **stock exchange** and want to know: "When will the next stock price be higher than today?" For each trading day, you need to find the next day where the price is higher. With Monotonic Stack, you can do this in **O(n) time** instead of O(n²)!

Another example: **Website analytics** - finding "next page view with longer duration" for each session.

## 📊 Concept Visualization
```
Stock Prices: [73, 74, 75, 71, 69, 72, 76, 73]
Goal: For each day, find how many days until a higher price?

Day 0 (73): Next higher is Day 1 (74) → wait 1 day
Day 1 (74): Next higher is Day 2 (75) → wait 1 day  
Day 2 (75): Next higher is Day 6 (76) → wait 4 days
Day 3 (71): Next higher is Day 5 (72) → wait 2 days
Day 4 (69): Next higher is Day 5 (72) → wait 1 day
Day 5 (72): Next higher is Day 6 (76) → wait 1 day
Day 6 (76): No higher price → 0 days
Day 7 (73): No higher price → 0 days

Result: [1, 1, 4, 2, 1, 1, 0, 0]

Monotonic Stack Process:
Stack stores INDICES of elements waiting for next greater element

Prices: [73, 74, 75, 71, 69, 72, 76, 73]
Index:   0   1   2   3   4   5   6   7

i=0: Stack=[], push 0 → Stack=[0]
i=1: 74>73, pop 0, answer[0]=1, push 1 → Stack=[1]
i=2: 75>74, pop 1, answer[1]=1, push 2 → Stack=[2]
i=3: 71<75, push 3 → Stack=[2,3]
i=4: 69<71, push 4 → Stack=[2,3,4]
i=5: 72>69, pop 4, answer[4]=1
     72>71, pop 3, answer[3]=2
     72<75, push 5 → Stack=[2,5]
i=6: 76>72, pop 5, answer[5]=1
     76>75, pop 2, answer[2]=4
     push 6 → Stack=[6]
i=7: 73<76, push 7 → Stack=[6,7]

Done! Remaining in stack get 0.
```

## 💻 Python Code with Visualization

```python
from typing import List

def daily_temperatures(temperatures: List[int]) -> List[int]:
    """
    For each day, calculate how many days until a warmer temperature
    
    Process visualization:
    temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
    
    Monotonic Stack (decreasing):
    Stack stores indices of temperatures waiting
    
    i=0, temp=73:
      Stack empty, push index 0
      Stack: [0] (contains index with temp 73)
      
    i=1, temp=74:
      74 > 73 (temperatures[0])
      Pop 0, set answer[0] = 1 - 0 = 1
      Push 1
      Stack: [1]
      
    i=2, temp=75:
      75 > 74 (temperatures[1])
      Pop 1, set answer[1] = 2 - 1 = 1
      Push 2
      Stack: [2]
      
    i=3, temp=71:
      71 < 75, just push 3
      Stack: [2, 3]
      
    i=4, temp=69:
      69 < 71, just push 4
      Stack: [2, 3, 4]
      
    i=5, temp=72:
      72 > 69 (temperatures[4])
      Pop 4, set answer[4] = 5 - 4 = 1
      72 > 71 (temperatures[3])
      Pop 3, set answer[3] = 5 - 3 = 2
      72 < 75, push 5
      Stack: [2, 5]
      
    i=6, temp=76:
      76 > 72 (temperatures[5])
      Pop 5, set answer[5] = 6 - 5 = 1
      76 > 75 (temperatures[2])
      Pop 2, set answer[2] = 6 - 2 = 4
      Push 6
      Stack: [6]
      
    i=7, temp=73:
      73 < 76, just push 7
      Stack: [6, 7]
    
    Stack still has items? They don't have next greater → answer = 0
    
    Args:
        temperatures: List of daily temperatures
    
    Returns:
        List of days to wait for warmer temperature
    """
    n = len(temperatures)
    answer = [0] * n
    stack = []  # Stack stores INDICES
    
    for i in range(n):
        # While current temp is greater than temp at top of stack
        while stack and temperatures[i] > temperatures[stack[-1]]:
            prev_index = stack.pop()
            answer[prev_index] = i - prev_index
        
        stack.append(i)
    
    return answer


def next_greater_element(nums: List[int]) -> List[int]:
    """
    For each element, find next greater element to the right
    Return -1 if none exists
    
    Visualization:
    nums = [2, 1, 2, 4, 3]
    
    i=0 (2): Next greater is 4 at index 3
    i=1 (1): Next greater is 2 at index 2
    i=2 (2): Next greater is 4 at index 3
    i=3 (4): No greater element → -1
    i=4 (3): No greater element → -1
    
    Result: [4, 2, 4, -1, -1]
    
    Args:
        nums: Input array
    
    Returns:
        Array with next greater element for each position
    """
    n = len(nums)
    result = [-1] * n
    stack = []
    
    for i in range(n):
        while stack and nums[i] > nums[stack[-1]]:
            prev_index = stack.pop()
            result[prev_index] = nums[i]
        
        stack.append(i)
    
    return result


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: DAILY TEMPERATURES (STOCK PRICES)")
    print("=" * 70)
    
    temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
    
    print(f"\nTemperatures: {temperatures}")
    print(f"Index:        {list(range(len(temperatures)))}")
    
    print("\nMonotonic Stack Process Step-by-Step:\n")
    
    # Simulate step by step
    n = len(temperatures)
    answer = [0] * n
    stack = []
    
    for i in range(n):
        print(f"Day {i}: Temperature = {temperatures[i]}")
        print(f"  Stack before: {stack}")
        
        popped = []
        while stack and temperatures[i] > temperatures[stack[-1]]:
            prev_index = stack.pop()
            answer[prev_index] = i - prev_index
            popped.append(prev_index)
            print(f"  ✓ Pop index {prev_index} (temp {temperatures[prev_index]})")
            print(f"    → Answer[{prev_index}] = {i} - {prev_index} = {answer[prev_index]} days")
        
        if not popped:
            print(f"  No element to pop (current temp not greater)")
        
        stack.append(i)
        print(f"  Push index {i}")
        print(f"  Stack after: {stack}")
        print()
    
    print(f"Final Answer: {answer}")
    print("\nInterpretation:")
    for i, days in enumerate(answer):
        if days > 0:
            print(f"  Day {i} ({temperatures[i]}°): Wait {days} day(s) for {temperatures[i+days]}°")
        else:
            print(f"  Day {i} ({temperatures[i]}°): No warmer day ahead")
    
    print("\n" + "=" * 70)
    print("CASE 2: NEXT GREATER ELEMENT")
    print("=" * 70)
    
    nums = [2, 1, 2, 4, 3]
    result = next_greater_element(nums)
    
    print(f"\nArray: {nums}")
    print(f"Result: {result}")
    
    print("\nInterpretation:")
    for i in range(len(nums)):
        if result[i] != -1:
            print(f"  nums[{i}] = {nums[i]} → Next greater = {result[i]}")
        else:
            print(f"  nums[{i}] = {nums[i]} → No greater element")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: STOCK TRADING")
    print("=" * 70)
    
    print("""
Scenario: Trader wants to know when stock price will rise
Prices: [100, 101, 102, 98, 97, 99, 105, 103]

Question: "How many days until price is higher than today?"

Day 0 ($100): Tomorrow $101 → 1 day ✅
Day 1 ($101): Tomorrow $102 → 1 day ✅
Day 2 ($102): Day 6 ($105) → 4 days ✅
Day 3 ($98):  Day 5 ($99) → 2 days ✅
Day 4 ($97):  Tomorrow $99 → 1 day ✅
Day 5 ($99):  Tomorrow $105 → 1 day ✅
Day 6 ($105): Never → 0 days ❌
Day 7 ($103): Never → 0 days ❌

Monotonic Stack solves this in O(n) time!
Without stack: O(n²) - for each day, scan all following days
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Monotonic Stack Pattern:
1. 📚 Stack stores INDICES (not values)
2. 📉 Maintain stack in specific order (increasing/decreasing)
3. 🔍 When element "breaks" monotonic property:
   → Pop elements and process them
   → Set their answer
4. ➕ Push current index to stack

When to use?
✅ Next Greater Element
✅ Next Smaller Element
✅ Largest Rectangle in Histogram
✅ Stock Span Problem

Time: O(n) - Each element pushed and popped max 1x
Space: O(n) - Stack size
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: DAILY TEMPERATURES (STOCK PRICES)
======================================================================

Temperatures: [73, 74, 75, 71, 69, 72, 76, 73]
Index:        [0, 1, 2, 3, 4, 5, 6, 7]

Monotonic Stack Process Step-by-Step:

Day 0: Temperature = 73
  Stack before: []
  No element to pop (current temp not greater)
  Push index 0
  Stack after: [0]

Day 1: Temperature = 74
  Stack before: [0]
  ✓ Pop index 0 (temp 73)
    → Answer[0] = 1 - 0 = 1 days
  Push index 1
  Stack after: [1]

Day 2: Temperature = 75
  Stack before: [1]
  ✓ Pop index 1 (temp 74)
    → Answer[1] = 2 - 1 = 1 days
  Push index 2
  Stack after: [2]

Day 3: Temperature = 71
  Stack before: [2]
  No element to pop (current temp not greater)
  Push index 3
  Stack after: [2, 3]

Day 4: Temperature = 69
  Stack before: [2, 3]
  No element to pop (current temp not greater)
  Push index 4
  Stack after: [2, 3, 4]

Day 5: Temperature = 72
  Stack before: [2, 3, 4]
  ✓ Pop index 4 (temp 69)
    → Answer[4] = 5 - 4 = 1 days
  ✓ Pop index 3 (temp 71)
    → Answer[3] = 5 - 3 = 2 days
  Push index 5
  Stack after: [2, 5]

Day 6: Temperature = 76
  Stack before: [2, 5]
  ✓ Pop index 5 (temp 72)
    → Answer[5] = 6 - 5 = 1 days
  ✓ Pop index 2 (temp 75)
    → Answer[2] = 6 - 2 = 4 days
  Push index 6
  Stack after: [6]

Day 7: Temperature = 73
  Stack before: [6]
  No element to pop (current temp not greater)
  Push index 7
  Stack after: [6, 7]

Final Answer: [1, 1, 4, 2, 1, 1, 0, 0]

Interpretation:
  Day 0 (73°): Wait 1 day(s) for 74°
  Day 1 (74°): Wait 1 day(s) for 75°
  Day 2 (75°): Wait 4 day(s) for 76°
  Day 3 (71°): Wait 2 day(s) for 72°
  Day 4 (69°): Wait 1 day(s) for 72°
  Day 5 (72°): Wait 1 day(s) for 76°
  Day 6 (76°): No warmer day ahead
  Day 7 (73°): No warmer day ahead

======================================================================
CASE 2: NEXT GREATER ELEMENT
======================================================================

Array: [2, 1, 2, 4, 3]
Result: [4, 2, 4, -1, -1]

Interpretation:
  nums[0] = 2 → Next greater = 4
  nums[1] = 1 → Next greater = 2
  nums[2] = 2 → Next greater = 4
  nums[3] = 4 → No greater element
  nums[4] = 3 → No greater element

======================================================================
REAL WORLD ANALOGY: STOCK TRADING
======================================================================

Scenario: Trader wants to know when stock price will rise
Prices: [100, 101, 102, 98, 97, 99, 105, 103]

Question: "How many days until price is higher than today?"

Day 0 ($100): Tomorrow $101 → 1 day ✅
Day 1 ($101): Tomorrow $102 → 1 day ✅
Day 2 ($102): Day 6 ($105) → 4 days ✅
Day 3 ($98):  Day 5 ($99) → 2 days ✅
Day 4 ($97):  Tomorrow $99 → 1 day ✅
Day 5 ($99):  Tomorrow $105 → 1 day ✅
Day 6 ($105): Never → 0 days ❌
Day 7 ($103): Never → 0 days ❌

Monotonic Stack solves this in O(n) time!
Without stack: O(n²) - for each day, scan all following days

======================================================================
KEY INSIGHTS
======================================================================

Monotonic Stack Pattern:
1. 📚 Stack stores INDICES (not values)
2. 📉 Maintain stack in specific order (increasing/decreasing)
3. 🔍 When element "breaks" monotonic property:
   → Pop elements and process them
   → Set their answer
4. ➕ Push current index to stack

When to use?
✅ Next Greater Element
✅ Next Smaller Element
✅ Largest Rectangle in Histogram
✅ Stock Span Problem

Time: O(n) - Each element pushed and popped max 1x
Space: O(n) - Stack size
```

## 🎯 When to Use This Pattern?

- ✅ Finding **next greater/smaller element**
- ✅ Problems with keywords: "next larger", "next warmer", "stock span"
- ✅ When need to track elements while maintaining specific order
- ✅ Optimization from O(n²) nested loop to O(n)

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Each element pushed and popped maximum 1 time
- **Space**: O(n) - Stack can contain all elements in worst case
- **Advantage**: Much faster than brute force O(n²)

## 🔗 LeetCode Problems

1. [Daily Temperatures (LeetCode #739)](https://leetcode.com/problems/daily-temperatures/)
2. [Next Greater Element I (LeetCode #496)](https://leetcode.com/problems/next-greater-element-i/)
3. [Largest Rectangle in Histogram (LeetCode #84)](https://leetcode.com/problems/largest-rectangle-in-histogram/)
4. [Trapping Rain Water (LeetCode #42)](https://leetcode.com/problems/trapping-rain-water/)

---

**Status**: ✅ REVIEWED & APPROVED
