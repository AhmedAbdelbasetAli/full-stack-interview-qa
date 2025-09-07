You’re about to master the **most important data structure in Java** — the one that powers caches, databases, APIs, and 90% of LeetCode “mediums”.

Let’s go.

—

# 🧱 1. What It Is — `HashMap`

> **Analogy**: A **library’s digital catalog**. You type “Harry Potter” → system hashes it → instantly tells you: “Aisle 7, Shelf 3”. No scanning every book.

- **Core Purpose**: Store **key-value pairs** with near-instant O(1) average lookup, insertion, deletion.
- **Java Class**: `java.util.HashMap<K, V>`
- **Structure**: Array of “buckets”. Each bucket holds a linked list (or tree, if too long) of entries with same hash.

```
[0] → [ ("cat", 😺) ]
[1] → [ ("dog", 🐶) → ("log", 📝) ]   ← collision → chained
[2] → [ ("ant", 🐜) ]
[3] → null
```

Used everywhere: user sessions, config maps, API response caching, counting, grouping, memoization.

—

# ⚙️ 2. How It Works (Java Implementation)

### ✅ Built-in Usage

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapExample {
    public static void main(String[] args) {
        // Create
        Map<String, Integer> wordCount = new HashMap<>();

        // Insert / Update
        wordCount.put("apple", 1);        // O(1) average
        wordCount.put("banana", 2);
        wordCount.put("apple", 5);        // update → still O(1)

        // Get
        int count = wordCount.get("apple");     // O(1) → returns 5
        int absent = wordCount.getOrDefault("grape", 0); // safe get → 0

        // Delete
        wordCount.remove("banana");       // O(1)

        // Check existence
        if (wordCount.containsKey("apple")) { ... }  // O(1)
        if (wordCount.containsValue(5)) { ... }      // O(n) — avoid!

        // Iterate
        for (Map.Entry<String, Integer> entry : wordCount.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }

        // Size
        System.out.println("Size: " + wordCount.size()); // O(1)
    }
}
```

### 🛠️ Internal Mechanics (Java 8+)

- **Initial capacity**: 16 (default)
- **Load factor**: 0.75 → resize when 75% full (12/16)
- **Collision handling**:
  - **Linked list** per bucket → if list grows > 8 AND table size >= 64 → converts to **red-black tree** (O(log n) worst-case).
  - Prevents DoS attacks (hash flooding).

```java
// Simplified Entry (Node) structure
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;  // linked list pointer
    // TreeNode subclass if treeified
}
```

### ⏱️ Time Complexity (Big-O)

| Operation               | Time Complexity      | Notes                                     |
|------------------------|----------------------|-------------------------------------------|
| `put(key, value)`      | O(1) average         | O(n) worst-case (bad hash)                |
| `get(key)`             | O(1) average         | O(log n) if treeified, O(n) if bad hash   |
| `remove(key)`          | O(1) average         | Same as get                               |
| `containsKey(key)`     | O(1) average         | Same as get                               |
| `containsValue(value)` | O(n)                 | Must scan all entries — avoid!            |
| `keySet().size()`      | O(1)                 | Stored as field                           |
| Iteration              | O(n)                 | Each entry visited once                   |

### 💾 Space Complexity

- O(n) — but includes bucket array (default 16, grows 2x) + Node objects (each has hash, key, value, next pointer).

—

# 🎯 3. When to Use It

### ✅ Real-World Java Use Cases

- **Spring Boot**: `@Cacheable` → backed by `ConcurrentHashMap`.
- **Android**: Storing view references by ID → `SparseArray` (for ints) or `HashMap`.
- **Web Sessions**: `sessionID → UserSession` map.
- **Counting Frequencies**: `word → count` in text processors.
- **Deduplication**: `seen.add(email)` → `HashSet` (which uses `HashMap` under hood).

> 💡 Uber: `riderId → DriverAssignment` → HashMap for O(1) lookup.  
> 💡 Netflix: `videoId → MetadataCache` → HashMap in memory cache layer.

### ✅ Interview Scenarios

- **Two Sum** → store `number → index`.
- **Group Anagrams** → sort word → use as key.
- **Subarray Sum Equals K** → prefix sum → `sum → count`.
- **LRU Cache** → often combines `HashMap` + `LinkedHashMap` or doubly linked list.

### ✅ System Design Contexts

- **In-memory caching layer** (before Redis).
- **Request deduplication** (idempotency keys).
- **Rate limiting** → `userId → requestCount`.
- **Feature flags** → `featureName → Boolean`.

—

# 🚫 4. When NOT to Use It

### ❌ Anti-Patterns & Misuse

- **Using mutable keys** → if key changes after insertion, you can’t retrieve it!

```java
// 🚫 BROKEN
List<String> key = new ArrayList<>(Arrays.asList("A", "B"));
map.put(key, "value");
key.add("C"); // key mutated → map.get(key) returns null!
```

→ Keys must be **immutable** or not change after insertion.

- **Using `containsValue()`** → O(n) scan. If you need reverse lookup, maintain a second map.

- **Ignoring load factor with huge maps** → causes frequent resizing → use `new HashMap<>(initialCapacity)`.

### 🐢 Performance Traps

- **Bad `hashCode()` implementation** → all keys collide → O(n) performance → DoS risk.

```java
// 🚫 TERRIBLE hashCode
class BadKey {
    public int hashCode() { return 42; } // all keys go to same bucket!
}
```

→ Always override `hashCode()` and `equals()` together (see `Objects.hash()`).

- **Autoboxing in loops** → hidden cost.

```java
Map<Integer, String> map = new HashMap<>();
for (int i = 0; i < 1_000_000; i++) {
    map.put(i, "val"); // autoboxes i → new Integer(i) → GC pressure
}
```

→ For primitive keys, consider `Int2ObjectOpenHashMap` (FastUtil) or similar.

### ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Need ordered keys              | `TreeMap`                 | O(log n), sorted by key                  |
| Thread-safe map                | `ConcurrentHashMap`       | Lock-free reads, segmented writes        |
| Key is `int` or `long`         | `Int2ObjectOpenHashMap`   | Avoid autoboxing, faster, less memory    |
| Insertion-order iteration      | `LinkedHashMap`           | Preserves order, same O(1) perf          |
| Small key space (e.g., status) | `EnumMap`                 | Fastest, type-safe, no hashing           |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

### ⚠️ Thread Safety

- `HashMap` is **NOT thread-safe**.
- **Never** use in multi-threaded write scenarios without external sync.
- **Reads while writing?** → Can cause infinite loop (resize corruption in Java 7) or `ConcurrentModificationException`.

✅ Use `ConcurrentHashMap` for concurrent access:

```java
Map<String, String> safeMap = new ConcurrentHashMap<>();
safeMap.computeIfAbsent("key", k -> expensiveInit()); // atomic
```

### 📦 Memory & Autoboxing

- Each entry = `Node` object → 32+ bytes overhead per entry.
- `Integer`, `Long` keys → boxed → more GC.

→ For performance-critical code, use Eclipse Collections or FastUtil.

### 🏭 Enterprise Best Practices

- **Initialize with expected size**:

```java
// If you expect 1000 entries:
Map<String, User> userMap = new HashMap<>(2000); // ~1000 / 0.75
```

- **Use `getOrDefault()` and `computeIfAbsent()`** — cleaner than null checks.

```java
// Instead of:
if (!map.containsKey(key)) {
    map.put(key, new ArrayList<>());
}
map.get(key).add(item);

