---

## 🎯 PROBLEM: Custom Sort String

> You are given two strings `order` and `s`. All characters in `order` are **unique**.  
> Rearrange the characters in `s` so that they appear in the same order as in `order`.  
> Characters not in `order` should be placed at the end in **any order**.

**Examples**:  
- Input: `order = "cba"`, `s = "abcd"` → Output: `"cbad"`  
- Input: `order = "cbafg"`, `s = "abcd"` → Output: `"cbad"`

---

## 🧠 STEP 1: Understand the Problem

- `order` defines the **priority** of characters.
- Characters in `s` that are in `order` must appear **in the same sequence** as in `order`.
- Characters not in `order` can appear **anywhere at the end**.
- All characters are lowercase letters (a-z).

---

## 🧩 STEP 2: Why Counting + Rebuild (Not Sorting)?

Because:

- **Sorting with custom comparator** would be O(n log n).
- But since we only have **26 letters**, we can:
  1. **Count frequency** of each char in `s`.
  2. **Rebuild** string by:
     - First, append chars in `order` (with their counts).
     - Then, append remaining chars (not in `order`).

→ O(n) time, O(1) space.

✅ **Counting is optimal** for small character sets.

---

## ✍️ STEP 3: Code It — Clean Java (Counting Approach)

```java
public class CustomSortString {
    public String customSortString(String order, String s) {
        // Step 1: Count frequency of each char in s
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }

        StringBuilder result = new StringBuilder();

        // Step 2: Append chars in order of 'order'
        for (char c : order.toCharArray()) {
            while (count[c - 'a'] > 0) {
                result.append(c);
                count[c - 'a']--;
            }
        }

        // Step 3: Append remaining chars (not in order)
        for (int i = 0; i < 26; i++) {
            while (count[i] > 0) {
                result.append((char) ('a' + i));
                count[i]--;
            }
        }

        return result.toString();
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `order = "cba"`, `s = "abcd"`

### Step 1: Count frequency

```
count = [1,1,1,1,0,...] → a:1, b:1, c:1, d:1
```

### Step 2: Append in `order` ("cba")

- c: append "c" → count[c]=0
- b: append "b" → count[b]=0
- a: append "a" → count[a]=0
→ result = "cba"

### Step 3: Append remaining

- d: count[d]=1 → append "d"
→ result = "cbad" ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n + m) — n = len(s), m = len(order) ≤ 26.
- **Space**: O(1) — count array of size 26.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: UI rendering (priority order), log processing (custom severity order), data export (custom column order).
- **Interview pattern**: **Counting Sort for Custom Order** — appears in Sort Characters By Frequency, Custom Priority Queue.
- **Why companies ask this**: Tests if you can avoid O(n log n) sort when character set is small, use counting, rebuild efficiently.

> 💡 Android: “Sort contacts by custom priority (VIP first).”  
> 💡 Logging: “Order log levels as [ERROR, WARN, INFO, DEBUG].”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using sorting with custom comparator** → O(n log n) when O(n) is possible.

```java
// 🚫 Overkill for small char set
Arrays.sort(sArray, (a, b) -> ...);
```

- **Not handling characters not in `order`** → missing letters.

- **Using HashMap instead of array** → unnecessary overhead.

```java
// 🚫 Unnecessary
Map<Character, Integer> count = new HashMap<>();

// ✅ Better for a-z
int[] count = new int[26];
```

---

## ✅ STEP 8: Edge Cases to Test

```java
customSortString("cba", "abcd") → "cbad"
customSortString("cbafg", "abcd") → "cbad"
customSortString("kqep", "pekeq") → "kqeep"
customSortString("h", "hhhh") → "hhhh"
customSortString("", "abc") → "abc"
customSortString("abc", "") → ""
```

---

## 🧠 BONUS: When to Use Sorting vs Counting?

| Scenario                        | Best Choice     | Why                                      |
|--------------------------------|-----------------|------------------------------------------|
| Small character set (a-z)      | **Counting**    | O(n) time, O(1) space                    |
| Large character set (Unicode)  | **Sorting**     | Counting array too big                   |
| Need stable sort               | **Sorting**     | Counting doesn’t preserve original order |

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Sorts string 's' according to custom 'order'.
 * Uses counting sort — O(n) time, O(1) space.
 * @param order custom priority order (unique chars)
 * @param s string to sort
 * @return sorted string
 */
public String customSortString(String order, String s) {
    // Count frequency of each char in s
    int[] count = new int[26];
    for (char c : s.toCharArray()) {
        count[c - 'a']++;
    }

    StringBuilder result = new StringBuilder();

    // Append chars in order of 'order'
    for (char c : order.toCharArray()) {
        while (count[c - 'a'] > 0) {
            result.append(c);
            count[c - 'a']--;
        }
    }

    // Append remaining chars (not in order)
    for (int i = 0; i < 26; i++) {
        while (count[i] > 0) {
            result.append((char) ('a' + i));
            count[i]--;
        }
    }

    return result.toString();
}
```

---


What’s next?
