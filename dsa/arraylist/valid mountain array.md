Check that the array **strictly increases** to a peak, then **strictly decreases** from the peak. Use two pointers from both ends: one climbs the increasing side, one climbs the decreasing side. If both meet in the middle (at the same peak index, not at the boundaries), it's a valid mountain.[1][2]

```java
class Solution {
    public boolean validMountainArray(int[] arr) {
        int n = arr.length;
        
        // Mountain must have at least 3 elements
        if (n < 3) {
            return false;
        }
        
        int left = 0;
        int right = n - 1;
        
        // Walk up from the left (must be strictly increasing)
        while (left + 1 < n && arr[left] < arr[left + 1]) {
            left++;
        }
        
        // Walk down from the right (must be strictly decreasing)
        while (right - 1 >= 0 && arr[right] < arr[right - 1]) {
            right--;
        }
        
        // Peak must not be at boundaries and both pointers must meet there
        return left > 0 && right < n - 1 && left == right;
    }
}
```

### How it works

1. **Walk left pointer uphill**: Move right while `arr[left] < arr[left+1]` (strict increase).
2. **Walk right pointer downhill**: Move left while `arr[right] < arr[right-1]` (strict decrease).
3. **Validate the peak**: 
   - `left > 0` ensures we climbed at least one step (not at start).
   - `right < n - 1` ensures we descended at least one step (not at end).
   - `left == right` ensures both pointers meet at the same peak index.

### Examples

- `[0,3,2,1]`:
  - left walks: 0→1→2 (0<3, stop at 3 because 3>2)
  - right walks: 3→2→1 (1<2, stop at 2 because 2<3)
  - left=2, right=2, both > 0 and < n-1 → true ✓

- `[3,5,5]`:
  - left walks: 0→1 (3<5, stop because 5≮5)
  - right walks: 2→1 (5≮5, stop immediately)
  - left=1, right=1, but left must be > 0 and right must be < 2, and they're equal → check: left > 0 ✓, right < 2 ✗ → false ✓

- `[0,3,2,1]`:
  - Already shown above → true ✓

**Complexity**:
- Time: O(n), single pass from both ends
- Space: O(1)
