

---

## 🎯 PROBLEM: Valid Palindrome

> A phrase is a **palindrome** if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward.  
> Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

**Examples**:  
- Input: `"A man, a plan, a canal: Panama"` → Output: `true`  
- Input: `"race a car"` → Output: `false`  
- Input: `" "` → Output: `true`

---

## 🧠 STEP 1: Understand the Problem

- Ignore **non-alphanumeric** characters (spaces, punctuation, symbols).
- Ignore **case** → convert to lowercase.
- Check if remaining string is same forward and backward.
- Use **two pointers** — no extra space.

---

## 🧩 STEP 2: Why Two Pointers (Collision)?

Because we need to compare **first with last**, **second with second-last**, etc.

- `left` starts at 0, `right` at end.
- Skip non-alphanumeric from both ends.
- Compare lowercase characters.
- If mismatch → return `false`.
- If pointers meet → return `true`.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ValidPalindrome {
    public boolean isPalindrome(String s) {
        int left = 0;
        int right = s.length() - 1;

        while (left < right) {
            // Skip non-alphanumeric from left
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
                left++;
            }
            // Skip non-alphanumeric from right
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
                right--;
            }

            // Compare (case-insensitive)
            if (Character.toLowerCase(s.charAt(left)) != 
                Character.toLowerCase(s.charAt(right))) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `s = "A man, a plan, a canal: Panama"`

### Step-by-Step:

| left char | right char | Action                     | left | right |
|-----------|------------|----------------------------|------|-------|
| 'A'       | 'a'        | both alphanumeric → compare → 'a'=='a' → continue | 1    | 29    |
| ' '       | 'm'        | left skip space            | 2    | 29    |
| 'm'       | 'm'        | 'm'=='m' → continue        | 3    | 28    |
| 'a'       | 'a'        | 'a'=='a' → continue        | 4    | 27    |
| 'n'       | 'n'        | 'n'=='n' → continue        | 5    | 26    |
| ','       | 'a'        | left skip comma            | 6    | 26    |
| ' '       | 'a'        | left skip space            | 7    | 26    |
| 'a'       | 'a'        | 'a'=='a' → continue        | 8    | 25    |
| ...       | ...        | ...                        | ...  | ...   |
| Eventually, left >= right → return `true` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each character visited at most twice (once by left, once by right).
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Text processing, DNA sequence analysis, UI input validation.
- **Interview pattern**: **Two Pointers (Collision) with Skipping** — appears in Valid Palindrome II, Isomorphic Strings.
- **Why companies ask this**: Tests if you can handle edge cases, skip characters, and use two pointers cleanly.

> 💡 Uber: “Validate promo code is palindrome (ignoring case/symbols).”  
> 💡 Android: “Check if user input is palindrome for game logic.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not skipping non-alphanumeric** → fails on punctuation.

```java
// 🚫 WRONG
if (s.charAt(left) != s.charAt(right)) ...

// ✅ CORRECT
while (left < right && !isLetterOrDigit(s.charAt(left))) left++;
```

- **Not converting to same case** → 'A' != 'a'.

```java
// 🚫 WRONG
if (s.charAt(left) != s.charAt(right)) ...

// ✅ CORRECT
if (toLowerCase(leftChar) != toLowerCase(rightChar)) ...
```

- **Off-by-one in skipping loops** → forget `left < right` condition → infinite loop or index out of bounds.

```java
// 🚫 DANGEROUS
while (!isLetterOrDigit(s.charAt(left))) left++; // could go out of bounds

// ✅ SAFE
while (left < right && !isLetterOrDigit(s.charAt(left))) left++;
```

- **Not moving pointers after compare** → infinite loop.

---

## ✅ STEP 8: Edge Cases to Test

```java
isPalindrome("A man, a plan, a canal: Panama") → true
isPalindrome("race a car") → false
isPalindrome(" ") → true
isPalindrome(".,") → true
isPalindrome("0P") → false (0 != p)
isPalindrome("a") → true
isPalindrome("") → true
```

---

## 🧠 BONUS: Why Two Pointers Works

Because a palindrome is **symmetric** — we only need to check mirror positions.

Skipping non-alphanumeric doesn’t break symmetry — we just **ignore noise**.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Checks if string is palindrome, ignoring non-alphanumeric and case.
 * Uses two pointers with skipping — O(n) time, O(1) space.
 * @param s input string
 * @return true if palindrome, false otherwise
 */
public boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;

    while (left < right) {
        // Skip non-alphanumeric characters from left
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
            left++;
        }
        // Skip non-alphanumeric characters from right
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
            right--;
        }

        // Compare characters (case-insensitive)
        if (Character.toLowerCase(s.charAt(left)) != 
            Character.toLowerCase(s.charAt(right))) {
            return false;
        }

        // Move pointers inward
        left++;
        right--;
    }

    return true;
}
```

---
