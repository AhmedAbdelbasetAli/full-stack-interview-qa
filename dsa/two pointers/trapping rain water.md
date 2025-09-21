.

---

## 🎯 PROBLEM: Trapping Rain Water

> Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

**Example**:  
Input: `height = [0,1,0,2,1,0,1,3,2,1,2,1]`  
Output: `6`  
→ Water trapped = 1+1+2+1+1 = 6 units.

```
       #
   #   # #   #
 # # # # # # # #
0 1 0 2 1 0 1 3 2 1 2 1
→ Water: 1 1 2 1 1 → total=6
```

---

## 🧠 STEP 1: Understand the Problem

- Each bar has height `height[i]`, width=1.
- Water is trapped between bars — if there are higher bars on both sides.
- Water level at position `i` = `min(leftMax, rightMax) - height[i]`.
- Sum water for all positions.

---

## 🧩 STEP 2: Why Two Pointers?

Because:

- Water at `i` depends on `max height to left` and `max height to right`.
- Instead of precomputing leftMax and rightMax arrays (O(n) space), we can use two pointers and track `leftMax` and `rightMax` on the fly.
- Move pointer with smaller max — because water is limited by smaller of leftMax and rightMax.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class TrappingRainWater {
    public int trap(int[] height) {
        if (height == null || height.length < 3) {
            return 0;
        }

        int left = 0;
        int right = height.length - 1;
        int leftMax = 0;
        int rightMax = 0;
        int water = 0;

        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    water += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    water += rightMax - height[right];
                }
                right--;
            }
        }

        return water;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `height = [0,1,0,2,1,0,1,3,2,1,2,1]`

### Step 0: Initialize

```
left=0, right=11, leftMax=0, rightMax=0, water=0
```

### Step 1: height[0]=0 < height[11]=1 → left side

- 0 >= leftMax(0) → leftMax=0
- left++ → left=1

### Step 2: height[1]=1 < height[11]=1 → left side

- 1 >= leftMax(0) → leftMax=1
- left++ → left=2

### Step 3: height[2]=0 < height[11]=1 → left side

- 0 < leftMax(1) → water += 1-0=1 → water=1
- left++ → left=3

### Step 4: height[3]=2 > height[11]=1 → right side

- 1 >= rightMax(0) → rightMax=1
- right-- → right=10

### Step 5: height[3]=2 > height[10]=2 → right side (equal → pick right)

- 2 >= rightMax(1) → rightMax=2
- right-- → right=9

### Step 6: height[3]=2 > height[9]=1 → right side

- 1 < rightMax(2) → water += 2-1=1 → water=2
- right-- → right=8

### Step 7: height[3]=2 < height[8]=2 → left side (equal → pick left)

- 2 >= leftMax(1) → leftMax=2
- left++ → left=4

### Step 8: height[4]=1 < height[8]=2 → left side

- 1 < leftMax(2) → water += 2-1=1 → water=3
- left++ → left=5

### Step 9: height[5]=0 < height[8]=2 → left side

- 0 < leftMax(2) → water += 2-0=2 → water=5
- left++ → left=6

### Step 10: height[6]=1 < height[8]=2 → left side

- 1 < leftMax(2) → water += 2-1=1 → water=6
- left++ → left=7

### Step 11: height[7]=3 > height[8]=2 → right side

- 2 >= rightMax(2) → rightMax=2 (no change)
- right-- → right=7 → break (left >= right)

✅ Return `6`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each pointer moves at most n steps.
- **Space**: O(1) — only a few variables.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Terrain modeling, urban planning (flood zones), game development (water simulation).
- **Interview pattern**: **Two Pointers with Greedy Height Tracking** — appears in Container With Most Water, Largest Rectangle in Histogram.
- **Why companies ask this**: Tests if you can visualize 2D problems, optimize space, and think greedily.

> 💡 Uber: “Simulate rainwater trapping in city elevation map.”  
> 💡 Game Dev: “Calculate water volume in terrain for rendering.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not handling edge cases** → n < 3 → return 0.

- **Updating max before adding water** → must check if current height >= max first.

```java
// 🚫 WRONG
water += leftMax - height[left];
leftMax = Math.max(leftMax, height[left]);

// ✅ CORRECT
if (height[left] >= leftMax) {
    leftMax = height[left];
} else {
    water += leftMax - height[left];
}
```

- **Moving wrong pointer** → move pointer with smaller height.

- **Forgetting to move pointer** → infinite loop.

---

## ✅ STEP 8: Edge Cases to Test

```java
trap([0,1,0,2,1,0,1,3,2,1,2,1]) → 6
trap([3,0,2,0,4]) → 7
trap([1,2,3,4,5]) → 0 (no trapping)
trap([5,4,3,2,1]) → 0
trap([2,0,2]) → 2
trap([0]) → 0
trap([]) → 0
```

---

## 🧠 BONUS: Why Two Pointers Works

- Water at any point is limited by the **smaller of leftMax and rightMax**.
- By moving the pointer with the smaller height, we ensure that the **other side’s max is the limiting factor** — so we can safely calculate water.

→ This avoids precomputing arrays — O(1) space.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Computes total water trapped between elevation bars.
 * Uses two pointers with greedy height tracking — O(n) time, O(1) space.
 * @param height array of bar heights
 * @return total units of water trapped
 */
public int trap(int[] height) {
    if (height == null || height.length < 3) {
        return 0;
    }

    int left = 0;
    int right = height.length - 1;
    int leftMax = 0;
    int rightMax = 0;
    int water = 0;

    while (left < right) {
        if (height[left] < height[right]) {
            // Left side is smaller — water limited by leftMax
            if (height[left] >= leftMax) {
                leftMax = height[left]; // Update max
            } else {
                water += leftMax - height[left]; // Add trapped water
            }
            left++;
        } else {
            // Right side is smaller — water limited by rightMax
            if (height[right] >= rightMax) {
                rightMax = height[right]; // Update max
            } else {
                water += rightMax - height[right]; // Add trapped water
            }
            right--;
        }
    }

    return water;
}
```

---

