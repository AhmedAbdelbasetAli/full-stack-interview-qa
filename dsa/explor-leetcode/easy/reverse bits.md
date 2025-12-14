Reverse the 32 bits of `n` by rebuilding a result bit-by-bit: repeatedly take the least significant bit of `n` and append it to the left of the result, then shift `n` right. This runs in O(32) = O(1) time and space.[1][2][3]

## Bit-by-bit solution (iterative)

```java
class Solution {
    // Reverse bits of a 32-bit integer
    public int reverseBits(int n) {
        int res = 0;
        // Process exactly 32 bits
        for (int i = 0; i < 32; i++) {
            // Shift result left to make room for next bit
            res <<= 1;
            // Add current least-significant bit of n
            res |= (n & 1);
            // Shift n right to process next bit
            n >>>= 1; // unsigned shift to avoid sign issues
        }
        return res;
    }
}
```

How it works:
- `res` starts at 0.
- At each step:
  - `res <<= 1` moves previously collected bits left.
  - `(n & 1)` extracts the current least significant bit of `n`.
  - `res |= (n & 1)` appends that bit at the right.
  - `n >>>= 1` moves the next bit into the least-significant position.[4][1]

Example for `n = 43261596` (`00000010100101000001111010011100`):
- After 32 iterations, `res` becomes `00111001011110000010100101000000` = `964176192`, as required.[3][1]

## Follow-up: Optimize for many calls

If `reverseBits` is called many times, use a **lookup-table** to reverse 8-bit chunks and combine them, reducing per-call work to a few table lookups and shifts.[5][6]

### 8-bit lookup table idea (outline)

1. Precompute `rev[0..255]` where `rev[b]` is `b` with its 8 bits reversed.
2. For a 32-bit `n`:
   - Extract each byte, reverse it via the table, and place it in the mirrored position:

```java
class Solution {
    private static final int[] REV = new int[256];

    static {
        for (int i = 0; i < 256; i++) {
            int x = i, r = 0;
            for (int j = 0; j < 8; j++) {
                r <<= 1;
                r |= (x & 1);
                x >>= 1;
            }
            REV[i] = r;
        }
    }

    public int reverseBits(int n) {
        return  (REV[n         & 0xff] << 24) |
                (REV[(n >>> 8) & 0xff] << 16) |
                (REV[(n >>>16) & 0xff] <<  8) |
                (REV[(n >>>24) & 0xff]      );
    }
}
```

- Static init runs once; afterwards each call is O(1) with very small constant.[6][5]
- This is a standard space–time trade-off, ideal when the function is hot.

[20](https://tomvanantwerp.com/coding-questions/leetcode-190-reverse-bits/)
