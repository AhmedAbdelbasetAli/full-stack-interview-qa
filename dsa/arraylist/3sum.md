Let’s solve **“3Sum”** — one of the most iconic (and feared) problems in coding interviews.

This is where **sorting + two pointers + deduplication** come together in a beautiful, brutal dance.

We’ll walk through it like senior engineers — with trade-offs, edge cases, and real-world relevance.

---

## 🎯 PROBLEM: 3Sum

> Given an integer array `nums`, return all **unique triplets** `[nums[i], nums[j], nums[k]]` such that `i != j != k` and `nums[i] + nums[j] + nums[k] == 0`.  
> The solution set must not contain duplicate triplets.

**Example**:  
Input: `nums = [-1,0,1,2,-1,-4]`  
Output: `[[-1,-1,2], [-1,0,1]]`

> 💡 Note: `[-1,0,1]` and `[0,-1,1]` are considered duplicates — order doesn’t matter.

---

## 🧠 STEP 1: Understand the Problem

- Find **all** triplets that sum to 0.
- Triplets must be **unique** — no duplicate sets.
- Indices must be distinct — but values can repeat (e.g., `[-1,-1,2]`).
- Return list of lists.

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Brute Force — O(n³)

```java
for i in 0..n-3
  for j in i+1..n-2
    for k in j+1..n-1
      if nums[i] + nums[j] + nums[k] == 0 → add to result
```

→ Works, but O(n³) — too slow. Also need deduplication.

---

### ❌ HashMap — O(n²) time, O(n) space

> For each i, j → check if `-(nums[i] + nums[j])` exists in map.

→ Still O(n²), and deduplication is messy.

---

### ✅ Sort + Two Pointers — O(n²) time, O(1) space (excluding output)

> “Fix one number, then use two pointers for the other two.”

- **Sort** the array → enables two pointers and easy deduplication.
- For each `i` from `0` to `n-3`:
  - Skip if `nums[i] == nums[i-1]` → avoid duplicate triplets.
  - Set `left = i+1`, `right = n-1`.
  - While `left < right`:
    - `sum = nums[i] + nums[left] + nums[right]`
    - If `sum == 0` → add triplet, move both pointers, skip duplicates.
    - If `sum < 0` → `left++`
    - If `sum > 0` → `right--`

→ Elegant, efficient, and handles deduplication cleanly.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class ThreeSum {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums); // Critical — enables two pointers and deduplication
        int n = nums.length;

        for (int i = 0; i < n - 2; i++) {
            // Skip duplicate for i
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1;
            int right = n - 1;

            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];

                if (sum == 0) {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                    // Skip duplicates for left
                    while (left < right && nums[left] == nums[left + 1]) {
                        left++;
                    }
                    // Skip duplicates for right
                    while (left < right && nums[right] == nums[right - 1]) {
                        right--;
                    }

                    left++;
                    right--;
                } else if (sum < 0) {
                    left++; // Need larger sum
                } else {
                    right--; // Need smaller sum
                }
            }
        }

        return result;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [-1,0,1,2,-1,-4]`

### Step 0: Sort → `[-4,-1,-1,0,1,2]`

### Step 1: i=0, nums[i]=-4

- left=1 (-1), right=5 (2) → sum = -4-1+2 = -3 < 0 → left++
- left=2 (-1), right=5 (2) → sum = -4-1+2 = -3 < 0 → left++
- left=3 (0), right=5 (2) → sum = -4+0+2 = -2 < 0 → left++
- left=4 (1), right=5 (2) → sum = -4+1+2 = -1 < 0 → left++
- left=5, right=5 → break

→ No triplet with -4.

---

### Step 2: i=1, nums[i]=-1

- left=2 (-1), right=5 (2) → sum = -1-1+2 = 0 → add `[-1,-1,2]`
  - Skip duplicates: left: -1→0 (left=3), right: 2→1 (right=4)
  - Now left=3 (0), right=4 (1)
- sum = -1+0+1 = 0 → add `[-1,0,1]`
  - Skip duplicates: none → left=4, right=3 → break

→ Triplets: `[-1,-1,2]`, `[-1,0,1]`

---

### Step 3: i=2, nums[i]=-1

- `nums[2] == nums[1]` → skip → avoid duplicate triplet.

---

### Step 4: i=3, nums[i]=0

- left=4 (1), right=5 (2) → sum=0+1+2=3 >0 → right--
- right=4 → left=4 → break

→ Done.

✅ Output: `[[-1,-1,2], [-1,0,1]]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n²) — sort O(n log n) + outer loop O(n) * inner while O(n).
- **Space**: O(1) — excluding output (which is required).

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Financial risk analysis (find offsetting positions), scientific computing (zero-sum systems), game AI (balanced teams).
- **Interview pattern**: **Sort + Two Pointers + Deduplication** — appears in 3Sum Closest, 4Sum, and many variants.
- **Why companies ask this**: Tests if you can handle deduplication, leverage sorting, and optimize nested loops.

> 💡 Uber: “Find three rides whose total fare = 0 (refunds + payments).”  
> 💡 Banking: “Find three transactions that net to zero.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not sorting** → two pointers won’t work.

- **Not skipping duplicates** → returns `[-1,0,1]` multiple times.

- **Skipping duplicates incorrectly** → might skip valid triplets.

```java
// 🚫 WRONG — skip before processing
if (nums[left] == nums[left+1]) left++;

// ✅ CORRECT — skip after adding triplet
left++;
while (left < right && nums[left] == nums[left-1]) left++;
```

- **Using same element twice** → ensure `i < left < right`.

- **Forgetting to move pointers after finding sum=0** → infinite loop.

---

## ✅ STEP 8: Edge Cases to Test

```java
threeSum([-1,0,1,2,-1,-4]) → [[-1,-1,2],[-1,0,1]]
threeSum([0,1,1]) → []
threeSum([0,0,0]) → [[0,0,0]]
threeSum([-2,0,1,1,2]) → [[-2,0,2],[-2,1,1]]
threeSum([-1,0,1,0]) → [[-1,0,1]]
```

---

## 🧠 BONUS: Why Sort + Two Pointers Works

- **Sorting** lets us use two pointers and skip duplicates easily.
- **Fixing one element** reduces 3Sum to 2Sum (which we solve with two pointers).
- **Deduplication** by skipping same values for `i`, `left`, `right` ensures uniqueness.

→ This pattern extends to **4Sum, KSum** — fix first k-2 elements, use two pointers for last two.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds all unique triplets that sum to zero.
 * Uses sort + two pointers + deduplication — O(n²) time, O(1) space.
 * @param nums array of integers
 * @return list of unique triplets [a,b,c] with a+b+c=0
 */
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    Arrays.sort(nums); // Essential for two pointers and deduplication
    int n = nums.length;

    for (int i = 0; i < n - 2; i++) {
        // Skip duplicate values for i to avoid duplicate triplets
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }

        int left = i + 1;
        int right = n - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];

            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));

                // Skip duplicates for left and right
                while (left < right && nums[left] == nums[left + 1]) {
                    left++;
                }
                while (left < right && nums[right] == nums[right - 1]) {
                    right--;
                }

                // Move both pointers
                left++;
                right--;
            } else if (sum < 0) {
                left++; // Need larger sum
            } else {
                right--; // Need smaller sum
            }
        }
    }

    return result;
}
