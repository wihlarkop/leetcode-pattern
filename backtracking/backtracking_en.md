# Pattern 10: Backtracking

## 🌍 Real World Case
Imagine you are playing **Sudoku** or finding your way out of a **maze/labyrinth**. You try one path, if you hit a dead end, you **go back** (backtrack) and try another path. Backtracking is a systematic **trial and error** technique to find all solutions that meet certain constraints.

Another example: **Chess engine** trying various possible moves, or **password cracker** trying character combinations until finding the correct one.

## 📊 Concept Visualization
```
Problem: Generate all combinations of 2 numbers from [1, 2, 3]

Decision Tree (Backtracking):
                        []
                    /   |   \
                   1    2    3
                 / \    |    
                2   3   3    
                
Result: [1,2], [1,3], [2,3]

Backtracking Process:

Step 1: Choose 1
path = [1]
        []
       /
      1

Step 2: Choose 2 (from remaining [2,3])
path = [1, 2]
        []
       /
      1
     /
    2
Found solution: [1, 2] ✅
Backtrack → Remove 2

Step 3: Choose 3 (from remaining [3])
path = [1, 3]
        []
       /
      1
       \
        3
Found solution: [1, 3] ✅
Backtrack → Remove 3
Backtrack → Remove 1

Step 4: Choose 2
path = [2]
        []
         \
          2

Step 5: Choose 3 (from remaining [3])
path = [2, 3]
        []
         \
          2
           \
            3
Found solution: [2, 3] ✅
Backtrack → Remove 3
Backtrack → Remove 2

Done! All combinations: [[1,2], [1,3], [2,3]]

Key Pattern:
1. Choose (pick an option)
2. Explore (recursively solve subproblem)
3. Unchoose (backtrack - remove choice)
```

## 💻 Python Code with Visualization

