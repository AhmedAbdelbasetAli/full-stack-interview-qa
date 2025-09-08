You’re about to master **Stack and Queue** — not as academic toys, but as **production-grade, high-performance tools** used in Spring, Android, Kafka, and every major system.

And yes — we’ll expose `ArrayDeque` as the **hidden gem** Java engineers overlook.

—

# 🧱 1. What It Is — Stack & Queue

> **Stack Analogy**: A **stack of plates in a cafeteria** — you add/remove from the top only. Last In, First Out (LIFO).  
> **Queue Analogy**: A **movie ticket line** — first person in, first person out. First In, First Out (FIFO).

- **Core Purpose**:
  - **Stack**: Track nested state — function calls, undo, parsing.
  - **Queue**: Orderly processing — tasks, events, messages.

Java doesn’t have a dedicated `Stack` or `Queue` *class* — they’re **interfaces or behaviors** implemented by:

- `Stack<E>` → legacy, synchronized, avoid.
- `ArrayDeque<E>` → modern, fast, recommended.
- `LinkedList<E>` → implements both, but slow (we already killed it).
- `PriorityQueue<E>` → for priority-based, not FIFO.

```
STACK (LIFO)        QUEUE (FIFO)
   ↑ Top               ↑ Head
[  C  ]             [  A  ]
[  B  ]             [  B  ]
[  A  ]             [  C  ]
   ↓ Bottom            ↓ Tail
push/pop            add/poll
```

Used in:  
- **Spring**: `@Async` task queues → `ThreadPoolTaskExecutor` uses `BlockingQueue`.  
- **Android**: `Handler` message queue → Looper uses native queue.  
- **Web**: Request processing pipelines → queues.  
- **Compiler**: Expression parsing → stacks.

—

# ⚙️ 2. How It Works (Java Implementation)

## ✅ Stack — Use `ArrayDeque`, NOT `Stack`

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class StackExample {
    public static void main(String[] args) {
        Deque<String> stack = new ArrayDeque<>();

        // Push = add to top
        stack.push("A"); // O(1)
        stack.push("B");
        stack.push("C");

        // Peek = view top
        System.out.println(stack.peek()); // "C" — O(1)

        // Pop = remove top
        System.out.println(stack.pop());  // "C" — O(1)
        System.out.println(stack.pop());  // "B"

        // Size & isEmpty
        System.out.println(stack.size()); // 1
        System.out.println(stack.isEmpty()); // false
    }
}
```

> 🚫 **Never use `java.util.Stack`** — it’s synchronized, slow, and legacy.

## ✅ Queue — Use `ArrayDeque` or `LinkedList` (but prefer `ArrayDeque`)

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class QueueExample {
    public static void main(String[] args) {
        Queue<String> queue = new ArrayDeque<>();

        // Enqueue = add to tail
        queue.offer("A"); // O(1) — preferred over add() (no exception on full)
        queue.offer("B");
        queue.offer("C");

        // Peek = view head
        System.out.println(queue.peek()); // "A" — O(1)

        // Dequeue = remove head
        System.out.println(queue.poll()); // "A" — O(1)
        System.out.println(queue.poll()); // "B"

        // Size & isEmpty
        System.out.println(queue.size()); // 1
        System.out.println(queue.isEmpty()); // false
    }
}
```

## 🛠️ ArrayDeque Internals (The Hidden Gem)

- **Backed by circular array** — no resizing on every add/remove.
- **Head and tail pointers** wrap around → no shifting.
- **Doubles capacity** when full (like ArrayList).
- Implements **both `Deque` (double-ended queue) and `Queue`**.

```
Initial: [ ][ ][ ][ ]   head=0, tail=0
After add: [A][B][C][ ] head=0, tail=3
After poll: [ ][B][C][ ] head=1, tail=3
Add more:  [D][B][C][E] head=1, tail=0 (wrapped!)
```

### ⏱️ Time Complexity (Big-O) — for `ArrayDeque`

| Operation       | Stack (push/pop/peek) | Queue (offer/poll/peek) | Notes               |
|----------------|------------------------|--------------------------|---------------------|
| Insert (push/offer) | O(1) amortized        | O(1) amortized           | Resize rare         |
| Remove (pop/poll)   | O(1)                  | O(1)                     | No shifting         |
| Peek               | O(1)                  | O(1)                     | Instant             |
| contains(e)        | O(n)                  | O(n)                     | Avoid               |
| Size               | O(1)                  | O(1)                     | Stored as field     |

### 💾 Space Complexity

- O(n) — array + head/tail pointers. Minimal overhead. No per-element node objects → cache-friendly.

—

# 🎯 3. When to Use It

## ✅ Real-World Java Use Cases

### Stack

- **Expression evaluation** → `(2 + 3) * 4` → push operators/operands.
- **Undo/Redo** → push state, pop to undo.
- **DFS traversal** → iterative tree/graph DFS.
- **Browser history** → back/forward (though often uses doubly linked list).

> 💡 Android: `FragmentManager` uses stack for fragment backstack.

### Queue

- **Task scheduling** → Spring `@Async`, `ThreadPoolTaskExecutor`.
- **Event processing** → UI events, game loops.
- **BFS traversal** → level-order tree/graph traversal.
- **Producer-Consumer** → `BlockingQueue` in multi-threaded apps.

