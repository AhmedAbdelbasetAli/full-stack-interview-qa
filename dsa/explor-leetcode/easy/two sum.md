## Problem Understanding

This is the legendary **"Two Sum"** problem - arguably the most famous interview question in tech. You need to find **two indices** where the values sum to a target.[1][2][3]

Key constraints:
1. **Exactly one solution exists** - guaranteed by problem statement
2. **Cannot use same element twice** - different indices required
3. **Return indices, not values**
4. **Follow-up**: Better than O(n²) time complexity[2][4]

## Optimal Solution: One-Pass HashMap

The **most elegant and efficient approach** uses a HashMap to achieve O(n) time complexity in a single pass.[4][5][1]

### The Core Insight

Instead of checking every pair, ask: **"For each number, does its complement exist?"**

**Mathematical relationship**:
$$
\text{If } nums[i] + nums[j] = target
$$
$$
\text{Then } nums[j] = target - nums[i]
$$

**Strategy**:[3][1]
1. For current number `nums[i]`, calculate `complement = target - nums[i]`
2. Check if complement already seen in HashMap
3. If yes → found the pair! Return indices
4. If no → store current number with its index for future lookups

### Why HashMap is Optimal

- **O(1) lookup**: HashMap provides constant-time search[4]
- **Single pass**: Only iterate through array once[5][1]
- **Space-time tradeoff**: Trade O(n) space for O(n) time[6]
- **Early termination**: Stops as soon as pair is found

## Java Solution (One-Pass HashMap)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Finds two indices where values sum to target
     * Uses one-pass HashMap for O(n) time complexity
     * 
     * @param nums - array of integers
     * @param target - target sum
     * @return array of two indices [i, j] where nums[i] + nums[j] = target
     */
    public int[] twoSum(int[] nums, int target) {
        // HashMap to store: value -> index
        Map<Integer, Integer> map = new HashMap<>();
        
        // Single pass through array
        for (int i = 0; i < nums.length; i++) {
            // Calculate what number we need to reach target
            int complement = target - nums[i];
            
            // Check if we've seen the complement before
            if (map.containsKey(complement)) {
                // Found it! Return the indices
                return new int[]{map.get(complement), i};
            }
            
            // Haven't found pair yet, store current number and index
            map.put(nums[i], i);
        }
        
        // Should never reach here based on problem constraints
        // (guaranteed exactly one solution exists)
        throw new IllegalArgumentException("No solution found");
    }
}
```

## Detailed Walkthrough

### Example 1: `nums = [11][12][13][14]`, `target = 9`

**Step-by-step execution**:

| i | nums[i] | complement | map.containsKey(complement)? | Action | HashMap State |
|---|---------|------------|------------------------------|--------|---------------|
| 0 | 2 | 9 - 2 = 7 | No (map is empty) | Add {2:0} | `{2:0}` |
| 1 | 7 | 9 - 7 = 2 | **Yes** (2 is at index 0) | **Return [7]** ✓ | - |

**Output**: `[15]` because `nums + nums[15] = 2 + 7 = 9`

**Key insight**: Found the answer in just 2 iterations!

### Example 2: `nums = [16][11][17]`, `target = 6`

| i | nums[i] | complement | Found? | Action | HashMap State |
|---|---------|------------|--------|--------|---------------|
| 0 | 3 | 6 - 3 = 3 | No | Add {3:0} | `{3:0}` |
| 1 | 2 | 6 - 2 = 4 | No | Add {2:1} | `{3:0, 2:1}` |
| 2 | 4 | 6 - 4 = 2 | **Yes** (2 at index 1) | **Return [7][8]** ✓ | - |

**Output**: `[15][11]` because `nums[15] + nums[11] = 2 + 4 = 6`

**Important**: We correctly skip using the same element twice (index 0 can't pair with itself).

### Example 3: `nums = [16][16]`, `target = 6`

| i | nums[i] | complement | Found? | Action | HashMap State |
|---|---------|------------|--------|--------|---------------|
| 0 | 3 | 6 - 3 = 3 | No | Add {3:0} | `{3:0}` |
| 1 | 3 | 6 - 3 = 3 | **Yes** (3 at index 0) | **Return [7]** ✓ | - |

**Output**: `[15]` because `nums + nums[15] = 3 + 3 = 6`

**Critical**: This demonstrates the algorithm correctly handles duplicate values at different indices.

## Visual Representation

Let's visualize the HashMap lookup process for `[11][12][13][14]`, `target = 9`:

```
Iteration 0: nums[0] = 2
  complement = 9 - 2 = 7
  HashMap: {} (empty)
  7 not found → add 2
  HashMap: {2 → 0}

Iteration 1: nums[1] = 7
  complement = 9 - 7 = 2
  HashMap: {2 → 0}
  2 FOUND at index 0! ✓
  Return [0, 1]
