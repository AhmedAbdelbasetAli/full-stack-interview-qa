# 🌟 Senior Java Interview Questions & Answers  
**Advanced Concepts, Design, Performance & Best Practices**

This document covers **30+ senior-level Java interview questions** — designed to test **deep knowledge**, **system design**, **concurrency**, and **real-world problem-solving**.

Each question includes:
- ✅ Clear explanation
- ✅ Real code examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 🔹 1. What is the JVM? Explain its architecture.

> **JVM (Java Virtual Machine)** is an **abstract computing machine** that enables Java programs to run on any platform.

### ✅ JVM Architecture
```
[Class Loader] → [Runtime Data Areas] → [Execution Engine]
```

### 🔹 Key Components
| Component | Purpose |
|--------|--------|
| **Class Loader** | Loads `.class` files (Bootstrap, Extension, Application) |
| **Method Area** | Stores class metadata, static variables |
| **Heap** | Runtime data (objects) — GC-managed |
| **Stack** | Thread-specific (local variables, method calls) |
| **PC Register** | Tracks current instruction |
| **Native Method Stack** | For native code (C/C++) |
| **Execution Engine** | Interprets bytecode, JIT compiler, GC |

---

### 📌 Interview Answer
> _"The JVM loads classes, manages memory, and executes bytecode. It has a heap for objects, stack for method calls, and a JIT compiler for performance. The GC automatically reclaims unused objects. This enables Java’s 'write once, run anywhere' promise."_  

---

## 🔹 2. What is the difference between `equals()` and `==`?

| `==` | `equals()` |
|------|----------|
| Compares **references** (memory addresses) | Compares **logical equality** (content) |
| For objects: "Are they the same instance?" | For objects: "Do they have the same value?" |
| For primitives: compares values | Must be overridden (e.g., in `String`, `Integer`) |

```java
String s1 = new String("hello");
String s2 = new String("hello");

System.out.println(s1 == s2);      // false (different objects)
System.out.println(s1.equals(s2)); // true (same content)
```

---

### 📌 Interview Answer
> _"== compares object references, while equals() compares logical equality. I override equals() (and hashCode) when I need value-based comparison, like for User objects with the same email. I always use Objects.equals() to avoid NPE."_  

---

## 🔹 3. What is the `volatile` keyword? When would you use it?

> `volatile` ensures that a variable is **always read from and written to main memory**, not cached in thread-local memory.

✅ Prevents **visibility issues** in multi-threaded environments.

```java
public class FlagExample {
    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    public void run() {
        while (running) {
            // do work
        }
    }
}
```

Without `volatile`, one thread might never see the updated value.

---

### 📌 When to Use `volatile`
| Use Case | Why |
|--------|-----|
| ✅ **Flag variables** | `shutdown`, `running` |
| ✅ **Double-Checked Locking** | In lazy initialization |
| ❌ **Compound operations** | Not atomic (e.g., `count++`) → use `AtomicInteger` |

---

### 📌 Interview Answer
> _"I use volatile for boolean flags like 'shutdown' to ensure visibility across threads. It forces reads/writes to main memory. But it doesn’t make operations atomic — for count++, I use AtomicInteger instead."_  

---

## 🔹 4. What is the Java Memory Model (JMM)?

> The **Java Memory Model (JMM)** defines how threads interact through memory — especially **visibility**, **ordering**, and **atomicity**.

### ✅ Key Concepts
- ✅ **Happens-Before Relationship**: Ensures one action is visible to another
- ✅ **Visibility**: Changes by one thread are visible to others
- ✅ **Atomicity**: Operations like `long` and `double` are not atomic by default
- ✅ **Reordering**: Compiler/JVM may reorder instructions (unless prevented by `volatile`, `synchronized`, `final`)

```java
volatile int x = 0;
int y = 0;

// Thread 1
y = 1;        // 1
x = 1;        // 2 (volatile write)

// Thread 2
if (x == 1) { // 3 (volatile read)
    System.out.println(y); // 4 → guaranteed to be 1
}
```

✅ Due to **happens-before**, `y=1` is visible when `x==1`

---

### 📌 Interview Answer
> _"The JMM defines how threads interact with memory. It guarantees visibility through volatile, synchronized, and final. It allows reordering for performance, but happens-before rules ensure correctness. I rely on it when building thread-safe classes."_  

---

