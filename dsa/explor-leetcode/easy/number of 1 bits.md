The number of set bits (Hamming weight) in \(n\) is the count of 1s in its binary representation. A very efficient way is to repeatedly clear the least significant set bit using \(n = n \& (n - 1)\) and count how many times this can be done before \(n\) becomes 0.[1][2][3]

## Optimal bit‑trick solution

```java
class Solution {
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            n &= (n - 1);  // clear lowest set bit
            count++;
        }
        return count;
    }
}
```

### Why `n & (n - 1)` works

- Subtracting 1 from `n` flips all bits **from the rightmost 1 (inclusive) to the end**.
- AND-ing `n` with `n - 1` clears exactly that **least significant 1 bit** while leaving all higher bits unchanged.[2][1]
- Each iteration removes one set bit, so the loop runs exactly `number_of_1_bits(n)` times.[4][2]

**Example**: `n = 11` (binary `1011`)
- Iteration 1: `1011 & 1010 = 1010`
- Iteration 2: `1010 & 1001 = 1000`
- Iteration 3: `1000 & 0111 = 0000` → 3 iterations → 3 bits set ✓

**Complexity**:
- Time: \(O(k)\), where \(k\) is the number of set bits (≤ 31 here). Very fast in practice.[3][2]
- Space: \(O(1)\).

## Follow‑up: Many calls optimization

If this function is called very frequently, you can use a **lookup table** for bytes or 16‑bit chunks.[5][6][1]

### 8‑bit lookup table approach

Precompute popcount for all 256 possible byte values:

```java
class Solution {
    private static final int[] BIT_COUNT = new int[256];

    static {
        for (int i = 0; i < 256; i++) {
            int x = i, c = 0;
            while (x != 0) {
                x &= (x - 1);
                c++;
            }
            BIT_COUNT[i] = c;
        }
    }

    public int hammingWeight(int n) {
        // treat n as unsigned 32-bit
        return BIT_COUNT[n & 0xff] +
               BIT_COUNT[(n >>> 8) & 0xff] +
               BIT_COUNT[(n >>> 16) & 0xff] +
               BIT_COUNT[(n >>> 24) & 0xff];
    }
}
```

- Each call becomes a few shifts, masks, and 4 table lookups → effectively **O(1)** per call after the static initialization.[6][1][5]
- This is ideal when the API is called many times (e.g., in tight loops).

On platforms or languages with built‑ins (like `Integer.bitCount` in Java), using that intrinsic is usually best, as it maps to a hardware popcount instruction when available.[7][1]
2-bit-integer)
