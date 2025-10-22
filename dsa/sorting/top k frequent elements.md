---

## 🎯 PROBLEM: Top K Frequent Elements

> Given an integer array `nums` and an integer `k`, return the `k` **most frequent elements**. You may return the answer in any order.

**Examples**:  
- Input: `nums = [1,1,1,2,2,3]`, `k = 2` → Output: `[1,2]`  
- Input: `nums = [1]`, `k = 1` → Output: `[1]`

---

## 🧠 STEP 1: Understand the Problem

- **Goal**: Return `k` elements with **highest frequencies**.
- **Order doesn’t matter** — any order is acceptable.
- **Duplicates in input** → count frequencies.

---

## 🧩 STEP 2: Why Hash Map + Min-Heap?

Because:

1. **Count frequencies** with `HashMap` → O(n).
2. **Use min-heap of size k** to keep top-k frequent elements:
   - If heap size < k → add element.
   - Else if current frequency > heap root → replace root.
3. **Extract elements** from heap.

→ O(n log k) time, O(n) space.

✅ **Optimal for interviews** — clean and efficient.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class TopKFrequent {
    public int[] topKFrequent(int[] nums, int k) {
        // Step 1: Count frequencies
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }

        // Step 2: Min-heap of size k (store numbers, compare by frequency)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(
            (a, b) -> freqMap.get(a) - freqMap.get(b)
        );

        // Step 3: Populate heap
        for (int num : freqMap.keySet()) {
            if (minHeap.size() < k) {
                minHeap.offer(num);
            } else if (freqMap.get(num) > freqMap.get(minHeap.peek())) {
                minHeap.poll();
                minHeap.offer(num);
            }
        }

        // Step 4: Extract result
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = minHeap.poll();
        }

        return result;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `nums = [1,1,1,2,2,3]`, `k = 2`

### Step 1: Frequency Map

```
freqMap = {1:3, 2:2, 3:1}
```

### Step 2: Min-Heap Process

| num | freq | heap (min-heap by freq) | action                     |
|-----|------|--------------------------|----------------------------|
| 1   | 3    | [1]                      | size<2 → add               |
| 2   | 2    | [2,1]                    | size<2 → add (2<3 → min=2) |
| 3   | 1    | [2,1]                    | 1 <= 2 → skip              |

### Step 3: Extract

- Poll → 2, then 1 → result = `[2,1]` (or reverse order — any order is fine)

✅ Output: `[1,2]` or `[2,1]` → both correct.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n + m log k) — n = len(nums), m = unique elements (m ≤ n).
- **Space**: O(m) — for HashMap and heap.

✅ Optimal for this problem.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Trending topics (Twitter), recommendation engines (Netflix), log analysis (error frequency).
- **Interview pattern**: **Top-K with Heap + Hash Map** — appears in Kth Largest Element, Find K Pairs with Smallest Sums.
- **Why companies ask this**: Tests if you can combine HashMap and Heap, handle custom comparators, optimize for k.

> 💡 Twitter: “Top 10 trending hashtags.”  
> 💡 Netflix: “Most watched genres this week.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Using max-heap** → O(m log m) → not optimal.

```java
// 🚫 WRONG — max-heap
PriorityQueue<Integer> heap = new PriorityQueue<>((a,b) -> freqMap.get(b) - freqMap.get(a));

// ✅ CORRECT — min-heap of size k
PriorityQueue<Integer> heap = new PriorityQueue<>((a,b) -> freqMap.get(a) - freqMap.get(b));
```

- **Not using `freqMap.keySet()`** → iterate over map entries efficiently.

- **Forgetting to check heap size before polling** → but we control size, so safe.

---

## ✅ STEP 8: Edge Cases to Test

```java
topKFrequent([1,1,1,2,2,3], 2) → [1,2]
topKFrequent([1], 1) → [1]
topKFrequent([1,2], 2) → [1,2]
topKFrequent([4,1,-1,2,-1,2,3], 2) → [-1,2] (freq 2 each)
topKFrequent([5,3,1,1,1,3,73,1], 2) → [1,3]
```

---

## 🧠 BONUS: Alternative Approach — Bucket Sort (O(n))

Since frequencies are in range `[1, n]`, we can:

1. Create buckets: `bucket[i]` = list of numbers with frequency `i`.
2. Traverse buckets from end to start, collect k elements.

→ O(n) time, O(n) space — but more code.

**Use when**: n is large and k is close to n.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Returns k most frequent elements using min-heap of size k.
 * O(n log k) time, O(n) space — interview favorite.
 * @param nums array of integers
 * @param k number of top frequent elements to return
 * @return array of k most frequent elements
 */
public int[] topKFrequent(int[] nums, int k) {
    // Count frequency of each number
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }

    // Min-heap: smallest frequency at top (size = k)
    PriorityQueue<Integer> minHeap = new PriorityQueue<>(
        (a, b) -> freqMap.get(a) - freqMap.get(b)
    );

    // Keep top k frequent elements in heap
    for (int num : freqMap.keySet()) {
        if (minHeap.size() < k) {
            minHeap.offer(num);
        } else if (freqMap.get(num) > freqMap.get(minHeap.peek())) {
            minHeap.poll();
            minHeap.offer(num);
        }
    }

    // Extract result
    int[] result = new int[k];
    for (int i = 0; i < k; i++) {
        result[i] = minHeap.poll();
    }

    return result;
}
```

---
