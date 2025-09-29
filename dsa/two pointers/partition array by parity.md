---

## 🎯 PROBLEM: Partition Array by Parity

> Given an integer array `nums`, move all the even integers at the beginning of the array followed by all the odd integers.  
> Return any array that satisfies this condition.

**Example**:  
Input: `nums = [3,1,2,4]`  
Output: `[2,4,3,1]` (any order of evens/odds is acceptable)

---

## 🧠 STEP 1: Understand the Problem

- Partition array into **evens first**, then **odds**.
- Order within evens/odds **doesn’t matter**.
- **In-place** — no extra array (preferred).
- Return the array (modified in-place).

---

## 🧩 STEP 2: Two Approaches

### ✅ Approach 1: Read/Write Pointers (Stable Order)

- **Read pointer** scans array.
- **Write pointer** tracks where to place next even.
- After placing all evens, odds are already in place (since we’re overwriting with evens, but odds remain in their positions).

→ Preserves relative order of evens and odds.

### ✅ Approach 2: Collision Pointers (Unstable, Fewer Writes)

- **Left pointer** starts at 0, **right pointer** at end.
- Move left until odd, move right until even.
- Swap them.
- Continue until pointers meet.

→ Does **not preserve order**, but **fewer writes**.

We’ll show **both** — but **Approach 2 (Collision)** is more efficient and commonly expected.

---

## ✍️ STEP 3: Code It — Clean Java (Collision Approach)

```java
public class SortArrayByParity {
    public int[] sortArrayByParity(int[] nums) {
        int left = 0;
        int right = nums.length - 1;

        while (left < right) {
            // Move left until we find an odd number
            while (left < right && nums[left] % 2 == 0) {
                left++;
            }
            // Move right until we find an even number
            while (left < right && nums[right] % 2 == 1) {
                right--;
            }
            // Swap odd (left) with even (right)
            if (left < right) {
                swap(nums, left, right);
                left++;
                right--;
            }
        }

        return nums;
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

Input: `nums = [3,1,2,4]`

| Step | left | right | nums[left] | nums[right] | Action          | Array State     |
|------|------|-------|------------|-------------|-----------------|-----------------|
| 1    | 0    | 3     | 3 (odd)    | 4 (even)    | swap → [4,1,2,3]| [4,1,2,3]       |
| 2    | 1    | 2     | 1 (odd)    | 2 (even)    | swap → [4,2,1,3]| [4,2,1,3]       |
| 3    | 2    | 1     | —          | —           | left >= right → break | ✅ Done |

✅ Output: `[4,2,1,3]` — evens first, then odds.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element visited once.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Quicksort partitioning, data preprocessing (separate valid/invalid), game dev (separate active/inactive entities).
- **Interview pattern**: **Collision Pointers for Partitioning** — appears in Sort Colors, Move Zeroes.
- **Why companies ask this**: Tests if you can partition in-place, handle two pointers, avoid extra space.

> 💡 Uber: “Partition rides into completed (even ID) and pending (odd ID).”  
> 💡 Game Dev: “Partition entities into alive (even health) and dead (odd health).”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not checking `left < right` in inner loops** → infinite loop or index out of bounds.

```java
// 🚫 DANGEROUS
while (nums[left] % 2 == 0) left++; // could go out of bounds

// ✅ SAFE
while (left < right && nums[left] % 2 == 0) left++;
```

- **Swapping without checking `left < right`** → swap same element or cross pointers.

- **Using extra array** → violates in-place preference.

---

## ✅ STEP 8: Edge Cases to Test

```java
sortArrayByParity([3,1,2,4]) → [2,4,3,1] or [4,2,1,3]
sortArrayByParity([0]) → [0]
sortArrayByParity([1]) → [1]
sortArrayByParity([2,4,6]) → [2,4,6]
sortArrayByParity([1,3,5]) → [1,3,5]
sortArrayByParity([1,2,3,4,5,6]) → [2,4,6,1,3,5]
```

---

## 🧠 BONUS: Read/Write Approach (Stable Order)

If you need to **preserve relative order** of evens and odds:

```java
public int[] sortArrayByParity(int[] nums) {
    int writeIndex = 0;
    int[] result = new int[nums.length]; // if allowed extra space

    // Write evens
    for (int num : nums) {
        if (num % 2 == 0) {
            result[writeIndex++] = num;
        }
    }
    // Write odds
    for (int num : nums) {
        if (num % 2 == 1) {
            result[writeIndex++] = num;
        }
    }
    return result;
}
```

→ But problem allows any order — so **collision approach is better** (in-place, fewer writes).

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Partitions array so all even integers come before odd integers.
 * Uses collision pointers — O(n) time, O(1) space.
 * @param nums array of integers
 * @return partitioned array (in-place)
 */
public int[] sortArrayByParity(int[] nums) {
    int left = 0;                    // Start at beginning
    int right = nums.length - 1;     // Start at end

    while (left < right) {
        // Find odd number from left
        while (left < right && nums[left] % 2 == 0) {
            left++;
        }
        // Find even number from right
        while (left < right && nums[right] % 2 == 1) {
            right--;
        }
        // Swap odd (left) with even (right)
        if (left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }

    return nums;
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

---

