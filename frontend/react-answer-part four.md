# 🧠 React Deep Dive: Advanced Concepts (Part 4)  
**Forms, HOCs, Children, Custom Hooks & Performance**

This document completes the React deep dive — covering **advanced patterns, best practices, and performance optimization**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 16. How do you handle forms in React?

There are **two main approaches** to handling forms in React:

| Approach | When to Use |
|--------|-------------|
| 🔹 **Controlled Components** | Most cases — real-time validation, dynamic UI |
| 🔹 **Uncontrolled Components** | File inputs, integrating with non-React libraries |

---

### 🔹 16.1 Controlled Components (Recommended)

> Form data is handled by **React state**.

```jsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState({});

  const validate = () => {
    const newErrors = {};
    if (!email) newErrors.email = 'Email is required';
    if (password.length < 6) newErrors.password = 'Too short';
    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const formErrors = validate();
    if (Object.keys(formErrors).length === 0) {
      console.log('Submitting:', { email, password });
    } else {
      setErrors(formErrors);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      {errors.email && <span>{errors.email}</span>}

      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      {errors.password && <span>{errors.password}</span>}

      <button type="submit">Login</button>
    </form>
  );
}
```

✅ Pros:
- Full control over input
- Real-time validation
- Easy to test
- Can reset programmatically

❌ Cons:
- More boilerplate
- Performance cost for large forms

---

### 🔹 16.2 Uncontrolled Components

> Form data is handled by the **DOM** — accessed via `ref`.

```jsx
function FileUpload() {
  const fileInput = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    const file = fileInput.current.files[0];
    console.log('File:', file);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" ref={fileInput} />
      <button type="submit">Upload</button>
    </form>
  );
}
```

