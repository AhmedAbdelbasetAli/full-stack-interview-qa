=

---

## 🎯 PROBLEM: Two Sum II — Input Array Is Sorted

> Given a **1-indexed** array of integers `numbers` that is **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target`.  
> Return the indices of the two numbers, `index1` and `index2`, added by one as an integer array `[index1, index2]`.

**Assumptions**:  
- Exactly one solution exists.  
- You may not use the same element twice.  
- Solution must use **constant space** (no HashMap).

**Example**:  
Input: `numbers = [2,7,11,15]`, `target = 9`  
Output: `[1,2]` → because `numbers[0] + numbers[1] = 2 + 7 = 9`

---

## 🧠 STEP 1: Understand the Problem

- Array is **sorted** → we can use that to our advantage.
- Must return **1-indexed** indices → add 1 to array indices.
- **Exactly one solution** → no need to handle multiple or none.
- **Constant space** → no HashMap → forces us to use two pointers.

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Brute Force — O(n²)

```java
for i in 0..n-2
  for j in i+1..n-1
    if numbers[i] + numbers[j] == target → return [i+1, j+1]
```

→ Works, but slow. Violates “sorted” advantage.

---

### ❌ HashMap — O(n) time, O(n) space

```java
Map<Integer, Integer> map = new HashMap<>();
for i in 0..n-1
  complement = target - numbers[i]
  if map contains complement → return [map.get(complement)+1, i+1]
  else → map.put(numbers[i], i)
```

→ Works, but uses extra space → violates problem constraint.

---

### ✅ Two Pointers (Collision) — O(n) time, O(1) space

> “Start with smallest and largest. If sum too small → move left pointer. If too large → move right pointer.”

- `left = 0`, `right = n-1`
- While `left < right`:
  - `sum = numbers[left] + numbers[right]`
  - If `sum == target` → return `[left+1, right+1]`
  - If `sum < target` → `left++` (need larger sum)
  - If `sum > target` → `right--` (need smaller sum)

→ Leverages sorted property. One pass. No extra space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class TwoSumII {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;

        while (left < right) {
            int sum = numbers[left] + numbers[right];

            if (sum == target) {
                return new int[]{left + 1, right + 1}; // 1-indexed
            } else if (sum < target) {
                left++; // need larger sum
            } else {
                right--; // need smaller sum
            }
        }

        // Unreachable (problem guarantees solution)
        return new int[]{-1, -1};
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `numbers = [2,7,11,15]`, `target = 9`

| Step | left | right | numbers[left] | numbers[right] | sum | Action             | Result     |
|------|------|-------|---------------|----------------|-----|--------------------|------------|
| 1    | 0    | 3     | 2             | 15             | 17  | 17 > 9 → right--   | —          |
| 2    | 0    | 2     | 2             | 11             | 13  | 13 > 9 → right--   | —          |
| 3    | 0    | 1     | 2             | 7              | 9   | 9 == 9 → return    | `[1,2]` ✅ |

✅ Perfect.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each pointer moves at most n steps.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Database range queries, financial pair matching, scientific computing.
- **Interview pattern**: **Two Pointers (Collision)** — appears in 3Sum, Container With Most Water, Valid Palindrome.
- **Why companies ask this**: Tests if you can leverage sorted property, avoid O(n²), use O(1) space.

> 💡 Uber: “Match rider with driver such that ETA sum = target.”  
> 💡 Netflix: “Find two movies whose durations sum to 2 hours.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using 0-indexed return** → problem asks for 1-indexed.

```java
// 🚫 WRONG
return new int[]{left, right};

// ✅ CORRECT
return new int[]{left + 1, right + 1};
```

- **Not leveraging sorted property** → using HashMap or brute force.

- **Off-by-one in loop** → `left <= right` → might use same element.

```java
// 🚫 WRONG — could use same element if left==right
while (left <= right)

// ✅ CORRECT
while (left < right)
```

- **Infinite loop** → forgetting to move pointers.

```java
// 🚫 BROKEN
if (sum == target) return ...;
else if (sum < target) left++; // ✅ good
// else → forgot to move right!
```

---

## ✅ STEP 8: Edge Cases to Test

```java
twoSum([2,7,11,15], 9) → [1,2]
twoSum([2,3,4], 6) → [1,3] (2+4)
twoSum([-1,0], -1) → [1,2] (-1+0)
twoSum([1,2,3,4,5], 8) → [3,5] (3+5)
twoSum([1,1], 2) → [1,2]
```

---

## 🧠 BONUS: Why Two Pointers Works

Because the array is **sorted**, we can make **greedy decisions**:

- If sum is too small → only moving `left` right (to larger values) can help.
- If sum is too large → only moving `right` left (to smaller values) can help.

→ No need to check all pairs — we eliminate half the search space with each move.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds two numbers in a sorted array that add to target.
 * Uses two pointers (collision) — O(n) time, O(1) space.
 * Returns 1-indexed indices.
 * @param numbers sorted array of integers
 * @param target target sum
 * @return [index1, index2] where index1 < index2
 */
public int[] twoSum(int[] numbers, int target) {
    int left = 0;                    // Start at smallest
    int right = numbers.length - 1;  // Start at largest

    while (left < right) {
        int sum = numbers[left] + numbers[right];

        if (sum == target) {
            return new int[]{left + 1, right + 1}; // Convert to 1-indexed
        } else if (sum < target) {
            left++; // Need larger sum → move left pointer right
        } else {
            right--; // Need smaller sum → move right pointer left
        }
    }

    return new int[]{-1, -1}; // Unreachable (problem guarantees solution)
}
```

---

