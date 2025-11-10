## Problem Understanding

This is the **"Reverse Integer"** problem with a critical challenge: **detecting overflow without using 64-bit integers**. The key constraints are:[1][2][3]

1. **32-bit signed integer range**: $$[-2^{31}, 2^{31} - 1]$$ = $$[-2147483648, 2147483647]$$
2. **Return 0 on overflow**: If reversed value exceeds range
3. **Cannot use 64-bit integers**: Must detect overflow using 32-bit arithmetic only[2]
4. **Handle negative numbers**: Preserve sign during reversal

## The Core Challenge: Overflow Detection

### Why Overflow Happens

When reversing `1534236469`:
- Reversed: `9646324351`
- This exceeds `Integer.MAX_VALUE` (2147483647)
- Must return 0[3]

### The Problem with Naive Approaches

```java
// WRONG: This can overflow!
reversed = reversed * 10 + digit;  // May exceed Integer.MAX_VALUE
```

**Critical insight**: We must check for overflow **before** performing `reversed * 10 + digit`.[1][2]

## Optimal Solution: Pre-Overflow Check

The **standard approach** checks if the next operation would cause overflow **before** executing it.[2][1]

### Mathematical Overflow Conditions

For positive numbers, overflow occurs when:[2]
$$
\text{reversed} \times 10 + \text{digit} > 2^{31} - 1
$$

Rearranging to check **before** multiplication:
$$
\text{reversed} > \frac{2^{31} - 1}{10} = 214748364
$$

Or if equal to boundary:
$$
\text{reversed} = 214748364 \text{ AND } \text{digit} > 7
$$

For negative numbers:[2]
$$
\text{reversed} < \frac{-2^{31}}{10} = -214748364
$$

Or if equal:
$$
\text{reversed} = -214748364 \text{ AND } \text{digit} < -8
$$

## Java Solution

```java
public class Solution {
    /**
     * Reverses a 32-bit signed integer with overflow detection
     * Returns 0 if reversed value overflows 32-bit range
     * 
     * @param x - integer to reverse
     * @return reversed integer, or 0 if overflow
     */
    public int reverse(int x) {
        int reversed = 0;
        
        // Constants for 32-bit signed integer boundaries
        int MAX = Integer.MAX_VALUE;  // 2147483647
        int MIN = Integer.MIN_VALUE;  // -2147483648
        
        while (x != 0) {
            // Extract last digit
            int digit = x % 10;
            x /= 10;
            
            // Check for overflow BEFORE performing operation
            // For positive overflow: reversed > MAX/10 
            // or (reversed == MAX/10 and digit > 7)
            if (reversed > MAX / 10 || (reversed == MAX / 10 && digit > 7)) {
                return 0;
            }
            
            // Check for negative overflow: reversed < MIN/10
            // or (reversed == MIN/10 and digit < -8)
            if (reversed < MIN / 10 || (reversed == MIN / 10 && digit < -8)) {
                return 0;
            }
            
            // Safe to perform operation
            reversed = reversed * 10 + digit;
        }
        
        return reversed;
    }
}
```

## Detailed Walkthrough

### Example 1: Simple Positive - `x = 123`

| Step | x | digit | reversed | Check | New reversed |
|------|---|-------|----------|-------|--------------|
| 1 | 123 | 3 | 0 | 0 ≤ 214748364 ✓ | 0×10+3 = 3 |
| 2 | 12 | 2 | 3 | 3 ≤ 214748364 ✓ | 3×10+2 = 32 |
| 3 | 1 | 1 | 32 | 32 ≤ 214748364 ✓ | 32×10+1 = 321 |
| 4 | 0 | - | 321 | Done | - |

**Output**: `321` ✓

### Example 2: Negative Number - `x = -123`

| Step | x | digit | reversed | Check | New reversed |
|------|---|-------|----------|-------|--------------|
| 1 | -123 | -3 | 0 | 0 ≥ -214748364 ✓ | 0×10+(-3) = -3 |
| 2 | -12 | -2 | -3 | -3 ≥ -214748364 ✓ | -3×10+(-2) = -32 |
| 3 | -1 | -1 | -32 | -32 ≥ -214748364 ✓ | -32×10+(-1) = -321 |
| 4 | 0 | - | -321 | Done | - |

**Output**: `-321` ✓

**Note**: Java's `%` operator preserves sign: `-123 % 10 = -3`[4]

### Example 3: Trailing Zeros - `x = 120`

| Step | x | digit | reversed | Check | New reversed |
|------|---|-------|----------|-------|--------------|
| 1 | 120 | 0 | 0 | Safe ✓ | 0×10+0 = 0 |
| 2 | 12 | 2 | 0 | Safe ✓ | 0×10+2 = 2 |
| 3 | 1 | 1 | 2 | Safe ✓ | 2×10+1 = 21 |

**Output**: `21` ✓ (leading zeros dropped naturally)

### Example 4: Overflow Case - `x = 1534236469`

| Step | x | digit | reversed | Check | Result |
|------|---|-------|----------|-------|--------|
| 1 | 1534236469 | 9 | 0 | Safe | 9 |
| 2 | 153423646 | 6 | 9 | Safe | 96 |
| 3 | 15342364 | 4 | 96 | Safe | 964 |
| ... | ... | ... | ... | ... | ... |
| 9 | 1 | 1 | 964632435 | **964632435 > 214748364** ✗ | **Return 0** |

**Output**: `0` ✓ (overflow detected before it happens)

### Visual: Digit Extraction Process

