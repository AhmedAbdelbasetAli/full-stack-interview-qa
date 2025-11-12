## Problem Understanding

This is the **"Valid Anagram"** problem where you need to determine if two strings are anagrams of each other.[1][2][3]

**Anagram definition**: Two strings are anagrams if they contain the **exact same characters with the exact same frequencies**, just in a different order.[2][3]

Key insights:
- "anagram" and "nagaram" → both have: a×3, n×1, g×1, r×1, m×1 → **true**
- "rat" and "car" → different characters → **false**
- Order doesn't matter, only character frequencies[2]

## Approach 1: Frequency Count (Optimal - O(n) Time)

The **most efficient approach** uses a frequency counter to track character occurrences.[3][2]

### The Strategy

1. **Quick check**: If lengths differ, cannot be anagrams[2]
2. **Count frequencies**: Count each character in first string
3. **Decrement counts**: Process second string, decrementing counts
4. **Validate**: If any count goes negative or non-zero remains, not anagrams[2]

### Why This is Optimal

- **O(n) time**: Single pass through each string[4][2]
- **O(1) space**: Array of size 26 for lowercase letters[3]
- **Early termination**: Can exit immediately when mismatch found[2]
- **Cannot do better**: Must examine all characters at least once[4]

## Java Solution (Array-Based - Best for Lowercase)

```java
public class Solution {
    /**
     * Checks if two strings are valid anagrams
     * Uses frequency array for O(n) time and O(1) space
     * 
     * @param s - first string
     * @param t - second string
     * @return true if t is anagram of s, false otherwise
     */
    public boolean isAnagram(String s, String t) {
        // Quick check: different lengths cannot be anagrams
        if (s.length() != t.length()) {
            return false;
        }
        
        // Frequency array for 26 lowercase letters
        int[] count = new int[26];
        
        // Count characters in s (increment) and t (decrement) simultaneously
        for (int i = 0; i < s.length(); i++) {
            count[s.charAt(i) - 'a']++;  // Increment for s
            count[t.charAt(i) - 'a']--;  // Decrement for t
        }
        
        // Check if all counts are zero
        for (int c : count) {
            if (c != 0) {
                return false;  // Mismatch in frequency
            }
        }
        
        return true;  // All frequencies match
    }
}
```

## Alternative Solution (HashMap - Better for Unicode)

For the **follow-up question** about Unicode characters:[5][2]

```java
import java.util.HashMap;
import java.util.Map;

public class SolutionHashMap {
    /**
     * Anagram checker using HashMap - works for any characters including Unicode
     * 
     * @param s - first string
     * @param t - second string
     * @return true if anagrams, false otherwise
     */
    public boolean isAnagram(String s, String t) {
        // Different lengths cannot be anagrams
        if (s.length() != t.length()) {
            return false;
        }
        
        // Build frequency map
        Map<Character, Integer> charCount = new HashMap<>();
        
        // Count characters in s
        for (char c : s.toCharArray()) {
            charCount.put(c, charCount.getOrDefault(c, 0) + 1);
        }
        
        // Decrement counts for characters in t
        for (char c : t.toCharArray()) {
            Integer count = charCount.get(c);
            
            // Character doesn't exist or already depleted
            if (count == null || count == 0) {
                return false;
            }
            
            charCount.put(c, count - 1);
        }
        
        // All counts should be zero (optional check, guaranteed by logic)
        return true;
    }
}
```

## Detailed Walkthrough

### Example 1: `s = "anagram"`, `t = "nagaram"`

**Using Array Approach**:

**Step 1**: Length check: `7 == 7` ✓

**Step 2**: Process both strings simultaneously

| i | s[i] | t[i] | s[i]-'a' | t[i]-'a' | count array updates |
|---|------|------|----------|----------|---------------------|
| 0 | 'a' | 'n' | 0 | 13 | `count++, count[11]--` |
| 1 | 'n' | 'a' | 13 | 0 | `count[11]++, count--` |
| 2 | 'a' | 'g' | 0 | 6 | `count++, count[12]--` |
| 3 | 'g' | 'a' | 6 | 0 | `count[12]++, count--` |
| 4 | 'r' | 'r' | 17 | 17 | `count[13]++, count[13]--` |
| 5 | 'a' | 'a' | 0 | 0 | `count++, count--` |
| 6 | 'm' | 'm' | 12 | 12 | `count[14]++, count[14]--` |

**Step 3**: Check array - all values are 0 ✓

**Output**: `true`

### Example 2: `s = "rat"`, `t = "car"`

**Step 1**: Length check: `3 == 3` ✓

**Step 2**: Process strings

| i | s[i] | t[i] | Action | count array |
|---|------|------|--------|-------------|
| 0 | 'r' | 'c' | `count[13]++, count[15]--` | `[0,...,0,-1,0,...,0,1,...]` |
| 1 | 'a' | 'a' | `count++, count--` | `[0,...,0,-1,0,...,0,1,...]` |
| 2 | 't' | 'r' | `count[16]++, count[13]--` | `[0,...,-1,0,...,0,0,...,1]` |

**Step 3**: Check array - has non-zero values: `count[15]=-1, count[16]=1` ✗

**Output**: `false`

### Visual Representation

```
"anagram" vs "nagaram"

Frequency analysis:
a: 3 vs 3 ✓
n: 1 vs 1 ✓
g: 1 vs 1 ✓
r: 1 vs 1 ✓
m: 1 vs 1 ✓

All match → true
```