## 🔹 5. How does the `synchronized` keyword work under the hood?

> `synchronized` uses **intrinsic locks (monitor locks)** to ensure **mutual exclusion**.

### ✅ How It Works
- Each object has a **monitor (mutex)**
- When a thread enters a `synchronized` block/method, it **acquires the monitor**
- Other threads **block** until it’s released

```java
public synchronized void criticalSection() {
    // Only one thread at a time
}
```

Or:
```java
synchronized (this) {
    // critical code
}
```

---

### ✅ Monitor States
| State | Description |
|------|-------------|
| **Contended** | Multiple threads competing |
| **Biased** | JVM optimizes for single-threaded access |
| **Thin** | Lightweight lock |
| **Heavy** | OS-level mutex (slow) |

---

### 📌 Interview Answer
> _"Synchronized uses monitor locks. When a thread enters a synchronized block, it acquires the object’s monitor. Others wait. The JVM optimizes with biased locking. For better performance, I use ReentrantLock or concurrent collections."_  

---

## 🔹 6. What is the difference between `ConcurrentHashMap` and `synchronizedMap`?

| Feature | `ConcurrentHashMap` | `Collections.synchronizedMap()` |
|--------|----------------------|-------------------------------|
| **Locking** | Segment-level (or CAS) | Entire map |
| **Performance** | High (multiple threads can read/write) | Low (entire map locked) |
| **Null Keys/Values** | ❌ Not allowed | ✅ Allowed |
| **Iteration** | Weakly consistent (no ConcurrentModificationException) | Requires external sync |
| **Use Case** | High-concurrency apps | Legacy code |

---

### 📌 Interview Answer
> _"ConcurrentHashMap uses fine-grained locking or CAS for high concurrency. synchronizedMap locks the entire map, causing contention. I use ConcurrentHashMap in caches or high-write systems. It doesn’t allow nulls, but that’s a small price for performance."_  

---

## 🔹 7. What is the Fork/Join Framework? When would you use it?

> A framework for **parallel processing** using **work-stealing**.

Ideal for **divide-and-conquer** algorithms.

```java
public class SumTask extends RecursiveTask<Long> {
    private long[] array;
    private int start, end;

    public SumTask(long[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }

    protected Long compute() {
        if (end - start <= 1000) {
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            int mid = (start + end) / 2;
            SumTask left = new SumTask(array, start, mid);
            SumTask right = new SumTask(array, mid, end);
            left.fork();
            return right.compute() + left.join();
        }
    }
}

// Usage
ForkJoinPool pool = new ForkJoinPool();
long sum = pool.invoke(new SumTask(data, 0, data.length));
```

✅ Best for **CPU-intensive**, **recursive** tasks

---

### 📌 Interview Answer
> _"I use Fork/Join for parallel processing of large datasets — like summing an array or sorting. It uses work-stealing to balance load. For simple parallelism, I might use parallel streams instead."_  

---

## 🔹 8. What is metaspace? How is it different from PermGen?

| Feature | **PermGen (Java 7 and earlier)** | **Metaspace (Java 8+)** |
|--------|-------------------------------|------------------------|
| **Location** | Heap | Native memory |
| **Size** | Fixed (`-XX:MaxPermSize`) | Grows by default |
| **Content** | Class metadata, interned strings | Class metadata only |
| **GC** | Part of heap GC | Separate GC |
| **Issues** | `OutOfMemoryError: PermGen space` | Rare, but `Metaspace` OOM possible |

✅ Metaspace is more flexible and scalable.

---

### 📌 Interview Answer
> _"Metaspace replaced PermGen in Java 8. It stores class metadata in native memory, not heap. It grows dynamically, reducing OOM errors. I monitor it in production and set -XX:MaxMetaspaceSize to prevent native memory exhaustion."_  

---

## 🔹 9. How do you detect and fix memory leaks in Java?

### ✅ Signs of Memory Leak
- Growing heap usage
- Frequent GC
- `OutOfMemoryError: Java heap space`

### ✅ Tools
- ✅ **VisualVM**, **JConsole** → Monitor heap
- ✅ **Eclipse MAT** → Analyze heap dumps
- ✅ **JFR (Java Flight Recorder)** → Production profiling

