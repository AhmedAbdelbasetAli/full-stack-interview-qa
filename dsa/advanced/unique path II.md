Use dynamic programming on the grid, treating obstacles as cells with 0 ways and free cells as accumulating ways from top and left.[1][2]

## Idea

- Let **dp[i][j]** be the number of unique paths to reach cell \((i, j)\).[2]
- If `obstacleGrid[i][j] == 1`, then `dp[i][j] = 0` (cannot stand on an obstacle).[3][2]
- Otherwise, `dp[i][j] = dp[i-1][j] + dp[i][j-1]` (from top and left), taking care of borders.[2][3]
- Initialize:
  - If start cell `(0,0)` is an obstacle → return 0.[2]
  - `dp[0][0] = 1` if it is free.[2]

## Java Code (LeetCode-style)

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;

        // If start or end is blocked, no paths
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }

        int[][] dp = new int[m][n];

        // Start cell
        dp[0][0] = 1;

        // First row
        for (int j = 1; j < n; j++) {
            if (obstacleGrid[0][j] == 1) {
                dp[0][j] = 0;
            } else {
                dp[0][j] = dp[0][j - 1];
            }
        }

        // First column
        for (int i = 1; i < m; i++) {
            if (obstacleGrid[i][0] == 1) {
                dp[i][0] = 0;
            } else {
                dp[i][0] = dp[i - 1][0];
            }
        }

        // Rest of the grid
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) {
                    dp[i][j] = 0;
                } else {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }

        return dp[m - 1][n - 1];
    }
}
```

## Complexity

- **Time:** \(O(m \times n)\) – one pass over the grid.[3][2]
- **Space:** \(O(m \times n)\) using the 2D dp array (can be optimized to \(O(n)\) with a 1D dp).[3][2]
