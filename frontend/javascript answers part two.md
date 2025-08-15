# 🧠 JavaScript Deep Dive: Core Concepts (Part 2)  
**Advanced Functions, Asynchronous Programming & DOM**

This document continues the deep dive into essential JavaScript concepts — critical for **interviews**, **real-world development**, and **senior-level roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Memory/execution model
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 6. Explain the differences between `map`, `filter`, and `reduce`.

| Method | Purpose | Return Value | Mutates Original? |
|-------|--------|--------------|-------------------|
| `map()` | Transform each element | New array (same length) | ❌ No |
| `filter()` | Select elements by condition | New array (≤ original length) | ❌ No |
| `reduce()` | Accumulate a single value | Any type (number, object, string, etc.) | ❌ No |

All three are **pure functions** — they don’t mutate the original array.

---

### 🔹 6.1 `map()` – Transform Elements

> Creates a **new array** by applying a function to each element.

```js
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6, 8]
```

#### 🟦 Use Cases
- Convert data: `users.map(u => u.name)`
- Format values: `prices.map(p => `$${p}`)`
- Prepare data for UI

✅ Always returns **same number of elements** as input.

---

### 🔹 6.2 `filter()` – Select Elements

> Creates a **new array** with elements that pass a test.

```js
const numbers = [1, 2, 3, 4, 5];
const evens = numbers.filter(n => n % 2 === 0);
console.log(evens); // [2, 4]
```

#### 🟦 Use Cases
- Search: `users.filter(u => u.age > 18)`
- Filter by status: `orders.filter(o => o.status === 'active')`
- Remove unwanted data

✅ Returns **zero or more elements** — can be empty.

---

### 🔹 6.3 `reduce()` – Accumulate a Value

> Applies a function to **accumulate a single result** from an array.

```js
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 10
```

#### 🟦 Parameters
- `acc`: Accumulator (starts with initial value)
- `curr`: Current element
- Initial value: `0` (for sum), `{}` (for object), `[]` (for array)

#### 🟦 Use Cases
- Sum, average, max/min
- Grouping: `array.reduce((groups, item) => { ... }, {})`
- Flattening: `arrays.reduce((acc, arr) => acc.concat(arr), [])`
- Building objects from arrays

---

### 🔹 6.4 Chaining Example

```js
const users = [
  { name: "Alice", age: 30, active: true },
  { name: "Bob", age: 25, active: false },
  { name: "Charlie", age: 35, active: true }
];

// Get names of active users over 28
const result = users
  .filter(u => u.active && u.age > 28)
  .map(u => u.name);

console.log(result); // ["Alice", "Charlie"]
```

✅ Chainable because each returns a new array.

---

### 📌 Interview Answer

> _"`map` transforms each element and returns a new array of the same length. `filter` selects elements based on a condition and returns a subset. `reduce` combines all elements into a single value — like a sum or grouped object. I use `map` for formatting, `filter` for selection, and `reduce` when I need to accumulate a result. They’re all immutable and chainable, making them ideal for functional programming."_  

---

## 7. What is Hoisting in JavaScript?

> **Hoisting** is JavaScript’s behavior of **moving declarations to the top** of their scope during compilation.

But only **declarations** are hoisted — **initializations are not**.

---

### 🔹 7.1 `var` Hoisting

```js
console.log(x); // undefined
var x = 10;
```

#### 🟦 How It Works
```js
// What you write
var x = 10;

// What JS sees (hoisted)
var x;
console.log(x); // undefined
x = 10;
```

✅ `var` is hoisted and initialized as `undefined`.

---

### 🔹 7.2 `let` and `const` Hoisting (Temporal Dead Zone)

```js
console.log(y); // ❌ ReferenceError
let y = 20;
```

#### 🟦 How It Works
- `let` and `const` are **hoisted but not initialized**
- Access before declaration → **Temporal Dead Zone (TDZ)**
- Throws `ReferenceError`

```js
// Conceptually:
let y; // hoisted but uninitialized
console.log(y); // ReferenceError
y = 20;
```

---

### 🔹 7.3 Function Hoisting

#### 🟦 Function Declarations (Hoisted)
```js
sayHello(); // "Hello"
function sayHello() {
  console.log("Hello");
}
```

✅ Entire function is hoisted.

#### 🟦 Function Expressions (Not Hoisted)
```js
sayHi(); // ❌ TypeError: not a function
var sayHi = function() {
  console.log("Hi");
};
```

✅ Only the `var` is hoisted as `undefined`.

---

### ⚠️ Common Pitfalls

| Code | Result |
|------|--------|
| `console.log(x); let x = 1;` | ReferenceError |
| `console.log(f()); var f = 1;` | TypeError |
| `g(); function g() {}` | Works (hoisted) |

---

### 📌 Interview Answer

> _"Hoisting is JavaScript’s mechanism of moving variable and function declarations to the top of their scope. `var` is hoisted and initialized as `undefined`. `let` and `const` are hoisted but live in the Temporal Dead Zone until declared. Function declarations are fully hoisted; function expressions are not. I avoid relying on hoisting and always declare variables at the top to prevent bugs."_  

---

## 8. What is an IIFE? Why Would You Use It?

> **IIFE** = **Immediately Invoked Function Expression**

A function that is **defined and executed immediately**.

```js
(function() {
  console.log("Runs immediately");
})();
```

---

### 🔹 8.1 Syntax

```js
(function() {
  // private scope
})()

// Or with arrow (not common)
(() => { })()

// Or with different grouping
!function() { }();
+function() { }();
```

The `()` around the function makes it an **expression**, not a declaration.

---

### 🔹 8.2 Why Use IIFE?

#### 🟦 1. Create Private Scope (Before `let`/`const`)
```js
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i); // 3, 3, 3
  }, 100);
}
```

