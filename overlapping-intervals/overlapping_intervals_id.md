# Pattern 8: Overlapping Intervals

## 🌍 Real World Case
Bayangkan Anda adalah **office manager** yang harus booking **meeting rooms**. Ada banyak meeting request dengan waktu berbeda: Meeting A (9-10), Meeting B (9:30-11), Meeting C (11-12). Anda perlu tahu: berapa ruangan minimum yang dibutuhkan? Meeting mana yang overlap dan perlu dimerge? Pattern ini solve masalah tersebut!

Contoh lain: **Calendar app** yang merge adjacent appointments, atau **server load balancer** yang menggabungkan time ranges dengan high traffic.

## 📊 Visualisasi Konsep
```
Problem: Merge overlapping intervals

Input: [[1,3], [2,6], [8,10], [15,18]]

Timeline visualization:
0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18
    [-------]
        [---------------]
                                [-------]
                                                                [----------]

Step 1: Sort by start time (already sorted)
[[1,3], [2,6], [8,10], [15,18]]

Step 2: Merge overlapping intervals

Check [1,3] and [2,6]:
    [-------]
        [---------------]
    ↓
    [-------------------]  ← Merged to [1,6]

Check [1,6] and [8,10]:
    [-------------------]
                                [-------]
    No overlap! Keep both.

Check [8,10] and [15,18]:
                                [-------]
                                                                [----------]
    No overlap! Keep both.

Result: [[1,6], [8,10], [15,18]]

Timeline after merge:
0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18
    [-------------------]
                                [-------]
                                                                [----------]

Overlap Detection Rule:
Two intervals [a,b] and [c,d] overlap if: b >= c
(end of first >= start of second)
```

## 💻 Code Python dengan Visualisasi

