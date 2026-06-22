# React Interview Preparation Notes

React is a **JavaScript library for building user interfaces**. In React, UI is broken into small reusable pieces called **components**, and those components are combined to build complete screens/pages. React’s official docs describe this component-based model as the foundation of React UI development. ([React][1])

For your profile, think like this:

> **Spring Boot = backend APIs**
> **React = frontend UI that consumes those APIs**

Example:

```text
React UI  --->  REST API  --->  Spring Boot Service  --->  Database
```

---

# 1. What is React?

React is used to create dynamic, fast, reusable frontend applications.

Example use cases:

```text
Login page
Dashboard
Admin panel
Product listing page
Blog application frontend
Job portal frontend
Banking UI
E-commerce UI
```

React is not a full framework like Angular. It mainly handles the **view layer**, but with libraries like React Router, Redux/Zustand, Axios, Formik, etc., we can build full frontend applications.

---

# 2. Why React?

React is popular because of:

| Feature         | Meaning                                                  |
| --------------- | -------------------------------------------------------- |
| Component-based | UI is divided into reusable pieces                       |
| Declarative     | You describe what UI should look like                    |
| Virtual DOM     | Efficient UI updates                                     |
| Hooks           | Use state, effects, refs, context in function components |
| Large ecosystem | Router, Redux, testing, UI libraries                     |
| Good for SPA    | Single Page Application development                      |

---

# 3. Component in React

A component is a reusable UI block.

Example:

```jsx
function Welcome() {
  return <h1>Welcome Danish</h1>;
}

export default Welcome;
```

Use it like:

```jsx
<Welcome />
```

Interview line:

> A component is an independent, reusable piece of UI. In modern React, function components are preferred over class components. React still supports class components, but official docs recommend function components for new code. ([React][2])

---

# 4. JSX

JSX means **JavaScript XML**. It allows us to write HTML-like syntax inside JavaScript.

```jsx
const name = "Danish";

function App() {
  return <h1>Hello, {name}</h1>;
}
```

Important points:

```text
class      -> className
for        -> htmlFor
onclick    -> onClick
style      -> object format
```

Example:

```jsx
<button className="btn btn-primary" onClick={handleClick}>
  Submit
</button>
```

Interview line:

> JSX is syntactic sugar for React element creation. It makes UI code easier to read and maintain.

---

# 5. Props

Props are used to pass data from **parent component to child component**.

```jsx
function UserCard({ name, role }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{role}</p>
    </div>
  );
}

function App() {
  return <UserCard name="Danish" role="Java Developer" />;
}
```

Interview line:

> Props are read-only. A child component should not modify props directly.

---

# 6. State

State is data managed inside a component. When state changes, React re-renders the component. React’s `useState` Hook is used to add state to function components. ([React][3])

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>
        Increase
      </button>
    </>
  );
}
```

Interview line:

> Props are passed from outside. State is managed inside the component.

---

# 7. Props vs State

| Props                        | State                       |
| ---------------------------- | --------------------------- |
| Passed from parent           | Managed inside component    |
| Read-only                    | Can be changed using setter |
| Used for communication       | Used for dynamic UI         |
| Child cannot modify directly | Component can update it     |

Example:

```jsx
function Profile({ username }) {
  const [isOnline, setIsOnline] = useState(false);

  return (
    <div>
      <h2>{username}</h2>
      <p>{isOnline ? "Online" : "Offline"}</p>
    </div>
  );
}
```

Here:

```text
username = prop
isOnline = state
```

---

# 8. Event Handling

React events use camelCase.

```jsx
function LoginButton() {
  function handleLogin() {
    alert("Login clicked");
  }

  return <button onClick={handleLogin}>Login</button>;
}
```

With parameter:

```jsx
<button onClick={() => deleteUser(user.id)}>
  Delete
</button>
```

Interview line:

> In React, we pass function references to event handlers. We should avoid directly calling the function during render.

Wrong:

```jsx
<button onClick={deleteUser(user.id)}>Delete</button>
```

Correct:

```jsx
<button onClick={() => deleteUser(user.id)}>Delete</button>
```

---

# 9. Conditional Rendering

Conditional rendering means showing UI based on condition. React supports normal JavaScript conditions like `if`, `&&`, and ternary operators. ([React][4])

```jsx
function Dashboard({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? <h1>Welcome</h1> : <h1>Please login</h1>}
    </div>
  );
}
```

Using `&&`:

```jsx
{isAdmin && <button>Delete User</button>}
```

---

# 10. Rendering Lists

React commonly uses JavaScript array methods like `map()` to render lists. Official docs also emphasize using array methods such as `map()` and `filter()` when rendering collections. ([React][5])

```jsx
const users = ["Danish", "Rahul", "Amit"];

