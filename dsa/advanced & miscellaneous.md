# 🧠 Dynamic Programming Deep Dive  
**Fibonacci, Coin Change, Knapsack & Memoization vs Tabulation**

This document covers **essential Dynamic Programming (DP)** concepts with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. What is dynamic programming? When should you use it?

> **Dynamic Programming (DP)** is an algorithmic technique for solving **complex problems by breaking them into overlapping subproblems** and **storing their solutions** to avoid recomputation.

It’s **"smart recursion"** + **memoization** or **iterative tabulation**.

---

### 🔹 1.1 When to Use DP

Use DP when the problem has:

| Property | Explanation |
|--------|-------------|
| ✅ **Optimal Substructure** | Optimal solution contains optimal solutions to subproblems |
| ✅ **Overlapping Subproblems** | Same subproblem is solved multiple times (e.g., Fibonacci) |

---

### 🔹 1.2 DP Approaches

| Approach | How | When to Use |
|--------|-----|------------|
| ✅ **Top-Down (Memoization)** | Recursive + cache results | Easier to think recursively |
| ✅ **Bottom-Up (Tabulation)** | Iterative, fill table from base cases | More control, no recursion stack |

---

### 🔹 1.3 Common DP Problems

| Problem | Type |
|--------|------|
| Fibonacci | Classic example |
| Coin Change | Min/Max coins |
| Knapsack | 0/1 or unbounded |
| Longest Common Subsequence | String DP |
| Edit Distance | String transformation |
| Climbing Stairs | Simple recurrence |

---

### 📌 Interview Answer
> _"Dynamic Programming solves problems by breaking them into subproblems and storing results to avoid recomputation. I use it when there's optimal substructure and overlapping subproblems — like Fibonacci, coin change, or knapsack. I can use memoization (top-down) or tabulation (bottom-up), depending on clarity and constraints."_  

---

## 2. Solve the Fibonacci sequence using recursion, memoization, and tabulation.

### 🔹 2.1 Recursive (Inefficient)
```java
public static long fibRecursive(int n) {
    if (n <= 1) return n;
    return fibRecursive(n - 1) + fibRecursive(n - 2);
}
```
- **Time**: O(2ⁿ) — exponential
- **Space**: O(n) — recursion stack
- ❌ **Too slow for n > 40**

---

### 🔹 2.2 Memoization (Top-Down)
```java
import java.util.*;

public static long fibMemo(int n, Map<Integer, Long> memo) {
    if (n <= 1) return n;
    if (memo.containsKey(n)) return memo.get(n);

    long result = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
    memo.put(n, result);
    return result;
}

// Wrapper
public static long fibonacciMemo(int n) {
    return fibMemo(n, new HashMap<>());
}
```
- **Time**: O(n)
- **Space**: O(n)

---

### 🔹 2.3 Tabulation (Bottom-Up)
```java
public static long fibTabulation(int n) {
    if (n <= 1) return n;

    long[] dp = new long[n + 1];
    dp[0] = 0;
    dp[1] = 1;

    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[n];
}
```
- **Time**: O(n)
- **Space**: O(n)

✅ **Space-Optimized** (O(1)):
```java
public static long fibOptimized(int n) {
    if (n <= 1) return n;
    long a = 0, b = 1;
    for (int i = 2; i <= n; i++) {
        long temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}
```

---

### 💡 Interview Tip
> _"I start with recursion to understand the recurrence, then add memoization to cache results. For production, I use tabulation — it's iterative and avoids stack overflow. For Fibonacci, I can optimize space to O(1) by storing only the last two values."_  

---

## 3. What is the difference between memoization and tabulation?

| Feature | Memoization (Top-Down) | Tabulation (Bottom-Up) |
|--------|------------------------|------------------------|
| **Direction** | Starts from main problem, goes to base cases | Starts from base cases, builds up |
| **Implementation** | Recursive | Iterative |
| **Ease of Coding** | Easier — just add cache to recursion | Requires thinking about order |
| **Space Usage** | Only solves needed subproblems | Solves all subproblems up to n |
| **Stack Overflow Risk** | Yes, for deep recursion | No |
| **When to Use** | Natural recursive structure | When you need full table or space is tight |

---

