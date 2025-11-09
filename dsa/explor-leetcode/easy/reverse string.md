## Problem Understanding

This is a classic **"Reverse String"** problem that requires **in-place reversal** with O(1) extra space. The constraint from the attached document means we must modify the input array directly without creating a new array.[1][2][3][4][5]

Key requirements:
1. **In-place modification**: Modify the input array directly[1]
2. **O(1) space**: Only constant extra memory allowed[3][5]
3. **Character array**: Input is already a char array (not a String object)

This is the **exact example** from the attached document about in-place algorithms.[1]

## Optimal Solution: Two-Pointer Technique

The **classic and most elegant approach** uses two pointers that swap elements from both ends moving toward the center.[2][5][3]

### The Core Insight

**Strategy**:[5][3]
1. Place one pointer at the **start** of the array
2. Place another pointer at the **end** of the array
3. **Swap** characters at both pointers
4. Move pointers **toward the center** (left++, right--)
5. **Stop** when pointers meet or cross

This is the **same algorithm** described in the attached document for reversing arrays.[1]

### Why This is Optimal

- **Single pass**: O(n/2) iterations = O(n) time[3][5]
- **True O(1) space**: Only uses two integer pointers and one temp variable[4][1]
- **In-place**: Modifies array directly, no additional data structures[5]
- **Simple**: Clean, readable, and easy to implement[2]

## Java Solution

```java
public class Solution {
    /**
     * Reverses a string represented as a character array in-place
     * Uses two-pointer technique with O(1) extra space
     * 
     * @param s - character array to reverse in-place
     */
    public void reverseString(char[] s) {
        // Edge case: null or single character
        if (s == null || s.length <= 1) {
            return;
        }
        
        // Initialize two pointers
        int left = 0;                // Start of array
        int right = s.length - 1;    // End of array
        
        // Swap characters from both ends, moving toward center
        while (left < right) {
            // Swap s[left] and s[right]
            char temp = s[left];
            s[left] = s[right];
            s[right] = temp;
            
            // Move pointers toward center
            left++;
            right--;
        }
        
        // Array is now reversed in-place
    }
}
```

This is **exactly the pattern** shown in the attached document:[1]
```
function reverse_in_place(a[0..n-1])
    for i from 0 to floor((n-2)/2)
        tmp := a[i]
        a[i] := a[n − 1 − i]
        a[n − 1 − i] := tmp
```

## Detailed Walkthrough

### Example 1: `s = ['h','e','l','l','o']`

**Step-by-step execution**:

| Step | left | right | s[left] | s[right] | Action | Array State |
|------|------|-------|---------|----------|--------|-------------|
| 0 | 0 | 4 | 'h' | 'o' | Swap | `['o','e','l','l','h']` |
| 1 | 1 | 3 | 'e' | 'l' | Swap | `['o','l','l','e','h']` |
| 2 | 2 | 2 | - | - | left == right, stop | `['o','l','l','e','h']` |

**Final result**: `['o','l','l','e','h']` ✓

**Key observation**: Middle element 'l' at index 2 doesn't move (palindrome center).

### Example 2: `s = ['H','a','n','n','a','h']`

| Step | left | right | s[left] | s[right] | Action | Array State |
|------|------|-------|---------|----------|--------|-------------|
| 0 | 0 | 5 | 'H' | 'h' | Swap | `['h','a','n','n','a','H']` |
| 1 | 1 | 4 | 'a' | 'a' | Swap (no change) | `['h','a','n','n','a','H']` |
| 2 | 2 | 3 | 'n' | 'n' | Swap (no change) | `['h','a','n','n','a','H']` |
| 3 | 3 | 2 | - | - | left > right, stop | `['h','a','n','n','a','H']` |

**Final result**: `['h','a','n','n','a','H']` ✓

**Note**: Even-length array, no middle element.

### Visual Representation

```
Initial: ['h', 'e', 'l', 'l', 'o']
           ↑              ↑
         left           right

Step 1:  ['o', 'e', 'l', 'l', 'h']
                ↑         ↑
              left      right

Step 2:  ['o', 'l', 'l', 'e', 'h']
                     ↑
                 left=right (stop)
```

## Alternative Solutions

### Approach 1: For Loop (Index-Based)

```java
public class SolutionForLoop {
    public void reverseString(char[] s) {
        int n = s.length;
        
        // Iterate through first half
        for (int i = 0; i < n / 2; i++) {
            // Swap i with its mirror position
            char temp = s[i];
            s[i] = s[n - 1 - i];
            s[n - 1 - i] = temp;
        }
    }
}
```

**Same complexity**, slightly different style.[6]

### Approach 2: Recursive (NOT Recommended for O(1) Space)

```java
public class SolutionRecursive {
    public void reverseString(char[] s) {
        reverseHelper(s, 0, s.length - 1);
    }
    
    private void reverseHelper(char[] s, int left, int right) {
        // Base case
        if (left >= right) {
            return;
        }
        
        // Swap current positions
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;
        
        // Recursive call
        reverseHelper(s, left + 1, right - 1);
    }
}
```

**Complexity**:[7]
- Time: O(n)
- Space: O(n) due to recursion stack - **violates O(1) constraint!**

**Note**: While elegant, recursion uses O(n) stack space, violating the problem's space constraint.[7][6]

### Approach 3: XOR Swap (Avoiding Temp Variable)

