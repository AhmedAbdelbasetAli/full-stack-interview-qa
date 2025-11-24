Problem Analysis:
Determine if a given binary tree is a valid binary search tree (BST). A valid BST requires that all nodes in the left subtree of any node are strictly less than the node’s key, all nodes in the right subtree are strictly greater, and both subtrees recursively satisfy BST properties. The constraints allow large trees up to 10^4 nodes with node values potentially spanning the full 32-bit integer range (including the limits). A common pitfall is to only check local parent-child relationships, which is insufficient; subtree nodes must satisfy range constraints derived from all ancestors.[1]

High-Level Approach & Justification:
Use a recursion that passes down valid ranges (min and max bounds) for each subtree. For the root, these bounds are initially unbounded (or set to null). For the left child, the upper bound is the parent node’s value; for the right child, the lower bound is the parent node’s value. At each node, validate that its value falls within the allowed range, then recurse to its left and right children with updated bounds. This approach ensures all BST properties globally without extra space besides recursion stack and runs in O(n) time visiting each node once.[2][1]

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
    public boolean isValidBST(TreeNode root) {
        return validate(root, null, null);
    }

    private boolean validate(TreeNode node, Integer lower, Integer upper) {
        if (node == null) return true;

        int val = node.val;

        if (lower != null && val <= lower) return false;
        if (upper != null && val >= upper) return false;

        // Left subtree must have values < val
        if (!validate(node.left, lower, val)) return false;

        // Right subtree must have values > val
        if (!validate(node.right, val, upper)) return false;

        return true;
    }
}
```


Detailed Code Walkthrough:
The validate method takes the current node and optional lower and upper bounds representing the valid value interval for the node; these bounds come from ancestors as constraints. If the node is null, it’s a valid BST subtree by definition. The node’s value is checked against bounds (if set); less strict check is done to prevent duplicates by using strict inequalities to ensure BST requirements. Recursion continues with updated bounds: upper bound for the left child is current node’s value; lower bound for right child is current node’s value. If either subtree returns false, this short-circuits the recursion and propagates false upward. Ultimately, if all nodes satisfy their bounds, true is returned; otherwise, false indicates a violation of BST rules.[1][2]

Complexity Analysis:
Time Complexity: $$O(n)$$ because each node is visited once during the recursive traversal.[2][1]
Space Complexity: $$O(h)$$ due to the recursion stack where $$h$$ is the tree height. In the worst case of a skewed tree, this can be $$O(n)$$, and in balanced trees, it is $$O(\log n)$$.[1][2]

