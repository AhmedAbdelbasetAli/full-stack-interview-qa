# 🧠 Tricky JavaScript Questions  
**25+ Mind-Bending JS Puzzles with Deep Explanations**

Here are **25+ tricky JavaScript questions** that test **deep language understanding** — perfect for **interviews**, **coding challenges**, and **mastering JS quirks**.

Each question includes:
- ✅ The code
- ✅ What it logs
- ✅ Why (in-depth explanation)
- ✅ Key concepts

---

## 🔹 1. Hoisting & Temporal Dead Zone

```js
console.log(a); // ?
console.log(b); // ?

var a = 1;
let b = 2;
```

✅ **Output**:
```
undefined
ReferenceError: Cannot access 'b' before initialization
```

### 💡 Why?
- `var` is **hoisted** and initialized to `undefined`
- `let` is hoisted but **not initialized** — in **Temporal Dead Zone (TDZ)**
- Accessing `b` before declaration → `ReferenceError`

---

## 🔹 2. Closures in Loops

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
```

✅ **Output**: `3 3 3` (not `0 1 2`)

### 💡 Why?
- `var` has **function scope**, not block scope
- All `setTimeout` callbacks close over the **same `i`**
- By the time they run, `i = 3`

### ✅ Fix with `let` (block scope):
```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0 1 2
}
```

---

## 🔹 3. `this` Binding

```js
const obj = {
  name: "Alice",
  greet: function() {
    console.log(this.name);
  }
};

const greet = obj.greet;
greet(); // ?
```

✅ **Output**: `undefined`

### 💡 Why?
- `greet()` is called as a **function**, not a method
- `this` → `global` (or `undefined` in strict mode)
- `global.name` is `undefined`

### ✅ Fix:
```js
const boundGreet = obj.greet.bind(obj);
boundGreet(); // "Alice"
```

---

## 🔹 4. Event Loop & Microtasks

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

✅ **Output**: `1 4 3 2`

### 💡 Why?
1. `1` → sync
2. `4` → sync
3. **Microtasks**: `Promise.then()` → `3`
4. **Macrotasks**: `setTimeout` → `2`

> ✅ **Microtasks (Promises)** run before **macrotasks (setTimeout)**

---

## 🔹 5. Equality & Coercion

```js
console.log([] == ![]); // ?
```

✅ **Output**: `true`

### 💡 Why?
- `![]` → `!true` → `false` (empty array is truthy)
- `[] == false`
- `[]` → `""` (toString)
- `"" == false` → `0 == 0` → `true`

> 💡 **Type coercion** can be unpredictable!

---

## 🔹 6. Function Expressions vs Declarations

```js
console.log(foo()); // ?
console.log(bar()); // ?

function foo() {
  return "function declaration";
}

var bar = function() {
  return "function expression";
};
```

✅ **Output**:
```
"function declaration"
TypeError: bar is not a function
```

### 💡 Why?
- `function foo()` is **hoisted completely**
- `var bar = function()` → only `var bar` is hoisted (→ `undefined`)
- `bar()` → `undefined()`

---

## 🔹 7. Object Keys & Coercion

```js
const a = {};
const b = { key: "b" };
const c = { key: "c" };

a[b] = 123;
a[c] = 456;

console.log(a[b]); // ?
```

✅ **Output**: `456`

### 💡 Why?
- Object keys are **strings** (or Symbols)
- `a[b]` → `a["[object Object]"]`
- `a[c]` → same key → overwrites
- So `a[b]` and `a[c]` point to the same property

---

## 🔹 8. Array Methods & `thisArg`

```js
[1, 2, 3].map(parseInt); // ?
```

✅ **Output**: `[1, NaN, NaN]`

### 💡 Why?
- `map(callback, thisArg)` passes **three args**: `(element, index, array)`
- `parseInt(string, radix)`
- So:
  - `parseInt(1, 0)` → `1` (radix 0 → 10)
  - `parseInt(2, 1)` → `NaN` (radix 1 invalid)
  - `parseInt(3, 2)` → `NaN` (3 not binary)

### ✅ Fix:
```js
[1, 2, 3].map(x => parseInt(x)); // [1, 2, 3]
```

---

## 🔹 9. `new` & Constructor Behavior

```js
function Dog(name) {
  this.name = name;
  return { name: "Buddy" };
}

const dog = new Dog("Max");
console.log(dog.name); // ?
```

✅ **Output**: `"Buddy"`

### 💡 Why?
- If constructor returns an **object**, that object is used
- If returns **primitive**, `this` is used
- Here, `{ name: "Buddy" }` is returned → `dog` = that object

---

## 🔹 10. `arguments` Object

```js
function foo() {
  arguments[0] = "changed";
  console.log(a);
}

foo("original"); // ?
```

✅ **Output**: `"changed"`

### 💡 Why?
- In **non-strict mode**, `arguments` is **mapped** to named parameters
- `arguments[0]` ↔ `a`
- Change one, change the other

> ⚠️ In strict mode: no mapping

---

## 🔹 11. `setTimeout` with `0` Delay

```js
console.log("start");

setTimeout(() => console.log("timeout"), 0);

Promise.resolve().then(() => console.log("promise"));

console.log("end");
```

✅ **Output**: `start end promise timeout`

### 💡 Why?
- `setTimeout` → macrotask queue
- `Promise.then()` → microtask queue
- **Microtasks run before next macrotask**

---

## 🔹 12. `var` in Loops

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
```

✅ **Output**: `3 3 3`

### 💡 Why?
- `var` is **function-scoped**
- All closures share the same `i`
- Loop finishes → `i = 3`

