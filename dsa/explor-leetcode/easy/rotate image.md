## Problem Understanding

This is the **"Rotate Image"** problem where you need to rotate an n×n matrix 90 degrees **clockwise in-place**. The critical constraint from the attached document is that you must use **O(1) extra space**.[1][2][3][4]

Key requirements:
1. **In-place rotation**: Modify the input matrix directly[1]
2. **No extra matrix**: Cannot allocate another n×n array
3. **90° clockwise**: Each element moves to a new position following rotation rules

## The Mathematical Insight

After a 90° clockwise rotation:[3][4]
- **First row** → **Last column**
- **Second row** → **Second-to-last column**  
- **Last row** → **First column**

**Position mapping**:
$$
matrix[i][j] \rightarrow matrix[j][n-1-i]
$$

## Optimal Solution: Transpose + Reverse Rows

The **most elegant approach** breaks the rotation into two simple operations:[5][4][6]

1. **Transpose the matrix**: Swap rows with columns
2. **Reverse each row**: Flip elements horizontally

### Why This Works

**Mathematical proof**:[4][5]

**Step 1 - Transpose**: Reflects matrix over main diagonal
$$
matrix[i][j] \leftrightarrow matrix[j][i]
$$

**Step 2 - Reverse rows**: Flips matrix horizontally
$$
matrix[i][j] \leftrightarrow matrix[i][n-1-j]
$$

**Combined effect**:
$$
matrix[i][j] \xrightarrow{\text{transpose}} matrix[j][i] \xrightarrow{\text{reverse}} matrix[j][n-1-i]
$$

This achieves the 90° clockwise rotation formula![4]

### Visual Demonstration

```
Original:           After Transpose:      After Reverse Rows:
[1, 2, 3]          [1, 4, 7]             [7, 4, 1]
[4, 5, 6]    →     [2, 5, 8]       →     [8, 5, 2]
[7, 8, 9]          [3, 6, 9]             [9, 6, 3]
```

**Step-by-step**:
1. Transpose: Row 0 `[1][12][13]` becomes Column 0 `[1][14][15]` which is now Row 0
2. Reverse: `[1][14][15]` becomes `[15][14][1]` ✓

## Java Solution (Transpose + Reverse)

```java
public class Solution {
    /**
     * Rotates n×n matrix 90 degrees clockwise in-place
     * Uses transpose + reverse rows approach
     * 
     * @param matrix - n×n 2D array to rotate
     */
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        
        // Step 1: Transpose the matrix (swap rows with columns)
        // Only process upper triangle to avoid double-swapping
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // Swap matrix[i][j] with matrix[j][i]
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        
        // Step 2: Reverse each row
        for (int i = 0; i < n; i++) {
            reverseRow(matrix[i]);
        }
    }
    
    /**
     * Helper method to reverse a single row in-place
     * Uses two-pointer technique
     */
    private void reverseRow(int[] row) {
        int left = 0;
        int right = row.length - 1;
        
        while (left < right) {
            int temp = row[left];
            row[left] = row[right];
            row[right] = temp;
            left++;
            right--;
        }
    }
}
```

## Detailed Walkthrough

### Example 1: 3×3 Matrix

**Input**: `[[1][12][13], [14][16][17], [15][18][19]]`

**Step 1: Transpose**

Process upper triangle (i < j):

| i | j | Before | Swap | After |
|---|---|--------|------|-------|
| 0 | 1 | `[[1][12][13],[14][16][17],[15][18][19]]` | (0,1)↔(1,0): 2↔4 | `[[1][14][13],[12][16][17],[15][18][19]]` |
| 0 | 2 | `[[1][14][13],[12][16][17],[15][18][19]]` | (0,2)↔(2,0): 3↔7 | `[[1][14][15],[12][16][17],[13][18][19]]` |
| 1 | 2 | `[[1][14][15],[12][16][17],[3,8,9]]` | (1,2)↔(2,1): 6↔8 | `[[1,4,7],[2,5,8],[3,6,9]]` |

**After transpose**: `[[1][4][7], [2][5][8], [3][6]]`[7]

**Step 2: Reverse Each Row**

| Row | Before | After Reverse |
|-----|--------|---------------|
| 0 | `[1,4,7]` | `[7,4,1]` |
| 1 | `[2,5,8]` | `[8,5,2]` |
| 2 | `[3,6,9]` | `[9,6,3]` |

**Final result**: `[[7][4][1], [8][5][2], [9][6]]` ✓[8]

### Example 2: 4×4 Matrix

**Input**: `[[5][1][9][11], [2][4][8][10], [13][3][6][7], [15][14]]`[9][10]

**After Transpose**:
```
[[5,2,13,15],
 [1,4,3,14],
 [9,8,6,12],
 [11,10,7,16]]
```

**After Reverse Rows**:
```
[[15,13,2,5],
 [14,3,4,1],
 [12,6,8,9],
 [16,7,10,11]]
```
✓

## Alternative Solution: Layer-by-Layer Rotation

Another in-place approach rotates elements in concentric layers:[7]

```java
public class SolutionLayered {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        
        // Process concentric layers from outside to inside
        for (int layer = 0; layer < n / 2; layer++) {
            int first = layer;
            int last = n - 1 - layer;
            
            // Rotate elements in current layer
            for (int i = first; i < last; i++) {
                int offset = i - first;
                
                // Save top element
                int top = matrix[first][i];
                
                // Left → Top
                matrix[first][i] = matrix[last - offset][first];
                
                // Bottom → Left
                matrix[last - offset][first] = matrix[last][last - offset];
                
                // Right → Bottom
                matrix[last][last - offset] = matrix[i][last];
                
                // Top → Right (from saved value)
                matrix[i][last] = top;
            }
        }
    }
}
```

