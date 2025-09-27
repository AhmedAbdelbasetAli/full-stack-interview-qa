---

## 🎯 PROBLEM: Move Zeroes

> Given an integer array `nums`, move all `0`s to the end while maintaining the relative order of the non-zero elements.  
> You must do this in-place without making a copy of the array.

**Example**:  
Input: `nums = [0,1,0,3,12]`  
Output: `nums = [1,3,12,0,0]`

---

## 🧠 STEP 1: Understand the Problem

- Move all zeros to the end.
- **Preserve order** of non-zero elements.
- **In-place** — no extra array.
- Must be **O(n) time, O(1) space**.

---

## 🧩 STEP 2: Why Read/Write Pointers?

Because:

- **Read pointer** scans entire array.
- **Write pointer** tracks where to place next non-zero element.
- After first pass, fill remaining positions with zeros.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class MoveZeroes {
    public void moveZeroes(int[] nums) {
        int writeIndex = 0; // Tracks where to write next non-zero

        // First pass: move all non-zeroes to front
        for (int readIndex = 0; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != 0) {
                nums[writeIndex] = nums[readIndex];
                writeIndex++;
            }
        }

        // Second pass: fill rest with zeros
        while (writeIndex < nums.length) {
            nums[writeIndex] = 0;
            writeIndex++;
        }
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [0,1,0,3,12]`

### First Pass:

| readIndex | nums[readIndex] | != 0? | writeIndex | nums becomes       |
|-----------|------------------|-------|------------|--------------------|
| 0         | 0                | ❌    | 0          | [0,1,0,3,12]       |
| 1         | 1                | ✅    | 1          | [1,1,0,3,12]       |
| 2         | 0                | ❌    | 1          | [1,1,0,3,12]       |
| 3         | 3                | ✅    | 2          | [1,3,0,3,12]       |
| 4         | 12               | ✅    | 3          | [1,3,12,3,12]      |

### Second Pass:

Fill from index 3 to 4 with zeros → `[1,3,12,0,0]` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — two passes (but still O(n)).
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Data preprocessing (remove missing values), game dev (remove dead entities), signal processing (filter zeros).
- **Interview pattern**: **Read/Write Pointers + Fill** — appears in Remove Element, Remove Duplicates.
- **Why companies ask this**: Tests if you can do in-place compaction, preserve order, handle edge cases.

> 💡 Uber: “Move canceled rides (0) to end of batch list.”  
> 💡 Game Dev: “Move dead enemies (0 health) to end of entity list.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Swapping zeros with non-zeros** → O(n²) or breaks order.

```java
// 🚫 WRONG — breaks order
for (int i = 0; i < nums.length; i++) {
    if (nums[i] == 0) {
        // swap with next non-zero
    }
}
```

- **Not filling zeros at the end** → leaves garbage values.

- **Using extra array** → violates in-place constraint.

---

## ✅ STEP 8: Edge Cases to Test

```java
moveZeroes([0,1,0,3,12]) → [1,3,12,0,0]
moveZeroes([0]) → [0]
moveZeroes([1,2,3]) → [1,2,3]
moveZeroes([0,0,1]) → [1,0,0]
moveZeroes([1,0,0,0,2]) → [1,2,0,0,0]
```

---

## 🧠 BONUS: One-Pass Alternative (Swap)

You can also do it in one pass with swapping — but it’s **less efficient** (more writes):

```java
public void moveZeroes(int[] nums) {
    int lastNonZero = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != 0) {
            swap(nums, i, lastNonZero);
            lastNonZero++;
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

→ But the **two-pass method** is **more cache-friendly** (fewer writes) and **clearer**.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Moves all zeros to the end of the array while preserving order of non-zeros.
 * Uses read/write pointers + fill — O(n) time, O(1) space.
 * @param nums array of integers
 */
public void moveZeroes(int[] nums) {
    int writeIndex = 0;

    // Pass 1: Compact non-zero elements to front
    for (int readIndex = 0; readIndex < nums.length; readIndex++) {
        if (nums[readIndex] != 0) {
            nums[writeIndex] = nums[readIndex];
            writeIndex++;
        }
    }

    // Pass 2: Fill remaining positions with zeros
    while (writeIndex < nums.length) {
        nums[writeIndex] = 0;
        writeIndex++;
    }
}
```

---

