# 🧠 React Deep Dive: Core Concepts  
**Functional vs Class Components, Hooks, and Key Props**

This document provides a **comprehensive, in-depth explanation** of essential React concepts — critical for **interviews**, **real-world development**, and **senior-level roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Memory/execution model
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 1. What is the difference between functional components and class components?

| Feature | Functional Components | Class Components |
|--------|------------------------|------------------|
| **Syntax** | Functions | ES6 Classes |
| **State** | `useState` Hook | `this.state` |
| **Lifecycle** | `useEffect` Hook | `componentDidMount`, `componentDidUpdate`, etc. |
| **`this` keyword** | Not used | Required for methods and state |
| **Performance** | Slightly faster (no class overhead) | Slightly heavier |
| **Reusability** | Custom Hooks | Higher-Order Components (HOCs), Render Props |
| **Status** | ✅ Modern standard (since React 16.8) | ❌ Legacy (still works, but not recommended) |

---

### 🔹 1.1 Functional Components – Modern Way

> A **function that returns JSX**.

```jsx
function Welcome({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

#### 🟦 With Hooks (Post-React 16.8)
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

✅ Pros:
- Simpler syntax
- Easier to test
- Better tree-shaking
- Enables Hooks

---

### 🔹 1.2 Class Components – Legacy Way

> A **class that extends `React.Component`** and implements `render()`.

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

#### 🟦 With State and Lifecycle
```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.handleClick}>
          Increment
        </button>
      </div>
    );
  }
}
```

❌ Cons:
- Verbose
- Harder to reuse logic (no Hooks)
- `this` binding issues
- Larger bundle size

---

### 🔹 1.3 Key Differences

| Aspect | Functional | Class |
|------|-----------|-------|
| **State** | `useState()` | `this.state`, `this.setState()` |
| **Lifecycle** | `useEffect()` | `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` |
| **Binding** | No `this` → no binding needed | Arrow functions or bind `this` |
| **Performance** | Faster (function call) | Slower (class instantiation) |
| **Reusability** | Custom Hooks | HOCs, Render Props |

---

### 📌 Interview Answer

> _"Functional components are functions that return JSX and use Hooks like `useState` and `useEffect` for state and side effects. Class components are ES6 classes that extend `React.Component` and use `this.state` and lifecycle methods. Since React 16.8, functional components with Hooks are the standard — they’re simpler, more reusable, and avoid `this` confusion. I always use functional components unless working with legacy code."_  

---

## 2. What are React Hooks? Name some built-in hooks and explain their purpose.

> **Hooks** are **functions** that let you “hook into” React state and lifecycle features **from functional components**.

They were introduced in **React 16.8** to solve:
- Logic reuse (before: HOCs, Render Props)
- Complex components
- Confusing `this` binding

---

### 🔹 2.1 Rules of Hooks
1. ✅ Only call Hooks **at the top level** (not in loops, conditions, nested functions)
2. ✅ Only call Hooks from **React functions** (components or custom Hooks)

---

### 🔹 2.2 Built-in Hooks

| Hook | Purpose |
|------|--------|
| `useState` | Add state to functional components |
| `useEffect` | Handle side effects (API calls, subscriptions, DOM updates) |
| `useContext` | Access context without wrapping in Consumer |
| `useReducer` | Manage complex state logic (like Redux) |
| `useCallback` | Memoize functions to prevent unnecessary re-renders |
| `useMemo` | Memoize expensive calculations |
| `useRef` | Access DOM elements or persist mutable values across renders |
| `useImperativeHandle` | Customize instance value exposed by `ref` |
| `useLayoutEffect` | Like `useEffect`, but fires synchronously after DOM mutations |

---

### 🔹 2.3 Example: Using Multiple Hooks
```jsx
import { useState, useEffect, useMemo, useCallback } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // Fetch user data
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setUser)
      .finally(() => setLoading(false));
  }, [userId]);

  // Expensive calculation
  const fullName = useMemo(() => {
    return user ? `${user.firstName} ${user.lastName}` : '';
  }, [user]);

  // Memoized function
  const handleSave = useCallback(() => {
    // Save logic
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>{fullName}</h1>
      <button onClick={handleSave}>Save</button>
    </div>
  );
}
```

---

### 📌 Interview Answer

> _"React Hooks are functions that allow functional components to use state and lifecycle features. `useState` adds state, `useEffect` handles side effects like API calls, `useContext` accesses context, and `useCallback`/`useMemo` optimize performance. Hooks make it easier to reuse logic without HOCs or render props. I use them to keep components clean, avoid `this` issues, and improve performance through memoization."_  

---

## 3. What does `useState` do? When would you use it?

> `useState` is a **Hook that adds state to a functional component**.

It returns an array: `[state, setState]`

```js
const [count, setCount] = useState(0);
```

---

### 🔹 3.1 How It Works

- `useState(initialValue)` → returns current state and a setter
- `setState` can take a value or a function
- Triggers a re-render when state changes

#### 🟦 Basic Example
```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

#### 🟦 Functional Update (Safe for Async)
```js
setCount(prev => prev + 1); // Always uses latest value
```

✅ Use when:
- State updates depend on previous state
- Multiple `setState` calls in one event

---

### 🔹 3.2 When to Use `useState`

| Use Case | Example |
|--------|--------|
| ✅ Toggle UI state | `const [isOpen, setIsOpen] = useState(false)` |
| ✅ Form inputs | `const [email, setEmail] = useState('')` |
| ✅ Loading states | `const [loading, setLoading] = useState(true)` |
| ✅ Counters, flags, selections | `const [selected, setSelected] = useState(null)` |

