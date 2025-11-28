## Problem Understanding

Given two sorted arrays `nums1` and `nums2` with counts `m` and `n` respectively, you need to merge them **in-place** inside `nums1` which has additional space allocated at the end for `nums2` elements.[1]

- `nums1` contains the first `m` valid elements and `n` trailing zeros.
- `nums2` contains `n` valid elements.
- You must merge `nums1` and `nums2` into a single sorted array in `nums1`.
- The expected time complexity is O(m + n).

## Optimal Solution: Merge from the End (Two-Pointer)

Because `nums1` has extra space at the end, we can fill it **backwards** starting from the last index `m + n - 1` to avoid overwriting elements that haven't been processed yet.[1]

### Why Merge Backwards?

- If we merged from the front, we'd overwrite the beginning elements in `nums1` before comparing.
- Starting from the end lets us place the largest elements first without overwriting.
- Two pointers track the current largest elements in `nums1` (index `m - 1`) and `nums2` (index `n - 1`).
- We compare and insert the larger element at the merging position from the back, moving pointers accordingly.

## Java Code Implementation

```java
public class Solution {
    /**
     * Merges two sorted arrays nums1 and nums2 into nums1 in-place.
     * @param nums1 - first array with size m + n, first m elements valid
     * @param m - number of valid elements in nums1
     * @param nums2 - second sorted array of size n
     * @param n - number of valid elements in nums2
     */
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // Pointers for nums1 and nums2 starting at last valid element
        int i = m - 1;   // Last element of nums1
        int j = n - 1;   // Last element of nums2
        int k = m + n - 1; // Last position of nums1
        
        // Merge backwards while elements exist in both arrays
        while (i >= 0 && j >= 0) {
            // Compare elements from nums1 and nums2 and insert the larger one
            if (nums1[i] > nums2[j]) {
                nums1[k] = nums1[i];
                i--;
            } else {
                nums1[k] = nums2[j];
                j--;
            }
            k--;
        }
        
        // If nums2 still has elements, copy remaining elements to nums1
        // No need to copy remaining nums1 because they are already in place
        while (j >= 0) {
            nums1[k] = nums2[j];
            j--;
            k--;
        }
    }
}
```

## Detailed Example Walkthrough

### Example 1:

```
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6], n = 3

Initial pointers:
i = 2 (nums1[2] = 3)
j = 2 (nums2[2] = 6)
k = 5 (last index)
```

**Step-by-step Merge**:

| i (nums1) | j (nums2) | k (nums1)| Action                  | Result                          |
|-----------|-----------|----------|-------------------------|--------------------------------|
| 3         | 6         | 5        | 6 > 3: nums1[2]=6       | `[1,2,3,0,0,6]`                |
| 3         | 5         | 4        | 5 > 3: nums1[3]=5       | `[1,2,3,0,5,6]`                |
| 3         | 2         | 3        | 3 > 2: nums1[4]=3       | `[1,2,3,3,5,6]`                |
| 2         | 2         | 2        | 2 == 2: nums1[5]=2      | `[1,2,2,3,5,6]`                |
| 1         | 1         | 1        | 2 exhausted, copy nums1 | Nothing changes                 |
| 1         | 1         | 0        | j exhausted              | Nothing changes                 |

Final merged array: `[1,2,2,3,5,6]`

### Example 2:

```
nums1 = [1], m = 1
nums2 = [], n = 0

No merge needed.
Output: [1]
```

### Example 3:

```
nums1 = [0], m = 0
nums2 = [1], n = 1

Copy nums2 to nums1:
nums1 = [1]
```

## Complexity Analysis

**Time Complexity: O(m + n)**  
- Each element is processed at most once  
- No nested loops, simple linear scan and insertion

**Space Complexity: O(1)**  
- In-place merge inside nums1 without extra array allocations

## Why This Approach is Optimal

- This is the canonical method to merge two sorted arrays in-place.[1]
- Cannot do better than linear time because every element must be processed.  
- Starts filling from the back so as not to overwrite unmerged parts of nums1.

## Common Mistakes to Avoid

1. **Merging Forward**: Attempting to merge from the front overwrites elements in nums1 before processing.  
2. **Not Copying Remaining Elements of nums2**: After one pointer exhausts, forgetting to copy remaining elements from nums2.  
3. **Off by One Errors in Indices**: Careful with indices `m-1`, `n-1`, and `m + n - 1`.  
4. **Handling Cases Where m=0 or n=0**: Make sure algorithm handles empty arrays correctly without errors.  

## Summary

This in-place merge algorithm leverages the extra space at the end of `nums1` and uses two pointers from the ends of valid ranges to efficiently merge the arrays in O(m + n) time and O(1) space, making it the gold standard for this problem.

