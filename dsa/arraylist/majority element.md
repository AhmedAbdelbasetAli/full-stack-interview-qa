

---

## 🎯 PROBLEM: Majority Element

> Given an array `nums` of size `n`, return the **majority element** — the element that appears **more than ⌊n/2⌋ times**.  
> You may assume the majority element **always exists**.

**Examples**:  
- `[3,2,3]` → `3` (appears 2 out of 3 times → 2 > 3/2 = 1.5)  
- `[2,2,1,1,1,2,2]` → `2` (appears 4 out of 7 times → 4 > 3.5)

---

## 🧠 STEP 1: Understand the Problem

- Majority = appears **more than half the time**.
- Guaranteed to exist → no need to validate.
- Array can be large → efficiency matters.
- We only need to return the **value**, not count or positions.

---

## 🧩 STEP 2: Brainstorm Approaches

### ✅ Approach 1: HashMap — O(n) time, O(n) space

> “Count frequency of each element → return one with count > n/2.”

- Traverse, count with `HashMap<Integer, Integer>`.
- Traverse again (or during) to find element with count > n/2.

→ Simple, readable, but uses extra space.

---

### ✅ Approach 2: Sort — O(n log n) time, O(1) space

> “If I sort, the majority element must be at the middle!”

- Sort the array.
- Return `nums[n/2]` — because majority appears > n/2 times → must occupy the center.

→ Clever, no extra space, but sorting is O(n log n).

---

### 🌟 Approach 3: Boyer-Moore Voting Algorithm — O(n) time, O(1) space

> “Cancel out each majority element with a non-majority → what’s left must be majority.”

- Maintain a `candidate` and a `count`.
- Traverse:
  - If `count == 0`, set `candidate = current`.
  - If `current == candidate`, `count++`, else `count--`.
- Return `candidate`.

→ **Magical**, O(1) space, one pass. The interview favorite.

---

## ✍️ STEP 3: Code It — Three Clean Java Solutions

### ✅ Solution 1: HashMap

```java
import java.util.HashMap;
import java.util.Map;

public class MajorityElement {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> count = new HashMap<>();

        for (int num : nums) {
            count.put(num, count.getOrDefault(num, 0) + 1);
        }

        int n = nums.length;
        for (Map.Entry<Integer, Integer> entry : count.entrySet()) {
            if (entry.getValue() > n / 2) {
                return entry.getKey();
            }
        }

        return -1; // unreachable (problem guarantees existence)
    }
}
```

---

### ✅ Solution 2: Sorting

```java
import java.util.Arrays;

public class MajorityElement {
    public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length / 2]; // majority must be at middle
    }
}
```

---

### 🌟 Solution 3: Boyer-Moore Voting Algorithm (Recommended)

```java
public class MajorityElement {
    public int majorityElement(int[] nums) {
        int candidate = 0;
        int count = 0;

        for (int num : nums) {
            if (count == 0) {
                candidate = num; // new candidate
            }
            count += (num == candidate) ? 1 : -1;
        }

        return candidate; // guaranteed to be majority
    }
}
```

---

## 🧪 STEP 4: Trace Through Examples

### Example 1: `[3,2,3]`

**Boyer-Moore**:
- num=3: count=0 → candidate=3, count=1
- num=2: 2≠3 → count=0
- num=3: count=0 → candidate=3, count=1
→ Return `3` ✅

### Example 2: `[2,2,1,1,1,2,2]`

**Boyer-Moore**:
- 2: count=0 → candidate=2, count=1
- 2: 2==2 → count=2
- 1: 1≠2 → count=1
- 1: 1≠2 → count=0
- 1: count=0 → candidate=1, count=1
- 2: 2≠1 → count=0
- 2: count=0 → candidate=2, count=1
→ Return `2` ✅

> 💡 Even though candidate changed to `1`, the majority `2` “fought back” and won.

---

## ⏱️ STEP 5: Complexity Analysis

| Approach       | Time          | Space     | Notes                                  |
|----------------|---------------|-----------|----------------------------------------|
| **HashMap**    | O(n)          | O(n)      | Simple, readable                       |
| **Sorting**    | O(n log n)    | O(1)      | Clever, but slower                     |
| **Boyer-Moore**| O(n)          | O(1)      | ✅ Optimal — interview gold            |

✅ **Boyer-Moore is the winner** — optimal time and space.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Distributed systems (majority consensus), voting systems, data aggregation.
- **Interview pattern**: **Boyer-Moore Voting** — appears in “Find Majority Element II” (appears > n/3 times).
- **Why companies ask this**: Tests if you know advanced O(1) space algorithms beyond HashMap.

> 💡 Uber: “Which driver got majority of 5-star ratings?”  
> 💡 Netflix: “Which genre is majority in user’s watch history?”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not handling `count == 0` correctly** → must set new candidate.

```java
// 🚫 WRONG
if (num == candidate) count++;
else count--;

// ✅ CORRECT
if (count == 0) candidate = num;
count += (num == candidate) ? 1 : -1;
```

- **Assuming candidate is always correct without problem guarantee** → if not guaranteed, you must validate in second pass.

- **Using sorting for large arrays** → O(n log n) when O(n) exists.

---

## ✅ STEP 8: Edge Cases to Test

```java
majorityElement([1]) → 1
majorityElement([1,1,2]) → 1
majorityElement([1,2,1,2,1]) → 1
majorityElement([3,3,4,2,4,4,2,4,4]) → 4
```

---

## 🧠 BONUS: Why Boyer-Moore Works

> “Every non-majority element can cancel out at most one majority element. Since majority has > n/2, it survives.”

Think of it as a **battle**:
- Majority soldiers: M
- Others: O
- M > O
- Each O kills one M → but M soldiers left → M wins.

The algorithm simulates this battle in one pass.

---

## ✅ FINAL CODE — Boyer-Moore (Recommended)

```java
/**
 * Finds the majority element (appears more than n/2 times).
 * Uses Boyer-Moore Voting Algorithm — O(n) time, O(1) space.
 * @param nums array of integers (majority element guaranteed)
 * @return the majority element
 */
public int majorityElement(int[] nums) {
    int candidate = 0;
    int count = 0;

    for (int num : nums) {
        if (count == 0) {
            candidate = num;
        }
        count += (num == candidate) ? 1 : -1;
    }

    return candidate;
}
```

---

