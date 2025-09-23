

---

## 🎯 PROBLEM: Linked List Cycle

> Given `head`, the head of a linked list, determine if the linked list has a cycle in it.  
> There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer.  
> Return `true` if there is a cycle, `false` otherwise.

**Example**:  
Input: `head = [3,2,0,-4]`, `pos = 1` (tail connects to index 1)  
Output: `true`

---

## 🧠 STEP 1: Understand the Problem

- Linked list — each node has `val` and `next`.
- Cycle = some node’s `next` points to a previous node.
- No cycle = `next` eventually becomes `null`.
- Must detect without extra space (no HashSet).

---

## 🧩 STEP 2: Why Fast/Slow (Tortoise & Hare)?

Because:

- **Fast pointer** moves 2 steps at a time.
- **Slow pointer** moves 1 step at a time.
- If there’s a cycle, fast will eventually “lap” slow → they meet.
- If no cycle, fast will reach `null`.

→ O(n) time, O(1) space.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class LinkedListCycle {
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;        // 1x speed
            fast = fast.next.next;   // 2x speed
            if (slow == fast) {      // meet → cycle!
                return true;
            }
        }

        return false; // fast reached end → no cycle
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `3 → 2 → 0 → -4 → 2 (cycle)`

### Step 0: Initialize

```
slow = 3, fast = 3
```

### Step 1: Move

```
slow = 2, fast = 0
```

### Step 2: Move

```
slow = 0, fast = 2
```

### Step 3: Move

```
slow = -4, fast = -4 → slow == fast → return true ✅
```

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — fast visits at most 2n nodes.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Garbage collection (detect reference cycles), network loop detection, debugging.
- **Interview pattern**: **Fast/Slow Pointers** — appears in Middle of List, Palindrome Linked List.
- **Why companies ask this**: Tests if you know Floyd’s Cycle Detection, handle pointers, avoid extra space.

> 💡 Uber: “Detect cyclic dependencies in ride assignment graph.”  
> 💡 Android: “Detect circular references in view hierarchy.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not checking `fast.next`** → NPE.

```java
// 🚫 WRONG
while (fast != null) {
    slow = slow.next;
    fast = fast.next.next; // if fast.next is null → crash!
}

// ✅ CORRECT
while (fast != null && fast.next != null) {
    ...
}
```

- **Starting fast ahead of slow** → not necessary — start both at head.

- **Not handling null head** → always check.

---

## ✅ STEP 8: Edge Cases to Test

```java
hasCycle([3,2,0,-4] with cycle) → true
hasCycle([1,2] with no cycle) → false
hasCycle([1] with no cycle) → false
hasCycle([1] with self-cycle) → true
hasCycle([]) → false
```

---

## 🧠 BONUS: Why Fast/Slow Works

### 🐢 Tortoise and Hare Analogy

- Tortoise (slow) and Hare (fast) start together.
- If there’s a cycle, hare will eventually catch up to tortoise inside the cycle.
- If no cycle, hare reaches end first.

### 🧮 Mathematical Proof

Let:
- Distance from start to cycle start = `F`
- Cycle length = `C`
- When slow enters cycle, fast is at position `(2F - F) % C = F % C` in cycle.
- Relative speed = 1 → slow will catch up in at most `C` steps.

→ Guaranteed to meet if cycle exists.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Detects if linked list has a cycle using Floyd's Tortoise and Hare algorithm.
 * O(n) time, O(1) space.
 * @param head head of linked list
 * @return true if cycle exists, false otherwise
 */
public boolean hasCycle(ListNode head) {
    if (head == null) {
        return false;
    }

    ListNode slow = head;   // Tortoise
    ListNode fast = head;   // Hare

    // Hare moves 2x, Tortoise 1x — if they meet, cycle exists
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return true; // Cycle detected
        }
    }

    return false; // No cycle — hare reached end
}
```

---

