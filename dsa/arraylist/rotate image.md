
---

## 🎯 PROBLEM: Rotate Image

> You are given an `n x n` 2D matrix representing an image.  
> Rotate the image by **90 degrees clockwise**.  
> **You must rotate the image in-place** — do not allocate another 2D matrix.

**Example**:  
Input:
```
[[1,2,3],
 [4,5,6],
 [7,8,9]]
```
Output:
```
[[7,4,1],
 [8,5,2],
 [9,6,3]]
```

---

## 🧠 STEP 1: Understand the Problem

- Matrix is **square** (`n x n`).
- Rotate **clockwise**.
- **In-place** — no extra matrix.
- Visualize: Top row becomes right column, right column becomes bottom row, etc.

---

## 🧩 STEP 2: Brainstorm Approaches

### ❌ Approach 1: Use Extra Matrix

→ Create new matrix, map `matrix[i][j]` to `newMatrix[j][n-1-i]`.  
→ Works, but violates “in-place” constraint.

---

### ✅ Approach 2: Transpose + Reverse Each Row — O(n²)

> “Rotate = Transpose + Reverse Rows”

- **Transpose**: Swap `matrix[i][j]` with `matrix[j][i]` → rows become columns.
- **Reverse each row**: To get clockwise rotation.

Example:
```
Original:      Transpose:     Reverse Rows:
1 2 3          1 4 7          7 4 1
4 5 6    →     2 5 8    →     8 5 2
7 8 9          3 6 9          9 6 3
```

→ Elegant, in-place, two passes.

---

### ✅ Approach 3: Rotate Layer by Layer — O(n²)

> “Rotate outer layer, then inner layer, like an onion.”

- For each layer, rotate 4 elements at a time.
- Use a temporary variable to swap in a cycle.

→ Also in-place, one pass per layer.

We’ll show both — but **Approach 2 (Transpose + Reverse)** is simpler and preferred in interviews.

---

## ✍️ STEP 3: Code It — Two Clean Java Solutions

### ✅ Solution 1: Transpose + Reverse Rows (Recommended)

```java
public class RotateImage {
    public void rotate(int[][] matrix) {
        int n = matrix.length;

        // Step 1: Transpose the matrix
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                swap(matrix, i, j, j, i);
            }
        }

        // Step 2: Reverse each row
        for (int i = 0; i < n; i++) {
            reverseRow(matrix, i);
        }
    }

    private void swap(int[][] matrix, int i1, int j1, int i2, int j2) {
        int temp = matrix[i1][j1];
        matrix[i1][j1] = matrix[i2][j2];
        matrix[i2][j2] = temp;
    }

    private void reverseRow(int[][] matrix, int row) {
        int left = 0, right = matrix.length - 1;
        while (left < right) {
            swap(matrix, row, left, row, right);
            left++;
            right--;
        }
    }
}
```

---

### ✅ Solution 2: Layer by Layer (For Understanding)

```java
public void rotate(int[][] matrix) {
    int n = matrix.length;
    int layers = n / 2;

    for (int layer = 0; layer < layers; layer++) {
        int first = layer;
        int last = n - 1 - layer;

        for (int i = first; i < last; i++) {
            int offset = i - first;

            // Save top
            int top = matrix[first][i];

            // left → top
            matrix[first][i] = matrix[last - offset][first];

            // bottom → left
            matrix[last - offset][first] = matrix[last][last - offset];

            // right → bottom
            matrix[last][last - offset] = matrix[i][last];

            // top → right
            matrix[i][last] = top;
        }
    }
}
```

→ More complex, but shows deep understanding.

---

## 🧪 STEP 4: Trace Through Example — Transpose + Reverse

Input:
```
1 2 3
4 5 6
7 8 9
```

### Step 1: Transpose (swap across diagonal)

```
1 4 7
2 5 8
3 6 9
```

### Step 2: Reverse each row

Row 0: `[1,4,7]` → `[7,4,1]`  
Row 1: `[2,5,8]` → `[8,5,2]`  
Row 2: `[3,6,9]` → `[9,6,3]`

✅ Output:
```
7 4 1
8 5 2
9 6 3
```

Perfect.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n²) — each element visited once (transpose) + once (reverse).
- **Space**: O(1) — in-place.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Image processing (rotate photos), game development (rotate sprites), robotics (sensor data rotation).
- **Interview pattern**: **In-place matrix manipulation** — appears in Spiral Matrix, Set Matrix Zeroes.
- **Why companies ask this**: Tests if you can visualize 2D transformations, handle indices, modify in-place.

> 💡 Uber: “Rotate map view 90° for landscape mode.”  
> 💡 Android: “Rotate bitmap in ImageView.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Transposing incorrectly** → swap `matrix[i][j]` with `matrix[j][i]` — not `matrix[i][j]` with `matrix[n-1-j][i]`.

- **Reversing columns instead of rows** → after transpose, you must reverse **rows** for clockwise.

- **Using extra matrix** → violates in-place constraint.

- **Off-by-one in layer loop** → use `layer < n/2`.

---

## ✅ STEP 8: Edge Cases to Test

```java
rotate([[1]]) → [[1]]
rotate([[1,2],[3,4]]) → [[3,1],[4,2]]
rotate([[1,2,3,4],[5,6,7,8],[9,10,11,12],[13,14,15,16]]) → 
[[13,9,5,1],[14,10,6,2],[15,11,7,3],[16,12,8,4]]
```

---

## 🧠 BONUS: Why Transpose + Reverse Works

### 🔄 Clockwise Rotation = Transpose + Reverse Rows

- **Transpose**: Converts rows to columns → but in wrong order.
- **Reverse Rows**: Fixes the order to get clockwise.

### 🔄 Counter-Clockwise? → Transpose + Reverse Columns

```java
// For counter-clockwise
transpose();
reverseColumns(); // reverse each column instead of row
```

→ Same logic — just different second step.

---

## ✅ FINAL CODE — Transpose + Reverse (Recommended)

```java
/**
 * Rotates a square matrix 90 degrees clockwise in-place.
 * Uses transpose + reverse rows — O(n²) time, O(1) space.
 * @param matrix n x n 2D array
 */
public void rotate(int[][] matrix) {
    int n = matrix.length;

    // Transpose: swap matrix[i][j] with matrix[j][i]
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }

    // Reverse each row
    for (int i = 0; i < n; i++) {
        int left = 0, right = n - 1;
        while (left < right) {
            int temp = matrix[i][left];
            matrix[i][left] = matrix[i][right];
            matrix[i][right] = temp;
            left++;
            right--;
        }
    }
}
```

---

