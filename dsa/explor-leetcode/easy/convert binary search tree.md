Problem Analysis:
Convert a strictly sorted ascending integer array into a height-balanced binary search tree (BST), where height-balanced means the depth of left and right subtrees of every node differs by at most 1. Multiple valid BSTs exist (e.g., different root choices), but any balanced one is accepted. The sorted property enables O(1) middle-element access to guarantee balance, and constraints ensure up to 10^4 nodes with values in [-10^4, 10^4].[1][2]

High-Level Approach & Justification:
Use divide-and-conquer recursion: for subarray nums[left:right], select the middle element nums[mid] as root (ensuring balance), recursively build left subtree from nums[left:mid-1], and right subtree from nums[mid+1:right]. This guarantees O(log n) height since each recursive call halves the array size, maintains BST property (left < root < right due to sorted input), and visits each array element exactly once for O(n) time.[2][3]

Java Solution:
```java
// Definition for a binary tree node.
// public class TreeNode {
//     int val;
//     TreeNode left;
//     TreeNode right;
//     TreeNode() {}
//     TreeNode(int val) { this.val = val; }
//     TreeNode(int val, TreeNode left, TreeNode right) {
//         this.val = val;
//         this.left = left;
//         this.right = right;
//     }
// }

class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }
    
    private TreeNode helper(int[] nums, int left, int right) {
        // Base case: no nodes
        if (left > right) return null;
        
        // Find middle element as root (balanced choice)
        int mid = left + (right - left) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        
        // Recursively build left and right subtrees
        root.left = helper(nums, left, mid - 1);
        root.right = helper(nums, mid + 1, right);
        
        return root;
    }
}
```


Detailed Code Walkthrough:
The helper function takes a subarray range [left, right]; if left > right, it returns null (empty subtree). The middle index mid is computed safely using left + (right - left)/2 to avoid integer overflow, and a new TreeNode is created with nums[mid] as its value. Recursively, the left subtree is built from elements smaller than root (left to mid-1), and right subtree from larger elements (mid+1 to right), preserving BST ordering; the root connects these subtrees and returns upward. The initial call processes the full array [0, nums.length-1], yielding a balanced BST root.[3][1][2][4]

Complexity Analysis:
Time Complexity: \(O(n)\), where \(n\) is array length, since each element is visited exactly once across all recursive calls (T(n) = T(n/2) + T(n/2) + O(1)).[2][4]
Space Complexity: \(O(\log n)\) recursion stack depth due to balanced tree construction; each call processes half the previous range.[3][4]

