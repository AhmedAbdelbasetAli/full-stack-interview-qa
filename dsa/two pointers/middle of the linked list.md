

---

## 🎯 PROBLEM: Middle of the Linked List

> Given the `head` of a singly linked list, return the **middle node**.  
> If there are two middle nodes, return the **second middle node**.

**Examples**:  
- Input: `[1,2,3,4,5]` → Output: `[3,4,5]` (node with value 3)  
- Input: `[1,2,3,4,5,6]` → Output: `[4,5,6]` (node with value 4 — second middle)

---

## 🧠 STEP 1: Understand the Problem

- Linked list — no random access.
- Need to find middle node — without knowing length.
- If even number of nodes → return **second** of two middles.
- Must do in **one pass** — no counting then traversing again.

---

## 🧩 STEP 2: Why Fast/Slow (Tortoise & Hare)?

Because:

- **Fast pointer** moves 2 steps at a time.
- **Slow pointer** moves 1 step at a time.
- When fast reaches end, slow is at middle.
- Works for both odd and even lengths.

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

public class MiddleOfLinkedList {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;        // 1x speed
            fast = fast.next.next;   // 2x speed
        }

        return slow; // When fast ends, slow is at middle
    }
}
```

---

## 🧪 STEP 4: Trace Through Examples

### Example 1: `[1,2,3,4,5]` (odd)

```
Step 0: slow=1, fast=1
Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: fast.next = null → break → return slow=3 ✅
```

### Example 2: `[1,2,3,4,5,6]` (even)

```
Step 0: slow=1, fast=1
Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: slow=4, fast=null → break → return slow=4 ✅
```

→ For even length, slow ends at **second middle** — perfect.

---

## ⏱️ STEP 5: Complexity Analysis

- **Time**: O(n) — fast visits n nodes, slow visits n/2.
- **Space**: O(1) — only two pointers.

✅ Optimal.

---

## 💡 STEP 6: Real-World / Interview Relevance

- **Used in**: Data structure libraries, game development (midpoint in path), debugging.
- **Interview pattern**: **Fast/Slow Pointers** — appears in Linked List Cycle, Palindrome Linked List.
- **Why companies ask this**: Tests if you can find middle without extra space, handle pointers, understand pointer speeds.

> 💡 Uber: “Find midpoint in route for driver handoff.”  
> 💡 Game Dev: “Find middle of path for NPC spawn point.”

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

- **Returning fast** → no, return slow.

- **Not handling single node** → slow=fast=head → returns head → correct.

---

## ✅ STEP 8: Edge Cases to Test

```java
middleNode([1,2,3,4,5]) → [3,4,5]
middleNode([1,2,3,4,5,6]) → [4,5,6]
middleNode([1]) → [1]
middleNode([1,2]) → [2]
middleNode([1,2,3]) → [2,3]
```

---

## 🧠 BONUS: Why Fast/Slow Works

### 🐢 Tortoise and Hare Analogy

- Tortoise (slow) and Hare (fast) start together.
- Hare moves twice as fast.
- When hare reaches end, tortoise has gone half the distance → at middle.

### 🧮 Mathematical Proof

Let:
- Total nodes = `n`
- Slow moves 1 step per iteration → after `k` steps, at position `k`.
- Fast moves 2 steps per iteration → after `k` steps, at position `2k`.
- When fast reaches end: `2k = n` (if even) or `2k = n-1` (if odd) → `k = n/2` or `(n-1)/2`.
- Slow is at position `k` → which is the middle (or first middle).

But wait — for even `n`, we want **second middle** → and our code returns `k = n/2` → which is the second middle (0-indexed).

✅ Perfect.

---

## ✅ FINAL CODE (WITH COMMENTS)

```java
/**
 * Finds middle node of linked list using fast/slow pointers.
 * For even length, returns second middle node.
 * O(n) time, O(1) space.
 * @param head head of linked list
 * @return middle node
 */
public ListNode middleNode(ListNode head) {
    ListNode slow = head;   // Tortoise — 1x speed
    ListNode fast = head;   // Hare — 2x speed

    // When hare reaches end, tortoise is at middle
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    return slow;
}
```

---