---

### ⚠️ Common Pitfalls

| Mistake | Fix |
|--------|-----|
| `setCount(count + 1)` in async | Use `setCount(c => c + 1)` |
| Forgetting to destructure | `const [state, setState] = useState()` |
| Using state immediately after `setState` | Remember: `setState` is asynchronous |

---

### 📌 Interview Answer

> _"`useState` is a Hook that lets functional components have local state. It returns the current state and a function to update it. I use it for any mutable data like form inputs, UI toggles, or loading states. When state depends on the previous value, I use the functional form `setState(prev => prev + 1)` to ensure correctness. It’s the foundation of React’s declarative state management."_  

---

## 4. How does `useEffect` work? What are its different use cases based on the dependency array?

> `useEffect` lets you perform **side effects** in functional components — like data fetching, subscriptions, or manual DOM manipulation.

```js
useEffect(() => {
  // Side effect
  return () => {
    // Cleanup
  };
}, [dependencies]);
```

---

### 🔹 4.1 Dependency Array Behavior

| Dependency Array | When It Runs | Use Case |
|------------------|-------------|---------|
| `[]` (empty) | Once after first render | Initialization, subscriptions |
| `[dep]` (with values) | When `dep` changes | Update based on props/state |
| **No array** | After every render | Rare (can cause infinite loops) |
| `undefined` | After every render | Same as no array |

---

### 🔹 4.2 Use Cases

#### 🟦 1. On Mount Only (Empty Array)
```jsx
useEffect(() => {
  console.log("Component mounted");
  const subscription = api.subscribe();
  return () => {
    subscription.unsubscribe();
  };
}, []);
```

✅ Like `componentDidMount`

---

#### 🟦 2. On Update (With Dependencies)
```jsx
useEffect(() => {
  if (userId) {
    fetchUser(userId);
  }
}, [userId]); // Re-runs when userId changes
```

✅ Like `componentDidUpdate`

---

#### 🟦 3. Cleanup (Return Function)
```jsx
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []);
```

✅ Prevents memory leaks

---

#### 🟦 4. No Dependencies (Rare)
```jsx
useEffect(() => {
  // Runs after every render
  // ❌ Can cause infinite loops if it updates state
});
```

> ⚠️ Avoid unless intentional.

---

### ⚠️ Common Mistakes

| Mistake | Fix |
|--------|-----|
| Missing dependencies | Add them to array |
| Infinite loop | Add proper cleanup or dependencies |
| Forgetting cleanup | Always clean up subscriptions, timers |
| Using async directly | Wrap in `async` function inside `useEffect` |

```jsx
useEffect(() => {
  const fetchData = async () => {
    const res = await fetch('/api');
    setData(await res.json());
  };
  fetchData();
}, []);
```

---

### 📌 Interview Answer

> _"`useEffect` runs side effects after rendering. With an empty dependency array, it runs once — like `componentDidMount`. With dependencies, it runs when those values change — like `componentDidUpdate`. It can return a cleanup function for subscriptions or event listeners. I use it for API calls, setting up listeners, or manual DOM updates. I always include correct dependencies to avoid bugs and infinite loops."_  

---

## 5. What is the purpose of the `key` prop when rendering lists?

> The `key` prop helps React **identify which items have changed, been added, or removed** in a list.

```jsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

---

### 🔹 5.1 Why Keys Are Required

Without `key`, React uses **index as default**:
```jsx
// ❌ Bad: Index as key
{items.map((item, index) => <li key={index}>{item.name}</li>)}
```

#### 🟦 Problem: Reordering Issues
```js
// Initial: ['Alice', 'Bob']
// After adding 'Charlie' at start:
// React thinks:
// index 0: 'Alice' → now 'Charlie' → update text
// index 1: 'Bob' → stays 'Bob'
// But DOM should be recreated
```

✅ React **reuses DOM elements incorrectly** → bugs

---

### 🔹 5.2 Good Keys

| Key Type | Good? | Why |
|--------|------|-----|
| `item.id` (unique) | ✅ Yes | Stable, predictable |
| `item.email` (unique) | ✅ Yes | If immutable |
| `index` | ❌ Only if list is static and never reordered |
| `Math.random()` | ❌ No | Changes every render → no reconciliation |

---

### 🔹 5.3 How Keys Work Internally

When React reconciles:
1. Compares new and old lists
2. Uses `key` to match items
3. Only updates, adds, or removes what changed

✅ With good keys → minimal DOM changes  
❌ With bad keys → unnecessary re-renders

---

### 🔹 5.4 Best Practices

| Rule | Why |
|-----|-----|
| ✅ Use stable, unique IDs (e.g., database `id`) | Ensures correct identity |
| ✅ Avoid `index` unless list is static | Prevents UI bugs |
| ✅ Never use `Math.random()` | Breaks reconciliation |

---

### 📌 Interview Answer

> _"The `key` prop helps React efficiently update the DOM by tracking each element’s identity. Without it, React defaults to the index, which can cause bugs when reordering or filtering lists. I always use a stable, unique identifier like a database `id`. This ensures React can correctly add, update, or remove items without unnecessary re-renders. Keys are essential for performance and correctness in dynamic lists."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use functional components with `useState` for state and `useEffect` for API calls. I render lists with proper `key` props and optimize with `useMemo` and `useCallback`."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---

