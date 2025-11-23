Problem Analysis:
The task is to compute the maximum depth of a binary tree, defined as the number of nodes along the longest path from the root down to any leaf node (a node with no children). An empty tree has depth 0, a single-node tree has depth 1, and for larger trees the depth is one plus the maximum depth of either the left or right subtree at each node.[1][2][3]

High-Level Approach & Justification:
Use a simple recursive DFS: for each node, recursively compute the maximum depth of its left and right children, then return $$1 + \max(\text{leftDepth}, \text{rightDepth})$$; if the node is null, return 0. This aligns naturally with the tree’s recursive structure, visits each node exactly once (O(n) time), and only uses stack space proportional to the tree height (O(h)), which is optimal for this problem given the constraints.[2][4][3][5]

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
    public int maxDepth(TreeNode root) {
        // Base case: empty subtree has depth 0
        if (root == null) return 0;

        // Recursively find the depth of left and right subtrees
        int leftDepth = maxDepth(root.left);
        int rightDepth = maxDepth(root.right);

        // Current node contributes 1 to the maximum of its subtrees
        return 1 + Math.max(leftDepth, rightDepth);
    }
}
```


Detailed Code Walkthrough:
If root is null, the function returns 0, handling the empty tree case and serving as the base case for recursion when traversing past leaves. For a non-null root, the function recursively computes depths for root.left and root.right, then returns 1 plus the larger of those depths, effectively counting the current node on top of the deepest path below it. This recurrence propagates upward so that the initial call at the root yields the number of nodes along the longest root-to-leaf path, which is exactly the maximum depth.[4][6][7][2][3]

Complexity Analysis:
Time Complexity: $$O(n)$$, where $$n$$ is the number of nodes, since each node is visited once to compute its contribution to the depth.[2][3]
Space Complexity: $$O(h)$$, where $$h$$ is the height of the tree, due to the recursion call stack; in the worst case of a skewed tree $$h = n$$, and in a balanced tree $$h = O(\log n)$$.[4][2]

