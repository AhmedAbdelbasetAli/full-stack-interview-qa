You’re entering the **nervous system of modern software** — where Uber finds your ride, Google Maps plots your route, LinkedIn shows “2nd-degree connections,” and Netflix recommends what to watch next.

This is **Graph Theory in Java — production-grade, interview-ready, system-aware**.

No fluff. No hand-waving. Let’s go.

—

# 🧱 1. What It Is — Graphs

> **Analogy**: A **city subway map**.  
> Stations = **nodes (vertices)**.  
> Tracks between stations = **edges**.  
> One-way track? → **directed edge**.  
> Distance or time on track? → **weighted edge**.

- **Graph**: A set of **vertices (V)** and **edges (E)** connecting them.
- **Types**:
  - **Directed** (Twitter follow) vs **Undirected** (Facebook friendship).
  - **Weighted** (road distance) vs **Unweighted** (just connections).
  - **Cyclic** vs **Acyclic** (DAG — Directed Acyclic Graph).

```
    Undirected, Unweighted        Directed, Weighted
        (A) — (B)                   (A) →5→ (B)
         |     |                    ↑2      ↓3
        (C) — (D)                  (C) ←4— (D)
```

Used in:
- **Maps & Routing** → Google Maps, Uber, Waze.
- **Social Networks** → “People You May Know”.
- **Recommendation Engines** → “Users who bought this also bought...”.
- **Dependency Resolution** → Maven, npm, Makefiles (DAGs).
- **Web Crawlers** → Pages = nodes, links = edges.

> 💡 Uber: Rider + Driver + Location = Graph → match via shortest path.  
> 💡 LinkedIn: “2nd-degree connections” → BFS with depth=2.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Graph Representation — Adjacency List (Most Common)

```java
import java.util.*;

// For unweighted graph
Map<Integer, List<Integer>> graph = new HashMap<>();

// For weighted graph
Map<Integer, List<Edge>> weightedGraph = new HashMap<>();

class Edge {
    int to;
    int weight;
    Edge(int to, int weight) {
        this.to = to;
        this.weight = weight;
    }
}

// Build graph
void addEdge(Map<Integer, List<Edge>> graph, int from, int to, int weight) {
    graph.computeIfAbsent(from, k -> new ArrayList<>()).add(new Edge(to, weight));
}

// Example: 0→1 (weight 4), 0→2 (weight 1), 1→3 (weight 2)
Map<Integer, List<Edge>> graph = new HashMap<>();
addEdge(graph, 0, 1, 4);
addEdge(graph, 0, 2, 1);
addEdge(graph, 1, 3, 2);
addEdge(graph, 2, 1, 2);
addEdge(graph, 2, 3, 5);
```

## ✅ BFS — Breadth-First Search (Level Order)

```java
public List<Integer> bfs(Map<Integer, List<Edge>> graph, int start) {
    Set<Integer> visited = new HashSet<>();
    Queue<Integer> queue = new ArrayDeque<>();
    List<Integer> result = new ArrayList<>();

    queue.offer(start);
    visited.add(start);

    while (!queue.isEmpty()) {
        int node = queue.poll();
        result.add(node);

        for (Edge edge : graph.getOrDefault(node, Collections.emptyList())) {
            if (!visited.contains(edge.to)) {
                visited.add(edge.to);
                queue.offer(edge.to);
            }
        }
    }
    return result;
}
```

## ✅ DFS — Depth-First Search (Recursive or Stack)

```java
public List<Integer> dfsRecursive(Map<Integer, List<Edge>> graph, int start) {
    Set<Integer> visited = new HashSet<>();
    List<Integer> result = new ArrayList<>();
    dfsHelper(graph, start, visited, result);
    return result;
}

private void dfsHelper(Map<Integer, List<Edge>> graph, int node, Set<Integer> visited, List<Integer> result) {
    if (visited.contains(node)) return;
    visited.add(node);
    result.add(node);
    for (Edge edge : graph.getOrDefault(node, Collections.emptyList())) {
        dfsHelper(graph, edge.to, visited, result);
    }
}

// Iterative DFS (explicit stack)
public List<Integer> dfsIterative(Map<Integer, List<Edge>> graph, int start) {
    Set<Integer> visited = new HashSet<>();
    Deque<Integer> stack = new ArrayDeque<>();
    List<Integer> result = new ArrayList<>();

    stack.push(start);

    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (visited.contains(node)) continue;
        visited.add(node);
        result.add(node);
        // Add neighbors in reverse order to match recursive behavior (optional)
        List<Edge> neighbors = graph.getOrDefault(node, Collections.emptyList());
        for (int i = neighbors.size() - 1; i >= 0; i--) {
            stack.push(neighbors.get(i).to);
        }
    }
    return result;
}
```

