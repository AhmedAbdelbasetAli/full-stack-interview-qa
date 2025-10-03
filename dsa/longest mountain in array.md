

---

## 🎯 PROBLEM: Longest Mountain in Array

> You may recall that an array `arr` is a **mountain array** if and only if:
> - `arr.length >= 3`
> - There exists some `i` with `0 < i < arr.length - 1` such that:
>   - `arr[0] < arr[1] < ... < arr[i - 1] < arr[i]`
>   - `arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`
>
> Given an integer array `arr`, return the **length of the longest mountain**. Return `0` if there is no mountain.

**Example**:  
Input: `arr = [2,1,4,7,3,2,5]`  
Output: `5` → because `[1,4,7,3,2]` is a mountain of length 5.

---

## 🧠 STEP 1: Understand the Problem

- A **mountain** has:
  - Strictly increasing sequence → **peak** → strictly decreasing sequence.
  - Length ≥ 3.
- Find the **longest such subarray**.
- Return `0` if no mountain exists.

---

## 🧩 STEP 2: Why Two Pointers with Expansion?

Because:

- For each potential **peak** (not at edges), expand left and right to measure mountain length.
- A peak is where `arr[i-1] < arr[i] > arr[i+1]`.
- Expand left while strictly increasing, right while strictly decreasing.
- Track max length.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class LongestMountain {
    public int longestMountain(int[] arr) {
        if (arr.length < 3) {
            return 0;
        }

        int maxLength = 0;

        // Check each potential peak (not at edges)
        for (int i = 1; i < arr.length - 1; i++) {
            // Check if i is a peak
            if (arr[i - 1] < arr[i] && arr[i] > arr[i + 1]) {
                int left = i - 1;
                int right = i + 1;

                // Expand left while strictly increasing
                while (left > 0 && arr[left - 1] < arr[left]) {
                    left--;
                }

                // Expand right while strictly decreasing
                while (right < arr.length - 1 && arr[right] > arr[right + 1]) {
                    right++;
                }

                maxLength = Math.max(maxLength, right - left + 1);
            }
        }

        return maxLength;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `arr = [2,1,4,7,3,2,5]`

### Step 1: i=1 → arr[0]=2, arr[1]=1, arr[2]=4 → 2>1<4 → not peak

### Step 2: i=2 → 1<4<7 → not peak

### Step 3: i=3 → 4<7>3 → **peak!**

- left=2, right=4
- Expand left: left=1 (1<4) → left=0 (2>1 → stop) → left=1
- Expand right: right=5 (3>2) → right=6 (2<5 → stop) → right=5
- Length = 5-1+1 = 5 → maxLength=5

### Step 4: i=4 → 7>3>2 → not peak

### Step 5: i=5 → 3>2<5 → not peak

✅ Output: `5`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each element visited at most twice (once as peak, once in expansion).
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Terrain analysis (GIS), signal processing (peak detection), stock market analysis (trend detection).
- **Interview pattern**: **Peak Detection + Expansion** — appears in Valid Mountain Array, Find Peak Element.
- **Why companies ask this**: Tests if you can detect patterns, expand from center, handle edge cases.

> 💡 GIS: “Find longest mountain range in elevation data.”  
> 💡 Finance: “Detect longest bullish-bearish trend in stock prices.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not checking peak condition** → `arr[i-1] < arr[i] > arr[i+1]`.

- **Allowing equal values** → must be **strictly** increasing/decreasing.

```java
// 🚫 WRONG — allows equals
if (arr[i-1] <= arr[i] && arr[i] >= arr[i+1])

// ✅ CORRECT — strict
if (arr[i-1] < arr[i] && arr[i] > arr[i+1])
```

- **Not handling array length < 3** → return 0.

- **Expanding beyond array bounds** → always check `left > 0` and `right < n-1`.

---

## ✅ STEP 8: Edge Cases to Test

```java
longestMountain([2,1,4,7,3,2,5]) → 5
longestMountain([2,2,2]) → 0
longestMountain([1,2,3]) → 0 (no decreasing)
longestMountain([3,2,1]) → 0 (no increasing)
longestMountain([0,1,2,3,4,5,4,3,2,1,0]) → 11
longestMountain([1,2,0,2,1]) → 3 ([2,0,2] is not mountain — wait, no: [1,2,0] is mountain? No — 1<2>0 → yes, length=3)
```

---

## 🧠 BONUS: Why This Works

- By checking only **true peaks** (strictly greater than neighbors), we avoid false positives.
- Expanding from the peak ensures we capture the **full mountain**.
- The **strictly increasing/decreasing** condition ensures valid mountain shape.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds length of longest mountain in array.
 * A mountain: strictly increasing → peak → strictly decreasing, length >= 3.
 * O(n) time, O(1) space.
 * @param arr array of integers
 * @return length of longest mountain, or 0 if none
 */
public int longestMountain(int[] arr) {
    if (arr.length < 3) {
        return 0;
    }

    int maxLength = 0;

    // Check each potential peak (indices 1 to n-2)
    for (int i = 1; i < arr.length - 1; i++) {
        // Identify peak: strictly greater than neighbors
        if (arr[i - 1] < arr[i] && arr[i] > arr[i + 1]) {
            int left = i - 1;
            int right = i + 1;

            // Expand left while strictly increasing
            while (left > 0 && arr[left - 1] < arr[left]) {
                left--;
            }

            // Expand right while strictly decreasing
            while (right < arr.length - 1 && arr[right] > arr[right + 1]) {
                right++;
            }

            maxLength = Math.max(maxLength, right - left + 1);
        }
    }

    return maxLength;
}
```

---
