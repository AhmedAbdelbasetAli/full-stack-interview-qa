# 🔍 Searching & Sorting Algorithms  
**Binary Search, Merge/Quick Sort, K-th Largest, Stable vs Unstable**

Here are **4 essential searching and sorting problems** with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. Implement binary search in a sorted array

> **Binary search** finds the index of a target value in a **sorted array** by repeatedly dividing the search space in half.

---

### ✅ Approach
- Use two pointers: `left` and `right`
- Compute `mid = left + (right - left) / 2` (avoid overflow)
- If `arr[mid] == target` → return `mid`
- If `arr[mid] < target` → search right half
- If `arr[mid] > target` → search left half
- Repeat until found or `left > right`

---

### ✅ Java Code
```java
public static int binarySearch(int[] arr, int target) {
    if (arr == null || arr.length == 0) return -1;

    int left = 0;
    int right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2; // Avoid overflow

        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1; // Not found
}

// Example usage
public static void main(String[] args) {
    int[] arr = {1, 3, 5, 7, 9, 11};
    System.out.println(binarySearch(arr, 7));  // 3
    System.out.println(binarySearch(arr, 4));  // -1
}
```

---

### 📌 Time & Space
- **Time**: O(log n)
- **Space**: O(1) — iterative version

✅ Can also be implemented recursively (O(log n) space due to call stack).

---

### 💡 Interview Tip
> _"I use two pointers and avoid integer overflow by computing mid as `left + (right - left) / 2`. I return -1 if not found. I always clarify: should I return first/last occurrence for duplicates? (This leads to 'lower/upper bound' questions.)"_  

---

## 2. Explain merge sort and quick sort. What are their time complexities?

---

### 🔹 2.1 Merge Sort

> **Divide and conquer** algorithm:
1. **Divide**: Split array into two halves
2. **Conquer**: Recursively sort both halves
3. **Combine**: Merge the two sorted halves

---

#### ✅ Java Code
```java
public static void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

private static void merge(int[] arr, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left, j = mid + 1, k = 0;

    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[k++] = arr[i++];
        } else {
            temp[k++] = arr[j++];
        }
    }

    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];

    System.arraycopy(temp, 0, arr, left, temp.length);
}
```

#### 📌 Time & Space
- **Best/Avg/Worst**: O(n log n)
- **Space**: O(n) — temporary array
- ✅ **Stable** — preserves order of equal elements
- ✅ **Not in-place** — requires extra memory

---

### 🔹 2.2 Quick Sort

> **Divide and conquer** with a **pivot**:
1. **Choose pivot** (e.g., first, last, random)
2. **Partition**: Rearrange so elements < pivot are on left, > on right
3. **Recursively sort** left and right subarrays

---

#### ✅ Java Code
```java
public static void quickSort(int[] arr, int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

private static int partition(int[] arr, int low, int high) {
    int pivot = arr[high]; // Choose last element as pivot
    int i = low - 1; // Index of smaller element

    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr, i, j);
        }
    }
    swap(arr, i + 1, high);
    return i + 1;
}

private static void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

#### 📌 Time & Space
- **Best/Avg**: O(n log n)
- **Worst**: O(n²) — when pivot is smallest/largest (e.g., already sorted)
- **Space**: O(log n) — recursion stack (best case)
- ❌ **Unstable** — may swap equal elements
- ✅ **In-place** — O(1) extra space (not counting recursion)

---

### 📌 Comparison Summary

| Feature | Merge Sort | Quick Sort |
|--------|------------|------------|
| **Time (Best/Avg)** | O(n log n) | O(n log n) |
| **Time (Worst)** | O(n log n) | O(n²) |
| **Space** | O(n) | O(log n) |
| **Stable** | ✅ Yes | ❌ No |
| **In-place** | ❌ No | ✅ Yes |
| **Use Case** | Stable sort, linked lists | General-purpose, arrays |

---

### 💡 Interview Tip
> _"Merge sort is stable, O(n log n) always, but uses O(n) space. Quick sort is in-place and fast on average, but worst-case O(n²). I use merge sort when stability matters, quick sort for general arrays. Randomized pivot reduces worst-case risk."_  

---

## 3. How do you find the k-th largest element in an unsorted array?

> Find the **k-th largest** element (1-indexed).

Example: `[3,2,1,5,6,4]`, `k=2` → `5`

---

### 🔹 3.1 Approach 1: Min-Heap (Optimal for small k)

- Use a **min-heap of size k**
- Add elements; if size > k, remove smallest
- At end, top is k-th largest

---

#### ✅ Java Code (Min-Heap)
```java
import java.util.*;