function UserList() {
  return (
    <ul>
      {users.map((user) => (
        <li key={user}>{user}</li>
      ))}
    </ul>
  );
}
```

Important interview point:

> Every list item should have a unique `key`. Key helps React identify which item changed, added, or removed.

Avoid index as key when list can change:

```jsx
// Avoid this if list is dynamic
<li key={index}>{user.name}</li>
```

Prefer unique ID:

```jsx
<li key={user.id}>{user.name}</li>
```

---

# 11. Forms in React

There are two common types:

```text
1. Controlled components
2. Uncontrolled components
```

## Controlled Component

React state controls the input value.

```jsx
import { useState } from "react";

function LoginForm() {
  const [email, setEmail] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    console.log(email);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />

      <button type="submit">Login</button>
    </form>
  );
}
```

Interview line:

> In a controlled component, form data is controlled by React state.

---

# 12. Hooks

Hooks allow function components to use React features like state, lifecycle behavior, context, refs, memoization, etc. React provides built-in Hooks and also allows custom Hooks. ([React][6])

Common Hooks:

| Hook          | Purpose                           |
| ------------- | --------------------------------- |
| `useState`    | Manage state                      |
| `useEffect`   | Side effects/API calls            |
| `useContext`  | Access global/context data        |
| `useRef`      | Store mutable value/reference DOM |
| `useMemo`     | Cache computed value              |
| `useCallback` | Cache function reference          |
| `useReducer`  | Complex state management          |

---

# 13. Rules of Hooks

Important interview topic.

Rules:

```text
1. Call Hooks only at top level.
2. Do not call Hooks inside loops, conditions, or nested functions.
3. Call Hooks only from React function components or custom Hooks.
```

React official docs clearly state that Hooks should not be called inside loops, conditions, nested functions, or `try/catch/finally` blocks. ([React][7])

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
  return <h1>Please login</h1>;
}
```

---

# 14. useEffect

`useEffect` is used for side effects such as:

```text
API calls
Subscriptions
Timers
DOM updates
Local storage
```

React defines `useEffect` as a Hook that synchronizes a component with an external system. ([React][8])

Example API call:

```jsx
import { useEffect, useState } from "react";

function Posts() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch("http://localhost:8080/api/posts")
      .then((response) => response.json())
      .then((data) => setPosts(data));
  }, []);

  return (
    <div>
      {posts.map((post) => (
        <h3 key={post.id}>{post.title}</h3>
      ))}
    </div>
  );
}
```

Dependency array:

| Dependency          | Meaning                      |
| ------------------- | ---------------------------- |
| No dependency array | Runs after every render      |
| `[]`                | Runs once after first render |
| `[id]`              | Runs when `id` changes       |

Example:

```jsx
useEffect(() => {
  console.log("User ID changed");
}, [userId]);
```

Cleanup example:

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running...");
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

Interview line:

> `useEffect` is not for every calculation. It is mainly for synchronizing with external systems like APIs, timers, browser APIs, or subscriptions.

---

# 15. React with Spring Boot Example

## Spring Boot Backend

```java
@RestController
@RequestMapping("/api/users")
@CrossOrigin(origins = "http://localhost:3000")
public class UserController {

    @GetMapping
    public List<UserDto> getUsers() {
        return List.of(
            new UserDto(1L, "Danish", "Java Developer"),
            new UserDto(2L, "Rahul", "React Developer")
        );
    }
}
```

DTO:

```java
public class UserDto {
    private Long id;
    private String name;
    private String role;

    public UserDto(Long id, String name, String role) {
        this.id = id;
        this.name = name;
        this.role = role;
    }

    public Long getId() { return id; }
    public String getName() { return name; }
    public String getRole() { return role; }
}
```

## React Frontend

```jsx
import { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("http://localhost:8080/api/users")
      .then((res) => res.json())
      .then((data) => setUsers(data))
      .catch((error) => console.error("Error:", error));
  }, []);

  return (
    <div>
      <h2>User List</h2>

      {users.map((user) => (
        <div key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.role}</p>
        </div>
      ))}
    </div>
  );
}

export default Users;
```

Interview explanation:

> React calls the Spring Boot REST API using `fetch` or `axios`, receives JSON data, stores it in state, and renders it on the UI.

