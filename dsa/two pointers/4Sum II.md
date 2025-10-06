---

## 🎯 PROBLEM: 4Sum II

> Given four integer arrays `nums1`, `nums2`, `nums3`, and `nums4` all of length `n`, return the number of tuples `(i, j, k, l)` such that:
> - `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

**Example**:  
Input:  
`nums1 = [1,2]`,  
`nums2 = [-2,-1]`,  
`nums3 = [-1,2]`,  
`nums4 = [0,2]`  
Output: `2`  
→ Valid tuples: `(0,0,0,1)` → `1 + (-2) + (-1) + 2 = 0`  
→ `(1,1,0,0)` → `2 + (-1) + (-1) + 0 = 0`

---

## 🧠 STEP 1: Understand the Problem

- Four arrays of equal length `n`.
- Count all combinations `(i,j,k,l)` such that sum = 0.
- **Not** returning the tuples — just the **count**.
- Arrays can have duplicates — all indices are valid.

---

## 🧩 STEP 2: Why Hash Map (Meet in the Middle)?

Because:

- Brute force is O(n⁴) — too slow for `n=200` (200⁴ = 1.6 billion).
- **Split into two pairs**:
  - Compute all sums `a + b` for `a in nums1, b in nums2` → store in HashMap.
  - For each sum `c + d` in `nums3, nums4`, check if `-(c + d)` exists in HashMap.
- Time: O(n²) — feasible for `n=200` (200² = 40,000).

→ O(n²) time, O(n²) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class FourSumII {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        Map<Integer, Integer> sumCount = new HashMap<>();

        // Step 1: Count all sums from nums1 and nums2
        for (int a : nums1) {
            for (int b : nums2) {
                sumCount.put(a + b, sumCount.getOrDefault(a + b, 0) + 1);
            }
        }

        // Step 2: For each sum from nums3 and nums4, check if complement exists
        int count = 0;
        for (int c : nums3) {
            for (int d : nums4) {
                int complement = -(c + d);
                count += sumCount.getOrDefault(complement, 0);
            }
        }

        return count;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input:  
`nums1 = [1,2]`, `nums2 = [-2,-1]`, `nums3 = [-1,2]`, `nums4 = [0,2]`

### Step 1: Build sumCount from nums1 + nums2

```
1 + (-2) = -1 → count[-1] = 1
1 + (-1) = 0  → count[0] = 1
2 + (-2) = 0  → count[0] = 2
2 + (-1) = 1  → count[1] = 1

sumCount = {-1:1, 0:2, 1:1}
```

### Step 2: Check nums3 + nums4

```
-1 + 0 = -1 → complement = 1 → count += 1
-1 + 2 = 1  → complement = -1 → count += 1
2 + 0 = 2   → complement = -2 → count += 0
2 + 2 = 4   → complement = -4 → count += 0

Total count = 2 ✅
```

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n²) — two nested loops for first pair, two for second pair.
- **Space**: O(n²) — HashMap stores up to n² sums.

✅ Optimal for this problem.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Distributed systems (count across shards), financial risk (offsetting positions), analytics (cross-dataset joins).
- **Interview pattern**: **Meet in the Middle with Hash Map** — appears in 2Sum, 4Sum II, Target Sum.
- **Why companies ask this**: Tests if you can reduce O(n⁴) to O(n²), use HashMap for counting, handle complements.

> 💡 Finance: “Count combinations of 4 trades that net to zero.”  
> 💡 Analytics: “Count user-item interactions across 4 dimensions that sum to zero.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using brute force O(n⁴)** → too slow.

- **Not using `getOrDefault`** → NPE or missing counts.

```java
// 🚫 WRONG
count += sumCount.get(complement); // returns null if not present

// ✅ CORRECT
count += sumCount.getOrDefault(complement, 0);
```

- **Storing tuples instead of counts** → wastes memory.

- **Not splitting evenly** → e.g., 3 arrays + 1 array → O(n³) → still too slow.

---

## ✅ STEP 8: Edge Cases to Test

```java
fourSumCount([1,2], [-2,-1], [-1,2], [0,2]) → 2
fourSumCount([0], [0], [0], [0]) → 1
fourSumCount([1], [1], [1], [1]) → 0
fourSumCount([-1,-1], [-1,-1], [-1,-1], [-1,-1]) → 16
```

---

## 🧠 BONUS: Why Meet in the Middle?

Because:

- O(n⁴) is infeasible for n=200.
- Splitting into two O(n²) steps reduces time dramatically.
- This is a **standard technique** for k-sum problems when k is even.

→ Generalizes to **2k-Sum**: split into two k-sum problems.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Counts number of tuples (i,j,k,l) such that 
 * nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0.
 * Uses meet-in-the-middle with HashMap — O(n²) time, O(n²) space.
 * @param nums1 first array
 * @param nums2 second array
 * @param nums3 third array
 * @param nums4 fourth array
 * @return number of valid tuples
 */
public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
    Map<Integer, Integer> sumCount = new HashMap<>();

    // Count all possible sums from first two arrays
    for (int a : nums1) {
        for (int b : nums2) {
            sumCount.put(a + b, sumCount.getOrDefault(a + b, 0) + 1);
        }
    }

    // For each sum from last two arrays, check if complement exists
    int count = 0;
    for (int c : nums3) {
        for (int d : nums4) {
            int complement = -(c + d);
            count += sumCount.getOrDefault(complement, 0);
        }
    }

    return count;
}
```

