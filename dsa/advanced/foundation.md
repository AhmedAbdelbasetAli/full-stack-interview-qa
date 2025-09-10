You’ve climbed the mountain.  
You’ve mastered arrays, trees, graphs, heaps, tries, union-find, bit magic, sliding windows, backtracking, and sorting.  

Now — **the summit**:  
> 💡 **System Design Patterns using Data Structures** — where algorithms meet architecture, and you design systems that scale, survive traffic spikes, and never lose data.

This is what **senior engineers and architects** do daily — and what **FAANG system design interviews** test.

We’ll cover:

- **LRU Cache** → HashMap + Doubly Linked List
- **Rate Limiting** → Token Bucket (Queue) vs Sliding Window (Deque)
- **Pub/Sub** → Topic → Subscriber Map + Message Queue
- **Distributed Lock** → Redis + Expire + UUID
- **Consistent Hashing** → For sharding — Ring + TreeMap

All in **Java**, production-aware, with trade-offs.

Let’s design like a pro. 🏗️

—

# 🧱 1. What It Is — System Design Patterns with DS/Algo

> **Core Idea**: Combine data structures to solve **real distributed systems problems** — caching, throttling, messaging, coordination.

```
Pattern                | Core DS/Algo Used
———————————————————————————————————————————————
LRU Cache              | HashMap + Doubly Linked List
Rate Limiting          | Queue (Token Bucket) or Deque (Sliding Window)
Pub/Sub                | HashMap<Topic, Set<Subscriber>> + Queue<Message>
Distributed Lock       | Redis (SET with NX, EX) + UUID
Consistent Hashing     | Sorted Map (TreeMap) + Hash Ring
```

> 💡 Uber: “LRU Cache for driver locations”, “Token Bucket for API rate limiting”.  
> 💡 Netflix: “Pub/Sub for microservices”, “Consistent Hashing for CDN routing”.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Pattern 1: LRU Cache — O(1) get/put

> **Data Structures**: `HashMap` (key → node) + **Doubly Linked List** (for eviction order).

```java
import java.util.*;

public class LRUCache {
    class Node {
        int key, value;
        Node prev, next;
        Node(int k, int v) { key = k; value = v; }
    }

    private Map<Integer, Node> cache;
    private Node head, tail;
    private int capacity;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        cache = new HashMap<>();
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!cache.containsKey(key)) return -1;
        Node node = cache.get(key);
        remove(node);
        add(node); // move to front
        return node.value;
    }

    public void put(int key, int value) {
        if (cache.containsKey(key)) {
            remove(cache.get(key));
        }
        Node node = new Node(key, value);
        cache.put(key, node);
        add(node);

        if (cache.size() > capacity) {
            Node lru = tail.prev;
            remove(lru);
            cache.remove(lru.key);
        }
    }

    private void add(Node node) {
        Node after = head.next;
        head.next = node;
        node.prev = head;
        node.next = after;
        after.prev = node;
    }

    private void remove(Node node) {
        Node before = node.prev, after = node.next;
        before.next = after;
        after.prev = before;
    }
}
```

→ **Time**: O(1) get/put. **Space**: O(capacity).  
→ **Java Alternative**: `LinkedHashMap` with `removeEldestEntry`.

```java
// Built-in — but not thread-safe
Map<Integer, Integer> cache = new LinkedHashMap<Integer, Integer>(16, 0.75f, true) {
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity;
    }
};
```

—

## ✅ Pattern 2: Rate Limiting — Token Bucket

> **Data Structure**: `Queue` (timestamps) + `AtomicInteger` (tokens).

```java
import java.util.concurrent.*;
import java.util.*;

public class TokenBucketRateLimiter {
    private final int capacity;
    private final Queue<Long> tokens;
    private final long refillRateMs;

    public TokenBucketRateLimiter(int capacity, int tokensPerSecond) {
        this.capacity = capacity;
        this.tokens = new ConcurrentLinkedQueue<>();
        this.refillRateMs = 1000 / tokensPerSecond;

        // Pre-fill bucket
        for (int i = 0; i < capacity; i++) {
            tokens.offer(System.currentTimeMillis());
        }

        // Refill thread
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
        scheduler.scheduleAtFixedRate(this::refill, refillRateMs, refillRateMs, TimeUnit.MILLISECONDS);
    }

    private void refill() {
        if (tokens.size() < capacity) {
            tokens.offer(System.currentTimeMillis());
        }
    }

    public boolean allow() {
        Long token = tokens.poll();
        if (token == null) return false; // no tokens
        long now = System.currentTimeMillis();
        if (now - token > 60_000) { // token expired (1 min)
            return false;
        }
        return true;
    }
}
```

→ **Alternative**: Sliding Window with `Deque` (store timestamps, remove expired).

—

## ✅ Pattern 3: Pub/Sub — Topic → Subscribers + Message Queue

> **Data Structures**: `ConcurrentHashMap<String, Set<Consumer>>` + `BlockingQueue` per subscriber.

