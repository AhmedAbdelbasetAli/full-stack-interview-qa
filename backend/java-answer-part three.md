# 🧠 Java Deep Dive: Core Concepts (Part 3)  
**Main Method, Exception Handling, and Garbage Collection**

This document completes the Java core concepts series — covering **entry points, error handling, and memory management**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Memory/execution model
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 11. What is the `main` method? Why is it `public static void`?

> The `main` method is the **entry point** of a Java application.

```java
public static void main(String[] args) {
    System.out.println("Hello, World!");
}
```

---

### 🔹 11.1 Why `public`?

> So the **JVM can access it from outside the class**.

- The JVM is not part of your class
- It needs `public` visibility to call `main()`

✅ If `main` were `private`, JVM couldn't call it → **"Main method not found"**

---

### 🔹 11.2 Why `static`?

> So the **JVM can call it without creating an object**.

- No instance of the class exists when the program starts
- `static` methods belong to the class, not instances

✅ If `main` weren't `static`, JVM would need to:
1. Load the class
2. Create an object
3. Call `main()` on it

❌ But how? No constructor parameters? No way to know which constructor to use.

---

### 🔹 11.3 Why `void`?

> Because the `main` method **does not return a value** to the JVM.

- The JVM starts the program, runs `main`, and exits
- No need to return anything

✅ Other return types (`int`, `String`) are invalid

---

### 🔹 11.4 Why `String[] args`?

> To **pass command-line arguments** to the program.

```bash
java MyApp Alice 30
```

```java
public static void main(String[] args) {
    System.out.println("Name: " + args[0]); // Alice
    System.out.println("Age: " + args[1]);  // 30
}
```

✅ `args` is an array of strings passed from the command line.

---

### 🔹 11.5 Alternative Syntaxes (All Valid)

```java
public static void main(String[] args)
public static void main(String args[])
public static void main(String... args) // varargs
static public void main(String[] args) // order doesn't matter
```

✅ But `public static void main(String[] args)` is the **standard**.

---

### 📌 Interview Answer

> _"The `main` method is the entry point of a Java application. It must be `public` so the JVM can access it, `static` so it can be called without creating an object, and `void` because it doesn't return a value. The `String[] args` parameter allows command-line arguments to be passed. This signature is required — if any part is missing, the JVM won't recognize it as the entry point."_  

---

## 12. What is exception handling in Java? Explain `try`, `catch`, `finally`, and `throw`.

> **Exception handling** is Java’s mechanism for **dealing with runtime errors** (like file not found, division by zero) in a **structured way**.

It prevents the program from crashing and allows graceful recovery.

---

### 🔹 12.1 `try` – Wrap Risky Code

> Enclose code that might throw an exception.

