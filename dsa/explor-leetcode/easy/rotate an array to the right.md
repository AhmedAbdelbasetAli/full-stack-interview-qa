## Problem Understanding

You need to rotate an array to the **right** by `k` steps in-place. Key observations:[1][2]

1. Rotating right by `k` means the last `k` elements move to the front
2. If `k > n`, we only need to rotate `k % n` times (rotations are cyclic)
3. The follow-up asks for **O(1) space complexity** - true in-place solution[1]

**Visual example**: `[11][12][13][14][15][16][17]`, `k=3`
- Last 3 elements `[5][6][7]` move to front
- First 4 elements `[1][2][3][4]` shift to the end
- Result: `[5][6][7][1][2][3]`[3]

## Approach 1: Reversal Algorithm (Optimal - In-Place)

This is the **most elegant and optimal solution** using the reversal technique. The brilliant insight is that array rotation can be achieved through **three strategic reversals**![2][1]

### The "Aha!" Moment

**Key Insight**: To rotate right by `k`, we can:
1. **Reverse the entire array**
2. **Reverse the first `k` elements**
3. **Reverse the remaining `n-k` elements**

Why this works mathematically:[4][2]
- Original: `[A...B]` where A = first `n-k` elements, B = last `k` elements
- After step 1: `[B'...A']` (both segments reversed)
- After step 2: `[B...A']` (first k reversed back)
- After step 3: `[B...A]` (remaining reversed back) ✓

### Java Solution

```java
public class Solution {
    /**
     * Rotates array to the right by k steps in-place
     * Uses the reversal algorithm for O(1) space complexity
     * 
     * @param nums - integer array to rotate
     * @param k - number of steps to rotate right
     */
    public void rotate(int[] nums, int k) {
        // Edge case: null or empty array
        if (nums == null || nums.length == 0) {
            return;
        }
        
        int n = nums.length;
        
        // Normalize k: if k > n, we only need k % n rotations
        // Example: rotating array of size 7 by 10 steps = rotating by 3 steps
        k = k % n;
        
        // If k is 0 after modulo, array stays the same
        if (k == 0) {
            return;
        }
        
        // Step 1: Reverse the entire array
        reverse(nums, 0, n - 1);
        
        // Step 2: Reverse the first k elements
        reverse(nums, 0, k - 1);
        
        // Step 3: Reverse the remaining n-k elements
        reverse(nums, k, n - 1);
    }
    
    /**
     * Helper method to reverse array elements in-place between indices
     * Uses two-pointer technique
     * 
     * @param nums - array to reverse
     * @param start - starting index (inclusive)
     * @param end - ending index (inclusive)
     */
    private void reverse(int[] nums, int start, int end) {
        // Two pointers: one at start, one at end
        while (start < end) {
            // Swap elements at start and end positions
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            
            // Move pointers towards center
            start++;
            end--;
        }
    }
}
```

### Detailed Walkthrough

Let's trace **Example 1**: `nums = [11][12][13][14][15][16][17]`, `k = 3`

**Initial Setup**:
- `n = 7`, `k = 3 % 7 = 3`

**Step 1: Reverse entire array** `reverse(nums, 0, 6)`

| Action | Array State | Explanation |
|--------|-------------|-------------|
| Initial | `[1,2,3,4,5,6,7]` | Original array |
| Swap(0,6) | `[7,2,3,4,5,6,1]` | Swap 1 ↔ 7 |
| Swap(1,5) | `[7,6,3,4,5,2,1]` | Swap 2 ↔ 6 |
| Swap(2,4) | `[7,6,5,4,3,2,1]` | Swap 3 ↔ 5 |
| Final | `[7,6,5,4,3,2,1]` | Completely reversed |

**Step 2: Reverse first k=3 elements** `reverse(nums, 0, 2)`

| Action | Array State | Explanation |
|--------|-------------|-------------|
| Start | `[7,6,5,4,3,2,1]` | After step 1 |
| Swap(0,2) | `[5,6,7,4,3,2,1]` | Swap 7 ↔ 5 |
| Final | `[5,6,7,4,3,2,1]` | First 3 elements reversed |

**Step 3: Reverse remaining n-k=4 elements** `reverse(nums, 3, 6)`

| Action | Array State | Explanation |
|--------|-------------|-------------|
| Start | `[5,6,7,4,3,2,1]` | After step 2 |
| Swap(3,6) | `[5,6,7,1,3,2,4]` | Swap 4 ↔ 1 |
| Swap(4,5) | `[5,6,7,1,2,3,4]` | Swap 3 ↔ 2 |
| Final | `[5,6,7,1,2,3,4]` | **Target result!** ✓ |

### Complexity Analysis

**Time Complexity: O(n)**[1]
- Step 1: Reverse all `n` elements → O(n)
- Step 2: Reverse first `k` elements → O(k)
- Step 3: Reverse remaining `n-k` elements → O(n-k)
- Total: O(n) + O(k) + O(n-k) = O(2n) = O(n)

**Space Complexity: O(1)**[2][1]
- Only uses constant extra space: `start`, `end`, `temp` variables
- No auxiliary arrays or recursion stack
- True in-place algorithm satisfying the follow-up requirement

## Approach 2: Using Extra Space (Straightforward)

This approach creates a new array to store rotated elements.[5][2]

```java
public class SolutionExtraSpace {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        
        // Create auxiliary array to store rotated result
        int[] rotated = new int[n];
        
        // Place each element at its new position
        for (int i = 0; i < n; i++) {
            // Element at index i goes to index (i + k) % n
            rotated[(i + k) % n] = nums[i];
        }
        
        // Copy back to original array
        for (int i = 0; i < n; i++) {
            nums[i] = rotated[i];
        }
    }
}
```