public static int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();

    for (int num : nums) {
        minHeap.offer(num);
        if (minHeap.size() > k) {
            minHeap.poll(); // Remove smallest
        }
    }

    return minHeap.peek();
}
```

- **Time**: O(n log k)
- **Space**: O(k)

---

### 🔹 3.2 Approach 2: Quick Select (Best Average)

- Based on quick sort partition
- Pick random pivot, partition array
- If pivot index == `n-k` → found
- Else, recurse on left or right

---

#### ✅ Java Code (Quick Select)
```java
public static int findKthLargestQuickSelect(int[] nums, int k) {
    int n = nums.length;
    return quickSelect(nums, 0, n - 1, n - k);
}

private static int quickSelect(int[] nums, int left, int right, int k) {
    if (left == right) return nums[left];

    Random rand = new Random();
    int pivotIndex = left + rand.nextInt(right - left + 1);
    pivotIndex = partition(nums, left, right, pivotIndex);

    if (k == pivotIndex) {
        return nums[k];
    } else if (k < pivotIndex) {
        return quickSelect(nums, left, pivotIndex - 1, k);
    } else {
        return quickSelect(nums, pivotIndex + 1, right, k);
    }
}

private static int partition(int[] nums, int left, int right, int pivotIndex) {
    int pivotValue = nums[pivotIndex];
    swap(nums, pivotIndex, right);
    int storeIndex = left;

    for (int i = left; i < right; i++) {
        if (nums[i] < pivotValue) {
            swap(nums, storeIndex, i);
            storeIndex++;
        }
    }
    swap(nums, storeIndex, right);
    return storeIndex;
}

private static void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

- **Time**: O(n) average, O(n²) worst
- **Space**: O(1)

---

### 📌 Summary of Approaches

| Method | Time | Space | Best For |
|-------|------|-------|---------|
| **Min-Heap** | O(n log k) | O(k) | Small k |
| **Quick Select** | O(n) avg | O(1) | General use |
| **Sort** | O(n log n) | O(1) | Simple, one-off |

---

### 💡 Interview Tip
> _"For small k, I use a min-heap of size k. For better average performance, I use quick select — it's like quick sort but only recurses on one side. It's O(n) on average and in-place. I randomize the pivot to avoid worst-case."_  

---

## 4. What is the difference between stable and unstable sorting algorithms?

> **Stability** refers to whether the **relative order of equal elements is preserved** after sorting.

---

### 🔹 4.1 Example
```java
// Array of people: (name, age)
// Original: [("Alice", 25), ("Bob", 20), ("Charlie", 25)]
// Sorted by age:
// Stable:   [("Bob", 20), ("Alice", 25), ("Charlie", 25)]  ← Alice before Charlie
// Unstable: [("Bob", 20), ("Charlie", 25), ("Alice", 25)]  ← Order may change
```

---

### 🔹 4.2 Stable Sorting Algorithms
| Algorithm | Stable? |
|---------|--------|
| ✅ **Merge Sort** | Yes |
| ✅ **Bubble Sort** | Yes |
| ✅ **Insertion Sort** | Yes |
| ✅ **Counting Sort** | Yes |
| ✅ **Radix Sort** | Yes |

---

### 🔹 4.3 Unstable Sorting Algorithms
| Algorithm | Stable? |
|---------|--------|
| ❌ **Quick Sort** | No |
| ❌ **Heap Sort** | No |
| ❌ **Selection Sort** | No |
| ❌ **Shell Sort** | No |

---

### 🔹 4.4 When Stability Matters
| Use Case | Why |
|--------|-----|
| ✅ **Multi-key sorting** | Sort by age, then by name — stability preserves name order |
| ✅ **UI sorting** | User expects same-order items to stay in order |
| ✅ **Data processing** | Preserve input order for equal keys |

---

### 📌 Interview Answer
> _"A stable sort preserves the relative order of equal elements. For example, if Alice and Charlie are both 25, a stable sort keeps Alice before Charlie if she was first in input. Merge sort is stable; quick sort is not. I use stable sorts when sorting by multiple criteria or when user experience depends on consistent ordering."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: Is the array sorted? Are duplicates allowed?
2. **State time & space complexity** for each approach
3. **Draw the algorithm** when explaining (e.g., partition, merge)
4. **Handle edge cases**: empty array, k=1, all same elements
5. **Choose the right tool**: heap for k-th largest, binary search for sorted arrays

---
