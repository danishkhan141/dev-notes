Below are **Top 50 React Interview Questions** explained professionally for your preparation. Since you are a **Java backend developer**, I’ll keep examples simple and practical, especially from a real project point of view like React frontend calling Spring Boot APIs.

React’s current official docs focus heavily on **function components, hooks, effects, state management, Suspense, Server Components, and performance patterns**. React docs also mention React **v19.2** currently, and React 19 introduced newer patterns such as Server Components support and reduced need for `forwardRef` in future React versions. ([React][1])

---

# React Interview Questions — Top 50

## 1. What is React?

**Answer:**
React is a JavaScript library used to build user interfaces, especially single-page applications. It allows us to build UI using reusable components.

**Example:**

```jsx
function Welcome() {
  return <h1>Welcome Danish</h1>;
}
```

**Interview line:**
“React helps us build reusable, component-based UI where the view automatically updates when state changes.”

---

## 2. What are components in React?

**Answer:**
Components are reusable building blocks of a React application. A component represents a small part of the UI.

Example: Header, Footer, LoginForm, PostCard.

```jsx
function Header() {
  return <h2>My Blog App</h2>;
}
```

**Real project example:**
In your blog app, you may have components like:

```text
Navbar
Login
Signup
PostList
PostDetails
CategoryList
CommentBox
```

---

## 3. What is JSX?

**Answer:**
JSX stands for JavaScript XML. It allows us to write HTML-like code inside JavaScript.

```jsx
const message = <h1>Hello React</h1>;
```

Behind the scenes, JSX is converted into JavaScript function calls.

**Important:**
JSX is not mandatory, but it makes React code easier to read.

---

## 4. Difference between functional and class components?

**Answer:**
Earlier React used class components for state and lifecycle methods. Modern React mostly uses functional components with hooks.

**Class component:**

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello</h1>;
  }
}
```

**Functional component:**

```jsx
function Welcome() {
  return <h1>Hello</h1>;
}
```

**Interview line:**
“Today, functional components are preferred because hooks allow state, lifecycle behavior, context, memoization, and custom reusable logic.”

---

## 5. What are props in React?

**Answer:**
Props are read-only data passed from parent component to child component.

```jsx
function UserCard({ name }) {
  return <h2>{name}</h2>;
}

function App() {
  return <UserCard name="Danish" />;
}
```

**Java comparison:**
Props are like method parameters.

```java
printUser("Danish");
```

In React:

```jsx
<UserCard name="Danish" />
```

---

## 6. What is state in React?

**Answer:**
State is component-level data that can change over time. When state changes, React re-renders the component.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </>
  );
}
```

**Interview line:**
“Props are passed from outside; state is managed inside the component.”

---

## 7. Difference between props and state?

| Props                       | State                     |
| --------------------------- | ------------------------- |
| Passed from parent          | Managed inside component  |
| Read-only                   | Mutable using setter      |
| Used for communication      | Used for dynamic behavior |
| Similar to method arguments | Similar to object fields  |

Example:

```jsx
function Product({ name }) {
  const [quantity, setQuantity] = useState(1);

  return <p>{name} - {quantity}</p>;
}
```

Here, `name` is prop and `quantity` is state.

---

## 8. What is one-way data binding?

**Answer:**
React follows one-way data flow. Data flows from parent to child through props.

```jsx
function Parent() {
  const user = "Danish";
  return <Child name={user} />;
}

function Child({ name }) {
  return <h2>{name}</h2>;
}
```

**Benefit:**
It makes debugging easier because data flow is predictable.

---

## 9. What is virtual DOM?

**Answer:**
Virtual DOM is a lightweight JavaScript representation of the real DOM. When state changes, React creates a new virtual DOM, compares it with the previous one, and updates only the required real DOM parts.

**Interview line:**
“React does not directly update the whole browser DOM every time. It calculates the minimum required changes and applies them efficiently.”

---

## 10. What is reconciliation in React?

**Answer:**
Reconciliation is the process where React compares the previous virtual DOM with the new virtual DOM and decides what needs to change in the real DOM.

Example:

```jsx
setCount(count + 1);
```

Only the count text may update, not the entire page.

---

## 11. Why do we need keys in lists?

**Answer:**
Keys help React identify which items changed, added, or removed.