### ✅ Common Causes
| Cause | Fix |
|------|-----|
| ❌ Static collections | Use `WeakHashMap`, clear periodically |
| ❌ Unclosed resources | Use try-with-resources |
| ❌ Listeners not removed | Unregister in `dispose()` |
| ❌ Caches without eviction | Use `Guava Cache`, `Caffeine` |

---

### 📌 Interview Answer
> _"I use VisualVM to monitor heap and trigger a heap dump on OOM. In MAT, I look for 'dominators' — large objects or collections. Common leaks: static maps, unclosed streams, or listeners. I fix with try-with-resources, weak references, or bounded caches."_  

---

## 🔹 10. What is the difference between `@Component`, `@Service`, and `@Repository`?

All are **specializations of `@Component`** — but with **semantic meaning** and **additional features**.

| Annotation | Purpose | Special Behavior |
|----------|--------|------------------|
| `@Component` | Generic stereotype | None |
| `@Service` | Business logic layer | No extra behavior (semantic only) |
| `@Repository` | Data access layer | Automatic exception translation (e.g., SQL → `DataAccessException`) |

```java
@Repository
public class UserRepository {
    // SQLException → DataAccessException
}

@Service
public class UserService {
    @Autowired
    private UserRepository repo;
}
```

---

### 📌 Interview Answer
> _"@Component is a generic stereotype. @Service marks business logic, @Repository marks DAOs and enables Spring's exception translation. I use them for clarity and to let Spring handle data access exceptions."_  

---

## 🔹 11. What is the Spring Bean Lifecycle?

1. **Instantiate** → Create object
2. **Populate Properties** → Inject dependencies
3. **Set Bean Name** → `BeanNameAware`
4. **Set Bean Factory** → `BeanFactoryAware`
5. **Pre-Initialization** → `BeanPostProcessor.postProcessBeforeInitialization`
6. **Initialize** → `@PostConstruct`, `InitializingBean`
7. **Post-Initialization** → `BeanPostProcessor.postProcessAfterInitialization`
8. **In Use**
9. **Destroy** → `@PreDestroy`, `DisposableBean`

---

### 📌 Interview Answer
> _"Spring creates the bean, injects dependencies, calls @PostConstruct, and manages its lifecycle. I use @PostConstruct for initialization, @PreDestroy for cleanup. BeanPostProcessors can intercept creation. This lifecycle enables powerful features like AOP and transaction management."_  

---

## 🔹 12. How would you design a thread-safe cache in Java?

```java
public class ThreadSafeCache<K, V> {
    private final ConcurrentHashMap<K, V> cache = new ConcurrentHashMap<>();
    private final int maxSize;
    private final Queue<K> evictionQueue = new ConcurrentLinkedQueue<>();

    public ThreadSafeCache(int maxSize) {
        this.maxSize = maxSize;
    }

    public V get(K key) {
        return cache.get(key);
    }

    public void put(K key, V value) {
        if (cache.size() >= maxSize) {
            K oldKey = evictionQueue.poll();
            if (oldKey != null) {
                cache.remove(oldKey);
            }
        }
        cache.put(key, value);
        evictionQueue.offer(key);
    }
}
```

✅ Uses `ConcurrentHashMap` and `ConcurrentLinkedQueue`  
✅ Simple LRU-like eviction

---

### 📌 Interview Answer
> _"I’d use ConcurrentHashMap for thread-safe access. For eviction, I’d use a queue to track insertion order. For production, I’d use Caffeine or Guava Cache — they support TTL, size limits, and async loading."_  

---

## 🔹 13. What is the difference between `fail-fast` and `fail-safe` iterators?

| Iterator | Behavior | Example |
|--------|--------|--------|
| **Fail-Fast** | Throws `ConcurrentModificationException` if collection is modified during iteration | `ArrayList`, `HashMap` |
| **Fail-Safe** | Works on a copy — no exception | `ConcurrentHashMap`, `CopyOnWriteArrayList` |