```java
public class SolutionXOR {
    public void reverseString(char[] s) {
        int left = 0;
        int right = s.length - 1;
        
        while (left < right) {
            // XOR swap without temp variable
            s[left] = (char)(s[left] ^ s[right]);
            s[right] = (char)(s[left] ^ s[right]);
            s[left] = (char)(s[left] ^ s[right]);
            
            left++;
            right--;
        }
    }
}
```

**Why not recommended**:
- Less readable
- No practical benefit (compiler optimizes temp variables)
- Can confuse in interviews

## Complexity Analysis

### Time Complexity: O(n)
- Iterate through half the array: n/2 iterations
- Each iteration performs O(1) swap
- Total: O(n/2) = O(n)[3][5]

**Precise**: O(⌊n/2⌋) swaps

### Space Complexity: O(1)
- Only uses 3 variables: `left`, `right`, `temp`[4][5][1]
- Space used is **constant**, independent of input size
- Satisfies the strict O(1) in-place requirement[1]

## Why This is the Gold Standard

### 1. **Textbook In-Place Algorithm**
This is the **exact example** used to teach in-place algorithms.[1]

### 2. **Optimal Complexity**
- **Cannot do better than O(n) time**: Must touch each element at least once
- **Cannot do better than O(1) space**: Only need pointers for swapping

### 3. **Simple and Clear**
- Easy to explain and implement
- No complex logic or edge cases[2][5]

### 4. **Foundation Pattern**
This two-pointer swap pattern applies to many problems:
- Palindrome checking
- Array rotation
- Reversing linked lists
- String permutations

## Edge Cases

### Case 1: Empty Array
```java
s = []
Output: []  // Handle with null/length check
```

### Case 2: Single Character
```java
s = ['a']
Output: ['a']  // No swap needed
```

### Case 3: Two Characters
```java
s = ['a','b']
Output: ['b','a']  // Single swap
```

### Case 4: Palindrome
```java
s = ['a','b','a']
Output: ['a','b','a']  // Still reversed correctly
```

### Case 5: All Same Characters
```java
s = ['a','a','a','a']
Output: ['a','a','a','a']  // Unchanged but algorithm works
```

### Case 6: Special Characters
```java
s = ['!','@','#','$']
Output: ['$','#','@','!']  // Works with any printable ASCII
```

### Case 7: Large Array
```java
s.length = 100,000
// Efficiently handles large inputs in O(n) time
```

## Common Mistakes to Avoid

### 1. **Wrong Loop Condition**
```java
// WRONG: Iterates too many times, undoing the reversal
while (left <= right) {  // Should be <, not <=
    swap(s[left], s[right]);
    left++;
    right--;
}

// For odd-length arrays, this swaps middle element with itself unnecessarily
```

### 2. **Creating New Array**
```java
// WRONG: Violates O(1) space and in-place constraints
char[] reversed = new char[s.length];
for (int i = 0; i < s.length; i++) {
    reversed[i] = s[s.length - 1 - i];
}
```

### 3. **Not Handling Empty/Null**
```java
// WRONG: Potential NullPointerException or ArrayIndexOutOfBounds
public void reverseString(char[] s) {
    int left = 0;
    int right = s.length - 1;  // Crashes if s is null
    // ...
}

// CORRECT: Add guard clause
if (s == null || s.length <= 1) return;
```

### 4. **Using StringBuilder**
```java
// WRONG: Uses O(n) extra space
StringBuilder sb = new StringBuilder();
for (int i = s.length - 1; i >= 0; i--) {
    sb.append(s[i]);
}
```

### 5. **Forgetting to Move Pointers**
```java
// WRONG: Infinite loop!
while (left < right) {
    swap(s[left], s[right]);
    // Forgot to increment/decrement - infinite loop!
}
```

## Interview Tips

1. **Recognize the pattern**:
   - "This is a classic two-pointer problem."
   - "I'll use pointers at both ends and swap while moving toward center."

2. **Mention in-place constraint**:
   - "The problem requires O(1) space, so I can't create a new array."
   - "I'll modify the input array directly."[1]

3. **Walk through example**:
   - Draw `['h','e','l','l','o']`
   - Show pointer positions at each step
   - Demonstrate the swaps visually

4. **Discuss complexity**:
   - "Time: O(n) - visit each element once through n/2 swaps"
   - "Space: O(1) - only use three variables regardless of input size"[5]

5. **Mention edge cases**:
   - "Works for empty arrays, single characters, and palindromes."
   - "Handles both even and odd-length arrays correctly."

6. **Compare alternatives**:
   - "Could use recursion, but that uses O(n) stack space."
   - "Could use XOR swap, but it's less readable with no real benefit."

7. **Reference the document**:
   - "This is the textbook in-place reversal algorithm."[1]

## Code Variations

### Version 1: Enhanced Readability
```java
public void reverseString(char[] s) {
    if (s == null || s.length <= 1) return;
    
    for (int left = 0, right = s.length - 1; left < right; left++, right--) {
        // Swap characters
        char temp = s[left];
        s[left] = s[right];
        s[right] = temp;
    }
}
```

### Version 2: Extract Swap to Helper
```java
public void reverseString(char[] s) {
    if (s == null || s.length <= 1) return;
    
    int left = 0, right = s.length - 1;
    while (left < right) {
        swap(s, left++, right--);
    }
}

private void swap(char[] arr, int i, int j) {
    char temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```