```jsx
const users = [
  { id: 1, name: "Danish" },
  { id: 2, name: "Rahul" }
];

users.map(user => <li key={user.id}>{user.name}</li>);
```

**Best practice:**
Use unique ID from database, not array index.

**Why not index?**
Index can cause UI bugs when list items are reordered, inserted, or deleted.

---

## 12. What is conditional rendering?

**Answer:**
Conditional rendering means showing UI based on a condition.

```jsx
function Dashboard({ isLoggedIn }) {
  return isLoggedIn ? <h1>Welcome</h1> : <h1>Please login</h1>;
}
```

Another example:

```jsx
{isAdmin && <button>Delete Post</button>}
```

---

## 13. What are fragments in React?

**Answer:**
Fragments allow returning multiple elements without adding an extra DOM node.

```jsx
function UserInfo() {
  return (
    <>
      <h2>Danish</h2>
      <p>Java Developer</p>
    </>
  );
}
```

Instead of unnecessary wrapper:

```jsx
<div>
  <h2>Danish</h2>
  <p>Java Developer</p>
</div>
```

---

## 14. What is event handling in React?

**Answer:**
React handles events using camelCase syntax.

```jsx
function Button() {
  function handleClick() {
    alert("Button clicked");
  }

  return <button onClick={handleClick}>Click</button>;
}
```

HTML:

```html
<button onclick="handleClick()">Click</button>
```

React:

```jsx
<button onClick={handleClick}>Click</button>
```

---

## 15. What is controlled component?

**Answer:**
A controlled component is a form element whose value is controlled by React state.

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");

  return (
    <input
      value={email}
      onChange={(e) => setEmail(e.target.value)}
    />
  );
}
```

**Interview line:**
“In controlled components, React state is the single source of truth.”

---

# Hooks Interview Questions

React Hooks let function components use React features like state, effects, context, refs, memoization, reducers, and transitions. Official React docs list built-in hooks and explain that hooks can also be combined into custom hooks. ([React][1])

---

## 16. What are hooks in React?

**Answer:**
Hooks are special functions that allow functional components to use React features like state, lifecycle behavior, context, refs, and memoization.

Common hooks:

```text
useState
useEffect
useContext
useRef
useMemo
useCallback
useReducer
```

Example:

```jsx
const [count, setCount] = useState(0);
```

---

## 17. What are the rules of hooks?

**Answer:**
Hooks must be called at the top level of a component or custom hook. They should not be called inside loops, conditions, or nested functions because React relies on the order of hook calls to preserve state correctly. ([React][2])

Wrong:

```jsx
if (isLoggedIn) {
  const [user, setUser] = useState(null);
}
```

Correct:

```jsx
const [user, setUser] = useState(null);

if (!isLoggedIn) {
  return <Login />;
}
```

---

## 18. Explain useState.

**Answer:**
`useState` is used to manage local component state.

```jsx
const [count, setCount] = useState(0);
```

Example:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**Important:**
State update is asynchronous-like; do not rely immediately on updated value.

Better for previous state:

```jsx
setCount(prev => prev + 1);
```

---

## 19. Explain useEffect.

**Answer:**
`useEffect` is used to synchronize a component with an external system such as API calls, browser events, timers, or external libraries. React docs describe effects as an escape hatch for interacting with systems outside React. ([React][3])

```jsx
useEffect(() => {
  console.log("Component rendered");
}, []);
```

API call example:

```jsx
useEffect(() => {
  fetch("http://localhost:8080/api/posts")
    .then(res => res.json())
    .then(data => setPosts(data));
}, []);
```

---

## 20. What is dependency array in useEffect?

**Answer:**
Dependency array controls when the effect runs.

```jsx
useEffect(() => {
  console.log("Runs after every render");
});
```

```jsx
useEffect(() => {
  console.log("Runs only once");
}, []);
```

```jsx
useEffect(() => {
  console.log("Runs when userId changes");
}, [userId]);
```

**Interview line:**
“Dependency array tells React which values the effect depends on.”

---

## 21. When should we avoid useEffect?

**Answer:**
Do not use `useEffect` for simple derived values. React docs clearly say if there is no external system involved, you probably do not need an effect. ([React][4])

Wrong:

```jsx
const [fullName, setFullName] = useState("");

