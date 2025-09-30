
---

## 🎯 PROBLEM: Sort Array By Parity II

> Given an array `nums` of non-negative integers, half of which are even and half are odd.  
> Sort the array so that **whenever `nums[i]` is even, `i` is even**, and **whenever `nums[i]` is odd, `i` is odd**.  
> You may return any answer array that satisfies this condition.

**Example**:  
Input: `nums = [4,2,5,7]`  
Output: `[4,5,2,7]` → even indices (0,2): 4,2 (even); odd indices (1,3): 5,7 (odd)

---

## 🧠 STEP 1: Understand the Problem

- Array has **equal number of even and odd numbers**.
- **Even numbers must be at even indices** (0,2,4...).
- **Odd numbers must be at odd indices** (1,3,5...).
- **In-place** rearrangement preferred.
- Any valid arrangement is acceptable.

---

## 🧩 STEP 2: Why Two Pointers for Even/Odd Indices?

Because:

- Use **two pointers**:
  - `even` → tracks next even index (0,2,4...)
  - `odd` → tracks next odd index (1,3,5...)
- If `nums[even]` is odd, and `nums[odd]` is even → swap them.
- Continue until both pointers reach end.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class SortArrayByParityII {
    public int[] sortArrayByParityII(int[] nums) {
        int even = 0; // even indices: 0, 2, 4, ...
        int odd = 1;  // odd indices: 1, 3, 5, ...

        while (even < nums.length && odd < nums.length) {
            // Find even index with odd number
            while (even < nums.length && nums[even] % 2 == 0) {
                even += 2;
            }
            // Find odd index with even number
            while (odd < nums.length && nums[odd] % 2 == 1) {
                odd += 2;
            }

            // Swap if both found
            if (even < nums.length && odd < nums.length) {
                swap(nums, even, odd);
                even += 2;
                odd += 2;
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

Input: `nums = [4,2,5,7]`

### Step 0: Initialize

```
even=0, odd=1
nums = [4,2,5,7]
```

### Step 1: Check even=0

- `nums[0]=4` (even) → correct → `even += 2` → even=2

### Step 2: Check even=2

- `nums[2]=5` (odd) → incorrect → stop

### Step 3: Check odd=1

- `nums[1]=2` (even) → incorrect → stop

### Step 4: Swap even=2 and odd=1

```
swap(5,2) → [4,5,2,7]
even += 2 → 4, odd += 2 → 3
```

### Step 5: even=4 (≥4) → break

✅ Output: `[4,5,2,7]` → even indices: 4,2 (even); odd indices: 5,7 (odd) → correct.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each pointer moves at most n/2 steps.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Memory alignment (even/odd addresses), signal processing (interleaved data), graphics (RGBA channels).
- **Interview pattern**: **Two Pointers for Index Parity** — appears in Wiggle Sort, Rearrange Array.
- **Why companies ask this**: Tests if you can manage multiple pointers, handle index parity, swap in-place.

> 💡 Graphics: “Interleave even/odd pixel channels.”  
> 💡 Embedded: “Align data to even/odd memory addresses.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not incrementing by 2** → pointers go to wrong parity indices.

```java
// 🚫 WRONG
even++; // goes to odd index

// ✅ CORRECT
even += 2; // stays on even indices
```

- **Not checking bounds in inner loops** → index out of bounds.

```java
// ✅ Always check
while (even < nums.length && nums[even] % 2 == 0)
```

- **Swapping without checking bounds** → swap out of bounds.

---

## ✅ STEP 8: Edge Cases to Test

```java
sortArrayByParityII([4,2,5,7]) → [4,5,2,7] or [4,7,2,5]
sortArrayByParityII([2,3]) → [2,3]
sortArrayByParityII([1,0]) → [0,1]
sortArrayByParityII([2,4,6,1,3,5]) → [2,1,4,3,6,5]
```

---

## 🧠 BONUS: Why This Works

- Since there are **equal numbers of even and odd values**, every misplaced even has a corresponding misplaced odd.
- By swapping them, we fix **two positions at once**.
- Incrementing by 2 ensures we only check correct parity indices.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Sorts array so even numbers are at even indices, odd at odd indices.
 * Uses two pointers for even/odd indices — O(n) time, O(1) space.
 * @param nums array with equal even/odd numbers
 * @return rearranged array (in-place)
 */
public int[] sortArrayByParityII(int[] nums) {
    int even = 0; // Tracks even indices (0, 2, 4, ...)
    int odd = 1;  // Tracks odd indices (1, 3, 5, ...)

    while (even < nums.length && odd < nums.length) {
        // Find even index with odd number
        while (even < nums.length && nums[even] % 2 == 0) {
            even += 2;
        }
        // Find odd index with even number
        while (odd < nums.length && nums[odd] % 2 == 1) {
            odd += 2;
        }

        // Swap misplaced even and odd
        if (even < nums.length && odd < nums.length) {
            swap(nums, even, odd);
            even += 2;
            odd += 2;
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