```java
import java.util.concurrent.*;
import java.util.*;
import java.util.function.Consumer;

public class PubSub {
    private final Map<String, Set<Consumer<String>>> subscribers = new ConcurrentHashMap<>();
    private final Map<Consumer<String>, BlockingQueue<String>> queues = new ConcurrentHashMap<>();

    public void subscribe(String topic, Consumer<String> subscriber) {
        subscribers.computeIfAbsent(topic, k -> ConcurrentHashMap.newKeySet()).add(subscriber);
        queues.putIfAbsent(subscriber, new LinkedBlockingQueue<>());

        // Start consumer thread
        new Thread(() -> {
            try {
                while (!Thread.currentThread().isInterrupted()) {
                    String message = queues.get(subscriber).take();
                    subscriber.accept(message);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }).start();
    }

    public void publish(String topic, String message) {
        Set<Consumer<String>> topicSubs = subscribers.get(topic);
        if (topicSubs != null) {
            for (Consumer<String> sub : topicSubs) {
                try {
                    queues.get(sub).put(message);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }
    }
}
```

→ **Use**: Decouple producers and consumers.  
→ **Production**: Use Kafka, RabbitMQ, or AWS SNS/SQS.

—

## ✅ Pattern 4: Distributed Lock — Redis + UUID

> **Data Structure**: Redis `SET` with `NX` (set if not exists) and `EX` (expire).

```java
import redis.clients.jedis.Jedis;
import java.util.UUID;

public class DistributedLock {
    private Jedis jedis;
    private String lockKey;
    private String lockValue; // UUID
    private int expireTimeSeconds;

    public DistributedLock(Jedis jedis, String lockKey, int expireTimeSeconds) {
        this.jedis = jedis;
        this.lockKey = lockKey;
        this.expireTimeSeconds = expireTimeSeconds;
        this.lockValue = UUID.randomUUID().toString();
    }

    public boolean tryLock() {
        String result = jedis.set(lockKey, lockValue, "NX", "EX", expireTimeSeconds);
        return "OK".equals(result);
    }

    public void unlock() {
        String script = 
            "if redis.call('get', KEYS[1]) == ARGV[1] then " +
            "return redis.call('del', KEYS[1]) " +
            "else return 0 end";
        jedis.eval(script, 1, lockKey, lockValue);
    }
}
```

→ **Why UUID?** Avoid deleting someone else’s lock.  
→ **Why Lua script?** Atomic check-and-delete.

—

## ✅ Pattern 5: Consistent Hashing — For Sharding

> **Data Structure**: `TreeMap` (hash ring) + hash function.

```java
import java.util.*;

public class ConsistentHashing<T> {
    private final TreeMap<Integer, T> ring = new TreeMap<>();
    private final int replicas;

    public ConsistentHashing(int replicas) {
        this.replicas = replicas;
    }

    public void addNode(T node) {
        for (int i = 0; i < replicas; i++) {
            int hash = hash(node.toString() + ":" + i);
            ring.put(hash, node);
        }
    }

    public void removeNode(T node) {
        for (int i = 0; i < replicas; i++) {
            int hash = hash(node.toString() + ":" + i);
            ring.remove(hash);
        }
    }

    public T getNode(String key) {
        if (ring.isEmpty()) return null;
        int hash = hash(key);
        Map.Entry<Integer, T> entry = ring.ceilingEntry(hash);
        if (entry == null) {
            entry = ring.firstEntry(); // wrap around
        }
        return entry.getValue();
    }

    private int hash(String key) {
        return Math.abs(key.hashCode()); // simple, replace with MD5/SHA in prod
    }
}
```

→ **Use**: Shard users across servers — add/remove servers with minimal remapping.  
→ **Production**: Use Ketama hashing, virtual nodes.

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

- **LRU Cache**: Session data, config, metadata — `Caffeine`, `Guava Cache`.
- **Rate Limiting**: API gateways — `Resilience4j`, `Sentinel`.
- **Pub/Sub**: Microservices communication — `Kafka`, `RabbitMQ`, `Redis Pub/Sub`.
- **Distributed Lock**: Leader election, idempotency — `Redis`, `ZooKeeper`.
- **Consistent Hashing**: Database sharding, CDN routing — `Vitess`, `Cassandra`.

> 💡 Spring Boot: `@Cacheable` → LRU via Caffeine. `@RateLimiter` → Resilience4j.  
> 💡 Android: Not typically — but backend services powering apps use all of these.

## ✅ Interview Scenarios (System Design)

- **Design Twitter** → Pub/Sub for tweets, LRU for feed cache.
- **Design TinyURL** → Rate limiting per IP, LRU for URL cache.
- **Design Dropbox** → Consistent hashing for file sharding.
- **Design Rate Limiter** → Token bucket vs sliding window.
- **Design Distributed Cache** → LRU + consistent hashing + replication.

## ✅ System Design Contexts

- **Microservices**: Pub/Sub for async communication.
- **APIs**: Rate limiting to prevent abuse.
- **Databases**: Sharding with consistent hashing.
- **Caching Layers**: LRU for hot data.
- **Coordination**: Distributed locks for critical sections.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **LRU Cache without size bound** → memory leak.

