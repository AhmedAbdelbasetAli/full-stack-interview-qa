# 🌲 Java Binary Tree Algorithms  
**Traversals, Height, Balance & BST Validation**

Here are **5 essential binary tree problems** with **clean Java implementations** — both **recursive and iterative** where applicable.

All code is written in **Java** and includes a `TreeNode` class.

---

## 1. Implement pre-order, in-order, and post-order traversal (recursive and iterative)

### ✅ TreeNode Class
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) { this.val = val; }
}
```

---

### 🔹 1.1 Pre-order Traversal (Root → Left → Right)

#### 🟦 Recursive
```java
public static void preOrderRecursive(TreeNode root, List<Integer> result) {
    if (root == null) return;
    result.add(root.val);
    preOrderRecursive(root.left, result);
    preOrderRecursive(root.right, result);
}
```

#### 🟦 Iterative (Using Stack)
```java
import java.util.*;

public static List<Integer> preOrderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);

    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);

        // Push right first, then left (stack is LIFO)
        if (node.right != null) stack.push(node.right);
        if (node.left != null) stack.push(node.left);
    }

    return result;
}
```

---

### 🔹 1.2 In-order Traversal (Left → Root → Right)

#### 🟦 Recursive
```java
public static void inOrderRecursive(TreeNode root, List<Integer> result) {
    if (root == null) return;
    inOrderRecursive(root.left, result);
    result.add(root.val);
    inOrderRecursive(root.right, result);
}
```

#### 🟦 Iterative (Using Stack)
```java
public static List<Integer> inOrderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
    TreeNode current = root;

    while (current != null || !stack.isEmpty()) {
        // Go to the leftmost node
        while (current != null) {
            stack.push(current);
            current = current.left;
        }

        // Current is null, pop from stack
        current = stack.pop();
        result.add(current.val);

        // Visit right subtree
        current = current.right;
    }

    return result;
}
```

---

### 🔹 1.3 Post-order Traversal (Left → Right → Root)

#### 🟦 Recursive
```java
public static void postOrderRecursive(TreeNode root, List<Integer> result) {
    if (root == null) return;
    postOrderRecursive(root.left, result);
    postOrderRecursive(root.right, result);
    result.add(root.val);
}
```

#### 🟦 Iterative (Using Stack – Two Stacks)
```java
public static List<Integer> postOrderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Stack<TreeNode> stack1 = new Stack<>();
    Stack<TreeNode> stack2 = new Stack<>();
    stack1.push(root);

    while (!stack1.isEmpty()) {
        TreeNode node = stack1.pop();
        stack2.push(node);

        if (node.left != null) stack1.push(node.left);
        if (node.right != null) stack1.push(node.right);
    }

    // Pop from stack2 to get reverse order
    while (!stack2.isEmpty()) {
        result.add(stack2.pop().val);
    }

    return result;
}
```

---

### 📌 Time & Space
- **Time**: O(n) for all
- **Space**: O(h) where h = height (worst O(n) for skewed tree)

---

### 💡 Interview Tip
> _"I use recursion for simplicity, but can implement iteratively with stacks. Pre-order: root first. In-order: left-root-right (key for BST). Post-order: children first. For iterative post-order, I use two stacks — one to reverse the order."_  

---

## 2. How do you perform level-order traversal (BFS) of a binary tree?

> **Level-order traversal** visits nodes **level by level** — also known as **Breadth-First Search (BFS)**.

---

### ✅ Approach: Use a Queue

- Enqueue root
- While queue not empty:
  - Dequeue node
  - Add to result
  - Enqueue left and right children

---

### ✅ Java Code
```java
import java.util.*;

public static List<Integer> levelOrder(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        TreeNode node = queue.poll();
        result.add(node.val);

        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }

    return result;
}
```

---

### ✅ Bonus: Level-by-Level (Return List of Lists)
```java
public static List<List<Integer>> levelOrderGrouped(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        List<Integer> currentLevel = new ArrayList<>();

        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll();
            currentLevel.add(node.val);

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }

        result.add(currentLevel);
    }

    return result;
}
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(w) where w = max width (can be O(n) in worst case)

---

### 💡 Interview Tip
> _"I use a queue for BFS. For level-by-level output, I process each level in a loop using the current queue size. This is essential for problems like 'average of levels' or 'right side view'."_  

---