// Do:
map.computeIfAbsent(key, k -> new ArrayList<>()).add(item);
```

- **In Spring Boot**: Use `@Cacheable` with `ConcurrentHashMap`-backed cache for simple cases.

### 🤯 Fun Fact

Java 8’s `HashMap` converts long chains to trees to prevent **hash collision attacks** (e.g., sending millions of keys with same hash to crash server).

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: First Unique Character in a String

> Given a string `s`, return the **index** of the first character that appears only once. If none, return -1.  
> Example: `"leetcode"` → return `0` (‘l’ is first unique). `"loveleetcode"` → return `2` (‘v’).

### 🧩 Starter Code

```java
import java.util.HashMap;
import java.util.Map;

public class FirstUniqueChar {
    public static int firstUniqChar(String s) {
        // ✏️ Use HashMap to count frequency, then scan for first count==1
        Map<Character, Integer> freq = new HashMap<>();
        
        // Step 1: Count frequency
        for (char c : s.toCharArray()) {
            freq.put(c, freq.getOrDefault(c, 0) + 1);
        }
        
        // Step 2: Find first char with freq 1
        for (int i = 0; i < s.length(); i++) {
            if (freq.get(s.charAt(i)) == 1) {
                return i;
            }
        }
        
        return -1;
    }

    public static void main(String[] args) {
        System.out.println(firstUniqChar("leetcode"));     // 0
        System.out.println(firstUniqChar("loveleetcode")); // 2
        System.out.println(firstUniqChar("aabb"));         // -1
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class FirstUniqueCharTest {
    @Test
    void testFirstUniqChar() {
        assertEquals(0, FirstUniqueChar.firstUniqChar("leetcode"));
        assertEquals(2, FirstUniqueChar.firstUniqChar("loveleetcode"));
        assertEquals(-1, FirstUniqueChar.firstUniqChar("aabb"));
    }
}
```

> 💡 Time: O(n), Space: O(1) — because only 26 lowercase letters max → but technically O(k) where k=charset size.

—


