## Problem Understanding

This is the classic **"Plus One"** problem where you need to **add 1 to a large number represented as an array of digits**. Key characteristics:[1][2]

1. **Array represents a number**: Most significant digit first (left to right)
2. **Cannot convert to integer**: Number might be too large (up to 100 digits)
3. **Handle carry propagation**: Just like manual addition, carries flow from right to left
4. **Edge case**: All 9's → need extra digit (e.g., 999 → 1000)

This simulates how you'd add numbers by hand, processing from the **least significant digit** (rightmost) to the **most significant** (leftmost).[3][2][1]

## Optimal Approach: Right-to-Left Carry Propagation

The **most elegant solution** processes digits from right to left, handling carry naturally.[2][1]

### Key Insights

1. **Start from the end**: Add 1 to the last digit first
2. **Early termination**: If no carry, we're done immediately
3. **Carry propagates left**: Only continue if current digit becomes 10
4. **All 9's special case**: If carry persists to the start, need new array

### Why This Approach is Optimal

- **In-place when possible**: Modifies original array, no extra space
- **Early exit**: Stops as soon as no carry (e.g., `[11][12][13]` → done after one step)
- **Efficient**: O(n) time, visits each digit at most once
- **Simple logic**: Mirrors how humans do addition[1]

## Java Solution

```java
public class Solution {
    /**
     * Increments a large integer represented as digit array by one
     * Uses carry propagation from right to left
     * 
     * @param digits - array where digits[i] is the i-th digit (most to least significant)
     * @return array representing the incremented number
     */
    public int[] plusOne(int[] digits) {
        // Edge case: null or empty array
        if (digits == null || digits.length == 0) {
            return new int[]{1};
        }
        
        // Process digits from right to left (least to most significant)
        for (int i = digits.length - 1; i >= 0; i--) {
            // Increment current digit
            digits[i]++;
            
            // If digit is less than 10, no carry needed - we're done!
            if (digits[i] < 10) {
                return digits;
            }
            
            // If digit is 10, set to 0 and carry continues to next iteration
            digits[i] = 0;
        }
        
        // If we exit the loop, all digits were 9 (e.g., [9,9,9] -> [1,0,0,0])
        // Need to create new array with extra digit at front
        int[] result = new int[digits.length + 1];
        result[0] = 1;  // All other digits are already 0 by default
        
        return result;
    }
}
```

### Alternative Cleaner Version

```java
public class SolutionAlternative {
    public int[] plusOne(int[] digits) {
        int n = digits.length;
        
        for (int i = n - 1; i >= 0; i--) {
            // If digit is less than 9, simply increment and return
            if (digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            // Otherwise, set to 0 and continue (carry propagates)
            digits[i] = 0;
        }
        
        // All digits were 9: create new array [1, 0, 0, ..., 0]
        int[] result = new int[n + 1];
        result[0] = 1;
        return result;
    }
}
```

## Detailed Walkthrough

### Example 1: No Carry Beyond First Digit

**Input**: `digits = [11][12][13]` → represents 123

| Step | i | digits[i] | Action | Array State | Return? |
|------|---|-----------|--------|-------------|---------|
| 1 | 2 | 3 | 3 + 1 = 4, no carry | `[11][12][14]` | **Yes** ✓ |

**Output**: `[11][12][14]` (represents 124)

**Key insight**: Since `digits[12] = 4 < 10`, no carry needed → immediate return!

### Example 2: Single Carry

**Input**: `digits = [11][12][15]` → represents 129

| Step | i | digits[i] | Action | Array State | Continue? |
|------|---|-----------|--------|-------------|-----------|
| 1 | 2 | 9 | 9 + 1 = 10 → set to 0, carry | `[11][12]` | Yes (carry) |
| 2 | 1 | 2 | 2 + 1 = 3, no carry | `[11][13]` | **No** ✓ |

**Output**: `[11][13]` (represents 130)

### Example 3: Multiple Carries

**Input**: `digits = [11][15][15]` → represents 199

| Step | i | digits[i] | Action | Array State | Continue? |
|------|---|-----------|--------|-------------|-----------|
| 1 | 2 | 9 | 9 + 1 = 10 → set to 0 | `[1,9,0]` | Yes (carry) |
| 2 | 1 | 9 | 9 + 1 = 10 → set to 0 | `[1,0,0]` | Yes (carry) |
| 3 | 0 | 1 | 1 + 1 = 2, no carry | `[2,0,0]` | **No** ✓ |

**Output**: `[2][0][0]` (represents 200)

### Example 4: All 9's (Edge Case)

**Input**: `digits = [9][9][9]` → represents 999

| Step | i | digits[i] | Action | Array State | Continue? |
|------|---|-----------|--------|-------------|-----------|
| 1 | 2 | 9 | 9 + 1 = 10 → set to 0 | `[9,9,0]` | Yes (carry) |
| 2 | 1 | 9 | 9 + 1 = 10 → set to 0 | `[9,0,0]` | Yes (carry) |
| 3 | 0 | 9 | 9 + 1 = 10 → set to 0 | `[0,0,0]` | Yes (carry) |
| 4 | - | - | Loop exits, carry still exists | - | Create new array |

**Create new array**: `result = [1][0][0][0]` (represents 1000) ✓

**Critical insight**: When loop exits, all digits are 0, and we still have carry → need extra digit!

### Example 5: Single Digit 9

**Input**: `digits = [15]` → represents 9

| Step | i | digits[i] | Action | Array State | Continue? |
|------|---|-----------|--------|-------------|-----------|
| 1 | 0 | 9 | 9 + 1 = 10 → set to 0 | `` | Loop exits |
| 2 | - | - | Create new array | `[11]` | ✓ |

