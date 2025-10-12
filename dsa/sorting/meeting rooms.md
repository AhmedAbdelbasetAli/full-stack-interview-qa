---

## 🎯 PROBLEM: Meeting Rooms

> Given an array of meeting time intervals `intervals` where `intervals[i] = [start_i, end_i]`, determine if a person could attend all meetings.

**Examples**:  
- Input: `intervals = [[0,30],[5,10],[15,20]]` → Output: `false` (first and second overlap)  
- Input: `intervals = [[7,10],[2,4]]` → Output: `true`

---

## 🧠 STEP 1: Understand the Problem

- Each meeting has a **start** and **end** time.
- A person can attend all meetings **only if no two meetings overlap**.
- Overlap = `meeting1.end > meeting2.start` (assuming sorted by start).

---

## 🧩 STEP 2: Why Sort + Check Adjacent?

Because:

- If we **sort meetings by start time**, then any overlap must be between **consecutive meetings**.
- For each meeting, check if its start time < previous meeting’s end time.
- If yes → overlap → return `false`.

→ O(n log n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class MeetingRooms {
    public boolean canAttendMeetings(int[][] intervals) {
        // Sort by start time
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        // Check for overlaps
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < intervals[i - 1][1]) {
                return false; // overlap found
            }
        }

        return true;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `intervals = [[0,30],[5,10],[15,20]]`

### Step 1: Sort by start time

```
[[0,30], [5,10], [15,20]]
```

### Step 2: Check overlaps

- i=1: `5 < 30` → overlap → return `false` ✅

Input: `intervals = [[7,10],[2,4]]`

### Step 1: Sort

```
[[2,4], [7,10]]
```

### Step 2: Check

- i=1: `7 < 4`? → false → no overlap → return `true` ✅

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n) — sorting dominates.
- **Space**: O(1) — only comparison.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Calendar apps (Google Calendar), conference room booking, resource scheduling.
- **Interview pattern**: **Sort Intervals + Greedy Check** — appears in Merge Intervals, Non-overlapping Intervals.
- **Why companies ask this**: Tests if you can sort intervals, detect overlaps, handle edge cases.

> 💡 Uber: “Check if driver can accept back-to-back rides.”  
> 💡 Google Calendar: “Detect meeting conflicts.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not sorting** → can’t assume order.

```java
// 🚫 WRONG — assumes input is sorted
for (int i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < intervals[i-1][1]) ...
}
```

- **Using `<=` for end time** → if one meeting ends at 10 and next starts at 10, it’s **not** an overlap.

```java
// ✅ CORRECT — end time is exclusive
if (intervals[i][0] < intervals[i-1][1]) // not <=
```

- **Not handling empty array** → return `true`.

---

## ✅ STEP 8: Edge Cases to Test

```java
canAttendMeetings([[0,30],[5,10],[15,20]]) → false
canAttendMeetings([[7,10],[2,4]]) → true
canAttendMeetings([]) → true
canAttendMeetings([[1,2]]) → true
canAttendMeetings([[1,3],[3,4]]) → true (end == start → no overlap)
canAttendMeetings([[1,4],[2,3]]) → false
```

---

## 🧠 BONUS: Why Sort by Start Time?

Because:

- After sorting, if meeting `i` overlaps with any previous meeting, it **must overlap with meeting `i-1`**.
- This is due to the **greedy choice property** — earliest start first reveals conflicts earliest.

→ This is the **standard pattern** for interval problems.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Determines if a person can attend all meetings (no overlaps).
 * Sorts intervals by start time, then checks adjacent pairs.
 * O(n log n) time, O(1) space.
 * @param intervals array of [start, end] meetings
 * @return true if no overlaps, false otherwise
 */
public boolean canAttendMeetings(int[][] intervals) {
    if (intervals.length <= 1) {
        return true;
    }

    // Sort by start time
    Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

    // Check each meeting against previous
    for (int i = 1; i < intervals.length; i++) {
        // If current start < previous end → overlap
        if (intervals[i][0] < intervals[i - 1][1]) {
            return false;
        }
    }

    return true;
}
```

---

