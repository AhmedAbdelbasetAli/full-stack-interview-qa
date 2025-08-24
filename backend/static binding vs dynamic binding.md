# 🔗 Static Binding vs Dynamic Binding in Java  
**Method Resolution at Compile Time vs Runtime**

This document explains **static binding (early binding)** and **dynamic binding (late binding)** — essential concepts for understanding **polymorphism**, **overloading**, and **overriding** in Java.

You’ll learn:
- ✅ Clear definitions
- ✅ How Java resolves methods
- ✅ Real code examples
- ✅ Key differences
- ✅ Interview-ready answers

---

## 🔹 What is Binding?

> **Binding** is the process of **connecting a method call to its definition**.

In Java, this happens in two ways:
- ✅ **Static Binding** — at **compile time**
- ✅ **Dynamic Binding** — at **runtime**

---

## 1. Static Binding (Early Binding)

> **Static binding** occurs when the method to call is determined at **compile time**.

It’s used for:
- ✅ **Private**, **static**, and **final** methods
- ✅ **Method overloading**

Because these methods **cannot be overridden**, the compiler knows exactly which method to call.

---

### ✅ Example: Static Binding with `static` and `private` Methods

```java
class Animal {
    // static method → bound at compile time
    public static void staticMethod() {
        System.out.println("Animal: staticMethod");
    }

    // private method → not inherited, so statically bound
    private void privateMethod() {
        System.out.println("Animal: privateMethod");
    }

    // Overloaded methods → resolved at compile time
    public void print(String s) {
        System.out.println("String: " + s);
    }

    public void print(int i) {
        System.out.println("Int: " + i);
    }
}

class Dog extends Animal {
    public static void staticMethod() {
        System.out.println("Dog: staticMethod");
    }

    private void privateMethod() {
        System.out.println("Dog: privateMethod");
    }
}
```

### ✅ Test Code
```java
public static void main(String[] args) {
    Animal a = new Dog();

    // Static methods are NOT polymorphic
    a.staticMethod(); // Output: Animal: staticMethod ❗

    // Private methods are not accessible
    // a.privateMethod(); // Compile error

    // Method overloading → resolved at compile time
    a.print("hello"); // Output: String: hello
    a.print(42);      // Output: Int: 42
}
```

### 📌 Key Points
- `staticMethod()` is **not overridden** — it’s **hidden**
- The method call is based on **reference type** (`Animal`), not object type (`Dog`)
- Compiler decides at **compile time** → **static binding**

---

## 2. Dynamic Binding (Late Binding)

> **Dynamic binding** occurs when the method to call is determined at **runtime** based on the **actual object type**.

It’s used for:
- ✅ **Instance methods** that are **overridden** (not `private`, `static`, or `final`)
- ✅ **Method overriding**

The JVM uses the **method override mechanism** and **virtual method table (vtable)** to resolve the correct method at runtime.

---

### ✅ Example: Dynamic Binding with Method Overriding

```java
class Animal {
    // Overridden method → dynamically bound
    public void makeSound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Cat meows");
    }
}
```

### ✅ Test Code
```java
public static void main(String[] args) {
    Animal a1 = new Dog();
    Animal a2 = new Cat();
    Animal a3 = new Animal();

    a1.makeSound(); // Output: Dog barks ✅
    a2.makeSound(); // Output: Cat meows ✅
    a3.makeSound(); // Output: Animal makes sound ✅
}
```

### 📌 Key Points
- The **reference type** is `Animal`, but the **object type** determines the method
- JVM checks the actual object at **runtime** → **dynamic binding**
- This is **polymorphism** in action

---

## 🔹 How Dynamic Binding Works (Under the Hood)

1. Each class has a **virtual method table (vtable)**
2. The vtable contains pointers to **actual method implementations**
3. When a method is called on an object:
   - JVM looks up the object’s class vtable
   - Calls the method at runtime
4. If the method is overridden, the vtable points to the subclass version

✅ This is why overriding works with polymorphism.

---

## 🔹 Comparison: Static vs Dynamic Binding

| Feature | **Static Binding** | **Dynamic Binding** |
|--------|--------------------|---------------------|
| **Time of Binding** | Compile time | Runtime |
| **Used For** | `static`, `private`, `final` methods, overloading | Overridden instance methods |
| **Based On** | Reference type | Actual object type |
| **Performance** | Faster (no lookup) | Slightly slower (vtable lookup) |
| **Polymorphism** | ❌ No | ✅ Yes |
| **Method Overriding** | ❌ Not applicable | ✅ Required |
| **Example** | `staticMethod()`, `privateMethod()` | `makeSound()` in `Dog` |

---

## 🔹 Real-World Analogy

| | Static Binding | Dynamic Binding |
|--|---------------|-----------------|
| **Car Manual** | "All sedans have 4 doors" → true at time of writing | "This specific sedan has 4 doors" → check the actual car |
| **Restaurant Menu** | "All burgers come with fries" → printed on menu | "This burger has no fries" → server decides at order time |

✅ Static = compile-time decision  
✅ Dynamic = runtime decision

---

## 📌 Interview Answer

> _"Static binding resolves method calls at compile time — used for static, private, and final methods. The compiler decides based on the reference type. Dynamic binding resolves at runtime — used for overridden methods. The JVM uses the actual object type, enabling polymorphism. I override instance methods for dynamic behavior, but static methods are bound statically and can't be overridden."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the vtable**:
> ```
> Animal: [ makeSound → Animal::makeSound ]
> Dog:    [ makeSound → Dog::makeSound ]
> ```
> And say:  
> _"The JVM uses the vtable to call the correct method at runtime — that’s dynamic binding."_

That shows **deep understanding of JVM internals**.

---
