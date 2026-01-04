An integer has exactly four positive divisors only if it is either the product of two distinct primes \(p \cdot q\) or the cube of a prime \(p^3\). For this problem, an efficient and simple approach is to factor each number up to \(\sqrt{n}\), track divisor count and sum, and only keep numbers with exactly four divisors.[1][2][3][4]

## Algorithm (per number)

For each \(x\) in `nums`:

- Initialize `count = 0`, `sum = 0`.
- For each `d` from 1 to \(\lfloor \sqrt{x} \rfloor\):
  - If `x % d == 0`:
    - Let `q = x / d`.
    - If `d == q` (perfect square):  
      - `count += 1`, `sum += d`.
    - Else (pair of divisors):  
      - `count += 2`, `sum += d + q`.
  - If `count > 4` early, break; `x` cannot have exactly 4 divisors anymore.
- After the loop, if `count == 4`, add `sum` to the global answer.[4][1]

Overall time: \(O(n \sqrt{m})\), where \(m = \max(nums[i]) \le 10^5\).[1]

## Java Implementation

```java
class Solution {
    public int sumFourDivisors(int[] nums) {
        int total = 0;

        for (int x : nums) {
            int count = 0;
            int sum = 0;

            // Count divisors of x and their sum
            for (int d = 1; d * d <= x; d++) {
                if (x % d == 0) {
                    int q = x / d;

                    if (d == q) {        // perfect square divisor
                        count++;
                        sum += d;
                    } else {             // divisor pair (d, q)
                        count += 2;
                        sum += d + q;
                    }

                    if (count > 4) {     // prune early
                        break;
                    }
                }
            }

            if (count == 4) {
                total += sum;
            }
        }

        return total;
    }
}
```

## Example: `nums = [21, 4, 7]`

- 21: divisors = 1, 3, 7, 21 → `count = 4`, `sum = 32` → included.  
- 4: divisors = 1, 2, 4 → `count = 3` → ignored.  
- 7: divisors = 1, 7 → `count = 2` → ignored.  

Result: `32`.[5][6]

This solution meets the constraints comfortably and is straightforward to implement and reason about.