---

# 16. useContext

`useContext` is used to read and subscribe to context from a component. ([React][9])

Use case:

```text
Logged-in user
Theme
Language
Auth token
Global configuration
```

Example:

```jsx
import { createContext, useContext } from "react";

const AuthContext = createContext();

function App() {
  const user = { name: "Danish", role: "ADMIN" };

  return (
    <AuthContext.Provider value={user}>
      <Dashboard />
    </AuthContext.Provider>
  );
}

function Dashboard() {
  const user = useContext(AuthContext);

  return <h1>Welcome {user.name}</h1>;
}
```

Interview line:

> Context avoids prop drilling, but it should not be overused for every state. For frequent updates, dedicated state management may be better.

---

# 17. useRef

`useRef` stores a mutable value that does not trigger re-render when changed. It can also be used to reference DOM elements. ([React][10])

Example:

```jsx
import { useRef } from "react";

function FocusInput() {
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

Interview line:

> `useRef` is useful when we need to store a value across renders but do not want to trigger a re-render.

---

# 18. useMemo

`useMemo` caches the result of an expensive calculation between re-renders. ([React][11])

```jsx
import { useMemo, useState } from "react";

function ProductList({ products }) {
  const [search, setSearch] = useState("");

  const filteredProducts = useMemo(() => {
    return products.filter((p) =>
      p.name.toLowerCase().includes(search.toLowerCase())
    );
  }, [products, search]);

  return (
    <>
      <input value={search} onChange={(e) => setSearch(e.target.value)} />

      {filteredProducts.map((p) => (
        <p key={p.id}>{p.name}</p>
      ))}
    </>
  );
}
```

Interview line:

> Use `useMemo` only when calculation is expensive or unnecessary recalculation is causing performance issues.

---

# 19. useCallback

`useCallback` caches a function definition between re-renders. ([React][12])

```jsx
const handleDelete = useCallback((id) => {
  deleteUser(id);
}, []);
```

Interview line:

> `useCallback` is useful when passing functions to memoized child components, so the child does not re-render unnecessarily because of a new function reference.

---

# 20. React.memo

`React.memo` prevents unnecessary re-rendering of a component when props have not changed.

```jsx
const UserCard = React.memo(function UserCard({ user }) {
  console.log("UserCard rendered");

  return <h2>{user.name}</h2>;
});
```

Interview line:

> `React.memo` is used for performance optimization, but it should not be applied everywhere blindly.

React’s latest docs also mention that React Compiler can automatically apply memoization behavior, reducing the need for manual memoization in supported setups. ([React][13])

---

# 21. Virtual DOM

Virtual DOM is a lightweight JavaScript representation of the actual DOM.

Flow:

```text
State/Props change
        ↓
React creates new Virtual DOM
        ↓
React compares old vs new Virtual DOM
        ↓
React updates only required real DOM parts
```

Interview line:

> Virtual DOM improves UI update efficiency by minimizing direct DOM manipulation.

---

# 22. Reconciliation

Reconciliation is the process React uses to compare the old UI tree with the new UI tree and update only the necessary parts.

Example:

```jsx
<h1>Hello Danish</h1>
```

Changed to:

```jsx
<h1>Hello Khan</h1>
```

React does not recreate the full page. It updates the changed text.

Interview line:

> Reconciliation is React’s diffing process used to update the UI efficiently.

---

# 23. Controlled vs Uncontrolled Components

| Controlled                   | Uncontrolled            |
| ---------------------------- | ----------------------- |
| Value handled by React state | Value handled by DOM    |
| Uses `value` and `onChange`  | Uses `ref`              |
| Better validation/control    | Simpler for small forms |

Controlled:

```jsx
<input value={email} onChange={(e) => setEmail(e.target.value)} />
```

Uncontrolled:

```jsx
const inputRef = useRef();

<input ref={inputRef} />
```

---

# 24. React Router

React Router is used for navigation in SPA applications.

Example:

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/users">Users</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users" element={<Users />} />
      </Routes>
    </BrowserRouter>
  );
}
```

Interview line:

> React Router enables client-side routing without full page reload.

---

# 25. SPA — Single Page Application

SPA means the application loads one HTML page, and React dynamically updates content without reloading the full page.

Example:

```text
/dashboard
/users
/profile
/settings
```

In SPA, navigation feels faster because the browser does not reload the entire page for each route.

---

# 26. Lifting State Up

When two child components need the same data, move the state to their common parent.

