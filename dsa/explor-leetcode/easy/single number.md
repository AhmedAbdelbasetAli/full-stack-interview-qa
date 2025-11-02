## Problem Understanding

This is a classic **bit manipulation problem** where you need to find the single element that appears once, while all other elements appear exactly twice. The critical constraints are:[1][2]

1. **Linear time complexity**: O(n) - single pass through array
2. **Constant space**: O(1) - can't use HashMap/HashSet
3. Every element appears **exactly twice** except one

These constraints eliminate common approaches like HashMap (O(n) space) and make this the perfect use case for **XOR bit manipulation**.[3][2]

## Optimal Solution: XOR Bit Manipulation

The **XOR (exclusive OR) operator** provides an elegant and mathematically beautiful solution to this problem.[2][4][3]

### The Magic of XOR - Key Properties

The XOR operator has two critical mathematical properties that make this solution work:[4][3][2]

**Property 1: Self-Cancellation**
$$
a \oplus a = 0
$$
Any number XORed with itself equals 0.[5][4]

**Property 2: Identity Element**
$$
a \oplus 0 = a
$$
Any number XORed with 0 remains unchanged.[3][2]

**Property 3: Commutative & Associative**
$$
a \oplus b \oplus c = c \oplus a \oplus b
$$
Order doesn't matter - we can rearrange operations.[3]

### Why This Solves the Problem

When we XOR all elements together:
- **Pairs cancel out**: Each duplicate pair XORs to 0
- **Single remains**: The lone element XORs with 0, leaving itself

Mathematical proof:[3]
$$
\text{nums} = [a, b, a, c, b]
$$
$$
a \oplus b \oplus a \oplus c \oplus b = (a \oplus a) \oplus (b \oplus b) \oplus c = 0 \oplus 0 \oplus c = c
$$

### Java Solution

```java
public class Solution {
    /**
     * Finds the single element that appears once while all others appear twice
     * Uses XOR bit manipulation for O(n) time and O(1) space
     * 
     * Mathematical basis:
     * - a ^ a = 0 (self-cancellation)
     * - a ^ 0 = a (identity)
     * - XOR is commutative and associative
     * 
     * @param nums - array where every element appears twice except one
     * @return the single element that appears only once
     */
    public int singleNumber(int[] nums) {
        // Initialize result to 0 (identity element for XOR)
        int result = 0;
        
        // XOR all elements together
        // Pairs will cancel out to 0, single element remains
        for (int num : nums) {
            result ^= num;  // result = result XOR num
        }
        
        // Return the single number
        return result;
    }
}
```

### Alternative One-Liner (Java 8 Streams)

```java
import java.util.Arrays;

public class SolutionStream {
    public int singleNumber(int[] nums) {
        return Arrays.stream(nums)
                     .reduce(0, (a, b) -> a ^ b);
    }
}
```

This uses the `reduce()` function to apply XOR cumulatively.[3]

## Detailed Walkthrough

### Example 1: `nums = [11][11][12]`

**Step-by-step XOR operations**:

| Step | Current Element | Result Before | Operation | Result After | Binary Explanation |
|------|----------------|---------------|-----------|--------------|-------------------|
| 0 | - | 0 | Initialize | 0 | `0000` |
| 1 | 2 | 0 | 0 ^ 2 | 2 | `0000 ^ 0010 = 0010` |
| 2 | 2 | 2 | 2 ^ 2 | 0 | `0010 ^ 0010 = 0000` |
| 3 | 1 | 0 | 0 ^ 1 | 1 | `0000 ^ 0001 = 0001` |

**Result**: 1 ✓

**Key insight**: The two 2's cancel each other out (2 ^ 2 = 0), leaving just 1.

### Example 2: `nums = [13][12][11][12][11]`

**Step-by-step**:

| Step | Element | Result | Explanation |
|------|---------|--------|-------------|
| 0 | - | 0 | Initialize |
| 1 | 4 | 4 | 0 ^ 4 = 4 |
| 2 | 1 | 5 | 4 ^ 1 = 5 |
| 3 | 2 | 7 | 5 ^ 2 = 7 |
| 4 | 1 | 6 | 7 ^ 1 = 6 |
| 5 | 2 | 4 | 6 ^ 2 = 4 |

**Result**: 4 ✓

**Conceptual rearrangement** (due to commutativity):[3]
$$
4 \oplus 1 \oplus 2 \oplus 1 \oplus 2 = 4 \oplus (1 \oplus 1) \oplus (2 \oplus 2) = 4 \oplus 0 \oplus 0 = 4
$$

### Example 3: `nums = [12]`

**Single element**:
- Result = 0 ^ 1 = 1 ✓

## Binary Visualization

Let's see how XOR works at the bit level for Example 2:

```
  0000 (initial)
^ 0100 (4)
------
  0100

^ 0001 (1)
------
  0101

^ 0010 (2)
------
  0111

^ 0001 (1) -- this cancels the previous 1
------
  0110

^ 0010 (2) -- this cancels the previous 2
------
  0100 (4) ✓
```

Each duplicate pair's bits cancel out, leaving only the single number's bits.[4][5]

## Complexity Analysis

### Time Complexity: O(n)
- Single pass through the array
- Each XOR operation is O(1)
- No nested loops or recursive calls
- Optimal - can't do better than O(n) as we must examine every element[2][5]

