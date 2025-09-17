

---

## 🎯 PROBLEM: Next Permutation

> Implement next permutation, which rearranges numbers into the **lexicographically next greater permutation** of numbers.  
> If such an arrangement is not possible (i.e., it’s the last permutation), rearrange it as the **lowest possible order** (i.e., sorted in ascending order).  
> **Must modify in-place** — no extra array.

**Examples**:  
- `[1,2,3]` → `[1,3,2]`  
- `[3,2,1]` → `[1,2,3]` (wrap around)  
- `[1,1,5]` → `[1,5,1]`

---

## 🧠 STEP 1: Understand the Problem

- “Lexicographically next greater” → think dictionary order.
- Must do **in-place** → no extra space.
- If no greater permutation → reset to sorted (first permutation).

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Brute Force — Generate All Permutations

→ O(n!) — not feasible.

---

### ✅ The “Next Permutation” Algorithm — O(n)

> Discovered by Narayana Pandita in 14th century — yes, really.

**Steps**:

1. **Find the largest index `i` such that `nums[i] < nums[i+1]`.**  
   → If no such index, reverse entire array (last permutation).

2. **Find the largest index `j > i` such that `nums[j] > nums[i]`.**

3. **Swap `nums[i]` and `nums[j]`.**

4. **Reverse the suffix starting at `i+1`.**

→ Why? To get the **next** permutation, we need to:
- Find the rightmost “ascending” pair → that’s where we can make a small increase.
- Swap with the smallest larger element to its right → to minimize increase.
- Reverse the suffix → to make it the smallest possible (lexicographically).

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class NextPermutation {
    public void nextPermutation(int[] nums) {
        int n = nums.length;

        // Step 1: Find first decreasing element from right
        int i = n - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }

        // Step 2: If found, find element to swap with
        if (i >= 0) {
            int j = n - 1;
            while (nums[j] <= nums[i]) {
                j--;
            }
            swap(nums, i, j);
        }

        // Step 3: Reverse suffix
        reverse(nums, i + 1, n - 1);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            swap(nums, start, end);
            start++;
            end--;
        }
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [1,2,3]`

### Step 1: Find `i` such that `nums[i] < nums[i+1]`

- i=1: nums[1]=2 < nums[2]=3 → found → i=1

### Step 2: Find `j > i` such that `nums[j] > nums[i]`

- j=2: nums[2]=3 > 2 → found → j=2

### Step 3: Swap `nums[i]` and `nums[j]`

- Swap 2 and 3 → `[1,3,2]`

### Step 4: Reverse suffix from `i+1=2` to end

- Suffix = `[2]` → reverse → still `[2]`

✅ Output: `[1,3,2]`

---

### Another Example: `[3,2,1]`

### Step 1: Find `i`

- i=1: 2 >= 1 → continue
- i=0: 3 >= 2 → continue
- i=-1 → not found

### Step 2: Since i<0 → skip swap

### Step 3: Reverse suffix from `0` to `2`

- Reverse `[3,2,1]` → `[1,2,3]`

✅ Output: `[1,2,3]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — three passes: find i, find j, reverse.
- **Space**: O(1) — in-place.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Combinatorics, game AI (next move generation), brute-force password cracking (ethical!), scientific computing.
- **Interview pattern**: **In-place array manipulation with lexicographic order** — appears in Permutations II, Next Greater Element III.
- **Why companies ask this**: Tests if you can reverse-engineer a mathematical algorithm, handle edge cases, modify in-place.

> 💡 Uber: “Generate next route permutation for delivery optimization.”  
> 💡 Netflix: “Next recommendation permutation for A/B testing.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not handling last permutation** → forget to reverse entire array.

- **Finding smallest `j` instead of largest** → should be largest `j` with `nums[j] > nums[i]` to ensure minimal increase.

- **Reversing wrong range** → must reverse from `i+1` to end — not from `i`.

- **Using extra array** → violates in-place constraint.

---

## ✅ STEP 8: Edge Cases to Test

```java
nextPermutation([1,2,3]) → [1,3,2]
nextPermutation([3,2,1]) → [1,2,3]
nextPermutation([1,1,5]) → [1,5,1]
nextPermutation([1]) → [1]
nextPermutation([1,3,2]) → [2,1,3]
nextPermutation([2,3,1]) → [3,1,2]
```

---

## 🧠 BONUS: Why This Algorithm Works

### 📚 Lexicographic Order

Think of permutations as words in a dictionary:

```
[1,2,3]
[1,3,2]
[2,1,3]
[2,3,1]
[3,1,2]
[3,2,1]
```

To get the next word:

1. Find the rightmost position where we can “increase” the letter.
2. Increase it to the next possible letter.
3. Make everything after it as small as possible.

→ That’s exactly what the algorithm does.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Rearranges numbers into lexicographically next greater permutation.
 * Modifies in-place. If no greater permutation, resets to sorted order.
 * @param nums array of integers
 */
public void nextPermutation(int[] nums) {
    int n = nums.length;

    // Step 1: Find the first decreasing element from the right
    int i = n - 2;
    while (i >= 0 && nums[i] >= nums[i + 1]) {
        i--;
    }

    // Step 2: If found, find the smallest element to the right that is larger than nums[i]
    if (i >= 0) {
        int j = n - 1;
        while (nums[j] <= nums[i]) {
            j--;
        }
        swap(nums, i, j);
    }

    // Step 3: Reverse the suffix to make it the smallest possible
    reverse(nums, i + 1, n - 1);
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

private void reverse(int[] nums, int start, int end) {
    while (start < end) {
        swap(nums, start, end);
        start++;
        end--;
    }
}
```

---

