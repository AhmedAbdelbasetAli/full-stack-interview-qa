## Problem Understanding

This is the **"Valid Sudoku"** problem where you need to validate a partially filled 9x9 Sudoku board. The key validation rules are:[1][2][3]

1. **Each row**: Contains digits 1-9 without repetition (excluding empty cells)
2. **Each column**: Contains digits 1-9 without repetition (excluding empty cells)
3. **Each 3x3 sub-box**: Contains digits 1-9 without repetition (excluding empty cells)

**Critical points**:
- Only validate **filled cells** (ignore `'.'` cells)
- Board doesn't need to be solvable, just valid
- Empty cells don't violate rules[2]

## Optimal Solution: One-Pass with HashSets

The **most efficient approach** uses HashSets to track seen digits in a single pass through the board.[4][5][2]

### The Core Insight

Instead of three separate passes (rows, columns, boxes), we can validate **all three constraints simultaneously** in one iteration.[5]

**Key technique for 3x3 boxes**:[4][5]
- Calculate box index using: `(row / 3) * 3 + (col / 3)`
- This maps any cell to its corresponding box number (0-8)

**Visual box numbering**:
```
Box indices:
[0][1][2]
[3][4][5]
[6][7][8]

Example: Cell (4, 7)
- Box index = (4/3)*3 + (7/3) = 1*3 + 2 = 5 ✓
```

### Why This Approach is Optimal

- **Single pass**: O(1) time - only 81 cells to check[2]
- **HashSet lookups**: O(1) for duplicate detection[4]
- **Efficient tracking**: Separate HashSets for rows, columns, and boxes
- **Early termination**: Returns false immediately on first duplicate

## Java Solution (One-Pass HashSet)

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /**
     * Validates a Sudoku board according to standard rules
     * Uses one-pass with HashSets for O(1) time complexity
     * 
     * @param board - 9x9 2D char array representing Sudoku board
     * @return true if valid, false otherwise
     */
    public boolean isValidSudoku(char[][] board) {
        // Create HashSets to track seen digits
        // Using array of HashSets: one for each row, column, and box
        Set<Character>[] rows = new HashSet[9];
        Set<Character>[] cols = new HashSet[9];
        Set<Character>[] boxes = new HashSet[9];
        
        // Initialize all HashSets
        for (int i = 0; i < 9; i++) {
            rows[i] = new HashSet<>();
            cols[i] = new HashSet<>();
            boxes[i] = new HashSet<>();
        }
        
        // Single pass through entire board
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                char current = board[row][col];
                
                // Skip empty cells
                if (current == '.') {
                    continue;
                }
                
                // Calculate which 3x3 box this cell belongs to
                int boxIndex = (row / 3) * 3 + (col / 3);
                
                // Check if digit already exists in current row
                if (rows[row].contains(current)) {
                    return false;  // Duplicate in row
                }
                
                // Check if digit already exists in current column
                if (cols[col].contains(current)) {
                    return false;  // Duplicate in column
                }
                
                // Check if digit already exists in current box
                if (boxes[boxIndex].contains(current)) {
                    return false;  // Duplicate in box
                }
                
                // Add digit to all three tracking sets
                rows[row].add(current);
                cols[col].add(current);
                boxes[boxIndex].add(current);
            }
        }
        
        // No duplicates found - board is valid
        return true;
    }
}
```

## Alternative Solution (Using String Encoding)

A clever approach using HashSet with encoded strings:[5][4]

```java
import java.util.HashSet;
import java.util.Set;

