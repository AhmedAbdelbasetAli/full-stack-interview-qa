

---

## 🎯 PROBLEM: 3Sum Closest

> Given an integer array `nums` of length `n` and an integer `target`, find three integers in `nums` such that the sum is **closest to target**.  
> Return the sum of the three integers.  
> You may assume that each input would have **exactly one solution**.

**Example**:  
Input: `nums = [-1,2,1,-4]`, `target = 1`  
Output: `2` → because `(-1) + 2 + 1 = 2` is closest to 1.

---

## 🧠 STEP 1: Understand the Problem

- Find **one triplet** (not all) whose sum is closest to `target`.
- “Closest” = smallest absolute difference `|sum - target|`.
- Return the **sum**, not the triplet.
- Exactly one solution → no ties to handle.

---

## 🧩 STEP 2: Why Sort + Two Pointers?

Same as 3Sum:

- **Sorting** enables two pointers.
- **Fix one element** → reduces to 2Sum Closest.
- **Two pointers** to find best pair for fixed element.

→ O(n²) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class ThreeSumClosest {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums); // Essential for two pointers
        int n = nums.length;
        int closestSum = nums[0] + nums[1] + nums[2]; // Initialize with first triplet

        for (int i = 0; i < n - 2; i++) {
            int left = i + 1;
            int right = n - 1;

            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                // Update closestSum if this sum is closer to target
                if (Math.abs(sum - target) < Math.abs(closestSum - target)) {
                    closestSum = sum;
                }

                if (sum < target) {
                    left++; // Need larger sum
                } else if (sum > target) {
                    right--; // Need smaller sum
                } else {
                    return sum; // Exact match — can't get closer
                }
            }
        }

        return closestSum;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [-1,2,1,-4]`, `target = 1`

### Step 0: Sort → `[-4,-1,1,2]`

### Step 1: i=0, nums[i]=-4

- left=1 (-1), right=3 (2) → sum = -4-1+2 = -3 → | -3 - 1 | = 4 → closestSum = min(?, -3) → closestSum=-3
- -3 < 1 → left++ → left=2
- left=2 (1), right=3 (2) → sum = -4+1+2 = -1 → | -1 - 1 | = 2 < 4 → closestSum = -1
- -1 < 1 → left++ → left=3 → break (left >= right)

### Step 2: i=1, nums[i]=-1

- left=2 (1), right=3 (2) → sum = -1+1+2 = 2 → |2-1|=1 < 2 → closestSum=2
- 2 > 1 → right-- → right=2 → break (left >= right)

### Step 3: i=2, nums[i]=1

- i < n-2 → 2 < 4-2 → 2<2 → false → loop ends

✅ Return `2`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n²) — sort O(n log n) + outer loop O(n) * inner while O(n).
- **Space**: O(1) — only a few variables.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Financial modeling (closest portfolio value), scientific computing (approximate solutions), game AI (closest achievable score).
- **Interview pattern**: **Sort + Two Pointers for Optimization** — appears in 3Sum, 4Sum Closest.
- **Why companies ask this**: Tests if you can adapt 3Sum logic to optimization, handle absolute difference, early return.

> 💡 Uber: “Find three rides whose total duration is closest to 1 hour.”  
> 💡 Netflix: “Find three movies whose total duration is closest to 2 hours.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not initializing `closestSum`** → use first triplet.

- **Not using `Math.abs`** → closest means smallest absolute difference.

- **Not early returning on exact match** → if `sum == target`, return immediately.

- **Using `i <= n-3`** → same as `i < n-2` — but stick to convention.

---

## ✅ STEP 8: Edge Cases to Test

```java
threeSumClosest([-1,2,1,-4], 1) → 2
threeSumClosest([0,0,0], 1) → 0
threeSumClosest([1,1,1,0], -100) → 2
threeSumClosest([1,1,1,1], 0) → 3
threeSumClosest([-100,-98,-2,-1], -101) → -101 (exact match)
```

---

## 🧠 BONUS: Why This Works

Same logic as 3Sum — but instead of checking `sum == 0`, we track `min |sum - target|`.

Early return on `sum == target` is a nice optimization — but not required.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds three integers in nums such that sum is closest to target.
 * Uses sort + two pointers — O(n²) time, O(1) space.
 * @param nums array of integers
 * @param target target sum
 * @return sum of three integers closest to target
 */
public int threeSumClosest(int[] nums, int target) {
    Arrays.sort(nums);
    int n = nums.length;
    int closestSum = nums[0] + nums[1] + nums[2]; // Initialize

    for (int i = 0; i < n - 2; i++) {
        int left = i + 1;
        int right = n - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            // Update closest sum if this is better
            if (Math.abs(sum - target) < Math.abs(closestSum - target)) {
                closestSum = sum;
            }

            if (sum < target) {
                left++; // Need larger sum
            } else if (sum > target) {
                right--; // Need smaller sum
            } else {
                return sum; // Exact match — can't get closer
            }
        }
    }

    return closestSum;
}
```

---

