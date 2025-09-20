

---

## 🎯 PROBLEM: Reverse String

> Write a function that reverses a string. The input string is given as an array of characters `char[]`.  
> Do not allocate extra space for another array — you must do this by **modifying the input array in-place** with O(1) extra memory.

**Example**:  
Input: `['h','e','l','l','o']`  
Output: `['o','l','l','e','h']`

---

## 🧠 STEP 1: Understand the Problem

- Input: `char[]` — array of characters.
- Output: Same array, but reversed.
- **In-place** — no extra array.
- **O(1) extra memory** — only pointers/temp variables.

---

## 🧩 STEP 2: Why Two Pointers (Collision)?

Because to reverse, we need to:

- Swap first with last.
- Swap second with second-last.
- Continue until pointers meet.

→ Perfect for `left` and `right` pointers moving toward center.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ReverseString {
    public void reverseString(char[] s) {
        int left = 0;
        int right = s.length - 1;

        while (left < right) {
            // Swap s[left] and s[right]
            char temp = s[left];
            s[left] = s[right];
            s[right] = temp;

            // Move pointers toward center
            left++;
            right--;
        }
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `s = ['h','e','l','l','o']`

| Step | left | right | s[left] | s[right] | Action          | Array State       |
|------|------|-------|---------|----------|-----------------|-------------------|
| 1    | 0    | 4     | 'h'     | 'o'      | swap → 'o','h'  | `['o','e','l','l','h']` |
| 2    | 1    | 3     | 'e'     | 'l'      | swap → 'l','e'  | `['o','l','l','e','h']` |
| 3    | 2    | 2     | —       | —        | left == right → break | ✅ Done |

✅ Final: `['o','l','l','e','h']`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n/2) → O(n) — each character swapped once.
- **Space**: O(1) — only two pointers and one temp variable.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Text editors (undo/redo), cryptography (reversing buffers), embedded systems (in-place data manipulation).
- **Interview pattern**: **Two Pointers (Collision) — Pure Swap** — appears in Reverse Words in String, Rotate Array.
- **Why companies ask this**: Tests if you can modify arrays in-place, handle pointers, avoid extra space.

> 💡 Uber: “Reverse driver ID buffer for anonymization.”  
> 💡 Android: “Reverse char array in custom TextView for RTL support.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using extra array** → violates in-place constraint.

```java
// 🚫 WRONG
char[] reversed = new char[s.length];
for (int i = 0; i < s.length; i++) {
    reversed[i] = s[s.length - 1 - i];
}
// ... then copy back? Still extra space!
```

- **Not using temp variable** → data loss.

```java
// 🚫 BROKEN — loses s[left]
s[left] = s[right];
s[right] = s[left]; // now both are s[right]!
```

- **Off-by-one in loop** → `left <= right` → swaps middle with itself (harmless but unnecessary).

```java
// ✅ Better
while (left < right) // stops when left == right
```

- **Forgetting to move pointers** → infinite loop.

---

## ✅ STEP 8: Edge Cases to Test

```java
reverseString(['h','e','l','l','o']) → ['o','l','l','e','h']
reverseString(['H','a','n','n','a','h']) → ['h','a','n','n','a','H']
reverseString(['a']) → ['a']
reverseString([]) → [] (no-op)
reverseString(['a','b']) → ['b','a']
```

---

## 🧠 BONUS: Why Two Pointers Works

Because reversing is **symmetric** — position `i` swaps with position `n-1-i`.

Two pointers let us do this in **one pass**, **in-place**, with **minimal swaps**.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Reverses char array in-place using two pointers.
 * O(n) time, O(1) space.
 * @param s char array to reverse
 */
public void reverseString(char[] s) {
    int left = 0;              // Start at beginning
    int right = s.length - 1;  // Start at end

    while (left < right) {
        // Swap characters at left and right
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;

        // Move pointers toward center
        left++;
        right--;
    }
}
```

---