useEffect(() => {
  setFullName(firstName + " " + lastName);
}, [firstName, lastName]);
```

Correct:

```jsx
const fullName = firstName + " " + lastName;
```

**Interview line:**
“Effects are for synchronization, not for every calculation.”

---

## 22. What is cleanup function in useEffect?

**Answer:**
Cleanup function is used to clean resources like timers, event listeners, or subscriptions.

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

**Use cases:**

```text
clear timer
remove event listener
cancel subscription
abort API request
```

---

## 23. Explain useContext.

**Answer:**
`useContext` is used to consume context data without prop drilling. React finds the nearest provider above the component and returns its value. ([React][5])

```jsx
const ThemeContext = createContext();

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Navbar />
    </ThemeContext.Provider>
  );
}

function Navbar() {
  const theme = useContext(ThemeContext);
  return <h1>Theme: {theme}</h1>;
}
```

---

## 24. What is prop drilling?

**Answer:**
Prop drilling means passing data through multiple intermediate components even when those components do not need the data.

```jsx
<App user={user}>
  <Layout user={user}>
    <Navbar user={user} />
  </Layout>
</App>
```

**Solution:**

```text
Context API
Redux
Zustand
React Query for server state
```

---

## 25. Explain useRef.

**Answer:**
`useRef` stores a mutable value that does not cause re-render when changed. It is also used to access DOM elements.

```jsx
function InputFocus() {
  const inputRef = useRef();

  function focusInput() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

**Interview line:**
“useRef is useful when we need to store a value across renders without triggering re-render.”

---

## 26. Difference between useState and useRef?

| useState           | useRef                                  |
| ------------------ | --------------------------------------- |
| Triggers re-render | Does not trigger re-render              |
| Used for UI data   | Used for DOM reference or mutable value |
| Updates visible UI | Stores hidden/internal value            |

Example:

```jsx
const [count, setCount] = useState(0);
const renderCount = useRef(0);
```

---

## 27. Explain useMemo.

**Answer:**
`useMemo` caches the result of an expensive calculation between re-renders. React docs define it as a hook for caching calculated values. ([React][6])

```jsx
const filteredUsers = useMemo(() => {
  return users.filter(user => user.active);
}, [users]);
```

**Use when:**
The calculation is expensive and dependencies do not change frequently.

**Do not overuse it.**

---

## 28. Explain useCallback.

**Answer:**
`useCallback` caches a function definition between re-renders. It is useful when passing callbacks to memoized child components. ([React][7])

```jsx
const handleDelete = useCallback((id) => {
  deletePost(id);
}, []);
```

**Difference:**

```text
useMemo     -> caches value
useCallback -> caches function
```

---

## 29. Difference between useMemo and useCallback?

| useMemo                         | useCallback                                |
| ------------------------------- | ------------------------------------------ |
| Caches calculated value         | Caches function reference                  |
| Returns value                   | Returns function                           |
| Used for expensive calculations | Used to avoid unnecessary child re-renders |

Example:

```jsx
const total = useMemo(() => calculateTotal(items), [items]);

const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

---

## 30. Explain useReducer.

**Answer:**
`useReducer` is used when state logic is complex. A reducer function receives current state and action, then returns next state. React docs explain reducer as a way to consolidate state update logic. ([React][8])

```jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>
        +
      </button>
    </>
  );
}
```

**Interview line:**
“useState is good for simple state; useReducer is better when state transitions are complex.”

---

# State Management, API, Routing, Forms

## 31. What is Context API?

**Answer:**
Context API allows sharing data globally across components without passing props manually at every level.

Use cases:

```text
logged-in user
theme
language
auth token
permissions
```

Example:

```jsx
const AuthContext = createContext();

function App() {
  const user = { name: "Danish", role: "ADMIN" };

  return (
    <AuthContext.Provider value={user}>
      <Dashboard />
    </AuthContext.Provider>
  );
}
```

---

## 32. Context API vs Redux?

| Context API                 | Redux                            |
| --------------------------- | -------------------------------- |
| Built into React            | External library                 |
| Good for simple global data | Good for complex state           |
| Less boilerplate            | More structured                  |
| Auth/theme/language         | Large app state, complex actions |

**Interview line:**
“For small to medium state sharing, Context is enough. For complex application-wide state with debugging, middleware, and predictable flow, Redux can be better.”

---

## 33. What is Redux?

**Answer:**
Redux is a predictable state management library. It stores global state in a central store.

Flow:

```text
Component -> dispatch action -> reducer updates state -> UI re-renders
```

Example reducer:

```jsx
function authReducer(state = { loggedIn: false }, action) {
  switch (action.type) {
    case "LOGIN":
      return { loggedIn: true, user: action.payload };
    case "LOGOUT":
      return { loggedIn: false, user: null };
    default:
      return state;
  }
}
```

---

## 34. What is React Router?

**Answer:**
React Router is used for client-side routing in React applications.

Example:

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/posts/:id" element={<PostDetails />} />
      </Routes>
    </BrowserRouter>
  );
}
```

