Problem Analysis:
Reverse a singly linked list and return the new head; handle empty and single-node lists where the result is the same as input or null, and maintain in-place reversal without extra memory beyond a few pointers. The follow-up asks for both iterative and recursive implementations; iterative is typically preferred for constant space, while recursive is elegant but uses call stack space up to the list length.[1][2][3][4]

High-Level Approach & Justification:
- Iterative two-pointer reversal: Walk the list once with pointers prev, cur, and next; for each node, redirect cur.next to prev, then advance prev and cur, resulting in O(n) time and O(1) extra space and avoiding recursion depth issues.[5][4]
- Recursive reversal: Recurse to the tail to reverse the sublist, then set head.next.next = head and head.next = null on the unwind; this is concise and mirrors the inductive structure of the list, with O(n) time and O(n) stack space.[6][3]

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
    // Iterative: O(n) time, O(1) space
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode cur = head;
        while (cur != null) {
            ListNode next = cur.next; // save next
            cur.next = prev;          // reverse link
            prev = cur;               // move prev
            cur = next;               // move cur
        }
        return prev; // new head
    }

    // Recursive: O(n) time, O(n) stack space
    public ListNode reverseListRecursive(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode newHead = reverseListRecursive(head.next);
        head.next.next = head; // reverse the link
        head.next = null;      // terminate the current tail
        return newHead;
    }
}
```


Detailed Code Walkthrough:
The iterative method initializes prev to null and cur to head; in each loop, it saves cur.next, reverses the current link to prev, then advances both prev and cur, ensuring no references are lost and all links are flipped once, making prev the final head. The recursive method treats head as the first node of a subproblem, recurses to reverse head.next..end, then connects head behind its next via head.next.next = head and severs head.next to avoid cycles; base case returns head when at null or the last node, propagating the new head upward. Both implementations conform to in-place reversal and handle empty and small lists naturally without special branching beyond base conditions.[3][1][6][5][4]

Complexity Analysis:
- Iterative: Time \(O(n)\) since each node is visited once; space \(O(1)\) using a fixed set of pointers.[1][4]
- Recursive: Time \(O(n)\) with one visit per node; additional space \(O(n)\) due to recursion stack depth up to list length, which is acceptable within constraints but less space-efficient than the iterative variant.[6][3]

