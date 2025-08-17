🔥 Absolutely! Here’s a **hand-curated list of tricky Java interview questions** — the kind that **look simple but trap juniors and mid-level developers**.

These questions test **deep understanding**, **attention to detail**, and **real-world experience** — not just syntax.

---

# 🧠 Tricky Java Interview Questions  
**Designed to Separate Juniors from Seniors**

Each question includes:
- ✅ The question
- ✅ What a junior says
- ✅ What a senior knows
- ✅ Explanation
- ✅ Why it’s tricky
- ✅ Interview tip

---

## 1. What does this code print?

```java
String s1 = "Hello";
String s2 = "Hello";
System.out.println(s1 == s2);
```

### ❌ Junior Answer:
> "It prints `false` because they are two objects."

### ✅ Senior Answer:
> "It prints `true` because both strings are in the **String Constant Pool** — they refer to the same object."

### 🔍 Explanation:
- `"Hello"` is a **string literal** → stored in the **String Pool**
- `s1` and `s2` point to the **same object** in the pool
- `==` checks reference equality → `true`

✅ Try this:
```java
String s3 = new String("Hello");
System.out.println(s1 == s3); // false → heap object
```

### 🎯 Interview Tip:
> _"I know string literals are interned in the String Pool, so `==` returns `true` for identical literals. But I always use `.equals()` for string comparison to avoid bugs."_  

---

## 2. What is the output?

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);

Integer c = 128;
Integer d = 128;
System.out.println(c == d);
```

### ❌ Junior Answer:
> "Both are `true` — same values."

### ✅ Senior Answer:
> "First is `true`, second is `false`. JVM caches `Integer` values from -128 to 127."

### 🔍 Explanation:
- JVM **caches** `Integer` objects for values **-128 to 127**
- `127` → cached → same object → `==` is `true`
- `128` → new object each time → `==` is `false`

✅ This is called **Integer caching**.

### 🎯 Interview Tip:
> _"I never rely on `==` for `Integer` comparison. I use `.equals()` to avoid issues with caching. This is a common trap in performance-critical code."_  

---

## 3. Does this code throw `NullPointerException`?

```java
Integer num = null;
int primitive = num;
```

### ❌ Junior Answer:
> "No — it just assigns `null` to `primitive`."

### ✅ Senior Answer:
> "Yes — it throws `NullPointerException` during **unboxing**."

### 🔍 Explanation:
- `num` is `null`
- `int primitive = num` → calls `num.intValue()`
- `null.intValue()` → `NullPointerException`

✅ This is **autounboxing** — silent but dangerous.

### 🎯 Interview Tip:
> _"Autoboxing and unboxing are convenient, but unboxing a `null` wrapper throws NPE. I always check for `null` or use `Optional` to avoid this."_  

---

## 4. What is the output?

```java
public class Test {
    static {
        System.out.print("Static ");
    }
    {
        System.out.print("Instance ");
    }
    public Test() {
        System.out.print("Constructor ");
    }
    public static void main(String[] args) {
        new Test();
        new Test();
    }
}
```

### ❌ Junior Answer:
> "Static Instance Constructor Static Instance Constructor"

### ✅ Senior Answer:
> "Static Instance Constructor Instance Constructor"

### 🔍 Explanation:
- `static {}` block runs **once** when class is loaded
- `{}` (instance block) runs **before every constructor**
- Constructor runs after instance block

✅ Order:
1. Static block (once)
2. Instance block (on each `new`)
3. Constructor (on each `new`)

### 🎯 Interview Tip:
> _"Static blocks run once on class loading. Instance blocks run before every constructor call. I use static blocks for one-time initialization, like loading config files."_  

---

## 5. Can you override a `static` method?

```java
class Parent {
    static void method() {
        System.out.println("Parent");
    }
}
class Child extends Parent {
    static void method() {
        System.out.println("Child");
    }
}
// In main:
Parent p = new Child();
p.method();
```

### ❌ Junior Answer:
> "Yes — it prints 'Child' due to polymorphism."

### ✅ Senior Answer:
> "No — `static` methods **cannot be overridden**. This is **method hiding**. It prints 'Parent'."

### 🔍 Explanation:
- `static` methods belong to the **class**, not the object
- Call is resolved at **compile time** based on **reference type** (`Parent`)
- `Child.method()` exists, but `Parent p` → calls `Parent.method()`

✅ This is **not polymorphism**.

### 🎯 Interview Tip:
> _"Static methods are bonded at compile time — they can't be overridden. I avoid calling static methods on references. If I need runtime polymorphism, I use instance methods."_  

---

## 6. What is the output?

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

for (String s : list) {
    if (s.equals("B")) {
        list.remove(s);
    }
}
System.out.println(list);
```

