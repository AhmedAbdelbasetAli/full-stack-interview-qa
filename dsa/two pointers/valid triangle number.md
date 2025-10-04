---

## 🎯 PROBLEM: Valid Triangle Number

> Given an integer array `nums`, return the number of triplets chosen from the array that can make **triangles** if we take them as side lengths of a triangle.

> **Triangle Inequality**: For sides `a`, `b`, `c`, must have:
> - `a + b > c`
> - `a + c > b`
> - `b + c > a`

> But if array is **sorted** and `a ≤ b ≤ c`, then only need to check `a + b > c`.

**Example**:  
Input: `nums = [2,2,3,4]`  
Output: `3` → Valid triplets: `(2,3,4)`, `(2,3,4)`, `(2,2,3)`

---

## 🧠 STEP 1: Understand the Problem

- Count **all unique triplets** `(i, j, k)` with `i < j < k` such that they form a valid triangle.
- **Triangle inequality** simplifies to `nums[i] + nums[j] > nums[k]` if sorted and `i < j < k`.
- Array may have duplicates — but indices must be distinct.

---

## 🧩 STEP 2: Why Sort + Two Pointers?

Because:

- **Sort** the array → enables triangle inequality simplification.
- Fix the **largest side** `k` (from end).
- Use **two pointers** (`i=0`, `j=k-1`) to find all pairs where `nums[i] + nums[j] > nums[k]`.
- If `nums[i] + nums[j] > nums[k]`, then all pairs `(i, i+1, ..., j-1, j)` with `j` are valid → add `j - i` to count.

→ O(n²) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class ValidTriangleNumber {
    public int triangleNumber(int[] nums) {
        Arrays.sort(nums); // Essential for triangle inequality simplification
        int count = 0;
        int n = nums.length;

        // Fix largest side k (from end)
        for (int k = n - 1; k >= 2; k--) {
            int i = 0;
            int j = k - 1;

            // Two pointers for smaller sides
            while (i < j) {
                if (nums[i] + nums[j] > nums[k]) {
                    // All pairs (i, i+1, ..., j-1, j) with j are valid
                    count += j - i;
                    j--; // Try smaller j
                } else {
                    i++; // Need larger sum
                }
            }
        }

        return count;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [2,2,3,4]` → sorted: `[2,2,3,4]`

### Step 1: k=3 (nums[k]=4)

- i=0, j=2 → nums[0]+nums[2]=2+3=5 > 4 → valid → count += 2-0 = 2 → j=1
- i=0, j=1 → 2+2=4 not > 4 → i=1
- i=1, j=1 → break

### Step 2: k=2 (nums[k]=3)

- i=0, j=1 → 2+2=4 > 3 → valid → count += 1-0 = 1 → total=3

### Step 3: k=1 → break (k>=2)

✅ Output: `3`

Valid triplets:
- (0,2,3) → (2,3,4)
- (1,2,3) → (2,3,4)
- (0,1,2) → (2,2,3)

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n²) — sort O(n log n) + outer loop O(n) * inner while O(n).
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Computational geometry, physics simulations (collision detection), graphics (mesh validation).
- **Interview pattern**: **Sort + Two Pointers for Triplets** — appears in 3Sum, 3Sum Closest.
- **Why companies ask this**: Tests if you can simplify triangle inequality, use two pointers for counting, handle duplicates.

> 💡 Graphics: “Validate triangle mesh in 3D model.”  
> 💡 Physics: “Check if three forces can form a closed triangle.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not sorting** → can’t simplify triangle inequality.

- **Checking all three inequalities** → unnecessary if sorted.

- **Using brute force** → O(n³) → too slow.

```java
// 🚫 WRONG — O(n³)
for (int i=0; i<n; i++)
  for (int j=i+1; j<n; j++)
    for (int k=j+1; k<n; k++)
      if (nums[i]+nums[j]>nums[k] && ...) count++;
```

- **Not counting all valid pairs** → when `nums[i] + nums[j] > nums[k]`, all `i` to `j-1` with `j` are valid.

---

## ✅ STEP 8: Edge Cases to Test

```java
triangleNumber([2,2,3,4]) → 3
triangleNumber([4,2,3,4]) → 4
triangleNumber([0,1,0]) → 0
triangleNumber([1,1,1]) → 1
triangleNumber([2,3,4,5]) → 3
triangleNumber([1,2]) → 0
```

---

## 🧠 BONUS: Why `count += j - i`?

Because:

- If `nums[i] + nums[j] > nums[k]`, then for **all** `i'` in `[i, j-1]`, `nums[i'] + nums[j] >= nums[i] + nums[j] > nums[k]`.
- So there are `j - i` valid pairs with `j` and `k`.

→ This avoids nested loops and counts in O(1).

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Counts number of triplets that can form a triangle.
 * Uses sort + two pointers — O(n²) time, O(1) space.
 * @param nums array of side lengths
 * @return number of valid triangle triplets
 */
public int triangleNumber(int[] nums) {
    Arrays.sort(nums);
    int count = 0;
    int n = nums.length;

    // Fix largest side (k) from end
    for (int k = n - 1; k >= 2; k--) {
        int i = 0;
        int j = k - 1;

        // Two pointers for smaller sides
        while (i < j) {
            if (nums[i] + nums[j] > nums[k]) {
                // All pairs from i to j-1 with j are valid
                count += j - i;
                j--; // Try smaller j
            } else {
                i++; // Need larger sum
            }
        }
    }

    return count;
}
```

---