### ✅ Fix with IIFE:
```js
for (var i = 0; i < 3; i++) {
  (function(i) {
    setTimeout(() => console.log(i), 100);
  })(i);
}
```

---

## 🔹 13. `this` in Arrow Functions

```js
const obj = {
  name: "Alice",
  greet: () => console.log(this.name)
};

obj.greet(); // ?
```

✅ **Output**: `undefined` (or global name)

### 💡 Why?
- Arrow functions **do not bind `this`**
- `this` → outer scope (global)
- Even when called as `obj.greet()`

### ✅ Fix with regular function:
```js
greet() { console.log(this.name); }
```

---

## 🔹 14. `JSON.stringify` & `undefined`

```js
console.log(
  JSON.stringify({ a: undefined, b: 0, c: null })
); // ?
```

✅ **Output**: `{"b":0,"c":null}`

### 💡 Why?
- `undefined`, `functions`, and `symbols` are **ignored** in `JSON.stringify`
- `null` is valid → included

---

## 🔹 15. `+` Operator with Objects

```js
console.log([] + []); // ?
console.log({} + []); // ?
console.log([] + {}); // ?
```

✅ **Output**:
```
""
"[object Object]"
"[object Object]"
```

### 💡 Why?
- `+` triggers `toString()` or `valueOf()`
- `[] + []` → `"" + ""` → `""`
- `{} + []` → `"[object Object]" + ""` → `"[object Object]"`
- `[] + {}` → same

---

## 🔹 16. `delete` Operator

```js
const obj = { a: 1, b: 2 };
const arr = [1, 2, 3];

delete obj.a;
delete arr[0];

console.log(obj); // ?
console.log(arr); // ?
```

✅ **Output**:
```
{ b: 2 }
[empty, 2, 3]
```

### 💡 Why?
- `delete` removes property → `obj.a` gone
- `delete arr[0]` → removes element, but **index remains empty**
- Array length unchanged

---

## 🔹 17. `0.1 + 0.2 === 0.3`?

```js
console.log(0.1 + 0.2 === 0.3); // ?
```

✅ **Output**: `false`

### 💡 Why?
- Floating-point precision: `0.1 + 0.2 = 0.30000000000000004`
- Never compare floats with `===`

### ✅ Fix:
```js
Math.abs(0.1 + 0.2 - 0.3) < Number.EPSILON
```

---

## 🔹 18. `typeof null`

```js
console.log(typeof null); // ?
```

✅ **Output**: `"object"`

### 💡 Why?
- **Historical bug** in JS
- `null` is **not an object**, but `typeof` returns `"object"`
- Can't be fixed (would break existing code)

---

## 🔹 19. `instanceof` with Primitives

```js
console.log("hello" instanceof String); // ?
console.log(new String("hello") instanceof String); // ?
```

✅ **Output**:
```
false
true
```

### 💡 Why?
- `"hello"` is a **primitive string**
- `new String("hello")` is a **String object**
- `instanceof` checks prototype chain

---

## 🔹 20. `Object.create(null)`

```js
const obj = Object.create(null);
obj.name = "Alice";
console.log(obj.toString()); // ?
```

✅ **Output**: `TypeError: obj.toString is not a function`

### 💡 Why?
- `Object.create(null)` → no prototype (`__proto__ = null`)
- No `toString`, `hasOwnProperty`, etc.
- "Dictionary" object

---

## 🔹 21. `==` vs `===` with Booleans

```js
console.log(true == '1');  // ?
console.log(true === '1'); // ?
```

✅ **Output**:
```
true
false
```

### 💡 Why?
- `==` does type coercion: `'1'` → `true` → `1 == 1`
- `===` no coercion → `true !== '1'`

---

## 🔹 22. `Array.isArray` vs `instanceof`

```js
console.log(Array.isArray([])); // ?
console.log([] instanceof Array); // ?
```

✅ Both: `true`

### 💡 But in iframes:
```js
const iframe = document.createElement('iframe');
document.body.appendChild(iframe);
const arr = iframe.contentWindow.Array();
console.log(Array.isArray(arr)); // true
console.log(arr instanceof Array); // false
```

✅ Use `Array.isArray()` — works across realms

---

## 🔹 23. `for...in` vs `for...of`

```js
const arr = [10, 20, 30];
arr.name = "numbers";

for (let key in arr) {
  console.log(key); // ?
}

for (let val of arr) {
  console.log(val); // ?
}
```

✅ **Output**:
```
0
1
2
name
10
20
30
```

### 💡 Why?
- `for...in` → enumerable **keys** (including inherited)
- `for...of` → iterable **values**

---

## 🔹 24. `try/catch/finally` Return Behavior

```js
function fn() {
  try {
    return "try";
  } catch (e) {
    return "catch";
  } finally {
    return "finally";
  }
}

console.log(fn()); // ?
```

✅ **Output**: `"finally"`

### 💡 Why?
- `finally` **overrides** any `return` in `try` or `catch`
- If `finally` returns, that value is used

---

## 🔹 25. `this` in `setTimeout`

```js
const obj = {
  name: "Alice",
  greet() {
    setTimeout(function() {
      console.log(this.name);
    }, 100);
  }
};

obj.greet(); // ?
```

✅ **Output**: `undefined`

### 💡 Why?
- `function()` has its own `this` → `global`
- Arrow function would fix it:
```js
setTimeout(() => console.log(this.name), 100); // "Alice"
```

---

## ✅ Final Tip

> 🎯 In interviews, **explain the "why"**:
> _"This happens because of hoisting / event loop / this binding..."_

That shows **deep understanding**, not just memorization.

---