```jsx
function Parent() {
  const [username, setUsername] = useState("");

  return (
    <>
      <InputBox username={username} setUsername={setUsername} />
      <Preview username={username} />
    </>
  );
}
```

Interview line:

> Lifting state up means moving shared state to the nearest common parent component.

---

# 27. Prop Drilling

Prop drilling means passing props through multiple levels of components.

```text
App -> Layout -> Dashboard -> Profile -> Avatar
```

Problem:

```jsx
<Avatar user={user} />
```

The middle components may not need `user`, but still pass it.

Solution:

```text
Context API
Redux/Zustand
Component composition
```

---

# 28. Error Boundary

Error boundaries catch rendering errors in child components.

Important:

```text
Class components are traditionally used for error boundaries.
They catch render-time errors.
They do not catch async errors inside promises directly.
```

Example:

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.error(error, info);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong</h1>;
    }

    return this.props.children;
  }
}
```

---

# 29. StrictMode

`StrictMode` helps detect problems during development. React docs mention that Strict Mode can re-render components and re-run effects extra times in development to find bugs caused by impure rendering or missing cleanup. ([React][14])

```jsx
<React.StrictMode>
  <App />
</React.StrictMode>
```

Interview line:

> StrictMode does not affect production behavior. It helps catch potential issues during development.

---

# 30. React Performance Optimization

Important techniques:

```text
Use key properly
Avoid unnecessary state
Use React.memo carefully
Use useMemo for expensive calculations
Use useCallback for stable function references
Code splitting
Lazy loading
Pagination
Debouncing
Virtualization for large lists
Avoid inline heavy calculations in render
```

Example lazy loading:

```jsx
import { lazy, Suspense } from "react";

const AdminDashboard = lazy(() => import("./AdminDashboard"));

function App() {
  return (
    <Suspense fallback={<h2>Loading...</h2>}>
      <AdminDashboard />
    </Suspense>
  );
}
```

---

# 31. React Project Structure

Professional structure:

```text
src/
 ├── api/
 │    └── userApi.js
 ├── components/
 │    └── UserCard.jsx
 ├── pages/
 │    └── UserList.jsx
 ├── hooks/
 │    └── useAuth.js
 ├── context/
 │    └── AuthContext.jsx
 ├── services/
 │    └── authService.js
 ├── utils/
 │    └── constants.js
 ├── App.jsx
 └── main.jsx
```

For your Spring Boot + React project, this structure is enough for interview-level discussion.

---

# 32. Axios Example

```bash
npm install axios
```

```jsx
import axios from "axios";

const API_URL = "http://localhost:8080/api/users";