✅ Use for:
- `<input type="file">` (can't set `value`)
- Integrating with jQuery plugins
- Performance-critical forms

---

### 🔹 16.3 Third-Party Libraries

For complex forms, use:
- **React Hook Form** (recommended — uncontrolled, fast)
- **Formik**
- **Yup** (validation)

```jsx
// React Hook Form example
import { useForm } from 'react-hook-form';

function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();
  
  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("email", { required: true })} />
      {errors.email && <span>Email is required</span>}
      <button type="submit">Submit</button>
    </form>
  );
}
```

✅ React Hook Form is **faster** because it uses uncontrolled components.

---

### 📌 Interview Answer

> _"I use controlled components for most forms — they give full control over input and enable real-time validation. For file uploads or performance-critical cases, I use uncontrolled components with `useRef`. For complex forms, I prefer React Hook Form because it's faster and reduces re-renders. I always prevent default on submit and validate before submission."_  

---

## 17. What are Higher-Order Components (HOCs)? Give an example.

> A **Higher-Order Component (HOC)** is a **function that takes a component and returns a new enhanced component**.

It’s a **pattern for reusing component logic** — like mixins, but functional.

---

### 🔹 17.1 HOC Pattern

```js
const withEnhancement = (WrappedComponent) => {
  return function EnhancedComponent(props) {
    return <WrappedComponent {...props} extraProp="value" />;
  };
};
```

✅ Naming convention: `withXxx`

---

### 🔹 17.2 Example: Authentication HOC

```jsx
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { user } = useAuth(); // Assume custom hook

    if (!user) {
      return <div>Please log in</div>;
    }

    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const ProfilePage = () => <div>Welcome, {user.name}</div>;
const ProtectedProfile = withAuth(ProfilePage);
```

---

### 🔹 17.3 Example: Logging HOC

```jsx
function withLogging(WrappedComponent) {
  return function LoggingComponent(props) {
    useEffect(() => {
      console.log('Component mounted:', WrappedComponent.name);
      return () => console.log('Component unmounted');
    }, []);

    return <WrappedComponent {...props} />;
  };
}

// Usage
const HomePage = () => <div>Home</div>;
const LoggedHomePage = withLogging(HomePage);
```

---

### 🔹 17.4 Modern Alternative: Custom Hooks

HOCs are **largely replaced by Custom Hooks** since React 16.8.

```jsx
// Instead of HOC, use a Hook
function useAuth() {
  const { user } = useContext(AuthContext);
  if (!user) throw new Error('Not authenticated');
  return user;
}

// In component
function Profile() {
  const user = useAuth();
  return <div>Welcome, {user.name}</div>;
}
```

✅ Hooks are simpler, composable, and avoid wrapper hell.

---

### 📌 Interview Answer

> _"A Higher-Order Component is a function that takes a component and returns a new one with added functionality. I’ve used HOCs for authentication, logging, and data fetching. For example, `withAuth` wraps a component and shows a login screen if the user isn’t logged in. But today, I prefer Custom Hooks — they’re simpler, composable, and avoid the wrapper component hierarchy that HOCs create."_  

---

## 18. What is the difference between `props.children` and `props`?

| Concept | Purpose | Example |
|--------|--------|--------|
| `props` | All properties passed to a component | `name`, `onClick`, `data` |
| `props.children` | Content between opening and closing tags | What’s inside `<Component>HERE</Component>` |

---

### 🔹 18.1 `props` – All Passed Properties

```jsx
function Welcome({ name, theme, onLogin }) {
  return <h1 className={theme}>Hello, {name}</h1>;
}

// Usage
<Welcome name="Alice" theme="dark" onLogin={handleLogin} />
```

✅ `props` includes `name`, `theme`, `onLogin`

---

### 🔹 18.2 `props.children` – Nested Content

```jsx
function Card({ children, title }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="content">
        {children}
      </div>
    </div>
  );
}

// Usage
<Card title="Profile">
  <p>Name: Alice</p>
  <button>Edit</button>
</Card>
```

✅ `children` = `<p>Name: Alice</p><button>Edit</button>`

---

### 🔹 18.3 `children` is Just a Prop

> `children` is **not special syntax** — it’s a regular prop.

```jsx
// These are equivalent:
<Card><p>Content</p></Card>

<Card children={<p>Content</p>} />
```

---

### 🔹 18.4 Advanced: Multiple Children as Props

```jsx
function Layout({ header, sidebar, content }) {
  return (
    <div>
      <header>{header}</header>
      <aside>{sidebar}</aside>
      <main>{content}</main>
    </div>
  );
}

// Usage
<Layout
  header={<Navbar />}
  sidebar={<Sidebar />}
  content={<Dashboard />}
/>
```

✅ More flexible than `children` alone.

---

### 📌 Interview Answer

> _"`props` refers to all properties passed to a component, like `name` or `onClick`. `props.children` is a special prop that contains the content between the opening and closing tags of a component. It’s commonly used for layout wrappers like `Card` or `Modal`. I use `children` for generic containers and named props like `header` or `footer` for more structured layouts. Under the hood, `children` is just a regular prop — React provides it for convenience."_  

---

## 19. Can you write a custom hook? What are the rules of hooks?

✅ **Yes!** Custom Hooks are **functions that use other Hooks** to encapsulate reusable logic.

They must follow **two rules**.

---

### 🔹 19.1 Rules of Hooks

1. ✅ **Only Call Hooks at the Top Level**
   - Don’t call Hooks inside loops, conditions, or nested functions
   ```js
   // ❌ Bad
   if (condition) {
     useEffect(() => {});
   }

   // ✅ Good
   useEffect(() => {
     if (condition) { ... }
   }, [condition]);
   ```

2. ✅ **Only Call Hooks from React Functions**
   - Components or other custom Hooks
   - Not regular JavaScript functions

> 🔧 ESLint plugin `eslint-plugin-react-hooks` enforces these rules.

---

### 🔹 19.2 Example: `useLocalStorage`

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      // Ignore write errors
    }
  }, [key, value]);

  return [value, setValue];
}

// Usage
function DarkModeToggle() {
  const [darkMode, setDarkMode] = useLocalStorage('darkMode', false);
  return (
    <button onClick={() => setDarkMode(!darkMode)}>
      {darkMode ? 'Light' : 'Dark'} Mode
    </button>
  );
}
```

---

### 🔹 19.3 Example: `useFetch`

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const res = await fetch(url);
        const json = await res.json();
        setData(json);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserProfile({ userId }) {
  const { data, loading } = useFetch(`/api/users/${userId}`);
  if (loading) return <div>Loading...</div>;
  return <div>{data.name}</div>;
}
```

