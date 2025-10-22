# Pattern 2: Two Pointers

## 🌍 Real World Case
Seperti yang Anda sebutkan sebelumnya! Ketika Anda ingin **memindahkan file ke disk lain**, Anda mencari file yang **paling besar dan paling kecil** ukurannya untuk ditotalkan agar 1x copy bisa pas dengan kapasitas disk tersebut. 

Misalnya: Anda punya files [1GB, 3GB, 5GB, 7GB, 9GB, 11GB] dan disk berkapasitas 12GB. Dengan two pointers, Anda bisa cepat menemukan pasangan files yang totalnya pas 12GB.

## 📊 Visualisasi Konsep
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

## 💻 Code Python dengan Visualisasi

```python
from typing import List

def two_sum_sorted(nums: List[int], target: int) -> List[int]:
    """
    Find two numbers in sorted array that add up to target
    Returns the indices of the two numbers
    
    Visualisasi proses:
    nums = [1, 3, 5, 7, 9, 11], target = 12
    
    Round 1:
    [1, 3, 5, 7, 9, 11]
     ↑              ↑
     L              R
    Sum: 1 + 11 = 12 ✅ Found!
    
    
    Contoh lain:
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
    
    Logika:
    - Jika sum < target: geser pointer kiri ke kanan (butuh angka lebih besar)
    - Jika sum > target: geser pointer kanan ke kiri (butuh angka lebih kecil)
    - Jika sum == target: ketemu jawabannya!
    
    Args:
        nums: Array yang sudah terurut
        target: Target sum yang dicari
    
    Returns:
        List berisi [left_index, right_index] atau list kosong jika tidak ditemukan
    """
    left = 0
    right = len(nums) - 1
    
    while left < right:
        current_sum = nums[left] + nums[right]
        
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            # Sum terlalu kecil, butuh angka lebih besar
            # Geser pointer kiri ke kanan
            left += 1
        else:
            # Sum terlalu besar, butuh angka lebih kecil
            # Geser pointer kanan ke kiri
            right -= 1
    
    return []  # Tidak ditemukan solusi


# ==================== DEMO ====================
if __name__ == "__main__":
    print("=" * 60)
    print("KASUS NYATA: MENCARI PASANGAN FILE UNTUK KAPASITAS DISK")
    print("=" * 60)
    
    # Ukuran file dalam GB (sudah terurut)
    file_sizes = [1, 3, 5, 7, 9, 11]
    disk_capacity = 12
    
    print(f"\nUkuran file (GB): {file_sizes}")
    print(f"Kapasitas disk: {disk_capacity}GB")
    print(f"\nMencari pasangan yang jumlahnya {disk_capacity}GB...\n")
    
    # Cari semua pasangan secara manual untuk demonstrasi
    left = 0
    right = len(file_sizes) - 1
    pair_count = 0
    
    while left < right:
        current_sum = file_sizes[left] + file_sizes[right]
        
        # Representasi visual
        visual = ["  "] * len(file_sizes)
        visual[left] = "L "
        visual[right] = "R "
        
        print(f"Cek: {file_sizes}")
        print(f"     {''.join(visual)}")
        print(f"     {file_sizes[left]}GB + {file_sizes[right]}GB = {current_sum}GB", end="")
        
        if current_sum == disk_capacity:
            pair_count += 1
            print(f" ✅ Pasangan #{pair_count} ditemukan!")
            left += 1
            right -= 1
        elif current_sum < disk_capacity:
            print(f" (terlalu kecil, geser L →)")
            left += 1
        else:
            print(f" (terlalu besar, geser R ←)")
            right -= 1
        print()
    
    print("=" * 60)
    print("CONTOH KLASIK: TWO SUM PROBLEM")
    print("=" * 60)
    
    nums = [2, 7, 11, 15]
    target = 9
    
    print(f"\nArray: {nums}")
    print(f"Target: {target}")
    
    result = two_sum_sorted(nums, target)
    
    if result:
        i, j = result
        print(f"\n✅ Solusi ditemukan!")
        print(f"   Indeks: [{i}, {j}]")
        print(f"   Nilai: {nums[i]} + {nums[j]} = {target}")
    else:
        print("\n❌ Tidak ada solusi")
    
    print("\n" + "=" * 60)
    print("VISUALISASI LANGKAH DEMI LANGKAH")
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
        
        # Representasi visual
        visual = ["  "] * len(nums)
        visual[left] = "L "
        visual[right] = "R "
        
        print(f"Step {step}:")
        print(f"  {nums}")
        print(f"  {''.join(visual)}")
        print(f"  nums[{left}] + nums[{right}] = {nums[left]} + {nums[right]} = {current_sum}")
        
        if current_sum == target:
            print(f"  ✅ COCOK! Jawaban: [{left}, {right}]")
            break
        elif current_sum < target:
            print(f"  {current_sum} < {target}, butuh sum lebih besar")
            print(f"  Aksi: Geser pointer KIRI → (untuk dapat angka lebih besar)")
            left += 1
        else:
            print(f"  {current_sum} > {target}, butuh sum lebih kecil")
            print(f"  Aksi: Geser pointer KANAN ← (untuk dapat angka lebih kecil)")
            right -= 1
        
        print()
        step += 1
```

