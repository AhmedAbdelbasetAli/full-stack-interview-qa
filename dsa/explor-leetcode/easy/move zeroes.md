## Problem Understanding

This is the classic **"Move Zeroes"** problem where you need to **move all zeros to the end while preserving the relative order of non-zero elements**. The critical constraints are:[1][2]

1. **In-place**: O(1) extra space - can't create new array
2. **Preserve order**: Non-zero elements maintain their relative positions
3. **Follow-up**: Minimize total operations (important for optimization)

## Optimal Solution: Two-Pointer Technique

The **most efficient approach** uses two pointers to partition the array in a single pass.[3][2][4][5]

### The "Snowball" Concept

Think of zeros as a **snowball rolling through the array**:[4]
- As you scan left to right, collect zeros in a "snowball"
- When you encounter a non-zero, swap it with the leftmost zero in the snowball
- The snowball (zeros) grows and moves right, non-zeros stay on the left

### Why This Approach is Optimal

- **Single pass**: O(n) time with one iteration
- **Minimal swaps**: Only swaps when necessary (when non-zero follows zeros)
- **In-place**: O(1) extra space
- **Preserves order**: Non-zero elements shift left maintaining relative positions[2][5][3]

## Java Solution (Optimal - Swap Approach)

```java
public class Solution {
    /**
     * Moves all zeros to the end while maintaining non-zero order
     * Uses two-pointer technique with swapping for minimal operations
     * 
     * @param nums - array to modify in-place
     */
    public void moveZeroes(int[] nums) {
        // Edge case: null or single element
        if (nums == null || nums.length <= 1) {
            return;
        }
        
        // Pointer for position of next non-zero element
        int leftMostZeroIndex = 0;
        
        // Scan through array with second pointer
        for (int i = 0; i < nums.length; i++) {
            // When we find a non-zero element
            if (nums[i] != 0) {
                // Swap with leftmost zero position
                // If leftMostZeroIndex == i, it's a self-swap (no zeros seen yet)
                int temp = nums[leftMostZeroIndex];
                nums[leftMostZeroIndex] = nums[i];
                nums[i] = temp;
                
                // Move leftMostZeroIndex forward
                leftMostZeroIndex++;
            }
        }
    }
}
```

## Alternative Solution (Two-Pass - Simpler but More Operations)

```java
public class SolutionTwoPass {
    /**
     * Two-pass approach: collect non-zeros, then fill zeros
     * Easier to understand but performs more write operations
     */
    public void moveZeroes(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return;
        }
        
        // First pass: move all non-zero elements to front
        int writeIndex = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                nums[writeIndex] = nums[i];
                writeIndex++;
            }
        }
        
        // Second pass: fill remaining positions with zeros
        while (writeIndex < nums.length) {
            nums[writeIndex] = 0;
            writeIndex++;
        }
    }
}
```

**Trade-off**: Simpler logic but does **2n writes** vs **n swaps** in optimal solution.[6][7]

## Detailed Walkthrough

### Example 1: `nums = [11][12][13]`

**Swap Approach (Optimal)**:

| i | nums[i] | leftMostZeroIndex | Action | Array State | Explanation |
|---|---------|-------------------|--------|-------------|-------------|
| 0 | 0 | 0 | Skip (is zero) | `[11][12][13]` | First zero found |
| 1 | 1 | 0 | Swap(0,1) | `[11][12][13]` | 1 swaps with first zero |
| 2 | 0 | 1 | Skip (is zero) | `[11][12][13]` | Second zero |
| 3 | 3 | 1 | Swap(1,3) | `[11][12][13]` | 3 swaps with first zero |
| 4 | 12 | 2 | Swap(2,4) | `[11][12][13]` | 12 swaps with first zero |

**Result**: `[11][12][13]` ✓

**Key observation**: `leftMostZeroIndex` always points to the first zero in the "snowball" of zeros.

### Visual Step-by-Step