```python
from typing import List

def merge_intervals(intervals: List[List[int]]) -> List[List[int]]:
    """
    Merge semua overlapping intervals
    
    Visualisasi proses:
    intervals = [[1,3], [2,6], [8,10], [15,18]]
    
    Step 1: Sort by start time
    [[1,3], [2,6], [8,10], [15,18]] (already sorted)
    
    Step 2: Initialize dengan interval pertama
    merged = [[1,3]]
    
    Step 3: Process [2,6]
    Last interval: [1,3]
    Current: [2,6]
    3 >= 2? YES → Overlap!
    Merge: [1, max(3,6)] = [1,6]
    merged = [[1,6]]
    
    Step 4: Process [8,10]
    Last interval: [1,6]
    Current: [8,10]
    6 >= 8? NO → No overlap
    Add [8,10]
    merged = [[1,6], [8,10]]
    
    Step 5: Process [15,18]
    Last interval: [8,10]
    Current: [15,18]
    10 >= 15? NO → No overlap
    Add [15,18]
    merged = [[1,6], [8,10], [15,18]]
    
    Key Pattern:
    1. Sort by start time
    2. Check if current overlaps with last merged
    3. If yes: extend last merged's end time
    4. If no: add current as new interval
    
    Args:
        intervals: List of intervals [start, end]
    
    Returns:
        List of merged intervals
    """
    if not intervals:
        return []
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        last = merged[-1]
        
        # Check overlap: last_end >= current_start
        if last[1] >= current[0]:
            # Merge: extend end time
            last[1] = max(last[1], current[1])
        else:
            # No overlap: add new interval
            merged.append(current)
    
    return merged


def insert_interval(intervals: List[List[int]], new_interval: List[int]) -> List[List[int]]:
    """
    Insert interval baru dan merge jika perlu
    
    Visualisasi:
    intervals = [[1,3], [6,9]]
    new_interval = [2,5]
    
    Timeline:
    0   1   2   3   4   5   6   7   8   9   10
        [---]           [-------]
            [-------]  ← new interval
    
    Step 1: Add all intervals before new_interval
    [2,5] starts at 2
    [1,3] ends at 3 >= 2 → overlap!
    
    Step 2: Merge overlapping
    [1,3] and [2,5] → [1,5]
    
    Step 3: Check next interval [6,9]
    [1,5] ends at 5 < 6 → no overlap
    Add [6,9]
    
    Result: [[1,5], [6,9]]
    
    Args:
        intervals: Sorted list of intervals
        new_interval: New interval to insert
    
    Returns:
        Updated list with new interval merged
    """
    result = []
    i = 0
    n = len(intervals)
    
    # Add all intervals before new_interval
    while i < n and intervals[i][1] < new_interval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge overlapping intervals
    while i < n and intervals[i][0] <= new_interval[1]:
        new_interval[0] = min(new_interval[0], intervals[i][0])
        new_interval[1] = max(new_interval[1], intervals[i][1])
        i += 1
    
    result.append(new_interval)
    
    # Add remaining intervals
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result


def min_meeting_rooms(intervals: List[List[int]]) -> int:
    """
    Hitung minimum meeting rooms yang dibutuhkan
    
    Visualisasi:
    intervals = [[0,30], [5,10], [15,20]]
    
    Timeline:
    0   5   10  15  20  25  30
    [---------------------------]  Meeting 1
        [---]                       Meeting 2
                [---]               Meeting 3
    
    At time 5:
    - Meeting 1 ongoing
    - Meeting 2 starts
    → Need 2 rooms
    
    At time 15:
    - Meeting 1 ongoing
    - Meeting 2 ended
    - Meeting 3 starts
    → Need 2 rooms
    
    Maximum concurrent meetings = 2 rooms needed
    
    Algorithm:
    1. Separate start and end times
    2. Sort both
    3. Use two pointers to track concurrent meetings
    
    Args:
        intervals: List of meeting time intervals
    
    Returns:
        Minimum number of rooms needed
    """
    if not intervals:
        return 0
    
    start_times = sorted([i[0] for i in intervals])
    end_times = sorted([i[1] for i in intervals])
    
    rooms_needed = 0
    max_rooms = 0
    start_ptr = 0
    end_ptr = 0
    
    while start_ptr < len(intervals):
        if start_times[start_ptr] < end_times[end_ptr]:
            # Meeting starts, need a room
            rooms_needed += 1
            max_rooms = max(max_rooms, rooms_needed)
            start_ptr += 1
        else:
            # Meeting ends, free a room
            rooms_needed -= 1
            end_ptr += 1
    
    return max_rooms


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("CASE 1: MERGE OVERLAPPING INTERVALS")
    print("=" * 70)
    
    intervals = [[1,3], [2,6], [8,10], [15,18]]
    
    print(f"\nOriginal intervals: {intervals}")
    
    print("\nTimeline visualization:")
    print("0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18")
    print("    [-------]")
    print("        [---------------]")
    print("                                [-------]")
    print("                                                                [----------]")
    
    print("\nMerge process step-by-step:\n")
    
    # Simulate
    sorted_intervals = sorted(intervals, key=lambda x: x[0])
    merged = [sorted_intervals[0]]
    
    print(f"Step 1: Initialize with first interval")
    print(f"  merged = {merged}")
    print()
    
    for i, current in enumerate(sorted_intervals[1:], 2):
        last = merged[-1]
        
        print(f"Step {i}: Process {current}")
        print(f"  Last interval: {last}")
        print(f"  Current: {current}")
        
        if last[1] >= current[0]:
            print(f"  {last[1]} >= {current[0]} → OVERLAP!")
            old_end = last[1]
            last[1] = max(last[1], current[1])
            print(f"  Merge: [{last[0]}, {old_end}] + {current} = [{last[0]}, {last[1]}]")
        else:
            print(f"  {last[1]} < {current[0]} → No overlap")
            merged.append(current)
            print(f"  Add new interval: {current}")
        
        print(f"  merged = {merged}")
        print()
    
    result = merge_intervals(intervals)
    print(f"Final result: {result}")
    
    print("\nTimeline after merge:")
    print("0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18")
    print("    [-------------------]")
    print("                                [-------]")
    print("                                                                [----------]")
    
    print("\n" + "=" * 70)
    print("CASE 2: INSERT NEW INTERVAL")
    print("=" * 70)
    
    intervals = [[1,3], [6,9]]
    new_interval = [2,5]
    
    print(f"\nExisting intervals: {intervals}")
    print(f"New interval: {new_interval}")
    
    print("\nBefore insertion:")
    print("0   1   2   3   4   5   6   7   8   9   10")
    print("    [---]           [-------]")
    print("        [-------]  ← new")
    
    result = insert_interval(intervals, new_interval[:])
    
    print(f"\nResult: {result}")
    
    print("\nAfter insertion & merge:")
    print("0   1   2   3   4   5   6   7   8   9   10")
    print("    [---------------]   [-------]")
    
    print("\n" + "=" * 70)
    print("CASE 3: MINIMUM MEETING ROOMS")
    print("=" * 70)
    
    meetings = [[0,30], [5,10], [15,20]]
    
    print(f"\nMeeting times: {meetings}")
    
    print("\nTimeline:")
    print("Time:  0   5   10  15  20  25  30")
    print("M1:    [---------------------------]")
    print("M2:        [---]")
    print("M3:                [---]")
    
    print("\nAnalysis:")
    print("Time 0-5:   1 room (M1)")
    print("Time 5-10:  2 rooms (M1, M2) ← Peak!")
    print("Time 10-15: 1 room (M1)")
    print("Time 15-20: 2 rooms (M1, M3)")
    print("Time 20-30: 1 room (M1)")
    
    rooms = min_meeting_rooms(meetings)
    print(f"\nMinimum rooms needed: {rooms}")
    
    print("\n" + "=" * 70)
    print("REAL WORLD ANALOGY: CALENDAR APP")
    print("=" * 70)
    
    print("""
Scenario: Google Calendar merging adjacent appointments

Your schedule:
- 9:00-10:00   Team Standup
- 9:30-11:00   Project Review (overlaps!)
- 11:00-12:00  Lunch
- 15:00-16:00  Client Call

Smart merge:
- 9:00-11:00   Team Standup + Project Review (merged!)
- 11:00-12:00  Lunch
- 15:00-16:00  Client Call

Benefits:
✅ Cleaner calendar view
✅ Avoid double-booking
✅ Better time management
✅ Easier conflict detection

Other use cases:
📅 Meeting room scheduling
🏥 Doctor appointment booking
🚗 Parking slot reservation
📺 TV program scheduling
    """)
    
    print("\n" + "=" * 70)
    print("KEY INSIGHTS")
    print("=" * 70)
    
    print("""
Overlapping Intervals Pattern:

1. 📊 Sort intervals by start time (critical!)

2. 🔍 Overlap detection:
   Two intervals [a,b] and [c,d] overlap if: b >= c

3. 🔄 Common operations:
   - Merge: [start1, max(end1, end2)]
   - Insert: Find position, merge overlaps
   - Count conflicts: Track concurrent intervals

4. ⚡ Algorithm steps:
   - Sort by start time: O(n log n)
   - Single pass merge: O(n)
   - Total: O(n log n)

Time Complexity: O(n log n) - dominated by sorting
Space Complexity: O(n) - for output array

Kapan digunakan?
✅ "Merge intervals"
✅ "Find conflicts/overlaps"
✅ "Minimum resources needed"
✅ Scheduling problems
    """)
```

