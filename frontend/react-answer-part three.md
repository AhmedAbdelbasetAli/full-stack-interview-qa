# 🧠 React Deep Dive: Advanced Concepts (Part 3)  
**Data Flow, Performance, and Reconciliation**

This document continues the deep dive into essential React concepts — critical for **interviews**, **real-world development**, and **senior-level roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Memory/execution model
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 11. How do you pass data between components in React?

There are **5 primary ways** to pass data in React:

| Method | Use Case |
|-------|---------|
| 🔹 **Props** | Parent → Child (most common) |
| 🔹 **Callback Functions** | Child → Parent |
| 🔹 **Context API** | Deeply nested components |
| 🔹 **useRef + Callbacks** | Imperative data access |
| 🔹 **State Management (Redux, Zustand)** | Global state across app |

---

### 🔹 11.1 Props – Parent to Child

```jsx
function Parent() {
  const [name, setName] = useState("Alice");
  return <Child name={name} />;
}

function Child({ name }) {
  return <p>Hello, {name}</p>;
}
```

✅ Simple, predictable  
❌ Only works for direct parent-child

---

### 🔹 11.2 Callback Functions – Child to Parent

```jsx
function Parent() {
  const handleNameChange = (newName) => {
    console.log("Name changed to:", newName);
  };
  return <Child onNameChange={handleNameChange} />;
}

function Child({ onNameChange }) {
  return (
    <button onClick={() => onNameChange("Bob")}>
      Change Name
    </button>
  );
}
```

✅ Enables two-way communication  
✅ Keeps child reusable

---

### 🔹 11.3 Context API – Anywhere in Tree

See **Question 12** below.

---

### 🔹 11.4 useRef – Imperative Access

```jsx
function Parent() {
  const childRef = useRef();
  const focusInput = () => childRef.current.focus();

  return (
    <>
      <Child ref={childRef} />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}

const Child = React.forwardRef((props, ref) => {
  return <input ref={ref} />;
});
```

✅ For DOM access or imperative methods  
❌ Not for general data flow

---

### 🔹 11.5 State Management Libraries

For large apps:
- **Redux**, **Zustand**, **Jotai**, **Recoil**

```jsx
// Zustand example
const useStore = create((set) => ({
  user: null,
  setUser: (user) => set({ user })
}));

// Any component
const { user, setUser } = useStore();
```

✅ Global state, complex logic  
❌ Overkill for simple apps

---

### 📌 Interview Answer

> _"I pass data using props for parent-child communication, callbacks for child-to-parent, and Context for deeply nested components. For global state like user auth or theme, I use Zustand or Redux. I avoid prop drilling by lifting state up or using Context. For DOM access, I use `useRef`. The choice depends on the app size and data flow complexity."_  

---

## 12. What is context in React? When would you use `useContext`?

> **Context** allows you to **pass data through the component tree** without manually passing props down.

It’s like a **global state** for a part of your app.

---

### 🔹 12.1 How Context Works

```jsx
// 1. Create Context
const ThemeContext = React.createContext('light');

// 2. Wrap components with Provider
function App() {
  const [theme, setTheme] = useState('dark');
  return (
    <ThemeContext.Provider value={theme}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// 3. Consume in any child
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>I am {theme}</button>;
}
```

---

### 🔹 12.2 When to Use Context

| Use Case | Example |
|--------|--------|
| ✅ Theme | Dark/light mode |
| ✅ User Authentication | Current user, login status |
| ✅ Language/Locale | i18n |
| ✅ UI Preferences | Font size, layout |
| ✅ Shared State | Shopping cart, notifications |

---

### 🔹 12.3 When NOT to Use Context

| Anti-Pattern | Better Alternative |
|-------------|-------------------|
| Every state update | Props or local state |
| High-frequency updates (e.g., mouse position) | Can cause unnecessary re-renders |
| Simple parent-child flow | Just use props |

> ⚠️ Context causes **all consumers to re-render** when value changes.

---

### 🔹 12.4 Performance Optimization

Split context to avoid unnecessary re-renders:
```jsx
// Instead of one big context
const AppContext = createContext();

// Use multiple small contexts
const ThemeContext = createContext();
const UserContext = createContext();
```

✅ Or use **state management libraries** for complex cases.

---

### 📌 Interview Answer

> _"Context lets me pass data through the component tree without prop drilling. I use `useContext` to consume values like theme, user auth, or locale. I avoid using it for high-frequency updates or simple props — it’s best for global, infrequently changing data. To prevent unnecessary re-renders, I split context by domain and use memoization when needed."_  

---

## 13. What is prop drilling? How can you avoid it?

> **Prop drilling** is the process of **passing props through multiple intermediate components** that don’t need them.

```jsx
<App>
  <Layout theme={theme}>
    <Sidebar theme={theme}>
      <UserProfile theme={theme} /> {/* Only this needs it */}
    </Sidebar>
  </Layout>
</App>
```

❌ `theme` is passed through `Layout` and `Sidebar` just to reach `UserProfile`.

---

### 🔹 13.1 Problems with Prop Drilling

| Issue | Impact |
|------|--------|
| 🔹 **Boilerplate** | Many components must accept and forward props |
| 🔹 **Tight Coupling** | Components depend on props they don’t use |
| 🔹 **Refactoring Difficulty** | Changing one prop affects many files |
| 🔹 **Performance** | Unnecessary re-renders if props change |

---

### 🔹 13.2 How to Avoid It

| Solution | When to Use |
|--------|-------------|
| ✅ **Context API** | Global data (theme, user, locale) |
| ✅ **State Management (Zustand, Redux)** | Complex global state |
| ✅ **Lifting State Up** | Shared state between siblings |
| ✅ **Composition (Children as Props)** | UI structure without data |

