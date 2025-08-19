# 🔗 Java Linked List Algorithms  
**Reverse, Cycle Detection, Middle, Removal & Merge**

Here are **5 essential linked list problems** with **Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java** and includes a `ListNode` class for clarity.

---

## 1. How do you reverse a singly linked list?

> Reverse the list **in-place** so that the last node becomes the first.

### ✅ Approach: Iterative with Three Pointers

- Use `prev`, `current`, and `next` pointers
- Traverse the list, reversing the `next` pointer of each node
- Return `prev` (which becomes the new head)

### ✅ Java Code
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public static ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode current = head;

    while (current != null) {
        ListNode next = current.next;  // Store next
        current.next = prev;           // Reverse link
        prev = current;                // Move prev
        current = next;                // Move current
    }

    return prev; // New head
}

// Example usage
public static void main(String[] args) {
    // 1 -> 2 -> 3 -> null
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);

    ListNode reversed = reverseList(head);
    printList(reversed); // 3 2 1
}

public static void printList(ListNode head) {
    ListNode current = head;
    while (current != null) {
        System.out.print(current.val + " ");
        current = current.next;
    }
    System.out.println();
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use three pointers: prev, current, next. I reverse the link at each step. This is O(1) space and O(n) time. I handle edge cases: null or single node."_  

---

## 2. How do you detect a cycle in a linked list?

> Use **Floyd’s Cycle Detection Algorithm** (Tortoise and Hare).

### ✅ Approach
- Use two pointers: `slow` (1 step), `fast` (2 steps)
- If they meet → cycle exists
- If `fast` reaches `null` → no cycle

### ✅ Java Code
```java
public static boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) return false;

    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow == fast) {
            return true; // Cycle detected
        }
    }

    return false;
}

// Example usage
public static void main(String[] args) {
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);
    head.next.next.next = head; // Cycle to head

    System.out.println(hasCycle(head)); // true
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use Floyd’s algorithm: slow and fast pointers. If they meet, there’s a cycle. It’s O(1) space and elegant. I always check for null pointers to avoid NPE."_  

---

## 3. Find the middle of a linked list in one pass.

> Use **two pointers**: one moves twice as fast.

When the fast pointer reaches the end, the slow pointer is at the middle.

### ✅ Java Code
```java
public static ListNode findMiddle(ListNode head) {
    if (head == null) return null;

    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    return slow; // Middle node
}

// Example usage
public static void main(String[] args) {
    // 1 -> 2 -> 3 -> 4 -> 5
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);
    head.next.next.next = new ListNode(4);
    head.next.next.next.next = new ListNode(5);

    ListNode mid = findMiddle(head);
    System.out.println(mid.val); // 3
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use the two-pointer technique: slow and fast. Fast moves two steps, slow one. When fast hits end, slow is at middle. For even length, it returns the second middle node — which is standard."_  

---

## 4. Remove a node from a linked list by value.

> Remove **all nodes** with the given value.

Handle cases where head might be removed.

### ✅ Approach
- Use a **dummy node** to simplify head removal
- Traverse with `prev` and `current`
- If `current.val == val`, skip it by `prev.next = current.next`

### ✅ Java Code
```java
public static ListNode removeElements(ListNode head, int val) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    ListNode current = head;

    while (current != null) {
        if (current.val == val) {
            prev.next = current.next;
        } else {
            prev = current;
        }
        current = current.next;
    }

    return dummy.next; // New head
}

// Example usage
public static void main(String[] args) {
    // 1 -> 2 -> 6 -> 3 -> 4 -> 5 -> 6
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(6);
    head.next.next.next = new ListNode(3);
    head.next.next.next.next = new ListNode(4);
    head.next.next.next.next.next = new ListNode(5);
    head.next.next.next.next.next.next = new ListNode(6);

    ListNode newHead = removeElements(head, 6);
    printList(newHead); // 1 2 3 4 5
}
```

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use a dummy node to handle head removal uniformly. I traverse with prev and current, skipping nodes with the target value. This avoids special cases and keeps code clean."_  

---

## 5. How do you merge two sorted linked lists?

> Merge two sorted lists into one sorted list.

### ✅ Approach
- Use a **dummy node** and `current` pointer
- Compare values of both lists, append smaller one
- Advance the chosen list
- Append remaining nodes

### ✅ Java Code
```java
public static ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;

    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            current.next = l1;
            l1 = l1.next;
        } else {
            current.next = l2;
            l2 = l2.next;
        }
        current = current.next;
    }

    // Append remaining
    if (l1 != null) {
        current.next = l1;
    } else {
        current.next = l2;
    }

    return dummy.next;
}

// Example usage
public static void main(String[] args) {
    // List 1: 1 -> 2 -> 4
    ListNode l1 = new ListNode(1);
    l1.next = new ListNode(2);
    l1.next.next = new ListNode(4);

    // List 2: 1 -> 3 -> 4
    ListNode l2 = new ListNode(1);
    l2.next = new ListNode(3);
    l2.next.next = new ListNode(4);

    ListNode merged = mergeTwoLists(l1, l2);
    printList(merged); // 1 1 2 3 4 4
}
```

### 📌 Time & Space
- **Time**: O(m + n)
- **Space**: O(1) — only pointers, no new nodes

### 💡 Interview Tip
> _"I use a dummy node and compare values from both lists, appending the smaller one. After one list ends, I append the rest of the other. It’s O(m + n) time and modifies the list in-place."_  
# 🔗 Advanced Linked List Algorithms  
**Singly vs Doubly, K-th from End, and Palindrome Check**

This document covers **3 advanced linked list problems** with **Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java** and includes clean, reusable classes.

---

## 1. What is the difference between a singly and doubly linked list?

| Feature | Singly Linked List | Doubly Linked List |
|--------|---------------------|---------------------|
| **Node Structure** | `val` + `next` | `val` + `next` + `prev` |
| **Traversal** | Forward only | Forward and backward |
| **Memory** | Less (one pointer per node) | More (two pointers) |
| **Insertion/Deletion** | Need previous node | Can delete with only current node |
| **Use Case** | Simpler, memory-efficient | When bidirectional access is needed |

---

### 🔹 1.1 Singly Linked List

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}
```

✅ **Pros**:  
- Less memory  
- Simpler to implement  

❌ **Cons**:  
- Can't traverse backward  
- To delete a node, you need the **previous node**

---

### 🔹 1.2 Doubly Linked List

```java
class DoublyListNode {
    int val;
    DoublyListNode next;
    DoublyListNode prev;
    DoublyListNode(int val) { this.val = val; }
}
```

✅ **Pros**:  
- Can traverse forward and backward  
- Can delete a node if you have a reference to it (no need for previous)  
- Efficient for operations like reverse traversal  

❌ **Cons**:  
- Uses more memory (extra pointer)  
- Slightly more complex logic

---

### 🔹 1.3 Example: Delete Node in Doubly vs Singly

#### ❌ Singly: Need previous
```java
// To delete node 'current', you need 'prev'
prev.next = current.next;
```

#### ✅ Doubly: Delete with current only
```java
// You can delete 'current' directly
current.prev.next = current.next;
if (current.next != null) {
    current.next.prev = current.prev;
}
```

---

### 📌 Interview Answer

> _"A singly linked list has `next` pointer only — traversal is one-way. A doubly linked list has both `next` and `prev` — enabling bidirectional traversal. Doubly lists allow deletion with only the node reference, but use more memory. I use singly for simple stacks/queues, and doubly when I need backward access or efficient deletions."_  

---

## 2. How do you find the k-th node from the end of a linked list?

> Find the **k-th node from the end** in **one pass**.

Example: In `1→2→3→4→5`, the 2nd from end is `4`.

---

### 🔹 2.1 Approach: Two Pointers (Fast and Slow)

- Use two pointers: `fast` and `slow`
- Move `fast` ahead by `k` steps
- Then move both `fast` and `slow` until `fast` reaches the end
- `slow` will be at the k-th node from the end

---

### 🔹 2.2 Java Code
```java
public static ListNode findKthFromEnd(ListNode head, int k) {
    if (head == null || k <= 0) return null;

    ListNode fast = head;
    ListNode slow = head;

    // Move fast ahead by k steps
    for (int i = 0; i < k; i++) {
        if (fast == null) return null; // k > list length
        fast = fast.next;
    }

    // Move both until fast reaches end
    while (fast != null) {
        slow = slow.next;
        fast = fast.next;
    }

    return slow;
}

