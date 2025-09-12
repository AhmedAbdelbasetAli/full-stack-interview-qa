

---

## 🎯 PROBLEM: Contains Duplicate

> Given an integer array `nums`, return `true` if any value appears **at least twice**.  
> Return `false` if every element is distinct.

**Examples**:  
- `[1,2,3,1]` → `true`  
- `[1,2,3,4]` → `false`  
- `[1,1,1,3,3,4,3,2,4,2]` → `true`

---

## 🧠 STEP 1: Understand the Problem

- We don’t care **which** element is duplicate — just if **any** exists.
- We don’t need positions, counts, or which one — just a `true`/`false`.
- Array can be large → efficiency matters.
- Elements are integers — but could be any object in real life.

---

## 🧩 STEP 2: Brainstorm Approaches

### ✅ Approach 1: HashSet — O(n) time, O(n) space

> “As I traverse, if I’ve seen this before → duplicate!”

- Use a `HashSet<Integer>` to track seen elements.
- For each element:
  - If in set → return `true`.
  - Else → add to set.
- If loop ends → return `false`.

→ Fast, clean, one pass.

---

### ✅ Approach 2: Sort + Adjacent Compare — O(n log n) time, O(1) space

> “If I sort, duplicates become neighbors.”

- Sort the array.
- Traverse and compare `nums[i]` with `nums[i-1]`.
- If equal → return `true`.
- If loop ends → return `false`.

→ No extra space (if sort is in-place), but slower.

---

### ❌ Approach 3: Brute Force Nested Loop — O(n²)

```java
for i in 0..n-2
  for j in i+1..n-1
    if nums[i] == nums[j] → return true
```

→ Works, but too slow for large n. Avoid.

---

## ✍️ STEP 3: Code It — Two Clean Java Solutions

### ✅ Solution 1: HashSet (Recommended for Interviews)

```java
import java.util.HashSet;
import java.util.Set;

public class ContainsDuplicate {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();

        for (int num : nums) {
            if (seen.contains(num)) {
                return true; // found duplicate!
            }
            seen.add(num);
        }

        return false; // no duplicates found
    }
}
```

### ✅ Solution 2: Sorting (Use if Space is Critical)

```java
import java.util.Arrays;

public class ContainsDuplicate {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums);

        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == nums[i - 1]) {
                return true;
            }
        }

        return false;
    }
}
```

---

## 🧪 STEP 4: Trace Through Examples

### Example 1: `[1,2,3,1]` → `true`

**HashSet Version**:
- i=0: num=1 → not in set → add → `{1}`
- i=1: num=2 → not in set → add → `{1,2}`
- i=2: num=3 → not in set → add → `{1,2,3}`
- i=3: num=1 → in set → return `true` ✅

**Sorting Version**:
- Sort → `[1,1,2,3]`
- i=1: `1 == 1` → return `true` ✅

---

### Example 2: `[1,2,3,4]` → `false`

**HashSet Version**:
- Add 1,2,3,4 → no duplicates → return `false` ✅

**Sorting Version**:
- Sort → `[1,2,3,4]`
- Compare adjacent → no equals → return `false` ✅

---

## ⏱️ STEP 5: Complexity Analysis

| Approach       | Time          | Space     | Notes                                  |
|----------------|---------------|-----------|----------------------------------------|
| **HashSet**    | O(n)          | O(n)      | Fast, one pass, recommended            |
| **Sorting**    | O(n log n)    | O(1)*     | *If sort is in-place (Arrays.sort is)  |
| **Brute Force**| O(n²)         | O(1)      | Avoid — too slow                       |

✅ **HashSet is the winner** for interviews and real systems — unless memory is extremely tight.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Data validation, deduplication, session ID collision detection.
- **Interview pattern**: **HashSet for O(1) lookup** — appears in Two Sum, Group Anagrams, Longest Substring Without Repeating Chars.
- **Why companies ask this**: Tests if you know when to use a hash set vs sort vs brute force.

> 💡 Uber: “Has this rider ID been seen today?” → HashSet.  
> 💡 Netflix: “Is this movie already in watchlist?” → HashSet.

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not checking `contains` before `add`** → always check first, or you’ll miss the duplicate on first occurrence.

```java
// 🚫 WRONG — adds first, then checks next time
seen.add(num);
if (seen.contains(num)) ... // always true after add!

// ✅ CORRECT
if (seen.contains(num)) return true;
seen.add(num);
```

- **Sorting and not handling edge cases** → array length 0 or 1.

- **Using `List.contains()`** → O(n) per check → O(n²) total → avoid.

```java
// 🚫 SLOW — don’t do this
List<Integer> list = new ArrayList<>();
if (list.contains(num)) ... // O(n) per check
```

---

## ✅ STEP 8: Edge Cases to Test

```java
containsDuplicate([]) → false
containsDuplicate([5]) → false
containsDuplicate([1,1]) → true
containsDuplicate([1,2,3,4,5,6,7,8,9,1]) → true (duplicate at end)
containsDuplicate([1,2,3,4,5]) → false
```

---

## 🧠 BONUS: When to Use Which?

| Scenario                        | Best Choice     | Why                                      |
|--------------------------------|-----------------|------------------------------------------|
| General purpose, fast          | **HashSet**     | O(n) time, clean, simple                 |
| Memory constrained             | **Sorting**     | O(1) space, acceptable O(n log n) time   |
| Small n (< 100)                | Brute force     | Simpler, less overhead                   |
| Need indices of duplicates     | HashMap<value, index> | Store first occurrence index        |

---

## ✅ FINAL CODE — HashSet Version (Recommended)

```java
/**
 * Checks if array contains any duplicate values.
 * @param nums array of integers
 * @return true if any value appears at least twice, false otherwise
 */
public boolean containsDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();

    for (int num : nums) {
        if (seen.contains(num)) {
            return true;
        }
        seen.add(num);
    }

    return false;
}
```

---
