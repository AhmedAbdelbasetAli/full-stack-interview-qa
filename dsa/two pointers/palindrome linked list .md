
```

---

## ✅ FINAL CORRECT CODE

```java
public boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) {
        return true;
    }

    // Step 1: Find middle
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Step 2: For odd length, skip middle node
    ListNode secondHalfStart = (fast == null) ? slow : slow.next;
    ListNode secondHalf = reverse(secondHalfStart);

    // Step 3: Compare
    ListNode firstHalf = head;
    while (secondHalf != null) {
        if (firstHalf == null || firstHalf.val != secondHalf.val) {
            return false; // Only return false here
        }
        firstHalf = firstHalf.next;
        secondHalf = secondHalf.next;
    }

    return true; // Return true only after full comparison
}

private ListNode reverse(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}
```

---

## ✅ WHY THIS WORKS

- The `return false` is only taken if there’s a mismatch.
- If no mismatch, we update pointers and continue.
- Only after the loop do we return `true`.

→ No unreachable code.

---

## 🧪 TEST CASES

```java
[1,2,2,1] → true
[1,2,3,2,1] → true
[1,2] → false
[1] → true
[1,2,3] → false
```

---