// Example usage
public static void main(String[] args) {
    // 1 -> 2 -> 3 -> 4 -> 5
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);
    head.next.next.next = new ListNode(4);
    head.next.next.next.next = new ListNode(5);

    ListNode kth = findKthFromEnd(head, 2);
    System.out.println(kth != null ? kth.val : "Not found"); // 4
}
```

---

### 🔹 2.3 Edge Cases
| Case | Handling |
|------|---------|
| `k = 0` or negative | Return `null` |
| `k > length` | Return `null` |
| Empty list | Return `null` |
| Single node, `k=1` | Return the node |

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I use two pointers: I move the fast pointer k steps ahead, then move both until fast hits the end. Slow will be at the k-th from end. I always validate k and handle edge cases like k > length."_  

---

## 3. How do you check if a linked list is a palindrome?

> A linked list is a palindrome if it reads the **same forward and backward**.

Example: `1→2→3→2→1` → true  
Example: `1→2→3→4→5` → false

---

### 🔹 3.1 Approach: Reverse Second Half

1. Use **two pointers** to find the **middle** of the list
2. **Reverse the second half**
3. Compare first half with reversed second half
4. (Optional) Restore the list by reversing back

✅ **O(n) time, O(1) space**

---

### 🔹 3.2 Java Code
```java
public static boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) return true;

    // Step 1: Find middle
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Step 2: Reverse second half
    ListNode secondHalf = reverse(slow);

    // Step 3: Compare
    ListNode firstHalf = head;
    ListNode temp = secondHalf;
    boolean isPalin = true;

    while (temp != null) {
        if (firstHalf.val != temp.val) {
            isPalin = false;
            break;
        }
        firstHalf = firstHalf.next;
        temp = temp.next;
    }

    // Optional: Restore list
    reverse(secondHalf);

    return isPalin;
}