```python
from typing import List

def combinations(nums: List[int], k: int) -> List[List[int]]:
    """
    Generate all combinations of k numbers from nums
    
    Visualization for nums=[1,2,3], k=2:
    
                        []
                    /   |   \
                  [1]  [2]  [3]
                 / \    |
              [1,2][1,3][2,3]
              ✅    ✅    ✅
    
    Backtracking process:
    
    Call backtrack([], 0):
      Choose 1 → path=[1]
        Call backtrack([1], 1):
          Choose 2 → path=[1,2], len=2 → Add to result ✅
          Unchoose 2 → path=[1]
          Choose 3 → path=[1,3], len=2 → Add to result ✅
          Unchoose 3 → path=[1]
      Unchoose 1 → path=[]
      
      Choose 2 → path=[2]
        Call backtrack([2], 2):
          Choose 3 → path=[2,3], len=2 → Add to result ✅
          Unchoose 3 → path=[2]
      Unchoose 2 → path=[]
      
      Choose 3 → path=[3]
        (No more numbers to choose)
      Unchoose 3 → path=[]
    
    Args:
        nums: List of numbers
        k: Size of combination
    
    Returns:
        List of all k-size combinations
    """
    result = []
    
    def backtrack(path: List[int], start: int):
        # Base case: found valid combination
        if len(path) == k:
            result.append(path[:])  # Make a copy
            return
        
        # Try all choices from start index
        for i in range(start, len(nums)):
            # Choose
            path.append(nums[i])
            
            # Explore
            backtrack(path, i + 1)
            
            # Unchoose (backtrack)
            path.pop()
    
    backtrack([], 0)
    return result


def permutations(nums: List[int]) -> List[List[int]]:
    """
    Generate all permutations from nums
    
    Visualization for nums=[1,2,3]:
    
    Level 0:            []
                    /   |   \
    Level 1:       1    2    3
                 / \   /\   / \
    Level 2:    2  3  1 3  1  2
                |  |  |  |  |  |
    Level 3:    3  2  3  1  2  1
    
    Results: [1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]
    
    Backtracking process:
    Path: []
    └─ Choose 1 → [1]
       └─ Choose 2 → [1,2]
          └─ Choose 3 → [1,2,3] ✅
          └─ Backtrack → [1,2]
       └─ Backtrack → [1]
       └─ Choose 3 → [1,3]
          └─ Choose 2 → [1,3,2] ✅
          └─ Backtrack → [1,3]
       └─ Backtrack → [1]
    └─ Backtrack → []
    └─ Choose 2 → [2]
       ... (continue)
    
    Args:
        nums: List of numbers
    
    Returns:
        List of all permutations
    """
    result = []
    
    def backtrack(path: List[int]):
        # Base case: used all numbers
        if len(path) == len(nums):
            result.append(path[:])
            return
        
        # Try each number
        for num in nums:
            if num in path:
                continue  # Skip used numbers
            
            # Choose
            path.append(num)
            
            # Explore
            backtrack(path)
            
            # Unchoose
            path.pop()
    
    backtrack([])
    return result


def solve_n_queens(n: int) -> List[List[str]]:
    """
    Solve N-Queens problem
    
    Visualization for n=4:
    
    Goal: Place 4 queens on 4x4 board so no queen attacks another
    
    Solution 1:        Solution 2:
    . Q . .            . . Q .
    . . . Q            Q . . .
    Q . . .            . . . Q
    . . Q .            . Q . .
    
    Backtracking process:
    Row 0: Try placing queen at each column
      Col 0: Place Q
        Row 1: Try columns
          Col 0: Invalid (same column)
          Col 1: Invalid (diagonal)
          Col 2: Place Q
            Row 2: Try columns
              Col 0: Invalid (diagonal)
              Col 1: Invalid (same column)
              Col 2: Invalid (same column)
              Col 3: Invalid (diagonal)
            Backtrack (no valid placement)
          Col 3: Place Q
            ... continue
    
    Args:
        n: Board size (n x n)
    
    Returns:
        All valid queen placements
    """
    result = []
    board = [['.'] * n for _ in range(n)]
    
    def is_valid(row: int, col: int) -> bool:
        # Check column
        for i in range(row):
            if board[i][col] == 'Q':
                return False
        
        # Check diagonal (top-left)
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j -= 1
        
        # Check diagonal (top-right)
        i, j = row - 1, col + 1
        while i >= 0 and j < n:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j += 1
        
        return True
    
    def backtrack(row: int):
        if row == n:
            # Found valid solution
            result.append([''.join(row) for row in board])
            return
        
        for col in range(n):
            if is_valid(row, col):
                # Choose
                board[row][col] = 'Q'
                
                # Explore
                backtrack(row + 1)
                
                # Unchoose
                board[row][col] = '.'
    
    backtrack(0)
    return result


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: COMBINATIONS")
    print("=" * 70)
    
    nums = [1, 2, 3]
    k = 2
    
    print(f"\nNumbers: {nums}")
    print(f"Combination size: {k}")
    
    print("\nDecision Tree:")
    print("                []")
    print("            /   |   \\")
    print("          [1]  [2]  [3]")
    print("         / \\    |")
    print("     [1,2][1,3][2,3]")
    print("      ✅    ✅    ✅")
    
    result = combinations(nums, k)
    print(f"\nAll combinations: {result}")
    
    print("\n" + "=" * 70)
    print("CASE 2: PERMUTATIONS")
    print("=" * 70)
    
    nums = [1, 2, 3]
    
    print(f"\nNumbers: {nums}")
    
    print("\nDecision Tree (partial):")
    print("            []")
    print("        /   |   \\")
    print("       1    2    3")
    print("      /\\   /\\   /\\")
    print("     2 3  1 3  1 2")
    print("     | |  | |  | |")
    print("     3 2  3 1  2 1")
    
    result = permutations(nums)
    print(f"\nAll permutations ({len(result)} total):")
    for perm in result:
        print(f"  {perm}")
    
    print("\n" + "=" * 70)
    print("CASE 3: N-QUEENS (4x4)")
    print("=" * 70)
    
    n = 4
    
    print(f"\nBoard size: {n}x{n}")
    print("Goal: Place 4 queens so none attack each other")
    
    solutions = solve_n_queens(n)
    
    print(f"\nFound {len(solutions)} solutions:\n")
    
    for i, solution in enumerate(solutions, 1):
        print(f"Solution {i}:")
        for row in solution:
            print(f"  {row}")
        print()
    
    print("=" * 70)
    print("REAL WORLD ANALOGY: SUDOKU SOLVER")
    print("=" * 70)
    
    print("""
Scenario: Solving a Sudoku puzzle

Backtracking approach:
1. Find empty cell
2. Try digits 1-9
   - Choose digit
   - Check if valid (row, col, box constraints)
   - If valid:
     * Place digit
     * Recursively solve rest
     * If stuck, backtrack (remove digit)
3. Repeat until puzzle solved

Example trace:
Cell (0,0): Try 1 → valid → place
  Cell (0,1): Try 1 → invalid (row)
  Cell (0,1): Try 2 → valid → place
    Cell (0,2): Try 1 → invalid (row)
    Cell (0,2): Try 2 → invalid (row)
    Cell (0,2): Try 3 → valid → place
      ... continue until solution or dead end
      If dead end: backtrack!

Other applications:
🎯 Constraint satisfaction problems
🧩 Puzzle solving (Crosswords, Sudoku)
♟️  Game AI (Chess, Checkers)
🗺️  Path finding with constraints
🔐 Password/combination cracking
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Backtracking Pattern:

1. 🎯 Template:
   def backtrack(path, choices):
       if is_solution(path):
           result.add(path)
           return
       
       for choice in choices:
           if is_valid(choice):
               # Choose
               path.add(choice)
               
               # Explore
               backtrack(path, new_choices)
               
               # Unchoose (backtrack)
               path.remove(choice)

2. 🔑 Key concepts:
   - Choose: Make a decision
   - Explore: Recursively solve subproblem
   - Unchoose: Undo decision (backtrack)

3. 🚀 Optimizations:
   - Pruning: Skip invalid branches early
   - Memoization: Cache results
   - Constraint checking: Validate before exploring

4. ⚡ Complexity:
   Time: O(b^d) where b=branching factor, d=depth
   Space: O(d) for recursion stack

When to use?
✅ "Find all solutions"
✅ "Generate all combinations/permutations"
✅ Constraint satisfaction problems
✅ Puzzle/game solving
✅ Path finding with constraints
    """)
```

