

---

## 🎯 PROBLEM: Merge Sorted Array (Revisited)

> You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.  
> Merge `nums2` into `nums1` so that `nums1` is sorted in non-decreasing order.  
> The final sorted array should be stored inside `nums1`.  
> Note: `nums1` has a size of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored.

**Example**:  
Input: `nums1 = [1,2,3,0,0,0]`, `m = 3`, `nums2 = [2,5,6]`, `n = 3`  
Output: `nums1 = [1,2,2,3,5,6]`

---

## 🧠 WHY THIS PROBLEM MATTERS

This isn’t just a LeetCode exercise — it’s the **core of merge sort**, **database index compaction**, and **external sorting** in big data systems.

> 💡 **Real-World**: When a database merges two sorted index segments during compaction, it uses **exactly this algorithm** — backward merge to avoid overwriting.

---

## 🔄 KEY INSIGHT (REVISITED)

- **Forward merge** would overwrite `nums1` elements.
- **Backward merge** uses the **buffer at the end** of `nums1` — safe and efficient.
- **Three pointers**:
  - `i` → last valid in `nums1` (`m-1`)
  - `j` → last in `nums2` (`n-1`)
  - `k` → last position in `nums1` (`m+n-1`)

---

## ✍️ FINAL CODE — PRODUCTION-GRADE

```java
/**
 * Merges nums2 into nums1 in sorted order.
 * Uses backward merge to avoid overwriting — O(m+n) time, O(1) space.
 * Critical for database index merging and external sort.
 */
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1;      // Last valid in nums1
    int j = n - 1;      // Last in nums2
    int k = m + n - 1;  // Last position in nums1

    // Merge from end — largest first
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j]) {
            nums1[k--] = nums1[i--];
        } else {
            nums1[k--] = nums2[j--];
        }
    }

    // Copy remaining from nums2 (nums1 already in place)
    while (j >= 0) {
        nums1[k--] = nums2[j--];
    }
}
```

---

## 🔍 WHY NO NEED TO COPY REMAINING `nums1`?

Because:
- If `j < 0`, all `nums2` elements are merged.
- Remaining `nums1` elements are **already in place** at the front.
- No overwriting occurred — they’re safe.

✅ This is the **elegant efficiency** of backward merge.

---

