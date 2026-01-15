
### 1. The Concept: The 2D Grid

To understand this problem, visualize it as a grid (matrix).

* String **s1** represents the **rows** (vertical movement).
* String **s2** represents the **columns** (horizontal movement).
* String **s3** represents the path from the top-left  to the bottom-right.

**The Rules:**

1. If we move **Down**, we are consuming a character from **s1**. It must match the current character in **s3**.
2. If we move **Right**, we are consuming a character from **s2**. It must match the current character in **s3**.
3. We start at index 0. If we can reach the end of both strings matching  along the way, the answer is `true`.

### 2. Space Optimization (The "Follow up")

A standard 2D grid requires  space. However, to calculate the current row, we only need information from:

1. The **Top** (the previous row, which represents the previous state of ).
2. The **Left** (the current row, which represents the previous state of ).

Therefore, we can compress the 2D grid into a **1D array** (`boolean[] dp`) of size . This reduces space complexity to .

---

### 3. Java Solution (Space Optimized)

```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int n = s1.length();
        int m = s2.length();

        // 1. Basic Check: Lengths must match
        if (n + m != s3.length()) {
            return false;
        }

        // 2. The DP Array
        // dp[j] implies: Can we form the prefix of s3 using 
        // a prefix of s1 (implied by loop iteration) and the first j chars of s2?
        boolean[] dp = new boolean[m + 1];

        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                
                // Base Case: empty s1 and empty s2 matches empty s3
                if (i == 0 && j == 0) {
                    dp[j] = true;
                } 
                // First Row: We only take chars from s2 (Left -> Right)
                else if (i == 0) {
                    dp[j] = dp[j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1);
                } 
                // First Column: We only take chars from s1 (Top -> Down)
                // Note: dp[j] here represents the value from the previous iteration (row i-1)
                else if (j == 0) {
                    dp[j] = dp[j] && s1.charAt(i - 1) == s3.charAt(i + j - 1);
                } 
                // Inner cells: Check Top (s1) or Left (s2)
                else {
                    // Check if we can come from s1 (Top)
                    // i.e., s1's current char matches s3's current char
                    boolean fromS1 = dp[j] && s1.charAt(i - 1) == s3.charAt(i + j - 1);
                    
                    // Check if we can come from s2 (Left)
                    // i.e., s2's current char matches s3's current char
                    boolean fromS2 = dp[j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1);
                    
                    dp[j] = fromS1 || fromS2;
                }
            }
        }

        return dp[m];
    }
}

```

---

### 4. Step-by-Step Trace (Example)

Let's trace a smaller example to make it very clear.

**Input:**

* 
* 
* 

**Initialization:**

* `dp` array size is .
* `dp` starts as `[false, false, false]`.

#### Iteration 0 (i=0): Processing  = "" (Empty)

* **j=0:** Base case. `dp[0] = true`.
* `dp`: `[T, F, F]`


* **j=1:** Take 'a' from . Matches  ('a')? Yes.
* `dp`: `[T, T, F]`


* **j=2:** Take 'c' from . Matches  ('a')? No.
* `dp`: `[T, T, F]`



#### Iteration 1 (i=1): Processing  = "a"

* **j=0:** Take 'a' from . Prev `dp[0]` was T.  ('a') ==  ('a')? Yes.
* `dp[0] = true`


* **j=1:**
* *From Top (s1)*: Prev `dp[1]` was T.  ('a') ==  ('a')? **True**.
* *From Left (s2)*: Curr `dp[0]` is T.  ('a') ==  ('a')? **True**.
* `dp[1] = true`


* **j=2:**
* *From Top (s1)*: Prev `dp[2]` was F.
* *From Left (s2)*: Curr `dp[1]` is T.  ('c') ==  ('c')? **True**.
* `dp[2] = true`
* Current `dp`: `[T, T, T]`



#### Iteration 2 (i=2): Processing  = "ab"

* **j=0:** Take 'b' from . Matches  ('a')? No.
* `dp[0] = false`


* **j=1:**
* *From Top (s1)*: Prev `dp[1]` was T.  ('b') ==  ('c')? False.
* *From Left (s2)*: Curr `dp[0]` is F.
* `dp[1] = false`


* **j=2:**
* *From Top (s1)*: Prev `dp[2]` was T.  ('b') ==  ('b')? **True**.
* *From Left (s2)*: Curr `dp[1]` is F.
* `dp[2] = true`



**Final Result:** `dp[2]` is **True**.

### Complexity Analysis

* **Time Complexity:**  — We calculate every cell in the grid once.
* **Space Complexity:**  — We only store one row (size of ) in memory.

