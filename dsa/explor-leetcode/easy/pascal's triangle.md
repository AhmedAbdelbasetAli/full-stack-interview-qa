Return a 2D list where each row is built from the previous one: first and last elements are 1, and each middle element is the sum of the two numbers above it. This is a classic DP / triangle-building pattern.[1][2]

## Approach

- Row 0 is `[1]`.  
- For each next row `i` (0-indexed):
  - Length is `i + 1`.
  - `row[0] = 1`, `row[i] = 1`.
  - For `1 <= j < i`: `row[j] = prevRow[j-1] + prevRow[j]`.[3][1]

## Java Implementation

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> result = new ArrayList<>();

        for (int i = 0; i < numRows; i++) {
            List<Integer> row = new ArrayList<>();
            
            // First and last elements are always 1
            for (int j = 0; j <= i; j++) {
                if (j == 0 || j == i) {
                    row.add(1);
                } else {
                    int val = result.get(i - 1).get(j - 1) + result.get(i - 1).get(j);
                    row.add(val);
                }
            }

            result.add(row);
        }

        return result;
    }
}
```

## Example

For `numRows = 5`, rows are built as:[4][2]

1. `[1]`  
2. `[1, 1]`  
3. `[1, 2, 1]`  
4. `[1, 3, 3, 1]`  
5. `[1, 4, 6, 4, 1]`  

Matches the required output.

