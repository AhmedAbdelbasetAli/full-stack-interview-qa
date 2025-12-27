Scan the array **from right to left**, tracking the running maximum. At each step, store the current maximum (from the right) as the result, then update the running maximum with the current element.[1][2]

```java
class Solution {
    public int[] replaceElements(int[] arr) {
        int n = arr.length;
        int[] result = new int[n];
        
        // Start from the right; last element is always -1
        result[n - 1] = -1;
        int maxFromRight = -1;
        
        // Traverse from right to left
        for (int i = n - 2; i >= 0; i--) {
            // Store the max seen so far on the right
            maxFromRight = Math.max(maxFromRight, arr[i + 1]);
            result[i] = maxFromRight;
        }
        
        return result;
    }
}
```

### How it works

1. Initialize `result[n-1] = -1` (last element has no right neighbors).
2. Start `maxFromRight = -1` and loop **backwards** from `n-2` to `0`:
   - For each position `i`, the greatest element to its right is the max from `arr[i+1..n-1]`.
   - Store that max in `result[i]`.
   - Then **update** `maxFromRight` to include `arr[i+1]`.

### Example: `[17,18,5,4,6,1]`

- i=5: result=-1, maxFromRight=-1
- i=4: maxFromRight=max(-1,1)=1, result=1
- i=3: maxFromRight=max(1,6)=6, result=6
- i=2: maxFromRight=max(6,4)=6, result=6
- i=1: maxFromRight=max(6,5)=6, result=6[3]
- i=0: maxFromRight=max(6,18)=18, result=18

**Result**: `[18,6,6,6,1,-1]` ✓

**Complexity**:
- Time: O(n), single pass right to left
- Space: O(1) excluding the output array

