--

## 🎯 PROBLEM: Non-overlapping Intervals

> Given an array of intervals `intervals` where `intervals[i] = [start_i, end_i]`, return the **minimum number of intervals you need to remove** to make the rest of the intervals non-overlapping.

**Examples**:  
- Input: `intervals = [[1,2],[2,3],[3,4],[1,3]]` → Output: `1` (remove `[1,3]`)  
- Input: `intervals = [[1,2],[1,2],[1,2]]` → Output: `2` (keep one, remove two)  
- Input: `intervals = [[1,2],[2,3]]` → Output: `0` (no overlap)

---

## 🧠 STEP 1: Understand the Problem

- **Goal**: Maximize the number of **non-overlapping intervals** → minimize removals.
- **Overlap definition**: `[1,3]` and `[2,4]` overlap (3 > 2).  
  `[1,2]` and `[2,3]` **do not overlap** (end == start → acceptable).
- **Key insight**: This is equivalent to **“Activity Selection Problem”** — a classic greedy problem.

---

## 🧩 STEP 2: Why Sort by End Time + Greedy?

Because:

- **Sort intervals by end time** → earliest ending first.
- **Greedy choice**: Always pick the interval that ends earliest — leaves max room for future intervals.
- Count how many intervals we can **keep** → removals = total - kept.

→ O(n log n) time, O(1) space.

✅ **Greedy is optimal** — proved by induction.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class NonOverlappingIntervals {
    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.length == 0) {
            return 0;
        }

        // Sort by end time
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));

        int kept = 1; // First interval is always kept
        int lastEnd = intervals[0][1];

        for (int i = 1; i < intervals.length; i++) {
            // If current start >= last end → no overlap
            if (intervals[i][0] >= lastEnd) {
                kept++;
                lastEnd = intervals[i][1];
            }
        }

        return intervals.length - kept; // removals = total - kept
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `intervals = [[1,2],[2,3],[3,4],[1,3]]`

### Step 1: Sort by end time

```
[[1,2], [2,3], [1,3], [3,4]] → sorted by end: [[1,2], [2,3], [1,3], [3,4]]
```

### Step 2: Greedy selection

| i  | interval | start | lastEnd | start >= lastEnd? | Action     | kept |
|----|----------|-------|---------|-------------------|------------|------|
| 0  | [1,2]    | —     | —       | —                 | keep       | 1    |
| 1  | [2,3]    | 2     | 2       | 2 >= 2 → yes      | keep       | 2    |
| 2  | [1,3]    | 1     | 3       | 1 >= 3? no        | skip       | 2    |
| 3  | [3,4]    | 3     | 3       | 3 >= 3 → yes      | keep       | 3    |

→ kept = 3 → removals = 4 - 3 = **1** ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n) — sorting dominates.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Calendar systems (Google Calendar), conference room booking, CPU scheduling.
- **Interview pattern**: **Greedy Interval Scheduling** — appears in Meeting Rooms II, Minimum Number of Arrows to Burst Balloons.
- **Why companies ask this**: Tests if you know classic greedy problems, sort by end time, prove optimality.

> 💡 Google Calendar: “Minimize meeting conflicts by removing overlapping events.”  
> 💡 Uber: “Maximize non-overlapping ride assignments for a driver.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Sorting by start time** → not optimal.

```java
// 🚫 WRONG — sort by start
Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

// ✅ CORRECT — sort by end
Arrays.sort(intervals, (a, b) -> a[1] - b[1]);
```

- **Counting removals directly** → harder than counting kept.

- **Using `>` instead of `>=`** → misses non-overlapping at boundaries.

```java
// 🚫 WRONG — [1,2] and [2,3] would be considered overlapping
if (intervals[i][0] > lastEnd)

// ✅ CORRECT — end == start is allowed
if (intervals[i][0] >= lastEnd)
```

---

## ✅ STEP 8: Edge Cases to Test

```java
eraseOverlapIntervals([[1,2],[2,3],[3,4],[1,3]]) → 1
eraseOverlapIntervals([[1,2],[1,2],[1,2]]) → 2
eraseOverlapIntervals([[1,2],[2,3]]) → 0
eraseOverlapIntervals([]) → 0
eraseOverlapIntervals([[1,100],[11,22],[1,11],[2,12]]) → 2
```

---

## 🧠 BONUS: Why Sort by End Time?

Because:

- The interval that **ends earliest** leaves the **most room** for future intervals.
- This is the **optimal greedy choice** — proven by the fact that any solution can be transformed to use the earliest-ending interval without reducing the count.

✅ This is the **standard solution** for activity selection.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds minimum number of intervals to remove to make rest non-overlapping.
 * Sorts by end time, uses greedy selection — O(n log n) time, O(1) space.
 * @param intervals array of [start, end] intervals
 * @return minimum removals
 */
public int eraseOverlapIntervals(int[][] intervals) {
    if (intervals.length == 0) {
        return 0;
    }

    // Sort by end time — greedy choice: earliest end first
    Arrays.sort(intervals, Comparator.comparingInt(a -> a[1]));

    int kept = 1;
    int lastEnd = intervals[0][1];

    // Keep interval if it starts after last kept ends
    for (int i = 1; i < intervals.length; i++) {
        if (intervals[i][0] >= lastEnd) {
            kept++;
            lastEnd = intervals[i][1];
        }
    }

    return intervals.length - kept;
}
```

---