## 3. Find the height/depth of a binary tree.

> The **height** of a tree is the **number of edges from root to deepest leaf**.

Height of empty tree = -1  
Height of single node = 0

---

### ✅ Recursive Approach
```java
public static int height(TreeNode root) {
    if (root == null) return -1; // or 0, depending on convention

    int leftHeight = height(root.left);
    int rightHeight = height(root.right);

    return Math.max(leftHeight, rightHeight) + 1;
}
```

---

### ✅ Iterative Approach (Using BFS)
```java
public static int heightIterative(TreeNode root) {
    if (root == null) return -1;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    int height = -1;

    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        height++;

        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll();
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
    }

    return height;
}
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(w) for BFS, O(h) for recursion

---

### 💡 Interview Tip
> _"I use recursion: height is max of left and right subtree + 1. For iterative, I use BFS and count levels. I clarify: is height in edges (return -1 for null) or nodes (return 0)? I usually use edge count."_  

---

## 4. How do you check if a binary tree is balanced?

> A binary tree is **balanced** if the **height difference** between left and right subtrees is **at most 1** for **every node**.

---

### ✅ Approach: Post-order DFS (Optimal)

- Use recursion to get height of left and right
- Check balance at each node
- Return height if balanced, `Integer.MIN_VALUE` if not

---

### ✅ Java Code
```java
public static boolean isBalanced(TreeNode root) {
    return checkHeight(root) != Integer.MIN_VALUE;
}

private static int checkHeight(TreeNode root) {
    if (root == null) return -1;

    int leftHeight = checkHeight(root.left);
    if (leftHeight == Integer.MIN_VALUE) return Integer.MIN_VALUE;

    int rightHeight = checkHeight(root.right);
    if (rightHeight == Integer.MIN_VALUE) return Integer.MIN_VALUE;

    if (Math.abs(leftHeight - rightHeight) > 1) {
        return Integer.MIN_VALUE; // Not balanced
    }

    return Math.max(leftHeight, rightHeight) + 1;
}
```

---

### 📌 Time & Space
- **Time**: O(n) — each node visited once
- **Space**: O(h) — recursion stack

---

### 💡 Interview Tip
> _"I use a post-order DFS that returns height or a sentinel value if unbalanced. This avoids recalculating height for each node (O(n²)). It’s O(n) time and optimal. I return early if any subtree is unbalanced."_  

---

## 5. Validate whether a binary tree is a valid BST.

> A **valid BST** satisfies:
- Left subtree values < root
- Right subtree values > root
- Both left and right subtrees are BSTs

---

### ✅ Approach: Recursive with Min/Max Bounds

- Use `isValidBST(root, min, max)`
- For left: `max = root.val`
- For right: `min = root.val`
- Handle `Integer.MIN_VALUE/MAX_VALUE` with `Long` or `null`

---

### ✅ Java Code
```java
public static boolean isValidBST(TreeNode root) {
    return isValidBST(root, null, null);
}

private static boolean isValidBST(TreeNode root, Long min, Long max) {
    if (root == null) return true;

    if (min != null && root.val <= min) return false;
    if (max != null && root.val >= max) return false;

    return isValidBST(root.left, min, (long) root.val) &&
           isValidBST(root.right, (long) root.val, max);
}
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(h)

---

### 💡 Interview Tip
> _"I use recursion with min/max bounds. A node must be greater than min and less than max. For left subtree, max is root.val; for right, min is root.val. I use Long to handle Integer bounds. This is cleaner than in-order traversal."_  

---

# 🌲 Advanced Binary Tree Algorithms  
**LCA, Serialize/Deserialize, and Sorted Array to BST**

Here are **3 advanced binary tree problems** with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java** and includes a `TreeNode` class.

---

## 1. Find the Lowest Common Ancestor (LCA) in a Binary Tree

> The **Lowest Common Ancestor (LCA)** of two nodes `p` and `q` is the **deepest node** that is an ancestor of both.

Works for **any binary tree** (not just BST).

---

### ✅ Approach: Recursive DFS

- Traverse the tree
- If current node is `p` or `q`, return it
- Recursively search left and right
- If both left and right return non-null → current node is LCA
- Else, return the non-null side

---

