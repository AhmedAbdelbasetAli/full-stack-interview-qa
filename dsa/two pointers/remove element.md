

---

## 🎯 PROBLEM: Remove Element

> Given an integer array `nums` and a value `val`, remove all instances of `val` in-place.  
> The relative order of the elements may be changed.  
> Return the new length.  
> Do not allocate extra space for another array — modify input array in-place with O(1) extra memory.

**Example**:  
Input: `nums = [3,2,2,3]`, `val = 3`  
Output: `2`, and `nums = [2,2,...]` (first 2 elements are `[2,2]`)

---

## 🧠 STEP 1: Understand the Problem

- Remove all occurrences of `val`.
- **In-place** — no new array.
- Return **new length** — elements beyond that don’t matter.
- Order of remaining elements **doesn’t need to be preserved** (but we’ll keep it for clarity).

---

## 🧩 STEP 2: Why Read/Write Pointers?

Because:

- **Read pointer** scans entire array.
- **Write pointer** tracks where to place next valid element.
- If element ≠ `val`, write it at write pointer and increment.
- Return write pointer as new length.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class RemoveElement {
    public int removeElement(int[] nums, int val) {
        int writeIndex = 0; // Tracks where to write next valid element

        for (int readIndex = 0; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != val) {
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

Input: `nums = [3,2,2,3]`, `val = 3`

| readIndex | nums[readIndex] | != val? | writeIndex | nums becomes         |
|-----------|------------------|---------|------------|----------------------|
| 0         | 3                | ❌      | 0          | [3,2,2,3]            |
| 1         | 2                | ✅      | 1          | [2,2,2,3]            |
| 2         | 2                | ✅      | 2          | [2,2,2,3]            |
| 3         | 3                | ❌      | 2          | [2,2,2,3]            |

→ Return `2`. First 2 elements: `[2,2]` → correct.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — one pass.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Log filtering (remove error codes), data compaction (remove nulls), game dev (remove dead entities).
- **Interview pattern**: **Read/Write Pointers** — appears in Remove Duplicates, Move Zeroes, Partition Array.
- **Why companies ask this**: Tests if you can optimize in-place, avoid O(n²), and handle edge cases.

> 💡 Uber: “Remove canceled rides from batch list.”  
> 💡 Game Dev: “Remove dead enemies from entity list.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Shifting elements** → O(n²) → don’t do it.

```java
// 🚫 WRONG — O(n²)
for (int i = 0; i < nums.length; i++) {
    if (nums[i] == val) {
        // shift all elements left
    }
}
```

- **Creating new array** → violates “in-place” constraint.

- **Not returning correct length** → e.g., returning `nums.length` or `writeIndex - 1`.

- **Off-by-one in loop** → always test with edge cases.

---

## ✅ STEP 8: Edge Cases to Test

```java
removeElement([], 1) → 0
removeElement([1,2,3], 4) → 3, [1,2,3]
removeElement([3,3,3], 3) → 0, [?,?,?]
removeElement([5], 5) → 0
removeElement([5], 3) → 1, [5]
```

---

## 🧠 BONUS: Why This Pattern Matters

This **read/write two-pointer** pattern is used in:

- `std::remove` in C++
- `ArrayList.removeAll()` internally (in some impls)
- Database row compaction after deletes
- Log file cleanup (remove expired entries)

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Removes all instances of `val` from `nums` in-place.
 * @param nums array of integers
 * @param val value to remove
 * @return new length of array after removal
 */
public int removeElement(int[] nums, int val) {
    int writeIndex = 0; // Tracks where to place next valid element

    // Traverse array with read pointer
    for (int readIndex = 0; readIndex < nums.length; readIndex++) {
        // If current element is not val, keep it
        if (nums[readIndex] != val) {
            nums[writeIndex] = nums[readIndex];
            writeIndex++; // Only increment when we write
        }
    }

    return writeIndex; // New logical length
}
```

---