```
Initial: [0, 1, 0, 3, 12]
          ↑
          leftMostZeroIndex = 0

Step 1 (i=0): nums[0]=0, skip
         [0, 1, 0, 3, 12]
          ↑
          leftMostZeroIndex = 0

Step 2 (i=1): nums[1]=1 (non-zero), swap with index 0
         [1, 0, 0, 3, 12]
             ↑
             leftMostZeroIndex = 1

Step 3 (i=2): nums[2]=0, skip
         [1, 0, 0, 3, 12]
             ↑
             leftMostZeroIndex = 1

Step 4 (i=3): nums[3]=3 (non-zero), swap with index 1
         [1, 3, 0, 0, 12]
                ↑
                leftMostZeroIndex = 2

Step 5 (i=4): nums[4]=12 (non-zero), swap with index 2
         [1, 3, 12, 0, 0]
                   ↑
                   leftMostZeroIndex = 3

Done! ✓
```

### Example 2: All Non-Zeros `nums = [11][14][12][15]`

| i | nums[i] | leftMostZeroIndex | Action | Array State |
|---|---------|-------------------|--------|-------------|
| 0 | 1 | 0 | Swap(0,0) - self swap | `[11][14][12][15]` |
| 1 | 2 | 1 | Swap(1,1) - self swap | `[11][14][12][15]` |
| 2 | 3 | 2 | Swap(2,2) - self swap | `[11][14][12][15]` |
| 3 | 4 | 3 | Swap(3,3) - self swap | `[11][14][12][15]` |

**Result**: `[11][14][12][15]` (unchanged) ✓

**Insight**: When no zeros exist, `leftMostZeroIndex` always equals `i`, causing harmless self-swaps.

### Example 3: All Zeros `nums = `

| i | nums[i] | leftMostZeroIndex | Action | Array State |
|---|---------|-------------------|--------|-------------|
| 0 | 0 | 0 | Skip | `` |
| 1 | 0 | 0 | Skip | `` |
| 2 | 0 | 0 | Skip | `` |
| 3 | 0 | 0 | Skip | `` |

**Result**: `` (unchanged) ✓

**Insight**: `leftMostZeroIndex` never moves, no swaps occur.

### Example 4: Zeros at End `nums = [11][14][12]`

| i | nums[i] | leftMostZeroIndex | Action | Array State |
|---|---------|-------------------|--------|-------------|
| 0 | 1 | 0 | Swap(0,0) | `[11][14][12]` |
| 1 | 2 | 1 | Swap(1,1) | `[11][14][12]` |
| 2 | 3 | 2 | Swap(2,2) | `[11][14][12]` |
| 3 | 0 | 3 | Skip | `[11][14][12]` |
| 4 | 0 | 3 | Skip | `[11][14][12]` |

**Result**: `[11][14][12]` (unchanged) ✓

**Insight**: Already optimal, only self-swaps occur.

## Complexity Analysis

### Swap Approach (Optimal)

**Time Complexity: O(n)**[5][3][2]
- Single pass through array
- Each element visited exactly once
- Swap is O(1) operation

**Space Complexity: O(1)**[3][5]
- Only two variables: `i` and `leftMostZeroIndex`
- Modifies array in-place
- No additional data structures

**Operation Count**: 
- Best case (no zeros): n self-swaps
- Worst case (alternating 0s): ~n/2 meaningful swaps

### Two-Pass Approach

**Time Complexity: O(n)**
- First pass: O(n)
- Second pass: O(n)
- Total: O(2n) = O(n)

**Space Complexity: O(1)**

**Operation Count**: Always ~2n writes (less efficient than swap approach)

## Why Swap Approach Minimizes Operations

The follow-up asks: "Could you minimize the total number of operations?"

**Swap vs Two-Pass Comparison**:

| Input | Swap Operations | Two-Pass Writes |
|-------|----------------|-----------------|
| `[11][12][13]` | 3 swaps (6 writes) | 5 + 2 = 7 writes |
| `[11][14][12][15]` | 4 self-swaps | 8 writes |
| `` | 0 swaps | 4 writes |

