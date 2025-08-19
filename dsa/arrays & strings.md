# 🧠 Java Coding Interview Questions  
**Array Manipulation & Algorithms**

Here are **5 common array-based coding problems** with **Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java** as requested.

---

## 1. How do you reverse an array in place?

> Reverse the array **without using extra space** — modify the original array.

### ✅ Approach
- Use **two pointers**: one at the start, one at the end
- Swap elements and move pointers toward center

### ✅ Java Code
```java
public static void reverseArray(int[] arr) {
    int left = 0;
    int right = arr.length - 1;
    
    while (left < right) {
        // Swap
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
        
        // Move pointers
        left++;
        right--;
    }
}

// Example usage
public static void main(String[] args) {
    int[] arr = {1, 2, 3, 4, 5};
    reverseArray(arr);
    System.out.println(Arrays.toString(arr)); // [5, 4, 3, 2, 1]
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1) — in-place

### 💡 Interview Tip
> _"I use two pointers to swap elements from both ends. This is optimal because it's O(n) time and O(1) space. I always test edge cases: empty array, single element, even/odd length."_  

---

## 2. How do you find the duplicate number in an array of integers from 1 to n?

> Given an array of **n+1 integers** where each integer is between **1 and n**, find the **one duplicate**.

### ✅ Approach
- Use **Floyd’s Cycle Detection Algorithm** (Tortoise and Hare)
- Treat array as a linked list: `index → value`
- Duplicate creates a cycle

### ✅ Java Code
```java
public static int findDuplicate(int[] nums) {
    int slow = nums[0];
    int fast = nums[0];
    
    // Phase 1: Find intersection point
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow != fast);
    
    // Phase 2: Find entrance to cycle
    slow = nums[0];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    
    return slow;
}