```

For `[16][11][17]`, `target = 6`:

```
i=0: nums[0]=3, complement=3, map={}, not found → map={3→0}
i=1: nums[1]=2, complement=4, map={3→0}, not found → map={3→0, 2→1}
i=2: nums[2]=4, complement=2, map={3→0, 2→1}, FOUND at index 1! ✓
Return [1, 2]
```

## Complexity Analysis

### One-Pass HashMap

**Time Complexity: O(n)**[1][4]
- Single iteration through array: O(n)
- HashMap operations (containsKey, get, put): O(1) average
- Total: O(n)

**Space Complexity: O(n)**[1][4]
- HashMap stores up to n elements in worst case
- Worst case: when solution is the last pair checked

## Alternative Approaches

### Approach 1: Brute Force (O(n²))

```java
public class SolutionBruteForce {
    public int[] twoSum(int[] nums, int target) {
        // Check every possible pair
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i, j};
                }
            }
        }
        throw new IllegalArgumentException("No solution");
    }
}
```

**Complexity**:[2]
- Time: O(n²) - nested loops
- Space: O(1) - no extra data structures

**When to use**: 
- Very small arrays (n < 10)
- Extremely memory-constrained environments
- Educational purposes to show inefficiency

### Approach 2: Two-Pass HashMap

```java
public class SolutionTwoPass {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        // First pass: populate HashMap
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        
        // Second pass: find complement
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            
            // Make sure not to use same element twice
            if (map.containsKey(complement) && map.get(complement) != i) {
                return new int[]{i, map.get(complement)};
            }
        }
        
        throw new IllegalArgumentException("No solution");
    }
}
```

**Complexity**:
- Time: O(n) - two passes
- Space: O(n)

**Drawback**: Less elegant than one-pass, requires extra check for duplicate indices.[9][10]

### Approach 3: Sorting + Two Pointers (Changes Indices)

```java
// WRONG for this problem: Sorting loses original indices!
public int[] twoSumSorted(int[] nums, int target) {
    Arrays.sort(nums);  // This destroys original indices!
    
    int left = 0, right = nums.length - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            // Can't return correct original indices
            return new int[]{left, right};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    throw new IllegalArgumentException("No solution");
}
```

**Problem**: Sorting changes array order, losing original indices.[3]

**When this works**: If problem asks for **values** instead of **indices**, or if array is already sorted.

## Comparison Table

| Approach | Time | Space | Pros | Cons |
|----------|------|-------|------|------|
| **One-Pass HashMap** | **O(n)** | **O(n)** | **Fastest, elegant** | **Uses extra space** |
| Two-Pass HashMap | O(n) | O(n) | Clear separation of logic | Slightly slower, extra check needed |
| Brute Force | O(n²) | O(1) | No extra space | Too slow for large inputs |
| Sort + Two Pointers | O(n log n) | O(1) | Works for sorted arrays | Loses original indices |

## Why One-Pass HashMap is Optimal

### 1. **Cannot Do Better Than O(n) Time**
Must examine each element at least once to find the solution.[4]

### 2. **Elegant Space-Time Tradeoff**
Trading O(n) space for O(n) time is worth it for performance.[6]

### 3. **Early Termination**
Stops immediately when pair is found, often before reaching end of array.

### 4. **Clean Code**
Simple, readable, and maintainable - no nested loops or complex logic.[5][1]

## Edge Cases

### Case 1: Solution at Start
```java
nums = [2,7,11,15], target = 9
Output: [0,1]  // Found immediately
```

### Case 2: Solution at End
```java
nums = [11,15,2,7], target = 9
Output: [2,3]  // Found at end
```

### Case 3: Negative Numbers
```java
nums = [-3,4,3,90], target = 0
Output: [0,2]  // -3 + 3 = 0
```

### Case 4: Duplicate Values (Different Indices)
```java
nums = [3,3], target = 6
Output: [0,1]  // Same value, different indices
```

### Case 5: Large Numbers
```java
nums = [1000000000, 2, -1000000000], target = 2
Output: [0,2]  // Handles large integers
```

### Case 6: Zero in Array
```java
nums = [0,4,3,0], target = 0
Output: [0,3]  // 0 + 0 = 0
```

## Common Mistakes to Avoid

### 1. **Using Same Element Twice**
```java
// WRONG: This would allow nums[i] + nums[i]
if (map.containsKey(complement)) {
    return new int[]{map.get(complement), i};
}
map.put(nums[i], i);
```

**Why it works correctly**: We add to map **after** checking, so current element isn't in map yet.[1]

### 2. **Returning Values Instead of Indices**
```java
// WRONG: Problem asks for indices, not values
return new int[]{nums[i], complement};
```

**Correct**: Return indices like `new int[]{map.get(complement), i}`.

### 3. **Not Handling No Solution**
```java
// Should throw exception or return empty array
// Based on problem: "exactly one solution exists", so this is theoretical
```

### 4. **Overcomplicating with Two-Pass**
```java
// WRONG: Unnecessarily complex with duplicate index check
if (map.containsKey(complement) && map.get(complement) != i)
```

**Better**: Use one-pass approach which naturally avoids this issue.

## Interview Tips

1. **Ask clarifying questions**:
   - "Can the array contain duplicates?" (Yes)
   - "Can numbers be negative?" (Yes)
   - "Is the array sorted?" (Usually no, but good to ask)

2. **Start with brute force**:
   - "I could check every pair with nested loops - O(n²) time, O(1) space."
   - "But we can do better..."

3. **Explain the optimization**:
   - "Instead of checking all pairs, I'll use a HashMap to look up complements in O(1) time."
   - "For each number, I check if its complement exists, then add it to the map."

4. **Walk through example**:
   - Draw the array `[11][12][13][14]`
   - Show HashMap state at each step
   - Demonstrate finding the answer at index 1

5. **Discuss trade-offs**:
   - "HashMap uses O(n) space but achieves O(n) time."
   - "This space-time tradeoff is worth it for large arrays."

6. **Mention follow-up variations**:
   - "If array is sorted, we could use two pointers without extra space."
   - "For finding three numbers (3Sum), we'd combine sorting + two pointers."

