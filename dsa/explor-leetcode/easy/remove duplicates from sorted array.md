## Problem Understanding

You need to remove duplicates from a **sorted** array in-place, keeping only one occurrence of each unique element. The key constraints are:[1]

1. The array is already sorted in non-decreasing order (crucial detail)
2. You must modify the array in-place (O(1) extra space)
3. Return `k` - the count of unique elements
4. The first `k` positions must contain the unique elements in order
5. Elements after index `k-1` are irrelevant

## Optimal Approach: Two-Pointer Technique

I'm using the **two-pointer technique** because it's the most efficient solution for this problem. Here's why this approach is ideal:[2][3][4]

### Why Two Pointers?

1. **Leverages the sorted property**: Since duplicates are adjacent in a sorted array, we only need to compare consecutive elements
2. **Single pass solution**: We traverse the array once - O(n) time complexity
3. **True in-place**: Only uses two integer pointers - O(1) space complexity
4. **Intuitive partitioning**: One pointer tracks where to write unique elements, the other scans for them

### Strategy

- **Pointer 1 (`i`)**: Marks the position where the next unique element should be placed (the "write" pointer)
- **Pointer 2 (`j`)**: Scans through the array to find unique elements (the "read" pointer)

Since the array is sorted, if `nums[j] != nums[i]`, we've found a new unique element.[4][5]

## Java Solution

```java
public class Solution {
    /**
     * Removes duplicates from sorted array in-place
     * @param nums - sorted array in non-decreasing order
     * @return k - number of unique elements
     */
    public int removeDuplicates(int[] nums) {
        // Edge case: empty array or single element
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        // i tracks the position of the last unique element
        // Start at 0 because first element is always unique
        int i = 0;
        
        // j scans through the array starting from index 1
        for (int j = 1; j < nums.length; j++) {
            // If current element is different from the last unique element
            if (nums[j] != nums[i]) {
                // Move i forward to next position
                i++;
                // Place the new unique element at position i
                nums[i] = nums[j];
            }
            // If nums[j] == nums[i], it's a duplicate, so we skip it
        }
        
        // Return count of unique elements (i is 0-indexed, so add 1)
        return i + 1;
    }
}
```

## Detailed Walkthrough

Let's trace through **Example 2**: `nums = [1][1][1][2][2][3][3][4]`

**Initial state:**
- `i = 0` (pointing to first element)
- Array: `[1][1][1][2][2][3][3][4]`
- Position: `i`

**Iteration by iteration:**

| j | nums[j] | nums[i] | Comparison | Action | Array State | i |
|---|---------|---------|------------|--------|-------------|---|
| 1 | 0 | 0 | 0 == 0 | Skip (duplicate) | `[1][1][1][2][2][3][3][4]` | 0 |
| 2 | 1 | 0 | 1 != 0 | i++, nums[1]=1 | `[1][1][1][1][2][2][3][3][4]` | 1 |
| 3 | 1 | 1 | 1 == 1 | Skip (duplicate) | `[0,1,1,1,1,2,2,3,3,4]` | 1 |
| 4 | 1 | 1 | 1 == 1 | Skip (duplicate) | `[0,1,1,1,1,2,2,3,3,4]` | 1 |
| 5 | 2 | 1 | 2 != 1 | i++, nums[6]=2 | `[0,1,2,1,1,2,2,3,3,4]` | 2 |
| 6 | 2 | 2 | 2 == 2 | Skip (duplicate) | `[0,1,2,1,1,2,2,3,3,4]` | 2 |
| 7 | 3 | 2 | 3 != 2 | i++, nums[2]=3 | `[0,1,2,3,1,2,2,3,3,4]` | 3 |
| 8 | 3 | 3 | 3 == 3 | Skip (duplicate) | `[0,1,2,3,1,2,2,3,3,4]` | 3 |
| 9 | 4 | 3 | 4 != 3 | i++, nums[3]=4 | `[0,1,2,3,4,2,2,3,3,4]` | 4 |

**Result:** Return `i + 1 = 5`, with first 5 elements being `[1][2][3][4]` ✓

## Complexity Analysis

### Time Complexity: O(n)
- Single pass through the array with pointer `j`
- Each element is visited exactly once
- All operations inside the loop are O(1)[5]

### Space Complexity: O(1)
- Only two integer variables (`i` and `j`) used
- No additional data structures
- Modifies array in-place as required[1][5]

## Why This Approach is Optimal

1. **Can't do better than O(n) time**: We must examine every element at least once to identify duplicates
2. **Minimal space usage**: Two pointers is the minimum needed for this algorithm pattern
3. **Exploits sorted property**: The sorted nature means duplicates are adjacent, so we don't need hash sets or sorting (which would be O(n) space or O(n log n) time respectively)
4. **Single pass**: Unlike naive approaches that might require multiple passes or nested loops
5. **Clean and maintainable**: Simple logic, easy to understand and debug

