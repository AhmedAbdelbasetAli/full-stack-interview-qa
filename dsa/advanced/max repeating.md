## Maximum K-Repeating Value Problem - Java Solution

I've created a comprehensive Java solution with three different approaches:

****

### Approach 1: Iterative String Building (Recommended)

This is the most straightforward approach:

```java
public static int maxRepeating(String sequence, String word) {
    String repeated = word;
    int count = 0;
    
    while (sequence.contains(repeated)) {
        count++;
        repeated += word;
    }
    
    return count;
}
```

**How it works:**
- Start with the word itself
- Keep concatenating the word and checking if it exists as a substring in the sequence
- Stop when the concatenated string is no longer found
- Return the count of successful concatenations

**Complexity Analysis:**
- **Time:** O(n × m) where n is sequence length and m is word length (substring search is O(n*m) per iteration)
- **Space:** O(n) for storing concatenated strings

### Approach 2: Optimized with StringBuilder

This version uses StringBuilder for better string concatenation performance:

```java
public static int maxRepeatingOptimized(String sequence, String word) {
    StringBuilder repeated = new StringBuilder(word);
    int count = 0;
    int maxPossible = sequence.length() / word.length();
    
    for (int i = 0; i < maxPossible; i++) {
        if (sequence.contains(repeated.toString())) {
            count++;
            repeated.append(word);
        } else {
            break;
        }
    }
    
    return count;
}
```

**Key optimization:** Adds an upper bound `maxPossible` to prevent unnecessary iterations since k cannot exceed sequence.length / word.length.

### Approach 3: Binary Search

For cases with very large inputs, binary search can be more efficient:

```java
public static int maxRepeatingBinarySearch(String sequence, String word) {
    int left = 0, right = sequence.length() / word.length();
    int result = 0;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        StringBuilder repeated = new StringBuilder();
        
        for (int i = 0; i < mid; i++) {
            repeated.append(word);
        }
        
        if (sequence.contains(repeated.toString())) {
            result = mid;
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return result;
}
```

**Complexity:** O(log(n/m) × n) - performs binary search on possible k values while doing substring matching.

### Test Results

The solution correctly handles all provided examples:
- Example 1: sequence = "ababc", word = "ab" → **2** ("abab" is a substring)
- Example 2: sequence = "ababc", word = "ba" → **1** ("ba" is found, "baba" is not)
- Example 3: sequence = "ababc", word = "ac" → **0** ("ac" not found)

**Recommendation:** Use **Approach 1** for most cases due to its simplicity and sufficient performance given the constraints (max length 100). Use **Approach 3** if dealing with much larger inputs.
