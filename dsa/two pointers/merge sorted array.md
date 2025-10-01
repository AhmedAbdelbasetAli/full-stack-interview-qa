---

## 🎯 PROBLEM: Merge Sorted Array

> You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.  
> Merge `nums2` into `nums1` so that `nums1` is sorted in non-decreasing order.  
> The final sorted array should be stored inside `nums1`.  
> Note: `nums1` has a size of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored.

**Example**:  
Input: `nums1 = [1,2,3,0,0,0]`, `m = 3`, `nums2 = [2,5,6]`, `n = 3`  
Output: `nums1 = [1,2,2,3,5,6]`

---

## 🧠 STEP 1: Understand the Problem

- `nums1` has size `m + n` — first `m` elements are valid, last `n` are zeros (buffer).
- `nums2` has `n` valid elements.
- Merge both into `nums1` in sorted order — **in-place**.
- **Cannot use extra array** — must use the buffer in `nums1`.

---

## 🧩 STEP 2: Why Merge from End (Backward)?

Because:

- If we merge from front, we’d **overwrite** elements in `nums1`.
- But `nums1` has a **buffer at the end** — so we can safely fill from the end.
- Use **three pointers**:
  - `i` → last valid element in `nums1` (`m-1`)
  - `j` → last element in `nums2` (`n-1`)
  - `k` → last position in `nums1` (`m+n-1`)

→ O(m + n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class MergeSortedArray {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;      // Last valid element in nums1
        int j = n - 1;      // Last element in nums2
        int k = m + n - 1;  // Last position in nums1

        // Merge from end to start
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) {
                nums1[k] = nums1[i];
                i--;
            } else {
                nums1[k] = nums2[j];
                j--;
            }
            k--;
        }

        // Copy remaining elements from nums2 (if any)
        while (j >= 0) {
            nums1[k] = nums2[j];
            j--;
            k--;
        }

        // No need to copy nums1 — already in place
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums1 = [1,2,3,0,0,0]`, `m=3`, `nums2 = [2,5,6]`, `n=3`

### Step 0: Initialize

```
i=2 (3), j=2 (6), k=5
nums1 = [1,2,3,0,0,0]
```

### Step 1: Compare 3 vs 6 → 6 > 3

```
nums1[5] = 6 → [1,2,3,0,0,6]
j=1, k=4
```

### Step 2: Compare 3 vs 5 → 5 > 3

```
nums1[4] = 5 → [1,2,3,0,5,6]
j=0, k=3
```

### Step 3: Compare 3 vs 2 → 3 > 2

```
nums1[3] = 3 → [1,2,3,3,5,6]
i=1, k=2
```

### Step 4: Compare 2 vs 2 → equal → pick nums2

```
nums1[2] = 2 → [1,2,2,3,5,6]
j=-1, k=1
```

### Step 5: j < 0 → break

✅ Output: `[1,2,2,3,5,6]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(m + n) — each element processed once.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Merge Sort, external sorting (large datasets), database index merging.
- **Interview pattern**: **Backward Merge with Buffer** — appears in Merge Intervals, Merge k Sorted Lists.
- **Why companies ask this**: Tests if you can avoid overwriting, use buffer space, handle pointers.

> 💡 Database: “Merge sorted index segments during compaction.”  
> 💡 Big Data: “Merge sorted chunks in external sort.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Merging from front** → overwrites `nums1` elements.

```java
// 🚫 WRONG — overwrites nums1
int i=0, j=0, k=0;
while (i < m && j < n) {
    if (nums1[i] <= nums2[j]) {
        nums1[k] = nums1[i]; // but nums1[i] might be overwritten later!
    }
}
```

- **Not copying remaining `nums2` elements** → misses tail of `nums2`.

- **Copying remaining `nums1` elements** → unnecessary (already in place).

---

## ✅ STEP 8: Edge Cases to Test

```java
merge([1,2,3,0,0,0], 3, [2,5,6], 3) → [1,2,2,3,5,6]
merge([1], 1, [], 0) → [1]
merge([0], 0, [1], 1) → [1]
merge([2,0], 1, [1], 1) → [1,2]
merge([4,5,6,0,0,0], 3, [1,2,3], 3) → [1,2,3,4,5,6]
```

---

## 🧠 BONUS: Why Merge from End?

Because:

- `nums1` has a **buffer at the end** — so we can safely write largest elements first.
- Largest element goes to `m+n-1`, second largest to `m+n-2`, etc.
- No risk of overwriting unprocessed elements in `nums1`.

→ This is the **standard technique** for in-place merging when buffer is available.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Merges nums2 into nums1 in sorted order.
 * Uses backward merge to avoid overwriting — O(m+n) time, O(1) space.
 * @param nums1 array of size m+n (first m valid, last n zeros)
 * @param m number of valid elements in nums1
 * @param nums2 array of size n
 * @param n number of elements in nums2
 */
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1;      // Last valid in nums1
    int j = n - 1;      // Last in nums2
    int k = m + n - 1;  // Last position in nums1

    // Merge from end — largest first
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j]) {
            nums1[k] = nums1[i];
            i--;
        } else {
            nums1[k] = nums2[j];
            j--;
        }
        k--;
    }

    // Copy remaining from nums2 (nums1 already in place)
    while (j >= 0) {
        nums1[k] = nums2[j];
        j--;
        k--;
    }
}
```

---