**Why swap is better**:[4][3]
- Only performs operations when necessary (non-zero after zeros)
- Self-swaps can be optimized with an `if` check
- Avoids redundant overwrites in two-pass approach

## Optimized Version (Avoid Self-Swaps)

```java
public class SolutionOptimized {
    public void moveZeroes(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return;
        }
        
        int leftMostZeroIndex = 0;
        
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                // Only swap if needed (avoid self-swap)
                if (i != leftMostZeroIndex) {
                    int temp = nums[leftMostZeroIndex];
                    nums[leftMostZeroIndex] = nums[i];
                    nums[i] = temp;
                }
                leftMostZeroIndex++;
            }
        }
    }
}
```

**Improvement**: Eliminates unnecessary self-swaps when no zeros have been encountered yet.

## Alternative Approaches (Less Optimal)

### Approach 1: Count and Fill

```java
public void moveZeroesCountFill(int[] nums) {
    int zeroCount = 0;
    
    // Count zeros
    for (int num : nums) {
        if (num == 0) zeroCount++;
    }
    
    // Collect non-zeros
    int index = 0;
    for (int num : nums) {
        if (num != 0) {
            nums[index++] = num;
        }
    }
    
    // Fill zeros
    while (index < nums.length) {
        nums[index++] = 0;
    }
}
```

**Issues**: Three passes (count, collect, fill) - not optimal.

### Approach 2: Using Extra Space (VIOLATES CONSTRAINT)

```java
// WRONG: Uses O(n) space
public void moveZeroesWrong(int[] nums) {
    List<Integer> nonZero = new ArrayList<>();
    List<Integer> zeros = new ArrayList<>();
    
    for (int num : nums) {
        if (num == 0) zeros.add(num);
        else nonZero.add(num);
    }
    
    int i = 0;
    for (int num : nonZero) nums[i++] = num;
    for (int num : zeros) nums[i++] = num;
}
```

**Problem**: Violates O(1) space constraint.

## Edge Cases

### Case 1: Single Element
```java
nums = [0]
Output: [0]  // No change needed
```

### Case 2: No Zeros
```java
nums = [1,2,3,4,5]
Output: [1,2,3,4,5]  // Already optimal
```

### Case 3: All Zeros
```java
nums = [0,0,0,0]
Output: [0,0,0,0]  // No swaps needed
```

### Case 4: Alternating Pattern
```java
nums = [0,1,0,1,0,1]
Output: [1,1,1,0,0,0]
```

### Case 5: Zeros at Start
```java
nums = [0,0,0,1,2,3]
Output: [1,2,3,0,0,0]
```

### Case 6: Large Numbers
```java
nums = [0, 2147483647, -2147483648, 0, 1]
Output: [2147483647, -2147483648, 1, 0, 0]
// Works with any integer values
```

## Common Mistakes to Avoid

### 1. **Creating New Array**
```java
// WRONG: Violates in-place constraint
int[] result = new int[nums.length];
```

### 2. **Using Remove/Insert Operations**
```java
// WRONG: Inefficient O(n²) due to array shifting
for (int i = 0; i < nums.length; i++) {
    if (nums[i] == 0) {
        // Remove and add to end - causes array shifts
    }
}
```

### 3. **Not Preserving Order**
```java
// WRONG: Doesn't maintain relative order
// Just swapping first zero with last non-zero
```

### 4. **Forgetting Edge Cases**
```java
// Missing checks for null or empty arrays
if (nums == null || nums.length <= 1) return;
```

## Interview Tips

1. **Start with brute force**: "I could create a new array, but that violates the O(1) space constraint."

2. **Explain the two-pointer pattern**: "I'll use one pointer to track where the next non-zero should go, and another to scan the array."

3. **Walk through example**: Draw the array and show how pointers move: `[11][12][13]`.

4. **Discuss optimization**: "The swap approach minimizes operations compared to two-pass collect-and-fill."

5. **Mention edge cases**: "Works for no zeros, all zeros, and single element arrays."

6. **Real-world connection**: "This pattern is similar to partitioning in quicksort or the Dutch National Flag problem."

