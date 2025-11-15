Problem Analysis:
Find the first index where needle appears in haystack, or return -1 if absent, with lengths up to $$10^{4}$$ and lowercase letters only; edge cases include needle longer than haystack and exact match at position 0. A naive scan is $$O(n \cdot m)$$ in worst case, which is acceptable for $$10^{4}$$ but can degrade on repetitive patterns like "aaaa...ab" vs "aaaab", whereas linear-time pattern matching avoids redundant comparisons.[1][2][3]

High-Level Approach & Justification:
Use the KMP algorithm: preprocess needle to build the LPS (longest proper prefix which is also suffix) array, then scan haystack once while using LPS to skip re-checking characters after mismatches, achieving $$O(n + m)$$ time and $$O(m)$$ space. KMP is preferable over the naive approach because it guarantees linear time regardless of adversarial inputs, which is robust for competitive programming and interviews; it also fits cleanly into the constraints and is a standard solution for this problem on LeetCode 28.[2][3][1]

Java Solution:
```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (needle == null || needle.length() == 0) return 0;     // empty needle found at 0
        if (haystack == null) return -1;
        int n = haystack.length(), m = needle.length();
        if (m > n) return -1;

        int[] lps = buildLPS(needle);
        int i = 0; // index in haystack
        int j = 0; // index in needle

        while (i < n) {
            if (haystack.charAt(i) == needle.charAt(j)) {
                i++; j++;
                if (j == m) {
                    return i - m; // match ends at i-1, starts at i-m
                }
            } else if (j > 0) {
                j = lps[j - 1]; // fall back in needle using LPS
            } else {
                i++; // no prefix to fall back to
            }
        }
        return -1;
    }

    // Build LPS (Longest Prefix Suffix) array for needle
    private int[] buildLPS(String p) {
        int m = p.length();
        int[] lps = new int[m];
        int len = 0;      // length of current longest prefix-suffix
        int i = 1;        // lps[0] is 0 by definition

        while (i < m) {
            if (p.charAt(i) == p.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else if (len > 0) {
                len = lps[len - 1]; // try shorter prefix
            } else {
                lps[i] = 0;
                i++;
            }
        }
        return lps;
    }
}
```


Detailed Code Walkthrough:
Early exits cover empty needle returning 0, null haystack returning -1, and the case where needle is longer than haystack returning -1, simplifying control flow before KMP proper begins. buildLPS computes, for each prefix of needle, the length of the longest proper prefix equal to a suffix, enabling efficient fallback on mismatch without rechecking characters already known to match, which is central to KMP’s linear performance. The main loop advances both indices on matches and uses j = lps[j-1] on mismatches after partial matches; when j reaches m, the start index i - m is returned as the first occurrence by construction, otherwise -1 is returned after scanning the text.[1][2][3]

Complexity Analysis:
Time Complexity: $$O(n + m)$$, as building LPS is $$O(m)$$ and the search processes each haystack character at most once with constant-time fallback via LPS. Space Complexity: $$O(m)$$ for the LPS array, with constant additional variables, which is efficient given $$m \le 10^{4}$$ and avoids extra allocations during the scan.[2][3][1]

