## Problem Understanding

This is the classic **"Best Time to Buy and Sell Stock II"** problem where you can perform **unlimited transactions** (buy and sell multiple times). The critical constraints are:[1][2]

1. You can only hold **at most one share** at any time (must sell before buying again)
2. You can buy and sell on the **same day** if needed
3. Goal: Maximize total profit across all transactions

The key difference from the single-transaction version is that we can make **as many transactions as we want**, which fundamentally changes the optimal strategy.[2]

## Optimal Approach: Greedy Algorithm (Peak-Valley Accumulation)

I'm using the **greedy approach** because it's the most elegant and efficient solution for this problem. Here's the crucial insight:[1][2]

### The "Aha!" Moment

**Key Insight**: Since we can make unlimited transactions, we should capture **every upward price movement**.[2]

Consider this mathematical equivalence:
- **Strategy 1**: Buy at 1, hold, sell at 4 → Profit = 3
- **Strategy 2**: Buy at 1, sell at 2 (+1), buy at 2, sell at 3 (+1), buy at 3, sell at 4 (+1) → Profit = 3

Both yield the **same total profit**! This means we don't need to find perfect valleys and peaks - we can simply accumulate all consecutive positive differences.[2]

### Why Greedy Works Here

1. **Unlimited transactions**: No penalty for frequent trading
2. **No transaction fees**: Every positive price movement is pure profit
3. **Decomposable profit**: Any multi-day gain equals the sum of daily gains
4. **Mathematical proof**: If `prices[i] < prices[j]`, then `prices[j] - prices[i] = (prices[i+1] - prices[i]) + (prices[i+2] - prices[i+1]) + ... + (prices[j] - prices[j-1])`

## Java Solution

```java
public class Solution {
    /**
     * Calculates maximum profit with unlimited transactions
     * Uses greedy approach: accumulate all positive price differences
     * 
     * @param prices - array of stock prices where prices[i] is price on day i
     * @return maximum profit achievable
     */
    public int maxProfit(int[] prices) {
        // Edge case: cannot make profit with less than 2 days
        if (prices == null || prices.length < 2) {
            return 0;
        }
        
        int totalProfit = 0;
        
        // Iterate through each consecutive pair of days
        for (int i = 1; i < prices.length; i++) {
            // Calculate profit if we bought yesterday and sold today
            int dailyProfit = prices[i] - prices[i - 1];
            
            // Only accumulate positive profits (price increases)
            // If price decreases or stays same, we don't trade (skip)
            if (dailyProfit > 0) {
                totalProfit += dailyProfit;
            }
            // Equivalent to: totalProfit += Math.max(0, dailyProfit);
        }
        
        return totalProfit;
    }
}
```

## Detailed Walkthrough

Let's trace through **Example 1**: `prices = [11][12][13][14][15][16]`

**Strategy**: Compare each consecutive pair and capture positive differences

| Day i | Price[i] | Price[i-1] | Difference | Action | Accumulated Profit |
|-------|----------|------------|------------|--------|-------------------|
| 0 | 7 | - | - | Starting point | 0 |
| 1 | 1 | 7 | 1 - 7 = -6 | Skip (negative) | 0 |
| 2 | 5 | 1 | 5 - 1 = +4 | **Add 4** (buy at 1, sell at 5) | 4 |
| 3 | 3 | 5 | 3 - 5 = -2 | Skip (negative) | 4 |
| 4 | 6 | 3 | 6 - 3 = +3 | **Add 3** (buy at 3, sell at 6) | 7 |
| 5 | 4 | 6 | 4 - 6 = -2 | Skip (negative) | 7 |

**Final Result**: 7 ✓

**Transaction interpretation**:
- Buy on day 1 (price = 1), sell on day 2 (price = 5) → profit = 4
- Buy on day 3 (price = 3), sell on day 4 (price = 6) → profit = 3
- Total = 4 + 3 = 7

### Example 2: Continuous Uptrend

`prices = [12][17][14][16][13]`