## ✅ Dijkstra’s Algorithm — Shortest Path in Weighted Graph (No Negative Weights)

```java
public Map<Integer, Integer> dijkstra(Map<Integer, List<Edge>> graph, int start) {
    Map<Integer, Integer> dist = new HashMap<>();
    PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1])); // [node, distance]

    // Initialize
    for (int node : graph.keySet()) {
        dist.put(node, Integer.MAX_VALUE);
    }
    dist.put(start, 0);
    pq.offer(new int[]{start, 0});

    while (!pq.isEmpty()) {
        int[] current = pq.poll();
        int u = current[0];
        int d = current[1];

        if (d > dist.get(u)) continue; // outdated entry

        for (Edge edge : graph.getOrDefault(u, Collections.emptyList())) {
            int v = edge.to;
            int newDist = d + edge.weight;
            if (newDist < dist.get(v)) {
                dist.put(v, newDist);
                pq.offer(new int[]{v, newDist});
            }
        }
    }
    return dist;
}
```

### ⏱️ Time Complexity (Big-O)

| Algorithm       | Time Complexity (Adjacency List) | Notes                                     |
|----------------|----------------------------------|-------------------------------------------|
| **BFS**        | O(V + E)                         | Queue, visit each node & edge once        |
| **DFS**        | O(V + E)                         | Stack/recursion, same as BFS              |
| **Dijkstra’s** | O((V + E) log V)                 | With PriorityQueue → log V per insert/extract |
| Graph Build    | O(E)                             | Adding edges                              |

### 💾 Space Complexity

- O(V + E) — store graph.
- O(V) — visited set, distance map, queue/stack.

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Ride-Sharing (Uber/Lyft)** → Dijkstra’s for shortest ETA between rider and driver.
- **Social Networks (LinkedIn/Facebook)** → BFS for “N-degree connections”.
- **Web Crawlers** → DFS or BFS to traverse links.
- **Network Routing (Cisco/Juniper)** → OSPF uses Dijkstra’s internally.
- **Build Systems (Maven/Gradle)** → DAG for dependency resolution.

> 💡 Android: `View` hierarchy → tree (special graph) → DFS for layout traversal.  
> 💡 Spring: `@DependsOn` → DAG for bean initialization order.

## ✅ Interview Scenarios

- **Number of Islands** → DFS/BFS on grid → LeetCode #200.
- **Clone Graph** → DFS with HashMap → LeetCode #133.
- **Course Schedule** → Detect cycle in DAG → LeetCode #207.
- **Network Delay Time** → Dijkstra’s → LeetCode #743.
- **Word Ladder** → BFS → LeetCode #127.

## ✅ System Design Contexts

- **Recommendation Engines** → “People who bought X also bought Y” → graph of product co-views.
- **Fraud Detection** → Graph of transactions → detect rings or suspicious paths.
- **Content Delivery Networks (CDNs)** → Shortest path to edge server → Dijkstra’s.
- **Microservices Dependency Graph** → Visualize and validate call chains.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Using adjacency matrix for sparse graphs** → O(V²) space → use adjacency list.

```java
// 🚫 BAD for sparse graph (e.g., social network with 1M users, avg 100 friends)
int[][] matrix = new int[1_000_000][1_000_000]; // 4TB memory!

// ✅ GOOD
Map<Integer, List<Integer>> adjList = new HashMap<>(); // ~100M entries
```

- **Using Dijkstra’s with negative weights** → undefined behavior → use **Bellman-Ford** or **SPFA**.

- **DFS for shortest path in unweighted graph** → not guaranteed → use **BFS**.

## 🐢 Performance Traps

- **Not checking visited in BFS/DFS** → infinite loop in cyclic graphs.

```java
// ✅ Always mark visited BEFORE enqueueing (BFS) or recursing (DFS)
visited.add(node);
queue.offer(node);
```