## 📤 Output When Executed

```
======================================================================
CASE 1: COMBINATIONS
======================================================================

Numbers: [1, 2, 3]
Combination size: 2

Decision Tree:
                []
            /   |   \
          [1]  [2]  [3]
         / \    |
     [1,2][1,3][2,3]
      ✅    ✅    ✅

All combinations: [[1, 2], [1, 3], [2, 3]]

======================================================================
CASE 2: PERMUTATIONS
======================================================================

Numbers: [1, 2, 3]

Decision Tree (partial):
            []
        /   |   \
       1    2    3
      /\   /\   /\
     2 3  1 3  1 2
     | |  | |  | |
     3 2  3 1  2 1

All permutations (6 total):
  [1, 2, 3]
  [1, 3, 2]
  [2, 1, 3]
  [2, 3, 1]
  [3, 1, 2]
  [3, 2, 1]

======================================================================
CASE 3: N-QUEENS (4x4)
======================================================================

Board size: 4x4
Goal: Place 4 queens so none attack each other

Found 2 solutions:

Solution 1:
  .Q..
  ...Q
  Q...
  ..Q.

Solution 2:
  ..Q.
  Q...
  ...Q
  .Q..

======================================================================
REAL WORLD ANALOGY: SUDOKU SOLVER
======================================================================

Scenario: Solving a Sudoku puzzle

Backtracking approach:
1. Find empty cell
2. Try digits 1-9
   - Choose digit
   - Check if valid (row, col, box constraints)
   - If valid:
     * Place digit
     * Recursively solve rest
     * If stuck, backtrack (remove digit)
3. Repeat until puzzle solved

Example trace:
Cell (0,0): Try 1 → valid → place
  Cell (0,1): Try 1 → invalid (row)
  Cell (0,1): Try 2 → valid → place
    Cell (0,2): Try 1 → invalid (row)
    Cell (0,2): Try 2 → invalid (row)
    Cell (0,2): Try 3 → valid → place
      ... continue until solution or dead end
      If dead end: backtrack!

Other applications:
🎯 Constraint satisfaction problems
🧩 Puzzle solving (Crosswords, Sudoku)
♟️  Game AI (Chess, Checkers)
🗺️  Path finding with constraints
🔐 Password/combination cracking

======================================================================
KEY INSIGHTS
======================================================================

Backtracking Pattern:

1. 🎯 Template:
   def backtrack(path, choices):
       if is_solution(path):
           result.add(path)
           return
       
       for choice in choices:
           if is_valid(choice):
               # Choose
               path.add(choice)
               
               # Explore
               backtrack(path, new_choices)
               
               # Unchoose (backtrack)
               path.remove(choice)

2. 🔑 Key concepts:
   - Choose: Make a decision
   - Explore: Recursively solve subproblem
   - Unchoose: Undo decision (backtrack)

3. 🚀 Optimizations:
   - Pruning: Skip invalid branches early
   - Memoization: Cache results
   - Constraint checking: Validate before exploring

4. ⚡ Complexity:
   Time: O(b^d) where b=branching factor, d=depth
   Space: O(d) for recursion stack

When to use?
✅ "Find all solutions"
✅ "Generate all combinations/permutations"
✅ Constraint satisfaction problems
✅ Puzzle/game solving
✅ Path finding with constraints
```

## 🎯 When to Use This Pattern?

- ✅ Finding **all solutions** that meet constraints
- ✅ Keywords: "all combinations", "all permutations", "solve puzzle"
- ✅ **Constraint satisfaction** problems
- ✅ Need to explore all possibilities systematically

## ⏱️ Time & Space Complexity

- **Time**: O(b^d) - b = branching factor, d = depth (exponential!)
- **Space**: O(d) - Recursion stack depth
- **Note**: Can be very slow, but optimizations like pruning help

## 🔗 LeetCode Problems

1. [Combinations (LeetCode #77)](https://leetcode.com/problems/combinations/)
2. [Permutations (LeetCode #46)](https://leetcode.com/problems/permutations/)
3. [N-Queens (LeetCode #51)](https://leetcode.com/problems/n-queens/)
4. [Sudoku Solver (LeetCode #37)](https://leetcode.com/problems/sudoku-solver/)
5. [Word Search (LeetCode #79)](https://leetcode.com/problems/word-search/)

---

**Status**: ✅ REVIEWED & APPROVED
