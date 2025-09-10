You’re stepping into the **foundation of data organization** — where ordering unlocks search, compression, analytics, and more.

> 💡 **Sorting Algorithms** are like **organizing a library** — some librarians bubble through shelves (slow), some split and merge sections (smart), some pick pivots and partition (fastest on average).

Used in:
- **Databases** → ORDER BY, index creation.
- **Search Engines** → Rank results by relevance.
- **Analytics** → Top-K, percentiles, histograms.
- **UIs** → Sorted lists, tables, dropdowns.
- **Compilers** → Symbol table ordering.

And yes — **FAANG interviews** test your understanding of sorting not to make you reimplement `Arrays.sort()`, but to assess your grasp of **trade-offs, recursion, and complexity**.

Let’s master it — Java-first, production-aware, interview-sharp.

—

# 🧱 1. What It Is — Sorting Algorithms

> **Core Purpose**: Rearrange elements in a list/array in a specified order (usually ascending/descending).

```
Types:
- Bubble Sort → O(n²) → educational only
- Selection Sort → O(n²) → slightly better constant
- Insertion Sort → O(n²) worst, O(n) best → good for small/partially sorted
- Merge Sort → O(n log n) stable → guaranteed performance
- Quick Sort → O(n log n) avg, O(n²) worst → in-place, fast in practice
- Heap Sort → O(n log n) → in-place, not stable
- Tim Sort → Hybrid (Python/Java) → adaptive, stable
```

> 💡 Uber: “Sort drivers by ETA” → TimSort in Java.  
> 💡 Android: “Sort contacts by name” → Arrays.sort() → TimSort.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ 1. Bubble Sort — O(n²) — Don’t Use (Educational)

```java
public class BubbleSort {
    public static void sort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    swap(arr, j, j + 1);
                }
            }
        }
    }

    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

→ **Why not use**: O(n²) — too slow for n > 1000.

## ✅ 2. Insertion Sort — O(n²) worst, O(n) best

```java
public class InsertionSort {
    public static void sort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;

            // Shift elements greater than key to right
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }
}
```

→ **Use when**: Small arrays (n < 50), or nearly sorted data.

## ✅ 3. Merge Sort — O(n log n), Stable, Not In-Place

```java
public class MergeSort {
    public static void sort(int[] arr) {
        if (arr.length < 2) return;
        mergeSort(arr, 0, arr.length - 1);
    }

    private static void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int mid = left + (right - left) / 2;
            mergeSort(arr, left, mid);
            mergeSort(arr, mid + 1, right);
            merge(arr, left, mid, right);
        }
    }

    private static void merge(int[] arr, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;

        int[] L = new int[n1], R = new int[n2];

        System.arraycopy(arr, left, L, 0, n1);
        System.arraycopy(arr, mid + 1, R, 0, n2);

        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            if (L[i] <= R[j]) {
                arr[k++] = L[i++];
            } else {
                arr[k++] = R[j++];
            }
        }

        while (i < n1) arr[k++] = L[i++];
        while (j < n2) arr[k++] = R[j++];
    }
}
```

→ **Use when**: You need **stable sort**, or guaranteed O(n log n).

## ✅ 4. Quick Sort — O(n log n) avg, O(n²) worst, In-Place

```java
public class QuickSort {
    public static void sort(int[] arr) {
        quickSort(arr, 0, arr.length - 1);
    }

    private static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }

    private static int partition(int[] arr, int low, int high) {
        int pivot = arr[high]; // rightmost as pivot
        int i = low - 1; // index of smaller element

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
}
```

→ **Use when**: Average-case performance matters, in-place is needed.

## ✅ 5. How Java’s `Arrays.sort()` Actually Works

> **For primitives (int[], double[], etc.)**: **Dual-Pivot QuickSort** — faster than classic quicksort in practice.  
> **For objects (Integer[], String[], etc.)**: **TimSort** — hybrid stable sort (merge + insertion), adaptive.

```java
// Java’s built-in — always use this in production
int[] arr = {5, 2, 8, 1};
Arrays.sort(arr); // Dual-Pivot QuickSort for primitives

