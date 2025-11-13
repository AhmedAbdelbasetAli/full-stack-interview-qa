A two-pointer scan from both ends, skipping non-alphanumeric and comparing case-insensitively, decides palindrome status in linear time and constant space. This avoids building filtered copies and handles empty-after-cleaning as palindrome by definition.[1][2]

### Approach
- Normalize comparison on the fly: move left and right indices inward; skip any character where Character.isLetterOrDigit(c) is false; compare Character.toLowerCase at both ends; return false on first mismatch; true if pointers cross.[2][3]
- This is the canonical two-pointer pattern for “valid palindrome” with O(n) time and O(1) extra space.[4][5]

### Java solution
```java
public class Solution {
    public boolean isPalindrome(String s) {
        int i = 0, j = s.length() - 1;
        while (i < j) {
            // skip left non-alphanumeric
            while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
            // skip right non-alphanumeric
            while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;
            // compare case-insensitively
            char a = Character.toLowerCase(s.charAt(i));
            char b = Character.toLowerCase(s.charAt(j));
            if (a != b) return false;
            i++; j--;
        }
        return true;
    }
}
```
This mirrors the standard two-pointer solution: skip with isLetterOrDigit, compare via toLowerCase, and early-exit on mismatch.[6][2]

### Why this approach
- Skips non-alphanumerics in place, avoiding extra memory from preprocessing or regex; handles long inputs efficiently.[7][2]
- Empty-after-filtering strings return true as they read the same both ways per the problem definition.[8][2]

### Complexity
- Time: O(n), each character is visited at most once by either pointer.[5][2]
- Space: O(1), only indices and a couple of temporaries are used.[1][5]

### Alternatives
- Build a cleaned StringBuilder with only lowercase alphanumerics, then two-pointer or reverse-compare; simpler but uses O(n) extra space.[3][9]
- Regex like s.replaceAll("[^A-Za-z0-9]", "").toLowerCase(); then check palindrome; concise but also O(n) space and regex overhead.[10][7]

### Edge cases
- Only punctuation/space (e.g., "   , ,"): reduces to empty and returns true as required.[2][8]
- Mixed cases and digits are handled by toLowerCase and isLetterOrDigit.[6][3]

