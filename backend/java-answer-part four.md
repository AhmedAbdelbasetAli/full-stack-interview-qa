# 🧠 Java Deep Dive: Collections & Multithreading  
**HashMap, ArrayList, Concurrency, and Threads**

This document covers **advanced Java concepts** — critical for **interviews**, **real-world development**, and **senior-level roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Memory/execution model
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 16. What is the `HashMap` in Java? How does it work internally?

> `HashMap<K, V>` is a **key-value store** that provides **average O(1)** time for `get()` and `put()`.

It’s based on **hashing** and uses **buckets** to store data.

---

### 🔹 16.1 How `HashMap` Works Internally

```
Key → hashCode() → Hash → Index → Bucket → Node (Key-Value)
```

#### 🟦 Step 1: Hashing
```java
int hash = key.hashCode();
// Then perturbed to reduce collisions
hash = (h = key.hashCode()) ^ (h >>> 16);
```

#### 🟦 Step 2: Index Calculation
```java
index = (table.length - 1) & hash;
```
- `table.length` is always a **power of 2**
- `&` is faster than `%`

#### 🟦 Step 3: Bucket & Collision Handling
- Each bucket holds a **linked list** (or tree) of `Node<K,V>` entries
- If two keys hash to the same bucket → **chaining**

```text
Bucket 0: [Node("Aa", 1)] → [Node("BB", 2)]
Bucket 1: [Node("C", 3)]
```

#### 🟦 Step 4: Treeify (Java 8+)
- If a bucket has **≥ 8 entries** and table size ≥ 64 → convert list to **Red-Black Tree**
- Changes lookup from **O(n)** to **O(log n)**

#### 🟦 Step 5: Resizing
- When size > `capacity * loadFactor` (default 0.75) → **resize**
- Create new table (2x size), rehash all entries

✅ Resizing is **O(n)** but amortized **O(1)** per operation.

---

### 🔹 16.2 Key Rules for Keys

- ✅ Override `equals()` and `hashCode()`
- ✅ `hashCode()` must be consistent with `equals()`
- ✅ Use **immutable keys** (e.g., `String`, `Integer`)

> ❌ If key changes after `put()`, `get()` may fail

---

### 📌 Interview Answer

> _"HashMap uses an array of buckets. It calculates the bucket index using `(length-1) & hash`. Collisions are handled with chaining — a linked list of nodes. In Java 8+, long chains are converted to Red-Black Trees for O(log n) worst-case performance. It resizes when load factor exceeds 0.75. I always use immutable keys and override `equals()` and `hashCode()` correctly."_  

---

## 17. What is the difference between `HashMap` and `HashTable`?

| Feature | `HashMap` | `HashTable` |
|--------|-----------|------------|
| **Thread Safety** | ❌ No | ✅ Yes (`synchronized`) |
| **Null Keys/Values** | ✅ One null key, multiple null values | ❌ No |
| **Performance** | Fast | Slower (due to sync) |
| **Iteration** | Fail-fast (throws `ConcurrentModificationException`) | Not fail-fast |
| **Inheritance** | Part of `java.util` | Extends `Dictionary` (legacy) |
| **Use Case** | General use | Legacy code only |

---

### 🔹 17.1 `HashMap` – Modern & Fast

```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put(null, 2); // ✅ OK
```

✅ Use in:
- Single-threaded apps
- With `Collections.synchronizedMap()` if needed

---

### 🔹 17.2 `HashTable` – Legacy & Synchronized

```java
Map<String, Integer> table = new Hashtable<>();
table.put("A", 1);
table.put(null, 2); // ❌ NullPointerException
```

✅ All methods are `synchronized` → thread-safe but slow

❌ Use `ConcurrentHashMap` instead

---

### 📌 Interview Answer

> _"HashMap is not thread-safe but fast; it allows one null key and multiple null values. HashTable is thread-safe due to synchronized methods, but slower and doesn't allow nulls. HashTable is legacy — I use ConcurrentHashMap for thread-safe maps. HashMap is the standard for most use cases."_  

---

## 18. What is the difference between `ArrayList` and `LinkedList`?

| Feature | `ArrayList` | `LinkedList` |
|--------|-------------|--------------|
| **Underlying Structure** | Dynamic array | Doubly-linked list |
| **Random Access** | O(1) | O(n) |
| **Insert/Delete at End** | Amortized O(1) | O(1) |
| **Insert/Delete in Middle** | O(n) (shift elements) | O(1) if node known |
| **Memory Overhead** | Low | High (two pointers per node) |
| **Use Case** | Frequent reads, iteration | Frequent insertions/deletions |

---

### 🔹 18.1 `ArrayList` – Dynamic Array

```java
List<String> list = new ArrayList<>();
list.add("A"); // O(1) amortized
String s = list.get(0); // O(1)
list.remove(0); // O(n) — shifts all elements
```

✅ Pros:
- Fast random access
- Cache-friendly (contiguous memory)
- Low memory overhead

