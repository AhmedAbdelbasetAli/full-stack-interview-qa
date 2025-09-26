

---

## 🎯 PROBLEM: Remove Duplicates from Sorted Array

> Given an integer array `nums` sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once.  
> The relative order of the elements should be kept the same.  
> Return the new length.  
> Do not allocate extra space for another array — modify input array in-place with O(1) extra memory.

**Example**:  
Input: `nums = [1,1,2]`  
Output: `2`, and `nums = [1,2,...]` (first 2 elements are `[1,2]`)

---

## 🧠 STEP 1: Understand the Problem

- Array is **sorted** → duplicates are adjacent.
- Remove duplicates → keep one copy of each unique element.
- **In-place** — no new array.
- Return **new length** — elements beyond that don’t matter.
- **Preserve order** — which is natural since array is sorted.

---

## 🧩 STEP 2: Why Read/Write Pointers?

Because:

- **Read pointer** scans entire array.
- **Write pointer** tracks where to place next unique element.
- Since array is sorted, a new unique element is found when `nums[read] != nums[read-1]`.
- Write it at write pointer and increment.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class RemoveDuplicates {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }

        int writeIndex = 1; // First element is always unique

        for (int readIndex = 1; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != nums[readIndex - 1]) {
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

Input: `nums = [1,1,2]`

| readIndex | nums[readIndex] | != nums[read-1]? | writeIndex | nums becomes |
|-----------|------------------|-------------------|------------|--------------|
| 1         | 1                | 1 == 1 → ❌       | 1          | [1,1,2]      |
| 2         | 2                | 2 != 1 → ✅       | 2          | [1,2,2]      |

→ Return `2`. First 2 elements: `[1,2]` → correct.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — one pass.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Log deduplication, database index compaction, UI list rendering (remove duplicate items).
- **Interview pattern**: **Read/Write Pointers on Sorted Array** — appears in Remove Element, Move Zeroes.
- **Why companies ask this**: Tests if you can leverage sorted property, avoid O(n²), and handle edge cases.

> 💡 Uber: “Deduplicate sorted ride logs.”  
> 💡 Android: “Remove duplicate contacts in sorted list.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Starting writeIndex at 0** → first element is always unique → start at 1.

- **Not handling empty array** → return 0.

- **Comparing with `nums[writeIndex-1]`** → should compare with previous read element.

```java
// 🚫 WRONG
if (nums[readIndex] != nums[writeIndex - 1])

// ✅ CORRECT
if (nums[readIndex] != nums[readIndex - 1])
```

- **Using nested loops** → O(n²) → unnecessary.

---

## ✅ STEP 8: Edge Cases to Test

```java
removeDuplicates([1,1,2]) → 2, [1,2]
removeDuplicates([0,0,1,1,1,2,2,3,3,4]) → 5, [0,1,2,3,4]
removeDuplicates([1]) → 1, [1]
removeDuplicates([]) → 0
removeDuplicates([1,2,3]) → 3, [1,2,3]
```

---

## 🧠 BONUS: Why This Works

Because the array is **sorted**, all duplicates are **adjacent**.  
So we only need to compare each element with its **immediate predecessor** to detect uniqueness.

→ This is more efficient than using a HashSet (which would be O(n) space).

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Removes duplicates from sorted array in-place.
 * Leverages sorted property — duplicates are adjacent.
 * O(n) time, O(1) space.
 * @param nums sorted array of integers
 * @return new length after removing duplicates
 */
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }

    int writeIndex = 1; // First element is always unique

    // Start from second element
    for (int readIndex = 1; readIndex < nums.length; readIndex++) {
        // If current element is different from previous, it's unique
        if (nums[readIndex] != nums[readIndex - 1]) {
            nums[writeIndex] = nums[readIndex];
            writeIndex++;
        }
    }

    return writeIndex; // New logical length
}
```

---

