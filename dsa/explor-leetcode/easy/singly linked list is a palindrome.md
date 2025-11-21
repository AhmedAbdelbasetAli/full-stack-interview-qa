Problem Analysis:
Determine if a singly linked list is a palindrome, where a palindrome reads the same forwards and backwards. With up to 10^5 nodes and the follow-up asking for O(n) time and O(1) space, a naive approach storing all values in an array uses O(n) extra space, which violates the space constraint. The key insight is to use the slow/fast pointer technique to find the middle, reverse the second half in-place, and compare both halves node-by-node, exploiting the list structure itself to avoid extra allocations.[1][2][3]

High-Level Approach & Justification:
Use two pointers: fast moves two steps per iteration, slow moves one step; when fast reaches the end, slow is at the middle, cleanly splitting the list. Reverse the second half starting from slow.next using the standard three-pointer reversal technique, modifying links in-place without allocating new nodes. Compare the first half (from head) with the reversed second half (from the new head returned by reversal) node-by-node; if all values match, it is a palindrome. This achieves O(n) time (one pass to find middle, one to reverse, one to compare) and O(1) auxiliary space (only pointers, no collections), directly satisfying the follow-up.[2][4][5][3][1]

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
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;

        // Step 1: Find the middle of the linked list using slow/fast pointers
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Step 2: Reverse the second half of the linked list
        ListNode secondHalfHead = reverseList(slow);

        // Step 3: Compare the first half with the reversed second half
        ListNode firstHalf = head, secondHalf = secondHalfHead;
        while (secondHalf != null) {
            if (firstHalf.val != secondHalf.val) return false;
            firstHalf = firstHalf.next;
            secondHalf = secondHalf.next;
        }

        return true;
    }

    // Helper function to reverse a linked list (in-place)
    private ListNode reverseList(ListNode head) {
        ListNode prev = null, curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev; // new head of reversed list
    }
}
```


Detailed Code Walkthrough:
The fast/slow pointer loop finds the middle: fast advances two steps per iteration while slow advances one; when fast exits the loop (reaching null or null.next), slow stands at the middle node, partitioning the list logically. The reverseList helper applies the three-pointer technique: save curr.next, redirect curr.next to prev, then advance both prev and curr, flipping all links in the second half and returning the new head. The comparison loop runs from the start of the first half and the start of the reversed second half, checking equality at each step; if all values match, the list is a palindrome, otherwise false. For odd-length lists, the middle element is naturally skipped in the second half, so its value does not affect the comparison.[3][1][2][4]

Complexity Analysis:
Time Complexity: \(O(n)\), decomposed as \(O(n/2)\) to find middle, \(O(n/2)\) to reverse the second half, and \(O(n/2)\) to compare, totaling linear traversal of the entire list. Space Complexity: \(O(1)\) auxiliary space, using only a constant number of pointers (slow, fast, prev, curr, etc.) and no additional data structures or collections.[1][2][3]

