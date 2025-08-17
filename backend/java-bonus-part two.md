🔥 Absolutely! Here’s **Part 2** of **Tricky Java Interview Questions** — even more **mind-bending, subtle, and real-world** questions that **trip up juniors and mid-level developers**, but **seniors answer with confidence**.

These questions test **deep JVM knowledge**, **memory model**, **immutability**, **inheritance quirks**, and **common misconceptions**.

---

# 🧠 Tricky Java Interview Questions (Part 2)  
**Advanced Puzzlers for Senior-Level Thinking**

Each question includes:
- ✅ The question
- ❌ What a junior says
- ✅ What a senior knows
- 🔍 In-depth explanation
- 🎯 Interview tip

---

## 11. What is the output?

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(new Child().getValue());
    }
}

class Parent {
    int value = 10;

    Parent() {
        printValue();
    }

    void printValue() {
        System.out.println(value);
    }
}

class Child extends Parent {
    int value = 20;

    @Override
    void printValue() {
        System.out.println(value);
    }
}
```

### ❌ Junior Answer:
> "10 — Parent constructor runs first."

### ✅ Senior Answer:
> "0"

### 🔍 Explanation:
- When `new Child()` is called, `Parent` constructor runs first
- But `Child`'s `value = 20` has **not been initialized yet**
- At this point, `Child.value` is still **0** (default `int` value)
- `printValue()` is **overridden**, so `Child.printValue()` is called
- But `value` in `Child` is **0**, not 20

✅ The **field initialization happens after the parent constructor**.

> 🔁 So:  
> 1. `Parent()` → calls `printValue()`  
> 2. `printValue()` → overridden → calls `Child.printValue()`  
> 3. `Child.value` → not initialized → **0**

### 🎯 Interview Tip:
> _"I avoid calling overridable methods in constructors. This is a classic initialization-order trap. I use `final` methods or initialize state after construction."_  

---

## 12. Can you overload `main` method?

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("Main with String[]");
    }

    public static void main(String args) {
        System.out.println("Main with String");
    }

    public static void main() {
        System.out.println("Main no args");
    }
}
```

### ❌ Junior Answer:
> "No — `main` is special, can’t be overloaded."

### ✅ Senior Answer:
> "Yes — you can overload `main`, but only `main(String[])` is the entry point."

### 🔍 Explanation:
- `main` is just a **static method** — it can be overloaded like any other
- But the **JVM only calls `main(String[])`**
- Other overloads are ignored at startup

✅ You can call them manually:
```java
public static void main(String[] args) {
    main(); // Calls main()
}
```

### 🎯 Interview Tip:
> _"Yes, `main` can be overloaded — it’s just a static method. But only `main(String[])` is the entry point. I’ve used overloads for testing or internal logic, but never rely on them for startup."_  

---

## 13. What is the output?

```java
Double d1 = Double.NaN;
Double d2 = Double.NaN;
System.out.println(d1 == d2);
System.out.println(d1.equals(d2));
```

### ❌ Junior Answer:
> "true, true"

### ✅ Senior Answer:
> "false, true"

### 🔍 Explanation:
- `Double.NaN == Double.NaN` → **false** (by IEEE 754 standard)
- But `Double.equals()` is **overridden** to return `true` for two `NaN` values

✅ From `Double.equals()` source:
```java
if (d1.isNaN() && d2.isNaN()) return true;
```

> 💡 This is a **rare case** where `==` and `.equals()` disagree.

### 🎯 Interview Tip:
> _"I know `NaN` is not equal to itself with `==`, but `Double.equals()` treats two `NaN` values as equal. I always use `.equals()` for object comparison to avoid this gotcha."_  

---

## 14. What is the output?

```java
String s = "hello" + "world";
String t = "helloworld";
System.out.println(s == t);
```

### ❌ Junior Answer:
> "false — different objects."

### ✅ Senior Answer:
> "true"

### 🔍 Explanation:
- `"hello" + "world"` is a **compile-time constant**
- JVM **evaluates it at compile time** → `"helloworld"`
- Both `s` and `t` point to the **same string in the pool**

✅ The compiler optimizes constant string concatenation.

✅ Try with variables:
```java
String a = "hello";
String b = "world";
String s = a + b; // Runtime → new object
System.out.println(s == t); // false
```

### 🎯 Interview Tip:
> _"Constant string concatenation is optimized at compile time and interned. But if any operand is a variable, it happens at runtime and creates a new object. I use `StringBuilder` for dynamic strings."_  

---

## 15. Can you call `super()` after `this()` in a constructor?

```java
class Test {
    Test() {
        this("default");
    }
    Test(String s) {
        super();
        System.out.println(s);
    }
}
```

### ❌ Junior Answer:
> "Yes — order doesn’t matter."

### ✅ Senior Answer:
> "Yes, but `super()` must be the **first executable statement** — and `this()` already was."

### 🔍 Explanation:
- `this("default")` → calls the second constructor
- But `this()` **is** the first statement
- `super()` in the second constructor is **not the first statement** → ❌ **Compilation error**

