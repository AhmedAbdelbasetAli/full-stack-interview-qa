---

## 🎯 PROBLEM: Minimum Size Subarray Sum

> Given an array of positive integers `nums` and a positive integer `target`, return the **minimal length** of a contiguous subarray `[nums[i], nums[i+1], ..., nums[j]]` of which the sum is **greater than or equal to target**.  
> If there is no such subarray, return `0`.

**Example**:  
Input: `target = 7`, `nums = [2,3,1,2,4,3]`  
Output: `2` → because `[4,3]` is the smallest subarray with sum ≥ 7.

---

## 🧠 STEP 1: Understand the Problem

- Array has **positive integers only** → sliding window works.
- Find **smallest length** of contiguous subarray with `sum >= target`.
- If no such subarray → return `0`.

---

## 🧩 STEP 2: Why Sliding Window?

Because:

- Array has **positive numbers** → expanding window increases sum, shrinking decreases sum.
- Use **two pointers** (`left`, `right`) to maintain a window.
- Expand `right` to increase sum, shrink `left` when sum ≥ target.
- Track minimal window size.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class MinSubArrayLen {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int sum = 0;
        int minLength = Integer.MAX_VALUE;

        for (int right = 0; right < nums.length; right++) {
            sum += nums[right]; // Expand window

            // Shrink window while sum >= target
            while (sum >= target) {
                minLength = Math.min(minLength, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }

        return minLength == Integer.MAX_VALUE ? 0 : minLength;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `target = 7`, `nums = [2,3,1,2,4,3]`

| right | nums[right] | sum | window | minLength | action |
|-------|-------------|-----|--------|-----------|--------|
| 0     | 2           | 2   | [2]    | ∞         | expand |
| 1     | 3           | 5   | [2,3]  | ∞         | expand |
| 2     | 1           | 6   | [2,3,1]| ∞         | expand |
| 3     | 2           | 8   | [2,3,1,2] | 4      | shrink → sum=6 → stop |
| 4     | 4           | 10  | [3,1,2,4] | 4 → 4  | shrink → sum=8 → min=4 → sum=7 → min=3 → sum=6 → stop |
| 5     | 3           | 9   | [2,4,3] | 3 → 2   | shrink → sum=7 → min=2 → sum=3 → stop |

✅ Final `minLength = 2`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element added/removed once.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Real-time analytics (min window with sum ≥ threshold), network monitoring (min burst size), financial systems (min transaction window).
- **Interview pattern**: **Sliding Window for Min/Max Subarray** — appears in Longest Substring Without Repeating, Subarray Product Less Than K.
- **Why companies ask this**: Tests if you can use sliding window on positive arrays, handle shrinking, track min/max.

> 💡 Uber: “Min ride duration with fare ≥ $10.”  
> 💡 Finance: “Min transaction window with sum ≥ $1000.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using brute force** → O(n²) → too slow.

```java
// 🚫 WRONG
for (int i = 0; i < n; i++) {
    int sum = 0;
    for (int j = i; j < n; j++) {
        sum += nums[j];
        if (sum >= target) {
            minLength = Math.min(minLength, j - i + 1);
            break;
        }
    }
}
```

- **Not shrinking window** → misses smaller windows.

- **Forgetting to handle no-solution case** → return 0.

- **Using `if` instead of `while`** → window not fully shrunk.

```java
// 🚫 WRONG
if (sum >= target) { ... }

// ✅ CORRECT
while (sum >= target) { ... }
```

---

## ✅ STEP 8: Edge Cases to Test

```java
minSubArrayLen(7, [2,3,1,2,4,3]) → 2
minSubArrayLen(4, [1,4,4]) → 1
minSubArrayLen(11, [1,1,1,1,1,1,1,1]) → 0
minSubArrayLen(1, [1]) → 1
minSubArrayLen(100, [1,2,3]) → 0
```

---

## 🧠 BONUS: Why Sliding Window Works

Because the array has **positive integers**:

- Expanding window → sum increases.
- Shrinking window → sum decreases.
- So once sum ≥ target, we can try to shrink to find smaller valid window.

→ This **monotonic property** is essential for sliding window.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds minimal length of contiguous subarray with sum >= target.
 * Uses sliding window — O(n) time, O(1) space.
 * @param target target sum
 * @param nums array of positive integers
 * @return minimal length, or 0 if no such subarray
 */
public int minSubArrayLen(int target, int[] nums) {
    int left = 0;
    int sum = 0;
    int minLength = Integer.MAX_VALUE;

    // Expand window with right pointer
    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];

        // Shrink window from left while sum >= target
        while (sum >= target) {
            minLength = Math.min(minLength, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }

    return minLength == Integer.MAX_VALUE ? 0 : minLength;
}
```

---
