# Pattern 3: Sliding Window

## 🌍 Real World Case
Bayangkan Anda adalah **manajer penjualan** yang ingin mengetahui **periode 7 hari dengan penjualan tertinggi** dalam sebulan. Alih-alih menghitung ulang total untuk setiap 7 hari dari awal (yang lambat), Anda menggunakan sliding window: **geser window**, **kurangi hari pertama**, **tambahkan hari baru**, lalu bandingkan!

Contoh lain: **Netflix** menganalisis **rata-rata waktu menonton dalam 3 hari berturut-turut** untuk mendeteksi pola binge-watching.

## 📊 Visualisasi Konsep
```
Penjualan Harian: [100, 200, 150, 300, 250, 400, 350, 500]
Window size k = 3 (mencari 3 hari berturut dengan penjualan tertinggi)

Step 1: Window di posisi awal
[100, 200, 150] 300, 250, 400, 350, 500
 └────────────┘
 Sum = 450

Step 2: Slide window ke kanan (hapus 100, tambah 300)
 100, [200, 150, 300] 250, 400, 350, 500
      └────────────┘
      Sum = 650

Step 3: Slide window lagi (hapus 200, tambah 250)
 100, 200, [150, 300, 250] 400, 350, 500
           └────────────┘
           Sum = 700

Step 4: Slide window lagi (hapus 150, tambah 400)
 100, 200, 150, [300, 250, 400] 350, 500
                └────────────┘
                Sum = 950 ← Maksimum sejauh ini!

... dan seterusnya

Result: Window [300, 250, 400] = 950 adalah yang tertinggi!
```

## 💻 Code Python dengan Visualisasi

```python
from typing import List

def max_sum_subarray(nums: List[int], k: int) -> int:
    """
    Find maximum sum of any contiguous subarray of size k
    
    Visualisasi proses:
    nums = [100, 200, 150, 300, 250, 400, 350, 500]
    k = 3
    
    Initial Window (i=0 to i=2):
    [100, 200, 150] 300, 250, 400, 350, 500
     └────────────┘
     window_sum = 100 + 200 + 150 = 450
     max_sum = 450
    
    Slide 1 (remove nums[0]=100, add nums[3]=300):
     100, [200, 150, 300] 250, 400, 350, 500
          └────────────┘
     window_sum = 450 - 100 + 300 = 650
     max_sum = 650
    
    Slide 2 (remove nums[1]=200, add nums[4]=250):
     100, 200, [150, 300, 250] 400, 350, 500
               └────────────┘
     window_sum = 650 - 200 + 250 = 700
     max_sum = 700
    
    Slide 3 (remove nums[2]=150, add nums[5]=400):
     100, 200, 150, [300, 250, 400] 350, 500
                    └────────────┘
     window_sum = 700 - 150 + 400 = 950
     max_sum = 950 ← Best so far!
    
    ... continue sliding
    
    Key Pattern:
    - Start: Calculate sum of first k elements
    - Slide: Remove leftmost, add rightmost
    - Formula: new_sum = old_sum - nums[i-k] + nums[i]
    
    Args:
        nums: Array of integers (sales data, etc)
        k: Size of the sliding window
    
    Returns:
        Maximum sum found in any window of size k
    """
    if len(nums) < k:
        return 0
    
    # Calculate sum of first window
    window_sum = sum(nums[:k])
    max_sum = window_sum
    
    # Slide the window from left to right
    for i in range(k, len(nums)):
        # Remove leftmost element of previous window
        # Add rightmost element of current window
        window_sum = window_sum - nums[i - k] + nums[i]
        max_sum = max(max_sum, window_sum)
    
    return max_sum


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 70)
    print("KASUS NYATA: MENCARI PERIODE 3 HARI DENGAN PENJUALAN TERTINGGI")
    print("=" * 70)
    
    # Penjualan harian dalam jutaan rupiah
    sales = [100, 200, 150, 300, 250, 400, 350, 500]
    k = 3
    
    print(f"\nPenjualan Harian (juta): {sales}")
    print(f"Window size: {k} hari berturut-turut")
    print(f"\nProses Sliding Window:\n")
    
    # Visualisasi step by step
    window_sum = sum(sales[:k])
    max_sum = window_sum
    max_window_start = 0
    
    # Print initial window
    window_visual = ["[" + str(sales[i]) if i == 0 else str(sales[i]) for i in range(k)]
    window_visual[-1] += "]"
    remaining = sales[k:]
    
    print(f"Window 1 (index 0-{k-1}):")
    print(f"  {', '.join(window_visual)}, {', '.join(map(str, remaining))}")
    print(f"  Sum = {' + '.join(map(str, sales[:k]))} = {window_sum}")
    print(f"  Max = {max_sum}\n")
    
    # Slide window
    for i in range(k, len(sales)):
        old_sum = window_sum
        removed = sales[i - k]
        added = sales[i]
        window_sum = window_sum - removed + added
        
        print(f"Window {i - k + 2} (index {i-k+1}-{i}):")
        
        # Visual representation
        prefix = sales[:i-k+1]
        window = sales[i-k+1:i+1]
        suffix = sales[i+1:] if i+1 < len(sales) else []
        
        prefix_str = ', '.join(map(str, prefix)) + ", " if prefix else ""
        window_str = "[" + ', '.join(map(str, window)) + "]"
        suffix_str = ", " + ', '.join(map(str, suffix)) if suffix else ""
        
        print(f"  {prefix_str}{window_str}{suffix_str}")
        print(f"  Sum = {old_sum} - {removed} + {added} = {window_sum}")
        
        if window_sum > max_sum:
            max_sum = window_sum
            max_window_start = i - k + 1
            print(f"  Max = {max_sum} ← NEW MAXIMUM! 🎉")
        else:
            print(f"  Max = {max_sum}")
        print()
    
    print("=" * 70)
    print("HASIL AKHIR")
    print("=" * 70)
    best_window = sales[max_window_start:max_window_start + k]
    print(f"\nPeriode terbaik: Day {max_window_start + 1} - Day {max_window_start + k}")
    print(f"Window: {best_window}")
    print(f"Total Penjualan: Rp {max_sum} juta")
    print(f"Rata-rata per hari: Rp {max_sum / k:.2f} juta")
    
    print("\n" + "=" * 70)
    print("CONTOH LAIN: LONGEST SUBSTRING WITHOUT REPEATING CHARACTERS")
    print("=" * 70)
    
    # Bonus example - variable size window
    def length_of_longest_substring(s: str) -> int:
        """
        Find length of longest substring without repeating characters
        Uses variable-size sliding window
        """
        char_set = set()
        left = 0
        max_length = 0
        
        for right in range(len(s)):
            # If character already in window, shrink from left
            while s[right] in char_set:
                char_set.remove(s[left])
                left += 1
            
            # Add current character and update max
            char_set.add(s[right])
            max_length = max(max_length, right - left + 1)
        
        return max_length
    
    test_string = "abcabcbb"
    result = length_of_longest_substring(test_string)
    
    print(f"\nString: '{test_string}'")
    print(f"Longest substring without repeating: {result}")
    print(f"(substring 'abc' has length 3)")
```