✅ The rule: **Only one** of `this()` or `super()` can be the first statement.

> ❌ Invalid:
> ```java
> Test(String s) {
>     this(); // ❌ Can't have both
>     super();
> }
> ```

### 🎯 Interview Tip:
> _"Only one constructor call — `this()` or `super()` — can be the first statement. I use `this()` for chaining within the class, and let the final constructor call `super()`."_  

---

## 16. What is the output?

```java
public class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try");
            return;
        } finally {
            System.out.println("finally");
        }
        System.out.println("after");
    }
}
```

### ❌ Junior Answer:
> "try finally after"

### ✅ Senior Answer:
> "try finally"

### 🔍 Explanation:
- `return` in `try` is **postponed** until `finally` runs
- `finally` executes **before** the method returns
- Code after `finally` is **unreachable** → compilation error

> ❌ **Compilation error**: "Unreachable statement"

✅ The `return` doesn’t skip `finally`.

### 🎯 Interview Tip:
> _"Finally always runs, even with return, break, or continue. But unreachable code after finally is a compile-time error. I use finally only for cleanup — never for return or unreachable logic."_  

---

## 17. What is the output?

```java
int[] arr = null;
System.out.println(arr.length);
```

### ❌ Junior Answer:
> "0"

### ✅ Senior Answer:
> "It throws `NullPointerException`"

### 🔍 Explanation:
- `arr` is `null` → no array object created
- `arr.length` → tries to access `.length` on `null` → NPE

✅ Array must be initialized:
```java
int[] arr = new int[5]; // then arr.length = 5
```

### 🎯 Interview Tip:
> _"Accessing `.length` on a null array reference throws NPE. I always check for null before accessing array properties."_  

---

## 18. Can you have a `try` block without `catch`?

```java
try {
    riskyOperation();
} finally {
    cleanup();
}
```

### ❌ Junior Answer:
> "No — every try needs a catch."

### ✅ Senior Answer:
> "Yes — `try-finally` is valid. You can omit `catch`."

### 🔍 Explanation:
- `try` must be followed by **either** `catch` **or** `finally`
- `try-catch`, `try-finally`, `try-catch-finally` are all valid
- `try` alone → ❌ invalid

✅ Use `try-finally` when you only need cleanup, not error handling.

### 🎯 Interview Tip:
> _"Yes, try-finally is allowed. I use it when I want to ensure cleanup but let the exception propagate up. For resource management, I prefer try-with-resources."_  

---

## 19. What is the output?

```java
class Parent {
    void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    private void show() {
        System.out.println("Child");
    }
}

// In main:
Parent p = new Child();
p.show();
```

### ❌ Junior Answer:
> "Child — method overriding."

### ✅ Senior Answer:
> "Parent"

### 🔍 Explanation:
- `Child.show()` is **private** → **not visible** to `Parent`
- **No overriding** — it’s a **different method**
- `p` is of type `Parent` → calls `Parent.show()`

✅ **Private methods cannot be overridden** — they are **not inherited** in the polymorphic sense.

### 🎯 Interview Tip:
> _"Private methods are not inherited for overriding. They belong only to the class. I never mark an overridden method as private — it breaks polymorphism."_  

---

## 20. What is the output?

```java
List<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.add(3);

for (int i = 0; i < list.size(); i++) {
    if (list.get(i) == 2) {
        list.remove(i);
    }
}
System.out.println(list);
```

### ❌ Junior Answer:
> "[1, 3]"

### ✅ Senior Answer:
> "[1, 3]" — but it’s **fragile**

### 🔍 Explanation:
- Removes index 1 (`2`)
- But **list size changes** — `i` increments to 2, but list only has indices 0 and 1 now
- So index 2 is out of bounds? No — loop condition is `i < size()` → `size()` is now 2 → loop ends

✅ It works **this time**, but:

```java
// If you remove at index 0:
list.remove(0); // [2,3] → i=1, size=2 → next get(1)=3 → misses 2
```

❌ Bug: **skips next element**

✅ Fix:
```java
for (int i = 0; i < list.size(); ) {
    if (list.get(i) == 2) {
        list.remove(i); // Don't increment i
    } else {
        i++;
    }
}
```

Or use `Iterator` or `removeIf`.

### 🎯 Interview Tip:
> _"Removing during indexed loop can skip elements. I use Iterator.remove() or removeIf() to avoid this subtle bug."_  

---

## ✅ Final Advice

> 🎯 These questions test:
> - Memory model (String Pool, heap)
> - Inheritance and polymorphism rules
> - Exception handling edge cases
> - Autoboxing/unboxing
> - Thread safety
> - JVM behavior (constant folding, caching)

> 💬 _"The best engineers don’t just know the answer — they know **why** the question exists."_  

---


Just say: _"Let’s do [topic]"_

You're mastering Java at a **senior level** — not just coding, but **thinking like an architect**. 💪
