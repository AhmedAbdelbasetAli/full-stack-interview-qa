The number of distinct ways to climb `n` stairs when each move is 1 or 2 steps follows the Fibonacci sequence: `ways(n) = ways(n-1) + ways(n-2)` with base cases `ways(0)=1` and `ways(1)=1`.[1][2][3]

***

## Dynamic Programming (Space Optimized)

Since each state only depends on the previous two, you only need **two variables** for optimal time and space:

```java
public int climbStairs(int n) {
    int prev2 = 1, prev1 = 1; // ways(0), ways(1)
    for (int i = 2; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```
- For `n=2`: returns 2 (1+1)
- For `n=3`: returns 3 (1+2)
- For `n=4`: returns 5
- This matches Fibonacci numbers shifted by one index.[4][3][5]

***

## Approach Reasoning

- At each step, you can arrive there from either one step below or two steps below:  
  `ways(i) = ways(i-1) + ways(i-2)`.[2][1]
- This is why the answer for `n` is Fibonacci(n+1).

***

## Complexity

- **Time**: O(n) — one pass from 2 to n.[5][6]
- **Space**: O(1) — just two integer variables.[7]

***

## Example Tracing

- **n=2**: ways = 2 (1+1, 2)
- **n=3**: ways = 3 (1+1+1, 1+2, 2+1)
- **n=4**: ways = 5 (1+1+1+1, 2+2, 1+2+1, 2+1+1, 1+1+2)

This is a classic dynamic programming and Fibonacci pattern problem.[3][8][2]
