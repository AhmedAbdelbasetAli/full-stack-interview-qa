You're asking for **searching algorithms** — the core techniques every engineer must know to **find data fast**, whether in arrays, trees, graphs, or databases.

Let’s break it down — **Java-first, production-aware, interview-sharp** — from linear scan to binary search to graph traversal.

—

# 🧱 1. What It Is — Searching Algorithms

> **Core Purpose**: Find an element (or its position) in a data structure — as fast as possible.

```
Types:
- Linear Search → O(n) → unsorted data
- Binary Search → O(log n) → sorted data
- BFS/DFS → O(V+E) → graphs/trees
- Hash Lookup → O(1) avg → key-value
- Interpolation Search → O(log log n) → uniformly distributed sorted data
```

> 💡 Uber: “Find nearest driver” → spatial index + binary search on sorted distances.  
> 💡 Android: “Find contact by name” → Trie or HashMap.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ 1. Linear Search — O(n)

```java
public class LinearSearch {
    public static int search(int[] arr, int target) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) {
                return i;
            }
        }
        return -1;
    }
}
```

→ Use when: **unsorted**, small n, or need to find first occurrence.

## ✅ 2. Binary Search — O(log n)

```java
public class BinarySearch {
    public static int search(int[] arr, int target) {
        int left = 0, right = arr.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2; // avoid overflow

            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }

    // Bonus: Find first occurrence (for duplicates)
    public static int findFirst(int[] arr, int target) {
        int left = 0, right = arr.length - 1;
        int result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == target) {
                result = mid;
                right = mid - 1; // keep searching left
            } else if (arr[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return result;
    }
}
```

→ Use when: **sorted array**, need O(log n).

## ✅ 3. BFS — O(V+E) for Graphs/Trees

```java
import java.util.*;

public class BFSSearch {
    public static boolean bfs(Map<Integer, List<Integer>> graph, int start, int target) {
        Set<Integer> visited = new HashSet<>();
        Queue<Integer> queue = new ArrayDeque<>();
        queue.offer(start);
        visited.add(start);

        while (!queue.isEmpty()) {
            int node = queue.poll();
            if (node == target) return true;

            for (int neighbor : graph.getOrDefault(node, Collections.emptyList())) {
                if (!visited.contains(neighbor)) {
                    visited.add(neighbor);
                    queue.offer(neighbor);
                }
            }
        }
        return false;
    }
}
```

→ Use when: **shortest path in unweighted graph**, level-order.

## ✅ 4. DFS — O(V+E)

```java
public class DFSSearch {
    public static boolean dfs(Map<Integer, List<Integer>> graph, int start, int target) {
        Set<Integer> visited = new HashSet<>();
        return dfsRecursive(graph, start, target, visited);
    }

    private static boolean dfsRecursive(Map<Integer, List<Integer>> graph, int node, int target, Set<Integer> visited) {
        if (node == target) return true;
        if (!visited.add(node)) return false;

        for (int neighbor : graph.getOrDefault(node, Collections.emptyList())) {
            if (dfsRecursive(graph, neighbor, target, visited)) {
                return true;
            }
        }
        return false;
    }
}
```

→ Use when: **path existence**, backtracking, tree traversal.

## ✅ 5. Hash Lookup — O(1) avg

```java
Map<String, User> userMap = new HashMap<>();
userMap.put("alice", new User("Alice"));
User user = userMap.get("alice"); // O(1)
```

→ Use when: **key-based lookup**, caching, counting.

### ⏱️ Time Complexity Summary

| Algorithm         | Time Complexity      | Space     | Best For                                  |
|-------------------|----------------------|-----------|-------------------------------------------|
| **Linear Search** | O(n)                 | O(1)      | Unsorted, small n                         |
| **Binary Search** | O(log n)             | O(1)      | Sorted arrays                             |
| **BFS**           | O(V + E)             | O(V)      | Shortest path (unweighted), level-order   |
| **DFS**           | O(V + E)             | O(V)      | Path existence, tree traversal            |
| **Hash Lookup**   | O(1) avg, O(n) worst | O(n)      | Key-value, frequency counting             |
| **Interpolation** | O(log log n) avg     | O(1)      | Uniformly distributed sorted data         |

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **Binary Search**: Database index range queries, auto-complete dropdowns.
- **BFS**: Social network “degrees of separation”, web crawling.
- **DFS**: File system traversal, dependency resolution.
- **Hash Lookup**: User session lookup, caching, deduplication.
- **Linear Search**: Small config lists, unsorted logs.