#### 🟦 Example: Composition
```jsx
function Layout({ sidebar, content }) {
  return (
    <div className="layout">
      <aside>{sidebar}</aside>
      <main>{content}</main>
    </div>
  );
}

// Usage
<Layout
  sidebar={<UserProfile />}
  content={<Dashboard />}
/>
```

✅ No prop drilling — parent decides what to render.

---

### 📌 Interview Answer

> _"Prop drilling is passing props through components that don’t need them — it creates boilerplate and tight coupling. I avoid it by using Context for global data like theme or user, Zustand for complex state, or lifting state up when siblings share data. For UI structure, I use composition with `children` or render props. This keeps components decoupled and easier to maintain."_  

---

## 14. What is the purpose of `useMemo` and `useCallback`? When should you use them?

> These are **performance optimization Hooks** — use them to **prevent unnecessary recalculations**.

---

### 🔹 14.1 `useMemo` – Memoize Expensive Values

> Returns a **memoized value** until dependencies change.

```jsx
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);
```

✅ Use when:
- Computation is expensive (large array filter, math)
- Value is used in rendering
- Dependencies don’t change often

#### 🟦 Example: Filtering a Large List
```jsx
const filteredItems = useMemo(() => {
  return items.filter(item => item.category === selectedCategory);
}, [items, selectedCategory]);
```

❌ Don’t use for cheap operations — overhead > benefit.

---

### 🔹 14.2 `useCallback` – Memoize Functions

> Returns a **memoized function** reference.

```jsx
const handleClick = useCallback(() => {
  console.log('Clicked', id);
}, [id]);
```

✅ Use when:
- Passing function to `React.memo` components
- Function is a dependency in `useEffect`
- Prevents unnecessary re-renders

#### 🟦 Example: With `React.memo`
```jsx
const Button = React.memo(({ onClick, children }) => {
  return <button onClick={onClick}>{children}</button>;
});

function Parent({ id }) {
  const handleClick = useCallback(() => {
    console.log('Button clicked:', id);
  }, [id]);

  return <Button onClick={handleClick}>Click Me</Button>;
}
```

✅ Without `useCallback`, `Button` would re-render every time.

---

### 🔹 14.3 Rules of Thumb

| Rule | Why |
|-----|-----|
| ✅ Use `useMemo` for expensive calculations | Avoid re-computing |
| ✅ Use `useCallback` for functions in dependencies | Prevent infinite loops in `useEffect` |
| ❌ Don’t optimize prematurely | Can hurt performance |
| ❌ Don’t wrap every function | Only when it matters |

---

### 📌 Interview Answer

> _"`useMemo` memoizes expensive values so they’re only recalculated when dependencies change. `useCallback` memoizes function references to prevent unnecessary re-renders in `React.memo` components or dependency changes in `useEffect`. I use them for performance-critical parts — like filtering large lists or passing callbacks to optimized children. But I avoid overusing them — premature optimization can hurt readability and performance."_  

---

## 15. What is reconciliation in React?

> **Reconciliation** is the process where React **updates the UI** by comparing the **new Virtual DOM** with the **previous one** and applying minimal changes to the **real DOM**.

It’s the core of React’s performance.

---

### 🔹 15.1 How Reconciliation Works

```
1. State/Props Change → New Virtual DOM
2. Diffing (Reconciliation) → Compare old vs new
3. Patching → Apply minimal changes to Real DOM
```

React uses a **heuristic diffing algorithm** to make this fast.

---

### 🔹 15.2 Reconciliation Rules

1. **Different Element Types** → Replace subtree
   ```jsx
   <div> → <span> // Entire subtree destroyed
   ```
2. **Same Type** → Update only changed props
   ```jsx
   <div className="old" /> → <div className="new" /> // Only className updated
   ```
3. **Lists** → Use `key` to track identity
   ```jsx
   <Item key={item.id} /> // React knows which item was added/removed
   ```

> Without `key`, React assumes items match by index — can cause bugs.

---

### 🔹 15.3 Why Keys Matter

#### ❌ Without Key (Index-based)
```jsx
// Initial: ['Alice', 'Bob']
// After adding 'Charlie' at start:
// React thinks:
// index 0: 'Alice' → now 'Charlie' → update text
// index 1: 'Bob' → stays 'Bob'
// But DOM should be recreated
```

✅ React **reuses DOM elements incorrectly**

#### ✅ With Key
```jsx
<li key="alice">Alice</li>
<li key="bob">Bob</li>
```
→ React knows `alice` and `bob` moved — updates correctly.

---

### 🔹 15.4 Performance Implications

| Practice | Effect |
|--------|--------|
| ✅ Use stable, unique `key` | Fast diffing |
| ❌ Use `index` in dynamic lists | Bugs, unnecessary re-renders |
| ✅ Memoize with `useMemo`/`useCallback` | Fewer changes to diff |
| ✅ Use `React.memo` | Skip reconciliation for unchanged components |

---

### 📌 Interview Answer

> _"Reconciliation is React’s process of updating the UI by comparing the new Virtual DOM with the previous one and applying minimal changes to the real DOM. It uses heuristics: if element types change, it replaces the subtree; if same, it updates props; for lists, it uses `key` to track identity. This makes React fast. I always use unique, stable `key` props in lists to ensure correct reconciliation and avoid bugs."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I avoid prop drilling with Context, optimize performance with `useMemo` and `useCallback`, and ensure correct reconciliation with proper `key` props."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---


Just say: _"Let’s do [topic]"_

You're mastering React at a **senior level**. 💪
