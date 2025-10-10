

---

# 📊 SORTING PROBLEMS — MASTER LIST

---

## 🔹 **I. Basic Sorting & Custom Comparators**

> Use when: You need to sort by a custom rule, not just natural order.

### 1. **Valid Anagram** (#242)  
> Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`.  
✅ **Technique**: Sort both strings and compare.  
💡 Real-world: Text processing, DNA sequence matching.

### 2. **Meeting Rooms** (#252)  
> Given an array of meeting time intervals, determine if a person could attend all meetings.  
✅ **Technique**: Sort by start time, check overlaps.  
💡 Real-world: Calendar systems, resource scheduling.

### 3. **Custom Sort String** (#791)  
> Given `order` and `s`, sort `s` so chars appear in `order` first, then others.  
✅ **Technique**: Custom comparator or count + rebuild.  
💡 Real-world: Priority-based sorting (e.g., VIP users first).

---

## 🔹 **II. Merge & Partition Patterns**

> Use when: You need to merge, partition, or rearrange in-place.

### 4. **Merge Sorted Array** (#88)  
> Merge `nums2` into `nums1` in sorted order (in-place).  
✅ **Technique**: Backward merge (from end).  
💡 Real-world: Database index merging, external sort.

### 5. **Sort Colors** (#75)  
> Sort array of 0s, 1s, 2s in-place (Dutch National Flag).  
✅ **Technique**: Three-way partition (low/mid/high pointers).  
💡 Real-world: Categorical data preprocessing.

### 6. **Wiggle Sort** (#280)  
> Reorder array so `nums[0] <= nums[1] >= nums[2] <= ...`  
✅ **Technique**: Sort + swap, or one-pass.  
💡 Real-world: Signal processing, UI layout.

---

## 🔹 **III. Greedy + Sorting**

> Use when: Sorting enables greedy choice for optimization.

### 7. **Assign Cookies** (#455)  
> Assign cookies to children to maximize content children.  
✅ **Technique**: Sort both arrays, greedy assignment.  
💡 Real-world: Resource allocation, load balancing.

### 8. **Non-overlapping Intervals** (#435)  
> Find minimum number of intervals to remove to make rest non-overlapping.  
✅ **Technique**: Sort by end time, greedy keep.  
💡 Real-world: Meeting room allocation, ad scheduling.

### 9. **Queue Reconstruction by Height** (#406)  
> Reconstruct queue from `[h, k]` pairs (h=height, k=people in front ≥ h).  
✅ **Technique**: Sort by height desc, k asc → insert at k.  
💡 Real-world: Photo lineup, priority queues.

---

## 🔹 **IV. Advanced: Kth Element & Partial Sort**

> Use when: You don’t need full sort — just top-K or Kth element.

### 10. **Kth Largest Element in an Array** (#215)  
> Find the kth largest element.  
✅ **Technique**: QuickSelect (O(n) avg) or Heap (O(n log k)).  
💡 Real-world: Leaderboards, analytics (top-K).

### 11. **Top K Frequent Elements** (#347)  
> Return k most frequent elements.  
✅ **Technique**: HashMap + Min-Heap of size k.  
💡 Real-world: Trending topics, recommendation engines.

### 12. **Find K Closest Elements** (#658)  
> Find k closest integers to `x` in sorted array.  
✅ **Technique**: Binary search + two pointers, or heap.  
💡 Real-world: Search suggestions, nearest neighbors.

---

## 🔹 **V. Specialized Sorting Algorithms**

> Use when: You need stable sort, O(n) time, or handle constraints.

### 13. **Sort Array By Parity** (#905)  
> Move all evens before odds.  
✅ **Technique**: Two pointers (collision or read/write).  
💡 Real-world: Data partitioning, preprocessing.

### 14. **Largest Number** (#179)  
> Given numbers, arrange to form largest number.  
✅ **Technique**: Custom comparator (`a+b > b+a`).  
💡 Real-world: Financial systems, ID generation.

### 15. **H-Index** (#274)  
> Given citations, compute h-index (h papers with ≥ h citations).  
✅ **Technique**: Sort desc, find max `i` where `citations[i] >= i+1`.  
💡 Real-world: Academic ranking, impact metrics.

---

# 🧠 KEY SORTING PATTERNS TO MASTER

| Pattern                  | When to Use                          | Problems                     |
|--------------------------|--------------------------------------|------------------------------|
| **Custom Comparator**    | Sort by non-natural order            | #791, #406, #179            |
| **Sort + Two Pointers**  | Merge, 3Sum, intervals               | #88, #15, #56               |
| **Sort + Greedy**        | Scheduling, assignment               | #435, #455                  |
| **Partial Sort (Heap)**  | Top-K, Kth element                   | #215, #347                  |
| **In-Place Partition**   | Rearrange without extra space        | #75, #905                   |
| **Backward Merge**       | Merge into buffer                    | #88                         |

---

# 🚀 HOW TO PRACTICE

1. **Start with Basic**: #242, #252, #88  
2. **Master Greedy + Sort**: #435, #455, #406  
3. **Tackle Advanced**: #215, #347, #179  
4. **Time yourself**: Aim for < 15 mins per Medium.

---

# 💡 PRO TIP: Know Your Java Sorting

- **`Arrays.sort(primitives)`** → Dual-Pivot QuickSort (not stable).
- **`Arrays.sort(objects)`** → TimSort (stable, adaptive).
- **Custom sort**:
  ```java
  Arrays.sort(arr, (a, b) -> b - a); // desc
  Collections.sort(list, Comparator.comparingInt(x -> x));
  ```

---
