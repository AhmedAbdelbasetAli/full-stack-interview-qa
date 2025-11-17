Problem Analysis:
Delete a given non-tail node from a singly linked list without head access by ensuring the node’s value no longer appears, the list size decreases by one, and relative order of other nodes remains unchanged; the key constraint is the node is guaranteed not to be the last node, enabling access to node.next for manipulation. Traditional deletion by relinking a previous node is impossible without head, so the solution must operate locally at node using only node and node.next, effectively removing the next node while making node take its place in sequence.[1][2][3]

High-Level Approach & Justification:
Copy-and-bypass trick: overwrite node.val with node.next.val, then set node.next to node.next.next, thereby eliminating the next node and removing the original value at node from the list while preserving order before and after; this uses O(1) time and O(1) space and satisfies the constraint that node is not the tail. This approach is correct because after copying, the logical identity of node becomes the next node, and bypassing next reduces the list size by one without needing the previous pointer, which cannot be accessed in a singly linked setup given the problem’s API.[2][3][1]

Java Solution:
```java
class Solution {
    // Definition for singly-linked list.
    // class ListNode { int val; ListNode next; ListNode(int x) { val = x; } }

    public void deleteNode(ListNode node) {
        // Precondition (by problem): node is not tail; node.next exists.
        // Copy next node's value into this node
        node.val = node.next.val;
        // Bypass the next node
        node.next = node.next.next;
        // The 'deleted' node becomes unreachable and eligible for GC
    }
}
```


Detailed Code Walkthrough:
The method assumes node.next is non-null due to the problem guarantee; attempting this on a tail would be invalid because there is no next value to copy and no node to bypass, which is why this constraint is essential for correctness. First, node.val is set to node.next.val so the value originally at node disappears from the list, then node.next is advanced to node.next.next to unlink the next node, reducing the list size by one while keeping the order of all remaining nodes unchanged. The unlinked node is no longer referenced by the list and will be garbage-collected in Java, fulfilling the requirement that deletion is logical rather than manual memory deallocation.[1][2][3]

Complexity Analysis:
Time Complexity: $$O(1)$$ because only constant-time field assignments are performed without traversal. Space Complexity: $$O(1)$$ as no auxiliary data structures are used, just direct modification of the provided node and its next reference.[2][3][1]

