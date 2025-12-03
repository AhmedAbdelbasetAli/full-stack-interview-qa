The maximum you can rob is the best of two choices at each house: rob it and add the best up to i‑2, or skip it and take the best up to i‑1. This gives a classic DP with O(n) time and O(1) space.[1][2][3]

## DP Recurrence

Let `dp[i]` = max money using houses `0..i`:
- `dp[0] = nums[0]`
- `dp[1] = max(nums[0], nums[1])`
- For `i ≥ 2`:  
  `dp[i] = max(dp[i-1], nums[i] + dp[i-2])`[4][1]

Space-optimized form keeps only the last two values:

- `prev2` = dp[i-2]  
- `prev1` = dp[i-1]  
- `curr` = max(prev1, nums[i] + prev2)[3][1]

## Java Implementation (Space Optimized)

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0];

        int prev2 = nums[0];                     // dp[0]
        int prev1 = Math.max(nums[0], nums[1]);  // dp[1]

        for (int i = 2; i < n; i++) {
            int curr = Math.max(prev1, nums[i] + prev2);
            prev2 = prev1;
            prev1 = curr;
        }

        return prev1;
    }
}
```

## Example Checks

- `[1,2,3,1]` → dp: 1, 2, 4, 4 → answer = 4.  
- `[2,7,9,3,1]` → dp: 2, 7, 11, 11, 12 → answer = 12.  

Both match the examples and satisfy the non-adjacent constraint.[2][1][3]

