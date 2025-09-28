
---

## 🎯 PROBLEM: Remove Duplicates from Sorted Array II

> Given an integer array `nums` sorted in non-decreasing order, remove duplicates in-place such that **each unique element appears at most twice**.  
> The relative order of the elements should be kept the same.  
> Return the new length.  
> Do not allocate extra space for another array — modify input array in-place with O(1) extra memory.

**Example**:  
Input: `nums = [1,1,1,2,2,3]`  
Output: `5`, and `nums = [1,1,2,2,3,...]`

---

## 🧠 STEP 1: Understand the Problem

- Array is **sorted** → duplicates are adjacent.
- Allow **at most 2 duplicates** per element.
- **In-place** — no new array.
- Return **new length** — elements beyond that don’t matter.
- **Preserve order** — which is natural since array is sorted.

---

## 🧩 STEP 2: Why Read/Write Pointers + Count?

Because:

- **Read pointer** scans entire array.
- **Write pointer** tracks where to place next valid element.
- Since array is sorted, we can **count consecutive duplicates**.
- Write element if count ≤ 2.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class RemoveDuplicatesII {
    public int removeDuplicates(int[] nums) {
        if (nums.length <= 2) {
            return nums.length;
        }

        int writeIndex = 2; // First two elements are always valid

        for (int readIndex = 2; readIndex < nums.length; readIndex++) {
            // Compare with element two positions before writeIndex
            if (nums[readIndex] != nums[writeIndex - 2]) {
                nums[writeIndex] = nums[readIndex];
                writeIndex++;
            }
        }

        return writeIndex; // New logical length
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [1,1,1,2,2,3]`

| readIndex | nums[readIndex] | nums[writeIndex-2] | != ? | writeIndex | nums becomes       |
|-----------|------------------|---------------------|------|------------|--------------------|
| 2         | 1                | nums[0]=1           | 1==1 → ❌ | 2          | [1,1,1,2,2,3]      |
| 3         | 2                | nums[0]=1           | 2!=1 → ✅ | 3          | [1,1,2,2,2,3]      |
| 4         | 2                | nums[1]=1           | 2!=1 → ✅ | 4          | [1,1,2,2,2,3]      |
| 5         | 3                | nums[2]=2           | 3!=2 → ✅ | 5          | [1,1,2,2,3,3]      |

→ Return `5`. First 5 elements: `[1,1,2,2,3]` → correct.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — one pass.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Log deduplication (allow bursts), time-series data (allow duplicate readings), UI rendering (limit duplicate items).
- **Interview pattern**: **Read/Write Pointers with Lookback** — appears in Remove Duplicates I, Move Zeroes.
- **Why companies ask this**: Tests if you can generalize deduplication logic, handle lookback, avoid extra space.

> 💡 Uber: “Allow up to 2 duplicate ride logs for redundancy.”  
> 💡 IoT: “Allow up to 2 duplicate sensor readings for noise tolerance.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using a separate count variable** → unnecessary.

```java
// 🚫 Overcomplicated
int count = 1;
for (int i = 1; i < nums.length; i++) {
    if (nums[i] == nums[i-1]) count++;
    else count = 1;
    if (count <= 2) { ... }
}
```

- **Comparing with `nums[readIndex-2]`** → should compare with `nums[writeIndex-2]`.

```java
// 🚫 WRONG
if (nums[readIndex] != nums[readIndex - 2])

// ✅ CORRECT
if (nums[readIndex] != nums[writeIndex - 2])
```

- **Not handling small arrays** → if n ≤ 2, return n.

---

## ✅ STEP 8: Edge Cases to Test

```java
removeDuplicates([1,1,1,2,2,3]) → 5, [1,1,2,2,3]
removeDuplicates([0,0,1,1,1,1,2,3,3]) → 7, [0,0,1,1,2,3,3]
removeDuplicates([1,1,1,1]) → 2, [1,1]
removeDuplicates([1,2,3]) → 3, [1,2,3]
removeDuplicates([1]) → 1, [1]
removeDuplicates([]) → 0
```

---

## 🧠 BONUS: Why Compare with `writeIndex - 2`?

Because:

- The first `writeIndex` elements are already valid (at most 2 duplicates).
- So `nums[writeIndex - 2]` is the **first occurrence** of the current group.
- If `nums[readIndex] != nums[writeIndex - 2]`, then it’s a **new group** → safe to write.

→ This avoids needing a separate count variable.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Removes duplicates from sorted array in-place, allowing at most 2 duplicates.
 * Uses lookback to nums[writeIndex - 2] — O(n) time, O(1) space.
 * @param nums sorted array of integers
 * @return new length after removing extra duplicates
 */
public int removeDuplicates(int[] nums) {
    if (nums.length <= 2) {
        return nums.length;
    }

    int writeIndex = 2; // First two elements are always valid

    // Start from third element
    for (int readIndex = 2; readIndex < nums.length; readIndex++) {
        // Compare with element two positions before writeIndex
        // If different, it's a new group → safe to write
        if (nums[readIndex] != nums[writeIndex - 2]) {
            nums[writeIndex] = nums[readIndex];
            writeIndex++;
        }
    }

    return writeIndex; // New logical length
}
```

---

