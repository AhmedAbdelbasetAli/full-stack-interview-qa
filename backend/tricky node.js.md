# 🧠 Tricky Node.js Interview Questions  
**20+ Mind-Bending JS/Node Puzzles with Deep Explanations**

Here are **20+ tricky Node.js questions** that test **deep language understanding**, **Event Loop behavior**, **module system quirks**, and **asynchronous patterns** — perfect for **senior backend** or **full-stack interviews**.

Each question includes:
- ✅ The code
- ✅ What it logs
- ✅ Why (in-depth explanation)
- ✅ Key concepts

---

## 🔹 1. Event Loop Order: `setTimeout`, `setImmediate`, `nextTick`

```js
console.log('1');

setTimeout(() => console.log('2'), 0);

setImmediate(() => console.log('3'));

process.nextTick(() => console.log('4'));

console.log('5');
```

✅ **Output**: `1 5 4 2 3`

### 💡 Why?
- `1`, `5` → sync
- `process.nextTick()` → runs **after current operation**, before Event Loop phases
- Then Event Loop:
  - **Timers**: `setTimeout` → `2`
  - **Check**: `setImmediate` → `3`

> ⚠️ `setImmediate()` runs **after** `setTimeout(0)` if no I/O is involved.

---

## 🔹 2. `nextTick` vs `setImmediate` with I/O

```js
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => console.log('timeout'), 0);
  setImmediate(() => console.log('immediate'));
});
```

✅ **Output**: `immediate`, then `timeout`

### 💡 Why?
- After file I/O → Event Loop enters **Check** phase → runs `setImmediate`
- Then goes to **Timers** phase → runs `setTimeout`

> `setImmediate()` **always runs before `setTimeout(0)`** when called inside an I/O callback.

---

## 🔹 3. `require` vs `import` in Same File

```js
// app.mjs
import fs from 'fs';
const path = require('path'); // ❌ TypeError: require is not a function
```

✅ **Error**: You can't use `require` in an ES Module (`.mjs` or `"type": "module"`)

### ✅ Fix:
```js
import { createRequire } from 'module';
const require = createRequire(import.meta.url);
const path = require('path');
```

> 💡 Common gotcha when migrating from CommonJS to ESM.

---

## 🔹 4. Module Caching in `require`

```js
// math.js
console.log('Loading math.js');
module.exports = { add: (a, b) => a + b };

// app.js
require('./math'); // Logs: Loading math.js
require('./math'); // No log! Module is cached
```

✅ **Output**: Only logs once

### 💡 Why?
- `require()` **caches modules** after first load
- Subsequent `require()` returns cached version
- No re-execution

> ✅ Use `delete require.cache[require.resolve('./math')]` to force reload (rarely needed)

---

## 🔹 5. Closure in `setTimeout` Inside Loop

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
```

✅ **Output**: `3 3 3`

### 💡 Why?
- `var` is **function-scoped**, not block-scoped
- All callbacks close over the **same `i`**
- Loop finishes → `i = 3`

### ✅ Fix with `let` (block scope):
```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0 1 2
}
```

---

## 🔹 6. `process.nextTick` Starvation

```js
process.nextTick(() => console.log('1'));
process.nextTick(() => console.log('2'));
process.nextTick(() => {
  console.log('3');
  process.nextTick(() => console.log('4'));
});
process.nextTick(() => console.log('5'));
```

✅ **Output**: `1 2 3 4 5`

### 💡 Why?
- `process.nextTick()` has **highest priority**
- All `nextTick` callbacks are processed **before** moving to next Event Loop phase
- Even if added during execution (like `4`), it runs before `5`

> ⚠️ Can **starve** the Event Loop — avoid recursion.

---

## 🔹 7. `__dirname` vs `.` in `require`

```js
// /home/user/app/utils/logger.js
console.log(__dirname); // /home/user/app/utils
console.log('.');       // Depends on CWD

require('./config');    // ❌ May fail if run from /tmp
require(path.join(__dirname, 'config')); // ✅ Always works
```

✅ **Best Practice**: Use `__dirname` for file paths

---

## 🔹 8. `exports` vs `module.exports`

```js
// math.js
exports.add = (a, b) => a + b;
module.exports = { multiply: (a, b) => a * b }; // Replaces exports

// app.js
const math = require('./math');
console.log(math.add);      // undefined
console.log(math.multiply); // ✅ Function
```

✅ `module.exports` **replaces** the object
✅ `exports` is just a reference to `module.exports`

> ❌ Don’t reassign `exports` — use `module.exports` instead

---

## 🔹 9. `Promise.resolve()` vs `setImmediate`

```js
console.log('start');

setImmediate(() => console.log('setImmediate'));
Promise.resolve().then(() => console.log('promise'));

console.log('end');
```

✅ **Output**: `start end promise setImmediate`

### 💡 Why?
- `Promise.then()` → **microtask queue**
- `setImmediate()` → **Check phase** (macrotask)
- **Microtasks run before macrotasks**

---

## 🔹 10. `fs.readFile` vs `fs.readFileSync`

```js
console.log('1');
fs.readFile('file.txt', () => console.log('2'));
fs.readFileSync('file.txt');
console.log('3');
```

✅ **Output**: `1 3 2`

### 💡 Why?
- `fs.readFileSync()` → **blocks** until file is read
- `fs.readFile()` → **non-blocking**, callback queued
- So:
  - `1`
  - Sync read → `3`
  - Async callback → `2`

---

## 🔹 11. `cluster.fork()` and Global Variables

```js
let counter = 0;