```java
List<String> list = new ArrayList<>(Arrays.asList("a", "b", "c"));
for (String s : list) {
    list.add("x"); // → ConcurrentModificationException
}
```

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>(Arrays.asList("a", "b", "c"));
for (String s : list) {
    list.add("x"); // ✅ No exception
}
```

---

### 📌 Interview Answer
> _"Fail-fast iterators throw ConcurrentModificationException if the collection is modified during iteration — good for detecting bugs. Fail-safe iterators (like in ConcurrentHashMap) work on a snapshot — safe for concurrent access. I choose based on use case: fail-fast for single-threaded, fail-safe for concurrent."_  

---

## 🔹 14. How does garbage collection work in Java?

### ✅ Generational GC
- **Young Generation** → Eden, S0, S1 (minor GC)
- **Old Generation** → Tenured (major GC)
- **Metaspace** → Class metadata

### ✅ GC Algorithms
| Algorithm | Use Case |
|---------|---------|
| **G1 GC** | Default (balanced) |
| **ZGC** | Low latency (<10ms pauses) |
| **Shenandoah** | Low pause time |
| **CMS (deprecated)** | Old low-latency |

---

### 📌 Interview Answer
> _"Java uses generational GC: most objects die young in Eden. Minor GCs are frequent and fast. Long-lived objects move to Old Gen. G1 is default — it partitions heap for efficient collection. I tune -Xmx, -Xms, and choose ZGC for low-latency systems."_  

---

## 🔹 15. What is the `record` keyword in Java 16+?

> A **record** is an **immutable data class** — boilerplate-free.

```java
public record User(String name, String email, int age) {}

// Usage
User user = new User("Alice", "a@x.com", 30);
System.out.println(user.name()); // getter
```

✅ Auto-generates:
- Constructor
- `equals()`, `hashCode()`, `toString()`
- Accessor methods (`name()`)

---

### 📌 Interview Answer
> _"Records are immutable data carriers. I use them for DTOs, config, or simple data transfer. They reduce boilerplate and make code more readable. I use them when I need a class that’s just data with no behavior."_  

---

---

## 🔹 16. What is the difference between `StringBuilder` and `StringBuffer`?

| Feature | `StringBuilder` | `StringBuffer` |
|--------|------------------|----------------|
| **Thread Safety** | ❌ Not synchronized | ✅ Synchronized |
| **Performance** | ✅ Faster (no locking) | ❌ Slower due to sync |
| **Use Case** | Single-threaded (most cases) | Multi-threaded (rare) |

```java
// ✅ Use StringBuilder in 99% of cases
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" World");

// ❌ StringBuffer (only if shared across threads)
StringBuffer buffer = new StringBuffer();
```

> ⚠️ `String` concatenation with `+` in loops → creates many objects → use `StringBuilder`.

---

### 📌 Interview Answer
> _"StringBuilder is faster and not thread-safe — I use it in single-threaded contexts like building SQL or JSON. StringBuffer is synchronized but slower — I avoid it unless shared across threads, which is rare. I always use StringBuilder in loops to avoid object creation."_  

---

## 🔹 17. How does `equals()` and `hashCode()` work together? Why must you override both?

> The **`equals()`-`hashCode()` contract** is critical for correct behavior in **hash-based collections** like `HashMap`, `HashSet`.

### ✅ Contract Rules
1. If `a.equals(b)`, then `a.hashCode() == b.hashCode()`
2. If `a.hashCode() != b.hashCode()`, then `!a.equals(b)`
3. `hashCode()` should be consistent across calls (unless object changes)

---

### ❌ Bad Example
```java
public class User {
    private String email;

    @Override
    public boolean equals(Object o) {
        // Proper equals
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User user = (User) o;
        return Objects.equals(email, user.email);
    }

    // ❌ Missing hashCode() → breaks HashMap
}
```

```java
Map<User, String> map = new HashMap<>();
map.put(new User("a@x.com"), "Alice");
map.get(new User("a@x.com")); // → null! (different hash codes)
```

---

### ✅ Good Example
```java
@Override
public int hashCode() {
    return Objects.hash(email);
}
```

✅ Now `get()` works correctly.

---

### 📌 Interview Answer
> _"If two objects are equal, they must have the same hash code. If I override equals(), I must override hashCode() to maintain this contract. I use Objects.equals() and Objects.hash() to implement them correctly. This ensures HashMaps and HashSets work properly."_  

---

## 🔹 18. What is the `try-with-resources` statement? How does it work?

> `try-with-resources` automatically **closes resources** that implement `AutoCloseable` or `Closeable`.

### ✅ Before (Verbose)
```java
InputStream is = null;
try {
    is = new FileInputStream("file.txt");
    // use is
} catch (IOException e) {
    // handle
} finally {
    if (is != null) {
        try {
            is.close();
        } catch (IOException e) {
            // log
        }
    }
}
```

### ✅ With try-with-resources
```java
try (InputStream is = new FileInputStream("file.txt")) {
    // use is
} catch (IOException e) {
    // handle
}
// is automatically closed
```

✅ Multiple resources:
```java
try (
    InputStream is = new FileInputStream("in.txt");
    OutputStream os = new FileOutputStream("out.txt")
) {
    // copy data
}
```

---

### 📌 Interview Answer
> _"try-with-resources ensures resources like streams, connections, and readers are closed automatically. It calls close() even if an exception occurs. I use it for all AutoCloseable resources — it’s cleaner and safer than finally blocks."_  

---

## 🔹 19. What is the `ThreadLocal` class? When would you use it?

> `ThreadLocal` provides **thread-local variables** — each thread has its own copy.

### ✅ Example: User Context
```java
public class UserContext {
    private static final ThreadLocal<String> userId = new ThreadLocal<>();

