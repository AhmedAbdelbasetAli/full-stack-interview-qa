

---

## 🎯 PROBLEM: Container With Most Water

> You are given an integer array `height` of length `n`.  
> There are `n` vertical lines drawn such that the two endpoints of the `i-th` line are `(i, 0)` and `(i, height[i])`.  
> Find two lines that, together with the x-axis, form a container that holds the **most water**.  
> Return the **maximum amount of water** a container can store.

**Note**: You cannot slant the container.

**Example**:  
Input: `height = [1,8,6,2,5,4,8,3,7]`  
Output: `49` → Formed by lines at index 1 (height=8) and index 8 (height=7) → width=7, height=min(8,7)=7 → area=49.

---

## 🧠 STEP 1: Understand the Problem

- Each line is at position `i`, height `height[i]`.
- Container between line `i` and line `j`:
  - **Width** = `j - i`
  - **Height** = `min(height[i], height[j])`
  - **Area** = `width * height`
- Maximize area.
- Cannot slant → must use vertical lines.

---

## 🧩 STEP 2: Why Two Pointers (Collision) + Greedy?

Because:

- Start with **widest container** (`left=0`, `right=n-1`) → max width.
- To find a larger area, we must **increase height** (since width only decreases as we move).
- The **shorter line limits the height** → moving the taller line won’t help.
- So we **move the shorter line** — hoping to find a taller one.

→ This **greedy choice** is optimal — proven mathematically.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ContainerWithMostWater {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int maxArea = 0;

        while (left < right) {
            int width = right - left;
            int h = Math.min(height[left], height[right]);
            int area = width * h;
            maxArea = Math.max(maxArea, area);

            // Move the shorter line — it's limiting the height
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxArea;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `height = [1,8,6,2,5,4,8,3,7]`

| Step | left | right | height[left] | height[right] | width | height | area | maxArea | Action             |
|------|------|-------|--------------|---------------|-------|--------|------|---------|--------------------|
| 1    | 0    | 8     | 1            | 7             | 8     | 1      | 8    | 8       | left++ (1<7)       |
| 2    | 1    | 8     | 8            | 7             | 7     | 7      | 49   | 49      | right-- (8>=7)     |
| 3    | 1    | 7     | 8            | 3             | 6     | 3      | 18   | 49      | right-- (8>=3)     |
| 4    | 1    | 6     | 8            | 8             | 5     | 8      | 40   | 49      | right-- (8>=8)     |
| 5    | 1    | 5     | 8            | 4             | 4     | 4      | 32   | 49      | right-- (8>=4)     |
| 6    | 1    | 4     | 8            | 5             | 3     | 5      | 15   | 49      | right-- (8>=5)     |
| 7    | 1    | 3     | 8            | 2             | 2     | 2      | 16   | 49      | right-- (8>=2)     |
| 8    | 1    | 2     | 8            | 6             | 1     | 6      | 6    | 49      | right-- (8>=6)     |
| 9    | 1    | 1     | —            | —             | —     | —      | —    | —       | break (left==right)|

✅ Final `maxArea = 49`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each pointer moves at most n steps.
- **Space**: O(1) — only a few variables.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Graphics (max area under histogram), UI layout (max space between elements), fluid dynamics simulations.
- **Interview pattern**: **Two Pointers + Greedy Choice** — appears in Trapping Rain Water, Largest Rectangle in Histogram.
- **Why companies ask this**: Tests if you can prove greedy choice is optimal, avoid O(n²), think geometrically.

> 💡 Uber: “Maximize ad space between two UI elements.”  
> 💡 Game Dev: “Largest water pool between terrain heights.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Moving the taller line** → violates greedy insight.

```java
// 🚫 WRONG
if (height[left] > height[right]) left++; // No! Move shorter!

// ✅ CORRECT
if (height[left] < height[right]) left++;
else right--;
```

- **Not updating maxArea before moving pointers** → might miss current area.

- **Using `<=` in loop** → `left == right` → width=0 → unnecessary.

```java
// 🚫 Unnecessary
while (left <= right)

// ✅ Correct
while (left < right)
```

- **Forgetting to calculate area** → just moving pointers.

---

## ✅ STEP 8: Edge Cases to Test

```java
maxArea([1,1]) → 1
maxArea([1,2,1]) → 2 (width=2, height=1)
maxArea([4,3,2,1,4]) → 16 (first and last)
maxArea([1,2,4,3]) → 4 (2 and 4 → width=2, height=2)
maxArea([1,8,6,2,5,4,8,3,7]) → 49
```

---

## 🧠 BONUS: Why Moving the Shorter Line is Optimal

### 🧮 Mathematical Proof Sketch

At any state `(left, right)`:

- Area = `(right - left) * min(height[left], height[right])`

Suppose `height[left] < height[right]`.

→ Current area is limited by `height[left]`.

If we move `right` leftward:

- Width decreases.
- Height ≤ `height[right]` → but still limited by `height[left]` → area can only decrease or stay same.

If we move `left` rightward:

- Width decreases.
- But new `height[left]` might be **larger** → might increase height → might increase area.

→ So only moving the shorter line has potential to find a larger area.

✅ **Greedy choice is safe.**

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds maximum area of water that can be contained between two lines.
 * Uses two pointers with greedy move — O(n) time, O(1) space.
 * @param height array of line heights
 * @return maximum area
 */
public int maxArea(int[] height) {
    int left = 0;                    // Start at leftmost
    int right = height.length - 1;   // Start at rightmost
    int maxArea = 0;

    while (left < right) {
        int width = right - left;
        int h = Math.min(height[left], height[right]);
        int area = width * h;
        maxArea = Math.max(maxArea, area);

        // Move the pointer pointing to the shorter line
        // Because moving the taller line won't increase height
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }

    return maxArea;
}
```

---


