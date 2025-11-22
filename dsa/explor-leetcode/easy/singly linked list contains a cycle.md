Problem Analysis:
You must detect whether a singly linked list contains a cycle—i.e., whether traversing next pointers can ever revisit a node already encountered. The key constraint is to do this in O(1) space (constant memory), disallowing the use of visited node sets or extra storage—even for large lists up to $$10^4$$ nodes. This is a canonical setting for Floyd’s Tortoise and Hare algorithm: use two pointers moving at different speeds to discover cycles mathematically.[1][2][3][4][5][6]

High-Level Approach & Justification:
Use two pointers, slow and fast, both initialized at head:
- In each step, move slow by one and fast by two nodes.
- If fast reaches the end (null), there is no cycle—the list is acyclic.
- If at any time slow == fast (excluding the start), a cycle is present.
This works because, in a cyclic list, fast will eventually “lap” slow and meet it inside the cycle after some number of steps; in an acyclic list, fast will reach null first. This uses only two pointers for O(1) space, and by design, visits each node at most twice for O(n) time.[3][4][1]

Java Solution:
```java
// Definition for singly-linked list.
// class ListNode {
//     int val;
//     ListNode next;
//     ListNode(int val) { this.val = val; this.next = null; }
// }

class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;           // move slow by 1
            fast = fast.next.next;      // move fast by 2

            if (slow == fast) return true; // pointers meet: cycle exists
        }
        return false; // fast reached end: no cycle
    }
}
```


Detailed Code Walkthrough:
Edge cases are handled up front: an empty list or a single-node list without a next reference can never form a cycle. The main loop advances slow by one and fast by two; if slow and fast ever coincide (other than both starting at head), a cycle exists and true is returned. The loop terminates when fast or fast.next is null, meaning the end is reached without any pointer meeting, so the function returns false.[6][1][3][4]

Complexity Analysis:
Time Complexity: $$O(n)$$, as each node is visited at most twice—the slow pointer traverses the list, and the fast pointer may circle but cannot exceed twice the number of nodes before either encountering a cycle or exiting.[1][4]
Space Complexity: $$O(1)$$, as only two pointers (slow, fast) and a few scalars are used regardless of list size.[3][4][6]