public class SolutionEncoded {
    public boolean isValidSudoku(char[][] board) {
        Set<String> seen = new HashSet<>();
        
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                char current = board[row][col];
                
                if (current == '.') {
                    continue;
                }
                
                // Create unique string identifiers for each constraint
                String rowKey = current + " in row " + row;
                String colKey = current + " in col " + col;
                String boxKey = current + " in box " + (row/3) + "-" + (col/3);
                
                // Try to add all three keys
                if (!seen.add(rowKey) || !seen.add(colKey) || !seen.add(boxKey)) {
                    return false;  // Duplicate detected
                }
            }
        }
        
        return true;
    }
}
```

**How it works**:
- Encodes each constraint as a unique string
- Example: `"5 in row 0"`, `"5 in col 3"`, `"5 in box 0-1"`
- Single HashSet tracks all constraints[5]

## Detailed Walkthrough

### Understanding Box Index Calculation

The formula `(row / 3) * 3 + (col / 3)` maps cells to boxes:[4][5]

| Cell (row, col) | row/3 | col/3 | (row/3)*3 + (col/3) | Box Index |
|----------------|-------|-------|---------------------|-----------|
| (0, 0) | 0 | 0 | 0*3 + 0 = 0 | 0 (top-left) |
| (0, 8) | 0 | 2 | 0*3 + 2 = 2 | 2 (top-right) |
| (4, 4) | 1 | 1 | 1*3 + 1 = 4 | 4 (center) |
| (8, 0) | 2 | 0 | 2*3 + 0 = 6 | 6 (bottom-left) |
| (8, 8) | 2 | 2 | 2*3 + 2 = 8 | 8 (bottom-right) |

**Visual representation**:
```
Cells → Boxes:
┌─────┬─────┬─────┐
│ 0 0 │ 0 1 │ 0 2 │   Box 0: rows 0-2, cols 0-2
│ 0 0 │ 0 1 │ 0 2 │   Box 1: rows 0-2, cols 3-5
│ 0 0 │ 0 1 │ 0 2 │   Box 2: rows 0-2, cols 6-8
├─────┼─────┼─────┤
│ 1 0 │ 1 1 │ 1 2 │   Box 3: rows 3-5, cols 0-2
│ 1 0 │ 1 1 │ 1 2 │   Box 4: rows 3-5, cols 3-5
│ 1 0 │ 1 1 │ 1 2 │   Box 5: rows 3-5, cols 6-8
├─────┼─────┼─────┤
│ 2 0 │ 2 1 │ 2 2 │   Box 6: rows 6-8, cols 0-2
│ 2 0 │ 2 1 │ 2 2 │   Box 7: rows 6-8, cols 3-5
│ 2 0 │ 2 1 │ 2 2 │   Box 8: rows 6-8, cols 6-8
└─────┴─────┴─────┘

Converted to box indices (row/3)*3 + (col/3):
┌─────┬─────┬─────┐
│ 0 0 │ 1 1 │ 2 2 │
│ 0 0 │ 1 1 │ 2 2 │
│ 0 0 │ 1 1 │ 2 2 │
├─────┼─────┼─────┤
│ 3 3 │ 4 4 │ 5 5 │
│ 3 3 │ 4 4 │ 5 5 │
│ 3 3 │ 4 4 │ 5 5 │
├─────┼─────┼─────┤
│ 6 6 │ 7 7 │ 8 8 │
│ 6 6 │ 7 7 │ 8 8 │
│ 6 6 │ 7 7 │ 8 8 │
└─────┴─────┴─────┘
```

### Example Walkthrough: Invalid Board

Let's trace the algorithm on **Example 2** (invalid board with duplicate 8):

```
Row 0: [8, 3, ., ., 7, ., ., ., .]
Row 3: [8, ., ., ., 6, ., ., ., 3]
```

**Processing cell (0, 0) = '8'**:
- Box index: (0/3)*3 + (0/3) = 0
- rows: {} → add '8' → {'8'}
- cols: {} → add '8' → {'8'}
- boxes: {} → add '8' → {'8'}
- Continue...

**Processing cell (3, 0) = '8'**:
- Box index: (3/3)*3 + (0/3) = 1*3 + 0 = 3
- rows: {} → add '8' → {'8'}[6]
- cols: {'8'} → **contains '8'!** → **return false** ✗

**Explanation**: Two 8's in column 0 violates column rule.

**Note**: The problem description says duplicate is in top-left 3x3 box, but actually both violations exist (column AND box).

## Complexity Analysis

### Time Complexity: O(1)
- Board is always 9x9 = 81 cells (constant)
- Each cell checked once: O(81) = O(1)
- HashSet operations: O(1) average[2]
- **Technically O(1) because input size is fixed**

**More precisely**: O(9 × 9 × 3) = O(243) = O(1) for:
- 9 rows × 9 columns × 3 checks per cell

### Space Complexity: O(1)
- 27 HashSets total: 9 rows + 9 cols + 9 boxes
- Each HashSet stores at most 9 digits
- Total: 27 × 9 = 243 entries max = O(1)[2]

**Alternative analysis**: O(27) = O(1) HashSets

## Alternative Approaches

### Approach 1: Three Separate Passes

```java
public class SolutionThreePass {
    public boolean isValidSudoku(char[][] board) {
        // Pass 1: Check all rows
        for (int row = 0; row < 9; row++) {
            Set<Character> seen = new HashSet<>();
            for (int col = 0; col < 9; col++) {
                char current = board[row][col];
                if (current != '.' && !seen.add(current)) {
                    return false;
                }
            }
        }
        
        // Pass 2: Check all columns
        for (int col = 0; col < 9; col++) {
            Set<Character> seen = new HashSet<>();
            for (int row = 0; row < 9; row++) {
                char current = board[row][col];
                if (current != '.' && !seen.add(current)) {
                    return false;
                }
            }
        }
        
        // Pass 3: Check all 3x3 boxes
        for (int box = 0; box < 9; box++) {
            Set<Character> seen = new HashSet<>();
            int startRow = (box / 3) * 3;
            int startCol = (box % 3) * 3;
            
            for (int i = 0; i < 3; i++) {
                for (int j = 0; j < 3; j++) {
                    char current = board[startRow + i][startCol + j];
                    if (current != '.' && !seen.add(current)) {
                        return false;
                    }
                }
            }
        }
        
        return true;
    }
}
```

**Complexity**: O(1) time, O(1) space
**Drawback**: More code, three passes instead of one

### Approach 2: Bit Manipulation

```java
public class SolutionBitMask {
    public boolean isValidSudoku(char[][] board) {
        int[] rows = new int[9];
        int[] cols = new int[9];
        int[] boxes = new int[9];
        
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                char current = board[row][col];
                
                if (current == '.') continue;
                
                int digit = current - '0';
                int bit = 1 << digit;  // Create bitmask for digit
                int boxIndex = (row / 3) * 3 + (col / 3);
                
                // Check if bit already set (duplicate)
                if ((rows[row] & bit) > 0 ||
                    (cols[col] & bit) > 0 ||
                    (boxes[boxIndex] & bit) > 0) {
                    return false;
                }
                
                // Set the bit
                rows[row] |= bit;
                cols[col] |= bit;
                boxes[boxIndex] |= bit;
            }
        }
        
        return true;
    }
}
```

**Complexity**: O(1) time, O(1) space
**Advantage**: More memory efficient (uses integers instead of HashSets)
**Disadvantage**: Less readable, bit manipulation is harder to understand

## Comparison of Approaches

| Approach | Time | Space | Readability | Memory Usage |
|----------|------|-------|-------------|--------------|
| **One-Pass HashSet** | **O(1)** | **O(1)** | **High** | **Moderate** |
| String Encoding | O(1) | O(1) | High | High (strings) |
| Three Separate Passes | O(1) | O(1) | Medium | Low |
| Bit Manipulation | O(1) | O(1) | Low | Very Low |

## Edge Cases

### Case 1: Empty Board
```java
board = all '.' cells
Output: true  // No duplicates
```

### Case 2: Single Filled Cell
```java
board = one '5', rest '.'
Output: true  // Valid
```

### Case 3: Duplicate in Row
```java
Row: [5, 5, ., ., ., ., ., ., .]
Output: false
```

### Case 4: Duplicate in Column
```java
Col 0: [5, ., ., 5, ., ., ., ., .]
Output: false
```

### Case 5: Duplicate in Box
```java
Top-left 3x3:
[5, ., .]
[., 5, .]
[., ., .]
Output: false
```

### Case 6: Multiple Valid Boxes
```java
Each 3x3 box has 1-9 without repetition
Output: true  // Even if not solvable
```

### Case 7: Completely Filled Valid Board
```java
All 81 cells filled correctly
Output: true
```

## Common Mistakes to Avoid

### 1. **Wrong Box Index Formula**
```java
// WRONG: Incorrect calculation
int boxIndex = row / 3 + col / 3;  // Missing multiplication

