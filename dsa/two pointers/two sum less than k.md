

---

## 🎯 PROBLEM: Two Sum Less Than K

> Given an array `nums` of integers and an integer `k`, return the **maximum sum** of two numbers such that the sum is **less than k**.  
> If no such pair exists, return `-1`.

**Example**:  
Input: `nums = [34,23,1,24,75,33,54,8]`, `k = 60`  
Output: `58` → because `34 + 24 = 58 < 60`, and no other pair has a larger sum < 60.

---

## 🧠 STEP 1: Understand the Problem

- Find **one pair** (not all) whose sum is **< k** and **as large as possible**.
- Return the **sum**, not the indices.
- If no pair sums to < k → return `-1`.

---

## 🧩 STEP 2: Why Sort + Two Pointers?

Because:

- **Sorting** lets us use two pointers to efficiently explore pairs.
- Start with `left=0`, `right=n-1`.
- If `sum < k` → candidate → try to find larger sum by moving `left` right.
- If `sum >= k` → too big → move `right` left.

→ O(n log n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class TwoSumLessThanK {
    public int twoSumLessThanK(int[] nums, int k) {
        Arrays.sort(nums); // Essential for two pointers
        int left = 0;
        int right = nums.length - 1;
        int maxSum = -1; // Initialize to -1 (no valid pair)

        while (left < right) {
            int sum = nums[left] + nums[right];

            if (sum < k) {
                maxSum = Math.max(maxSum, sum); // Update candidate
                left++; // Try to find larger sum
            } else {
                right--; // Sum too big → reduce
            }
        }

        return maxSum;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [34,23,1,24,75,33,54,8]`, `k = 60`

### Step 0: Sort → `[1,8,23,24,33,34,54,75]`

### Step 1: left=0 (1), right=7 (75)

- sum = 1+75=76 >=60 → right-- → right=6

### Step 2: left=0 (1), right=6 (54)

- sum=1+54=55 <60 → maxSum=55 → left++ → left=1

### Step 3: left=1 (8), right=6 (54)

- sum=8+54=62 >=60 → right-- → right=5

### Step 4: left=1 (8), right=5 (34)

- sum=8+34=42 <60 → maxSum=max(55,42)=55 → left++ → left=2

### Step 5: left=2 (23), right=5 (34)

- sum=23+34=57 <60 → maxSum=max(55,57)=57 → left++ → left=3

### Step 6: left=3 (24), right=5 (34)

- sum=24+34=58 <60 → maxSum=max(57,58)=58 → left++ → left=4

### Step 7: left=4 (33), right=5 (34)

- sum=33+34=67 >=60 → right-- → right=4 → break (left >= right)

✅ Return `58`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n) — sort dominates.
- **Space**: O(1) — only a few variables.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Budget allocation (max spend under limit), resource allocation, game balancing.
- **Interview pattern**: **Sort + Two Pointers for Optimization** — appears in 3Sum Closest, Container With Most Water.
- **Why companies ask this**: Tests if you can adapt two pointers to optimization, handle edge cases, initialize correctly.

> 💡 Uber: “Maximize fare under surge limit.”  
> 💡 Netflix: “Maximize watch time under 2 hours.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not initializing `maxSum` to -1** → if no valid pair, must return -1.

- **Not sorting** → two pointers won’t work.

- **Moving wrong pointer** → if sum < k, move `left` to get larger sum.

- **Using `<=` in loop** → `left == right` → invalid pair.

---

## ✅ STEP 8: Edge Cases to Test

```java
twoSumLessThanK([34,23,1,24,75,33,54,8], 60) → 58
twoSumLessThanK([10,20,30], 15) → -1
twoSumLessThanK([1,2,3], 4) → 3 (1+2)
twoSumLessThanK([1,1,1], 3) → 2 (1+1)
twoSumLessThanK([1], 2) → -1
```

---

## 🧠 BONUS: Why This Works

- Sorting lets us **greedily** explore pairs.
- If sum < k → we record it and try to **increase** sum by moving `left`.
- If sum >= k → we **decrease** sum by moving `right`.

→ We cover all possible pairs without nested loops.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds maximum sum of two numbers less than k.
 * Uses sort + two pointers — O(n log n) time, O(1) space.
 * @param nums array of integers
 * @param k upper bound (exclusive)
 * @return maximum sum < k, or -1 if no such pair
 */
public int twoSumLessThanK(int[] nums, int k) {
    Arrays.sort(nums);
    int left = 0;
    int right = nums.length - 1;
    int maxSum = -1; // No valid pair initially

    while (left < right) {
        int sum = nums[left] + nums[right];

        if (sum < k) {
            maxSum = Math.max(maxSum, sum); // Update best candidate
            left++; // Try to find larger sum
        } else {
            right--; // Sum too big — reduce
        }
    }

    return maxSum;
}
```

---
