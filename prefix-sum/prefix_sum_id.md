# Pattern 1: Prefix Sum

## 🌍 Real World Case
Bayangkan Anda bekerja di e-commerce dan ingin menghitung **total penjualan dalam periode tertentu** (misalnya penjualan dari hari ke-5 sampai hari ke-10 dalam sebulan). Tanpa prefix sum, Anda harus menjumlahkan manual setiap hari. Dengan prefix sum, Anda bisa langsung hitung dengan formula `P[j] - P[i-1]`.

## 📊 Visualisasi Konsep
```
Penjualan Harian:     [100, 200, 150, 300, 250, 400]
                       Day0 Day1 Day2 Day3 Day4 Day5

Prefix Sum Array:     [100, 300, 450, 750, 1000, 1400]
                       ↑    ↑    ↑    ↑     ↑     ↑
                      Sum  Sum  Sum  Sum   Sum   Sum
                      0-0  0-1  0-2  0-3   0-4   0-5

Query: Total penjualan dari Day 2 sampai Day 4?
Answer: P[4] - P[1] = 1000 - 300 = 700 ✅
       (150 + 300 + 250 = 700)
```

## 💻 Code Python dengan Visualisasi

```python
from typing import List

def build_prefix_sum(sales: List[int]) -> List[int]:
    """
    Membangun prefix sum array dari sales harian
    
    Visualisasi proses:
    sales  = [100, 200, 150, 300, 250, 400]
    
    Step 0: prefix[0] = sales[0] = 100
    prefix = [100, ?, ?, ?, ?, ?]
    
    Step 1: prefix[1] = prefix[0] + sales[1] = 100 + 200 = 300
    prefix = [100, 300, ?, ?, ?, ?]
    
    Step 2: prefix[2] = prefix[1] + sales[2] = 300 + 150 = 450
    prefix = [100, 300, 450, ?, ?, ?]
    
    ... dan seterusnya
    """
    prefix = [0] * len(sales)
    prefix[0] = sales[0]
    
    for i in range(1, len(sales)):
        prefix[i] = prefix[i-1] + sales[i]
    
    return prefix


def range_sum(prefix: List[int], start: int, end: int) -> int:
    """
    Menghitung total penjualan dari start hingga end
    
    Visualisasi:
    
    Case 1 - Start dari 0:
    ----------------------
    range_sum(prefix, 0, 3)
    
    prefix = [100, 300, 450, 750, ...]
              ↑              ↑
            start          end
    
    Return: prefix[3] = 750
    
    
    Case 2 - Start dari tengah:
    ---------------------------
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
    
    Ini sama dengan: 150 + 300 + 250 = 700
    """
    if start == 0:
        return prefix[end]
    return prefix[end] - prefix[start - 1]


# ==================== DEMO ====================
if __name__ == "__main__":
    # Data penjualan harian (dalam ribu rupiah)
    sales = [100, 200, 150, 300, 250, 400]
    
    print("=" * 50)
    print("PENJUALAN HARIAN:")
    print("=" * 50)
    for i, sale in enumerate(sales):
        print(f"Day {i}: Rp {sale},000")
    
    # Build prefix sum
    prefix = build_prefix_sum(sales)
    
    print("\n" + "=" * 50)
    print("PREFIX SUM (Total Akumulasi):")
    print("=" * 50)
    for i, total in enumerate(prefix):
        print(f"Day 0 - Day {i}: Rp {total},000")
    
    print("\n" + "=" * 50)
    print("QUERY EXAMPLES:")
    print("=" * 50)
    
    # Query 1: Total penjualan dari awal (Day 0) sampai Day 3
    result1 = range_sum(prefix, 0, 3)
    print(f"\n1. Total penjualan Day 0-3: Rp {result1},000")
    print(f"   Visualisasi: [100, 200, 150, 300]")
    print(f"   Manual check: 100 + 200 + 150 + 300 = {sum(sales[0:4])}")
    
    # Query 2: Total penjualan dari Day 2 sampai Day 4
    result2 = range_sum(prefix, 2, 4)
    print(f"\n2. Total penjualan Day 2-4: Rp {result2},000")
    print(f"   Visualisasi: [150, 300, 250]")
    print(f"   Manual check: 150 + 300 + 250 = {sum(sales[2:5])}")
    print(f"   Cara hitung: prefix[4] - prefix[1] = {prefix[4]} - {prefix[1]} = {result2}")
    
    # Query 3: Total semua penjualan
    result3 = range_sum(prefix, 0, 5)
    print(f"\n3. Total semua penjualan: Rp {result3},000")
    print(f"   Visualisasi: [100, 200, 150, 300, 250, 400]")
    print(f"   Manual check: {' + '.join(map(str, sales))} = {sum(sales)}")
```

## 📤 Output Ketika Dijalankan

```
==================================================
PENJUALAN HARIAN:
==================================================
Day 0: Rp 100,000
Day 1: Rp 200,000
Day 2: Rp 150,000
Day 3: Rp 300,000
Day 4: Rp 250,000
Day 5: Rp 400,000

==================================================
PREFIX SUM (Total Akumulasi):
==================================================
Day 0 - Day 0: Rp 100,000
Day 0 - Day 1: Rp 300,000
Day 0 - Day 2: Rp 450,000
Day 0 - Day 3: Rp 750,000
Day 0 - Day 4: Rp 1000,000
Day 0 - Day 5: Rp 1400,000

==================================================
QUERY EXAMPLES:
==================================================

1. Total penjualan Day 0-3: Rp 750,000
   Visualisasi: [100, 200, 150, 300]
   Manual check: 100 + 200 + 150 + 300 = 750

2. Total penjualan Day 2-4: Rp 700,000
   Visualisasi: [150, 300, 250]
   Manual check: 150 + 300 + 250 = 700
   Cara hitung: prefix[4] - prefix[1] = 1000 - 300 = 700

3. Total semua penjualan: Rp 1400,000
   Visualisasi: [100, 200, 150, 300, 250, 400]
   Manual check: 100 + 200 + 150 + 300 + 250 + 400 = 1400
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Ketika perlu menghitung sum dari subarray berkali-kali
- ✅ Ketika ada multiple queries untuk range sum
- ✅ Untuk optimasi dari O(n) per query menjadi O(1) per query
- ✅ Problem yang melibatkan cumulative sum atau running total

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