**Interview line:**
“It allows navigation without full page reload.”

---

## 35. What is SPA?

**Answer:**
SPA means Single Page Application. In SPA, the browser loads one main HTML page, and React updates the UI dynamically without full page reload.

Example:

```text
/login
/dashboard
/posts/10
```

All routes are handled on client side.

**Benefit:**

```text
fast navigation
smooth user experience
less server page rendering
```

---

## 36. How do you call backend API from React?

**Answer:**
React can call backend APIs using `fetch`, `axios`, or data-fetching libraries.

Example with Spring Boot API:

```jsx
useEffect(() => {
  fetch("http://localhost:8080/api/v1/posts")
    .then(response => response.json())
    .then(data => setPosts(data.content))
    .catch(error => console.error(error));
}, []);
```

With JWT:

```jsx
fetch("http://localhost:8080/api/v1/posts", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

---

## 37. How do you handle loading and error states?

**Answer:**
Professional React code should handle loading, success, and error states.

```jsx
function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    fetch("/api/posts")
      .then(res => res.json())
      .then(data => setPosts(data))
      .catch(() => setError("Failed to load posts"))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>{error}</p>;

  return posts.map(post => <h3 key={post.id}>{post.title}</h3>);
}
```

**Interview line:**
“I always handle loading, error, and empty data states to make UI stable.”

---

## 38. Controlled vs uncontrolled components?

| Controlled           | Uncontrolled            |
| -------------------- | ----------------------- |
| React controls value | DOM controls value      |
| Uses state           | Uses ref                |
| Better validation    | Less React code         |
| Common in forms      | Useful for simple cases |

Controlled:

```jsx
<input value={email} onChange={e => setEmail(e.target.value)} />
```

Uncontrolled:

```jsx
const inputRef = useRef();

<input ref={inputRef} />
```

---

## 39. How do you validate forms in React?

**Answer:**
Validation can be done manually or using libraries like Formik, React Hook Form, or Yup.

Manual example:

```jsx
function validate(email, password) {
  if (!email.includes("@")) return "Invalid email";
  if (password.length < 6) return "Password too short";
  return "";
}
```

Submit:

```jsx
function handleSubmit(e) {
  e.preventDefault();

  const error = validate(email, password);
  if (error) {
    setError(error);
    return;
  }

  login(email, password);
}
```

---

## 40. How do you handle authentication in React?

**Answer:**
Common JWT flow:

```text
1. User enters email/password.
2. React sends login request to Spring Boot.
3. Backend returns JWT token.
4. React stores token.
5. React sends token in Authorization header.
6. Backend validates token.
```

Example:

```jsx
localStorage.setItem("token", jwtToken);
```

API call:

```jsx
fetch("/api/posts", {
  headers: {
    Authorization: `Bearer ${localStorage.getItem("token")}`
  }
});
```

**Important interview point:**
For stronger security, many production apps prefer secure, HttpOnly cookies for tokens where possible.

---

# Performance and Advanced React

## 41. What is React.memo?

**Answer:**
`React.memo` prevents unnecessary re-rendering of a component when props have not changed.

```jsx
const UserCard = React.memo(function UserCard({ user }) {
  return <h2>{user.name}</h2>;
});
```

**Use case:**
Large lists, expensive child components, repeated rendering.

---

## 42. What causes re-render in React?

**Answer:**
A component re-renders when:

```text
state changes
props change
parent component re-renders
context value changes
```

Example:

```jsx
setCount(count + 1);
```

This causes the component to re-render.

---

## 43. How do you optimize React application performance?

**Answer:**

Common optimization techniques:

```text
Use React.memo for pure child components
Use useMemo for expensive calculations
Use useCallback for stable function references
Use lazy loading
Use pagination or virtualization for large lists
Avoid unnecessary state
Avoid unnecessary useEffect
Split large components
```

React also has newer optimization support through React Compiler, which aims to automatically handle memoization in supported setups. ([React][9])

---

## 44. What is lazy loading in React?

**Answer:**
Lazy loading means loading a component only when needed. React’s `lazy` returns a component that can be rendered while its code is loaded, usually inside `Suspense`. ([React][10])

```jsx
import { lazy, Suspense } from "react";

