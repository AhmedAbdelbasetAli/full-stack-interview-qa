## Problem Understanding

This is the **"Best Time to Buy and Sell Stock"** problem. Find the maximum profit from a **single buy-sell transaction** where you must buy before you sell.[1][2]

**Key constraints**:
- Buy on day i, sell on day j where i < j
- Maximize: `prices[j] - prices[i]`
- If no profit possible, return 0

## Optimal Solution: Track Minimum Price (O(n) Time, O(1) Space)

The most efficient approach is a **greedy single-pass algorithm** that tracks the minimum price seen so far and calculates max profit at each step.[3][4]

### The Insight

At each day, we have two choices:
1. Sell on this day (profit = current price - min price so far)
2. Update the minimum price

```java
public class Solution {
    /**
     * Finds maximum profit from single buy-sell transaction
     * O(n) time, O(1) space
     */
    public int maxProfit(int[] prices) {
        int minPrice = prices[0];  // Minimum price seen so far
        int maxProfit = 0;         // Maximum profit so far
        
        for (int i = 1; i < prices.length; i++) {
            // Profit if we sell at current price
            int profit = prices[i] - minPrice;
            
            // Update max profit
            maxProfit = Math.max(maxProfit, profit);
            
            // Update minimum price
            minPrice = Math.min(minPrice, prices[i]);
        }
        
        return maxProfit;
    }
}
```

## Example Walkthrough

### Example 1: `prices = [7,1,5,3,6,4]`

| Day | Price | minPrice | profit | maxProfit | Decision |
|-----|-------|----------|--------|-----------|----------|
| 0 | 7 | 7 | - | 0 | Initialize |
| 1 | 1 | 1 | 1-1=0 | 0 | New min |
| 2 | 5 | 1 | 5-1=4 | 4 | Sell? |
| 3 | 3 | 1 | 3-1=2 | 4 | Skip |
| 4 | 6 | 1 | 6-1=5 | 5 | Sell! ✓ |
| 5 | 4 | 1 | 4-1=3 | 5 | Skip |

**Result**: Buy at 1, sell at 6 → profit = 5 ✓

### Example 2: `prices = [7,6,4,3,1]`

| Day | Price | minPrice | profit | maxProfit |
|-----|-------|----------|--------|-----------|
| 0 | 7 | 7 | - | 0 |
| 1 | 6 | 6 | 0 | 0 |
| 2 | 4 | 4 | 0 | 0 |
| 3 | 3 | 3 | 0 | 0 |
| 4 | 1 | 1 | 0 | 0 |

**Result**: No profit possible → return 0 ✓

## Dynamic Programming Approach (Alternative)

If specifically using DP, we can define states:

Let `dp[i]` = max profit using prices up to index i:
- `dp[i] = max(dp[i-1], prices[i] - min_price_so_far)`

```java
public class SolutionDP {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        
        // dp[i] = max profit up to day i
        int[] dp = new int[n];
        dp[0] = 0;  // No profit on first day
        
        int minPrice = prices[0];
        
        for (int i = 1; i < n; i++) {
            // Either don't sell today (dp[i-1]) or sell today
            dp[i] = Math.max(dp[i-1], prices[i] - minPrice);
            
            // Update minimum price
            minPrice = Math.min(minPrice, prices[i]);
        }
        
        return dp[n-1];
    }
}
```

**Complexity**: O(n) time, O(n) space (can optimize to O(1))

## Space-Optimized DP

```java
public class SolutionDPOptimized {
    public int maxProfit(int[] prices) {
        int maxProfit = 0;
        int minPrice = prices[0];
        
        for (int i = 1; i < prices.length; i++) {
            // Max profit by selling at current price
            maxProfit = Math.max(maxProfit, prices[i] - minPrice);
            
            // Update minimum price
            minPrice = Math.min(minPrice, prices[i]);
        }
        
        return maxProfit;
    }
}
```

This is equivalent to the greedy approach but framed as DP.

## Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| **Greedy (min tracking)** | **O(n)** | **O(1)** |
| DP with array | O(n) | O(n) |
| DP space-optimized | O(n) | O(1) |

## Why This Works

**Proof by DP state transition**:
- At each day i, we either:
  1. **Sell today**: profit = `prices[i] - min_price_before_i`
  2. **Don't sell today**: profit = best profit up to day i-1

- We always want to maximize: `max(don't sell, sell today)`
- To sell profitably, we need the **minimum price before today**
- Single pass tracks both, giving us the answer[4][3]

## Edge Cases

| Case | Input | Output |
|------|-------|--------|
| Single price |  | 0 |
| Decreasing | [5][6] | 0 ✓ |
| Increasing | [6][5] | 4 ✓ |
| One spike | [6] | 6 ✓ |

## Interview Tips

1. **State the constraint**:
   - "I need to buy before I sell and only one transaction."

2. **Explain the insight**:
   - "At each price, if I sell here, the profit is current price minus the minimum price I've seen."
   - "I track the minimum and maximum profit simultaneously."

3. **Walk through example**:
   ```
   [7,1,5,3,6,4]
   Min: 7 → 1 → 1 → 1 → 1 → 1
   Max profit: 0 → 0 → 4 → 4 → 5 → 5 ✓
   ```

4. **Mention DP framing**:
   - "This is DP: at each day, either don't sell or sell at max profit."
   - "DP state: maxProfit[i] = max(maxProfit[i-1], prices[i] - minPrice)"

5. **Note the optimization**:
   - "We only need two variables instead of an array, so space is O(1)."

This is a foundational problem teaching **greedy with state tracking** and **DP state transitions**.[2][3][4]

