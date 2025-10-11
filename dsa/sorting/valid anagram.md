---

## 🎯 PROBLEM: Valid Anagram

> Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.  
> An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

**Examples**:  
- Input: `s = "anagram"`, `t = "nagaram"` → Output: `true`  
- Input: `s = "rat"`, `t = "car"` → Output: `false`

---

## 🧠 STEP 1: Understand the Problem

- Two strings are anagrams if they have:
  - Same length
  - Same characters with same frequencies
- Order doesn’t matter — only character counts.

---

## 🧩 STEP 2: Why Sorting Works

Because:

- If two strings are anagrams, their **sorted versions are identical**.
- Sorting is O(n log n), but simple and clean for small strings.

→ Alternative: Use **HashMap to count frequencies** — O(n) time.

We’ll show **both** — but **sorting is the simplest** for interviews.

---

## ✍️ STEP 3: Code It — Clean Java (Sorting Approach)

```java
import java.util.*;

public class ValidAnagram {
    public boolean isAnagram(String s, String t) {
        // Edge case: different lengths → not anagrams
        if (s.length() != t.length()) {
            return false;
        }

        // Convert to char arrays and sort
        char[] sArray = s.toCharArray();
        char[] tArray = t.toCharArray();
        Arrays.sort(sArray);
        Arrays.sort(tArray);

        // Compare sorted arrays
        return Arrays.equals(sArray, tArray);
    }
}
```

---

## ✍️ STEP 4: Code It — HashMap Approach (O(n))

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }

    Map<Character, Integer> count = new HashMap<>();

    // Count characters in s
    for (char c : s.toCharArray()) {
        count.put(c, count.getOrDefault(c, 0) + 1);
    }

    // Subtract characters in t
    for (char c : t.toCharArray()) {
        count.put(c, count.getOrDefault(c, 0) - 1);
        if (count.get(c) == 0) {
            count.remove(c);
        }
    }

    return count.isEmpty();
}
```

---

## 🧪 STEP 5: Trace Through Example

Input: `s = "anagram"`, `t = "nagaram"`

### Sorting Approach:

- `sArray` sorted → `['a','a','a','g','m','n','r']`
- `tArray` sorted → `['a','a','a','g','m','n','r']`
- Equal → `true` ✅

### HashMap Approach:

- Count for `s`: `{a:3, n:1, g:1, r:1, m:1}`
- After processing `t`: all counts zero → map empty → `true` ✅

---

## ⏱️ STEP 6: Complexity Analysis

| Approach       | Time          | Space     | Notes                                  |
|----------------|---------------|-----------|----------------------------------------|
| **Sorting**    | O(n log n)    | O(n)      | Simple, readable                       |
| **HashMap**    | O(n)          | O(1)*     | *O(26) for lowercase letters → O(1)   |

✅ **HashMap is optimal** for large strings.  
✅ **Sorting is preferred** for interviews (cleaner code).

---

## 💡 STEP 7: Real-World / Interview Relevance

- **Used in**: Text processing, plagiarism detection, DNA sequence analysis, cryptography.
- **Interview pattern**: **String Anagram Detection** — appears in Group Anagrams, Find All Anagrams in a String.
- **Why companies ask this**: Tests if you know multiple approaches, handle edge cases, optimize.

> 💡 Bioinformatics: “Check if two DNA strands are permutations.”  
> 💡 Security: “Detect anagram-based password reuse.”

---

## 🚫 STEP 8: Common Mistakes / Anti-Patterns

- **Not checking length first** → unnecessary work.

```java
// 🚫 WRONG — sort even if lengths differ
Arrays.sort(sArray);
Arrays.sort(tArray);

// ✅ CORRECT — early return
if (s.length() != t.length()) return false;
```

- **Using `==` for string comparison** → use `.equals()`.

- **Not handling Unicode** → for production, consider `Character` vs `char`.

---

## ✅ STEP 9: Edge Cases to Test

```java
isAnagram("anagram", "nagaram") → true
isAnagram("rat", "car") → false
isAnagram("", "") → true
isAnagram("a", "ab") → false
isAnagram("a", "b") → false
isAnagram("abc", "bca") → true
```

---

## 🧠 BONUS: When to Use Which Approach?

| Scenario                        | Best Choice     | Why                                      |
|--------------------------------|-----------------|------------------------------------------|
| Small strings, interview       | **Sorting**     | Cleaner, less code                       |
| Large strings, performance     | **HashMap**     | O(n) time                                |
| Only lowercase letters         | **Array[26]**   | Even faster than HashMap                 |

### Array[26] Approach (Fastest for lowercase):

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    int[] count = new int[26];
    for (int i = 0; i < s.length(); i++) {
        count[s.charAt(i) - 'a']++;
        count[t.charAt(i) - 'a']--;
    }
    for (int c : count) {
        if (c != 0) return false;
    }
    return true;
}
```

→ O(n) time, O(1) space — **optimal for lowercase letters**.

---

## ✅ FINAL CODE — Sorting (Interview Favorite)

```java
/**
 * Checks if two strings are anagrams using sorting.
 * O(n log n) time, O(n) space.
 * @param s first string
 * @param t second string
 * @return true if anagrams, false otherwise
 */
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }
    char[] sArray = s.toCharArray();
    char[] tArray = t.toCharArray();
    Arrays.sort(sArray);
    Arrays.sort(tArray);
    return Arrays.equals(sArray, tArray);
}
```

---