    public static void setUserId(String id) {
        userId.set(id);
    }

    public static String getUserId() {
        return userId.get();
    }

    public static void clear() {
        userId.remove();
    }
}
```

✅ In a web filter:
```java
// On request
UserContext.setUserId(extractFromToken(request));

// Later in service
String userId = UserContext.getUserId(); // Safe, thread-local

// On response
UserContext.clear(); // Prevent memory leak
```

---

### 📌 Best Practices
| Rule | Why |
|------|-----|
| ✅ Always call `remove()` | Prevents memory leaks in thread pools |
| ✅ Use for request-scoped data | User ID, transaction ID |
| ❌ Don’t overuse | Makes code hard to test and reason about |

---

### 📌 Interview Answer
> _"I use ThreadLocal to store request-scoped data like user ID in web apps. Each thread gets its own copy — safe in thread pools. But I always call remove() to prevent memory leaks. I avoid it when possible — dependency injection is cleaner."_  

---

## 🔹 20. What is the `Optional` class? How does it help avoid `NullPointerException`?

> `Optional<T>` is a **container** that may or may not contain a non-null value.

It encourages **explicit null handling**.

### ✅ Example
```java
public Optional<User> findUser(String email) {
    User user = userRepository.findByEmail(email);
    return Optional.ofNullable(user);
}

// Usage
Optional<User> user = service.findUser("a@x.com");
if (user.isPresent()) {
    System.out.println(user.get().getName());
}

// Better: Functional style
user.ifPresent(u -> System.out.println(u.getName()));
user.orElse(new User("Guest"));
user.orElseThrow(() -> new UserNotFoundException());
```

✅ Eliminates `null` checks and NPEs

---

### 📌 Interview Answer
> _"Optional forces callers to handle absence explicitly. I use it in return types for find methods. I avoid get() without check — use ifPresent, orElse, or orElseThrow. It makes APIs safer and more expressive."_  

---

## 🔹 21. What is the difference between `ClassNotFoundException` and `NoClassDefFoundError`?

| Exception | Type | Cause |
|---------|------|-------|
| `ClassNotFoundException` | **Checked Exception** | Class not found at **runtime** (e.g., `Class.forName("com.example.Missing")`) |
| `NoClassDefFoundError` | **Error** | Class was present at **compile time**, but **missing at runtime** (e.g., dependency not in classpath) |

### ✅ Example
```java
// ClassNotFoundException
try {
    Class.forName("com.example.NonExistent");
} catch (ClassNotFoundException e) {
    // Handle
}

