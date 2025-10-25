---

## 🎯 PROBLEM: H-Index

> Given an array of integers `citations` where `citations[i]` is the number of citations a researcher received for their `i-th` paper, return the researcher’s **h-index**.

> The **h-index** is defined as the maximum value of `h` such that the researcher has **at least `h` papers with at least `h` citations each**.

**Examples**:  
- Input: `citations = [3,0,6,1,5]` → Output: `3`  
  → Researcher has 3 papers with at least 3 citations each ([3,6,5]).
- Input: `citations = [1,3,1]` → Output: `1`

---

## 🧠 STEP 1: Understand the Problem

- **h-index** = largest `h` where **at least `h` papers have ≥ `h` citations**.
- Example: `[3,0,6,1,5]` sorted → `[0,1,3,5,6]`
  - h=1: 4 papers ≥1 → ok
  - h=2: 3 papers ≥2 → ok
  - h=3: 3 papers ≥3 → ok
  - h=4: 2 papers ≥4 → not ok
  - → max h = 3

---

## 🧩 STEP 2: Why Sort + Linear Scan?

Because:

- **Sort citations in descending order**.
- For each index `i` (0-indexed), the number of papers with ≥ `citations[i]` citations is `i+1`.
- We want the largest `h` such that `citations[i] >= i+1`.
- Scan from left to right, track max `h = min(citations[i], i+1)`.

→ O(n log n) time, O(1) space.

✅ **Simple and optimal**.

> 💡 **Alternative**: Counting sort (O(n)) — but sorting is cleaner for interviews.

---

## ✍️ STEP 3: Code It — Clean Java (Sorting Approach)

```java
import java.util.*;

public class HIndex {
    public int hIndex(int[] citations) {
        // Sort in descending order
        Arrays.sort(citations);
        int n = citations.length;
        
        // Traverse from end (largest citations)
        for (int i = 0; i < n; i++) {
            // papers with >= citations[i] = n - i
            // h = min(citations[i], n - i)
            if (citations[i] >= n - i) {
                return n - i;
            }
        }
        
        return 0;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `citations = [3,0,6,1,5]`

### Step 1: Sort ascending → `[0,1,3,5,6]`

### Step 2: Traverse from left

| i | citations[i] | n-i (papers ≥ citations[i]) | condition (citations[i] >= n-i) |
|---|--------------|-----------------------------|----------------------------------|
| 0 | 0            | 5                           | 0 >= 5? ❌                      |
| 1 | 1            | 4                           | 1 >= 4? ❌                      |
| 2 | 3            | 3                           | 3 >= 3? ✅ → return 3           |

✅ Output: `3`

Input: `citations = [1,3,1]` → sorted: `[1,1,3]`

| i | citations[i] | n-i | condition |
|---|--------------|-----|-----------|
| 0 | 1            | 3   | 1>=3? ❌  |
| 1 | 1            | 2   | 1>=2? ❌  |
| 2 | 3            | 1   | 3>=1? ✅ → return 1 |

✅ Output: `1`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n) — sorting dominates.
- **Space**: O(1) — only pointers.

✅ Optimal for interview setting.

> 💡 **Counting Sort Alternative**: O(n) time — create buckets for citations (max n), then scan from end. Use when n is large.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Academic ranking (Google Scholar), performance evaluation, analytics dashboards.
- **Interview pattern**: **Sort + Linear Scan for Threshold** — appears in H-Index II (sorted input), Find the Duplicate Number.
- **Why companies ask this**: Tests if you can interpret a metric, sort data, and find threshold efficiently.

> 💡 Google Scholar: “Compute researcher h-index.”  
> 💡 HR Systems: “Evaluate employee performance metrics.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Sorting in ascending order but scanning from front** → wrong condition.

```java
// 🚫 WRONG — ascending sort, but check citations[i] >= i+1
Arrays.sort(citations);
for (int i = 0; i < n; i++) {
    if (citations[i] >= i+1) { ... }
}

// ✅ CORRECT — ascending sort, check citations[i] >= n-i
```

- **Not handling edge case** → all zeros → return 0.

- **Using brute force** → O(n²) — check all h from n down to 1.

---

## ✅ STEP 8: Edge Cases to Test

```java
hIndex([3,0,6,1,5]) → 3
hIndex([1,3,1]) → 1
hIndex([0]) → 0
hIndex([1]) → 1
hIndex([100]) → 1
hIndex([0,0,0]) → 0
hIndex([1,2,3,4,5]) → 3 (3 papers with ≥3 citations)
```

---

## 🧠 BONUS: Counting Sort Approach (O(n))

Since h-index ≤ n, we can:

1. Create `count` array of size `n+1`.
2. For each citation:
   - If `citation >= n`, increment `count[n]`.
   - Else, increment `count[citation]`.
3. Traverse `count` from end, accumulate papers, find largest `h` where `papers >= h`.

→ O(n) time, O(n) space — optimal for large n.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Computes h-index: max h such that at least h papers have >= h citations.
 * Sorts array, scans from end — O(n log n) time, O(1) space.
 * @param citations array of paper citations
 * @return h-index
 */
public int hIndex(int[] citations) {
    Arrays.sort(citations);
    int n = citations.length;
    
    // For each paper, check if citations[i] >= (n - i)
    for (int i = 0; i < n; i++) {
        if (citations[i] >= n - i) {
            return n - i;
        }
    }
    
    return 0; // no papers with citations
}
```

---

