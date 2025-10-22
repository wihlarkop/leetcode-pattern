# Pattern 2: Two Pointers

## 🌍 Real World Case
As you mentioned earlier! When you want to **transfer files to another disk**, you search for the **largest and smallest** file sizes to total them up so that 1 copy operation can fit perfectly with that disk's capacity.

For example: You have files [1GB, 3GB, 5GB, 7GB, 9GB, 11GB] and a disk with 12GB capacity. With two pointers, you can quickly find file pairs that total exactly 12GB.

## 📊 Concept Visualization
```
Files sorted by size: [1, 3, 5, 7, 9, 11] GB
Target capacity: 12GB

Step 1: Check smallest + largest
        ↓                    ↓
       [1, 3, 5, 7, 9, 11]
        L                 R
        1 + 11 = 12 ✅ MATCH!

Step 2: Move both pointers inward
             ↓           ↓
       [1, 3, 5, 7, 9, 11]
             L        R
        3 + 9 = 12 ✅ MATCH!

Step 3: Continue...
                 ↓   ↓
       [1, 3, 5, 7, 9, 11]
                 L R
        5 + 7 = 12 ✅ MATCH!

Result: Found 3 pairs!
```

## 💻 Python Code with Visualization

```python
from typing import List

def two_sum_sorted(nums: List[int], target: int) -> List[int]:
    """
    Find two numbers in sorted array that add up to target
    Returns the indices of the two numbers
    
    Process visualization:
    nums = [1, 3, 5, 7, 9, 11], target = 12
    
    Round 1:
    [1, 3, 5, 7, 9, 11]
     ↑              ↑
     L              R
    Sum: 1 + 11 = 12 ✅ Found!
    
    
    Another example:
    nums = [2, 7, 11, 15], target = 9
    
    Round 1:
    [2, 7, 11, 15]
     ↑         ↑
     L         R
    Sum: 2 + 15 = 17 > 9, move R ←
    
    Round 2:
    [2, 7, 11, 15]
     ↑      ↑
     L      R
    Sum: 2 + 11 = 13 > 9, move R ←
    
    Round 3:
    [2, 7, 11, 15]
     ↑   ↑
     L   R
    Sum: 2 + 7 = 9 ✅ Found!
    
    Logic:
    - If sum < target: move left pointer right (need larger number)
    - If sum > target: move right pointer left (need smaller number)
    - If sum == target: found the answer!
    
    Args:
        nums: Sorted array of integers
        target: Target sum we're looking for
    
    Returns:
        List containing [left_index, right_index] or empty list if not found
    """
    left = 0
    right = len(nums) - 1
    
    while left < right:
        current_sum = nums[left] + nums[right]
        
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            # Sum too small, need bigger number
            # Move left pointer to the right
            left += 1
        else:
            # Sum too big, need smaller number
            # Move right pointer to the left
            right -= 1
    
    return []  # No solution found


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 60)
    print("REAL CASE: FINDING FILE PAIRS FOR DISK CAPACITY")
    print("=" * 60)
    
    # File sizes in GB (already sorted)
    file_sizes = [1, 3, 5, 7, 9, 11]
    disk_capacity = 12
    
    print(f"\nFile sizes (GB): {file_sizes}")
    print(f"Disk capacity: {disk_capacity}GB")
    print(f"\nSearching for pairs that sum to {disk_capacity}GB...\n")
    
    # Find all pairs manually for demonstration
    left = 0
    right = len(file_sizes) - 1
    pair_count = 0
    
    while left < right:
        current_sum = file_sizes[left] + file_sizes[right]
        
        # Visual representation
        visual = ["  "] * len(file_sizes)
        visual[left] = "L "
        visual[right] = "R "
        
        print(f"Check: {file_sizes}")
        print(f"       {''.join(visual)}")
        print(f"       {file_sizes[left]}GB + {file_sizes[right]}GB = {current_sum}GB", end="")
        
        if current_sum == disk_capacity:
            pair_count += 1
            print(f" ✅ Pair #{pair_count} found!")
            left += 1
            right -= 1
        elif current_sum < disk_capacity:
            print(f" (too small, move L →)")
            left += 1
        else:
            print(f" (too big, move R ←)")
            right -= 1
        print()
    
    print("=" * 60)
    print("CLASSIC EXAMPLE: TWO SUM PROBLEM")
    print("=" * 60)
    
    nums = [2, 7, 11, 15]
    target = 9
    
    print(f"\nArray: {nums}")
    print(f"Target: {target}")
    
    result = two_sum_sorted(nums, target)
    
    if result:
        i, j = result
        print(f"\n✅ Solution found!")
        print(f"   Indices: [{i}, {j}]")
        print(f"   Values: {nums[i]} + {nums[j]} = {target}")
    else:
        print("\n❌ No solution exists")
    
    print("\n" + "=" * 60)
    print("STEP-BY-STEP VISUALIZATION")
    print("=" * 60)
    
    nums = [2, 7, 11, 15]
    target = 9
    left = 0
    right = len(nums) - 1
    step = 1
    
    print(f"\nArray: {nums}")
    print(f"Target: {target}\n")
    
    while left < right:
        current_sum = nums[left] + nums[right]
        
        # Visual representation
        visual = ["  "] * len(nums)
        visual[left] = "L "
        visual[right] = "R "
        
        print(f"Step {step}:")
        print(f"  {nums}")
        print(f"  {''.join(visual)}")
        print(f"  nums[{left}] + nums[{right}] = {nums[left]} + {nums[right]} = {current_sum}")
        
        if current_sum == target:
            print(f"  ✅ MATCH! Answer: [{left}, {right}]")
            break
        elif current_sum < target:
            print(f"  {current_sum} < {target}, need larger sum")
            print(f"  Action: Move LEFT pointer → (to get bigger number)")
            left += 1
        else:
            print(f"  {current_sum} > {target}, need smaller sum")
            print(f"  Action: Move RIGHT pointer ← (to get smaller number)")
            right -= 1
        
        print()
        step += 1
```

