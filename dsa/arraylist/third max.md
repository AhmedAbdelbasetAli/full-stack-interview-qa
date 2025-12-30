Track only the three largest distinct values using three variables (`first`, `second`, `third`). Scan the array once, updating these three in O(n) time and O(1) space. If fewer than three distinct values exist, return the maximum.[1][2]

```java
class Solution {
    public int thirdMax(int[] nums) {
        Long first = null;
        Long second = null;
        Long third = null;
        
        for (int num : nums) {
            // Skip duplicates of existing max values
            if ((first != null && first == num) ||
                (second != null && second == num) ||
                (third != null && third == num)) {
                continue;
            }
            
            // Insert num into the correct position
            if (first == null || num > first) {
                third = second;
                second = first;
                first = (long) num;
            } else if (second == null || num > second) {
                third = second;
                second = (long) num;
            } else if (third == null || num > third) {
                third = (long) num;
            }
        }
        
        // If we have three distinct values, return the third max
        return (third != null) ? third.intValue() : first.intValue();
    }
}
```

### Why use `Long`

Using `Long` allows us to set initial values to `null` to distinguish "not set" from a valid integer value. Since `Integer.MIN_VALUE` (-2³¹) is a valid element, we can't use `Integer.MIN_VALUE` as a sentinel.[2]

### How it works

1. Maintain `first`, `second`, `third` as the three largest distinct values found so far.
2. For each number:
   - Skip if it equals any of the three (duplicate).
   - Insert it in the correct position, shifting smaller values down.
3. If `third` is set, return it; otherwise, return `first` (the maximum).

### Example: `[3,2,1]`

- num=3 → first=3
- num=2 → second=2
- num=1 → third=1
- Result: third=1 ✓

### Example: `[1,2]`

- num=1 → first=1
- num=2 → first=2 (shift), second=1
- Result: third is null, return first=2 ✓

### Example: `[2,2,3,1]`

- num=2 → first=2
- num=2 → skip (duplicate)
- num=3 → first=3 (shift), second=2
- num=1 → third=1
- Result: third=1 ✓

**Complexity**:
- Time: O(n), single pass through array
- Space: O(1), only three variables
