---

## 🎯 PROBLEM: Remove Nth Node From End of List

> Given the `head` of a linked list, remove the `n-th` node from the end of the list and return its head.

**Examples**:  
- Input: `head = [1,2,3,4,5]`, `n = 2` → Output: `[1,2,3,5]`  
- Input: `head = [1]`, `n = 1` → Output: `[]`  
- Input: `head = [1,2]`, `n = 1` → Output: `[1]`

---

## 🧠 STEP 1: Understand the Problem

- Remove the `n-th` node **from the end**.
- Must do in **one pass** — no counting then traversing again.
- Handle edge cases: remove head, single node, etc.

---

## 🧩 STEP 2: Why Fast/Slow with Lead?

Because:

- **Fast pointer** moves `n` steps ahead first.
- Then **both move together** until fast reaches end.
- When fast is at end, slow is at node **before** the one to remove.
- Use a **dummy head** to handle removal of head node.

→ O(n) time, O(1) space.

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

public class RemoveNthFromEnd {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // Use dummy node to handle head removal
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode slow = dummy;
        ListNode fast = dummy;

        // Move fast n+1 steps ahead (so slow stops before target)
        for (int i = 0; i <= n; i++) {
            fast = fast.next;
        }

        // Move both until fast reaches end
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }

        // Remove nth node from end
        slow.next = slow.next.next;

        return dummy.next;
    }
}
```

---

## 🧪 STEP 4: Trace Through Example

Input: `head = [1,2,3,4,5]`, `n = 2`

### Step 0: Initialize

```
dummy → 1 → 2 → 3 → 4 → 5 → null
slow = dummy, fast = dummy
```

### Step 1: Move fast `n+1 = 3` steps

```
fast = 1 → 2 → 3 → (after 3 steps)
```

### Step 2: Move both until fast = null

```
slow=1, fast=4
slow=2, fast=5
slow=3, fast=null → break
```

### Step 3: Remove

```
slow.next = slow.next.next → 3 → 5
```

✅ Output: `[1,2,3,5]`

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — fast moves n+1 + (n-n-1) = n steps.
- **Space**: O(1) — only pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Log trimming (remove last N entries), cache eviction, undo/redo stacks.
- **Interview pattern**: **Fast/Slow with Lead Distance** — appears in Middle of List, Linked List Cycle.
- **Why companies ask this**: Tests if you can handle “from end” without extra space, use dummy node, manage pointers.

> 💡 Uber: “Remove last N ride records for privacy.”  
> 💡 Android: “Trim undo stack to last 10 actions.”

---

## 🚫 STEP 7: Common Mistakes / Anti-Patterns

- **Not using dummy node** → can’t remove head node easily.

```java
// 🚫 WRONG — fails when removing head
if (n == length) return head.next;

// ✅ CORRECT — use dummy node
ListNode dummy = new ListNode(0);
dummy.next = head;
```

- **Moving fast only `n` steps** → slow ends at target, not before.

```java
// 🚫 WRONG
for (int i = 0; i < n; i++) fast = fast.next;

// ✅ CORRECT — move n+1 steps so slow stops BEFORE target
for (int i = 0; i <= n; i++) fast = fast.next;
```

- **Not handling single node** → dummy node handles it.

---

## ✅ STEP 8: Edge Cases to Test

```java
removeNthFromEnd([1,2,3,4,5], 2) → [1,2,3,5]
removeNthFromEnd([1], 1) → []
removeNthFromEnd([1,2], 1) → [1]
removeNthFromEnd([1,2], 2) → [2]
removeNthFromEnd([1,2,3], 3) → [2,3]
```

---

## 🧠 BONUS: Why n+1 Steps?

Because:

- We want `slow` to stop at the node **before** the one to remove.
- If fast is `n+1` steps ahead, when fast reaches `null`, slow is at `length - n - 1` → which is the node before the target.

→ Perfect for `slow.next = slow.next.next`.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Removes nth node from end of linked list using fast/slow pointers.
 * Uses dummy node to handle head removal.
 * O(n) time, O(1) space.
 * @param head head of linked list
 * @param n position from end (1-indexed)
 * @return new head of list
 */
public ListNode removeNthFromEnd(ListNode head, int n) {
    // Dummy node handles edge case of removing head
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode slow = dummy;
    ListNode fast = dummy;

    // Move fast n+1 steps ahead so slow stops before target
    for (int i = 0; i <= n; i++) {
        fast = fast.next;
    }

    // Move both until fast reaches end
    while (fast != null) {
        slow = slow.next;
        fast = fast.next;
    }

    // Remove nth node from end
    slow.next = slow.next.next;

    return dummy.next;
}
```

---

