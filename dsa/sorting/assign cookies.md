---

## 🎯 PROBLEM: Assign Cookies

> Assume you are an awesome parent and want to give your children some cookies. But, you should give each child **at most one cookie**.  
> Each child `i` has a **greed factor** `g[i]`, which is the minimum size of a cookie that the child will be content with.  
> Each cookie `j` has a **size** `s[j]`.  
> If `s[j] >= g[i]`, we can assign the cookie `j` to child `i`, and the child `i` will be content.  
> Your goal is to **maximize the number of content children** and output that maximum number.

**Examples**:  
- Input: `g = [1,2,3]`, `s = [1,1]` → Output: `1` (only child 0 can be content)  
- Input: `g = [1,2]`, `s = [1,2,3]` → Output: `2` (both children content)

---

## 🧠 STEP 1: Understand the Problem

- **Goal**: Maximize number of children who get a cookie that satisfies their greed.
- **Constraints**:
  - One cookie per child.
  - One child per cookie.
- **Strategy**: Assign smallest sufficient cookie to each child — **greedy choice**.

---

## 🧩 STEP 2: Why Sort + Two Pointers (Greedy)?

Because:

- **Sort children by greed** → handle least greedy first.
- **Sort cookies by size** → use smallest sufficient cookie.
- Use **two pointers**:
  - `child` → current child
  - `cookie` → current cookie
- If `s[cookie] >= g[child]` → assign cookie, increment both.
- Else → cookie too small, try next cookie.

→ O(n log n + m log m) time, O(1) space.

✅ **Greedy is optimal** — proved by exchange argument.

---

## ✍️ STEP 3: Code It — Clean Java

```java
import java.util.*;

public class AssignCookies {
    public int findContentChildren(int[] g, int[] s) {
        // Sort children by greed, cookies by size
        Arrays.sort(g);
        Arrays.sort(s);

        int child = 0;   // pointer for children
        int cookie = 0;  // pointer for cookies

        // Assign cookies greedily
        while (child < g.length && cookie < s.length) {
            if (s[cookie] >= g[child]) {
                // Cookie satisfies child → assign
                child++;
            }
            cookie++; // Move to next cookie (assigned or too small)
        }

        return child; // Number of content children
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `g = [1,2,3]`, `s = [1,1]`

### Step 1: Sort

```
g = [1,2,3], s = [1,1]
```

### Step 2: Assign

| child | cookie | s[cookie] | g[child] | s >= g? | Action          | Content |
|-------|--------|-----------|----------|---------|-----------------|---------|
| 0     | 0      | 1         | 1        | yes     | child++, cookie++ | 1       |
| 1     | 1      | 1         | 2        | no      | cookie++        | 1       |
| 1     | 2      | —         | —        | —       | break           | ✅ 1    |

✅ Output: `1`

Input: `g = [1,2]`, `s = [1,2,3]`

| child | cookie | s[cookie] | g[child] | s >= g? | Action          | Content |
|-------|--------|-----------|----------|---------|-----------------|---------|
| 0     | 0      | 1         | 1        | yes     | child++, cookie++ | 1       |
| 1     | 1      | 2         | 2        | yes     | child++, cookie++ | 2       |
| 2     | 2      | —         | —        | —       | break           | ✅ 2    |

✅ Output: `2`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n log n + m log m) — sorting dominates.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Resource allocation (CPU, memory), ad targeting (assign ad to user), load balancing (assign task to worker).
- **Interview pattern**: **Greedy + Sorting for Maximization** — appears in Non-overlapping Intervals, Queue Reconstruction by Height.
- **Why companies ask this**: Tests if you can prove greedy choice is optimal, sort inputs, use two pointers.

> 💡 Uber: “Assign drivers to rides based on min car size.”  
> 💡 AWS: “Assign EC2 instances to tasks based on min CPU.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not sorting** → greedy fails.

```java
// 🚫 WRONG — unsorted
if (s[cookie] >= g[child]) ...
```

- **Assigning largest cookie to child** → wastes resources.

- **Using nested loops** → O(n*m) → too slow.

```java
// 🚫 WRONG — O(n*m)
for (int i=0; i<g.length; i++) {
    for (int j=0; j<s.length; j++) {
        if (s[j] >= g[i]) { ... }
    }
}
```

---

## ✅ STEP 8: Edge Cases to Test

```java
findContentChildren([1,2,3], [1,1]) → 1
findContentChildren([1,2], [1,2,3]) → 2
findContentChildren([1,2,3], []) → 0
findContentChildren([], [1,2,3]) → 0
findContentChildren([10,9,8,7], [5,6,7,8]) → 2 ([7→7, 8→8])
```

---

## 🧠 BONUS: Why Greedy Works

Because:

- Assigning the **smallest sufficient cookie** to the **least greedy child** leaves larger cookies for greedier children.
- This **maximizes total assignments** — no better strategy exists.

✅ **Exchange argument**: Any optimal solution can be transformed into greedy solution without loss.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Maximizes number of content children by assigning cookies greedily.
 * Sorts both arrays, uses two pointers — O(n log n) time, O(1) space.
 * @param g greed factors of children
 * @param s sizes of cookies
 * @return maximum number of content children
 */
public int findContentChildren(int[] g, int[] s) {
    Arrays.sort(g); // Sort children by greed
    Arrays.sort(s); // Sort cookies by size

    int child = 0;
    int cookie = 0;

    // Greedily assign smallest sufficient cookie to each child
    while (child < g.length && cookie < s.length) {
        if (s[cookie] >= g[child]) {
            child++; // Child is content
        }
        cookie++; // Move to next cookie (assigned or too small)
    }

    return child;
}
```

---

