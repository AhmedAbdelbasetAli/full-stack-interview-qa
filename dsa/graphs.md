# 🌐 Graph Algorithms Deep Dive  
**DFS, BFS, Cycle Detection, Islands, Topological Sort & Shortest Path**

Here are **5 essential graph problems** with **clean Java implementations**, **step-by-step explanations**, and **interview tips**.

All code is written in **Java**.

---

## 1. Implement depth-first search (DFS) and breadth-first search (BFS) for a graph.

We'll use **adjacency list** representation.

### ✅ Graph Representation
```java
import java.util.*;

public class Graph {
    private int vertices;
    private List<List<Integer>> adjList;

    public Graph(int v) {
        vertices = v;
        adjList = new ArrayList<>(v);
        for (int i = 0; i < v; i++) {
            adjList.add(new ArrayList<>());
        }
    }

    public void addEdge(int u, int v) {
        adjList.get(u).add(v);
        adjList.get(v).add(u); // For undirected graph
    }
}
```

---

### 🔹 1.1 DFS (Depth-First Search) – Recursive

```java
public static void dfsRecursive(Graph graph, int start, boolean[] visited) {
    visited[start] = true;
    System.out.print(start + " ");

    for (int neighbor : graph.adjList.get(start)) {
        if (!visited[neighbor]) {
            dfsRecursive(graph, neighbor, visited);
        }
    }
}
```

### 🔹 1.2 DFS – Iterative (Using Stack)
```java
public static void dfsIterative(Graph graph, int start) {
    boolean[] visited = new boolean[graph.vertices];
    Stack<Integer> stack = new Stack<>();
    stack.push(start);

    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (!visited[node]) {
            visited[node] = true;
            System.out.print(node + " ");

            // Push all unvisited neighbors
            for (int neighbor : graph.adjList.get(node)) {
                if (!visited[neighbor]) {
                    stack.push(neighbor);
                }
            }
        }
    }
}
```

---

### 🔹 1.3 BFS (Breadth-First Search) – Iterative (Using Queue)
```java
public static void bfs(Graph graph, int start) {
    boolean[] visited = new boolean[graph.vertices];
    Queue<Integer> queue = new LinkedList<>();
    visited[start] = true;
    queue.offer(start);

    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.print(node + " ");

        for (int neighbor : graph.adjList.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
}
```

---

### 📌 Time & Space
- **Time**: O(V + E) for both
- **Space**: O(V) for `visited` array and stack/queue

---

### 💡 Interview Tip
> _"DFS uses a stack (recursion or explicit) — goes deep first. BFS uses a queue — explores level by level. I use DFS for pathfinding, connected components. BFS for shortest path in unweighted graphs. I always mark visited to avoid cycles."_  

---

## 2. How do you detect a cycle in a directed/undirected graph?

---

### 🔹 2.1 Cycle in Undirected Graph

> Use DFS with **parent tracking** — if you visit a node that’s already visited and not the parent → cycle.

```java
public static boolean hasCycleUndirected(Graph graph) {
    boolean[] visited = new boolean[graph.vertices];
    for (int i = 0; i < graph.vertices; i++) {
        if (!visited[i]) {
            if (dfsUndirected(graph, i, visited, -1)) {
                return true;
            }
        }
    }
    return false;
}

private static boolean dfsUndirected(Graph graph, int node, boolean[] visited, int parent) {
    visited[node] = true;
    for (int neighbor : graph.adjList.get(node)) {
        if (!visited[neighbor]) {
            if (dfsUndirected(graph, neighbor, visited, node)) {
                return true;
            }
        } else if (neighbor != parent) {
            return true; // Back edge to non-parent → cycle
        }
    }
    return false;
}
```

---

### 🔹 2.2 Cycle in Directed Graph

> Use **DFS with recursion stack** — if a node is in recursion stack and visited again → cycle.

```java
public static boolean hasCycleDirected(GraphDirected graph) {
    boolean[] visited = new boolean[graph.vertices];
    boolean[] recStack = new boolean[graph.vertices];

    for (int i = 0; i < graph.vertices; i++) {
        if (dfsDirected(graph, i, visited, recStack)) {
            return true;
        }
    }
    return false;
}

private static boolean dfsDirected(GraphDirected graph, int node, boolean[] visited, boolean[] recStack) {
    if (recStack[node]) return true; // Node in recursion stack → cycle
    if (visited[node]) return false;

    visited[node] = true;
    recStack[node] = true;

    for (int neighbor : graph.adjList.get(node)) {
        if (dfsDirected(graph, neighbor, visited, recStack)) {
            return true;
        }
    }

    recStack[node] = false; // Remove from recursion stack
    return false;
}
```

✅ `GraphDirected` is same as `Graph` but `addEdge(u,v)` only adds `u→v`.

---

### 📌 Time & Space
- **Time**: O(V + E)
- **Space**: O(V)

---

### 💡 Interview Tip
> _"For undirected graphs, I use DFS with parent check — if I see a visited node that's not my parent, it's a cycle. For directed graphs, I use a recursion stack — if a node is visited again while still in the stack, there's a cycle. This is key for topological sorting."_  

---

## 3. Solve the "Number of Islands" problem.

> Given a 2D grid of `'1'` (land) and `'0'` (water), count the number of **connected land regions** (islands).

Use **DFS or BFS** to mark connected components.

---