**How it works**:[7]
- Divides matrix into concentric square layers
- Performs 4-way rotation for each layer
- Elements move in circular fashion: top → right → bottom → left → top

**Complexity**: Same as transpose method, but more complex logic.

## Complexity Analysis

### Transpose + Reverse Approach

**Time Complexity: O(n²)**[6][4]
- Transpose: O(n²/2) to swap upper triangle
- Reverse rows: O(n²/2) to reverse n rows of n elements
- Total: O(n²)

**Space Complexity: O(1)**[1][4]
- Only uses constant extra space for `temp` variable
- All swaps done in-place
- Satisfies the in-place constraint

### Layer-by-Layer Approach

**Time Complexity: O(n²)**
- Visits each element once

**Space Complexity: O(1)**
- Only uses constant variables

## Comparison of Approaches

| Approach | Time | Space | Readability | Lines of Code |
|----------|------|-------|-------------|---------------|
| **Transpose + Reverse** | **O(n²)** | **O(1)** | **High** | **~20** |
| Layer-by-Layer | O(n²) | O(1) | Medium | ~35 |
| Create New Matrix | O(n²) | O(n²) | High | ~15 (violates constraint) |

## Why Transpose + Reverse is Optimal

### 1. **Simple and Intuitive**
Two well-known operations (transpose, reverse) combined elegantly.[6][4]

### 2. **Easy to Remember**
"Transpose then reverse rows" is a simple mnemonic.[5][3]

### 3. **Reusable Pattern**
- **90° clockwise**: Transpose + reverse rows
- **90° counter-clockwise**: Reverse rows + transpose (or transpose + reverse columns)
- **180°**: Reverse rows + reverse columns[5]

### 4. **Minimal Code**
Clean, maintainable implementation in ~20 lines.

### 5. **No Complex Index Math**
Unlike layer approach, no complicated offset calculations.[7]

## Rotation Variations

### 90° Counter-Clockwise

```java
public void rotateCounterClockwise(int[][] matrix) {
    int n = matrix.length;
    
    // Method 1: Reverse rows first, then transpose
    for (int i = 0; i < n; i++) {
        reverseRow(matrix[i]);
    }
    
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }
}
```

### 180° Rotation

```java
public void rotate180(int[][] matrix) {
    int n = matrix.length;
    
    // Reverse all rows
    for (int i = 0; i < n; i++) {
        reverseRow(matrix[i]);
    }
    
    // Reverse all columns (reverse row order)
    for (int i = 0; i < n / 2; i++) {
        int[] temp = matrix[i];
        matrix[i] = matrix[n - 1 - i];
        matrix[n - 1 - i] = temp;
    }
}
```

## Edge Cases

### Case 1: 1×1 Matrix
```java
matrix = [[1]]
Output: [[1]]  // No change needed
```

### Case 2: 2×2 Matrix
```java
matrix = [[1,2], [3,4]]
Transpose: [[1,3], [2,4]]
Reverse: [[3,1], [4,2]]
Output: [[3,1], [4,2]] ✓
```

### Case 3: Negative Numbers
```java
matrix = [[-1,-2], [-3,-4]]
Output: [[-3,-1], [-4,-2]]  // Works with negatives
```

### Case 4: All Same Elements
```java
matrix = [[5,5], [5,5]]
Output: [[5,5], [5,5]]  // Still works correctly
```

### Case 5: Large Matrix (20×20)
```java
n = 20
// Algorithm handles efficiently
```

## Common Mistakes to Avoid

### 1. **Double-Swapping in Transpose**
```java
// WRONG: Swaps entire matrix, undoing the transpose
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {  // Should be j = i + 1
        swap(matrix[i][j], matrix[j][i]);
    }
}

// CORRECT: Only swap upper triangle
for (int i = 0; i < n; i++) {
    for (int j = i + 1; j < n; j++) {
        swap(matrix[i][j], matrix[j][i]);
    }
}
```

### 2. **Reversing Columns Instead of Rows**
```java
// WRONG for clockwise: Reverses columns
// This gives counter-clockwise rotation

// CORRECT for clockwise: Reverse rows
for (int i = 0; i < n; i++) {
    reverseRow(matrix[i]);
}
```

### 3. **Creating New Matrix**
```java
// WRONG: Violates in-place constraint
int[][] rotated = new int[n][n];  // O(n²) space!
```

### 4. **Wrong Order of Operations**
```java
// WRONG: Reverse first, then transpose gives counter-clockwise
reverseRows(matrix);
transpose(matrix);

// CORRECT for clockwise: Transpose first, then reverse
transpose(matrix);
reverseRows(matrix);
```

## Interview Tips

1. **Visualize the transformation**:
   - Draw a 3×3 matrix
   - Show transpose step
   - Show reverse step
   - This demonstrates clear understanding[3]

2. **Explain the two-step approach**:
   - "First, I'll transpose to swap rows with columns."
   - "Then, I'll reverse each row to complete the rotation."

3. **Mention space constraint**:
   - "The problem requires O(1) space, so I can't create a new matrix."
   - "Transpose and reverse can both be done in-place."[1]

4. **Discuss alternatives**:
   - "Could also rotate layer-by-layer with 4-way swaps."
   - "But transpose + reverse is simpler and equally efficient."

5. **Show rotation variations**:
   - "For counter-clockwise, I'd transpose then reverse columns."
   - "For 180°, I'd reverse rows then reverse columns."

6. **Walk through example**:
   ```
   [1,2,3]  transpose→  [1,4,7]  reverse→  [7,4,1]
   [4,5,6]              [2,5,8]            [8,5,2]
   [7,8,9]              [3,6,9]            [9,6,3]
   ```
