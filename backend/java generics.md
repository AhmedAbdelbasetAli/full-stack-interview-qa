# 🧩 Java Generics Deep Dive  
**Type Safety, Reusability & Best Practices**

This document covers **Java Generics** — a **powerful feature** that enables **type-safe**, **reusable**, and **clean code**.

You’ll learn:
- ✅ What generics are and why they matter
- ✅ How to use them in classes, methods, and interfaces
- ✅ Wildcards (`?`, `extends`, `super`)
- ✅ Type erasure and limitations
- ✅ Best practices
- ✅ Interview-ready answers

---

## 🔹 What Are Generics?

> **Generics** allow you to **write code that works with any type**, while providing **compile-time type safety**.

They were introduced in **Java 5** to eliminate `ClassCastException` and reduce casting.

---

### ✅ The Problem Before Generics

```java
List list = new ArrayList();
list.add("Hello");
list.add(123);

String s = (String) list.get(1); // ❌ ClassCastException at runtime!
```

❌ No type safety  
❌ Manual casting required  
❌ Errors only at **runtime**

---

### ✅ With Generics

```java
List<String> list = new ArrayList<>();
list.add("Hello");
// list.add(123); // ❌ Compile error!

String s = list.get(0); // ✅ No cast needed
```

✅ **Type-safe** at compile time  
✅ **No casting**  
✅ **Self-documenting** code

---

## 1️⃣ Generic Classes

Define a class with a **type parameter** (e.g., `T`, `E`, `K`, `V`).

### ✅ Example: Generic Box
```java
public class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}

// Usage
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String s = stringBox.get(); // No cast

Box<Integer> intBox = new Box<>();
intBox.set(42);
Integer i = intBox.get();
```

✅ Reusable for any type  
✅ Type safety enforced

---

## 2️⃣ Generic Methods

A method can have its own type parameter.

### ✅ Example: Generic Utility Method
```java
public class Util {
    public static <T> void printList(List<T> list) {
        for (T item : list) {
            System.out.println(item);
        }
    }

    // Another example: swap two elements
    public static <T> void swap(T[] array, int i, int j) {
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}

// Usage
List<String> names = Arrays.asList("Alice", "Bob");
Util.printList(names);

Integer[] nums = {1, 2, 3};
Util.swap(nums, 0, 2);
```

✅ Works with any type  
✅ No need to overload for each type

---

## 3️⃣ Generic Interfaces

### ✅ Example: Comparable<T>
```java
public class Person implements Comparable<Person> {
    private String name;
    private int age;

    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age);
    }
}
```

✅ Type-safe comparison  
✅ No casting in `compareTo`

---

## 4️⃣ Bounded Type Parameters

Restrict the types that can be used with generics.

### ✅ Upper Bound: `T extends Number`
```java
public class Calculator<T extends Number> {
    public double sum(T a, T b) {
        return a.doubleValue() + b.doubleValue();
    }
}

// Usage
Calculator<Integer> calc = new Calculator<>();
double result = calc.sum(5, 10); // ✅ Integer is a Number
```

✅ Only allows `Number` and its subclasses (`Integer`, `Double`, etc.)

---

### ✅ Multiple Bounds
```java
<T extends Number & Comparable<T>>
```

✅ `T` must be a `Number` **and** implement `Comparable<T>`

---

## 5️⃣ Wildcards

Used when you want to **relax type constraints**.

### ✅ 1. Unbounded Wildcard: `<?>`
```java
public static void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

✅ Works with `List<String>`, `List<Integer>`, etc.  
❌ Can only read as `Object`

---

### ✅ 2. Upper-Bounded Wildcard: `<? extends T>`
```java
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number n : list) {
        sum += n.doubleValue();
    }
    return sum;
}

// Usage
List<Integer> ints = Arrays.asList(1, 2, 3);
List<Double> doubles = Arrays.asList(1.5, 2.5);

sumOfList(ints);    // ✅
sumOfList(doubles); // ✅
```

✅ Allows subtypes of `Number`  
❌ **"Producer"** — you can **read**, but **not write** (except `null`)

> 📌 **PECS Rule**: **Producer** `extends`, **Consumer** `super`

---

### ✅ 3. Lower-Bounded Wildcard: `<? super T>`
```java
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    list.add(3);
}

// Usage
List<Number> nums = new ArrayList<>();
addNumbers(nums); // ✅ Integer is a subtype of Number

// List<Integer> ints = new ArrayList<>();
// addNumbers(ints); // ✅ Also works
```

✅ Allows `Integer` or its **superclasses** (`Number`, `Object`)  
❌ **"Consumer"** — you can **write**, but when reading, only as `Object`

---

## 🔹 PECS Rule (Producer `extends`, Consumer `super`)

| Use Case | Wildcard |
|--------|---------|
| ✅ **Reading from a collection** (Producer) | `<? extends T>` |
| ✅ **Writing to a collection** (Consumer) | `<? super T>` |

### ✅ Example: Collections.max()
```java
public static <T> T max(Collection<? extends T> coll, Comparator<? super T> comp)
```

- `<? extends T>`: Collection **produces** `T` values
- `<? super T>`: Comparator **consumes** `T` values

---

## 🔹 Type Erasure

> Java generics are implemented using **type erasure** — type information is **removed at runtime**.

### ✅ What Happens at Compile Time
```java
List<String> list = new ArrayList<>();
list.add("Hello");

// After compilation (bytecode):
List list = new ArrayList();
list.add("Hello");
```

✅ Type checks are done at **compile time**  
✅ No type information at runtime  
✅ Ensures backward compatibility

---

### ✅ Consequences of Type Erasure

| Rule | Why |
|------|-----|
| ❌ **No `new T()`** | Type not available at runtime |
| ❌ **No `instanceof List<String>`** | Only `instanceof List` |
| ❌ **Cannot overload `method(List<String>)` and `method(List<Integer>)`** | Same erased type |
| ✅ **Cast to parameterized type is unchecked** | Warning: "unchecked cast" |

---

## ✅ Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use meaningful type names** | `T` (Type), `E` (Element), `K` (Key), `V` (Value) |
| ✅ **Prefer generics over raw types** | Type safety |
| ✅ **Use wildcards when appropriate** | More flexible APIs |
| ✅ **Follow PECS** | Producer `extends`, Consumer `super` |
| ✅ **Don’t ignore unchecked warnings** | Can lead to `ClassCastException` |

---

## 📌 Common Mistakes

| ❌ Anti-Pattern | ✅ Fix |
|----------------|--------|
| `List list = new ArrayList();` | `List<String> list = new ArrayList<>();` |
| Ignoring unchecked warnings | Fix or suppress with `@SuppressWarnings("unchecked")` |
| Using raw types in APIs | Always use generics |
| Misusing wildcards | Follow PECS |

---

## 📌 Interview Answer

> _"Generics allow me to write type-safe, reusable code. I use them in collections, custom classes, and methods to avoid casting and catch errors at compile time. I use bounded types for constraints, and wildcards with the PECS rule: 'Producer extends, Consumer super'. Due to type erasure, generics are compile-time only, so I can't use `new T()` or check `instanceof List<String>`."_  

---

## ✅ Final Tip

> 🎯 In interviews, **demonstrate PECS**:
> _"If I'm reading from a list, I use `<? extends T>` (producer). If I'm adding to a list, I use `<? super T>` (consumer). This makes APIs flexible and safe."_  

That shows **deep understanding of generics**.

---