### ❌ Junior Answer:
> "[A, C] — removes B."

### ✅ Senior Answer:
> "It throws `ConcurrentModificationException`."

### 🔍 Explanation:
- Enhanced `for` loop uses an **iterator**
- Modifying the list **during iteration** (except via `iterator.remove()`) → fails-fast
- Iterator detects `modCount` mismatch

✅ Fix:
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("B")) {
        it.remove(); // ✅ Safe
    }
}
```

Or:
```java
list.removeIf(s -> s.equals("B")); // ✅ Java 8+
```

### 🎯 Interview Tip:
> _"I never modify a collection during enhanced for-loop iteration. I use `Iterator.remove()` or `removeIf()` to avoid `ConcurrentModificationException`."_  

---

## 7. Is this code thread-safe?

```java
class Counter {
    int count = 0;
    void increment() {
        count++;
    }
}
```

### ❌ Junior Answer:
> "Yes — it’s a simple operation."

### ✅ Senior Answer:
> "No — `count++` is not atomic. It’s read → modify → write. Multiple threads can interfere."

### 🔍 Explanation:
- `count++` → three operations:
  1. Read `count`
  2. Increment
  3. Write back
- Two threads can read the same value → both write same incremented value → **lost update**

✅ Fix:
```java
synchronized void increment() { ... }
// or
AtomicInteger count = new AtomicInteger();
count.incrementAndGet();
```

### 🎯 Interview Tip:
> _"Even simple operations like `i++` aren’t thread-safe. I use `synchronized`, `Atomic` classes, or `ReentrantLock` for shared state in multithreaded environments."_  

---

## 8. What is the output?

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(getString());
    }
    static String getString() {
        try {
            return "try";
        } finally {
            return "finally";
        }
    }
}
```

### ❌ Junior Answer:
> "try"

### ✅ Senior Answer:
> "finally"

### 🔍 Explanation:
- `finally` **overrides** the `try` return
- If `finally` has a `return`, it **takes precedence**
- Same for `catch`

✅ This is a **tricky edge case** — avoid `return` in `finally`

### 🎯 Interview Tip:
> _"I never use `return` in `finally` blocks — it can override the try/catch return. I use `finally` only for cleanup, like closing resources."_  

---

## 9. Can you start a thread twice?

```java
Thread t = new Thread(() -> System.out.println("Hello"));
t.start();
t.start(); // ?
```

### ❌ Junior Answer:
> "Yes — it runs the task again."

### ✅ Senior Answer:
> "No — it throws `IllegalThreadStateException`."

### 🔍 Explanation:
- Once a thread is started, its state becomes `RUNNABLE`
- Calling `start()` again → invalid state transition
- Thread can only be started **once**

✅ To run multiple times, create new `Thread` objects.

### 🎯 Interview Tip:
> _"A thread can only be started once. I use `ExecutorService` instead of raw threads for better lifecycle management."_  

---

## 10. What is the output?

```java
String s = "hello";
s.toUpperCase();
System.out.println(s);
```

### ❌ Junior Answer:
> "HELLO"

### ✅ Senior Answer:
> "hello"

### 🔍 Explanation:
- `String` is **immutable**
- `toUpperCase()` returns a **new string**
- Original `s` is unchanged

✅ Fix:
```java
s = s.toUpperCase();
```

### 🎯 Interview Tip:
> _"Strings are immutable — any modification returns a new object. I always reassign the result of string operations."_  

---


Just say: _"Let’s do [topic]"_

You're not just coding — you're **thinking like a senior engineer**. 💪