```java
try {
    int result = 10 / 0; // ArithmeticException
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

✅ Only one `try` block per `catch`/`finally`

---

### 🔹 12.2 `catch` – Handle the Exception

> Catch and handle specific exceptions.

```java
try {
    int[] arr = new int[5];
    arr[10] = 1; // ArrayIndexOutOfBoundsException
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Index out of bounds");
}
```

#### 🟦 Multiple Catch Blocks
```java
try {
    // ...
} catch (IOException e) {
    // Handle IO error
} catch (SQLException e) {
    // Handle DB error
}
```

✅ Specific exceptions first (e.g., `FileNotFoundException` before `IOException`)

---

### 🔹 12.3 `finally` – Always Execute

> Code that runs **regardless of whether an exception occurred**.

```java
FileInputStream fis = null;
try {
    fis = new FileInputStream("file.txt");
    // Read file
} catch (IOException e) {
    System.out.println("File error");
} finally {
    if (fis != null) {
        try {
            fis.close(); // Always close
        } catch (IOException e) {
            System.out.println("Failed to close");
        }
    }
}
```

✅ Use for cleanup: closing files, connections, etc.

---

### 🔹 12.4 `throw` – Throw an Exception

> Manually throw an exception.

```java
if (age < 0) {
    throw new IllegalArgumentException("Age cannot be negative");
}
```

✅ Can throw:
- Built-in exceptions (`IllegalArgumentException`, `NullPointerException`)
- Custom exceptions

---

### 🔹 12.5 `throws` – Declare Exception

> Indicate that a method might throw an exception.

```java
public void readFile() throws IOException {
    FileReader fr = new FileReader("file.txt");
}
```

✅ Required for **checked exceptions**

---

### 📌 Interview Answer

> _"Exception handling in Java uses `try` to wrap risky code, `catch` to handle specific exceptions, `finally` to run cleanup code (like closing files), and `throw` to manually throw exceptions. `throws` declares that a method might throw a checked exception. This allows programs to handle errors gracefully instead of crashing. I always use `try-with-resources` when possible to auto-close resources."_  

---

## 13. What is the difference between checked and unchecked exceptions?

| Feature | Checked Exceptions | Unchecked Exceptions |
|--------|---------------------|-----------------------|
| **Compile-Time Check** | ✅ Yes | ❌ No |
| **Inheritance** | `Exception` (but not `RuntimeException`) | `RuntimeException` or `Error` |
| **Handling** | Must be caught or declared | Can be caught, but not required |
| **Examples** | `IOException`, `SQLException` | `NullPointerException`, `ArrayIndexOutOfBoundsException` |
| **Use Case** | Recoverable conditions | Programming errors |

---

### 🔹 13.1 Checked Exceptions – "Handle or Declare"

> Must be **caught or declared** with `throws`.

```java
public void readFile() throws IOException {
    FileReader fr = new FileReader("file.txt"); // Checked exception
}
```

✅ Use for:
- Recoverable conditions (file not found, network error)
- Situations where the caller can reasonably handle the error

---

### 🔹 13.2 Unchecked Exceptions – Runtime Errors

> Extend `RuntimeException` — **not checked at compile time**.

```java
int[] arr = new int[5];
arr[10] = 1; // ArrayIndexOutOfBoundsException (unchecked)
```

✅ Use for:
- Programming bugs (null reference, invalid argument)
- Conditions that should be prevented by proper coding

---

### 🔹 13.3 `Error` – Severe System Problems

> Extend `Error` — **should not be caught**.

- `OutOfMemoryError`
- `StackOverflowError`

✅ These indicate **serious JVM problems** — recovery is usually impossible.

---

### 📌 Interview Answer

> _"Checked exceptions are checked at compile time — you must catch them or declare with `throws`. They represent recoverable conditions like file not found. Unchecked exceptions extend `RuntimeException` and are not checked — they represent programming errors like null pointer or array out of bounds. I use checked exceptions for external failures, and avoid catching `Error` types like `OutOfMemoryError`."_  

---

## 14. What is the `finally` block? Is it always executed?

> The `finally` block contains code that **runs after `try-catch`, regardless of exceptions**.

---

### 🔹 14.1 When Is `finally` Executed?

| Scenario | `finally` Runs? |
|--------|-----------------|
| ✅ No exception | Yes |
| ✅ Exception caught | Yes |
| ✅ Exception not caught | Yes |
| ✅ `return` in `try` | Yes (after `return` is delayed) |
| ✅ `System.exit(0)` | ❌ No |
| ✅ JVM crash | ❌ No |
| ✅ Infinite loop in `try` | ❌ No (never exits `try`) |

---

### 🔹 14.2 Example: `return` vs `finally`

```java
public static int getValue() {
    try {
        return 1;
    } finally {
        System.out.println("Finally runs");
        // return 2; // ❌ Don't do this — overrides try's return
    }
}
// Output: "Finally runs" → returns 1
```

✅ `finally` runs **before** the method returns.

---

### 🔹 14.3 `try-with-resources` (Java 7+)

> Automatic resource management — `close()` is called in `finally` behind the scenes.

```java
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // Use resource
} catch (IOException e) {
    // Handle error
}
// fis automatically closed
```

✅ Classes must implement `AutoCloseable`.

---

### 📌 Interview Answer

> _"The `finally` block runs after `try-catch`, even if an exception occurs or a `return` statement is executed. It's ideal for cleanup, like closing files or connections. It doesn't run only if the JVM exits via `System.exit()` or crashes. I prefer `try-with-resources` for auto-closing, but use `finally` for legacy code or complex cleanup logic."_  

---

## 15. What is garbage collection in Java? How does it work?

> **Garbage Collection (GC)** is the **automatic memory management** system in Java that **reclaims memory** from objects that are no longer reachable.

It prevents memory leaks and manual `free()` calls (like in C/C++).

---

### 🔹 15.1 How Garbage Collection Works

```
Objects in Heap → Reachability Analysis → Unreachable → GC → Memory Reclaimed
```

#### 🟦 Step 1: Object Creation
```java
User user = new User("Alice"); // Object in heap, referenced by 'user'
```

#### 🟦 Step 2: Object Becomes Unreachable
```java
user = null; // No references → eligible for GC
```

#### 🟦 Step 3: GC Runs
- JVM runs GC when memory is low
- Identifies unreachable objects
- Reclaims memory

---

### 🔹 15.2 Reachability Rules

An object is **eligible for GC** if:
- No **strong reference** to it
- Not reachable via any chain from **GC Roots** (e.g., static variables, local variables on stack)

```java
void method() {
    User u1 = new User("A");
    User u2 = new User("B");
    u1 = u2; // Original "A" becomes unreachable
    // "A" eligible for GC
}
```

---

### 🔹 15.3 Generational Garbage Collection

Java uses **generational GC** based on the **weak generational hypothesis**:
> Most objects die young.

#### 🟦 Memory Regions
| Region | Purpose |
|------|--------|
| **Young Generation** | New objects |
| - Eden Space | New objects created here |
| - S0, S1 (Survivor Spaces) | Objects that survive GC |
| **Old Generation (Tenured)** | Long-lived objects |
| **Metaspace** | Class metadata (replaces PermGen) |

#### 🟦 GC Types
| GC Type | Trigger | Impact |
|-------|--------|--------|
| **Minor GC** | Young Gen full | Fast, frequent |
| **Major GC / Full GC** | Old Gen full | Slower, pauses app |

---

### 🔹 15.4 GC Algorithms (Java 8+)

| Algorithm | Features |
|---------|----------|
| **G1 GC** (Default in Java 9+) | Low pause times, parallel |
| **ZGC** (Java 11+) | Ultra-low pause (<10ms), for large heaps |
| **Shenandoah** | Concurrent compaction |

---

### 🔹 15.5 You Can't Force GC

```java
System.gc(); // ❌ Suggestion only — no guarantee
Runtime.getRuntime().gc(); // Same
```

✅ GC is **automatic** — don’t rely on `System.gc()`

---

### 📌 Interview Answer

> _"Garbage Collection is Java’s automatic memory management system. It reclaims memory from unreachable objects using reachability analysis from GC roots. Java uses generational GC: new objects go to Young Generation (Eden), and long-lived ones move to Old Generation. Minor GC runs frequently on Young Gen; Full GC on Old Gen. Modern collectors like G1 and ZGC minimize pauses. I don’t call `System.gc()` — it’s just a suggestion. GC makes Java memory-safe but requires monitoring in production."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use `main` as entry point, handle exceptions with `try-catch-finally`, and trust GC for memory management."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



Just say: _"Let’s do [topic]"_

You're mastering Java at a **senior level**. 💪
