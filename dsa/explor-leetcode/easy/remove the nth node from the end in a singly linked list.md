Problem Analysis:
Remove the nth node from the end in a singly linked list and return the (possibly new) head; edge cases include removing the head itself when n equals the list length and lists with a single node where the result is empty. The follow-up asks for a one-pass solution, which rules out the straightforward two-pass approach that first computes the length and then deletes the (L−n+1)-th node from the start.[1][2][3][4]

High-Level Approach & Justification:
Use two pointers with a dummy node: create dummy -> head, advance a fast pointer n+1 steps to maintain a fixed gap of n between fast and slow (both initially at dummy), then move both until fast reaches null; slow will land just before the node to remove, enabling O(1) deletion via slow.next = slow.next.next in a single pass. The dummy node cleanly handles removing the head (when the target is the first node), avoiding null checks and special cases, and overall runs in O(L) time and O(1) space, which meets the follow-up requirement.[5][3][4][1]

Java Solution:
```java
// Definition for singly-linked list.
// class ListNode {
//     int val;
//     ListNode next;
//     ListNode() {}
//     ListNode(int val) { this.val = val; }
//     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
// }

class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // Dummy node to simplify edge cases (e.g., removing head)
        ListNode dummy = new ListNode(0, head);
        ListNode fast = dummy, slow = dummy;

        // Advance fast by n + 1 steps to keep a gap of n between fast and slow
        for (int i = 0; i <= n; i++) {
            fast = fast.next; // by constraints, n <= size ensures this is safe
        }

        // Move both pointers until fast reaches the end
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }

        // slow is just before the target node; remove it
        slow.next = slow.next.next;

        // Return the possibly new head
        return dummy.next;
    }
}
```


Detailed Code Walkthrough:
The dummy node points to head so that both pointers can start from a node preceding the actual head, which standardizes deletion logic, including when the head must be removed (after the shift, dummy.next correctly becomes head.next). Advancing fast by n+1 steps establishes an invariant that the distance between fast and slow is exactly n; when fast reaches null, slow is positioned at the node immediately before the nth-from-end target, enabling deletion in O(1) by skipping slow.next. Finally, returning dummy.next yields the updated head in all cases, satisfying the examples and constraints without a second traversal or extra space.[3][4][1][2][5]