---

### 📌 Interview Answer

> _"Yes, I write custom hooks to reuse stateful logic. A custom hook is a function that uses other hooks like `useState` and `useEffect`. It must follow two rules: only call hooks at the top level, and only from React functions. I’ve written hooks like `useLocalStorage` for persistent state and `useFetch` for data loading. Custom hooks make components cleaner and logic reusable without HOCs or render props."_  

---

## 20. How do you optimize performance in a React application?

Performance optimization in React is about **reducing unnecessary re-renders and work**.

Here are **10 proven strategies**:

---

### 🔹 20.1 1. Use `React.memo()` for Pure Components

```jsx
const Button = React.memo(({ onClick, children }) => {
  return <button onClick={onClick}>{children}</button>;
});
```

✅ Prevents re-render if props haven’t changed  
❌ Don’t overuse — has overhead

---

### 🔹 20.2 2. Memoize Expensive Calculations with `useMemo`

```jsx
const filteredItems = useMemo(() => 
  items.filter(item => item.active), 
  [items]
);
```

✅ Avoids re-computing on every render  
❌ Only for expensive operations

---

### 🔹 20.3 3. Prevent Function Recreation with `useCallback`

```jsx
const handleClick = useCallback(() => {
  console.log(id);
}, [id]);
```

✅ Keeps function reference stable  
✅ Essential for `React.memo` and `useEffect` dependencies

---

### 🔹 20.4 4. Use `useTransition` for Non-Blocking Updates (React 18+)

```jsx
const [isPending, startTransition] = useTransition();

const handleSearch = (query) => {
  startTransition(() => {
    setFilter(query);
  });
};
```

✅ Keeps UI responsive during heavy rendering  
✅ Lowers priority of non-urgent updates

---

### 🔹 20.5 5. Code Splitting with `React.lazy` + `Suspense`

```jsx
const Dashboard = React.lazy(() => import('./Dashboard'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Dashboard />
    </Suspense>
  );
}
```

✅ Reduces initial bundle size  
✅ Improves load time

---

### 🔹 20.6 6. Virtualize Long Lists

Use `react-window` or `react-virtualized`:

```jsx
<FixedSizeList height={600} itemCount={1000} itemSize={50}>
  {({ index, style }) => <div style={style}>Row {index}</div>}
</FixedSizeList>
```

✅ Renders only visible items  
✅ Essential for large lists

---

### 🔹 20.7 7. Avoid Inline Objects/Functions in Render

❌ Bad:
```jsx
<ListItem key={item.id} style={{ color: 'red' }} onClick={() => handle(item.id)} />
```

✅ Good:
```jsx
const itemStyle = useMemo(() => ({ color: 'red' }), []);
const handleClick = useCallback((id) => () => handle(id), []);

<ListItem key={item.id} style={itemStyle} onClick={handleClick(item.id)} />
```

---

### 🔹 20.8 8. Use Production Build

✅ Always test performance in `npm run build` — not dev mode  
✅ Dev mode has extra checks and is slower

---

### 🔹 20.9 9. Profile with React DevTools

- Use **Profiler** to measure render times
- Identify expensive components
- Optimize hot paths

---

### 🔹 20.10 10. Avoid Premature Optimization

✅ **Rule**: Optimize only when needed  
✅ Use profiling to find bottlenecks  
✅ Don’t memoize everything — it can hurt performance

---

### 📌 Interview Answer

> _"I optimize React performance by reducing unnecessary re-renders. I use `React.memo`, `useMemo`, and `useCallback` for expensive components and calculations. For large lists, I use virtualization. I code-split with `React.lazy` to reduce bundle size. In React 18, I use `useTransition` for non-urgent updates. I avoid inline functions and objects in JSX. But I don’t optimize prematurely — I profile first with React DevTools and focus on actual bottlenecks."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I optimize with `useMemo` and `useCallback`, avoid prop drilling with Context, and use virtualization for large lists."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---


Just say: _"Let’s do [topic]"_

You're mastering React at a **senior level**. 💪
