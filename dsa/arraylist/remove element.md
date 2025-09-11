
---

## 🎯 PROBLEM: Remove Element

> Given an integer array `nums` and a value `val`, remove all instances of `val` in-place.  
> Return the new length.  
> Order of remaining elements doesn’t matter.  
> Do not allocate extra space for another array — modify input array in-place with O(1) extra memory.

**Example**:  
Input: `nums = [3,2,2,3]`, `val = 3`  
Output: `2` (length), and `nums = [2,2,?,?]` — where `?` = don’t care.

---

## 🧠 STEP 1: Understand the Problem

- We’re working **in-place** → no new array.
- We can **ignore elements beyond the returned length** → no need to erase or shift everything.
- We care about **efficiency** → O(n) time, O(1) space.
- Order of remaining elements **doesn’t matter** → gives us flexibility.

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Brute Force (Don’t Do This)
- Loop, find `val`, shift all elements left → O(n²) → too slow.

### ✅ Two Pointers — The Elegant Way
- Use a **write pointer** and a **read pointer**.
- Read pointer scans entire array.
- If element ≠ `val`, write it at write pointer, then increment write pointer.
- Return write pointer as new length.

→ This is **in-place, O(n), one pass, minimal writes**.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class RemoveElement {
    public int removeElement(int[] nums, int val) {
        int writeIndex = 0; // where to write next valid element

        for (int readIndex = 0; readIndex < nums.length; readIndex++) {
            if (nums[readIndex] != val) {
                nums[writeIndex] = nums[readIndex];
                writeIndex++;
            }
        }

        return writeIndex; // new logical length
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

- **Used in**: Compacting arrays after filtering (e.g., remove invalid entries, nulls, flagged items).
- **Interview pattern**: **Two Pointers (Read/Write)** — appears in Remove Duplicates, Move Zeroes, Partition.
- **Why companies ask this**: Tests if you can optimize in-place, avoid O(n²), and handle edge cases.

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Shifting elements** → O(n²) → don’t do it.
- **Creating new array** → violates “in-place” constraint.
- **Not returning correct length** → e.g., returning `nums.length` or `writeIndex - 1`.
- **Off-by-one in loop** → always test with edge cases.

---

## ✅ STEP 8: Edge Cases to Test

```java
// Empty array
removeElement([], 1) → 0

// No target
removeElement([1,2,3], 4) → 3, [1,2,3]

// All targets
removeElement([3,3,3], 3) → 0, [?,?,?]

// Single element match
removeElement([5], 5) → 0

// Single element no match
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