- **Dijkstra’s without PriorityQueue** → O(V²) → use heap!

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Dense graphs (E ≈ V²)          | Adjacency Matrix          | O(1) edge lookup                         |
| Negative weights               | Bellman-Ford              | Handles negative, slower O(VE)           |
| All-pairs shortest path        | Floyd-Warshall            | O(V³), simple for small graphs           |
| Topological Sort               | Kahn’s Algorithm (BFS)    | For DAGs → build order, task scheduling  |
| Very large graphs              | Graph DB (Neo4j)          | Optimized storage, traversal, indexing    |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Thread Safety

- Custom graph → **not thread-safe**.
- For concurrent graph processing → use immutable graphs or external sync.
- Libraries like **JGraphT** offer thread-safe wrappers.

## 📦 Memory & Performance

- **Adjacency List** → best for sparse graphs → memory = O(V + E).
- **Primitive optimizations** → for performance-critical code, use `int[]` arrays and avoid `Edge` objects.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use `@Graph` annotations? Not built-in — but you can model with entities + relationships (JPA) or use Neo4j with Spring Data Neo4j.

```java
// Pseudo — using relationships
@Repository
public interface PersonRepository extends Neo4jRepository<Person, Long> {
    @Query("MATCH (p:Person)-[:FRIEND*1..2]-(friend) WHERE p.id = $id RETURN friend")
    List<Person> findFriendsOfFriends(Long id); // BFS depth=2
}
```

- **In Android**: Avoid deep recursion in DFS → use iterative DFS with `ArrayDeque` to prevent stack overflow.

## 🤯 Fun Fact

Google Maps doesn’t use vanilla Dijkstra’s — it uses **Contraction Hierarchies** and **A*** with heuristics for continent-scale routing → but Dijkstra’s is the foundation.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Network Delay Time (Dijkstra’s)

> You are given a network of `n` nodes, labeled from `1` to `n`.  
> `times[i] = (ui, vi, wi)` means a signal from `ui` to `vi` takes `wi` time.  
> Send a signal from node `k`. Return the time it takes for all nodes to receive the signal. If impossible, return `-1`.

LeetCode #743.

### 🧩 Starter Code

```java
import java.util.*;

public class NetworkDelayTime {
    public static int networkDelayTime(int[][] times, int n, int k) {
        // Build graph: node -> list of (neighbor, weight)
        Map<Integer, List<Edge>> graph = new HashMap<>();
        for (int[] time : times) {
            graph.computeIfAbsent(time[0], x -> new ArrayList<>())
                 .add(new Edge(time[1], time[2]));
        }

        // Dijkstra's
        Map<Integer, Integer> dist = new HashMap<>();
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));

        for (int i = 1; i <= n; i++) {
            dist.put(i, Integer.MAX_VALUE);
        }
        dist.put(k, 0);
        pq.offer(new int[]{k, 0});

        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int u = current[0];
            int d = current[1];

            if (d > dist.get(u)) continue;

            for (Edge edge : graph.getOrDefault(u, Collections.emptyList())) {
                int v = edge.to;
                int newDist = d + edge.weight;
                if (newDist < dist.get(v)) {
                    dist.put(v, newDist);
                    pq.offer(new int[]{v, newDist});
                }
            }
        }

        // Find max delay
        int maxDelay = Collections.max(dist.values());
        return maxDelay == Integer.MAX_VALUE ? -1 : maxDelay;
    }

    static class Edge {
        int to, weight;
        Edge(int to, int weight) { this.to = to; this.weight = weight; }
    }

    public static void main(String[] args) {
        int[][] times = {{2,1,1},{2,3,1},{3,4,1}};
        System.out.println(networkDelayTime(times, 4, 2)); // 2
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class NetworkDelayTimeTest {
    @Test
    void testNetworkDelayTime() {
        int[][] times = {{2,1,1},{2,3,1},{3,4,1}};
        assertEquals(2, NetworkDelayTime.networkDelayTime(times, 4, 2));
    }
}
```

> 💡 Time: O((V + E) log V), Space: O(V + E)

—

# 📚 7. Further Reading / Java Docs Reference

- 📘 **Official JavaDoc**: N/A (no built-in Graph) — but see:  
  - [PriorityQueue](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/PriorityQueue.html)  
  - [HashMap](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/HashMap.html)
- 🧩 **LeetCode Problem**: [#743 Network Delay Time](https://leetcode.com/problems/network-delay-time/) — Medium, Dijkstra’s, Graph
- 📖 **Book Reference**: “Algorithm Design Manual” by Steven Skiena — Chapter 6: Weighted Graph Algorithms

—

