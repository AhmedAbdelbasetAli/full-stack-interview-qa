Use a single pass with a hash set: for each value, check if its double or (if even) its half has already been seen. This is O(n) time and O(n) space and handles negatives correctly.[1][2]

```java
class Solution {
    public boolean checkIfExist(int[] arr) {
        Set<Integer> seen = new HashSet<>();

        for (int x : arr) {
            // Check if we've already seen a value that is double or half of x
            if (seen.contains(2 * x) || (x % 2 == 0 && seen.contains(x / 2))) {
                return true;
            }
            seen.add(x);
        }

        return false;
    }
}
```

### Why this works

- If `arr[i] == 2 * arr[j]`, then when visiting `arr[i]`, either `2 * arr[i]` or `arr[i] / 2` will match a previously seen value, depending on which index came first.[3][1]
- The check `(x % 2 == 0 && seen.contains(x / 2))` ensures integer division is valid and covers the case where the current element is the double of some earlier element.[1]

### Examples

- `[10,2,5,3]`:  
  - Visit 10 → seen={10}.  
  - Visit 2 → seen={10,2}.  
  - Visit 5 → `seen.contains(10)` is true (2*5), return true. ✓  

- `[3,1,7,11]`:  
  - No element finds its double or half in `seen`, return false. ✓