### 📌 Example: Fibonacci
- **Memoization**: Only computes `fib(5)`, `fib(4)`, `fib(3)`... as needed
- **Tabulation**: Computes `fib(0)` to `fib(n)` in order

---

### 📌 Interview Answer
> _"Memoization is top-down: recursive with a cache. It only solves subproblems that are needed. Tabulation is bottom-up: iterative, filling a table from base cases. It solves all subproblems. I use memoization when the recurrence is clear, and tabulation when I want to avoid recursion or optimize space."_  

---

## 4. Solve the Coin Change problem (minimum coins to make a sum)

> Given coins `[1, 3, 4]` and amount `6`, find **minimum number of coins** to make the amount.

Return `-1` if impossible.

---

### ✅ Approach: DP (Unbounded Knapsack style)

- `dp[i]` = minimum coins to make amount `i`
- Base: `dp[0] = 0`
- Recurrence:  
  `dp[i] = min(dp[i], dp[i - coin] + 1)` for each coin ≤ `i`

---

### ✅ Java Code (Tabulation)
```java
public static int coinChange(int[] coins, int amount) {
    if (amount == 0) return 0;

    int[] dp = new int[amount + 1];
    Arrays.fill(dp, amount + 1); // Use a big number as "infinity"
    dp[0] = 0;

    for (int i = 1; i <= amount; i++) {
        for (int coin : coins) {
            if (coin <= i) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
    }

    return dp[amount] > amount ? -1 : dp[amount];
}

// Example usage
public static void main(String[] args) {
    int[] coins = {1, 3, 4};
    System.out.println(coinChange(coins, 6)); // 2 (3+3)
    System.out.println(coinChange(coins, 5)); // 2 (1+4)
}
```

---

### 📌 Time & Space
- **Time**: O(amount × coins)
- **Space**: O(amount)

---

### 💡 Interview Tip
> _"I use DP: dp[i] is min coins for amount i. I initialize with 'infinity' (amount+1), set dp[0]=0, then for each amount, try every coin. If coin ≤ amount, update dp[i] = min(dp[i], dp[i-coin] + 1). This is classic unbounded knapsack."_  

---

## 5. Solve the Knapsack problem (0/1 or unbounded)

We'll solve **0/1 Knapsack**: each item can be used **once**.

> Given weights `[2,3,4,5]`, values `[3,4,5,6]`, capacity `5`, maximize value.

---

### ✅ Approach: DP with 2D Table

- `dp[i][w]` = max value using first `i` items and capacity `w`
- Recurrence:
  ```
  dp[i][w] = max(
      dp[i-1][w],  // Don't take item i
      dp[i-1][w-weight[i]] + value[i]  // Take it (if weight ≤ w)
  )
  ```

---

### ✅ Java Code (0/1 Knapsack)
```java
public static int knapsack01(int[] weights, int[] values, int capacity) {
    int n = weights.length;
    int[][] dp = new int[n + 1][capacity + 1];

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= capacity; w++) {
            if (weights[i-1] <= w) {
                dp[i][w] = Math.max(
                    dp[i-1][w],
                    dp[i-1][w - weights[i-1]] + values[i-1]
                );
            } else {
                dp[i][w] = dp[i-1][w];
            }
        }
    }

    return dp[n][capacity];
}

// Example usage
public static void main(String[] args) {
    int[] weights = {2, 3, 4, 5};
    int[] values = {3, 4, 5, 6};
    int capacity = 5;
    System.out.println(knapsack01(weights, values, capacity)); // 7 (items 0 and 1: weight 2+3=5, value 3+4=7)
}
```

---

### 🔹 Space-Optimized (1D Array)
```java
public static int knapsack01Optimized(int[] weights, int[] values, int capacity) {
    int[] dp = new int[capacity + 1];

    for (int i = 0; i < weights.length; i++) {
        for (int w = capacity; w >= weights[i]; w--) {
            dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }

    return dp[capacity];
}
```
✅ Reverse inner loop to avoid using updated values.

---