// NoClassDefFoundError
// If 'Helper' class is removed after compilation
Helper.doSomething(); // → NoClassDefFoundError
```

---

### 📌 Interview Answer
> _"ClassNotFoundException is thrown when you try to load a class that doesn't exist at runtime — like with Class.forName(). NoClassDefFoundError means the class was there at compile time but missing at runtime — often due to a missing dependency. The first is recoverable, the second usually crashes the app."_  

---

## 🔹 22. How do you tune JVM for low-latency applications?

> For **low-latency** (e.g., trading systems), minimize GC pauses.

### ✅ Tuning Strategy

| Goal | JVM Flags |
|------|---------|
| ✅ **Low Pause Time** | `-XX:+UseZGC` or `-XX:+UseShenandoahGC` |
| ✅ **Predictable GC** | `-XX:+UnlockExperimentalVMOptions -XX:+UseZGC` |
| ✅ **Large Heap** | `-Xms16g -Xmx16g` (avoid resizing) |
| ✅ **Pre-touch Memory** | `-XX:+AlwaysPreTouch` |
| ✅ **Monitor GC** | `-Xlog:gc*:file=gc.log` |

```bash
java -XX:+UseZGC -Xms8g -Xmx8g -XX:+AlwaysPreTouch MyApp
```

---

### 📌 Best Practices
| Rule | Why |
|------|-----|
| ✅ Use ZGC or Shenandoah | Sub-10ms pauses |
| ✅ Avoid object allocation in hot paths | Reduce GC pressure |
| ✅ Use object pooling (carefully) | For expensive objects |
| ✅ Monitor GC logs | Detect issues early |

---

### 📌 Interview Answer
> _"I use ZGC for sub-10ms pauses, set fixed heap size, and pre-touch memory. I minimize object creation in critical paths and use off-heap or pooling if needed. I monitor GC logs to ensure no long pauses. For trading systems, every millisecond counts."_  

---

## 🔹 23. What is the `module-info.java` file in Java 9+?

> `module-info.java` defines a **module** — a **named, self-describing** unit of code.

### ✅ Example
```java
module com.example.app {
    requires java.base;
    requires java.logging;
    requires com.fasterxml.jackson.databind;

    exports com.example.service;
    exports com.example.model;
}
```

### ✅ Benefits
- ✅ **Strong encapsulation** (private packages not accessible)
- ✅ **Reliable configuration** (no classpath issues)
- ✅ **Improved performance** (faster class loading)

---

### 📌 Interview Answer
> _"module-info.java defines a module — its dependencies and exported packages. It enables strong encapsulation and reliable configuration. I use it in large apps to enforce clean boundaries and reduce classpath issues. It's part of Project Jigsaw — Java's modularity system."_  

---

## 🔹 24. How does Spring Boot auto-configuration work?

> Spring Boot **auto-configures** your app based on:
- ✅ Classpath dependencies
- ✅ Presence of beans
- ✅ Properties

### ✅ How It Works
1. `@SpringBootApplication` → `@EnableAutoConfiguration`
2. Scans `META-INF/spring.factories` for `org.springframework.boot.autoconfigure.EnableAutoConfiguration`
3. Loads auto-configuration classes (e.g., `DataSourceAutoConfiguration`)
4. Applies them **only if conditions met** (`@ConditionalOnClass`, `@ConditionalOnMissingBean`)

### ✅ Example
```java
@Configuration
@ConditionalOnClass(DataSource.class)
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {
    @Bean
    @ConditionalOnMissingBean
    public DataSource dataSource() {
        // Auto-create DataSource
    }
}
```

---

### 📌 Interview Answer
> _"Spring Boot auto-configuration uses @Conditional annotations to configure beans based on classpath and existing beans. If H2 is on classpath, it auto-configures a DataSource. I can override defaults with my own @Bean. It reduces boilerplate and enables convention over configuration."_  

---

## 🔹 25. What is the difference between `@Autowired` and `@Resource`?

| Annotation | Source | Injection Type | Lookup Order |
|----------|--------|---------------|-------------|
| `@Autowired` | **Spring** | By **type** | Type → Qualifier |
| `@Resource` | **JSR-250 (Java EE)** | By **name** | Name → Type |

### ✅ Example
```java
@Autowired
private DataSource dataSource; // By type

@Resource
private DataSource dataSource; // By name (looks for 'dataSource')

@Resource(name = "primaryDS")
private DataSource dataSource; // By name
```

---

### 📌 Interview Answer
> _"@Autowired is Spring-specific and injects by type. @Resource is Java EE standard and injects by name. I use @Autowired with @Qualifier for clarity. @Resource is useful when I want to inject by bean name directly."_  

---

## 🔹 26. What is the `CompletableFuture` class? How is it better than `Future`?

> `CompletableFuture` is a **composable asynchronous programming** class.

### ✅ Before: `Future`
```java
Future<String> future = executor.submit(() -> fetchUser());
String result = future.get(); // Blocks
```

### ✅ With `CompletableFuture`
```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> fetchUser())
    .thenApply(user -> user.toUpperCase())
    .thenApply(String::length)
    .exceptionally(ex -> -1);

