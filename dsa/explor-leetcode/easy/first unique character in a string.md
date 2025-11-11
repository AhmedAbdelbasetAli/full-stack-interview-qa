## Problem Understanding

This is the **"First Unique Character in a String"** problem where you need to find the **first character that appears exactly once** and return its index. Key points:[1][2][3]

1. **First** unique character - order matters
2. **Unique** means appears exactly once (frequency = 1)
3. Return **index**, not the character itself
4. Return **-1** if no unique character exists

## Optimal Solution: Two-Pass HashMap

The **most efficient and commonly used approach** uses a HashMap to count frequencies, then scans to find the first unique character.[4][5][1]

### The Strategy

**Two-pass approach**:[3][1]
1. **First pass**: Count frequency of each character using HashMap
2. **Second pass**: Iterate through string to find first character with frequency = 1

### Why This Works

- **HashMap** provides O(1) lookup for character frequencies[6]
- **Two passes** maintain original order while checking uniqueness
- **First iteration** finds the leftmost unique character[1]

### Why HashMap Over Array?

While we could use an array of size 26 (since only lowercase letters), HashMap is more general and equally efficient for this problem size.[3][1]

## Java Solution

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Finds the index of the first non-repeating character in a string
     * Uses HashMap for frequency counting and two-pass approach
     * 
     * @param s - input string of lowercase English letters
     * @return index of first unique character, or -1 if none exists
     */
    public int firstUniqChar(String s) {
        // Edge case: empty string
        if (s == null || s.length() == 0) {
            return -1;
        }
        
        // Step 1: Build frequency map
        Map<Character, Integer> freqMap = new HashMap<>();
        
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        // Step 2: Find first character with frequency 1
        for (int i = 0; i < s.length(); i++) {
            if (freqMap.get(s.charAt(i)) == 1) {
                return i;  // Found first unique character
            }
        }
        
        // No unique character found
        return -1;
    }
}
```

## Detailed Walkthrough

### Example 1: `s = "leetcode"`

**Step 1: Build Frequency Map**

| Pass | Character | Frequency Map |
|------|-----------|---------------|
| 1 | 'l' | `{l:1}` |
| 2 | 'e' | `{l:1, e:1}` |
| 3 | 'e' | `{l:1, e:2}` |
| 4 | 't' | `{l:1, e:2, t:1}` |
| 5 | 'c' | `{l:1, e:2, t:1, c:1}` |
| 6 | 'o' | `{l:1, e:2, t:1, c:1, o:1}` |
| 7 | 'd' | `{l:1, e:2, t:1, c:1, o:1, d:1}` |
| 8 | 'e' | `{l:1, e:3, t:1, c:1, o:1, d:1}` |

**Step 2: Find First Unique**

| Index | Character | Frequency | Unique? | Action |
|-------|-----------|-----------|---------|--------|
| 0 | 'l' | 1 | **Yes** | **Return 0** ✓ |

**Output**: `0`

### Example 2: `s = "loveleetcode"`

**Frequency Map**: `{l:2, o:2, v:1, e:4, t:1, c:1, d:1}`

**Second Pass**:

| Index | Character | Frequency | Unique? |
|-------|-----------|-----------|---------|
| 0 | 'l' | 2 | No |
| 1 | 'o' | 2 | No |
| 2 | 'v' | 1 | **Yes** → **Return 2** ✓ |

**Output**: `2`

### Example 3: `s = "aabb"`

**Frequency Map**: `{a:2, b:2}`

**Second Pass**:

| Index | Character | Frequency | Unique? |
|-------|-----------|-----------|---------|
| 0 | 'a' | 2 | No |
| 1 | 'a' | 2 | No |
| 2 | 'b' | 2 | No |
| 3 | 'b' | 2 | No |

**No unique character found** → **Return -1** ✓

## Alternative Approach: Array-Based (Optimized for Lowercase Letters)

Since the constraint specifies **only lowercase English letters**, we can use an array of size 26:[3]

```java
public class SolutionArray {
    public int firstUniqChar(String s) {
        // Array to store frequency of each character (a-z)
        int[] freq = new int[26];
        
        // Step 1: Count frequencies
        for (char c : s.toCharArray()) {
            freq[c - 'a']++;
        }
        
        // Step 2: Find first unique character
        for (int i = 0; i < s.length(); i++) {
            if (freq[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        
        return -1;
    }
}
```

**Advantages**:
- Slightly faster (array access vs HashMap)
- Less memory overhead
- Works well for fixed character set[3]

**Disadvantages**:
- Less flexible (only works for lowercase a-z)
- HashMap is more general-purpose

## Comparison of Approaches

| Approach | Time | Space | Best Use Case |
|----------|------|-------|---------------|
| **HashMap (Two-Pass)** | **O(n)** | **O(k)** | **General strings, any characters** |
| Array[7] (Two-Pass) | O(n) | O(1) | Lowercase letters only |
| Nested Loops | O(n²) | O(1) | Small strings, educational |
| LinkedHashMap | O(n) | O(k) | Alternative, maintains insertion order |

**Where**:
- n = length of string
- k = number of unique characters (≤26 for lowercase letters)

## Complexity Analysis

### Time Complexity: O(n)
- **First pass**: O(n) to build frequency map
- **Second pass**: O(n) to find first unique character
- **Total**: O(2n) = O(n)[1]

### Space Complexity: O(k) or O(1)
- **HashMap**: O(k) where k = unique characters (max 26 for lowercase)
- **Effective**: O(1) since k is bounded by 26[1]
- **Array approach**: Explicit O(1) with size 26[3]

## Alternative Approaches (Less Optimal)

### Approach 1: Brute Force (Nested Loops)

```java
public class SolutionBruteForce {
    public int firstUniqChar(String s) {
        for (int i = 0; i < s.length(); i++) {
            boolean isUnique = true;
            
            // Check if s[i] appears anywhere else
            for (int j = 0; j < s.length(); j++) {
                if (i != j && s.charAt(i) == s.charAt(j)) {
                    isUnique = false;
                    break;
                }
            }
            
            if (isUnique) {
                return i;
            }
        }
        
        return -1;
    }
}
```

**Complexity**:[8]
- Time: O(n²) - nested loops
- Space: O(1)

**When to use**: Very small strings or when space is extremely limited.

### Approach 2: Using String Methods

```java
public class SolutionStringMethods {
    public int firstUniqChar(String s) {
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            
            // Check if first and last occurrence are the same
            if (s.indexOf(c) == s.lastIndexOf(c)) {
                return i;
            }
        }
        
        return -1;
    }
}
```

**Complexity**:[5]
- Time: O(n²) - indexOf/lastIndexOf are O(n) each
- Space: O(1)

**Why less optimal**: Multiple string scans for each character.

### Approach 3: LinkedHashMap (Maintains Insertion Order)

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class SolutionLinkedHashMap {
    public int firstUniqChar(String s) {
        Map<Character, Integer> freqMap = new LinkedHashMap<>();
        
        // Count frequencies
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        // Find first unique character
        for (Map.Entry<Character, Integer> entry : freqMap.entrySet()) {
            if (entry.getValue() == 1) {
                // Need to find index in original string
                return s.indexOf(entry.getKey());
            }
        }
        
        return -1;
    }
}
```

**Issue**: Still need to use `indexOf()` to get the actual index, making it less efficient.[4]

## Edge Cases

### Case 1: Empty String
```java
s = ""
Output: -1  // No characters
```

### Case 2: Single Character
```java
s = "a"
Output: 0  // Only one character, it's unique
```

### Case 3: All Same Characters
```java
s = "aaaa"
Output: -1  // No unique characters
```

### Case 4: All Unique Characters
```java
s = "abcdef"
Output: 0  // First character 'a' is unique
```

### Case 5: Unique Character at End
```java
s = "aabbccdd e"
Output: 8  // 'e' at index 8
```

### Case 6: Multiple Unique, Return First
```java
s = "aabbccddeeff"
Output: -1  // All repeat
s = "aabbccdde"
Output: 8  // 'e' is first unique
```

### Case 7: Very Long String
```java
s.length = 100,000
// Still O(n) time with HashMap approach
```

## Common Mistakes to Avoid

### 1. **Returning Character Instead of Index**
```java
// WRONG: Returns character, not index
public char firstUniqChar(String s) {
    // ...
    return s.charAt(i);  // Should return i, not s.charAt(i)
}

// CORRECT: Return index
return i;
```

### 2. **Forgetting to Return -1**
```java
// WRONG: No return statement after loop
for (int i = 0; i < s.length(); i++) {
    if (freqMap.get(s.charAt(i)) == 1) {
        return i;
    }
}
// Missing: return -1;
```

### 3. **Using indexOf() Inefficiently**
```java
// WRONG: O(n²) time complexity
for (char c : s.toCharArray()) {
    if (s.indexOf(c) == s.lastIndexOf(c)) {
        return s.indexOf(c);  // indexOf is O(n)
    }
}
```

### 4. **Not Handling Edge Cases**
```java
// WRONG: No null/empty check
public int firstUniqChar(String s) {
    Map<Character, Integer> map = new HashMap<>();
    for (char c : s.toCharArray()) {  // NPE if s is null
        // ...
    }
}
```

### 5. **LinkedHashMap Confusion**
```java
// WRONG: LinkedHashMap doesn't solve the problem directly
// Still need to iterate through original string to find index
for (Map.Entry<Character, Integer> entry : map.entrySet()) {
    if (entry.getValue() == 1) {
        return entry.getKey();  // Returns char, not index!
    }
}
```

## Interview Tips

1. **Clarify requirements**:
   - "Should I return the character or its index?" (Index)
   - "What if multiple characters are unique?" (Return the first one)
   - "Is the string guaranteed to be lowercase?" (Yes, per constraints)

2. **Explain the two-pass approach**:
   - "First, I'll count character frequencies using a HashMap."
   - "Then, I'll iterate through the string to find the first character with frequency 1."

3. **Walk through example**:
   - Use `"leetcode"` to show frequency map: `{l:1, e:3, t:1, c:1, o:1, d:1}`
   - Show second pass finding 'l' at index 0

4. **Discuss optimization**:
   - "For lowercase letters only, I could use an array of size 26 instead of HashMap."
   - "This would be slightly faster but less general."

5. **Mention edge cases**:
   - "Handles empty strings by returning -1."
   - "Works when all characters repeat (return -1)."
   - "Works when first character is unique (return 0)."

6. **Complexity analysis**:
   - "Time: O(n) - two passes through the string."
   - "Space: O(1) - at most 26 characters stored, constant space."[1]

