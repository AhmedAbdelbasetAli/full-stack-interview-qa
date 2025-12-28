Find which integers in [1, n] are missing from the array by using the array itself as a hash table: mark positions as visited by negating the value at that index, then collect unmarked indices. This gives O(n) time and O(1) extra space (excluding the output list).[1][2]

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        int n = nums.length;
        
        // Mark visited numbers by negating the value at that index
        for (int num : nums) {
            int idx = Math.abs(num) - 1;  // Convert to 0-indexed
            nums[idx] = -Math.abs(nums[idx]);  // Negate if positive
        }
        
        // Collect indices where value is still positive
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (nums[i] > 0) {
                result.add(i + 1);  // Convert back to 1-indexed
            }
        }
        
        return result;
    }
}
```

### How it works

1. For each number `num` in the array, mark the position `abs(num) - 1` as visited by negating it.
2. After processing all numbers, any index that remains **positive** indicates that the corresponding number (index + 1) is **missing**.
3. Collect all 1-indexed values where the position is still positive.

### Example: `[4,3,2,7,8,2,3,1]`

**After marking (negating)**:
- num=4 → negate at index 3
- num=3 → negate at index 2
- num=2 → negate at index 1
- num=7 → negate at index 6
- num=8 → negate at index 7
- num=2 → negate at index 1 (already negative)
- num=3 → negate at index 2 (already negative)
- num=1 → negate at index 0

After this pass: `[-1,-2,-2,-4,5,-8,-7,-8]` (approx, indices matter)

Positive values at indices 4 and 5 (0-indexed) correspond to missing numbers 5 and 6. ✓

**Complexity**:
- Time: O(n), two passes through the array
- Space: O(1) extra (only output list, which doesn't count)

This satisfies the follow-up constraint: no extra space and O(n) runtime.
