

---

## 🎯 PROBLEM: Sort Colors (Dutch National Flag)

> Given an array `nums` with `n` objects colored red, white, or blue, represented by integers `0`, `1`, and `2` respectively, **sort them in-place** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.  
> You must solve this without using the library’s sort function.

**Example**:  
Input: `nums = [2,0,2,1,1,0]`  
Output: `[0,0,1,1,2,2]`

---

## 🧠 STEP 1: Understand the Problem

- Array contains only `0`, `1`, `2`.
- Sort in-place → no extra array.
- Order: `0` (red), `1` (white), `2` (blue).
- Must be **one pass** → O(n) time, O(1) space.

---

## 🧩 STEP 2: Why Three Pointers (Dutch National Flag)?

Because:

- We need to partition array into **three sections**: `0`s, `1`s, `2`s.
- Use three pointers:
  - `low` → right boundary of `0`s.
  - `mid` → current element being processed.
  - `high` → left boundary of `2`s.
- Traverse with `mid` — swap to move `0`s to left, `2`s to right.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class SortColors {
    public void sortColors(int[] nums) {
        int low = 0;      // right boundary of 0s
        int mid = 0;      // current element
        int high = nums.length - 1; // left boundary of 2s

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++; // leave 1 in place
            } else { // nums[mid] == 2
                swap(nums, mid, high);
                high--; // mid not incremented — new element at mid needs processing
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [2,0,2,1,1,0]`

### Step 0: Initialize

```
low=0, mid=0, high=5
nums = [2,0,2,1,1,0]
```

### Step 1: mid=0, nums[mid]=2 → swap with high

```
swap(0,5) → [0,0,2,1,1,2]
high-- → high=4
mid not incremented → mid=0
```

### Step 2: mid=0, nums[mid]=0 → swap with low

```
swap(0,0) → [0,0,2,1,1,2]
low++ → low=1
mid++ → mid=1
```

### Step 3: mid=1, nums[mid]=0 → swap with low

```
swap(1,1) → [0,0,2,1,1,2]
low++ → low=2
mid++ → mid=2
```

### Step 4: mid=2, nums[mid]=2 → swap with high

```
swap(2,4) → [0,0,1,1,2,2]
high-- → high=3
mid not incremented → mid=2
```

### Step 5: mid=2, nums[mid]=1 → mid++

```
mid++ → mid=3
```

### Step 6: mid=3, nums[mid]=1 → mid++

```
mid++ → mid=4
```

### Step 7: mid=4, nums[mid]=2 → swap with high

```
swap(4,3) → [0,0,1,1,2,2] (no change)
high-- → high=2
mid not incremented → mid=4
```

### Step 8: mid=4, high=2 → mid > high → break

✅ Final: `[0,0,1,1,2,2]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element processed once.
- **Space**: O(1) — only three pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Data preprocessing (categorical data), quicksort (3-way partition), embedded systems (in-place sorting).
- **Interview pattern**: **Three Pointers (Dutch National Flag)** — appears in Sort Array By Parity, Wiggle Sort.
- **Why companies ask this**: Tests if you can partition in-place, handle multiple pointers, avoid extra space.

> 💡 Uber: “Sort ride types (0=UberX, 1=Pool, 2=Black) for batch processing.”  
> 💡 Android: “Sort UI elements by type (0=text, 1=image, 2=button) for rendering.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not handling `mid` correctly when swapping with `high`** → must not increment `mid` because new element at `mid` is unprocessed.

```java
// 🚫 WRONG
if (nums[mid] == 2) {
    swap(nums, mid, high);
    high--;
    mid++; // NO! New element at mid not processed
}

// ✅ CORRECT
if (nums[mid] == 2) {
    swap(nums, mid, high);
    high--; // mid stays — process new element
}
```

- **Using `mid < high`** → should be `mid <= high`.

- **Forgetting to swap** → just moving pointers.

---

## ✅ STEP 8: Edge Cases to Test

```java
sortColors([2,0,2,1,1,0]) → [0,0,1,1,2,2]
sortColors([2,0,1]) → [0,1,2]
sortColors([0]) → [0]
sortColors([1,1,1]) → [1,1,1]
sortColors([2,2,0,0]) → [0,0,2,2]
```

---

## 🧠 BONUS: Why Three Pointers Works

- `low` tracks where next `0` should go.
- `high` tracks where next `2` should go.
- `mid` scans and swaps:
  - If `0` → swap with `low`, both move forward.
  - If `1` → leave it, `mid` moves forward.
  - If `2` → swap with `high`, only `high` moves back (because new element at `mid` is unknown).

→ This ensures all `0`s are before `low`, all `2`s after `high`, and `1`s in between.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Sorts array of 0s, 1s, 2s in-place using Dutch National Flag algorithm.
 * O(n) time, O(1) space.
 * @param nums array with values 0,1,2
 */
public void sortColors(int[] nums) {
    int low = 0;      // right boundary of 0s
    int mid = 0;      // current element being processed
    int high = nums.length - 1; // left boundary of 2s

    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums, low, mid);
            low++;
            mid++;
        } else if (nums[mid] == 1) {
            mid++; // 1 is in correct place
        } else { // nums[mid] == 2
            swap(nums, mid, high);
            high--; // mid not incremented — new element at mid needs processing
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
