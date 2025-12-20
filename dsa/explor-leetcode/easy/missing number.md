The missing value is the only number in \([0,n]\) that doesn’t appear in `nums`, so you can get it in O(n) time and O(1) space either via a sum formula or via XOR. Both are optimal and avoid extra data structures.[1][2]

## Approach 1: Sum formula (simple)

Sum of 0..n is \( n \times (n+1) / 2 \). The missing number is:

\[
\text{missing} = \frac{n(n+1)}{2} - \sum_{x \in nums} x
\] [1][3]

```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;
        long expected = (long) n * (n + 1) / 2;  // avoid overflow
        long actual = 0;
        for (int x : nums) {
            actual += x;
        }
        return (int) (expected - actual);
    }
}
```

- Time: O(n)  
- Space: O(1)  
- Works because `nums` contains all numbers except one from 0..n.[4][1]

## Approach 2: XOR Trick (overflow-proof, also O(1) space)

Use the property \(a \oplus a = 0\) and \(a \oplus 0 = a\). XOR all numbers from 0..n and all elements in `nums`; equal values cancel out, leaving the missing number.[2][5]

```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;
        int xor = 0;
        
        // XOR all numbers from 0 to n
        for (int i = 0; i <= n; i++) {
            xor ^= i;
        }
        
        // XOR all elements in nums
        for (int x : nums) {
            xor ^= x;
        }
        
        // Remaining value is the missing number
        return xor;
    }
}
```

- Time: O(n)  
- Space: O(1)  
- Avoids any risk of integer overflow, since XOR is bitwise.[3][2]

Both methods satisfy the follow-up requirement: **O(n)** runtime and **O(1)** extra space.

[20](https://www.geeksforgeeks.org/dsa/find-two-missing-numbers-set-2-xor-based-solution/)
