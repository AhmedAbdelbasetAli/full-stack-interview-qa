## Solution Summary

I've provided **three production-ready approaches** to transpose a text file in Bash:

### **Best Solution: AWK (Recommended)**
```bash
awk '
{
    for (i = 1; i <= NF; i++) {
        matrix[NR][i] = $i
    }
    max_cols = NF
}
END {
    for (j = 1; j <= max_cols; j++) {
        for (i = 1; i <= NR; i++) {
            printf "%s", matrix[i][j]
            if (i < NR) printf " "
        }
        print ""
    }
}
' file.txt
```

**Why AWK?**
- **Maximum compatibility**: Works on any Unix/Linux system
- **Performance**: Handles large files efficiently
- **Simplicity**: Elegant two-pass solution (read → transpose → print)

### **Alternative: Python (Most Readable)**
```bash
python3 << 'EOF'
with open('file.txt', 'r') as f:
    lines = [line.strip().split() for line in f]

transposed = list(zip(*lines))

for row in transposed:
    print(' '.join(row))
EOF
```

**Why Python?**
- **Clarity**: The `zip(*lines)` operation is immediately understandable
- **Conciseness**: Only 5 lines of logic

### **Alternative: Pure Bash (No Dependencies)**
Uses bash associative arrays to build a 2D matrix in memory, then iterate by column instead of row.

**Why Pure Bash?**
- No external command dependencies
- Useful in restricted environments
- Better for automation scripts

***

### **How It Works**
1. **Read phase**: Parse each line and store values in a 2D structure indexed by `[row][column]`
2. **Transpose phase**: Iterate columns first (outer loop), then rows (inner loop)
3. **Output phase**: Print space-separated values with newlines between rows

### **Example Execution**
```
Input (file.txt):
name age
alice 21
ryan 30

Output:
name alice ryan
age 21 30
```