### ✅ Java Code
```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) { this.val = val; }
}

public static TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // Base case: reached null or found p/q
    if (root == null || root == p || root == q) {
        return root;
    }

    // Search left and right subtrees
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);

    // If both sides found something, root is LCA
    if (left != null && right != null) {
        return root;
    }

    // Otherwise, return the non-null side
    return left != null ? left : right;
}
```

---

### 🔹 Example
```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4

LCA of 5 and 1 → 3
LCA of 5 and 4 → 5
```

---

### 📌 Time & Space
- **Time**: O(n) — visit each node once
- **Space**: O(h) — recursion stack (h = height)

---

### 💡 Interview Tip
> _"I use DFS: if I find p or q, I return it. If both left and right subtrees return non-null, the current node is the LCA. Otherwise, I return the side that found a match. This works because the LCA is the first node where p and q split into different subtrees."_  

---

## 2. How do you serialize and deserialize a binary tree?

> **Serialize**: Convert tree to a string  
> **Deserialize**: Reconstruct tree from string

Handles **any binary tree** (not just BST).

---

### ✅ Approach: Pre-order Traversal with `null` markers

- Use **pre-order** (root → left → right)
- Represent `null` as `"#"`, values as strings
- For deserialization, use a queue to rebuild recursively

---

### ✅ Java Code
```java
import java.util.*;

public class Codec {

    // Serialize: tree → string
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }

    private void serializeHelper(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append("#,");
            return;
        }
        sb.append(root.val).append(",");
        serializeHelper(root.left, sb);
        serializeHelper(root.right, sb);
    }

    // Deserialize: string → tree
    public TreeNode deserialize(String data) {
        Queue<String> nodes = new LinkedList<>(Arrays.asList(data.split(",")));
        return deserializeHelper(nodes);
    }

    private TreeNode deserializeHelper(Queue<String> nodes) {
        String val = nodes.poll();
        if (val.equals("#")) {
            return null;
        }
        TreeNode node = new TreeNode(Integer.parseInt(val));
        node.left = deserializeHelper(nodes);
        node.right = deserializeHelper(nodes);
        return node;
    }
}
```

---

### 🔹 Example
```
Tree:
    1
   / \
  2   3
     / \
    4   5

Serialize → "1,2,#,#,3,4,#,#,5,#,#"
```

---

### 📌 Time & Space
- **Time**: O(n)
- **Space**: O(n) — for string and queue

---

### 💡 Interview Tip
> _"I use pre-order traversal with null markers. During serialization, I append '#' for null. For deserialization, I use a queue and recursively rebuild the tree. This preserves structure and works for any tree. I could use BFS, but DFS is simpler with recursion."_  

---

## 3. Convert a Sorted Array into a Balanced BST

> Given a **sorted array**, build a **height-balanced BST** — where the depth of the two subtrees of every node never differs by more than 1.

---

### ✅ Approach: Recursive with Midpoint

- Choose **middle element** as root
- Recursively build left subtree from left half
- Recursively build right subtree from right half

---

### ✅ Java Code
```java
public static TreeNode sortedArrayToBST(int[] nums) {
    if (nums == null || nums.length == 0) {
        return null;
    }
    return buildBST(nums, 0, nums.length - 1);
}

private static TreeNode buildBST(int[] nums, int left, int right) {
    if (left > right) {
        return null;
    }

    int mid = left + (right - left) / 2;
    TreeNode root = new TreeNode(nums[mid]);

    root.left = buildBST(nums, left, mid - 1);
    root.right = buildBST(nums, mid + 1, right);

    return root;
}
```

---

### 🔹 Example
```java
int[] nums = {-10, -3, 0, 5, 9};
TreeNode root = sortedArrayToBST(nums);
```

✅ Resulting BST:
```
      0
     / \
   -3   9
   /   /
 -10  5
```

Balanced — height difference ≤ 1 at every node.

---

### 📌 Time & Space
- **Time**: O(n) — each element visited once
- **Space**: O(log n) — recursion stack (height of balanced tree)

---

### 💡 Interview Tip
> _"I use divide and conquer: pick the middle element as root, then recursively build left and right subtrees from the left and right halves. This ensures the tree is balanced because each subtree has roughly half the elements. It’s O(n) time and optimal."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify assumptions**: Are nodes unique? Is the tree BST?
2. **Draw the tree** when explaining
3. **Handle edge cases**: empty array, single node, duplicates
4. **Use helper methods** for clean, readable code
5. **State time & space complexity**

---



