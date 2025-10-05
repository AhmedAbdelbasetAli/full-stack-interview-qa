
---

## 🎯 PROBLEM: Subarray Product Less Than K

> Given an array of positive integers `nums` and an integer `k`, return the number of contiguous subarrays where the **product of all elements** is **strictly less than k**.

**Example**:  
Input: `nums = [10,5,2,6]`, `k = 100`  
Output: `8`  
→ Valid subarrays: `[10], [5], [2], [6], [5,2], [2,6], [5,2,6], [10,5]`

---

## 🧠 STEP 1: Understand the Problem

- Array has **positive integers only** → product increases as we expand window.
- Count **all contiguous subarrays** with `product < k`.
- Subarrays are **contiguous** and **non-empty**.

---

## 🧩 STEP 2: Why Sliding Window?

Because:

- Array has **positive numbers** → expanding window increases product, shrinking decreases product.
- Use **two pointers** (`left`, `right`) to maintain a window with `product < k`.
- For each `right`, the number of new valid subarrays ending at `right` is `right - left + 1`.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class NumSubarrayProductLessThanK {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k <= 1) {
            return 0; // product of positive integers >= 1, so no subarray < k
        }

        int left = 0;
        long product = 1; // use long to avoid overflow
        int count = 0;

        for (int right = 0; right < nums.length; right++) {
            product *= nums[right]; // Expand window

            // Shrink window while product >= k
            while (product >= k) {
                product /= nums[left];
                left++;
            }

            // All subarrays ending at 'right' are valid: [left..right], [left+1..right], ..., [right]
            count += right - left + 1;
        }

        return count;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [10,5,2,6]`, `k = 100`

| right | nums[right] | product | window | count added | total count |
|-------|-------------|---------|--------|-------------|-------------|
| 0     | 10          | 10      | [10]   | 1           | 1           |
| 1     | 5           | 50      | [10,5] | 2           | 3           |
| 2     | 2           | 100     | → shrink → product=10, left=1 → [5,2] | 2 | 5 |
| 3     | 6           | 60      | [5,2,6]| 3           | 8           |

✅ Output: `8`

Valid subarrays:
- Ending at 0: `[10]`
- Ending at 1: `[5]`, `[10,5]`
- Ending at 2: `[2]`, `[5,2]`
- Ending at 3: `[6]`, `[2,6]`, `[5,2,6]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element added/removed once.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Financial risk (max product of returns < threshold), IoT (sensor data bursts), real-time analytics.
- **Interview pattern**: **Sliding Window for Counting Subarrays** — appears in Minimum Size Subarray Sum, Longest Substring Without Repeating.
- **Why companies ask this**: Tests if you can count subarrays efficiently, handle product overflow, use sliding window on positive arrays.

> 💡 Finance: “Count time windows where product of daily returns < risk threshold.”  
> 💡 IoT: “Count sensor bursts where product of readings < safe limit.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not handling `k <= 1`** → since all numbers are positive, product ≥ 1 → no subarray < k.

- **Using `int` for product** → overflow → use `long`.

```java
// 🚫 WRONG
int product = 1;

// ✅ CORRECT
long product = 1;
```

- **Counting incorrectly** → must add `right - left + 1` (number of subarrays ending at `right`).

- **Using `if` instead of `while`** → window not fully shrunk.

```java
// 🚫 WRONG
if (product >= k) { ... }

// ✅ CORRECT
while (product >= k) { ... }
```

---

## ✅ STEP 8: Edge Cases to Test

```java
numSubarrayProductLessThanK([10,5,2,6], 100) → 8
numSubarrayProductLessThanK([1,2,3], 0) → 0
numSubarrayProductLessThanK([1,1,1], 2) → 6
numSubarrayProductLessThanK([1,2,3], 1) → 0
numSubarrayProductLessThanK([2], 3) → 1
```

---

## 🧠 BONUS: Why `count += right - left + 1`?

Because:

- For window `[left, right]`, the valid subarrays **ending at `right`** are:
  - `[right]`
  - `[right-1, right]`
  - ...
  - `[left, left+1, ..., right]`
- Total = `right - left + 1`

→ This counts all new valid subarrays in O(1).

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Counts number of contiguous subarrays with product < k.
 * Uses sliding window — O(n) time, O(1) space.
 * @param nums array of positive integers
 * @param k threshold (exclusive)
 * @return number of valid subarrays
 */
public int numSubarrayProductLessThanK(int[] nums, int k) {
    // Edge case: k <= 1 → no subarray possible (product >= 1)
    if (k <= 1) {
        return 0;
    }

    int left = 0;
    long product = 1; // Use long to prevent overflow
    int count = 0;

    // Expand window with right pointer
    for (int right = 0; right < nums.length; right++) {
        product *= nums[right];

        // Shrink window from left while product >= k
        while (product >= k) {
            product /= nums[left];
            left++;
        }

        // Add count of subarrays ending at 'right'
        count += right - left + 1;
    }

    return count;
}
```

---
