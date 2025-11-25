Problem Analysis:
The task is to check if a binary tree is symmetric around its center, meaning the left subtree is a mirror of the right subtree in both structure and node values. Mirror means: root values must match, left child of the left subtree must match right child of the right subtree, and right child of the left must match left child of the right, recursively at every level.[1][2][3][4]

High-Level Approach & Justification:
- Recursive: Write a helper that takes two nodes (left, right) and checks if their subtrees are mirrors by comparing values and recursing on (left.left, right.right) and (left.right, right.left). This directly encodes the mirror definition and is concise and natural for tree recursion.[3][5]
- Iterative: Use a queue, pushing nodes in pairs that should mirror each other; repeatedly pop two, check symmetry conditions, and enqueue their children in mirrored order (left.left with right.right, left.right with right.left). Both approaches visit each node a constant number of times, yielding $$O(n)$$ time and $$O(h)$$ or $$O(n)$$ space (recursion depth or queue size).[6][7][8][3]

Java Solution (recursive + iterative):
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
    // --- Public API: recursive version ---
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }

    // Helper: check if two subtrees are mirror images
    private boolean isMirror(TreeNode left, TreeNode right) {
        if (left == null && right == null) return true;
        if (left == null || right == null) return false;
        if (left.val != right.val) return false;

        // Left's left with right's right, left's right with right's left
        return isMirror(left.left, right.right)
            && isMirror(left.right, right.left);
    }

    // --- Iterative version using a queue ---
    public boolean isSymmetricIterative(TreeNode root) {
        if (root == null) return true;

        java.util.Queue<TreeNode> q = new java.util.LinkedList<>();
        // Start by comparing root with itself
        q.offer(root.left);
        q.offer(root.right);

        while (!q.isEmpty()) {
            TreeNode left = q.poll();
            TreeNode right = q.poll();

            if (left == null && right == null) continue;
            if (left == null || right == null) return false;
            if (left.val != right.val) return false;

            // Enqueue children in mirrored order
            q.offer(left.left);
            q.offer(right.right);
            q.offer(left.right);
            q.offer(right.left);
        }
        return true;
    }
}
```


Detailed Code Walkthrough:
The recursive isSymmetric calls isMirror on root.left and root.right; isMirror first handles null cases (both null is symmetric, exactly one null is not), then compares values and recurses on the mirrored child pairs (left.left with right.right and left.right with right.left). The iterative isSymmetricIterative initializes a queue with the two children of root, then repeatedly dequeues two nodes that should mirror each other; if both are null it continues, if one is null or values differ it returns false, otherwise it enqueues their children in mirrored order to check the next level. If the queue empties without violation, all mirrored node pairs matched, so the tree is symmetric and true is returned.[7][4][5][3][6]

Complexity Analysis:
Both implementations have Time Complexity $$O(n)$$, where $$n$$ is the number of nodes, since each node is enqueued/recursed on a constant number of times.[4][3]
Space Complexity is $$O(h)$$ for the recursive approach (call stack, worst-case $$O(n)$$ for skewed trees) and $$O(w)$$ for the iterative queue, where $$w$$ is the maximum width of the tree, bounded by $$O(n)$$ in the worst case.[8][6]

