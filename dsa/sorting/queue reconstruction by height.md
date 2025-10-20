---

## 🎯 PROBLEM: Queue Reconstruction by Height

> You are given an array of people `people` where `people[i] = [h_i, k_i]`:
> - `h_i` = height of person `i`
> - `k_i` = number of people in front of person `i` who have height **≥ h_i**
>
> Reconstruct and return the queue that matches the given array.

**Examples**:  
- Input: `people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]`  
  Output: `[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]`

---

## 🧠 STEP 1: Understand the Problem

- Each person says: “There are `k` people in front of me who are **at least as tall** as me.”
- We need to **reconstruct the original queue order**.
- **Key insight**: Taller people are **less affected** by shorter people — so insert taller people first.

---

## 🧩 STEP 2: Why Sort + Insert by k?

Because:

1. **Sort people**:
   - **Descending by height** (`h`)
   - **Ascending by k** (if same height)
2. **Insert each person at index `k`** in the result list.
   - Since taller people are inserted first, `k` directly gives the correct position.
   - Shorter people don’t affect taller people’s `k` counts.

→ O(n²) time (due to insert), O(n) space.

✅ **Greedy insertion is optimal**.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class QueueReconstruction {
    public int[][] reconstructQueue(int[][] people) {
        // Sort: descending by height, ascending by k
        Arrays.sort(people, (a, b) -> {
            if (a[0] != b[0]) {
                return b[0] - a[0]; // taller first
            }
            return a[1] - b[1]; // smaller k first
        });

        // Insert each person at index k
        List<int[]> result = new ArrayList<>();
        for (int[] person : people) {
            result.add(person[1], person); // insert at k
        }

        return result.toArray(new int[result.size()][]);
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]`

### Step 1: Sort

```
Sorted: [[7,0], [7,1], [6,1], [5,0], [5,2], [4,4]]
```

### Step 2: Insert by k

| Person   | Insert at | Result                                  |
|----------|-----------|-----------------------------------------|
| [7,0]    | 0         | [[7,0]]                                |
| [7,1]    | 1         | [[7,0], [7,1]]                         |
| [6,1]    | 1         | [[7,0], [6,1], [7,1]]                  |
| [5,0]    | 0         | [[5,0], [7,0], [6,1], [7,1]]           |
| [5,2]    | 2         | [[5,0], [7,0], [5,2], [6,1], [7,1]]    |
| [4,4]    | 4         | [[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]] ✅ |

✅ Matches expected output.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n + n²) — sort + n inserts (each O(n)).
- **Space**: O(n) — result list.

✅ Optimal for this approach.

> 💡 **Note**: In Java, `ArrayList.add(index, element)` is O(n) — but for n=1000, it’s acceptable.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Photo lineup (tall people first), priority queues, UI layout (z-index ordering).
- **Interview pattern**: **Greedy Insertion with Custom Sort** — appears in Russian Doll Envelopes, Max Width Ramp.
- **Why companies ask this**: Tests if you can reverse-engineer a greedy strategy, handle custom sorting, use insertion.

> 💡 Event Planning: “Line up guests by height and priority.”  
> 💡 UI Frameworks: “Order overlapping elements by z-index and priority.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Sorting by ascending height** → shorter people inserted first → `k` becomes invalid.

```java
// 🚫 WRONG — sort by ascending height
Arrays.sort(people, (a, b) -> a[0] - b[0]);

// ✅ CORRECT — sort by descending height
Arrays.sort(people, (a, b) -> b[0] - a[0]);
```

- **Not handling same height correctly** → must sort by `k` ascending.

- **Using array instead of list** → hard to insert at index.

---

## ✅ STEP 8: Edge Cases to Test

```java
reconstructQueue([[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]) → [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
reconstructQueue([[6,0],[5,0],[4,0],[3,2],[2,2],[1,4]]) → [[4,0],[5,0],[2,2],[3,2],[1,4],[6,0]]
reconstructQueue([[1,0]]) → [[1,0]]
reconstructQueue([]) → []
```

---

## 🧠 BONUS: Why Insert Taller People First?

Because:

- Taller people’s `k` values are **not affected** by shorter people.
- When we insert a shorter person at index `k`, it doesn’t change the `k` of taller people already placed.
- This **decouples the problem** — we can greedily insert in sorted order.

✅ This is the **key insight** that makes the solution work.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Reconstructs queue from people's [height, k] pairs.
 * Sorts by descending height, ascending k — then inserts at k.
 * O(n²) time, O(n) space.
 * @param people array of [h, k] pairs
 * @return reconstructed queue
 */
public int[][] reconstructQueue(int[][] people) {
    // Sort: tallest first, and if same height, smaller k first
    Arrays.sort(people, (a, b) -> {
        if (a[0] == b[0]) {
            return a[1] - b[1]; // ascending k
        }
        return b[0] - a[0]; // descending height
    });

    // Insert each person at position k
    List<int[]> queue = new ArrayList<>();
    for (int[] person : people) {
        queue.add(person[1], person);
    }

    return queue.toArray(new int[queue.size()][]);
}
```
