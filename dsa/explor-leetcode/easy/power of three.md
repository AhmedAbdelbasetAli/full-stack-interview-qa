## Problem Understanding

Determine if an integer `n` is a **power of three** (n = 3^x for some integer x ≥ 0).[1][2]

Key constraints:
- Powers of three are always positive (3^x > 0 for x ≥ 0)
- Negative numbers and 0 cannot be powers of three
- x must be a non-negative integer

## Solution 1: Division Loop (Straightforward)

```java
class Solution {
    public boolean isPowerOfThree(int n) {
        // Powers of three are always positive
        if (n <= 0) {
            return false;
        }
        
        // Keep dividing by 3 until we reach 1 or a non-divisible number
        while (n % 3 == 0) {
            n /= 3;
        }
        
        // If n equals 1, it was a power of three
        return n == 1;
    }
}
```

**Example**: `n = 27`
- 27 % 3 = 0 → n = 9
- 9 % 3 = 0 → n = 3
- 3 % 3 = 0 → n = 1
- n == 1 → return true ✓

**Complexity**:
- Time: O(log₃ n) - divide by 3 repeatedly[2][1]
- Space: O(1) - constant space[3]

## Solution 2: Mathematical Approach (No Loops/Recursion - Follow-up)

**Key insight**: The largest power of three within 32-bit integer range is \(3^{19} = 1162261467\).

If `n` is a power of three and `n ≤ 2^31 - 1`, then `n` must divide the largest power of three.[2]

```java
class Solution {
    public boolean isPowerOfThree(int n) {
        // Largest power of 3 within 32-bit range: 3^19
        int maxPowerOfThree = 1162261467; // 3^19
        
        // n must be positive and divide the maximum power of three
        return n > 0 && maxPowerOfThree % n == 0;
    }
}
```

**Why this works**:
- If n = 3^x for x ∈ , then 3^19%% 3^x = 0
- If n is not a power of three, 3^19% n ≠ 0
- For negative or zero, we filter with `n > 0`

**Example**: `n = 27`
- 27 > 0 ✓
- 1162261467 % 27 = 0 ✓
- Return true ✓

**Complexity**:
- Time: O(1) - single arithmetic operation[2]
- Space: O(1) - no extra space[2]

## Solution 3: Logarithm Approach

```java
class Solution {
    public boolean isPowerOfThree(int n) {
        if (n <= 0) {
            return false;
        }
        
        // x = log₃(n); if x is an integer, n is a power of 3
        double x = Math.log(n) / Math.log(3);
        
        // Check if x is close to an integer (accounting for floating-point precision)
        return Math.abs(x - Math.round(x)) < 1e-10;
    }
}
```

**Note**: Avoid this in practice due to floating-point precision issues.

## Comparison of Approaches

| Approach | Time | Space | Pros | Cons |
|----------|------|-------|------|------|
| **Division Loop** | **O(log n)** | **O(1)** | Clear, intuitive | Not O(1) time |
| **Math (Modulo)** | **O(1)** | **O(1)** | **Elegant, fast** | **Requires knowledge of max** |
| Logarithm | O(1) | O(1) | Clean formula | Floating-point precision |

## Edge Cases

| Case | Input | Expected | Result |
|------|-------|----------|--------|
| Power of 3 | 27 | true | ✓ |
| Power of 3 | 1 | true | ✓ |
| Power of 3 | 3 | true | ✓ |
| Not power | 10 | false | ✓ |
| Zero | 0 | false | ✓ |
| Negative | -3 | false | ✓ |
| One | 1 | true | ✓ (3^0 = 1) |

## Interview Tips

1. **Start with the straightforward approach**:
   - "I'll repeatedly divide by 3 and check if we reach 1."
   - Easy to understand and implement.

2. **For the follow-up (no loops)**:
   - "Powers of three < 2^31 all divide the maximum power of three (3^19)."
   - Show the calculation: `3^19 = 1162261467`

3. **Trace through examples**:
   - Division approach: 27 → 9 → 3 → 1 ✓
   - Math approach: 1162261467 % 27 = 0 ✓

4. **Discuss trade-offs**:
   - "Division loop is intuitive but O(log n)."
   - "Math approach is O(1) but requires knowing the constraint."
   - "Logarithm works but has floating-point precision issues."

5. **Mention similar problems**:
   - "Same approach works for power of 2, power of 4, etc."
   - "For power of 2, can also use bit manipulation: `n & (n - 1) == 0`"

This problem tests understanding of **number theory**, **logarithms**, and **mathematical properties** beyond typical algorithmic thinking.[1][2]