**Output**: `[11]` (represents 10)

## Visual Representation of Carry Propagation

Let's visualize how carry flows for `[11][15][15]`:

```
   1  9  9   (original)
+        1   (add 1 to last digit)
-----------
   1  9 10   (9 + 1 = 10, carry 1)
   1  9  0   (set to 0, carry left)
   
   1  9  0   (after step 1)
+     1      (carry propagates)
-----------
   1 10  0   (9 + 1 = 10, carry 1)
   1  0  0   (set to 0, carry left)
   
   1  0  0   (after step 2)
+  1         (carry propagates)
-----------
   2  0  0   (1 + 1 = 2, no carry) ✓
```

For all 9's `[9][9][9]`:

```
   9  9  9
+        1
-----------
   9  9 10 → 9  9  0 (carry)
+     1
-----------
   9 10  0 → 9  0  0 (carry)
+  1
-----------
  10  0  0 → 0  0  0 (carry remains!)
  
Need new array: [1, 0, 0, 0]
```

## Complexity Analysis

### Time Complexity: O(n)
- **Best case**: O(1) when last digit < 9 (e.g., `[1][2][3]`)
- **Average case**: O(k) where k = number of trailing 9's
- **Worst case**: O(n) when all digits are 9[2][1]

### Space Complexity: O(1) or O(n)
- **Best/Average case**: O(1) - modifies array in-place
- **Worst case**: O(n) - creates new array when all 9's[1]

## Why This Approach is Optimal

### 1. **Cannot Do Better Than O(n)**
In worst case (all 9's), must check every digit and create new array.

### 2. **Early Termination**
Most numbers don't end in 9, so algorithm typically exits after 1-2 iterations.[1]

**Statistics**:
- 90% of numbers don't end in 9 → O(1)
- 9% end in one 9 → O(2)
- Only 1% end in two or more 9's

### 3. **In-Place When Possible**
Only allocates new memory when absolutely necessary (all 9's case).

### 4. **Simulates Manual Addition**
Mirrors how humans add numbers, making it intuitive and easy to explain.[2]

## Alternative Approaches (Less Optimal)

### Approach 1: Convert to Number (WRONG)

```java
// WRONG: Doesn't work for large numbers
public int[] plusOneWrong(int[] digits) {
    long num = 0;
    for (int digit : digits) {
        num = num * 10 + digit;  // Overflows for large arrays!
    }
    num++;
    
    // Convert back to array...
}
```

**Problem**: Violates the constraint that number can be **up to 100 digits** (far exceeds `Long.MAX_VALUE`)

### Approach 2: Recursive (Unnecessary)

```java
public int[] plusOneRecursive(int[] digits) {
    return plusOneHelper(digits, digits.length - 1);
}

private int[] plusOneHelper(int[] digits, int index) {
    if (index < 0) {
        // All digits were 9
        int[] result = new int[digits.length + 1];
        result[0] = 1;
        return result;
    }
    
    digits[index]++;
    if (digits[index] < 10) {
        return digits;
    }
    
    digits[index] = 0;
    return plusOneHelper(digits, index - 1);
}
```

**Problems**:
- Unnecessary recursion overhead
- Stack space O(n) in worst case
- More complex than iterative solution

## Edge Cases to Consider

### Case 1: Single Digit (Non-9)
```java
digits = [5]
Output: [6]
```

### Case 2: Single Digit 9
```java
digits = [9]
Output: [1,0]  // Special case!
```

### Case 3: No Trailing 9's
```java
digits = [1,2,3,4,5]
Output: [1,2,3,4,6]  // O(1) operation
```

### Case 4: Some Trailing 9's
```java
digits = [1,2,9,9]
Output: [1,3,0,0]
```

### Case 5: All 9's
```java
digits = [9,9,9,9]
Output: [1,0,0,0,0]  // New array needed
```

### Case 6: Leading 9's (Not Trailing)
```java
digits = [9,9,1,2]
Output: [9,9,1,3]  // Works normally
```

## Common Mistakes to Avoid

### 1. **Not Handling All 9's Case**
```java
// WRONG: Doesn't create new array for all 9's
for (int i = n - 1; i >= 0; i--) {
    digits[i]++;
    if (digits[i] < 10) return digits;
    digits[i] = 0;
}
return digits;  // WRONG! Returns [0,0,0] instead of [1,0,0,0]
```

### 2. **Using % 10 Incorrectly**
```java
// WRONG: Overcomplicates with modulo
digits[i] = (digits[i] + 1) % 10;  // This loses the carry information
```

**Correct**: Use explicit check `if (digits[i] < 10)`.

### 3. **Copying Entire Array Unnecessarily**
```java
// WRONG: Wasteful copying
int[] result = Arrays.copyOf(digits, digits.length);
// ... modify result
```

**Correct**: Modify in-place unless all 9's.

### 4. **Not Handling Null/Empty**
```java
// Missing edge case check
public int[] plusOne(int[] digits) {
    // Should check: if (digits == null || digits.length == 0) return ...
}
```

## Interview Tips

1. **Start with examples**: "Let me trace through  to show how carry works."[4][5][6]

2. **Explain the edge case**: "The tricky case is all 9's like  →, where we need a new array."[6][4]

3. **Mention optimization**: "Most numbers don't end in 9, so this typically runs in O(1) time with early exit."

4. **Discuss space complexity**: "We modify in-place except when all digits are 9."

5. **Compare to alternatives**: "We can't convert to integer because the number might be larger than Long.MAX_VALUE with 100 digits."

6. **Real-world connection**: "This is how arbitrary-precision arithmetic libraries (like Java's BigInteger) work internally."
