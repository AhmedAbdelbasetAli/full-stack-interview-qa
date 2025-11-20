Problem Analysis:
Given two sorted singly linked lists, merge them into a single sorted list by splicing together their nodes, not by creating new nodes. You must return the head of the sorted merged list. Edge cases include empty lists and duplicate values. The order in both input lists is non-decreasing, so the merged output must preserve sortedness. The operation must reuse the original nodes, so avoid allocating new ones beyond a possible dummy head for easier merging.

High-Level Approach & Justification:
Use the two-pointer merge technique (classic for merging two sorted sequences in O(N) time) with a dummy head to simplify edge case handling. At each step, compare the current nodes of both lists, append the smaller node to the output, and move the corresponding pointer forward. Continue until one list is exhausted, then append the rest of the other list. This modifies the list links in-place without unnecessary node creation or data copying, and is optimal for both time and space for this problem size.

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
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // Dummy node to make edge case handling smooth
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;

        // Merge while both lists are nonempty
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
        // At most one of list1, list2 is non-null; append it
        if (list1 != null) {
            current.next = list1;
        } else {
            current.next = list2;
        }
        return dummy.next;
    }
}
```

Detailed Code Walkthrough:
- A dummy node is created to serve as the head of the new merged list; this avoids special handling for the first insert and allows us to always operate on current.next.
- current starts at dummy and is used to build the result list node by node.
- The main loop continues as long as neither input list is exhausted: compare current nodes and link the smaller one to current.next, advancing current and the consumed input list pointer (`list1` or `list2`).
- Once one input list finishes, link the remainder of the other input list (which is already sorted) directly to current.next.
- Return dummy.next, which is a pointer to the head of the newly merged list.

Complexity Analysis:
- Time Complexity: \(O(N + M)\), where N and M are the lengths of list1 and list2 respectively—the function processes every node once.
- Space Complexity: \(O(1)\) auxiliary, since no additional structures are used beyond a few pointers; all list manipulation is performed in-place by relinking nodes. 

This is the optimal and canonical solution for this merging problem for singly linked lists.
