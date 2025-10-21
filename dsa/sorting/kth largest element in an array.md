

---

## 🎯 PROBLEM: Kth Largest Element in an Array

> Given an integer array `nums` and an integer `k`, return the **kth largest element** in the array.  
> Note: kth largest means **kth distinct by value**, not by index.

**Examples**:  
- Input: `nums = [3,2,1,5,6,4]`, `k = 2` → Output: `5`  
- Input: `nums = [3,2,3,1,2,4,5,5,6]`, `k = 4` → Output: `4`

---

## 🧠 STEP 1: Understand the Problem

- **kth largest** = element at position `k` if array were sorted in **descending order**.
- **Duplicates count separately** → `[5,5,4]`, k=2 → 5 (not 4).
- Must be **efficient** — no full sort if possible.

---

## 🧩 STEP 2: Three Approaches — Which to Use?

### ✅ Approach 1: Min-Heap of Size k (Recommended for Interviews)

- Keep a **min-heap** of size `k`.
- For each number:
  - If heap size < k → add it.
  - Else if number > heap root → replace root.
- Root of heap = kth largest.

→ O(n log k) time, O(k) space.

### ✅ Approach 2: QuickSelect (Optimal for Large n)

- Use **QuickSort partitioning** to find kth largest in O(n) avg time.
- Worst-case O(n²) — but can be mitigated with random pivot.

→ O(n) avg time, O(1) space.

### ❌ Approach 3: Full Sort

- Sort array, return `nums[n-k]`.
- Simple but O(n log n) — not optimal.

→ Use only for small n.

---

## ✍️ STEP 3: Code It — Min-Heap Approach (Interview Favorite)

```java
import java.util.*;

public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        // Min-heap of size k
        PriorityQueue<Integer> heap = new PriorityQueue<>();

        for (int num : nums) {
            if (heap.size() < k) {
                heap.offer(num);
            } else if (num > heap.peek()) {
                heap.poll();
                heap.offer(num);
            }
        }

        return heap.peek(); // kth largest
    }
}
```

---

## ✍️ STEP 4: Code It — QuickSelect Approach (Optimal)

```java
public class KthLargestQuickSelect {
    public int findKthLargest(int[] nums, int k) {
        int targetIndex = nums.length - k;
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int pivotIndex = partition(nums, left, right);
            if (pivotIndex == targetIndex) {
                return nums[pivotIndex];
            } else if (pivotIndex < targetIndex) {
                left = pivotIndex + 1;
            } else {
                right = pivotIndex - 1;
            }
        }

        return -1; // unreachable
    }

    private int partition(int[] nums, int left, int right) {
        // Random pivot to avoid worst-case
        int randomIndex = left + (int)(Math.random() * (right - left + 1));
        swap(nums, randomIndex, right);

        int pivot = nums[right];
        int i = left;

        for (int j = left; j < right; j++) {
            if (nums[j] <= pivot) {
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

---

## 🧪 STEP 5: Trace Through Example (Min-Heap)

Input: `nums = [3,2,1,5,6,4]`, `k = 2`

| num | heap (min-heap) | action                     |
|-----|------------------|----------------------------|
| 3   | [3]              | size<2 → add               |
| 2   | [2,3]            | size<2 → add               |
| 1   | [2,3]            | 1 <= 2 → skip              |
| 5   | [3,5]            | 5>2 → replace 2 with 5     |
| 6   | [5,6]            | 6>3 → replace 3 with 6     |
| 4   | [5,6]            | 4 <= 5 → skip              |

→ Return `5` ✅

---

## ⏱️ STEP 6: Complexity Analysis

| Approach       | Time               | Space     | Notes                                  |
|----------------|--------------------|-----------|----------------------------------------|
| **Min-Heap**   | O(n log k)         | O(k)      | Best for interviews, stable            |
| **QuickSelect**| O(n) avg, O(n²) worst | O(1)   | Best for large n, but worst-case risky |
| **Full Sort**  | O(n log n)         | O(1)      | Simple, but not optimal                |

✅ **Min-Heap is interview favorite** — clean, efficient, no worst-case surprises.

---

## 💡 STEP 7: Real-World / Interview Relevance

- **Used in**: Leaderboards (top-k players), analytics (top-k products), real-time ranking.
- **Interview pattern**: **Top-K with Heap** — appears in Top K Frequent Elements, K Closest Points.
- **Why companies ask this**: Tests if you know heap vs QuickSelect trade-offs, handle kth element efficiently.

> 💡 Netflix: “Top 10 trending shows.”  
> 💡 Uber: “Top 5 drivers by rating in region.”

---

## 🚫 STEP 8: Common Mistakes / Anti-Patterns

- **Using max-heap** → O(n log n) → not optimal.

```java
// 🚫 WRONG — max-heap
PriorityQueue<Integer> heap = new PriorityQueue<>(Collections.reverseOrder());

// ✅ CORRECT — min-heap of size k
PriorityQueue<Integer> heap = new PriorityQueue<>();
```

- **Not handling duplicates correctly** → problem counts duplicates.

- **Off-by-one in QuickSelect** → kth largest = `n-k` index in sorted array.

---

## ✅ STEP 9: Edge Cases to Test

```java
findKthLargest([3,2,1,5,6,4], 2) → 5
findKthLargest([3,2,3,1,2,4,5,5,6], 4) → 4
findKthLargest([1], 1) → 1
findKthLargest([2,1], 1) → 2
findKthLargest([1,2,3,4,5], 5) → 1
```

---

## 🧠 BONUS: When to Use Which Approach?

| Scenario                        | Best Choice     | Why                                      |
|--------------------------------|-----------------|------------------------------------------|
| Interview, small k             | **Min-Heap**    | Clean, O(n log k), no worst-case         |
| Large n, performance critical  | **QuickSelect** | O(n) avg, but need random pivot          |
| Small n (< 1000)               | **Full Sort**   | Simple, less code                        |

---

## ✅ FINAL CODE — Min-Heap (Interview Favorite)

```java
/**
 * Finds kth largest element using min-heap of size k.
 * O(n log k) time, O(k) space — interview favorite.
 * @param nums array of integers
 * @param k kth largest (1-indexed)
 * @return kth largest element
 */
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    
    for (int num : nums) {
        if (minHeap.size() < k) {
            minHeap.offer(num);
        } else if (num > minHeap.peek()) {
            minHeap.poll();
            minHeap.offer(num);
        }
    }
    
    return minHeap.peek();
}
```

---