// Example usage
public static void main(String[] args) {
    int[] nums = {1, 3, 4, 2, 2};
    System.out.println(findDuplicate(nums)); // 2
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use Floyd’s algorithm because it’s O(1) space. The array values act as pointers. The duplicate creates a cycle. I first find where slow and fast meet, then reset one pointer to find the duplicate. This is better than using a HashSet, which uses O(n) space."_  

---

## 3. Write a function to find two numbers in an array that add up to a target (Two Sum).

> Return **indices** of two numbers that sum to `target`.

### ✅ Approach
- Use **HashMap** to store `value → index`
- For each number, check if `target - num` exists

### ✅ Java Code
```java
import java.util.*;

public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        
        map.put(nums[i], i);
    }
    
    throw new IllegalArgumentException("No solution found");
}

// Example usage
public static void main(String[] args) {
    int[] nums = {2, 7, 11, 15};
    int[] result = twoSum(nums, 9);
    System.out.println(Arrays.toString(result)); // [0, 1]
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(n)

### 💡 Interview Tip
> _"I use a HashMap to store each number and its index. For every number, I check if its complement (target - num) exists. If yes, I return both indices. This is O(n) time and handles duplicates correctly. I always clarify: can I use the same element twice? (No)."_  

---

## 4. How do you remove duplicates from a sorted array?

> Modify the array **in-place** so that each unique element appears **only once**. Return the **new length**.

### ✅ Approach
- Use **two pointers**:
  - `i` → position to write
  - `j` → scan through array
- Only write when current element is different from previous

### ✅ Java Code
```java
public static int removeDuplicates(int[] arr) {
    if (arr.length == 0) return 0;
    
    int i = 0; // write index
    
    for (int j = 1; j < arr.length; j++) {
        if (arr[j] != arr[i]) {
            i++;
            arr[i] = arr[j];
        }
    }
    
    return i + 1; // new length
}

// Example usage
public static void main(String[] args) {
    int[] arr = {1, 1, 2, 2, 3, 4, 4};
    int newLength = removeDuplicates(arr);
    
    System.out.println("New length: " + newLength);
    for (int k = 0; k < newLength; k++) {
        System.out.print(arr[k] + " "); // 1 2 3 4
    }
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"Since the array is sorted, duplicates are adjacent. I use two pointers: one to write unique values, one to read. I only advance the write pointer when the current value differs from the last written one. This is optimal — O(n) time and O(1) space."_  

---

## 5. How do you rotate an array by k positions?

> Rotate the array to the **right by k steps**.

Example: `[1,2,3,4,5]`, `k=2` → `[4,5,1,2,3]`

### ✅ Approach
- Use **reverse trick**:
  1. Reverse entire array
  2. Reverse first `k` elements
  3. Reverse remaining elements

### ✅ Java Code
```java
public static void rotate(int[] nums, int k) {
    int n = nums.length;
    k = k % n; // Handle k > n
    
    if (k == 0) return;
    
    // Step 1: Reverse entire array
    reverse(nums, 0, n - 1);
    // Step 2: Reverse first k elements
    reverse(nums, 0, k - 1);
    // Step 3: Reverse last n-k elements
    reverse(nums, k, n - 1);
}

// Helper method to reverse a portion of array
private static void reverse(int[] nums, int start, int end) {
    while (start < end) {
        int temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start++;
        end--;
    }
}

// Example usage
public static void main(String[] args) {
    int[] nums = {1, 2, 3, 4, 5};
    rotate(nums, 2);
    System.out.println(Arrays.toString(nums)); // [4, 5, 1, 2, 3]
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use the reverse trick: reverse the whole array, then reverse the first k and last n-k parts. It's O(1) space and elegant. I always handle edge cases: k=0, k > n (use modulo), and empty array."_  

---

# 🧠 Java String Algorithms  part 2 


---

## 1. Write code to reverse a string without using built-in functions.

> Reverse a string **without using `StringBuilder.reverse()` or similar**.

### ✅ Approach
- Convert string to `char[]`
- Use two pointers: one at start, one at end
- Swap characters and move toward center

### ✅ Java Code
```java
public static String reverseString(String str) {
    if (str == null || str.isEmpty()) {
        return str;
    }

    char[] chars = str.toCharArray();
    int left = 0;
    int right = chars.length - 1;

    while (left < right) {
        // Swap
        char temp = chars[left];
        chars[left] = chars[right];
        chars[right] = temp;

        left++;
        right--;
    }

    return new String(chars);
}

// Example usage
public static void main(String[] args) {
    System.out.println(reverseString("hello")); // "olleh"
    System.out.println(reverseString("Java"));   // "avaJ"
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(n) — for char array

### 💡 Interview Tip
> _"I use two pointers to swap characters from both ends. This is optimal — O(n) time. I handle edge cases: null, empty, single character. I avoid `StringBuilder.reverse()` as requested."_  

---

## 2. How do you check if two strings are anagrams?

> Two strings are **anagrams** if they contain the **same characters in the same frequency** (ignoring case and spaces).

### ✅ Approach
- Remove spaces and convert to lowercase
- Count frequency of each character
- Compare frequency maps

### ✅ Java Code
```java
import java.util.*;

public static boolean areAnagrams(String s1, String s2) {
    if (s1 == null || s2 == null) return false;
    
    // Normalize: remove spaces, lowercase
    s1 = s1.replaceAll("\\s", "").toLowerCase();
    s2 = s2.replaceAll("\\s", "").toLowerCase();
    
    if (s1.length() != s2.length()) return false;

    Map<Character, Integer> freq = new HashMap<>();

    // Count frequency in s1
    for (char c : s1.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }

    // Subtract frequency for s2
    for (char c : s2.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) - 1);
        if (freq.get(c) < 0) {
            return false;
        }
    }

    // All counts should be zero
    return freq.values().stream().allMatch(count -> count == 0);
}

// Example usage
public static void main(String[] args) {
    System.out.println(areAnagrams("listen", "silent")); // true
    System.out.println(areAnagrams("hello", "bello"));   // false
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1) — at most 26 letters (if only a-z)

### 💡 Interview Tip
> _"I normalize strings (lowercase, no spaces), then use a frequency map. I increment for first string, decrement for second. If all counts are zero, they’re anagrams. This handles duplicates and is O(n). I could also sort both strings and compare — but that’s O(n log n)."_  

---

## 3. Find the longest substring without repeating characters.

> Given a string, find the **length of the longest substring with all unique characters**.

### ✅ Approach: Sliding Window with HashSet

- Use two pointers: `left` and `right`
- Expand `right` and add characters to a `Set`
- If duplicate found, move `left` and remove characters until no duplicate
- Track max length

### ✅ Java Code
```java
import java.util.*;

public static int longestUniqueSubstring(String s) {
    if (s == null || s.isEmpty()) return 0;

    Set<Character> seen = new HashSet<>();
    int left = 0, maxLen = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);

        while (seen.contains(c)) {
            seen.remove(s.charAt(left));
            left++;
        }

        seen.add(c);
        maxLen = Math.max(maxLen, right - left + 1);
    }

    return maxLen;
}

// Example usage
public static void main(String[] args) {
    System.out.println(longestUniqueSubstring("abcabcbb")); // 3 ("abc")
    System.out.println(longestUniqueSubstring("bbbbb"));    // 1 ("b")
    System.out.println(longestUniqueSubstring("pwwkew"));   // 3 ("wke")
}
```

### 📌 Time & Space
- **Time**: O(n) — each character visited at most twice
- **Space**: O(min(m,n)) — m = charset size

### 💡 Interview Tip
> _"I use the sliding window technique with a HashSet. I expand the right pointer and shrink left when a duplicate is found. This ensures all characters in the window are unique. I track the maximum window size. It’s O(n) time and optimal."_  

---

## 4. Implement string compression (e.g., "aaabcc" → "a3bc2").

> Compress consecutive repeated characters into `char + count`.

> If compressed string is not shorter, return original.

### ✅ Approach
- Traverse string, count consecutive characters
- Append char and count to result
- Only return compressed if shorter

### ✅ Java Code
```java
public static String compressString(String s) {
    if (s == null || s.length() <= 1) return s;

    StringBuilder compressed = new StringBuilder();
    char current = s.charAt(0);
    int count = 1;

    for (int i = 1; i < s.length(); i++) {
        if (s.charAt(i) == current) {
            count++;
        } else {
            compressed.append(current);
            if (count > 1) {
                compressed.append(count);
            }
            current = s.charAt(i);
            count = 1;
        }
    }

    // Append last group
    compressed.append(current);
    if (count > 1) {
        compressed.append(count);
    }

    // Return compressed only if shorter
    return compressed.length() < s.length() ? compressed.toString() : s;
}

// Example usage
public static void main(String[] args) {
    System.out.println(compressString("aaabcc"));     // "a3bc2"
    System.out.println(compressString("abc"));       // "abc" (no compression)
    System.out.println(compressString("aabbcc"));    // "a2b2c2" → same length → "aabbcc"
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(n) — for StringBuilder

### 💡 Interview Tip
> _"I traverse the string, count consecutive characters, and build the compressed version. I only return it if it’s shorter than the original — otherwise, I return the original to avoid bloating. I use StringBuilder for efficiency."_  

---

## 5. How do you find the first non-repeating character in a string?

> Return the **first character that appears only once** in the string.

Return its **index**, or `-1` if none.

### ✅ Approach
- First pass: count frequency of each character
- Second pass: find first character with count = 1

### ✅ Java Code
```java
import java.util.*;

public static int firstNonRepeatingChar(String s) {
    if (s == null || s.isEmpty()) return -1;

    Map<Character, Integer> freq = new HashMap<>();

    // Count frequency
    for (char c : s.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }

    // Find first char with count 1
    for (int i = 0; i < s.length(); i++) {
        if (freq.get(s.charAt(i)) == 1) {
            return i;
        }
    }

    return -1;
}

// Example usage
public static void main(String[] args) {
    System.out.println(firstNonRepeatingChar("abccba"));  // -1
    System.out.println(firstNonRepeatingChar("abca"));    // 1 ('b')
    System.out.println(firstNonRepeatingChar("leetcode")); // 0 ('l')
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1) — at most 26 letters

### 💡 Interview Tip
> _"I use a two-pass approach: first count frequencies, then scan for the first character with count 1. It’s O(n) time and optimal. I could use LinkedHashMap to track order, but this is simpler and faster."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify the problem** — case sensitivity? spaces? empty string?
2. **Talk through your approach** — don’t jump into coding
3. **Write clean, readable code** — use meaningful variable names
4. **Test with examples** — edge cases: empty, single char, all same
5. **State time & space complexity**

---