// Helper: Reverse a linked list
private static ListNode reverse(ListNode head) {
    ListNode prev = null;
    ListNode current = head;
    while (current != null) {
        ListNode next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }
    return prev;
}

// Example usage
public static void main(String[] args) {
    // 1 -> 2 -> 3 -> 2 -> 1
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);
    head.next.next.next = new ListNode(2);
    head.next.next.next.next = new ListNode(1);

    System.out.println(isPalindrome(head)); // true
}
```

---

### 🔹 3.3 Alternative Approaches

| Method | Time | Space | Notes |
|-------|------|-------|------|
| ✅ **Reverse second half** | O(n) | O(1) | Best — optimal space |
| 🟨 **Stack** | O(n) | O(n) | Push first half, compare with second |
| 🟨 **Recursion** | O(n) | O(n) | Uses call stack |
| 🟨 **Convert to array** | O(n) | O(n) | Simple but extra space |

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(1)

### 💡 Interview Tip
> _"I find the middle with two pointers, reverse the second half, and compare with the first. This is O(1) space. I optionally restore the list to its original state. It's efficient and handles odd/even lengths naturally."_  

---

## ✅ Final Tips for Coding Interviews

1. **Always draw the list** when explaining
2. **Handle edge cases**: empty, single node, two nodes
3. **Clarify**: Should you restore the list after reversing?
4. **Use helper methods** (like `reverse`) to keep code clean
5. **State time & space complexity**

---