> 💡 Kafka consumers: poll messages → process → commit → poll again → classic queue pattern.

## ✅ Interview Scenarios

- **Valid Parentheses** → use stack to match `()`, `{}`, `[]`.
- **Implement Queue using Stacks** → LeetCode #232.
- **Sliding Window Maximum** → use `Deque` as monotonic queue (advanced).
- **Binary Tree Level Order Traversal** → BFS with queue.

## ✅ System Design Contexts

- **Async Processing**: Queue tasks → worker threads consume.
- **Rate Limiting**: Queue requests → leaky bucket.
- **Load Balancing**: Queue incoming requests → round-robin dispatch.
- **Circuit Breaker**: Queue fallback actions.

—

# 🚫 4. When NOT to Use It

## ❌ Anti-Patterns & Misuse

- **Using `java.util.Stack`** → synchronized, slow, legacy.

```java
// 🚫 NEVER
Stack<String> stack = new Stack<>();
```

- **Using `LinkedList` as queue** → memory-heavy, cache-unfriendly.

```java
// 🚫 AVOID (unless you need nulls or List behavior)
Queue<String> q = new LinkedList<>();
```

- **Using `add()` instead of `offer()`** → `add()` throws exception if queue is “full” (rare, but `offer()` returns `false` → safer).

- **Random access** → `ArrayDeque` doesn’t support `get(index)` → use `ArrayList` instead.

## 🐢 Performance Traps

- **Not pre-sizing** → if you know size, use `new ArrayDeque<>(initialCapacity)`.

```java
// ✅ Good
Deque<String> stack = new ArrayDeque<>(1000);
```

- **Using `contains()`** → O(n) scan → avoid.

## ✅ Better Alternatives

| Scenario                        | Better Choice             | Why                                      |
|--------------------------------|---------------------------|------------------------------------------|
| Need thread-safe stack/queue   | `ConcurrentLinkedDeque`   | Non-blocking, concurrent                 |
| Need blocking ops (producer-consumer) | `LinkedBlockingQueue` | wait/notify built-in                     |
| Need priority order            | `PriorityQueue`           | min/max heap                             |
| Need insertion-order iteration | `LinkedList` (rarely)     | Only if you also need List methods       |

—

# 💡 5. Pro Tips & Gotchas (Java-Specific)

## ⚠️ Thread Safety

- `ArrayDeque` is **NOT thread-safe**.
- For multi-threaded queues → use:
  - `ConcurrentLinkedDeque` — lock-free, non-blocking.
  - `LinkedBlockingQueue` — blocking, bounded/unbounded.

```java
// Thread-safe non-blocking
Deque<String> concurrentStack = new ConcurrentLinkedDeque<>();

// Thread-safe blocking (for producer-consumer)
BlockingQueue<String> taskQueue = new LinkedBlockingQueue<>();
```

## 📦 Memory & Performance

- `ArrayDeque` has **minimal overhead** — array + 2 ints (head, tail).
- No autoboxing issues unless your elements are primitives → then use third-party libs.

## 🏭 Enterprise Best Practices

- **In Spring Boot**:

```java
// For async:
@Async
public void processTask(String task) { ... }

// Backed by ThreadPoolTaskExecutor → uses BlockingQueue internally.
```

- **In Android**:

```java
// Handler for UI thread message queue
Handler mainHandler = new Handler(Looper.getMainLooper());
mainHandler.post(() -> updateUI());
```

- **Always prefer `ArrayDeque`** over `Stack` or `LinkedList` for stack/queue behavior.

## 🤯 Fun Fact

`ArrayDeque` is so efficient, it’s used internally in Java’s own `Collections.asLifoQueue(Deque)` and even in garbage collectors for traversal stacks.

—

# ✍️ 6. Mini Challenge (Java Coding Exercise)

## 🎯 Problem: Valid Parentheses

> Given a string `s` containing `'('`, `')'`, `'{'`, `'}'`, `'['`, `']'`, determine if it’s valid.  
> Open brackets must be closed in the correct order.  
> Example: `"()[]{}"` → `true`, `"([)]"` → `false`.

### 🧩 Starter Code

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ValidParentheses {
    public static boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '{' || c == '[') {
                stack.push(c); // push openers
            } else {
                if (stack.isEmpty()) return false; // no opener to match
                char top = stack.pop();
                // Check if opener matches closer
                if ((c == ')' && top != '(') ||
                    (c == '}' && top != '{') ||
                    (c == ']' && top != '[')) {
                    return false;
                }
            }
        }
        
        return stack.isEmpty(); // all openers closed?
    }

    public static void main(String[] args) {
        System.out.println(isValid("()[]{}")); // true
        System.out.println(isValid("([)]"));   // false
        System.out.println(isValid("{[]}"));   // true
    }
}
```

### 🧪 Optional: JUnit Test

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class ValidParenthesesTest {
    @Test
    void testIsValid() {
        assertTrue(ValidParentheses.isValid("()[]{}"));
        assertFalse(ValidParentheses.isValid("([)]"));
        assertTrue(ValidParentheses.isValid("{[]}"));
        assertFalse(ValidParentheses.isValid("(]"));
    }
}
```

> 💡 Time: O(n), Space: O(n) — worst case all openers.

—

