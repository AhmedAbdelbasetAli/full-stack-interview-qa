

---

## 🎯 PROBLEM: Best Time to Buy and Sell Stock I

> You are given an array `prices` where `prices[i]` is the price of a stock on day `i`.  
> You want to maximize your profit by choosing a **single day to buy** and a **different day in the future to sell**.  
> Return the maximum profit you can achieve. If no profit is possible, return `0`.

**Example**:  
Input: `prices = [7,1,5,3,6,4]`  
Output: `5` → Buy on day 1 (price=1), sell on day 4 (price=6) → profit = 5.

---

## 🧠 STEP 1: Understand the Problem

- Buy first, sell later — **no time travel**.
- Only **one transaction** allowed (buy once, sell once).
- Maximize `prices[j] - prices[i]` where `j > i`.
- If no profit → return `0`.

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Brute Force — O(n²)
Try every buy day + every sell day after it → keep max profit.

```java
for i in 0..n-2
  for j in i+1..n-1
    profit = prices[j] - prices[i]
    maxProfit = max(maxProfit, profit)
```

→ Works, but too slow for large n.

---

### ✅ Greedy — O(n) — The Smart Way

> “At each day, if I were to sell today, what’s the best day I could have bought?”

- Track the **minimum price seen so far**.
- For each day, calculate profit if sold today → `currentPrice - minSoFar`.
- Track **maximum profit** seen so far.

→ One pass. No extra space. Elegant.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class BestTimeToBuyAndSellStock {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }

        int minPrice = prices[0];   // lowest price seen so far
        int maxProfit = 0;          // best profit so far

        for (int i = 1; i < prices.length; i++) {
            // If we sell today, profit = today's price - minPrice so far
            int profit = prices[i] - minPrice;
            maxProfit = Math.max(maxProfit, profit);

            // Update minPrice for future days
            minPrice = Math.min(minPrice, prices[i]);
        }

        return maxProfit;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `prices = [7,1,5,3,6,4]`

| i | prices[i] | minPrice | profit | maxProfit |
|---|-----------|----------|--------|-----------|
| 1 | 1         | 1        | 1-7=-6 → 0 | 0     |
| 2 | 5         | 1        | 5-1=4  | 4         |
| 3 | 3         | 1        | 3-1=2  | 4         |
| 4 | 6         | 1        | 6-1=5  | 5         |
| 5 | 4         | 1        | 4-1=3  | 5         |

→ Return `5` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — one pass.
- **Space**: O(1) — only two variables.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Trading systems, backtesting, financial dashboards.
- **Interview pattern**: **Greedy — track minimum + compute local optimum**.
- **Why companies ask this**: Tests if you can avoid O(n²), think greedily, handle edge cases.

> 💡 Uber: “When to dispatch driver for max fare delta?”  
> 💡 Amazon: “When to buy/sell inventory for max margin?”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Buying after selling** → ensure `j > i` → our loop from 1 ensures that.
- **Not handling no-profit case** → return 0 if all prices descending.
- **Initializing `maxProfit` to negative** → if no profit, must return 0.
- **Using `Integer.MIN_VALUE` without checking** → if no transaction, return 0, not negative.

---

## ✅ STEP 8: Edge Cases to Test

```java
maxProfit([]) → 0
maxProfit([5]) → 0
maxProfit([7,6,4,3,1]) → 0 (prices only fall)
maxProfit([1,2,3,4,5]) → 4 (buy first, sell last)
maxProfit([2,4,1]) → 2 (buy at 2, sell at 4 — not 1!)
```

---

## 🧠 BONUS: Why Greedy Works Here

This is a **“local optimum leads to global optimum”** problem.

At each step:
- We know the best day to have bought so far (`minPrice`).
- Selling today gives us the best possible profit for today.
- We track the best of all those daily bests.

→ No need to look back — greedy choice is safe.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds maximum profit from one buy + one sell transaction.
 * Buy must happen before sell.
 * @param prices array of daily stock prices
 * @return maximum profit, or 0 if no profit possible
 */
public int maxProfit(int[] prices) {
    // Edge case: need at least 2 days
    if (prices == null || prices.length < 2) {
        return 0;
    }

    int minPrice = prices[0];   // Cheapest day to buy so far
    int maxProfit = 0;          // Best profit we can get

    // Start from day 1 (can't sell on day 0)
    for (int i = 1; i < prices.length; i++) {
        // What if we sell today?
        int profitIfSellToday = prices[i] - minPrice;
        maxProfit = Math.max(maxProfit, profitIfSellToday);

        // Update cheapest buy day for future
        minPrice = Math.min(minPrice, prices[i]);
    }

    return maxProfit;
}
```

---