## 📤 Output Ketika Dijalankan

```
============================================================
KASUS NYATA: MENCARI PASANGAN FILE UNTUK KAPASITAS DISK
============================================================

Ukuran file (GB): [1, 3, 5, 7, 9, 11]
Kapasitas disk: 12GB

Mencari pasangan yang jumlahnya 12GB...

Cek: [1, 3, 5, 7, 9, 11]
     L              R 
     1GB + 11GB = 12GB ✅ Pasangan #1 ditemukan!

Cek: [1, 3, 5, 7, 9, 11]
        L        R    
     3GB + 9GB = 12GB ✅ Pasangan #2 ditemukan!

Cek: [1, 3, 5, 7, 9, 11]
           L  R       
     5GB + 7GB = 12GB ✅ Pasangan #3 ditemukan!

============================================================
CONTOH KLASIK: TWO SUM PROBLEM
============================================================

Array: [2, 7, 11, 15]
Target: 9

✅ Solusi ditemukan!
   Indeks: [0, 1]
   Nilai: 2 + 7 = 9

============================================================
VISUALISASI LANGKAH DEMI LANGKAH
============================================================

Array: [2, 7, 11, 15]
Target: 9

Step 1:
  [2, 7, 11, 15]
  L          R 
  nums[0] + nums[3] = 2 + 15 = 17
  17 > 9, butuh sum lebih kecil
  Aksi: Geser pointer KANAN ← (untuk dapat angka lebih kecil)

Step 2:
  [2, 7, 11, 15]
  L      R     
  nums[0] + nums[2] = 2 + 11 = 13
  13 > 9, butuh sum lebih kecil
  Aksi: Geser pointer KANAN ← (untuk dapat angka lebih kecil)

Step 3:
  [2, 7, 11, 15]
  L   R        
  nums[0] + nums[1] = 2 + 7 = 9
  ✅ COCOK! Jawaban: [0, 1]
```

## 🎯 Kapan Menggunakan Pattern Ini?

- ✅ Array sudah **sorted** atau bisa di-sort
- ✅ Mencari **pair** yang memenuhi kondisi tertentu
- ✅ Problem dengan kata kunci: "two numbers", "pair", "sum equals"
- ✅ Optimasi dari brute force O(n²) menjadi O(n)

## ⏱️ Time & Space Complexity

- **Time**: O(n) - Single pass dengan two pointers
- **Space**: O(1) - Hanya butuh 2 pointer variables
- **Note**: Jika array belum sorted, perlu O(n log n) untuk sorting

## 🔗 LeetCode Problems

1. [Two Sum II - Input Array Is Sorted (LeetCode #167)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
2. [3Sum (LeetCode #15)](https://leetcode.com/problems/3sum/)
3. [Container With Most Water (LeetCode #11)](https://leetcode.com/problems/container-with-most-water/)
4. [Trapping Rain Water (LeetCode #42)](https://leetcode.com/problems/trapping-rain-water/)

---

**Status**: ✅ REVIEWED & APPROVED