```
x = 123

Step 1:
  digit = 123 % 10 = 3
  x = 123 / 10 = 12
  reversed = 0 * 10 + 3 = 3

Step 2:
  digit = 12 % 10 = 2
  x = 12 / 10 = 1
  reversed = 3 * 10 + 2 = 32

Step 3:
  digit = 1 % 10 = 1
  x = 1 / 10 = 0
  reversed = 32 * 10 + 1 = 321

Step 4:
  x = 0, loop ends
  return 321
```

## Complexity Analysis

### Time Complexity: O(log₁₀(x))
- Number of digits in x: log₁₀(|x|)
- Each iteration processes one digit
- Example: 123 has 3 digits → 3 iterations[3]

### Space Complexity: O(1)
- Only uses constant variables: `reversed`, `digit`, `MAX`, `MIN`
- No arrays, strings, or recursion[3]

## Alternative Approaches

### Approach 1: Using Long (Violates Constraint)

```java
// WRONG: Problem says "cannot use 64-bit integers"
public int reverseUsingLong(int x) {
    long reversed = 0;
    
    while (x != 0) {
        reversed = reversed * 10 + x % 10;
        x /= 10;
    }
    
    // Check after the fact
    if (reversed > Integer.MAX_VALUE || reversed < Integer.MIN_VALUE) {
        return 0;
    }
    
    return (int) reversed;
}
```

**Problem**: Violates the constraint "cannot use 64-bit integers".[5][2]

### Approach 2: String Conversion

```java
public int reverseUsingString(int x) {
    boolean isNegative = x < 0;
    String str = String.valueOf(Math.abs(x));
    String reversed = new StringBuilder(str).reverse().toString();
    
    try {
        int result = Integer.parseInt(reversed);
        return isNegative ? -result : result;
    } catch (NumberFormatException e) {
        return 0;  // Overflow occurred
    }
}
```

**Disadvantages**:[6]
- Less efficient (string operations)
- Relies on exception for overflow (bad practice)
- Uses extra space for strings

## Understanding the Overflow Boundaries

### Why MAX/10 = 214748364?

```java
Integer.MAX_VALUE = 2147483647
MAX / 10 = 214748364 (integer division)
MAX % 10 = 7 (the last digit)
```

**Overflow logic**:
- If `reversed > 214748364`, then `reversed * 10` definitely overflows
- If `reversed == 214748364` and `digit > 7`, then `reversed * 10 + digit > 2147483647`

### Why MIN/10 = -214748364?

```java
Integer.MIN_VALUE = -2147483648
MIN / 10 = -214748364 (integer division)
MIN % 10 = -8 (the last digit)
```

**Underflow logic**:
- If `reversed < -214748364`, then `reversed * 10` definitely underflows
- If `reversed == -214748364` and `digit < -8`, then `reversed * 10 + digit < -2147483648`

## Edge Cases

### Case 1: Zero
```java
x = 0
Output: 0  // No digits to reverse
```

### Case 2: Single Digit
```java
x = 5
Output: 5  // Already reversed
```

### Case 3: Negative Single Digit
```java
x = -5
Output: -5
```

### Case 4: Trailing Zeros
```java
x = 1000
Output: 1  // Leading zeros dropped
```

### Case 5: Maximum Safe Integer
```java
x = 2147483647
Reversed would be: 7463847412
Output: 0  // Overflow
```

### Case 6: Minimum Integer
```java
x = -2147483648
Reversed would be: -8463847412
Output: 0  // Underflow
```

### Case 7: All Nines
```java
x = 999999999
Reversed: 999999999
Output: 999999999  // No overflow
```

## Common Mistakes to Avoid

### 1. **Not Checking Overflow Before Operation**
```java
// WRONG: Overflow can happen here!
reversed = reversed * 10 + digit;
if (reversed > Integer.MAX_VALUE) {  // Too late!
    return 0;
}
```

### 2. **Using Long for Overflow Check**
```java
// WRONG: Violates "cannot use 64-bit integers" constraint
long reversed = 0;
```

### 3. **Wrong Boundary Checks**
```java
// WRONG: Incorrect boundary
if (reversed > Integer.MAX_VALUE / 10) {  // Missing equality case
    return 0;
}

// CORRECT: Must check both > and == cases
if (reversed > MAX / 10 || (reversed == MAX / 10 && digit > 7)) {
    return 0;
}
```

### 4. **Forgetting Negative Numbers**
```java
// WRONG: Only checks positive overflow
if (reversed > Integer.MAX_VALUE / 10) {
    return 0;
}
// Missing: negative overflow check!
```

### 5. **Incorrect Modulo Handling**
```java
// WRONG: Trying to make negative modulo positive
int digit = Math.abs(x % 10);  // Breaks for negative numbers
```

**Correct**: Java's `%` naturally preserves sign.[4]

## Interview Tips

1. **Clarify the constraints**:
   - "Can I use a `long` to check overflow?" (Usually no)
   - "What should I return on overflow?" (0)

2. **Explain the overflow logic**:
   - "I need to check if the next operation would overflow **before** doing it."
   - "I'll compare `reversed` against `MAX/10` and handle the boundary case."

3. **Walk through overflow example**:
   - Show `1534236469` reversing to detect overflow at the last step
   - Demonstrate the boundary check: `964632435 > 214748364`

4. **Discuss the mathematical approach**:
   - "I extract digits using `% 10` and build reversed using `* 10 + digit`."
   - "This avoids string conversion and is O(log n) time, O(1) space."

5. **Mention edge cases**:
   - "Handles negatives naturally since `%` preserves sign in Java."
   - "Trailing zeros disappear automatically as leading zeros."

6. **Compare alternatives**:
   - "Could use `long` but that violates the constraint."
   - "Could use string reversal but it's less efficient."