export async function getUsers() {
  const response = await axios.get(API_URL);
  return response.data;
}
```

Use in component:

```jsx
useEffect(() => {
  getUsers()
    .then((data) => setUsers(data))
    .catch((error) => console.error(error));
}, []);
```

---

# 33. JWT Authentication in React

Common flow:

```text
User enters email/password
React calls Spring Boot login API
Backend validates user
Backend returns JWT
React stores JWT
React sends JWT in Authorization header
Protected APIs return data
```

Example:

```jsx
async function login(email, password) {
  const response = await axios.post("http://localhost:8080/api/auth/login", {
    email,
    password,
  });

  localStorage.setItem("token", response.data.token);
}
```

Send token:

```jsx
axios.get("http://localhost:8080/api/posts", {
  headers: {
    Authorization: `Bearer ${localStorage.getItem("token")}`,
  },
});
```

Interview caution:

> Storing JWT in `localStorage` is common in small projects, but it has XSS risk. In production, HTTP-only secure cookies are often preferred depending on architecture.

---

# 34. React Interview Questions and Answers

## 1. What is React?

React is a JavaScript library for building reusable, component-based user interfaces.

## 2. Is React a framework?

No. React is mainly a UI library. A complete app usually uses additional libraries for routing, API calls, state management, and testing.

## 3. What is a component?

A component is a reusable UI block. Example: `Navbar`, `LoginForm`, `UserCard`.

## 4. What are function components?

Function components are JavaScript functions that return JSX.

```jsx
function Header() {
  return <h1>Header</h1>;
}
```

## 5. What are class components?

Class components are older React components created using ES6 classes. They are still supported, but function components are preferred for new code. ([React][2])

## 6. What is JSX?

JSX is HTML-like syntax used inside JavaScript to describe UI.

## 7. What are props?

Props are inputs passed from parent to child component.

## 8. Can we modify props?

No. Props are read-only.

## 9. What is state?

State is internal data of a component. When state changes, React re-renders the component.

## 10. Difference between props and state?

Props come from parent. State belongs to the component.

## 11. What is `useState`?

`useState` is a Hook used to add state to a function component. ([React][3])

## 12. What is `useEffect`?

`useEffect` is used to synchronize a component with an external system, such as API calls, subscriptions, or timers. ([React][8])

## 13. What is dependency array in `useEffect`?

It controls when the effect runs.

```jsx
useEffect(() => {}, []);        // once
useEffect(() => {}, [userId]);  // when userId changes
```

## 14. What happens if dependency array is missing?

The effect runs after every render.

## 15. What is cleanup in `useEffect`?

Cleanup is used to remove timers, subscriptions, or listeners.

```jsx
return () => clearInterval(timer);
```

## 16. What are Hooks?

Hooks are functions that let function components use React features like state, effects, refs, context, and memoization. ([React][6])

## 17. What are Rules of Hooks?

Hooks should be called only at the top level and not inside conditions, loops, or nested functions. ([React][7])

## 18. What is Virtual DOM?

Virtual DOM is a lightweight copy of the real DOM used by React to update UI efficiently.

## 19. What is reconciliation?

Reconciliation is React’s process of comparing old and new UI trees and updating only changed parts.

## 20. What is key in React list?

Key uniquely identifies list elements and helps React update lists efficiently.

## 21. Can we use index as key?

Yes, but avoid it for dynamic lists where items can be added, removed, or reordered.

## 22. What is conditional rendering?

Rendering different UI based on conditions.

```jsx
{isLoggedIn ? <Dashboard /> : <Login />}
```

## 23. What is controlled component?

A form element controlled by React state.

## 24. What is uncontrolled component?

A form element where value is managed by DOM, usually accessed using `ref`.

## 25. What is `useRef`?

`useRef` stores mutable values that do not cause re-render and can also reference DOM elements. ([React][10])

## 26. What is `useContext`?

`useContext` reads context value in a component without prop drilling. ([React][9])

## 27. What is prop drilling?

Passing props through many component levels unnecessarily.

## 28. How to avoid prop drilling?

Use Context API, Redux, Zustand, or better component composition.

## 29. What is `useMemo`?

`useMemo` caches calculated values between re-renders. ([React][11])

## 30. What is `useCallback`?

`useCallback` caches function definitions between re-renders. ([React][12])

## 31. Difference between `useMemo` and `useCallback`?

```text
useMemo     -> caches value
useCallback -> caches function
```

## 32. What is React.memo?

It memoizes a component and avoids re-render if props are unchanged.

## 33. What is SPA?

Single Page Application loads one HTML page and updates views dynamically without full page reload.

## 34. What is React Router?

React Router handles client-side navigation in React apps.

## 35. What is lifting state up?

Moving shared state to the nearest common parent component.

## 36. What is fragment?

Fragment lets us return multiple elements without adding extra DOM node.

```jsx
<>
  <h1>Hello</h1>
  <p>Welcome</p>
</>
```

## 37. Why can’t component return multiple parent elements?

JSX expression must return a single parent wrapper. Fragment solves this.

## 38. What is `children` prop?

`children` allows a component to receive nested content.

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}
```

## 39. What is error boundary?

A component that catches rendering errors in child components and shows fallback UI.

## 40. What is StrictMode?

StrictMode helps detect development-time problems like impure rendering, missing cleanup, and deprecated API usage. ([React][14])

## 41. How does React communicate with backend?

Using HTTP clients like `fetch` or `axios`.

```jsx
axios.get("/api/users");
```

## 42. How do you send JWT token from React?

By adding it in the `Authorization` header.

```jsx
Authorization: `Bearer ${token}`
```

## 43. What is CORS issue?

CORS issue happens when frontend and backend run on different origins and backend does not allow frontend origin.

Example:

```text
React: http://localhost:3000
Spring Boot: http://localhost:8080
```

Spring Boot fix:

```java
@CrossOrigin(origins = "http://localhost:3000")
```

## 44. What is lazy loading?

Loading a component only when it is required.

## 45. What is code splitting?

Breaking JavaScript bundle into smaller chunks to improve initial load time.

## 46. What is debouncing?

Delaying function execution until the user stops typing/clicking for a short time.

Use case:

```text
Search box API call
```

## 47. What is difference between React and Angular?