❌ Cons:
- Resizing (copy to new array)
- Slow middle removal

---

### 🔹 18.2 `LinkedList` – Doubly-Linked List

```java
List<String> list = new LinkedList<>();
list.add("A"); // O(1)
String s = list.get(0); // O(1) → but list.get(n) is O(n)
list.removeFirst(); // O(1)
```

✅ Pros:
- Fast insert/delete at ends
- No resizing
- Efficient for queues (`addFirst`, `removeLast`)

❌ Cons:
- Slow random access
- High memory (prev/next pointers)
- Poor cache locality

---

### 📌 Interview Answer

> _"ArrayList uses a dynamic array — fast for random access and iteration, but slow for middle insertions. LinkedList uses a doubly-linked list — efficient for frequent insertions/deletions at ends, but slow for random access. I use ArrayList 90% of the time due to better performance and memory. LinkedList is better for queues or when using ListIterator for bidirectional traversal."_  

---

## 19. What is the `ConcurrentModificationException`? How can you avoid it?

> `ConcurrentModificationException` is thrown when a **collection is modified while being iterated**, except through the iterator’s own `remove()` method.

---

### 🔹 19.1 When It Occurs

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// ❌ Throws ConcurrentModificationException
for (String s : list) {
    if (s.equals("B")) {
        list.remove(s); // Modifies list during iteration
    }
}
```

✅ The iterator detects modification via `modCount` (modification count)

---

### 🔹 19.2 How to Avoid It

#### 🟦 1. Use `Iterator.remove()`
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("B")) {
        it.remove(); // ✅ Safe
    }
}
```

#### 🟦 2. Use `removeIf()` (Java 8+)
```java
list.removeIf(s -> s.equals("B")); // ✅ Safe and clean
```

#### 🟦 3. Use `CopyOnWriteArrayList` (for reads > writes)
```java
List<String> list = new CopyOnWriteArrayList<>(Arrays.asList("A", "B", "C"));
// Safe to modify during iteration — but expensive
```

#### 🟦 4. Collect and Remove After
```java
List<String> toRemove = list.stream()
    .filter(s -> s.equals("B"))
    .collect(Collectors.toList());
list.removeAll(toRemove);
```

---

### 📌 Interview Answer

> _"ConcurrentModificationException occurs when a collection is modified during iteration, except via the iterator's remove method. I avoid it by using Iterator.remove(), removeIf(), or collecting items to remove later. For multi-threaded scenarios, I use CopyOnWriteArrayList, but only when reads far exceed writes due to its high cost."_  

---

## 20. What is multithreading in Java? How do you create and manage threads?

> **Multithreading** allows a program to perform **multiple tasks concurrently** — improving performance and responsiveness.

Java supports multithreading via the `Thread` class and `ExecutorService`.

---

### 🔹 20.1 Why Use Multithreading?

| Benefit | Example |
|--------|--------|
| ✅ **Performance** | Process data in parallel |
| ✅ **Responsiveness** | UI doesn’t freeze during long tasks |
| ✅ **Resource Utilization** | Use all CPU cores |

---

### 🔹 20.2 Creating Threads

#### 🟦 Method 1: Extend `Thread`
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running in thread");
    }
}

MyThread t = new MyThread();
t.start(); // Don't call run() directly
```

#### 🟦 Method 2: Implement `Runnable`
```java
class Task implements Runnable {
    public void run() {
        System.out.println("Task running");
    }
}

Thread t = new Thread(new Task());
t.start();
```

✅ Prefer `Runnable` — separates task from thread mechanism

#### 🟦 Method 3: Lambda (Java 8+)
```java
new Thread(() -> System.out.println("Hello")).start();
```

---

### 🔹 20.3 Using `ExecutorService` (Recommended)

> Thread pool for efficient thread management.

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

executor.submit(() -> {
    System.out.println("Task in thread pool");
});

executor.shutdown(); // Graceful shutdown
```

✅ Benefits:
- Reuse threads
- Control pool size
- Better resource management

---

### 🔹 20.4 Thread Lifecycle

```
New → Runnable → Running → Blocked/Waiting → Terminated
```

---

### 🔹 20.5 Synchronization

> Prevent race conditions with `synchronized`:

```java
synchronized void increment() {
    count++; // Atomic
}
```

Or use `ReentrantLock`, `AtomicInteger`, etc.

---

### 📌 Interview Answer

> _"Multithreading allows concurrent execution for better performance and responsiveness. I create threads by implementing Runnable or using ExecutorService with a thread pool. I prefer ExecutorService for production — it manages threads efficiently. I use synchronization or atomic classes to handle shared data safely. I avoid extending Thread directly to separate task from thread mechanism."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use HashMap for fast lookups, avoid ConcurrentModificationException with removeIf(), and manage threads with ExecutorService."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



Just say: _"Let’s do [topic]"_

You're mastering Java at a **senior level**. 💪
