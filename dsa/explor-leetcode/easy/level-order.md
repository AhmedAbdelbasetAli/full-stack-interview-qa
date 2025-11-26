Problem Analysis:
Perform level-order (breadth-first) traversal of a binary tree and return a list of lists, where each inner list contains the values of nodes at that level from left to right. Edge cases include an empty tree (return empty list), a single-node tree (return one list with one value), and trees with up to 2000 nodes, all of which the queue-based BFS naturally handles .[1]

High-Level Approach & Justification:
Use a queue to process nodes level by level: initialize the queue with the root, then while the queue is not empty, process all nodes currently in the queue (one level), extract their values into a list, and enqueue their non-null children for the next level . This guarantees left-to-right order within each level and correct level separation; it visits each node exactly once in $$O(n)$$ time and uses $$O(w)$$ space where $$w$$ is the maximum width of the tree, typically much smaller than $$O(n)$$ for balanced trees .

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result; // empty tree

        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);

        while (!q.isEmpty()) {
            int levelSize = q.size(); // number of nodes at current level
            List<Integer> currentLevel = new ArrayList<>();

            // Process all nodes at the current level
            for (int i = 0; i < levelSize; i++) {
                TreeNode node = q.poll();
                currentLevel.add(node.val);

                // Enqueue children for next level
                if (node.left != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }

            result.add(currentLevel);
        }

        return result;
    }
}
```


Detailed Code Walkthrough:
The result list is initialized to store each level's values, and an empty tree returns an empty result immediately . The queue starts with the root node; on each iteration, levelSize captures the count of nodes currently in the queue (exactly one level), then a for loop processes each node at that level—polling it, adding its value to currentLevel, and enqueuing its non-null children . After all nodes in a level are processed, currentLevel is added to result, and the loop continues with the next level already queued . This ensures that nodes are processed left to right within each level and levels are processed top to bottom .

Complexity Analysis:
Time Complexity: $$O(n)$$, where $$n$$ is the number of nodes, since each node is enqueued and dequeued exactly once .  
Space Complexity: $$O(w)$$, where $$w$$ is the maximum width of the tree; in the worst case of a complete binary tree, the last level can have up to $$n/2$$ nodes, making space $$O(n)$$, but for many practical trees it is much smaller .
