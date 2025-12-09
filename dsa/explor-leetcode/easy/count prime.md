Use the **Sieve of Eratosthenes** to mark non-primes up to \(n-1\) and count the remaining primes in \(O(n \log \log n)\) time and \(O(n)\) space, which fits \(n \le 5 \times 10^6\) comfortably.[1][2]

## Algorithm (Sieve of Eratosthenes)

1. If `n <= 2`, return 0 (no primes < 2).[2]
2. Create a boolean array `isPrime[0..n-1]`, initialize all to `true` except indices 0 and 1.[3][2]
3. For each `i` from 2 to `i * i < n`:
   - If `isPrime[i]` is true, mark all multiples `j = i * i, i * i + i, ... < n` as non-prime.[4][3]
4. Count how many `isPrime[i]` are true for `2 <= i < n`.[2]

## Java Implementation

```java
class Solution {
    public int countPrimes(int n) {
        if (n <= 2) {
            return 0; // no primes less than 2
        }

        boolean[] isPrime = new boolean[n];
        // Assume all numbers >= 2 are prime initially
        Arrays.fill(isPrime, true);
        isPrime[0] = false;
        isPrime[1] = false;

        // Sieve: mark non-primes
        for (int i = 2; (long) i * i < n; i++) {
            if (isPrime[i]) {
                // Start marking from i*i to avoid overflow and redundant work
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }

        // Count primes
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) {
                count++;
            }
        }
        return count;
    }
}
```

## Example

For `n = 10`, `isPrime` after sieve is:

- `true` at indices: 2, 3, 5, 7  
- Count = 4 → primes are 2, 3, 5, 7.[5][2]

## Complexity

- **Time**: \(O(n \log \log n)\) due to sieve; effectively near-linear for practical `n`.[6][3]
- **Space**: \(O(n)\) for the boolean array.[4][2]

