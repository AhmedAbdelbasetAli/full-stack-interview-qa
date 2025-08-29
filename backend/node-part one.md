# 🧠 Node.js Deep Dive  
**Event Loop, Concurrency & Asynchronous Internals**

This document covers **advanced Node.js concepts** — essential for **backend developers**, **system design**, and **senior JavaScript interviews**.

You’ll learn:
- ✅ How Node.js works under the hood
- ✅ The Event Loop and its phases
- ✅ `setImmediate`, `setTimeout`, `process.nextTick`
- ✅ Concurrency in a single-threaded environment
- ✅ Interview-ready answers

---

## 1️⃣ What is Node.js? How does it work under the hood?

> **Node.js** is a **JavaScript runtime built on Chrome’s V8 engine** that allows you to **run JavaScript on the server**.

It’s **not a framework** — it’s a **runtime environment**.

---

### ✅ How Node.js Works Under the Hood

```
[Your JavaScript Code] → [Node.js C++ Layer] → [V8 Engine + Libuv] → [Operating System]
```

### 🔹 Key Components

| Component | Purpose |
|--------|--------|
| ✅ **V8 Engine** | Google’s JavaScript engine — compiles JS to machine code |
| ✅ **Libuv** | C library that handles **asynchronous I/O** (file system, network, timers) |
| ✅ **C++ Addons** | Bridge between JS and system calls |
| ✅ **Event Loop** | Core of Node.js — handles concurrency via event-driven model |
| ✅ **Thread Pool** | 4-thread pool (default) for CPU-intensive tasks (file I/O, crypto) |

---

### ✅ Example: Reading a File
```js
const fs = require('fs');

fs.readFile('data.txt', 'utf8', (err, data) => {
  console.log(data);
});
console.log('Reading file...');
```

### 🔹 What Happens Internally?
1. `fs.readFile()` → Libuv
2. Libuv **starts async file read** (offloaded to OS or thread pool)
3. **Node continues** → logs `"Reading file..."`
4. When file is ready → callback is **queued in Event Loop**
5. Callback runs → logs file content

✅ No blocking — **non-blocking I/O**

---

## 2️⃣ Why is Node.js single-threaded? How does it handle concurrency?

> Node.js is **single-threaded for JavaScript execution**, but uses **libuv and OS-level async I/O** to handle **concurrency**.

---

### ✅ Why Single-Threaded?

| Reason | Explanation |
|-------|-------------|
| ✅ **No Thread Management Overhead** | No locks, deadlocks, race conditions |
| ✅ **Event-Driven Model** | Better for I/O-bound apps (APIs, real-time) |
| ✅ **Simplicity** | Easier to reason about code |
| ✅ **High Throughput** | Thousands of connections with low memory |

> ⚠️ **Not ideal for CPU-heavy tasks** (video encoding, AI)

---

### ✅ How Concurrency Works

```
[Event Loop] → [Handle I/O Events] → [Execute Callbacks]
       ↑
[Libuv] ←→ [Thread Pool (4 threads)] ←→ [OS (Kernel)]
```

- ✅ **I/O Operations** (network, file) → handled asynchronously by **OS or thread pool**
- ✅ **Event Loop** → processes callbacks in phases
- ✅ **Single JS thread** → executes callbacks one at a time

✅ **Concurrency ≠ Parallelism**  
Node.js handles **many connections concurrently**, but executes JS **sequentially**.

---

## 3️⃣ What is the Event Loop in Node.js? Explain its phases.

> The **Event Loop** is the **heartbeat of Node.js** — it continuously checks for **pending callbacks** and executes them.

It runs in **phases**, each with a specific purpose.

---

### ✅ Event Loop Phases (in order)

| Phase | Purpose |
|------|--------|
| 1. **Timers** | Execute callbacks from `setTimeout()` and `setInterval()` |
| 2. **Pending Callbacks** | Execute I/O callbacks deferred to next loop iteration |
| 3. **Idle, Prepare** | Internal use (ignore) |
| 4. **Poll** | **Retrieve new I/O events**; execute I/O callbacks; handle timers if queue empty |
| 5. **Check** | Execute `setImmediate()` callbacks |
| 6. **Close Callbacks** | Execute `socket.on('close', ...)` |

---

### ✅ Example: Event Loop in Action
```js
setTimeout(() => console.log('setTimeout'), 0);
setImmediate(() => console.log('setImmediate'));
process.nextTick(() => console.log('nextTick'));

console.log('Start');
```

✅ **Output**:
```
Start
nextTick
setTimeout
setImmediate
```

### 💡 Why?
- `process.nextTick()` runs **after current operation**, before next phase
- `setTimeout(0)` → goes to **Timers** phase
- `setImmediate()` → goes to **Check** phase
- **Poll** → **Check** → **Timers** (if no I/O)

> ⚠️ Order can vary if I/O is involved.

---

## 4️⃣ What is the difference between `setImmediate()` and `setTimeout()`?

| Feature | `setTimeout(fn, 0)` | `setImmediate(fn)` |
|--------|----------------------|-------------------|
| **Phase** | Timers | Check |
| **Timing** | After at least 0ms | After current poll phase |
| **Execution Order** | Depends on I/O | Runs after I/O callbacks |
| **Use Case** | Delay execution | Run after I/O |

---

### ✅ Example: With I/O
```js
fs.readFile('file.txt', () => {
  setTimeout(() => console.log('setTimeout'), 0);
  setImmediate(() => console.log('setImmediate'));
});
```

✅ **Output**: `setImmediate`, then `setTimeout`

### 💡 Why?
- After I/O → Event Loop enters **Check** phase → runs `setImmediate`
- Then goes to **Timers** → runs `setTimeout`

> `setImmediate()` **always runs before `setTimeout(0)`** when called inside an I/O callback.

---

## 5️⃣ What is the difference between `process.nextTick()` and `setImmediate()`?

| Feature | `process.nextTick()` | `setImmediate()` |
|--------|-----------------------|------------------|
| **Timing** | **After current operation**, before next phase | After current **poll** phase |
| **Queue** | `nextTick` queue (higher priority) | `check` queue |
| **Execution** | Before any other I/O event | After I/O callbacks |
| **Use Case** | Recursive calls, error handling | Defer execution after I/O |

---

### ✅ Example
```js
console.log('Start');

process.nextTick(() => console.log('nextTick 1'));
setImmediate(() => console.log('setImmediate'));

process.nextTick(() => console.log('nextTick 2'));

console.log('End');
```

✅ **Output**:
```
Start
End
nextTick 1
nextTick 2
setImmediate
```

### 💡 Why?
- `nextTick` callbacks run **before** Event Loop moves to next phase
- All `nextTick` callbacks are processed **before** `setImmediate`

> ⚠️ `process.nextTick()` can **starve** the Event Loop if used recursively.

---

## 📌 Interview Answer (Summary)

> _"Node.js is a single-threaded JavaScript runtime built on V8 and libuv. It handles concurrency using the Event Loop and non-blocking I/O. The Event Loop has phases: Timers, Poll, Check, etc. `setTimeout` runs in Timers phase, `setImmediate` in Check phase. `process.nextTick` runs before any other phase and has higher priority. I use `setImmediate` to defer work after I/O, and avoid `nextTick` recursion to prevent starvation."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the Event Loop phases**:
> ```
> Timers → Pending → Poll → Check → Close
> ```
> And say:  
> _"Callbacks are queued in different phases — `setImmediate` in Check, `setTimeout` in Timers."_  

That shows **deep understanding of Node.js internals**.

---