### 🔹 Unbounded Knapsack (Items can be reused)
```java
public static int knapsackUnbounded(int[] weights, int[] values, int capacity) {
    int[] dp = new int[capacity + 1];

    for (int w = 1; w <= capacity; w++) {
        for (int i = 0; i < weights.length; i++) {
            if (weights[i] <= w) {
                dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
            }
        }
    }

    return dp[capacity];
}
```
✅ Same as Coin Change (maximize value instead of minimize coins).

---

### 📌 Time & Space
- **0/1**: Time O(n×W), Space O(n×W) or O(W) optimized
- **Unbounded**: Time O(n×W), Space O(W)

---

### 💡 Interview Tip
> _"For 0/1 Knapsack, I use a 2D DP table: dp[i][w] = max value with first i items and capacity w. I either skip the item or take it (if it fits). I can optimize space to O(W) by iterating backwards. For unbounded, I iterate forwards — items can be reused."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: 0/1 or unbounded? Can amount be zero?
2. **Start with recursion** to find recurrence
3. **Add memoization** for top-down
4. **Convert to tabulation** for bottom-up
5. **Optimize space** if possible
6. **Handle edge cases**: amount 0, no coins, impossible

---

# 🧩 Advanced Algorithm Patterns  
**Sliding Window, Permutations & Subsets**

This document covers **two powerful algorithmic techniques** with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. What is the sliding window technique? Give an example.

> The **sliding window** technique is used to **efficiently solve problems involving subarrays or substrings** by maintaining a **window of elements** that **expands and contracts** as it moves through the data.

It’s ideal for problems with **contiguous sequences** and **constraints** (e.g., "longest substring with at most K distinct characters").

---

### 🔹 1.1 When to Use Sliding Window

| Problem Type | Example |
|-------------|--------|
| ✅ **Fixed Size Window** | Max sum of subarray of size K |
| ✅ **Variable Size Window** | Longest substring without repeating characters |
| ✅ **At Most K Distinct** | Longest substring with ≤ K unique characters |
| ✅ **Minimum Window Substring** | Smallest substring containing all chars of T |

---

### 🔹 1.2 Two Types of Sliding Window

| Type | How It Works |
|------|-------------|
| ✅ **Fixed Size** | Expand to size K, then slide right by one, left by one |
| ✅ **Dynamic Size** | Expand right until condition breaks, then shrink left |

---

### 🔹 1.3 Example: Maximum Sum of Subarray of Size K

> Given array `[2,1,4,3,2,5]` and `k=3`, find the **maximum sum of any contiguous subarray of size 3**.

✅ Answer: `4+3+2 = 9`

---

### ✅ Java Code (Fixed Window)
```java
public static int maxSumSubarray(int[] arr, int k) {
    if (arr == null || arr.length < k) return -1;

    int windowSum = 0;

    // First window
    for (int i = 0; i < k; i++) {
        windowSum += arr[i];
    }

    int maxSum = windowSum;

    // Slide the window
    for (int i = k; i < arr.length; i++) {
        windowSum = windowSum - arr[i - k] + arr[i];
        maxSum = Math.max(maxSum, windowSum);
    }

    return maxSum;
}

// Example usage
public static void main(String[] args) {
    int[] arr = {2, 1, 4, 3, 2, 5};
    System.out.println(maxSumSubarray(arr, 3)); // 9
}
```

---

### 🔹 1.4 Example: Longest Substring with At Most K Distinct Characters

> Given `"eceba"` and `k=2`, find the **length of the longest substring with ≤ 2 distinct characters**.

✅ Answer: `"ece"` → length 3

---

### ✅ Java Code (Dynamic Window)
```java
import java.util.*;

public static int longestSubstringKDistinct(String s, int k) {
    if (s == null || s.isEmpty() || k == 0) return 0;

    Map<Character, Integer> charCount = new HashMap<>();
    int left = 0, maxLength = 0;

    for (int right = 0; right < s.length(); right++) {
        char rightChar = s.charAt(right);
        charCount.put(rightChar, charCount.getOrDefault(rightChar, 0) + 1);

        // Shrink window if more than k distinct
        while (charCount.size() > k) {
            char leftChar = s.charAt(left);
            charCount.put(leftChar, charCount.get(leftChar) - 1);
            if (charCount.get(leftChar) == 0) {
                charCount.remove(leftChar);
            }
            left++;
        }

        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}

// Example usage
public static void main(String[] args) {
    System.out.println(longestSubstringKDistinct("eceba", 2)); // 3
    System.out.println(longestSubstringKDistinct("aa", 1));    // 2
}
```