if (cluster.isMaster) {
  cluster.fork();
  cluster.fork();
} else {
  counter++;
  console.log(`Worker ${process.pid}: ${counter}`); // Always 1
}
```

✅ **Output**: Each worker logs `1`

### 💡 Why?
- Each worker is a **separate process**
- No shared memory
- `counter` is **not shared**

> ✅ Use **Redis** or **message passing** (`process.send`) for inter-process communication

---

## 🔹 12. `unhandledRejection` vs `uncaughtException`

```js
process.on('unhandledRejection', (err) => {
  console.log('Unhandled Rejection:', err.message);
});

process.on('uncaughtException', (err) => {
  console.log('Uncaught Exception:', err.message);
  process.exit(1); // Recommended
});

Promise.reject('Oops'); // → unhandledRejection

throw new Error('Boom'); // → uncaughtException
```

✅ **Key Difference**:
- `unhandledRejection`: Unhandled **Promise rejection**
- `uncaughtException`: Unhandled **sync error** — process is in **undefined state**

> ⚠️ `uncaughtException` should **log and exit** — never continue

---

## 🔹 13. `Buffer` and Character Encoding

```js
const buf = Buffer.from('hello');
console.log(buf); // <Buffer 68 65 6c 6c 6f>

const str = buf.toString('utf8');
console.log(str); // hello

// Truncate buffer
buf[0] = 0;
console.log(buf.toString()); // ello (68 → 00)
```

✅ `Buffer` is mutable — can modify in place

---

## 🔹 14. `EventEmitter` Memory Leak

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();

// Add 11+ listeners → warning
for (let i = 0; i < 11; i++) {
  emitter.on('event', () => console.log(i));
}
```

✅ **Output**: `MaxListenersExceededWarning`

### ✅ Fix:
```js
emitter.setMaxListeners(20); // Increase limit
// Or remove listeners: emitter.removeListener('event', handler)
```

---

## 🔹 15. `child_process.exec` vs `spawn`

```js
const { exec, spawn } = require('child_process');

exec('ls -lh', (err, stdout) => {
  console.log(stdout); // Entire output as string
});

spawn('ls', ['-lh']).stdout.on('data', (data) => {
  console.log(data.toString()); // Stream chunks
});
```

✅ `exec`: **buffered**, good for small output  
✅ `spawn`: **streaming**, good for large/long output

---

## 🔹 16. `require` and Circular Dependencies

```js
// a.js
console.log('a starting');
exports.done = false;
const b = require('./b.js');
exports.done = true;
console.log('in a, b.done = %j', b.done);

// b.js
console.log('b starting');
exports.done = false;
const a = require('./a.js');
exports.done = true;
console.log('in b, a.done = %j', a.done);

// main.js
console.log('main starting');
const a = require('./a');
const b = require('./b');
console.log('in main, a.done = %j, b.done = %j', a.done, b.done);
```

✅ **Output**:
```
main starting
a starting
b starting
in b, a.done = false
in a, b.done = true
in main, a.done = true, b.done = true
```

### 💡 Why?
- `b.js` requires `a.js` → but `a.js` not fully loaded → returns **partial** `a` (with `done = false`)
- Later, `a.done` becomes `true`

> ⚠️ Circular deps work but can cause bugs — avoid when possible.

---

## 🔹 17. `process.exit()` vs `return`

```js
console.log('1');
process.exit(0);
console.log('2'); // Never runs
```

✅ `process.exit()` **immediately terminates** the process — no more code runs

> ❌ Don’t use in Express middleware — use `res.end()` or `next()`

---

## 🔹 18. `global` vs `window` vs `self`

```js
console.log(global === window); // false (Node.js)
console.log(global === self);   // false (Node.js)

// In browser: window === self
```

✅ `global` is Node.js global object  
✅ `window` and `self` are browser globals

---

## 🔹 19. `Buffer.allocUnsafe()` vs `Buffer.alloc()`

```js
const buf1 = Buffer.alloc(5);        // Filled with 0
const buf2 = Buffer.allocUnsafe(5);  // Uninitialized memory (faster, but may contain old data)
```

✅ Use `Buffer.alloc()` in **security-sensitive** code  
✅ `Buffer.allocUnsafe()` can leak memory — only use if you overwrite all bytes

---

## 🔹 20. `__filename` and ES Modules

```js
// In CommonJS (.js)
console.log(__filename); // /path/to/file.js

// In ES Modules (.mjs)
import { fileURLToPath } from 'url';
import { dirname } from 'path';
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
```

✅ `__filename` and `__dirname` **not available** in ES Modules — must derive from `import.meta.url`

---

## ✅ Final Tip

> 🎯 In interviews, **explain the "why"**:
> _"This happens because of the Event Loop phase order / module caching / microtask queue..."_

That shows **deep understanding**, not just memorization.

---