String[] names = {"Alice", "Bob", "Charlie"};
Arrays.sort(names); // TimSort for objects
```

### ⏱️ Time & Space Complexity Summary

| Algorithm       | Time (Best) | Time (Avg) | Time (Worst) | Space     | Stable? | In-Place? |
|----------------|-------------|------------|--------------|-----------|---------|-----------|
| **Bubble Sort**| O(n)        | O(n²)      | O(n²)        | O(1)      | Yes     | Yes       |
| **Insertion Sort**| O(n)     | O(n²)      | O(n²)        | O(1)      | Yes     | Yes       |
| **Merge Sort** | O(n log n)  | O(n log n) | O(n log n)   | O(n)      | Yes     | No        |
| **Quick Sort** | O(n log n)  | O(n log n) | O(n²)        | O(log n)  | No      | Yes       |
| **Heap Sort**  | O(n log n)  | O(n log n) | O(n log n)   | O(1)      | No      | Yes       |
| **TimSort**    | O(n)        | O(n log n) | O(n log n)   | O(n)      | Yes     | No        |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Arrays.sort()** → 99% of cases — optimized, battle-tested.
- **Custom Comparator** → `Arrays.sort(users, Comparator.comparing(User::getAge))`.
- **Partial Sort** → `PriorityQueue` for top-K → O(n log k).
- **External Sort** → For data larger than RAM → merge sort variant.

> 💡 Spring Boot: Sort REST API results → `Arrays.sort()` or `List.sort()`.  
> 💡 Android: Sort `RecyclerView` items → `Collections.sort()` → TimSort.

## ✅ Interview Scenarios

- **Implement QuickSort/MergeSort** → test recursion, partitioning.
- **Sort Colors (Dutch Flag)** → #75 → partitioning without extra space.
- **Top K Frequent Elements** → #347 → heap or partial sort.
- **Merge Intervals** → #56 → sort by start time.

## ✅ System Design Contexts

- **Database Indexes** → B-tree sort → O(n log n) build, O(log n) search.
- **MapReduce** → Sort phase before reduce.
- **Leaderboards** → Sort by score → `Arrays.sort()` or priority queue for top-K.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Reimplementing `Arrays.sort()`** → unless for learning — Java’s is optimized.

```java
// 🚫 Don’t do this in prod
bubbleSort(arr);

// ✅ Always
Arrays.sort(arr);
```

- **QuickSort on already sorted data** → O(n²) if pivot is first/last → use random pivot or median-of-three.

- **MergeSort when memory is tight** → O(n) space → use HeapSort or QuickSort.

## 🐢 Performance Traps

- **Not using `Collections.sort()` for `List<T>`** → it’s TimSort — stable and adaptive.

```java
List<Integer> list = new ArrayList<>();
Collections.sort(list); // TimSort
```

- **Autoboxing cost** → `Integer[]` vs `int[]` → primitives faster.

```java
// ✅ Faster
int[] arr = new int[1000000];

// 🐢 Slower — autoboxing
Integer[] arr = new Integer[1000000];
```

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| General purpose                | `Arrays.sort()`           | Optimized, adaptive                      |
| Need stable sort               | `Arrays.sort()` (objects) | TimSort is stable                        |
| Memory constrained             | HeapSort / QuickSort      | O(1) or O(log n) space                   |
| Partially sorted               | Insertion Sort / TimSort  | Adaptive — O(n) best case                |
| Top-K elements                 | `PriorityQueue`           | O(n log k) — better than full sort       |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Use `Arrays.sort()` — But Know What’s Under the Hood

- **Primitives** → Dual-Pivot QuickSort (Yaroslavskiy’s algorithm).
- **Objects** → TimSort — stable, adaptive, hybrid.

## 📦 Memory & Performance

- **Primitives** → faster, no GC pressure.
- **Objects** → stable sort → preserves order of equal elements.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use `@SortDefault` in `Pageable` for sorted API responses.
- **In Android**: Avoid sorting large lists on UI thread → use background thread.
- **Logging**: Log sort time for large datasets — “Sorting 1M users took 120ms”.

## 🤯 Fun Fact

**TimSort** was invented by Tim Peters for Python in 2002 — and adopted by Java in JDK 7 for object arrays because it’s **faster than MergeSort on real-world data** (partially sorted, with runs).

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Sort an Array

> Given an array of integers `nums`, sort it in ascending order.  
> You may implement any algorithm — but in production, you’d use `Arrays.sort()`.

LeetCode #912 — implement your own sort.

### 🧩 Starter Code (QuickSort)

```java
public class SortArray {
    public static int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }

    private static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;

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

    public static void main(String[] args) {
        int[] nums = {5,2,3,1};
        System.out.println(Arrays.toString(sortArray(nums))); // [1,2,3,5]
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class SortArrayTest {
    @Test
    void testSortArray() {
        assertArrayEquals(
            new int[]{1,2,3,5},
            SortArray.sortArray(new int[]{5,2,3,1})
        );
    }
}
```

> 💡 Time: O(n log n) avg, Space: O(log n) — QuickSort.

—

