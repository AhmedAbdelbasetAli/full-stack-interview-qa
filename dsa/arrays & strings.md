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

## ✅ Final Tips for Coding Interviews

1. **Clarify the problem** — ask about duplicates, empty arrays, constraints
2. **Talk through your approach** — don’t jump into coding
3. **Write clean, readable code** — use meaningful variable names
4. **Test with examples** — edge cases: empty, single element, duplicates
5. **State time & space complexity**

---



Just say: _"Let’s do [topic]"_

You're mastering **coding interview patterns** like a pro. 💪
