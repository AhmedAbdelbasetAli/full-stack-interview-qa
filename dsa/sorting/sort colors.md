---

## 🎯 PROBLEM: Sort Colors (Revisited)

> Given an array `nums` with `n` objects colored red, white, or blue, represented by integers `0`, `1`, and `2` respectively, **sort them in-place** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.  
> You must solve this without using the library’s sort function.

**Example**:  
Input: `nums = [2,0,2,1,1,0]`  
Output: `[0,0,1,1,2,2]`

---

## 🧠 WHY THIS PROBLEM MATTERS

This isn’t just a LeetCode exercise — it’s the **foundation of 3-way quicksort**, **categorical data preprocessing**, and **in-place partitioning** in embedded systems.

> 💡 **Real-World**:  
> - **Database systems**: Partition rows by category (e.g., status: 0=pending, 1=active, 2=archived).  
> - **Embedded systems**: Sort sensor data (0=low, 1=medium, 2=high) with **zero extra memory**.  
> - **Quicksort optimization**: 3-way partition handles duplicates efficiently.

---

## 🔄 KEY INSIGHT (REVISITED)

- **Three pointers**:
  - `low` → right boundary of `0`s.
  - `mid` → current element being processed.
  - `high` → left boundary of `2`s.
- **Rules**:
  - If `nums[mid] == 0` → swap with `low`, increment both.
  - If `nums[mid] == 1` → leave it, increment `mid`.
  - If `nums[mid] == 2` → swap with `high`, decrement `high` (**don’t increment `mid`** — new element needs processing).

---

## ✍️ FINAL CODE — PRODUCTION-GRADE

```java
/**
 * Sorts array of 0s, 1s, 2s in-place using Dutch National Flag algorithm.
 * O(n) time, O(1) space — critical for embedded systems and 3-way quicksort.
 */
public void sortColors(int[] nums) {
    int low = 0;      // Right boundary of 0s
    int mid = 0;      // Current element
    int high = nums.length - 1; // Left boundary of 2s

    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums, low++, mid++);
        } else if (nums[mid] == 1) {
            mid++; // 1 is in correct place
        } else { // nums[mid] == 2
            swap(nums, mid, high--);
            // mid not incremented — new element at mid needs processing
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

---

## 🔍 WHY NOT INCREMENT `mid` WHEN SWAPPING WITH `high`?

Because:
- After swapping `nums[mid]` with `nums[high]`, the new `nums[mid]` came from the unprocessed right section.
- It could be `0`, `1`, or `2` — so we **must reprocess it** in the next iteration.

✅ This is the **subtle but critical detail** that makes the algorithm work.

---

## 🧪 EDGE CASE: `[2,0,2,1,1,0]`

| Step | low | mid | high | nums               | Action          |
|------|-----|-----|------|--------------------|-----------------|
| 0    | 0   | 0   | 5    | [2,0,2,1,1,0]      | swap(0,5) → [0,0,2,1,1,2] |
| 1    | 1   | 1   | 4    | [0,0,2,1,1,2]      | mid++ → 2       |
| 2    | 1   | 2   | 4    | [0,0,2,1,1,2]      | swap(2,4) → [0,0,1,1,2,2] |
| 3    | 1   | 2   | 3    | [0,0,1,1,2,2]      | mid++ → 3       |
| 4    | 1   | 3   | 3    | [0,0,1,1,2,2]      | mid++ → 4       |
| 5    | 1   | 4   | 3    | break (mid > high) | ✅ Done         |

---



What’s next?
