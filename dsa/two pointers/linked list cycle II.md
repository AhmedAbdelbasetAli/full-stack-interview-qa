

---

## 🎯 PROBLEM: Linked List Cycle II

> Given the `head` of a linked list, return the node where the cycle begins. If there is no cycle, return `null`.  
> There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer.

**Example**:  
Input: `head = [3,2,0,-4]`, `pos = 1` (tail connects to index 1)  
Output: node with value `2`

---

## 🧠 STEP 1: Understand the Problem

- First, **detect if there’s a cycle** (like #141).
- If cycle exists, **find the starting node** of the cycle.
- If no cycle, return `null`.
- Must do in **O(1) space** — no HashSet.

---

## 🧩 STEP 2: Why Fast/Slow + Reset?

Because:

1. **Phase 1**: Use fast/slow to detect cycle (if they meet, cycle exists).
2. **Phase 2**: Reset one pointer to head, move both at 1x — they meet at cycle start.

→ This is **Floyd’s Cycle Detection Algorithm** — proven mathematically.

---

## ✍️ STEP 3: Code It — Clean Java

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

public class LinkedListCycleII {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode slow = head;
        ListNode fast = head;

        // Phase 1: Detect cycle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                break; // Cycle detected
            }
        }

        // If no cycle
        if (fast == null || fast.next == null) {
            return null;
        }

        // Phase 2: Find start of cycle
        slow = head; // Reset slow to head
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }

        return slow; // Start of cycle
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `3 → 2 → 0 → -4 → 2 (cycle)`

### Phase 1: Detect Cycle

```
Step 0: slow=3, fast=3
Step 1: slow=2, fast=0
Step 2: slow=0, fast=2
Step 3: slow=-4, fast=-4 → meet → cycle detected
```

### Phase 2: Find Start

```
Reset slow to head: slow=3, fast=-4
Step 1: slow=2, fast=2 → meet → return node with value 2 ✅
```

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — each pointer moves at most 2n steps.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Garbage collection (find cycle root), network loop detection, debugging.
- **Interview pattern**: **Fast/Slow Pointers + Reset** — appears in Find Duplicate Number (array version).
- **Why companies ask this**: Tests if you know Floyd’s algorithm, handle pointers, prove math.

> 💡 Uber: “Find root of cyclic dependency in microservices.”  
> 💡 Android: “Find root of circular reference in view hierarchy.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not checking `fast.next` in Phase 1** → NPE.

- **Resetting fast instead of slow** → must reset slow to head.

- **Not handling no-cycle case** → after Phase 1, check if fast reached end.

- **Moving pointers at different speeds in Phase 2** → both must move 1x.

---

## ✅ STEP 8: Edge Cases to Test

```java
detectCycle([3,2,0,-4] with cycle at 2) → node 2
detectCycle([1,2] with no cycle) → null
detectCycle([1] with self-cycle) → node 1
detectCycle([]) → null
detectCycle([1,2,3,4,5,6,7,8,9,10] with cycle at 5) → node 5
```

---

## 🧠 BONUS: Why Reset Works — Mathematical Proof

Let:
- Distance from head to cycle start = `F`
- Distance from cycle start to meeting point = `a`
- Cycle length = `C`

When slow and fast meet:
- Slow traveled = `F + a`
- Fast traveled = `F + a + k*C` (k = number of laps)

But fast = 2 * slow → `F + a + k*C = 2*(F + a)`  
→ `F + a + k*C = 2F + 2a`  
→ `F = k*C - a`

Now, reset slow to head, move both 1x:
- Slow travels `F` to reach cycle start.
- Fast travels `F` from meeting point → `a + F = a + (k*C - a) = k*C` → back to cycle start.

→ They meet at cycle start.

✅ Proven.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds start of cycle in linked list using Floyd's algorithm.
 * Phase 1: Detect cycle with fast/slow pointers.
 * Phase 2: Reset slow to head, move both 1x — meet at cycle start.
 * O(n) time, O(1) space.
 * @param head head of linked list
 * @return start of cycle, or null if no cycle
 */
public ListNode detectCycle(ListNode head) {
    if (head == null) {
        return null;
    }

    ListNode slow = head;
    ListNode fast = head;

    // Phase 1: Detect if cycle exists
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            break; // Cycle detected
        }
    }

    // If no cycle
    if (fast == null || fast.next == null) {
        return null;
    }

    // Phase 2: Find start of cycle
    slow = head; // Reset slow to head
    while (slow != fast) {
        slow = slow.next;
        fast = fast.next;
    }

    return slow; // Start of cycle
}
```

---

