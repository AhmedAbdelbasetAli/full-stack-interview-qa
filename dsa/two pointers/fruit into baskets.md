---

## 🎯 PROBLEM: Fruit Into Baskets

> You are visiting a farm that has a single row of fruit trees.  
> You will only pick **one fruit from each tree** (including the start tree) until you cannot pick any more:  
> - You have **two baskets**, and each basket can only hold **one type of fruit**.  
> - There is no limit on the amount of fruit each basket can hold.  
> - Starting from any tree, you must pick **exactly one fruit** from every tree (including the start tree) while moving to the right. The picked fruits must fit in your baskets.  
> - Once you reach a tree with fruit that cannot fit in your baskets, you must stop.  
>
> Given the integer array `fruits`, where `fruits[i]` is the type of fruit the `i-th` tree produces, return the **maximum number of fruits you can collect**.

**Example**:  
Input: `fruits = [1,2,1]`  
Output: `3` → You can collect `[1,2,1]` (types 1 and 2).

Input: `fruits = [0,1,2,2]`  
Output: `3` → You can collect `[1,2,2]` (types 1 and 2).

---

## 🧠 STEP 1: Understand the Problem

- Find the **longest contiguous subarray** with **at most 2 distinct values**.
- This is equivalent to: **Longest Substring with At Most Two Distinct Characters** (but with integers).
- Return the **length** of this subarray.

---

## 🧩 STEP 2: Why Sliding Window + Hash Map?

Because:

- We need to maintain a window with **at most 2 distinct fruit types**.
- Use **two pointers** (`left`, `right`) to represent the window.
- Use a **HashMap** to track fruit type counts in the current window.
- Expand `right` to include new fruits.
- If distinct types > 2, shrink `left` until back to 2 types.
- Track max window size.

→ O(n) time, O(1) space (HashMap size ≤ 3).

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class FruitIntoBaskets {
    public int totalFruit(int[] fruits) {
        Map<Integer, Integer> fruitCount = new HashMap<>();
        int left = 0;
        int maxLength = 0;

        for (int right = 0; right < fruits.length; right++) {
            // Add fruit at right to window
            fruitCount.put(fruits[right], fruitCount.getOrDefault(fruits[right], 0) + 1);

            // Shrink window while more than 2 types
            while (fruitCount.size() > 2) {
                fruitCount.put(fruits[left], fruitCount.get(fruits[left]) - 1);
                if (fruitCount.get(fruits[left]) == 0) {
                    fruitCount.remove(fruits[left]);
                }
                left++;
            }

            // Update max length
            maxLength = Math.max(maxLength, right - left + 1);
        }

        return maxLength;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `fruits = [1,2,1]`

| right | fruit | fruitCount | size | action | maxLength |
|-------|-------|------------|------|--------|-----------|
| 0     | 1     | {1:1}      | 1    | expand | 1         |
| 1     | 2     | {1:1,2:1}  | 2    | expand | 2         |
| 2     | 1     | {1:2,2:1}  | 2    | expand | 3         |

✅ Output: `3`

Input: `fruits = [0,1,2,2]`

| right | fruit | fruitCount | size | action | maxLength |
|-------|-------|------------|------|--------|-----------|
| 0     | 0     | {0:1}      | 1    | expand | 1         |
| 1     | 1     | {0:1,1:1}  | 2    | expand | 2         |
| 2     | 2     | {0:1,1:1,2:1} | 3 | shrink → remove 0 → {1:1,2:1} | 2 → 3 |
| 3     | 2     | {1:1,2:2}  | 2    | expand | 3         |

✅ Output: `3`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element added/removed once.
- **Space**: O(1) — HashMap stores at most 3 keys.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Session tracking (at most 2 user types), ad targeting (at most 2 categories), real-time analytics (at most 2 event types).
- **Interview pattern**: **Sliding Window with Hash Map for Distinct Count** — appears in Longest Substring with At Most K Distinct Characters.
- **Why companies ask this**: Tests if you can use sliding window with dynamic distinct count, handle HashMap updates, shrink correctly.

> 💡 Uber: “Track user sessions with at most 2 ride types.”  
> 💡 Netflix: “Monitor viewing sessions with at most 2 genres.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not removing zero-count fruits** → HashMap size stays > 2.

```java
// 🚫 WRONG
fruitCount.put(fruits[left], fruitCount.get(fruits[left]) - 1);
left++;

// ✅ CORRECT
fruitCount.put(fruits[left], fruitCount.get(fruits[left]) - 1);
if (fruitCount.get(fruits[left]) == 0) {
    fruitCount.remove(fruits[left]);
}
left++;
```

- **Using `if` instead of `while`** → window not fully shrunk.

- **Not updating max length inside loop** → miss valid windows.

---

## ✅ STEP 8: Edge Cases to Test

```java
totalFruit([1,2,1]) → 3
totalFruit([0,1,2,2]) → 3
totalFruit([1,2,3,2,2]) → 4 ([2,3,2,2])
totalFruit([3,3,3,1,2,1,1,2,3,3,4]) → 5 ([1,2,1,1,2])
totalFruit([1]) → 1
totalFruit([1,1,1,1]) → 4
```

---

## 🧠 BONUS: Why This Works

- The **HashMap size** tracks the number of distinct fruit types.
- **Shrinking from left** ensures we maintain a valid window.
- **Max length** is updated for every valid window.

→ This is the **standard pattern** for “at most K distinct” problems.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds maximum number of fruits you can collect with at most 2 types.
 * Uses sliding window with HashMap — O(n) time, O(1) space.
 * @param fruits array of fruit types
 * @return maximum number of fruits
 */
public int totalFruit(int[] fruits) {
    Map<Integer, Integer> fruitCount = new HashMap<>();
    int left = 0;
    int maxLength = 0;

    // Expand window with right pointer
    for (int right = 0; right < fruits.length; right++) {
        fruitCount.put(fruits[right], fruitCount.getOrDefault(fruits[right], 0) + 1);

        // Shrink window from left while more than 2 types
        while (fruitCount.size() > 2) {
            fruitCount.put(fruits[left], fruitCount.get(fruits[left]) - 1);
            if (fruitCount.get(fruits[left]) == 0) {
                fruitCount.remove(fruits[left]);
            }
            left++;
        }

        // Update max length for valid window
        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}
```

