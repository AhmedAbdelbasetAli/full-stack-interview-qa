## Problem Understanding

This is the **"First Bad Version"** problem, a classic **binary search on a monotonic property**. The key insights:[1][2][3]

- Versions [1, 2, ..., n] form a **sorted sequence** where good versions (false) transition to bad versions (true) at some point
- Once a bad version appears, **all subsequent versions are bad** (monotonic property)
- Goal: Find the **first bad version** (leftmost transition point) with **minimum API calls**
- Linear scan would take O(n) calls; we need **O(log n)** calls

## Optimal Solution: Binary Search for First True

Use binary search to find the **first position where `isBadVersion(mid)` returns true**.[2][3]

### Binary Search Strategy

1. **Initialize**: `left = 1`, `right = n`
2. **While** `left < right`:
   - Compute `mid = left + (right - left) / 2` (avoid overflow)
   - If `isBadVersion(mid) == true`: first bad version is `mid` or **left** → `right = mid`
   - If `false`: first bad version is **right** → `left = mid + 1`
3. **Return** `left` (guaranteed to be first bad version)

## Java Solution

```java
/* The isBadVersion API is defined in the parent class VersionControl.
   boolean isBadVersion(int version); */

public class Solution extends VersionControl {
    /**
     * Finds the first bad version using binary search
     * Minimizes API calls to O(log n)
     * 
     * @param n - total number of versions [1, 2, ..., n]
     * @return the first bad version number
     */
    public int firstBadVersion(int n) {
        int left = 1;
        int right = n;
        
        while (left < right) {
            // Avoid potential integer overflow
            int mid = left + (right - left) / 2;
            
            // If mid is bad, first bad version is mid or left
            if (isBadVersion(mid)) {
                right = mid;
            } else {
                // If mid is good, first bad version is right
                left = mid + 1;
            }
        }
        
        return left;
    }
}
```

## Detailed Walkthrough

### Example 1: `n = 5`, first bad = 4

```
Versions: [1,2,3,4,5]
Status:   [G,G,G,B,B]  (G=Good, B=Bad)

Initial: left=1, right=5

Iteration 1:
mid = 1 + (5-1)/2 = 3
isBadVersion(3) = false → left = 4, right = 5  [1 API call]

Iteration 2:  
mid = 4 + (5-4)/2 = 4
isBadVersion(4) = true → right = 4, left = 4   [2 API calls]

left == right, return 4 ✓
Total API calls: 2 (instead of 4 for linear scan)
```

### Example 2: `n = 1`, first bad = 1

```
left=1, right=1 → left == right, return 1 ✓
Total API calls: 0
```

### Visual Binary Search Tree

```
         n=5
        /   \
    mid=3(G) mid=4(B)
     |         |
  left=4    right=4 ✓
```

## Why This Works

**Monotonic Property Proof**:[3][2]
- If `isBadVersion(mid) == true`: first bad version ∈ [left, mid]
- If `isBadVersion(mid) == false`: first bad version ∈ [mid+1, right]

**Invariant**: At all times, `[left, right]` contains the first bad version.

**Termination**: `left < right` ensures we converge to the exact boundary.

## Complexity Analysis

**Time Complexity: O(log n)**[2][3]
- Binary search halves search space each iteration
- Maximum iterations: ⌈log₂(n)⌉ API calls
- For n=2³¹-1: ~31 calls maximum

**Space Complexity: O(1)**
- Only uses three integer variables

## Edge Cases

| Case | n | First Bad | API Calls | Result |
|------|---|-----------|-----------|--------|
| Single version | 1 | 1 | 0 | 1 |
| First version bad | 5 | 1 | 2 | 1 |
| Last version bad | 5 | 5 | 2 | 5 |
| All good* | 5 | 6* | 3 | 6* |

*Note: Problem guarantees 1 ≤ bad ≤ n, so "all good" won't occur.

## Common Mistakes to Avoid

### 1. **Integer Overflow in Mid Calculation**
```java
// WRONG: Can overflow for large n
int mid = (left + right) / 2;

// CORRECT: Safe calculation
int mid = left + (right - left) / 2;
```

### 2. **Wrong Loop Condition**
```java
// WRONG: Misses boundary case
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (isBadVersion(mid)) {
        right = mid - 1;  // Wrong direction!
    }
}

// CORRECT: left < right, search for first true
while (left < right) {
    int mid = left + (right - left) / 2;
    if (isBadVersion(mid)) {
        right = mid;  // Can be first bad
    } else {
        left = mid + 1;
    }
}
```

### 3. **Returning Wrong Pointer**
```java
// WRONG: Returns right instead of left
return right;  // May not be first bad version

// CORRECT: left == right == first bad version
return left;
```

### 4. **Standard Binary Search Confusion**
```
Standard BS (find exact target): if arr[mid] == target → return mid
First Bad Version: if isBadVersion(mid) → right = mid (could be first)
```

## Linear Scan (For Comparison)

```java
// O(n) - DON'T USE
public int firstBadVersionLinear(int n) {
    for (int i = 1; i <= n; i++) {
        if (isBadVersion(i)) {
            return i;
        }
    }
    return n + 1;  // Won't happen per constraints
}
```

**Why Binary Search Wins**:
```
n = 1,000,000,000
Linear: 1B API calls
Binary: ~30 API calls ✓
```

## Interview Tips

1. **Recognize the pattern**:
   - "This is binary search for the first true value in a monotonic boolean array."

2. **Explain the invariant**:
   - "I maintain [left, right] always contains the first bad version."
   - "When mid is bad, search left half including mid."
   - "When mid is good, search right half excluding mid."

3. **Walk through example**:
   ```
   n=5, versions: G G G B B
   mid=3(G) → left=4
   mid=4(B) → right=4 ✓
   ```

4. **Mention edge cases**:
   - "Works for n=1 (0 calls)."
   - "Handles first/last version being bad."

5. **Complexity**:
   - "O(log n) time, O(1) space, minimizes API calls as required."

6. **Contrast with linear**:
   - "Linear scan = O(n) calls, binary search = O(log n) calls."

## Alternative Implementations

### Iterative with While (left <= right)
```java
public int firstBadVersionAlt(int n) {
    int left = 1, right = n;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            if (mid == 1 || !isBadVersion(mid - 1)) {
                return mid;
            }
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return left;
}
```

**Same complexity**, slightly more complex logic.

## Real-World Applications

- **Software regression**: Find commit introducing a bug
- **A/B testing**: Find first experiment causing metric drop
- **Deployment rollback**: Find first bad deployment version
- **Binary search debugging**: "When did this start failing?"

This problem teaches **binary search on decision boundaries** - a fundamental pattern for many "first of something" problems.[1][3][2]