> 💡 Spring Boot: `@Cacheable` → hash lookup. `@Query` with `BETWEEN` → binary search on index.  
> 💡 Android: `RecyclerView` search → linear or binary if sorted.

## ✅ Interview Scenarios

- **Binary Search**: “Find peak element”, “Search in rotated sorted array”, “Find first/last position”.
- **BFS/DFS**: “Number of islands”, “Clone graph”, “Word ladder”.
- **Hash**: “Two sum”, “Group anagrams”, “Longest substring without repeating chars”.

## ✅ System Design Contexts

- **Database Indexes**: B-tree → binary search.
- **Caching Layers**: HashMap → O(1) lookup.
- **Recommendation Engines**: BFS for “friends of friends”.
- **Log Analysis**: Linear scan for unindexed fields.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Binary search on unsorted data** → wrong results.

```java
// 🚫 Broken
Arrays.sort(arr); // forgot this!
int idx = binarySearch(arr, target);
```

- **DFS for shortest path in unweighted graph** → not guaranteed → use BFS.

- **Linear search on large sorted array** → O(n) when O(log n) possible.

- **Hash lookup with mutable keys** → can’t find after mutation.

## 🐢 Performance Traps

- **Not using `Arrays.binarySearch()`** → it’s optimized.

```java
// ✅ Use built-in
int idx = Arrays.binarySearch(arr, target);
```

- **Recursive DFS without visited set** → infinite loop in cyclic graphs.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Sorted array                   | Binary Search             | O(log n) > O(n)                          |
| Key-value lookup               | HashMap                   | O(1) > O(log n)                          |
| Shortest path (unweighted)     | BFS                       | DFS not optimal                          |
| Very small n (< 100)           | Linear Search             | Simpler, less overhead                   |
| Spatial data                   | KD-Tree / R-Tree          | 2D/3D range queries                      |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Use `Arrays.binarySearch()` — But Know Its Quirks

```java
int idx = Arrays.binarySearch(arr, target);
if (idx < 0) {
    idx = -idx - 1; // insertion point
}
```

## 📦 Memory & Performance

- **Binary search** → no extra space → cache-friendly.
- **BFS/DFS** → O(V) space → can be heavy for large graphs.

## 🏭 Enterprise Best Practices

- **In Spring Boot**: Use `TreeMap` for sorted key lookup → `O(log n)` with ordering.
- **In Android**: Use `SparseArray` for integer keys → avoids autoboxing.
- **Logging**: Log search type and time for analytics — “Binary search took 2ms”.

## 🤯 Fun Fact

**Binary search is notoriously hard to implement bug-free** — even Jon Bentley’s 1986 study showed 90% of professional programmers got it wrong on first try.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Search in Rotated Sorted Array

> Given a sorted array rotated at an unknown pivot, find the index of target.  
> Example: `[4,5,6,7,0,1,2]`, target=0 → return 4.

LeetCode #33 — binary search with a twist.

### 🧩 Starter Code

```java
public class SearchRotated {
    public static int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            }

            // Left half is sorted
            if (nums[left] <= nums[mid]) {
                if (nums[left] <= target && target < nums[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else { // Right half is sorted
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        System.out.println(search(new int[]{4,5,6,7,0,1,2}, 0)); // 4
        System.out.println(search(new int[]{4,5,6,7,0,1,2}, 3)); // -1
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class SearchRotatedTest {
    @Test
    void testSearch() {
        assertEquals(4, SearchRotated.search(new int[]{4,5,6,7,0,1,2}, 0));
        assertEquals(-1, SearchRotated.search(new int[]{4,5,6,7,0,1,2}, 3));
    }
}
```

> 💡 Time: O(log n), Space: O(1) — modified binary search.

—
