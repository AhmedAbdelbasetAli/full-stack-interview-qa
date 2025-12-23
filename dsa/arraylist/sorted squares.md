Use a **two-pointer** scan from both ends and fill the result array from the back, always placing the larger square of the two candidates. This gives an O(n) solution without an extra sort.[1][2][3]

### Idea

- The array is sorted, but after squaring, large-magnitude negatives can become the largest values.
- Largest square must come from either the **leftmost** (most negative) or **rightmost** (most positive) element.[4][1]
- Compare `abs(nums[left])` and `abs(nums[right])`, square the larger, put it at the end of the result, and move that pointer inward.[5][3]

### Java implementation

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        
        int left = 0;
        int right = n - 1;
        int idx = n - 1; // fill from the end
        
        while (left <= right) {
            int leftVal = nums[left];
            int rightVal = nums[right];
            int leftSq = leftVal * leftVal;
            int rightSq = rightVal * rightVal;
            
            if (leftSq > rightSq) {
                res[idx] = leftSq;
                left++;
            } else {
                res[idx] = rightSq;
                right--;
            }
            idx--;
        }
        
        return res;
    }
}
```

### Example: `[-4,-1,0,3,10]`

- Compare 16 vs 100 → place 100 at res, move right.
- Compare 16 vs 9 → place 16 at res, move left.
- Compare 1 vs 9 → place 9 at res, move right.
- Compare 1 vs 0 → place 1 at res, move left.[6]
- Place 0 at res. Result: `[0,1,9,16,100]`.[7][8]

### Complexity

- Time: O(n) – each element processed once.[2][1]
- Space: O(n) for the result (required by problem; extra working space is O(1)).