// Non-blocking
future.thenAccept(System.out::println);
```

✅ Composable, non-blocking, supports pipelines

---

### 📌 Interview Answer
> _"CompletableFuture is better than Future because it's composable — I can chain async operations with thenApply, thenCompose, handle errors with exceptionally. It supports non-blocking pipelines and is great for microservices orchestration. I use it for parallel calls and aggregating results."_  

---

## 🔹 27. How would you design a rate limiter in Java?

```java
public class RateLimiter {
    private final int limit;
    private final long windowMs;
    private final Queue<Long> timestamps = new ConcurrentLinkedQueue<>();

    public RateLimiter(int limit, long windowMs) {
        this.limit = limit;
        this.windowMs = windowMs;
    }

    public synchronized boolean allow() {
        long now = System.currentTimeMillis();
        // Remove timestamps outside window
        while (!timestamps.isEmpty() && timestamps.peek() < now - windowMs) {
            timestamps.poll();
        }
        if (timestamps.size() < limit) {
            timestamps.offer(now);
            return true;
        }
        return false;
    }
}
```

✅ Sliding window rate limiter  
✅ Thread-safe with `synchronized`

---

### 📌 Interview Answer
> _"I implement a sliding window rate limiter using a queue to track request timestamps. I remove old entries and check count. For high scale, I'd use Redis with INCR and EXPIRE. I make it thread-safe with synchronization or use AtomicInteger for fixed windows."_  

---

## 🔹 28. What is the `jcmd` tool? How do you use it?

> `jcmd` is a **command-line tool** to send diagnostic commands to the JVM.

### ✅ Common Uses
```bash
# List running Java processes
jcmd

# Print heap histogram
jcmd <pid> GC.run_finalization
jcmd <pid> GC.class_histogram

# Take heap dump
jcmd <pid> GC.run
jcmd <pid> VM.gc
jcmd <pid> VM.class_hierarchy
jcmd <pid> Thread.print

# Enable JFR
jcmd <pid> JFR.start
jcmd <pid> JFR.dump filename=recording.jfr
jcmd <pid> JFR.stop
```

✅ Lightweight alternative to VisualVM

---

### 📌 Interview Answer
> _"jcmd is a powerful CLI tool for JVM diagnostics. I use it to take heap dumps, print thread stacks, and start JFR recordings in production. It's lightweight and doesn't require GUI tools. I use it when I need quick insights without attaching a profiler."_  

---

## 🔹 29. What is the difference between `@Controller` and `@RestController`?

| Annotation | Purpose | Response Body |
|----------|--------|---------------|
| `@Controller` | MVC controller | Must use `@ResponseBody` per method |
| `@RestController` | REST controller | All methods return JSON/XML by default |

```java
@Controller
public class PageController {
    @GetMapping("/home")
    public String home() {
        return "home"; // View name
    }
}

@RestController
public class ApiRestController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.findAll(); // JSON
    }
}
```

✅ `@RestController = @Controller + @ResponseBody`

---

### 📌 Interview Answer
> _"@Controller is for MVC apps that return views. @RestController is for REST APIs — it automatically serializes return values to JSON. I use @RestController for APIs and @Controller for server-rendered pages."_  

---

## 🔹 30. How do you secure a Spring Boot application?

| Layer | Security Measure |
|------|------------------|
| ✅ **Authentication** | Spring Security + JWT or OAuth2 |
| ✅ **Authorization** | `@PreAuthorize("hasRole('ADMIN')")` |
| ✅ **Input Validation** | `@Valid` with Bean Validation |
| ✅ **Headers** | `spring-boot-starter-security` + `HttpSecurity` |
| ✅ **Rate Limiting** | Custom filter or Redis |
| ✅ **Secrets** | `application.yml` + profile-specific files or Vault |
| ✅ **HTTPS** | Redirect HTTP to HTTPS |
| ✅ **CORS** | Configure `CorsConfigurationSource` |

---

### 📌 Interview Answer
> _"I secure Spring Boot with Spring Security — JWT for stateless auth, OAuth2 for social login. I use @PreAuthorize for role checks, validate input with @Valid, and enable CSRF and CORS. I store secrets in environment variables or HashiCorp Vault. I also add rate limiting and enforce HTTPS."_  

---

## ✅ Final Tip

> 🎯 In interviews, **show depth and balance**:
> _"I use records for DTOs, CompletableFuture for async, and ZGC for low latency. But I avoid over-engineering — simplicity wins."_  

That shows **senior judgment**.

---