| React                 | Angular                     |
| --------------------- | --------------------------- |
| Library               | Framework                   |
| JSX                   | HTML templates              |
| Flexible              | Opinionated                 |
| Needs extra libraries | Built-in routing/forms/http |

## 48. What is difference between React and Vue?

React uses JSX heavily. Vue uses template-based syntax. Both are component-based frontend technologies.

## 49. What are common React performance issues?

```text
Unnecessary re-renders
Wrong keys
Large list rendering
Too much state at top level
Expensive calculations inside render
Unoptimized images/assets
```

## 50. How do you make a React app production-ready?

```text
Use proper folder structure
Handle errors
Use environment variables
Validate forms
Secure token handling
Use route protection
Optimize bundle
Write tests
Use linting
Handle loading/error states
Connect with backend cleanly
```

---

# 35. Very Important Interview Differences

## React vs Java Backend Thinking

| Backend Java/Spring Boot | React                      |
| ------------------------ | -------------------------- |
| Controller               | Component                  |
| DTO                      | Props/data object          |
| Service state/database   | Component state            |
| REST API                 | API call using fetch/axios |
| Spring Security JWT      | React stores/sends JWT     |
| Exception handling       | Error/loading UI           |
| Pagination API           | Pagination component       |

---

# 36. Real Interview Explanation: React Login Flow

You can say:

> In my project, React handles login form input using controlled components. On submit, it calls the Spring Boot authentication API using Axios. The backend validates credentials and returns a JWT token. React stores the token and sends it in the Authorization header for protected APIs. Based on the user role, React conditionally renders dashboard or admin pages.

Code:

```jsx
async function handleLogin(e) {
  e.preventDefault();

  const response = await axios.post("http://localhost:8080/api/auth/login", {
    email,
    password,
  });

  localStorage.setItem("token", response.data.token);
  navigate("/dashboard");
}
```

---

# 37. React Revision Roadmap for Interview

Prepare in this order:

```text
1. JSX
2. Components
3. Props
4. State
5. Events
6. Conditional rendering
7. Lists and keys
8. Forms
9. useState
10. useEffect
11. API integration
12. React Router
13. Context API
14. useRef
15. useMemo/useCallback
16. React.memo
17. JWT integration with Spring Boot
18. Performance optimization
19. Error handling
20. Project explanation
```

---

# 38. Best Project Explanation for Your Resume

For your Spring Boot + React blog application, you can explain like this:

> I developed a React frontend integrated with a Spring Boot REST API. The frontend contains login, signup, post listing, category-based filtering, and protected routes. React components consume backend APIs using Axios/fetch. Authentication is handled using JWT, where the token is sent in the Authorization header for protected APIs. I used reusable components, controlled forms, conditional rendering, and proper state management for UI behavior.

---

# 39. Final Interview Summary

For React interviews, focus strongly on:

```text
Component
JSX
Props
State
Hooks
useEffect
Forms
List rendering
Keys
API calls
React Router
Context API
JWT flow
Performance optimization
Project explanation
```

One strong line to remember:

> React is a component-based UI library where data flows through props and state. Components re-render when state or props change, and Hooks allow function components to manage state, side effects, context, refs, and performance optimizations.

[1]: https://react.dev/?utm_source=chatgpt.com "React"
[2]: https://react.dev/reference/react/Component?utm_source=chatgpt.com "Component"
[3]: https://react.dev/reference/react/useState?utm_source=chatgpt.com "useState"
[4]: https://react.dev/learn/conditional-rendering?utm_source=chatgpt.com "Conditional Rendering"
[5]: https://react.dev/learn/rendering-lists?utm_source=chatgpt.com "Rendering Lists"
[6]: https://react.dev/reference/react/hooks?utm_source=chatgpt.com "Built-in React Hooks"
[7]: https://react.dev/reference/rules/rules-of-hooks?utm_source=chatgpt.com "Rules of Hooks"
[8]: https://react.dev/reference/react/useEffect?utm_source=chatgpt.com "useEffect"
[9]: https://react.dev/reference/react/useContext?utm_source=chatgpt.com "useContext"
[10]: https://react.dev/reference/react/useRef?utm_source=chatgpt.com "useRef"
[11]: https://react.dev/reference/react/useMemo?utm_source=chatgpt.com "useMemo"
[12]: https://react.dev/reference/react/useCallback?utm_source=chatgpt.com "useCallback"
[13]: https://react.dev/reference/react/memo?utm_source=chatgpt.com "memo"
[14]: https://react.dev/reference/react/StrictMode?utm_source=chatgpt.com "<StrictMode> – React"