**Pros**:
- Simple and intuitive logic
- Easy to understand: directly place elements in new positions

**Cons**:
- O(n) extra space - violates the follow-up requirement
- Additional memory allocation overhead

**Complexity**: O(n) time, O(n) space

## Approach 3: Cyclic Replacements (Juggling Algorithm)

This approach uses the mathematical property that rotation creates cycles.[6]

```java
public class SolutionCyclic {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        
        if (k == 0) return;
        
        // Number of cycles = gcd(n, k)
        int cycles = gcd(n, k);
        
        // Process each cycle
        for (int start = 0; start < cycles; start++) {
            int current = start;
            int temp = nums[start];
            
            // Follow the cycle
            do {
                int next = (current + k) % n;
                int nextTemp = nums[next];
                nums[next] = temp;
                temp = nextTemp;
                current = next;
            } while (current != start);
        }
    }
    
    // Helper method to calculate Greatest Common Divisor
    private int gcd(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
}
```

**How it works**:[6]
- Elements form `gcd(n, k)` independent cycles
- Each cycle is rotated separately
- Example: `[11][12][13][14][15][16]`, `k=2` has `gcd(6,2)=2` cycles:
  - Cycle 1: 1→3→5→1
  - Cycle 2: 2→4→6→2

**Complexity**: O(n) time, O(1) space

**Note**: While this is O(1) space, it's more complex than the reversal method.

## Approach 4: Brute Force (One-by-One)

Rotate one position at a time, `k` times.[7]

```java
public class SolutionBruteForce {
    public void rotate(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        
        // Rotate one step at a time
        for (int step = 0; step < k; step++) {
            // Store last element
            int last = nums[n - 1];
            
            // Shift all elements one position to the right
            for (int i = n - 1; i > 0; i--) {
                nums[i] = nums[i - 1];
            }
            
            // Place last element at first position
            nums[0] = last;
        }
    }
}
```

**Complexity**: O(n × k) time, O(1) space

**Why it's inefficient**: If `k` is large, we do many redundant operations.

## Comparison of All Approaches

| Approach | Time | Space | Difficulty | Interview Preference |
|----------|------|-------|------------|---------------------|
| **Reversal Algorithm** | **O(n)** | **O(1)** | **Medium** | **⭐ Best choice** |
| Extra Space | O(n) | O(n) | Easy | Good for explaining |
| Cyclic Replacements | O(n) | O(1) | Hard | Shows advanced knowledge |
| Brute Force | O(n×k) | O(1) | Easy | Too slow |

## Why Reversal Algorithm is Optimal

### 1. **Satisfies All Requirements**
- ✓ In-place with O(1) space[2][1]
- ✓ Optimal O(n) time complexity
- ✓ Works for all edge cases (k=0, k>n, k=n)

### 2. **Elegant and Intuitive**
Once you understand the "three reversals" pattern, it's easy to remember and implement.[4][2]

### 3. **No Complex Mathematics**
Unlike cyclic replacements, you don't need to calculate GCD or track cycles.[6]

### 4. **Reusable Pattern**
The reversal technique applies to other problems:
- Rotating subarrays
- String rotations
- Palindrome problems

### 5. **Cache-Friendly**
Sequential memory access patterns work well with CPU caching.[1]

## Edge Cases to Consider

### Case 1: k = 0
```java
nums = [1,2,3,4,5], k = 0
// No rotation needed, array stays same
// Output: [1,2,3,4,5]
```

### Case 2: k ≥ n
```java
nums = [1,2,3], k = 5
// k % 3 = 2, so effectively rotate by 2
// Output: [2,3,1]
```

### Case 3: k = n
```java
nums = [1,2,3,4], k = 4
// k % 4 = 0, full rotation = no change
// Output: [1,2,3,4]
```

### Case 4: Single Element
```java
nums = [1], k = 100
// Single element always stays same
// Output: [1]
```

### Case 5: All Same Elements
```java
nums = [1,1,1,1], k = 2
// Still works correctly
// Output: [1,1,1,1]
```

## Common Mistakes to Avoid

### 1. **Forgetting k % n**
```java
// WRONG: If k > n, will go out of bounds
reverse(nums, 0, k - 1); 

// CORRECT: Normalize k first
k = k % n;
reverse(nums, 0, k - 1);
```

### 2. **Wrong Reversal Order**
For **right rotation**, the order must be:[4][2]
1. Reverse all
2. Reverse first k
3. Reverse last n-k

For **left rotation**, you'd reverse in different order.

### 3. **Off-by-One Errors**
```java
// WRONG: end index is exclusive
reverse(nums, 0, n); 

// CORRECT: end index is inclusive
reverse(nums, 0, n - 1);
```

### 4. **Not Handling k = 0**
While the algorithm handles it naturally, checking early improves efficiency:
```java
if (k == 0 || k % n == 0) return;
```

## Interview Tips

When solving this in an interview:[2]

1. **Start by clarifying**: "By 'right rotation', you mean moving elements from end to beginning, correct?"

2. **Mention multiple approaches**: "I can think of 3 approaches - using extra space O(n), reversal algorithm O(1), or cyclic replacements. Which would you prefer?"

3. **Explain the intuition**: Draw the three-reversal process on the whiteboard to visualize it.

4. **Discuss trade-offs**: "The reversal method is optimal for both time and space, making it the best choice."

5. **Test edge cases**: Walk through k=0, k>n, and single-element cases.