---

### 📌 Time & Space
- **Time**: O(n) — each element visited at most twice
- **Space**: O(min(k, alphabet size)) — for the map

---

### 💡 Interview Tip
> _"I use sliding window for contiguous subarray/substring problems. For fixed size (like max sum of K), I use a fixed window and slide. For variable size (like K distinct), I expand right and shrink left when condition breaks. I use a map to track character counts and update the window accordingly."_  

---

## 2. How do you generate all permutations or subsets of a set?

---

### 🔹 2.1 Generate All Subsets (Power Set)

> Given a set `[1,2,3]`, generate all possible subsets:  
> `[], [1], [2], [3], [1,2], [1,3], [2,3], [1,2,3]`

✅ Total: 2ⁿ subsets

---

#### ✅ Approach: Backtracking
- At each element, **choose to include or exclude**
- Recurse on remaining elements

---

#### ✅ Java Code
```java
import java.util.*;

public static List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrackSubsets(nums, 0, new ArrayList<>(), result);
    return result;
}

private static void backtrackSubsets(int[] nums, int start, List<Integer> current, List<List<Integer>> result) {
    result.add(new ArrayList<>(current)); // Add current subset

    for (int i = start; i < nums.length; i++) {
        current.add(nums[i]);
        backtrackSubsets(nums, i + 1, current, result);
        current.remove(current.size() - 1); // Backtrack
    }
}

// Example usage
public static void main(String[] args) {
    int[] nums = {1, 2, 3};
    List<List<Integer>> result = subsets(nums);
    System.out.println(result);
    // [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
}
```

---

### 🔹 2.2 Generate All Permutations

> Given `[1,2,3]`, generate all possible orderings:  
> `[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]`

✅ Total: n! permutations

---

#### ✅ Approach: Backtracking with Swap or Used Array

We'll use a **used boolean array** to track which elements are in current permutation.

---

#### ✅ Java Code
```java
public static List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    boolean[] used = new boolean[nums.length];
    backtrackPermute(nums, new ArrayList<>(), used, result);
    return result;
}

private static void backtrackPermute(int[] nums, List<Integer> current, boolean[] used, List<List<Integer>> result) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }

    for (int i = 0; i < nums.length; i++) {
        if (!used[i]) {
            used[i] = true;
            current.add(nums[i]);
            backtrackPermute(nums, current, used, result);
            current.remove(current.size() - 1);
            used[i] = false; // Backtrack
        }
    }
}

// Example usage
public static void main(String[] args) {
    int[] nums = {1, 2, 3};
    List<List<Integer>> result = permute(nums);
    System.out.println(result);
    // [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
}
```

---

### 🔹 2.3 Alternative: Iterative (Subsets using Bit Manipulation)

For subsets, you can also use **bit masks** (0 to 2ⁿ - 1):

```java
public static List<List<Integer>> subsetsBitMask(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    int n = nums.length;
    int total = 1 << n; // 2^n

    for (int i = 0; i < total; i++) {
        List<Integer> subset = new ArrayList<>();
        for (int j = 0; j < n; j++) {
            if ((i & (1 << j)) != 0) {
                subset.add(nums[j]);
            }
        }
        result.add(subset);
    }

    return result;
}
```

✅ Time: O(n × 2ⁿ), Space: O(2ⁿ)

---

### 📌 Time & Space
| Problem | Time | Space |
|-------|------|-------|
| **Subsets** | O(n × 2ⁿ) | O(2ⁿ) |
| **Permutations** | O(n × n!) | O(n!) |

---

### 💡 Interview Tip
> _"I use backtracking for both. For subsets, I include/exclude each element and recurse. For permutations, I try every unused element at each position. I use a 'used' array to avoid duplicates. Both are classic backtracking problems with exponential output — I always clarify if duplicates are allowed in input."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: Are duplicates allowed? Should subsets be sorted?
2. **Draw the recursion tree** when explaining
3. **Use helper methods** for clean backtracking
4. **State time & space complexity** — exponential in output size
5. **Know both recursive and iterative approaches**

---