const AdminDashboard = lazy(() => import("./AdminDashboard"));

function App() {
  return (
    <Suspense fallback={<p>Loading...</p>}>
      <AdminDashboard />
    </Suspense>
  );
}
```

**Benefit:**
Reduces initial bundle size.

---

## 45. What is Suspense?

**Answer:**
`Suspense` lets React show fallback UI while some child component is loading or waiting. Official docs describe fallback as alternate UI such as a spinner or skeleton while children are not ready. ([React][11])

```jsx
<Suspense fallback={<h2>Loading...</h2>}>
  <Profile />
</Suspense>
```

Use cases:

```text
lazy-loaded components
some data-fetching patterns
server components frameworks
```

---

## 46. What is useTransition?

**Answer:**
`useTransition` allows non-urgent UI updates to be marked as transitions so urgent updates like typing remain responsive. React docs describe it as a hook for non-blocking updates with pending state. ([React][12])

Example:

```jsx
const [isPending, startTransition] = useTransition();

function handleSearch(e) {
  const value = e.target.value;
  setInput(value);

  startTransition(() => {
    setSearchQuery(value);
  });
}
```

**Interview line:**
“It improves user experience when expensive UI updates should not block immediate user input.”

---

## 47. What are error boundaries?

**Answer:**
Error boundaries catch JavaScript errors in child components and show fallback UI instead of breaking the whole app.

Class-based example:

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong</h2>;
    }

    return this.props.children;
  }
}
```

Usage:

```jsx
<ErrorBoundary>
  <PostDetails />
</ErrorBoundary>
```

---

## 48. What are custom hooks?

**Answer:**
Custom hooks allow reusable logic between components.

Example:

```jsx
function useFetchPosts() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch("/api/posts")
      .then(res => res.json())
      .then(data => setPosts(data));
  }, []);

  return posts;
}
```

Usage:

```jsx
function Posts() {
  const posts = useFetchPosts();

  return posts.map(post => <h2 key={post.id}>{post.title}</h2>);
}
```

**Interview line:**
“Custom hooks help separate business logic from UI.”

---

## 49. What are React Server Components?

**Answer:**
React Server Components are components rendered in a server environment separate from the client app or SSR server. They can run at build time or per request depending on the framework/setup. ([React][13])

**Simple understanding:**

```text
Client Component -> runs in browser
Server Component -> runs on server
```

**Benefit:**

```text
less JavaScript sent to browser
better performance
direct server-side data access in supported frameworks
```

**Interview-safe line:**
“Server Components are mostly used through frameworks like Next.js, not usually in plain React SPA projects.”

---

## 50. How would you explain your React project in an interview?

**Answer:**
You should explain React project like this:

> “I built a React frontend integrated with a Spring Boot backend. The frontend has pages like Login, Signup, Dashboard, Post List, Post Details, Category List, and Comment section. I used React components for reusable UI, React Router for navigation, hooks like useState and useEffect for state and API calls, and JWT token handling for secured backend APIs. I also handled loading, error, validation, and conditional rendering based on login status.”

Example architecture:

```text
React Frontend
   |
   | REST API with JWT
   v
Spring Boot Backend
   |
   v
MySQL Database
```

Example folder structure:

```text
src/
 ├── components/
 │    ├── Navbar.jsx
 │    ├── PostCard.jsx
 │    └── CommentBox.jsx
 ├── pages/
 │    ├── Login.jsx
 │    ├── Signup.jsx
 │    ├── Dashboard.jsx
 │    └── PostDetails.jsx
 ├── services/
 │    ├── authService.js
 │    └── postService.js
 ├── context/
 │    └── AuthContext.jsx
 └── App.jsx
```

---

# Most Important React Interview Coding Examples

## 1. Login API call with JWT