### ✅ Approach
- Traverse grid
- When you find `'1'`, increment island count and **flood-fill** (DFS/BFS) to mark all connected land as visited (or set to `'0'`)

---

### ✅ Java Code (DFS)
```java
public static int numIslands(char[][] grid) {
    if (grid == null || grid.length == 0) return 0;

    int islands = 0;
    int rows = grid.length;
    int cols = grid[0].length;

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == '1') {
                islands++;
                dfsFloodFill(grid, i, j, rows, cols);
            }
        }
    }

    return islands;
}

private static void dfsFloodFill(char[][] grid, int i, int j, int rows, int cols) {
    if (i < 0 || i >= rows || j < 0 || j >= cols || grid[i][j] == '0') {
        return;
    }

    grid[i][j] = '0'; // Mark as visited

    // Explore 4 directions
    dfsFloodFill(grid, i+1, j, rows, cols);
    dfsFloodFill(grid, i-1, j, rows, cols);
    dfsFloodFill(grid, i, j+1, rows, cols);
    dfsFloodFill(grid, i, j-1, rows, cols);
}
```

---

### 📌 Time & Space
- **Time**: O(m × n) — each cell visited once
- **Space**: O(m × n) — recursion stack in worst case (all land)

---

### 💡 Interview Tip
> _"I use DFS to 'flood-fill' each island. When I hit land ('1'), I increment the count and recursively turn all connected land to '0' to avoid recounting. This is a classic connected components problem in a grid. I could use BFS too — same complexity."_  

---

## 4. What is topological sorting? When is it used?

> **Topological sorting** is a **linear ordering** of vertices in a **Directed Acyclic Graph (DAG)** such that for every directed edge `u → v`, `u` comes before `v`.

Used for **dependency resolution**.

---

### 🔹 4.1 When to Use
| Use Case | Example |
|--------|--------|
| ✅ **Task Scheduling** | Build system: A must compile before B |
| ✅ **Course Prerequisites** | Take CS101 before CS202 |
| ✅ **Package Management** | Install dependencies first |
| ✅ **Makefile Execution** | Rule dependencies |

---

### 🔹 4.2 Algorithm: Kahn's Algorithm (BFS)

- Compute **in-degree** of each node
- Enqueue all nodes with in-degree 0
- While queue not empty:
  - Dequeue node → add to result
  - Reduce in-degree of neighbors
  - If neighbor's in-degree becomes 0 → enqueue

---

### ✅ Java Code
```java
public static List<Integer> topologicalSort(GraphDirected graph) {
    int[] inDegree = new int[graph.vertices];
    for (int u = 0; u < graph.vertices; u++) {
        for (int v : graph.adjList.get(u)) {
            inDegree[v]++;
        }
    }

    Queue<Integer> queue = new LinkedList<>();
    for (int i = 0; i < graph.vertices; i++) {
        if (inDegree[i] == 0) {
            queue.offer(i);
        }
    }

    List<Integer> result = new ArrayList<>();
    while (!queue.isEmpty()) {
        int u = queue.poll();
        result.add(u);

        for (int v : graph.adjList.get(u)) {
            inDegree[v]--;
            if (inDegree[v] == 0) {
                queue.offer(v);
            }
        }
    }

    // If result size < vertices → cycle exists
    if (result.size() != graph.vertices) {
        throw new IllegalArgumentException("Graph has a cycle");
    }

    return result;
}
```

---

### 📌 Time & Space
- **Time**: O(V + E)
- **Space**: O(V)

---

### 💡 Interview Tip
> _"Topological sort orders nodes so all dependencies come first. I use Kahn's algorithm: compute in-degrees, start with nodes that have no incoming edges, and process them level by level. It's used in build systems, course scheduling, and dependency resolution. If the result doesn't include all nodes, the graph has a cycle."_  

---

## 5. Find the shortest path in an unweighted graph.

> In an **unweighted graph**, the **shortest path** is the one with **fewest edges**.

Use **BFS** — it explores level by level.

---

### ✅ Approach
- Use BFS from source
- Track `distance[]` array
- When you reach target → return distance

---

### ✅ Java Code
```java
public static int shortestPath(Graph graph, int src, int dest) {
    if (src == dest) return 0;

    int[] dist = new int[graph.vertices];
    Arrays.fill(dist, -1);
    Queue<Integer> queue = new LinkedList<>();
    dist[src] = 0;
    queue.offer(src);

    while (!queue.isEmpty()) {
        int u = queue.poll();

        for (int v : graph.adjList.get(u)) {
            if (dist[v] == -1) { // Not visited
                dist[v] = dist[u] + 1;
                if (v == dest) return dist[v];
                queue.offer(v);
            }
        }
    }

    return -1; // No path
}
```

---

### 📌 Time & Space
- **Time**: O(V + E)
- **Space**: O(V)

---

### 💡 Interview Tip
> _"In unweighted graphs, BFS finds the shortest path because it explores all nodes at distance 1, then 2, etc. I use a distance array and stop when I reach the destination. For weighted graphs, I'd use Dijkstra's algorithm."_  

---

## ✅ Final Tips for Coding Interviews

1. **Clarify**: Directed or undirected? Weighted? Multiple components?
2. **Use adjacency list** for sparse graphs
3. **Always mark visited** to avoid infinite loops
4. **Know when to use DFS vs BFS**
5. **State time & space complexity**

---