```java
// 🚫 Broken
cache.put(key, value); // without eviction

// ✅ Always bound
if (cache.size() > MAX) evict();
```

- **Distributed Lock without timeout** → deadlock if client crashes.

```java
// ✅ Always use expire
jedis.set(lockKey, value, "NX", "EX", 30);
```

- **Consistent Hashing with poor hash function** → uneven distribution.

```java
// 🚫 Bad
int hash = key.hashCode(); // can be negative, uneven

// ✅ Good
int hash = Murmur3.hash32(key.getBytes());
```

## 🐢 Performance Traps

- **Not using `ConcurrentHashMap`** in multi-threaded pub/sub → race conditions.
- **Blocking in rate limiter** → use non-blocking `offer`/`poll` with timeout.
- **LRU with `LinkedHashMap` in high concurrency** → not thread-safe → use `Caffeine`.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| LRU Cache                      | Caffeine / Guava Cache    | Thread-safe, optimized, stats            |
| Rate Limiting                  | Resilience4j / Sentinel   | Production-grade, metrics, config         |
| Pub/Sub                        | Kafka / RabbitMQ          | Durable, scalable, fault-tolerant        |
| Distributed Lock               | Redisson / ZooKeeper      | Battle-tested, reentrant, lease support  |
| Consistent Hashing             | Ketama / Rendezvous Hash  | Better distribution, less remapping      |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Thread Safety is Non-Negotiable

- Use `ConcurrentHashMap`, `ConcurrentLinkedQueue`, `AtomicInteger`.
- Use `synchronized` or `ReentrantLock` if needed — but prefer lock-free.

## 📦 Memory & Performance

- **LRU**: Use `Caffeine` — optimized, off-heap options.
- **Pub/Sub**: Use bounded queues → avoid OOM.

```java
new ArrayBlockingQueue<>(1000); // bounded
```

## 🏭 Enterprise Best Practices

- **In Spring Boot**:
  - Use `@Cacheable` with `CaffeineCacheManager`.
  - Use `Resilience4j` for rate limiting.
  - Use `Spring Kafka` for pub/sub.
- **In Android**: Avoid — these are backend patterns. But understand for interviews.

## 🤯 Fun Fact

**Consistent Hashing** was invented in 1997 at MIT for Akamai’s CDN — and it’s still the backbone of **Cassandra, DynamoDB, and Memcached** sharding.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Design LRU Cache

> Design a data structure that follows LRU cache constraints:  
> - `LRUCache(int capacity)` Initialize with positive size.  
> - `int get(int key)` Return value if exists, else -1.  
> - `void put(int key, int value)` Update or insert. Evict LRU if full.

LeetCode #146 — the classic.

### 🧩 Starter Code

```java
import java.util.*;

public class LRUCacheChallenge {
    class Node {
        int key, value;
        Node prev, next;
        Node(int k, int v) { key = k; value = v; }
    }

    private Map<Integer, Node> cache;
    private Node head, tail;
    private int capacity;

    public LRUCacheChallenge(int capacity) {
        this.capacity = capacity;
        cache = new HashMap<>();
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!cache.containsKey(key)) return -1;
        Node node = cache.get(key);
        remove(node);
        add(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (cache.containsKey(key)) {
            remove(cache.get(key));
        }
        Node node = new Node(key, value);
        cache.put(key, node);
        add(node);

        if (cache.size() > capacity) {
            Node lru = tail.prev;
            remove(lru);
            cache.remove(lru.key);
        }
    }

    private void add(Node node) {
        Node after = head.next;
        head.next = node;
        node.prev = head;
        node.next = after;
        after.prev = node;
    }

    private void remove(Node node) {
        Node before = node.prev, after = node.next;
        before.next = after;
        after.prev = before;
    }

    public static void main(String[] args) {
        LRUCacheChallenge lru = new LRUCacheChallenge(2);
        lru.put(1, 1);
        lru.put(2, 2);
        System.out.println(lru.get(1)); // 1
        lru.put(3, 3); // evicts key 2
        System.out.println(lru.get(2)); // -1
        lru.put(4, 4); // evicts key 1
        System.out.println(lru.get(1)); // -1
        System.out.println(lru.get(3)); // 3
        System.out.println(lru.get(4)); // 4
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class LRUCacheChallengeTest {
    @Test
    void testLRUCache() {
        LRUCacheChallenge lru = new LRUCacheChallenge(2);
        lru.put(1, 1);
        lru.put(2, 2);
        assertEquals(1, lru.get(1));
        lru.put(3, 3); // evicts 2
        assertEquals(-1, lru.get(2));
        lru.put(4, 4); // evicts 1
        assertEquals(-1, lru.get(1));
        assertEquals(3, lru.get(3));
        assertEquals(4, lru.get(4));
    }
}
```

> 💡 Time: O(1) per op, Space: O(capacity) — textbook LRU.

—




