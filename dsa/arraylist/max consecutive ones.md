Track the current streak of 1s and the maximum streak seen so far with a single pass and O(1) extra space. This is a straightforward counting pattern.[1][2][3]

```java
class Solution {
    public int findMaxConsecutiveOnes(int[] nums) {
        int max = 0;
        int count = 0;

        for (int num : nums) {
            if (num == 1) {
                count++;                 // extend current streak
                if (count > max) {
                    max = count;         // update max streak
                }
            } else {
                count = 0;               // reset streak on 0
            }
        }

        return max;
    }
}
```

- For `[1,1,0,1,1,1]`: `count` runs as 1,2,0,1,2,3 → `max = 3`.  
- For `[1,0,1,1,0,1]`: `count` runs as 1,0,1,2,0,1 → `max = 2`.  

Time complexity: O(n), single pass. Space complexity: O(1).[2][1]