✅ Fix with IIFE:
```js
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(() => console.log(j), 100);
  })(i);
}
// Logs: 0, 1, 2
```

#### 🟦 2. Avoid Global Pollution
```js
(function() {
  const apiKey = "secret123";
  function initApp() { /* use apiKey */ }
  initApp();
})();
// apiKey not accessible globally
```

#### 🟦 3. Module Pattern
```js
const Counter = (function() {
  let count = 0;
  return {
    increment: () => ++count,
    getCount: () => count
  };
})();

Counter.increment();
console.log(Counter.getCount()); // 1
```

---

### 🟦 Modern Alternative: Block Scope with `let`/`const`

```js
{
  const apiKey = "secret123";
  function initApp() { }
  initApp();
}
// apiKey not accessible outside
```

✅ IIFE is less needed now — but still used in legacy code and UMD patterns.

---

### 📌 Interview Answer

> _"An IIFE is a function that runs as soon as it’s defined. It creates a private scope, preventing variables from polluting the global namespace. I’d use it to capture loop variables before ES6, create modules with private state, or run setup code immediately. Today, block scope with `let`/`const` reduces the need for IIFEs, but they’re still useful in certain patterns like UMD or when supporting older environments."_  

---

## 9. How Do `Promise`, `async`, and `await` Work in JavaScript?

> They enable **asynchronous, non-blocking** code that’s **easier to read and maintain**.

---

### 🔹 9.1 `Promise` – The Foundation

A `Promise` is an object representing the **eventual completion (or failure)** of an async operation.

#### 🟦 States
- **Pending**: Initial state
- **Fulfilled**: Operation succeeded → `.then()`
- **Rejected**: Operation failed → `.catch()`

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    Math.random() > 0.5 ? resolve("Success!") : reject("Error!");
  }, 1000);
});

promise
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

#### 🟦 Static Methods
- `Promise.resolve(value)` → fulfilled promise
- `Promise.reject(reason)` → rejected promise
- `Promise.all([p1, p2])` → all must succeed
- `Promise.race([p1, p2])` → first to settle wins

---

### 🔹 9.2 `async/await` – Syntactic Sugar

> `async` functions **always return a Promise**  
> `await` **pauses execution** until the Promise settles

```js
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Fetch failed:", error);
  }
}

// Usage
fetchData().then(data => console.log(data));
```

#### 🟦 How `await` Works
- It **doesn’t block the thread** — JavaScript yields control
- The function is **paused**, not the event loop
- When Promise resolves, execution resumes

---

### 🔹 9.3 Error Handling

#### 🟦 With `.catch()`
```js
fetchData().catch(err => console.log(err));
```

#### 🟦 With `try/catch` (async/await)
```js
try {
  const data = await fetchData();
} catch (err) {
  console.log(err);
}
```

---

### 🔹 9.4 Parallel vs Sequential

#### 🟦 Sequential (awaits one by one)
```js
const a = await fetch('/a');
const b = await fetch('/b'); // Waits for a
```

#### 🟦 Parallel (faster)
```js
const [resA, resB] = await Promise.all([
  fetch('/a'),
  fetch('/b')
]);
```

---

### 📌 Interview Answer

> _"A Promise represents an async operation that will complete in the future. It has three states: pending, fulfilled, and rejected. `async/await` is syntactic sugar over Promises — `async` functions return a Promise, and `await` pauses execution until the Promise resolves. This makes async code look synchronous and easier to read. I use `Promise.all` for parallel operations and `try/catch` with `await` for clean error handling. The Event Loop ensures that `await` doesn’t block the thread — it just pauses the function."_  

---

## 10. What is Event Delegation? Why Is It Useful?

> **Event delegation** is a technique where you **attach a single event listener** to a **parent element** to handle events from **child elements**.

It works due to **event bubbling**.

---

### 🔹 10.1 How Event Bubbling Works

When an event happens on a child:
1. It runs handlers on the child
2. Then on parent
3. Then on ancestors

```html
<ul id="list">
  <li>Item 1</li>
  <li>Item 2</li>
</ul>
```

```js
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    console.log("Clicked:", e.target.textContent);
  }
});
```

✅ Clicking any `<li>` triggers the handler on `<ul>`.

---

### 🔹 10.2 Why Use Event Delegation?

| Benefit | Explanation |
|--------|-------------|
| ✅ **Performance** | One listener vs many |
| ✅ **Dynamic Content** | Works for elements added later |
| ✅ **Memory Efficient** | Fewer listeners → less memory |
| ✅ **Cleaner Code** | Centralized event handling |

#### 🟦 Example: Dynamic List
```js
// Works for current and future <li> elements
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.matches("li")) {
    e.target.remove();
  }
});

// Add new items later
const newItem = document.createElement("li");
newItem.textContent = "New Item";
list.appendChild(newItem); // Click handler still works!
```

---

### 🔹 10.3 `event.target` vs `event.currentTarget`

| Property | Meaning |
|--------|--------|
| `event.target` | The **actual element** clicked |
| `event.currentTarget` | The **element that has the listener** |

```js
parent.addEventListener("click", (e) => {
  console.log(e.target);        // clicked child
  console.log(e.currentTarget); // parent
});
```

---

### 📌 Interview Answer

> _"Event delegation leverages event bubbling to handle events from multiple children using a single listener on a parent. It’s useful for performance, dynamic content, and memory efficiency. For example, I use it on tables, lists, or grids where items are added/removed dynamically. I check `event.target` to identify which child was clicked and act accordingly. This pattern is essential for building scalable, maintainable UIs."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use `map` and `filter` for data transformation, wrap async logic in `async/await`, and use event delegation for dynamic lists. I avoid `var` and `==`, and use closures for private state."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---

