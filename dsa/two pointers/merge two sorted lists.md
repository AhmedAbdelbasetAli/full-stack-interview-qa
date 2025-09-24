

---

## 🎯 PROBLEM: Merge Two Sorted Lists

> You are given the heads of two sorted linked lists `list1` and `list2`.  
> Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.  
> Return the head of the merged list.

**Examples**:  
- Input: `list1 = [1,2,4]`, `list2 = [1,3,4]` → Output: `[1,1,2,3,4,4]`  
- Input: `list1 = []`, `list2 = []` → Output: `[]`  
- Input: `list1 = []`, `list2 = [0]` → Output: `[0]`

---

## 🧠 STEP 1: Understand the Problem

- Both lists are **sorted in non-decreasing order**.
- Merge them into **one sorted list**.
- **Splice nodes** — don’t create new nodes.
- Handle empty lists.

---

## 🧩 STEP 2: Why Two Pointers (Parallel)?

Because:

- Each list is sorted → we can compare heads and pick the smaller one.
- Use two pointers — one for each list.
- Build result by linking smaller node.
- Use a **dummy head** to simplify edge cases.

→ O(m + n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}

public class MergeTwoSortedLists {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // Dummy node to simplify head handling
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;

        // Two pointers — traverse both lists
        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1;
                list1 = list1.next;
            } else {
                current.next = list2;
                list2 = list2.next;
            }
            current = current.next;
        }

        // Attach remaining nodes (one list is exhausted)
        current.next = (list1 != null) ? list1 : list2;

        return dummy.next;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `list1 = [1,2,4]`, `list2 = [1,3,4]`

### Step 0: Initialize

```
dummy → null
current = dummy
list1 = 1 → 2 → 4
list2 = 1 → 3 → 4
```

### Step 1: Compare 1 vs 1 → pick list1

```
current → 1 (from list1)
list1 = 2 → 4
```

### Step 2: Compare 2 vs 1 → pick list2

```
current → 1 → 1 (from list2)
list2 = 3 → 4
```

### Step 3: Compare 2 vs 3 → pick list1

```
current → 1 → 1 → 2
list1 = 4
```

### Step 4: Compare 4 vs 3 → pick list2

```
current → 1 → 1 → 2 → 3
list2 = 4
```

### Step 5: Compare 4 vs 4 → pick list1

```
current → 1 → 1 → 2 → 3 → 4
list1 = null
```

### Step 6: Attach remaining

```
current.next = list2 → 4
Final: 1 → 1 → 2 → 3 → 4 → 4 ✅
```

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(m + n) — visit each node once.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Merge Sort, external sorting (large datasets), k-way merge in databases.
- **Interview pattern**: **Two Pointers (Parallel)** — appears in Merge Intervals, Merge k Sorted Lists.
- **Why companies ask this**: Tests if you can merge sorted data, handle pointers, use dummy node.

> 💡 Uber: “Merge sorted ride logs from two regions.”  
> 💡 Database: “Merge sorted index segments during compaction.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not using dummy node** → complex head handling.

```java
// 🚫 WRONG — messy head logic
if (list1.val <= list2.val) {
    head = list1;
    list1 = list1.next;
} else {
    head = list2;
    list2 = list2.next;
}
// ... then continue
```

- **Creating new nodes** → violates “splice” requirement.

```java
// 🚫 WRONG
current.next = new ListNode(list1.val);
```

- **Not attaching remaining nodes** → lose tail of longer list.

---

## ✅ STEP 8: Edge Cases to Test

```java
mergeTwoLists([1,2,4], [1,3,4]) → [1,1,2,3,4,4]
mergeTwoLists([], []) → []
mergeTwoLists([], [0]) → [0]
mergeTwoLists([1], [2]) → [1,2]
mergeTwoLists([5], [1,2,4]) → [1,2,4,5]
```

---

## 🧠 BONUS: Why This Works

- Both lists are sorted → the smallest remaining element is always at one of the heads.
- By always picking the smaller head, we build the result in sorted order.
- The dummy node eliminates special cases for the first node.

→ This is the **core merge step** of Merge Sort.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Merges two sorted linked lists into one sorted list.
 * Uses two pointers and dummy head — O(m+n) time, O(1) space.
 * @param list1 head of first sorted list
 * @param list2 head of second sorted list
 * @return head of merged sorted list
 */
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    // Dummy node simplifies head handling
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;

    // Compare heads and link smaller node
    while (list1 != null && list2 != null) {
        if (list1.val <= list2.val) {
            current.next = list1;
            list1 = list1.next;
        } else {
            current.next = list2;
            list2 = list2.next;
        }
        current = current.next;
    }

    // Attach remaining nodes from non-empty list
    current.next = (list1 != null) ? list1 : list2;

    return dummy.next;
}
```

---



What’s next?