### Space Complexity: O(1)
- Only one integer variable (`result`) used
- No additional data structures (no HashMap, HashSet, or arrays)
- Space usage independent of input size
- Satisfies the "constant extra space" requirement[5][2][3]

## Why XOR is the Perfect Solution

### 1. **Mathematically Elegant**
The solution leverages fundamental properties of XOR to solve the problem in a single pass with zero extra space.[4][3]

### 2. **Impossible to Beat**
- Time: O(n) is optimal - must check each element
- Space: O(1) is optimal - can't use less space

### 3. **Hardware Efficient**
XOR is a native CPU instruction, making it extremely fast at the hardware level.[5]

### 4. **No Edge Cases**
Works perfectly for:
- Single element array: `[12]` → 1
- Small arrays: `[11][11][12]` → 1
- Negative numbers: `[-1,-1,5]` → 5
- Large arrays: Scales linearly

## Alternative Approaches (Non-Optimal)

### Approach 1: HashMap (O(n) Space)

```java
import java.util.HashMap;
import java.util.Map;

public class SolutionHashMap {
    public int singleNumber(int[] nums) {
        Map<Integer, Integer> count = new HashMap<>();
        
        // Count occurrences
        for (int num : nums) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }
        
        // Find element with count = 1
        for (Map.Entry<Integer, Integer> entry : count.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }
        
        return -1; // Should never reach here
    }
}
```

**Complexity**: O(n) time, O(n) space
**Problem**: Violates the O(1) space constraint

### Approach 2: HashSet (O(n) Space)

```java
import java.util.HashSet;
import java.util.Set;

public class SolutionHashSet {
    public int singleNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        
        for (int num : nums) {
            if (set.contains(num)) {
                set.remove(num);  // Remove duplicate
            } else {
                set.add(num);  // Add first occurrence
            }
        }
        
        // Set contains only the single element
        return set.iterator().next();
    }
}
```

**Complexity**: O(n) time, O(n) space
**Problem**: Still uses O(n) space

### Approach 3: Sorting (Modifies Input)

```java
import java.util.Arrays;

public class SolutionSorting {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);
        
        // Check pairs
        for (int i = 0; i < nums.length - 1; i += 2) {
            if (nums[i] != nums[i + 1]) {
                return nums[i];
            }
        }
        
        // Last element is single
        return nums[nums.length - 1];
    }
}
```

**Complexity**: O(n log n) time, O(1) space
**Problems**: 
- Slower than XOR
- Modifies input array

### Approach 4: Mathematical Sum

```java
import java.util.HashSet;
import java.util.Set;

public class SolutionMath {
    public int singleNumber(int[] nums) {
        Set<Integer> unique = new HashSet<>();
        int arraySum = 0;
        
        for (int num : nums) {
            unique.add(num);
            arraySum += num;
        }
        
        int uniqueSum = 0;
        for (int num : unique) {
            uniqueSum += num;
        }
        
        // 2 * (sum of unique) - (sum of array) = single number
        return 2 * uniqueSum - arraySum;
    }
}
```

**Complexity**: O(n) time, O(n) space
**Problem**: Still uses extra space for HashSet

## Comparison Table

| Approach | Time | Space | Modifies Input | Meets Requirements |
|----------|------|-------|----------------|-------------------|
| **XOR Bit Manipulation** | **O(n)** | **O(1)** | **No** | **✓ Yes** |
| HashMap | O(n) | O(n) | No | ✗ No (space) |
| HashSet | O(n) | O(n) | No | ✗ No (space) |
| Sorting | O(n log n) | O(1) | Yes | ✗ No (time) |
| Math Sum | O(n) | O(n) | No | ✗ No (space) |

## Understanding XOR Truth Table

For those new to XOR, here's the truth table:[4]

| A | B | A XOR B | Explanation |
|---|---|---------|-------------|
| 0 | 0 | 0 | Same bits → 0 |
| 0 | 1 | 1 | Different bits → 1 |
| 1 | 0 | 1 | Different bits → 1 |
| 1 | 1 | 0 | Same bits → 0 |

**Key takeaway**: XOR returns 1 only when bits are **different**, and 0 when they're the **same**.

## Common Mistakes to Avoid

### 1. **Using Addition/Subtraction**
```java
// WRONG: Won't work with negative numbers or overflow
int result = 0;
for (int num : nums) {
    result += num;  // This doesn't help
}
```

### 2. **Confusing XOR with OR**
```java
// WRONG: Using OR (|) instead of XOR (^)
result |= num;  // This doesn't cancel duplicates
```

### 3. **Initializing to Wrong Value**
```java
// WRONG: Starting with non-zero
int result = 1;  // Should be 0 for XOR identity
```

### 4. **Not Understanding the Constraint**
If elements can appear **more than twice**, XOR won't work. This solution specifically requires exactly two occurrences.[6]

## Interview Tips

1. **Explain the XOR properties first**: "XOR has the property that `a ^ a = 0` and `a ^ 0 = a`, which is perfect for this problem."

2. **Draw binary examples**: Show how XOR cancels bits at the binary level.

3. **Mention why other approaches fail**: "HashMap would work but uses O(n) space, violating the constraint."

4. **Discuss the mathematical elegance**: "This is a classic example of how understanding bit manipulation can transform a seemingly complex problem into a simple one-liner."

5. **Be ready for follow-ups**: 
   - "What if elements appear 3 times?" (Different XOR-based solution required)
   - "What if there are two single numbers?" (XOR them together, then separate)