```
"rat" vs "car"

Frequency analysis:
r: 1 vs 1 ✓
a: 1 vs 1 ✓
t: 1 vs 0 ✗
c: 0 vs 1 ✗

Mismatch → false
```

## Approach 2: Sorting (O(n log n) Time)

A simpler but less efficient approach sorts both strings and compares them:[6][3]

```java
import java.util.Arrays;

public class SolutionSorting {
    public boolean isAnagram(String s, String t) {
        // Different lengths cannot be anagrams
        if (s.length() != t.length()) {
            return false;
        }
        
        // Convert to char arrays and sort
        char[] sChars = s.toCharArray();
        char[] tChars = t.toCharArray();
        
        Arrays.sort(sChars);
        Arrays.sort(tChars);
        
        // Compare sorted arrays
        return Arrays.equals(sChars, tChars);
    }
}
```

**Complexity**:[6][3]
- Time: O(n log n) - dominated by sorting
- Space: O(n) - for char arrays

**When to use**: Quick implementation, code simplicity over performance.

## Comparison of Approaches

| Approach | Time | Space | Best For |
|----------|------|-------|----------|
| **Frequency Array** | **O(n)** | **O(1)** | **Lowercase letters (a-z)** |
| HashMap | O(n) | O(k)* | Unicode, mixed case, any characters |
| Sorting | O(n log n) | O(n) | Simple implementation |
| Brute Force | O(n²) | O(n) | Educational only |

**Where k = number of unique characters*

## Complexity Analysis

### Frequency Array Approach

**Time Complexity: O(n)**[4][2]
- First loop: O(n) to process both strings
- Second loop: O(26) = O(1) to check counts
- Total: O(n)

**Space Complexity: O(1)**[3]
- Array of size 26 (constant)
- Independent of input size

### HashMap Approach

**Time Complexity: O(n)**[2]
- O(n) to build map
- O(n) to validate

**Space Complexity: O(k)** where k ≤ n
- Stores unique characters
- For lowercase: O(1) effective
- For Unicode: O(k) where k = unique chars

## Follow-Up: Unicode Characters

**Question**: What if inputs contain Unicode characters?

**Answer**: Use **HashMap instead of array**[5]

**Why HashMap for Unicode**:
- Unicode has 143,859 characters (as of Unicode 15.0)
- Array of this size would be wasteful
- HashMap only stores characters actually present[5]

**Example with Unicode**:
```java
s = "café"  // with é (U+00E9)
t = "éfac"

// Array approach: would need 65,536+ size array (impractical)
// HashMap approach: only stores {c, a, f, é} → 4 entries
```

## Edge Cases

### Case 1: Empty Strings
```java
s = "", t = ""
Output: true  // Both empty, same frequencies
```

### Case 2: Single Character
```java
s = "a", t = "a"
Output: true

s = "a", t = "b"
Output: false
```

### Case 3: Different Lengths
```java
s = "abc", t = "abcd"
Output: false  // Early exit in length check
```

### Case 4: Same Characters, Different Frequencies
```java
s = "aab", t = "abb"
Output: false  // a:2 vs a:1
```

### Case 5: All Same Character
```java
s = "aaaa", t = "aaaa"
Output: true
```

### Case 6: Long Strings
```java
s.length = 50,000
// Still O(n) with frequency approach
```

## Common Mistakes to Avoid

### 1. **Forgetting Length Check**
```java
// WRONG: Wastes time processing strings of different lengths
public boolean isAnagram(String s, String t) {
    int[] count = new int[26];
    // ... process without checking lengths first
}

// CORRECT: Early exit for different lengths
if (s.length() != t.length()) return false;
```

### 2. **Wrong Array Index Calculation**
```java
// WRONG: Incorrect offset
count[s.charAt(i) - 'A']++;  // Wrong if lowercase

// CORRECT: Subtract 'a' for lowercase
count[s.charAt(i) - 'a']++;
```

### 3. **Not Handling Case Sensitivity**
```java
// WRONG: 'A' and 'a' treated as different
// If problem requires case-insensitive:
s = s.toLowerCase();
t = t.toLowerCase();
```

### 4. **Using Array for Unicode**
```java
// WRONG: Array too large for Unicode
int[] count = new int[65536];  // Wasteful!

// CORRECT: Use HashMap for Unicode
Map<Character, Integer> count = new HashMap<>();
```

### 5. **Incorrect Null Check**
```java
// WRONG: No null check
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {  // NPE if s or t is null
        return false;
    }
}

// CORRECT: Check for null
if (s == null || t == null) return false;
```

## Interview Tips

1. **Clarify requirements**:
   - "Are the strings only lowercase?" (Important for choosing approach)
   - "Should I consider case sensitivity?" (Usually not mentioned)
   - "Can there be spaces or special characters?" (Per constraints: only letters)

2. **Explain the frequency approach**:
   - "Two strings are anagrams if they have identical character frequencies."
   - "I'll use an array to count frequencies for O(n) time and O(1) space."

3. **Walk through example**:
   - Use "anagram" vs "nagaram"
   - Show frequency counting: a=3, n=1, g=1, r=1, m=1
   - Demonstrate how counts match

4. **Discuss optimization**:
   - "I can process both strings simultaneously, incrementing for s and decrementing for t."
   - "This reduces code and maintains O(n) time."

5. **Address the follow-up**:
   - "For Unicode, I'd switch to HashMap since Unicode has 100,000+ characters."
   - "HashMap provides O(n) time with O(k) space where k is unique characters."

6. **Compare alternatives**:
   - "Could sort both strings in O(n log n), but frequency counting is faster."
   - "Sorting is simpler code but not optimal."[6]

