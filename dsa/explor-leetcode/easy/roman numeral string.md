## Problem Understanding

Convert a **Roman numeral string** to an integer using the rules of Roman numeral subtraction.[1][2]

**Key rule**: If a smaller value appears **before** a larger value, it's subtracted; otherwise, it's added.

## Solution: Left-to-Right Comparison

**Insight**: Compare each numeral with the next one. If current < next, subtract; otherwise, add.[2][3]

```java
class Solution {
    public int romanToInt(String s) {
        // Map Roman symbols to their values
        Map<Character, Integer> romanMap = new HashMap<>();
        romanMap.put('I', 1);
        romanMap.put('V', 5);
        romanMap.put('X', 10);
        romanMap.put('L', 50);
        romanMap.put('C', 100);
        romanMap.put('D', 500);
        romanMap.put('M', 1000);
        
        int total = 0;
        
        for (int i = 0; i < s.length(); i++) {
            int current = romanMap.get(s.charAt(i));
            
            // Check if there's a next character and if current < next
            if (i + 1 < s.length()) {
                int next = romanMap.get(s.charAt(i + 1));
                
                if (current < next) {
                    // Subtraction case: subtract current from total
                    total -= current;
                } else {
                    // Normal case: add current
                    total += current;
                }
            } else {
                // Last character: always add
                total += current;
            }
        }
        
        return total;
    }
}
```

## Example Walkthrough

### Example 1: `s = "III"`

| i | Current | Next | Compare | Action | Total |
|---|---------|------|---------|--------|-------|
| 0 | I(1) | I(1) | 1 < 1? NO | Add 1 | 1 |
| 1 | I(1) | I(1) | 1 < 1? NO | Add 1 | 2 |
| 2 | I(1) | - | Last | Add 1 | 3 |

**Result**: 3 ✓

### Example 2: `s = "LVIII"`

| i | Current | Next | Compare | Action | Total |
|---|---------|------|---------|--------|-------|
| 0 | L(50) | V(5) | 50 < 5? NO | Add 50 | 50 |
| 1 | V(5) | I(1) | 5 < 1? NO | Add 5 | 55 |
| 2 | I(1) | I(1) | 1 < 1? NO | Add 1 | 56 |
| 3 | I(1) | I(1) | 1 < 1? NO | Add 1 | 57 |
| 4 | I(1) | - | Last | Add 1 | 58 |

**Result**: 58 ✓

### Example 3: `s = "MCMXCIV"`

| i | Current | Next | Compare | Action | Total |
|---|---------|------|---------|--------|-------|
| 0 | M(1000) | C(100) | 1000 < 100? NO | Add 1000 | 1000 |
| 1 | C(100) | M(1000) | 100 < 1000? **YES** | **Subtract 100** | **900** |
| 2 | M(1000) | X(10) | 1000 < 10? NO | Add 1000 | 1900 |
| 3 | X(10) | C(100) | 10 < 100? **YES** | **Subtract 10** | **1890** |
| 4 | C(100) | I(1) | 100 < 1? NO | Add 100 | 1990 |
| 5 | I(1) | V(5) | 1 < 5? **YES** | **Subtract 1** | **1989** |
| 6 | V(5) | - | Last | Add 5 | 1994 |

**Result**: 1000 + (1000-100) + (100-10) + (5-1) = 1000 + 900 + 90 + 4 = 1994 ✓

## Why This Works

**Subtraction Principle**: A smaller numeral before a larger one means subtraction.

- We encounter M, then C: since C < M, the C subtracts from the next value
- We encounter C, then M: we subtract C now (not add), which effectively gives us CM = 1000 - 100 = 900 ✓

## Complexity Analysis

- **Time**: O(n) - single pass through the string[2]
- **Space**: O(1) - HashMap has fixed size (7 symbols)[2]

## Alternative: Right-to-Left Comparison

Process from **right to left** and adjust based on whether next (left) is smaller:

```java
class Solution {
    public int romanToInt(String s) {
        Map<Character, Integer> romanMap = new HashMap<>();
        romanMap.put('I', 1);
        romanMap.put('V', 5);
        romanMap.put('X', 10);
        romanMap.put('L', 50);
        romanMap.put('C', 100);
        romanMap.put('D', 500);
        romanMap.put('M', 1000);
        
        int total = 0;
        int prevValue = 0;
        
        // Process from right to left
        for (int i = s.length() - 1; i >= 0; i--) {
            int current = romanMap.get(s.charAt(i));
            
            // If current < previous, subtract; otherwise add
            if (current < prevValue) {
                total -= current;
            } else {
                total += current;
            }
            
            prevValue = current;
        }
        
        return total;
    }
}
```

**Example**: `s = "IV"`
- i=1: V(5), prevValue=0 → 5 >= 0, add 5 → total=5, prevValue=5
- i=0: I(1), prevValue=5 → 1 < 5, subtract 1 → total=4, prevValue=1
- Return 4 ✓

**Advantage**: Slightly cleaner logic, no need to check bounds.

## Edge Cases

| Input | Output | Reason |
|-------|--------|--------|
| "I" | 1 | Single symbol |
| "IV" | 4 | Subtraction case |
| "IX" | 9 | Subtraction case |
| "XL" | 40 | Subtraction case |
| "XC" | 90 | Subtraction case |
| "CD" | 400 | Subtraction case |
| "CM" | 900 | Subtraction case |
| "MMXXIII" | 2023 | Large number |
| "MMMCMXCIX" | 3999 | Maximum in range |

## Interview Tips

1. **Recognize the pattern**:
   - "If a smaller value appears before a larger value, it's subtracted."
   - "Otherwise, values are added."

2. **Explain the comparison logic**:
   - "At each position, compare the current symbol with the next."
   - "If current < next, we subtract; otherwise add."

3. **Trace through example**:
   - Use "MCMXCIV" to show subtraction cases
   - M(1000) + (1000-100) + (100-10) + (5-1) = 1994

4. **Discuss alternatives**:
   - "Right-to-left processing is slightly cleaner."
   - "Could also use an array instead of HashMap for fixed symbols."

5. **Mention the right-to-left approach**:
   - "Going backwards is elegant: if current < previous, subtract."

This problem tests **string parsing**, **conditional logic**, and **understanding of problem constraints**.[1][2]
