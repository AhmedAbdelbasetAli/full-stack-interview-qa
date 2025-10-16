---

## 🎯 PROBLEM: Wiggle Sort

> Given an integer array `nums`, reorder it such that `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.

**Examples**:  
- Input: `nums = [3,5,2,1,6,4]`  
  Output: `[3,5,1,6,2,4]` (one possible valid answer)  
- Input: `nums = [6,6,5,6,3,8]`  
  Output: `[6,6,5,6,3,8]` → valid because `6<=6>=5<=6>=3<=8`

---

## 🧠 STEP 1: Understand the Problem

- The pattern is: **low, high, low, high, ...**
- **Adjacent elements must alternate**: `≤, ≥, ≤, ≥, ...`
- **Duplicates are allowed** — so `6 <= 6 >= 5` is valid.
- You can return **any valid arrangement**.

---

## 🧩 STEP 2: Why One-Pass Swap (Not Full Sort)?

Because:

- **Full sort + rearrange** would work but is **O(n log n)**.
- But we can do it in **O(n) time with one pass**:
  - For even `i`: ensure `nums[i] <= nums[i+1]`
  - For odd `i`: ensure `nums[i] >= nums[i+1]`
  - If not, **swap** the two elements.

→ This greedy swap **locally fixes** the wiggle condition without breaking previous ones.

✅ **One-pass swap is optimal**.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class WiggleSort {
    public void wiggleSort(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            if (i % 2 == 0) {
                // Even index: should be <= next
                if (nums[i] > nums[i + 1]) {
                    swap(nums, i, i + 1);
                }
            } else {
                // Odd index: should be >= next
                if (nums[i] < nums[i + 1]) {
                    swap(nums, i, i + 1);
                }
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

Input: `nums = [3,5,2,1,6,4]`

| i  | i%2 | nums[i] | nums[i+1] | Condition       | Action     | Array State       |
|----|-----|---------|-----------|-----------------|------------|-------------------|
| 0  | 0   | 3       | 5         | 3 <= 5 → ok     | none       | [3,5,2,1,6,4]     |
| 1  | 1   | 5       | 2         | 5 >= 2 → ok     | none       | [3,5,2,1,6,4]     |
| 2  | 0   | 2       | 1         | 2 <= 1? ❌      | swap       | [3,5,1,2,6,4]     |
| 3  | 1   | 2       | 6         | 2 >= 6? ❌      | swap       | [3,5,1,6,2,4]     |
| 4  | 0   | 2       | 4         | 2 <= 4 → ok     | none       | [3,5,1,6,2,4] ✅   |

✅ Final: `[3,5,1,6,2,4]` → valid wiggle.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — one pass.
- **Space**: O(1) — only swap.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Signal processing (zigzag encoding), UI layout (alternating card heights), data obfuscation.
- **Interview pattern**: **Greedy One-Pass Rearrangement** — appears in Wiggle Sort II, Pancake Sorting.
- **Why companies ask this**: Tests if you can solve with greedy swap, avoid full sort, handle edge cases.

> 💡 Graphics: “Zigzag scan in JPEG compression.”  
> 💡 UI: “Alternate card heights in feed for visual rhythm.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using full sort** → O(n log n) when O(n) is possible.

```java
// 🚫 Overkill
Arrays.sort(nums);
// then rearrange
```

- **Not handling duplicates** → `6 <= 6` is valid.

- **Swapping incorrectly** → must only swap adjacent pairs.

---

## ✅ STEP 8: Edge Cases to Test

```java
wiggleSort([3,5,2,1,6,4]) → [3,5,1,6,2,4]
wiggleSort([6,6,5,6,3,8]) → [6,6,5,6,3,8] (valid as-is)
wiggleSort([1]) → [1]
wiggleSort([1,2]) → [1,2]
wiggleSort([2,1]) → [1,2] (after swap)
wiggleSort([1,2,3,4,5]) → [1,3,2,5,4]
```

---

## 🧠 BONUS: Why One-Pass Swap Works

Because:

- When you fix position `i` and `i+1`, you **don’t break** the condition at `i-1` and `i`.
- For even `i`:
  - Before: `nums[i-1] >= nums[i]`
  - After swap: `nums[i]` becomes smaller → `nums[i-1] >= new nums[i]` still holds.
- For odd `i`:
  - Before: `nums[i-1] <= nums[i]`
  - After swap: `nums[i]` becomes larger → `nums[i-1] <= new nums[i]` still holds.

✅ **Local fix preserves global validity**.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Reorders array into wiggle pattern: nums[0] <= nums[1] >= nums[2] <= ...
 * Uses one-pass greedy swap — O(n) time, O(1) space.
 * @param nums array to wiggle sort
 */
public void wiggleSort(int[] nums) {
    for (int i = 0; i < nums.length - 1; i++) {
        if (i % 2 == 0) {
            // Even index: ensure nums[i] <= nums[i+1]
            if (nums[i] > nums[i + 1]) {
                swap(nums, i, i + 1);
            }
        } else {
            // Odd index: ensure nums[i] >= nums[i+1]
            if (nums[i] < nums[i + 1]) {
                swap(nums, i, i + 1);
            }
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
