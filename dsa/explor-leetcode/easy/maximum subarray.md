## Problem Understanding

This is the **"Maximum Subarray"** problem, also known as the **maximum segment sum problem**. Find the contiguous subarray with the largest sum.[1][2]

This is a classic problem with multiple elegant solutions demonstrating different algorithmic paradigms.[3][4][1]

## Solution 1: Kadane's Algorithm (Optimal - O(n) Time, O(1) Space)

**Kadane's algorithm** uses dynamic programming to track the maximum sum ending at each position.[4][1][3]

### Key Insight

At each element, we decide: **extend the previous maximum subarray or start fresh?**
- `maxEnding = max(current_element, maxEnding + current_element)`[1]

```java
public class Solution {
    /**
     * Finds maximum subarray sum using Kadane's algorithm
     * O(n) time, O(1) space
     */
    public int maxSubArray(int[] nums) {
        int maxEnding = nums[0];  // Max sum ending at current index
        int maxSoFar = nums[0];   // Global maximum
        
        for (int i = 1; i < nums.length; i++) {
            // Either extend previous subarray or start new from current element
            maxEnding = Math.max(nums[i], maxEnding + nums[i]);
            
            // Update global maximum if current is better
            maxSoFar = Math.max(maxSoFar, maxEnding);
        }
        
        return maxSoFar;
    }
}
```

### Example Walkthrough: `nums = [-2,1,-3,4,-1,2,1,-5,4]`

| i | nums[i] | maxEnding | maxSoFar | Decision |
|---|---------|-----------|----------|----------|
| 0 | -2 | -2 | -2 | Start |
| 1 | 1 | max(1, -1) = 1 | 1 | Start fresh |
| 2 | -3 | max(-3, -2) = -2 | 1 | Extend |
| 3 | 4 | max(4, 2) = 4 | 4 | Start fresh |
| 4 | -1 | max(-1, 3) = 3 | 4 | Extend |
| 5 | 2 | max(2, 5) = 5 | 5 | Extend |
| 6 | 1 | max(1, 6) = 6 | 6 | Extend |
| 7 | -5 | max(-5, 1) = 1 | 6 | Extend |
| 8 | 4 | max(4, 5) = 5 | 6 | Extend |

**Result**: `6` (subarray [4,-1,2,1]) ✓

**Complexity**:
- **Time**: O(n) - single pass[3][1]
- **Space**: O(1) - only two variables[1]

***

## Solution 2: Divide and Conquer (Follow-up)

The divide-and-conquer approach splits the array and finds the maximum subarray crossing the midpoint.[5][6]

```java
public class SolutionDivideConquer {
    /**
     * Finds maximum subarray sum using divide and conquer
     * O(n log n) time, O(log n) space (recursion stack)
     */
    public int maxSubArray(int[] nums) {
        return maxSubArrayHelper(nums, 0, nums.length - 1);
    }
    
    private int maxSubArrayHelper(int[] nums, int left, int right) {
        // Base case: single element
        if (left == right) {
            return nums[left];
        }
        
        // Divide
        int mid = left + (right - left) / 2;
        
        // Conquer: find max in left and right halves
        int leftMax = maxSubArrayHelper(nums, left, mid);
        int rightMax = maxSubArrayHelper(nums, mid + 1, right);
        
        // Find max crossing the midpoint
        int crossMax = maxCrossingSubarray(nums, left, mid, right);
        
        // Combine: return the maximum of three
        return Math.max(crossMax, Math.max(leftMax, rightMax));
    }
    
    private int maxCrossingSubarray(int[] nums, int left, int mid, int right) {
        // Max subarray ending at mid (going left)
        int leftSum = Integer.MIN_VALUE;
        int sum = 0;
        for (int i = mid; i >= left; i--) {
            sum += nums[i];
            leftSum = Math.max(leftSum, sum);
        }
        
        // Max subarray starting at mid+1 (going right)
        int rightSum = Integer.MIN_VALUE;
        sum = 0;
        for (int i = mid + 1; i <= right; i++) {
            sum += nums[i];
            rightSum = Math.max(rightSum, sum);
        }
        
        // Combine the crossing subarrays
        return leftSum + rightSum;
    }
}
```

### How Divide and Conquer Works

For `[-2,1,-3,4,-1,2,1,-5,4]`:

```
                    Full Array (0-8)
                   /              \
             Left (0-3)         Right (4-8)
            /        \           /        \
        (0-1)    (2-3)     (4-6)      (7-8)
```

**Three cases for each subproblem**:
1. Maximum subarray in **left half**
2. Maximum subarray in **right half**
3. Maximum subarray **crossing midpoint**

Return the best of these three.[6][5]

**Complexity**:
- **Time**: O(n log n) - T(n) = 2T(n/2) + O(n)[6]
- **Space**: O(log n) - recursion stack depth[5]

***

## Edge Cases

| Case | Input | Output |
|------|-------|--------|
| Single element | [7] | 1 ✓ |
| All negative | [-2,-4] | -2 ✓ |
| All positive | [8][9][7][10][11] | 23 ✓ |
| Mixed | [-2,1,-3,4,-1,2,1,-5,4] | 6 ✓ |

## Comparison

| Approach | Time | Space | Use Case |
|----------|------|-------|----------|
| **Kadane's** | **O(n)** | **O(1)** | **Production, interviews** |
| Divide & Conquer | O(n log n) | O(log n) | Educational, teaches DC pattern |
| Brute Force | O(n²) | O(1) | Small inputs only |

## Interview Tips

1. **Start with Kadane's**:
   - "At each element, I decide: extend the previous max subarray or start fresh."
   - "I track the max ending at current position and global max."

2. **Trace through example**:
   ```
   [-2, 1, -3, 4, -1, 2, 1, -5, 4]
   maxEnding: -2 → 1 → -2 → 4 → 3 → 5 → 6 → 1 → 5
   maxSoFar:  -2 → 1 → 1  → 4 → 4 → 5 → 6 → 6 → 6 ✓
   ```

3. **Mention divide and conquer for follow-up**:
   - "Divide array at midpoint, recursively solve both halves."
   - "Find max subarray crossing midpoint, return the best of three."
   - "Slightly less efficient (O(n log n)) but demonstrates DC pattern."

4. **Handle edge cases**:
   - "Works for all negative numbers - returns largest negative."
   - "Works for single element."

Kadane's algorithm is the gold standard solution for this classic problem.[2][4][3][1]