```jsx
async function login(email, password) {
  const response = await fetch("http://localhost:8080/api/v1/auth/login", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ email, password })
  });

  if (!response.ok) {
    throw new Error("Invalid credentials");
  }

  const data = await response.json();
  localStorage.setItem("token", data.token);

  return data;
}
```

---

## 2. Protected API call

```jsx
async function getPosts() {
  const token = localStorage.getItem("token");

  const response = await fetch("http://localhost:8080/api/v1/posts", {
    headers: {
      Authorization: `Bearer ${token}`
    }
  });

  return response.json();
}
```

---

## 3. Protected Route concept

```jsx
function ProtectedRoute({ children }) {
  const token = localStorage.getItem("token");

  if (!token) {
    return <Navigate to="/login" />;
  }

  return children;
}
```

Usage:

```jsx
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  }
/>
```

---

## 4. Reusable PostCard component

```jsx
function PostCard({ post }) {
  return (
    <div className="card">
      <h2>{post.title}</h2>
      <p>{post.content.substring(0, 100)}...</p>
      <button>Read More</button>
    </div>
  );
}
```

Usage:

```jsx
posts.map(post => <PostCard key={post.id} post={post} />);
```

---

# Quick Revision Table

| Topic          | Must Know                                         |
| -------------- | ------------------------------------------------- |
| Components     | Reusable UI blocks                                |
| Props          | Parent to child data                              |
| State          | Dynamic component data                            |
| JSX            | HTML-like syntax in JS                            |
| Hooks          | React features in functional components           |
| useState       | Local state                                       |
| useEffect      | External synchronization/API/timers               |
| useContext     | Avoid prop drilling                               |
| useRef         | DOM access/mutable value                          |
| useMemo        | Cache calculated value                            |
| useCallback    | Cache function reference                          |
| useReducer     | Complex state logic                               |
| React Router   | Client-side navigation                            |
| Context API    | Global data sharing                               |
| Redux          | Predictable global state                          |
| Lazy loading   | Load component only when needed                   |
| Suspense       | Fallback UI while loading                         |
| Error boundary | Catch UI errors                                   |
| JWT auth       | Token-based frontend-backend security             |
| Performance    | memo, lazy, pagination, avoid unnecessary renders |

---

# Best Interview Strategy for React

For your profile, you should prepare React from this angle:

```text
1. Component basics
2. Props and state
3. Hooks deeply
4. API integration with Spring Boot
5. JWT login flow
6. React Router
7. Form validation
8. Error/loading handling
9. Performance optimization
10. Project explanation
```

Most interviewers will not expect you to be a pure frontend expert if your main profile is Java backend, but they will expect you to confidently explain:

```text
How React talks to Spring Boot
How JWT is stored and sent
How routing works
How components are structured
How hooks work
How you handle forms, validation, loading and errors
```

That is the safest preparation path for your Java Full Stack interviews.

[1]: https://react.dev/reference/react/hooks?utm_source=chatgpt.com "Built-in React Hooks"
[2]: https://react.dev/reference/eslint-plugin-react-hooks/lints/rules-of-hooks?utm_source=chatgpt.com "rules-of-hooks"
[3]: https://react.dev/reference/react/useEffect?utm_source=chatgpt.com "useEffect"
[4]: https://react.dev/learn/you-might-not-need-an-effect?utm_source=chatgpt.com "You Might Not Need an Effect"
[5]: https://react.dev/reference/react/useContext?utm_source=chatgpt.com "useContext"
[6]: https://react.dev/reference/react/useMemo?utm_source=chatgpt.com "useMemo"
[7]: https://react.dev/reference/react/useCallback?utm_source=chatgpt.com "useCallback"
[8]: https://react.dev/learn/extracting-state-logic-into-a-reducer?utm_source=chatgpt.com "Extracting State Logic into a Reducer"
[9]: https://react.dev/learn/react-compiler?utm_source=chatgpt.com "React Compiler"
[10]: https://react.dev/reference/react/lazy?utm_source=chatgpt.com "lazy"
[11]: https://react.dev/reference/react/Suspense?utm_source=chatgpt.com "<Suspense> – React"
[12]: https://react.dev/reference/react/useTransition?utm_source=chatgpt.com "useTransition"
[13]: https://react.dev/reference/rsc/server-components?utm_source=chatgpt.com "Server Components"
