---

## 🎯 PROBLEM: Largest Number

> Given a list of non-negative integers `nums`, arrange them such that they form the **largest number** and return it.  
> Since the result may be very large, return it as a **string**.

**Examples**:  
- Input: `nums = [10,2]` → Output: `"210"`  
- Input: `nums = [3,30,34,5,9]` → Output: `"9534330"`  
- Input: `nums = [0,0]` → Output: `"0"` (not `"00"`)

---

## 🧠 STEP 1: Understand the Problem

- **Goal**: Concatenate numbers to form the largest possible number.
- **Key insight**: The optimal order is **not numeric sort** — e.g., `3` > `30` because `"330" > "303"`.
- **Edge case**: All zeros → return `"0"`.

---

## 🧩 STEP 2: Why Custom Comparator?

Because:

- For two numbers `a` and `b`, we should place `a` before `b` if `a + b > b + a` (as strings).
- Example: `a=3`, `b=30` → `"330" > "303"` → so `3` comes before `30`.
- **Sort the array with this comparator**, then concatenate.

→ O(n log n) time, O(n) space.

✅ **Optimal and clean**.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class LargestNumber {
    public String largestNumber(int[] nums) {
        // Convert to strings for easy concatenation
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = String.valueOf(nums[i]);
        }

        // Custom sort: a should come before b if (a+b) > (b+a)
        Arrays.sort(strs, (a, b) -> (b + a).compareTo(a + b));

        // Handle edge case: all zeros
        if (strs[0].equals("0")) {
            return "0";
        }

        // Concatenate result
        StringBuilder result = new StringBuilder();
        for (String s : strs) {
            result.append(s);
        }

        return result.toString();
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [3,30,34,5,9]`

### Step 1: Convert to strings

```
strs = ["3","30","34","5","9"]
```

### Step 2: Custom Sort

Compare pairs:
- `"9" + "5" = "95" > "59"` → 9 before 5
- `"5" + "34" = "534" > "345"` → 5 before 34
- `"34" + "3" = "343" > "334"` → 34 before 3
- `"3" + "30" = "330" > "303"` → 3 before 30

Sorted: `["9","5","34","3","30"]`

### Step 3: Concatenate

→ `"9534330"` ✅

Edge case: `nums = [0,0]` → sorted: `["0","0"]` → first is "0" → return `"0"` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n * m) — n = number of elements, m = average number of digits (for string concatenation in comparator).
- **Space**: O(n * m) — for string array.

✅ Optimal for this problem.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Financial systems (largest transaction ID), competitive programming, ID generation.
- **Interview pattern**: **Custom Comparator for String Concatenation** — appears in Reorganize String, Custom Sort String.
- **Why companies ask this**: Tests if you can define custom ordering, handle edge cases, avoid numeric overflow.

> 💡 Banking: “Generate largest possible transaction ID from segments.”  
> 💡 E-commerce: “Create largest discount code from parts.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Sorting numerically** → `30 > 3` → wrong order.

```java
// 🚫 WRONG — numeric sort
Arrays.sort(nums);

// ✅ CORRECT — custom string comparator
Arrays.sort(strs, (a, b) -> (b+a).compareTo(a+b));
```

- **Not handling all-zero case** → returns `"00"` instead of `"0"`.

- **Using integer comparison** → overflow for large numbers.

---

## ✅ STEP 8: Edge Cases to Test

```java
largestNumber([10,2]) → "210"
largestNumber([3,30,34,5,9]) → "9534330"
largestNumber([0,0]) → "0"
largestNumber([1]) → "1"
largestNumber([111311, 1113]) → "1113111311" (because "1113111113" > "1113111311"? Wait — let's check:
  "111311" + "1113" = "1113111113"
  "1113" + "111311" = "1113111311"
  → "1113111113" > "1113111311" → so [111311, 1113] → "1113111113"
```

---

## 🧠 BONUS: Why `(b + a).compareTo(a + b)`?

Because:

- We want **descending order** by concatenation value.
- If `b + a > a + b`, then `b` should come before `a` → so return negative in comparator.
- `compareTo` returns negative if `b+a < a+b` — so we swap: `(b+a).compareTo(a+b)` gives:
  - Negative if `b+a < a+b` → `a` comes before `b` → wrong.
  - **Wait — let’s clarify**:

Actually, the comparator should return:
- **Negative** if `a` should come before `b`.
- We want `a` before `b` if `a+b > b+a`.
- So: `return (b+a).compareTo(a+b);` → 
  - If `a+b > b+a`, then `b+a < a+b` → `compareTo` returns negative → `a` comes before `b` → correct.

✅ Yes — the code is correct.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Forms the largest number by custom sorting numbers as strings.
 * Uses comparator: a before b if (a+b) > (b+a).
 * O(n log n) time, handles all-zero edge case.
 * @param nums array of non-negative integers
 * @return largest number as string
 */
public String largestNumber(int[] nums) {
    // Convert to strings for concatenation
    String[] strs = Arrays.stream(nums)
                          .mapToObj(String::valueOf)
                          .toArray(String[]::new);

    // Sort: a comes before b if (a+b) > (b+a)
    Arrays.sort(strs, (a, b) -> (b + a).compareTo(a + b));

    // Edge case: all zeros
    if (strs[0].equals("0")) {
        return "0";
    }

    // Build result
    return String.join("", strs);
}
```

---