// CORRECT:
int boxIndex = (row / 3) * 3 + (col / 3);
```

### 2. **Not Skipping Empty Cells**
```java
// WRONG: Processing '.' cells
if (board[row][col] != '0') {  // Wrong comparison
    // ...
}

// CORRECT:
if (board[row][col] == '.') {
    continue;
}
```

### 3. **Character vs Integer Comparison**
```java
// WRONG: Comparing char with int
if (board[row][col] != 0) {  // '0' is not 0

// CORRECT:
if (board[row][col] == '.') {
```

### 4. **Forgetting to Initialize HashSets**
```java
// WRONG: NullPointerException
Set<Character>[] rows = new HashSet[9];
rows[0].add('5');  // NPE!

// CORRECT:
for (int i = 0; i < 9; i++) {
    rows[i] = new HashSet<>();
}
```

### 5. **Validating Solvability Instead of Validity**
```java
// WRONG: Trying to solve the Sudoku
// Problem only asks to validate current state, not solve it
```

## Interview Tips

1. **Clarify requirements**:
   - "Should I validate that the board is solvable?" (No, just valid)
   - "Do empty cells count as duplicates?" (No, skip them)

2. **Explain the box index formula**:
   - Draw the 9 boxes numbered 0-8
   - Show how `(row/3)*3 + (col/3)` maps cells to boxes
   - This is the "tricky" part that impresses interviewers[5][4]

3. **Start with approach**:
   - "I could do three separate passes, but I'll combine them for efficiency."
   - "I'll use HashSets to track seen digits in O(1) time."

4. **Walk through example**:
   - Show how duplicate '8' gets detected
   - Demonstrate box index calculation for a few cells

5. **Discuss alternatives**:
   - "Could use bit manipulation for less memory, but HashSets are more readable."
   - "Could encode constraints as strings in a single HashSet."

6. **Mention complexity**:
   - "Technically O(1) since board size is fixed at 9x9."
   - "But the pattern scales to larger Sudoku variants."