| Pair | Difference | Profit |
|------|------------|--------|
| (1,2) | +1 | 1 |
| (2,3) | +1 | 1 |
| (3,4) | +1 | 1 |
| (4,5) | +1 | 1 |

**Total**: 1 + 1 + 1 + 1 = 4

This is equivalent to buying at 1 and selling at 5 (profit = 4), but we capture it through consecutive gains.[2]

### Example 3: Continuous Downtrend

`prices = [11][15][16][14][12]`

All consecutive differences are negative, so we **never trade**. Total profit = 0 ✓

## Alternative Approach: Valley-Peak Detection

While the greedy approach is simpler, here's the valley-peak detection method for comparison :[1]

```java
public class SolutionAlternative {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        
        int totalProfit = 0;
        int i = 0;
        int n = prices.length;
        
        while (i < n - 1) {
            // Find valley (local minimum) - keep moving while price decreases
            while (i < n - 1 && prices[i] >= prices[i + 1]) {
                i++;
            }
            int valley = prices[i];
            
            // Find peak (local maximum) - keep moving while price increases
            while (i < n - 1 && prices[i] <= prices[i + 1]) {
                i++;
            }
            int peak = prices[i];
            
            // Add profit from this valley-peak pair
            totalProfit += (peak - valley);
        }
        
        return totalProfit;
    }
}
```

**This approach**:
1. Finds each local minimum (valley) - point where prices stop falling
2. Finds each local maximum (peak) - point where prices stop rising
3. Accumulates profit from each valley-peak pair[1]

**Why the simple greedy is better**: Same result, less code, easier to understand and debug.

## Complexity Analysis

### Time Complexity: O(n)
- Single pass through the array with one loop
- Each element visited exactly once
- Constant-time operations inside the loop[2]

### Space Complexity: O(1)
- Only uses a single integer variable (`totalProfit`)
- No additional data structures required
- In-place computation with minimal memory[2]

## Why This Approach is Optimal

### 1. **Can't Do Better Than O(n) Time**
We must examine every price at least once to determine all profit opportunities.[2]

### 2. **Greedy is Provably Optimal Here**
Unlike many problems where greedy fails, this problem has the **greedy choice property**:
- Local optimal choices (capturing each upward movement) lead to global optimum
- No future price affects whether a past upward movement should have been captured

### 3. **Contrast with Other Stock Problems**

| Problem Variant | Transactions | Approach | Complexity |
|----------------|--------------|----------|------------|
| Stock I | 1 transaction | Track minimum, calculate max difference | O(n) time, O(1) space |
| **Stock II** | **Unlimited** | **Greedy: sum all positive differences** | **O(n) time, O(1) space** |
| Stock III | 2 transactions | Dynamic Programming | O(n) time, O(1) space |
| Stock IV | k transactions | Dynamic Programming | O(nk) time, O(k) space |

The unlimited transactions constraint makes this problem **simpler** than limited transaction variants.[1]

### 4. **No Need for DP**
While this could be solved with dynamic programming, the greedy approach is:
- More intuitive
- More efficient (no state tracking)
- Equally correct

## Common Mistakes to Avoid

### 1. **Overthinking the "Look Ahead" Problem**
**Wrong thinking**: "I can't know tomorrow's price, so how can I decide to buy today?"

**Correct understanding**: We're analyzing historical data to find what the maximum profit *would have been*. The algorithm shows the optimal strategy in hindsight.[2]

### 2. **Trying to Track Buy/Sell States**
**Unnecessary**:
```java
boolean holding = false;
int buyPrice = 0;
// Complex state management...
```

**Better**: Just sum positive differences - the algorithm implicitly handles buy/sell timing.

### 3. **Worrying About Multiple Transactions Per Day**
The problem allows buying and selling on the same day, but our algorithm handles this naturally. If prices go 1→3→2, we capture +2 on day 2, then don't trade on day 3 (difference is -1).

### 4. **Thinking Greedy Doesn't Work**
In many problems, greedy fails. But here, it's **provably optimal** because:
- Capturing every gain is always beneficial (no transaction cost)
- Missing any upward movement strictly decreases total profit
- Order of transactions doesn't matter for the total sum

