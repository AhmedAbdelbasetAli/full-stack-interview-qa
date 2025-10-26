
---

# 🌳 GRAPH & TREE PROBLEMS — MASTER LIST

---

## 🔹 **I. Binary Tree Traversal (DFS)**

> Use when: You need to visit every node, compute sums, or validate structure.

### 1. **Binary Tree Inorder Traversal** (#94)  
> Return inorder traversal of binary tree.  
✅ **Technique**: Recursive/Iterative DFS.  
💡 Real-world: Expression evaluation, BST validation.

### 2. **Validate Binary Search Tree** (#98)  
> Given root, determine if it’s a valid BST.  
✅ **Technique**: DFS with min/max bounds.  
💡 Real-world: Database index validation, config tree validation.

### 3. **Maximum Depth of Binary Tree** (#104)  
> Find max depth of binary tree.  
✅ **Technique**: DFS recursion or BFS level count.  
💡 Real-world: Call stack depth, UI view hierarchy depth.

---

## 🔹 **II. BFS & Level-Order**

> Use when: You need level-by-level processing or shortest path in unweighted graphs.

### 4. **Binary Tree Level Order Traversal** (#102)  
> Return level order traversal of binary tree.  
✅ **Technique**: BFS with queue.  
💡 Real-world: UI layout engines, game level loading.

### 5. **Course Schedule** (#207)  
> Given prerequisites, can you finish all courses?  
✅ **Technique**: Topological sort (Kahn’s algorithm).  
💡 Real-world: Build systems (Maven), microservices startup order.

### 6. **Number of Islands** (#200)  
> Given 2D grid, count islands (connected '1's).  
✅ **Technique**: DFS/BFS on grid.  
💡 Real-world: Terrain analysis, network cluster detection.

---

## 🔹 **III. Tree Construction & Serialization**

> Use when: You need to build trees from data or serialize for storage.

### 7. **Construct Binary Tree from Preorder and Inorder Traversal** (#105)  
> Rebuild tree from traversals.  
✅ **Technique**: Recursion + HashMap for root index.  
💡 Real-world: Database query plan reconstruction.

### 8. **Serialize and Deserialize Binary Tree** (#297)  
> Convert tree to string and back.  
✅ **Technique**: DFS with delimiters.  
💡 Real-world: Cache storage, RPC serialization.

---

## 🔹 **IV. Advanced Tree Problems**

> Use when: You need path sums, diameter, or LCA.

### 9. **Path Sum** (#112)  
> Does root-to-leaf path sum to target?  
✅ **Technique**: DFS with running sum.  
💡 Real-world: Financial transaction path validation.

### 10. **Lowest Common Ancestor of a Binary Tree** (#236)  
> Find LCA of two nodes.  
✅ **Technique**: DFS with path tracking or recursive postorder.  
💡 Real-world: Git merge base, family tree analysis.

### 11. **Binary Tree Maximum Path Sum** (#124)  
> Find max path sum (any path).  
✅ **Technique**: DFS with global max and return max branch.  
💡 Real-world: Optimal route in weighted network.

---

## 🔹 **V. Graph Algorithms**

> Use when: You need shortest paths, cycles, or connectivity.

### 12. **Clone Graph** (#133)  
> Deep copy a connected undirected graph.  
✅ **Technique**: BFS/DFS with HashMap for node mapping.  
💡 Real-world: Network simulation, state duplication.

### 13. **Word Ladder** (#127)  
> Transform `beginWord` to `endWord` with min steps.  
✅ **Technique**: BFS on word graph.  
💡 Real-world: Spellcheck, DNA sequence transformation.

### 14. **Network Delay Time** (#743)  
> Find min time for signal to reach all nodes.  
✅ **Technique**: Dijkstra’s algorithm.  
💡 Real-world: Network latency analysis, Uber ETA calculation.

### 15. **Redundant Connection** (#684)  
> Find edge that creates cycle in undirected graph.  
✅ **Technique**: Union-Find (Disjoint Set Union).  
💡 Real-world: Network loop detection, dependency cycle resolution.

---

# 🧠 KEY TREE/GRAPH PATTERNS TO MASTER

| Pattern                  | When to Use                          | Problems                     |
|--------------------------|--------------------------------------|------------------------------|
| **DFS (Recursive)**      | Tree traversal, path sums            | #94, #112, #124             |
| **BFS (Queue)**          | Level-order, shortest path (unweighted) | #102, #200, #127           |
| **Topological Sort**     | Dependency resolution                | #207, #210                  |
| **Union-Find**           | Dynamic connectivity, cycle detection| #684, #323                  |
| **Dijkstra’s**           | Shortest path (weighted)             | #743                        |
| **LCA**                  | Common ancestor queries              | #236                        |

---

# 🚀 HOW TO PRACTICE

1. **Start with Traversal**: #94, #104, #102  
2. **Master BFS/DFS**: #200, #133, #127  
3. **Tackle Advanced**: #124, #236, #743  
4. **Time yourself**: Aim for < 20 mins per Medium.

---

# 💡 PRO TIP: Know Your Java Graph Representation

- **Tree Node**:
  ```java
  class TreeNode {
      int val;
      TreeNode left, right;
  }
  ```
- **Graph Node**:
  ```java
  class Node {
      public int val;
      public List<Node> neighbors;
  }
  ```
- **Adjacency List**:
  ```java
  Map<Integer, List<Integer>> graph = new HashMap<>();
  ```