## 📤 Output Ketika Dijalankan

```
======================================================================
CASE 1: MERGE OVERLAPPING INTERVALS
======================================================================

Original intervals: [[1, 3], [2, 6], [8, 10], [15, 18]]

Timeline visualization:
0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18
    [-------]
        [---------------]
                                [-------]
                                                                [----------]

Merge process step-by-step:

Step 1: Initialize with first interval
  merged = [[1, 3]]

Step 2: Process [2, 6]
  Last interval: [1, 3]
  Current: [2, 6]
  3 >= 2 → OVERLAP!
  Merge: [1, 3] + [2, 6] = [1, 6]
  merged = [[1, 6]]

Step 3: Process [8, 10]
  Last interval: [1, 6]
  Current: [8, 10]
  6 < 8 → No overlap
  Add new interval: [8, 10]
  merged = [[1, 6], [8, 10]]

Step 4: Process [15, 18]
  Last interval: [8, 10]
  Current: [15, 18]
  10 < 15 → No overlap
  Add new interval: [15, 18]
  merged = [[1, 6], [8, 10], [15, 18]]

Final result: [[1, 6], [8, 10], [15, 18]]

Timeline after merge:
0   1   2   3   4   5   6   7   8   9   10  11  12  13  14  15  16  17  18
    [-------------------]
                                [-------]
                                                                [----------]

======================================================================
CASE 2: INSERT NEW INTERVAL
======================================================================

Existing intervals: [[1, 3], [6, 9]]
New interval: [2, 5]

Before insertion:
0   1   2   3   4   5   6   7   8   9   10
    [---]           [-------]
        [-------]  ← new

Result: [[1, 5], [6, 9]]

After insertion & merge:
0   1   2   3   4   5   6   7   8   9   10
    [---------------]   [-------]

======================================================================
CASE 3: MINIMUM MEETING ROOMS
======================================================================

Meeting times: [[0, 30], [5, 10], [15, 20]]

Timeline:
Time:  0   5   10  15  20  25  30
M1:    [---------------------------]
M2:        [---]
M3:                [---]

Analysis:
Time 0-5:   1 room (M1)
Time 5-10:  2 rooms (M1, M2) ← Peak!
Time 10-15: 1 room (M1)
Time 15-20: 2 rooms (M1, M3)
Time 20-30: 1 room (M1)

Minimum rooms needed: 2

======================================================================
REAL WORLD ANALOGY: CALENDAR APP
======================================================================

Scenario: Google Calendar merging adjacent appointments

Your schedule:
- 9:00-10:00   Team Standup
- 9:30-11:00   Project Review (overlaps!)
- 11:00-12:00  Lunch
- 15:00-16:00  Client Call

Smart merge:
- 9:00-11:00   Team Standup + Project Review (merged!)
- 11:00-12:00  Lunch
- 15:00-16:00  Client Call

Benefits:
✅ Cleaner calendar view
✅ Avoid double-booking
✅ Better time management
✅ Easier conflict detection

Other use cases:
📅 Meeting room scheduling
🏥 Doctor appointment booking
🚗 Parking slot reservation
📺 TV program scheduling

======================================================================
KEY INSIGHTS
======================================================================

Overlapping Intervals Pattern:

1. 📊 Sort intervals by start time (critical!)

2. 🔍 Overlap detection:
   Two intervals [a,b] and [c,d] overlap if: b >= c

3. 🔄 Common operations:
   - Merge: [start1, max(end1, end2)]
   - Insert: Find position, merge overlaps
   - Count conflicts: Track concurrent intervals

4. ⚡ Algorithm steps:
   - Sort by start time: O(n log n)
   - Single pass merge: O(n)
   - Total: O(n log n)

Time Complexity: O(n log n) - dominated by sorting
Space Complexity: O(n) - for output array

Kapan digunakan?
✅ "Merge intervals"
✅ "Find conflicts/overlaps"
✅ "Minimum resources needed"
✅ Scheduling problems
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Problem dengan **intervals/ranges** yang overlap
- ✅ Kata kunci: "merge intervals", "overlapping", "conflicts", "scheduling"
- ✅ **Resource allocation** problems (meeting rooms, parking, etc)
- ✅ Time-based data yang perlu digabungkan atau dianalisa

## ⏱️ Time & Space Complexity

- **Time**: O(n log n) - Sorting dominates, merge hanya O(n)
- **Space**: O(n) - Output array untuk hasil
- **Key step**: ALWAYS sort by start time first!

## 🔗 LeetCode Problems

1. [Merge Intervals (LeetCode #56)](https://leetcode.com/problems/merge-intervals/)
2. [Insert Interval (LeetCode #57)](https://leetcode.com/problems/insert-interval/)
3. [Meeting Rooms (LeetCode #252)](https://leetcode.com/problems/meeting-rooms/)
4. [Meeting Rooms II (LeetCode #253)](https://leetcode.com/problems/meeting-rooms-ii/)
5. [Non-overlapping Intervals (LeetCode #435)](https://leetcode.com/problems/non-overlapping-intervals/)

---

**Status**: ✅ REVIEWED & APPROVED
