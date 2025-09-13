

---

## 🎯 PROBLEM: Plus One

> Given a **non-empty** array of digits representing a non-negative integer, **increment the integer by one**.  
> The digits are stored such that the **most significant digit is at the head of the list**, and each element is a single digit.  
> Return the array of digits.

**Examples**:  
- `[1,2,3]` → `[1,2,4]`  
- `[4,3,2,1]` → `[4,3,2,2]`  
- `[9,9,9]` → `[1,0,0,0]` ← carry propagates all the way

---

## 🧠 STEP 1: Understand the Problem

- Input: Array of digits → represents a number → e.g., `[1,2,3]` = 123.
- Output: Array of digits → number + 1 → e.g., 124 → `[1,2,4]`.
- **Most significant digit first** → index 0 = leftmost digit.
- **Carry can propagate** → 999 + 1 = 1000 → array grows by 1.
- Must handle **all 9s** → requires new array.

---

## 🧩 STEP 2: Brainstorm Approaches

### ✅ Approach: Process from Right to Left (Like Addition)

> “Start from least significant digit (end of array), add 1, propagate carry.”

- Start at last index.
- Add 1 → if digit < 9 → done.
- If digit == 9 → set to 0, carry = 1 → move left.
- If carry remains at start → insert 1 at beginning.

→ One pass. In-place if no carry overflow. O(n) time.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class PlusOne {
    public int[] plusOne(int[] digits) {
        int n = digits.length;

        // Traverse from right to left
        for (int i = n - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++; // no carry → done
                return digits;
            }
            digits[i] = 0; // 9 becomes 0, carry = 1 (implicit)
        }

        // If we're here → all digits were 9 → need new array
        int[] newDigits = new int[n + 1];
        newDigits[0] = 1; // e.g., [9,9,9] → [1,0,0,0]
        return newDigits;
    }
}
```

---

## 🧪 STEP 4: Trace Through Examples

### Example 1: `[1,2,3]`

- i=2: digit=3 < 9 → 3+1=4 → return `[1,2,4]` ✅

### Example 2: `[9,9,9]`

- i=2: 9 → set to 0 → `[9,9,0]`
- i=1: 9 → set to 0 → `[9,0,0]`
- i=0: 9 → set to 0 → `[0,0,0]`
- Loop ends → create new array `[1,0,0,0]` ✅

### Example 3: `[8,9,9]`

- i=2: 9 → 0 → `[8,9,0]`
- i=1: 9 → 0 → `[8,0,0]`
- i=0: 8 < 9 → 8+1=9 → return `[9,0,0]` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — worst-case traverse entire array.
- **Space**: O(1) if no carry overflow → O(n) if new array needed (but output space doesn’t count in complexity analysis).

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Big integer libraries, financial systems (exact decimal math), embedded systems (BCD arithmetic).
- **Interview pattern**: **In-place array manipulation with carry** — tests edge case handling.
- **Why companies ask this**: Tests if you can handle “grow array” scenario, avoid off-by-one, think backwards.

> 💡 Uber: “Increment ride counter for driver stats.”  
> 💡 Banking: “Add 1 cent to account balance stored as digit array.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Processing left to right** → carry propagates right → must go right to left.

```java
// 🚫 WRONG — can’t handle carry properly
for (int i = 0; i < n; i++) { ... }
```

- **Not handling all-9s case** → returns `[0,0,0]` instead of `[1,0,0,0]`.

- **Using `Integer.parseInt()` or `BigInteger`** → violates spirit of problem (and fails for huge arrays).

```java
// 🚫 DON’T — defeats purpose
String num = "";
for (int d : digits) num += d;
int val = Integer.parseInt(num) + 1; // overflows for large n
```

- **Creating new array always** → wasteful if no carry overflow.

---

## ✅ STEP 8: Edge Cases to Test

```java
plusOne([0]) → [1]
plusOne([1]) → [2]
plusOne([9]) → [1,0]
plusOne([1,9]) → [2,0]
plusOne([9,9]) → [1,0,0]
plusOne([8,9,9,9]) → [9,0,0,0]
plusOne([9,8,9]) → [9,9,0]
```

---

## 🧠 BONUS: Why Right-to-Left?

In addition, **carry propagates from least significant to most significant digit**.

```
  129
+   1
-----
  130 → carry from units → tens
```

→ So we must start from the end (units place) and move left.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Increments a non-negative integer represented as an array of digits by one.
 * Digits are stored with most significant digit first.
 * Handles carry propagation and array growth (e.g., [9,9,9] → [1,0,0,0]).
 * @param digits array of digits (0-9)
 * @return array representing digits + 1
 */
public int[] plusOne(int[] digits) {
    int n = digits.length;

    // Process digits from right (least significant) to left
    for (int i = n - 1; i >= 0; i--) {
        if (digits[i] < 9) {
            digits[i]++; // No carry — we're done
            return digits;
        }
        digits[i] = 0; // 9 becomes 0, carry = 1 (implicit)
    }

    // If we reach here, all digits were 9 → result is 1 followed by n zeros
    int[] result = new int[n + 1];
    result[0] = 1;
    return result;
}
```

---



What’s next?
