Use the **Fisher–Yates shuffle** to guarantee all permutations are equally likely, and keep a copy of the original array for `reset`.[1][2][3]

## Key Ideas

- Store the **original array** so `reset()` can return it unchanged.[4][1]
- Maintain a **working array** that `shuffle()` will modify in-place.[5][1]
- Fisher–Yates (modern version): for `i` from `n-1` down to `0`, pick random `j ∈ [0, i]` and swap `a[i]` with `a[j]`.[2][3]
  This gives each of the `n!` permutations probability `1/n!`.[6][3]

## Java Implementation

```java
import java.util.Random;

class Solution {
    private final int[] original;  // immutable reference to original configuration
    private int[] nums;            // working array we shuffle
    private final Random rand;

    public Solution(int[] nums) {
        // Store a copy as the original configuration
        this.original = nums.clone();
        // Start with another copy as the working array
        this.nums = nums.clone();
        this.rand = new Random();
    }

    public int[] reset() {
        // Restore nums to the original configuration
        nums = original.clone();
        return nums;
    }

    public int[] shuffle() {
        // Fisher–Yates shuffle on the working array
        for (int i = nums.length - 1; i > 0; i--) {
            // pick a random index from [0, i]
            int j = rand.nextInt(i + 1);
            // swap nums[i] and nums[j]
            int tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        return nums;
    }
}
```

### Why This Is Correct

- `reset()` returns a fresh clone of the original, so further shuffles don’t affect the stored initial state.[1][4]
- Fisher–Yates ensures each element is equally likely to end up in each position and no permutation is favored.[3][2][1]
- Time complexity: `O(n)` per `shuffle` and `reset`; space: `O(n)` for storing the original and working copies.[4][5]

