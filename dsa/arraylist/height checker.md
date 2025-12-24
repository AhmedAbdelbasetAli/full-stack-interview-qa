The number of mismatched indices is just how many positions differ between the original array and its sorted version. Build the expected sorted order, then compare element by element.[1][2][3]

## Approach

1. Clone `heights` into `expected`.  
2. Sort `expected` in non-decreasing order.  
3. Loop over indices `i` and count how many times `heights[i] != expected[i]`.  
4. Return that count.[2][4][5]

This is \(O(n \log n)\) due to the sort; with `n ≤ 100` this is more than fast enough.

## Java Implementation

```java
class Solution {
    public int heightChecker(int[] heights) {
        // Step 1: Copy original heights
        int[] expected = heights.clone();
        
        // Step 2: Sort to get expected order
        Arrays.sort(expected);
        
        // Step 3: Count mismatches
        int mismatchCount = 0;
        for (int i = 0; i < heights.length; i++) {
            if (heights[i] != expected[i]) {
                mismatchCount++;
            }
        }
        
        return mismatchCount;
    }
}
```

- Example 1:  
  - heights:  `[1,1,4,2,1,3]`  
  - expected: `[1,1,1,2,3,4]`  
  - mismatches at indices 2, 4, 5 → answer = 3.[3][2]

This directly matches the problem’s definition of indices where `heights[i] != expected[i]`.

