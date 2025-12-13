## Problem Understanding

The **Hamming distance** is the number of positions where corresponding bits differ between two equal-length binary representations. Count the number of 1s in the XOR of `x` and `y`.[1][2]

## Optimal Solution: XOR + Bit Count

**Key insight**: XOR produces 1 where bits differ, 0 where they match. Then count the 1s.[1]

```java
class Solution {
    public int hammingDistance(int x, int y) {
        // XOR produces 1 where bits differ
        int xor = x ^ y;
        
        // Count number of 1s using n & (n-1) trick
        int distance = 0;
        while (xor != 0) {
            xor &= (xor - 1);  // Clear lowest set bit
            distance++;
        }
        return distance;
    }
}
```

## Example Walkthrough

### Example 1: `x = 1, y = 4`

```
x = 1:  0 0 0 1
y = 4:  0 1 0 0
XOR:    0 1 0 1  (2 ones)

Loop iterations:
0101 & 0100 = 0100  → distance = 1
0100 & 0011 = 0000  → distance = 2
Result: 2 ✓
```

### Example 2: `x = 3, y = 1`

```
x = 3:  0 0 1 1
y = 1:  0 0 0 1
XOR:    0 0 1 0  (1 one)

Loop iterations:
0010 & 0001 = 0000  → distance = 1
Result: 1 ✓
```

## One-Liner (Using Java Intrinsic)

```java
class Solution {
    public int hammingDistance(int x, int y) {
        // Integer.bitCount counts the number of 1s
        return Integer.bitCount(x ^ y);
    }
}
```

This uses a built-in Java function that maps to hardware popcount when available.[2][1]

## Complexity

- **Time**: O(k) where k = number of differing bits (≤ 31), or O(1) with bitCount intrinsic
- **Space**: O(1)

Both approaches are optimal for this problem.[2][1]