## 📤 Output Ketika Dijalankan

```
======================================================================
KASUS NYATA: MENCARI PERIODE 3 HARI DENGAN PENJUALAN TERTINGGI
======================================================================

Penjualan Harian (juta): [100, 200, 150, 300, 250, 400, 350, 500]
Window size: 3 hari berturut-turut

Proses Sliding Window:

Window 1 (index 0-2):
  [100, 200, 150], 300, 250, 400, 350, 500
  Sum = 100 + 200 + 150 = 450
  Max = 450

Window 2 (index 1-3):
  100, [200, 150, 300], 250, 400, 350, 500
  Sum = 450 - 100 + 300 = 650
  Max = 650 ← NEW MAXIMUM! 🎉

Window 3 (index 2-4):
  100, 200, [150, 300, 250], 400, 350, 500
  Sum = 650 - 200 + 250 = 700
  Max = 700 ← NEW MAXIMUM! 🎉

Window 4 (index 3-5):
  100, 200, 150, [300, 250, 400], 350, 500
  Sum = 700 - 150 + 400 = 950
  Max = 950 ← NEW MAXIMUM! 🎉

Window 5 (index 4-6):
  100, 200, 150, 300, [250, 400, 350], 500
  Sum = 950 - 300 + 350 = 1000
  Max = 1000 ← NEW MAXIMUM! 🎉

Window 6 (index 5-7):
  100, 200, 150, 300, 250, [400, 350, 500]
  Sum = 1000 - 250 + 500 = 1250
  Max = 1250 ← NEW MAXIMUM! 🎉

======================================================================
HASIL AKHIR
======================================================================

Periode terbaik: Day 6 - Day 8
Window: [400, 350, 500]
Total Penjualan: Rp 1250 juta
Rata-rata per hari: Rp 416.67 juta

======================================================================
CONTOH LAIN: LONGEST SUBSTRING WITHOUT REPEATING CHARACTERS
======================================================================

String: 'abcabcbb'
Longest substring without repeating: 3
(substring 'abc' has length 3)
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Problem dengan **subarray/substring yang berurutan (contiguous)**
- ✅ Mencari **maximum/minimum** dalam fixed atau variable window
- ✅ Kata kunci: "consecutive", "contiguous", "subarray of size k"
- ✅ Optimasi dari O(n*k) menjadi O(n)

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Hanya iterate sekali dengan window yang slide
- **Space**: O(1) atau O(k) tergantung kebutuhan tracking
- **Brute Force alternative**: O(n*k) - Much slower!

## 🔗 LeetCode Problems

1. [Maximum Average Subarray I (LeetCode #643)](https://leetcode.com/problems/maximum-average-subarray-i/)
2. [Longest Substring Without Repeating Characters (LeetCode #3)](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
3. [Minimum Window Substring (LeetCode #76)](https://leetcode.com/problems/minimum-window-substring/)
4. [Sliding Window Maximum (LeetCode #239)](https://leetcode.com/problems/sliding-window-maximum/)

---

**Status**: ✅ REVIEWED & APPROVED
