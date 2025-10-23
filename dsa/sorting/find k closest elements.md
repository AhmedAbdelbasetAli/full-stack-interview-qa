

---

## 🎯 PROBLEM: Find K Closest Elements

> Given a **sorted** integer array `arr`, two integers `k` and `x`, return the `k` closest integers to `x` in the array. The result should also be sorted in ascending order.

> An integer `a` is closer to `x` than an integer `b` if:
> - `|a - x| < |b - x|`, or
> - `|a - x| == |b - x|` and `a < b`

**Examples**:  
- Input: `arr = [1,2,3,4,5]`, `k = 4`, `x = 3` → Output: `[1,2,3,4]`  
- Input: `arr = [1,2,3,4,5]`, `k = 4`, `x = -1` → Output: `[1,2,3,4]`

---

## 🧠 STEP 1: Understand the Problem

- Array is **sorted** → we can use binary search.
- Need **k consecutive elements** (because closest elements to `x` in a sorted array are always consecutive).
- **Tie-breaker**: If distances equal, prefer smaller number.

---

## 🧩 STEP 2: Why Binary Search for Left Bound?

Because:

- The result is a **subarray of length k**.
- We need to find the **left bound** `L` such that `arr[L:L+k-1]` is the closest to `x`.
- Use **binary search on left bound** (from `0` to `n-k`).
- Compare `arr[mid]` and `arr[mid+k]` to decide direction.

→ O(log(n-k) + k) time, O(1) space.

✅ **Optimal and elegant**.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class FindKClosest {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int left = 0;
        int right = arr.length - k; // left bound can be at most n-k

        // Binary search for left bound
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            // Compare arr[mid] and arr[mid + k]
            // If x - arr[mid] > arr[mid + k] - x, then mid is too left
            if (x - arr[mid] > arr[mid + k] - x) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        // Extract k elements from left
        List<Integer> result = new ArrayList<>();
        for (int i = left; i < left + k; i++) {
            result.add(arr[i]);
        }

        return result;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `arr = [1,2,3,4,5]`, `k = 4`, `x = 3`

### Step 1: Binary Search Bounds

- `left = 0`, `right = 5-4 = 1`

### Step 2: Iterations

- `mid = 0` → compare `x - arr[0] = 3-1=2` vs `arr[0+4] - x = 5-3=2`  
  → `2 > 2`? No → `right = 0`
- `left == right = 0` → break

### Step 3: Extract

- `arr[0:3]` = `[1,2,3,4]` ✅

Input: `arr = [1,2,3,4,5]`, `k = 4`, `x = -1`

- `mid = 0` → `x - arr[0] = -1-1 = -2` → use absolute? No — but logic still works:
  - `-1 - 1 = -2` → but we compare `x - arr[mid]` and `arr[mid+k] - x`
  - Actually: `x - arr[0] = -2`, `arr[4] - x = 5 - (-1) = 6`
  - `-2 > 6`? No → `right = 0`
- Extract `[1,2,3,4]` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(log(n-k) + k) — binary search + extraction.
- **Space**: O(1) — excluding output.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Search suggestions (“closest matches”), nearest neighbor search, financial analytics (closest stock prices).
- **Interview pattern**: **Binary Search for Bounds + Two Pointers** — appears in Find First/Last Position, Search Insert Position.
- **Why companies ask this**: Tests if you can combine binary search with custom comparison, handle edge cases.

> 💡 Google Search: “Show k closest results to query.”  
> 💡 Finance: “Find k closest stock prices to target.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using two pointers from x outward** → O(n) — not optimal.

- **Not handling tie-breaker correctly** → but our binary search condition `x - arr[mid] > arr[mid+k] - x` handles it:
  - If distances equal, `x - arr[mid] == arr[mid+k] - x` → we move `right = mid` → prefer left (smaller numbers).

- **Binary search bounds wrong** → `right = n - k`, not `n - 1`.

---

## ✅ STEP 8: Edge Cases to Test

```java
findClosestElements([1,2,3,4,5], 4, 3) → [1,2,3,4]
findClosestElements([1,2,3,4,5], 4, -1) → [1,2,3,4]
findClosestElements([1,1,1,10,10,10], 1, 9) → [10]
findClosestElements([0,0,1,2,3,3,4,7,7,8], 3, 5) → [3,3,4]
findClosestElements([1,2,3,4,5], 1, 3) → [3]
```

---

## 🧠 BONUS: Why Compare `arr[mid]` and `arr[mid+k]`?

Because:

- The window `[mid, mid+k-1]` and `[mid+1, mid+k]` are the only candidates.
- If `x` is closer to `arr[mid+k]` than `arr[mid]`, then the left bound must be **right of mid**.
- The condition `x - arr[mid] > arr[mid+k] - x` means `arr[mid+k]` is closer → move left to `mid+1`.

✅ This is the **key insight** that makes binary search work.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds k closest elements to x in sorted array.
 * Uses binary search for left bound — O(log(n-k) + k) time, O(1) space.
 * @param arr sorted array
 * @param k number of closest elements
 * @param x target value
 * @return list of k closest elements (sorted)
 */
public List<Integer> findClosestElements(int[] arr, int k, int x) {
    // Left bound can be from 0 to n-k
    int left = 0;
    int right = arr.length - k;

    // Binary search for optimal left bound
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        // If x is closer to arr[mid+k] than arr[mid], move left
        if (x - arr[mid] > arr[mid + k] - x) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }

    // Extract k elements from left bound
    List<Integer> result = new ArrayList<>();
    for (int i = left; i < left + k; i++) {
        result.add(arr[i]);
    }

    return result;
}
```

---