## 📤 Output When Executed

```
============================================================
REAL CASE: FINDING FILE PAIRS FOR DISK CAPACITY
============================================================

File sizes (GB): [1, 3, 5, 7, 9, 11]
Disk capacity: 12GB

Searching for pairs that sum to 12GB...

Check: [1, 3, 5, 7, 9, 11]
       L              R 
       1GB + 11GB = 12GB ✅ Pair #1 found!

Check: [1, 3, 5, 7, 9, 11]
          L        R    
       3GB + 9GB = 12GB ✅ Pair #2 found!

Check: [1, 3, 5, 7, 9, 11]
             L  R       
       5GB + 7GB = 12GB ✅ Pair #3 found!

============================================================
CLASSIC EXAMPLE: TWO SUM PROBLEM
============================================================

Array: [2, 7, 11, 15]
Target: 9

✅ Solution found!
   Indices: [0, 1]
   Values: 2 + 7 = 9

============================================================
STEP-BY-STEP VISUALIZATION
============================================================

Array: [2, 7, 11, 15]
Target: 9

Step 1:
  [2, 7, 11, 15]
  L          R 
  nums[0] + nums[3] = 2 + 15 = 17
  17 > 9, need smaller sum
  Action: Move RIGHT pointer ← (to get smaller number)

Step 2:
  [2, 7, 11, 15]
  L      R     
  nums[0] + nums[2] = 2 + 11 = 13
  13 > 9, need smaller sum
  Action: Move RIGHT pointer ← (to get smaller number)

Step 3:
  [2, 7, 11, 15]
  L   R        
  nums[0] + nums[1] = 2 + 7 = 9
  ✅ MATCH! Answer: [0, 1]
```

## 🎯 When to Use This Pattern?

- ✅ Array is **sorted** or can be sorted
- ✅ Looking for **pairs** that meet certain conditions
- ✅ Problems with keywords: "two numbers", "pair", "sum equals"
- ✅ Optimization from brute force O(n²) to O(n)

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Single pass with two pointers
- **Space**: O(1) - Only need 2 pointer variables
- **Note**: If array is not sorted, need O(n log n) for sorting

## 🔗 LeetCode Problems

1. [Two Sum II - Input Array Is Sorted (LeetCode #167)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
2. [3Sum (LeetCode #15)](https://leetcode.com/problems/3sum/)
3. [Container With Most Water (LeetCode #11)](https://leetcode.com/problems/container-with-most-water/)
4. [Trapping Rain Water (LeetCode #42)](https://leetcode.com/problems/trapping-rain-water/)

---

**Status**: ✅ REVIEWED & APPROVED
