Below is a **React learning + interview preparation guide** designed specifically for your **7-year Java Backend / Full Stack Java profile**.

React should not be learned like a pure frontend fresher. For you, React should be learned as:

> **“Frontend layer that consumes my Spring Boot / Microservices REST APIs, handles JWT authentication, displays data, manages UI state, and gives users a clean production-ready application experience.”**

React’s official docs define the core daily concepts as components, markup, data display, conditional rendering, lists, events, state updates, and sharing data between components. That is exactly the foundation you should master first. ([React][1])

---

# 1. Complete Learning Roadmap

## A. Beginner Level

Start here first.

### Learn these first

1. **HTML / CSS / JavaScript basics**
   You do not need deep frontend mastery initially, but you must know:

   * `let`, `const`
   * arrow functions
   * arrays: `map`, `filter`, `find`, `reduce`
   * objects
   * destructuring
   * spread operator
   * promises
   * async/await
   * modules: `import/export`

2. **React basics**

   * What is React?
   * Component
   * JSX
   * Props
   * State
   * Event handling
   * Conditional rendering
   * List rendering
   * Forms

3. **Create React app using Vite**
   Vite is now a common modern build tool for React projects. Official Vite docs describe it as a build tool with a dev server and fast HMR for modern web development. ([vitejs][2])

```bash
npm create vite@latest blog-ui
cd blog-ui
npm install
npm run dev
```

### Skip initially

Skip these in the beginning:

* Redux
* Advanced performance optimization
* Server-side rendering
* Next.js
* GraphQL
* Webpack customization
* Advanced animation libraries

---

## B. Intermediate Level

After basics, learn how React works with backend APIs.

### Learn these

1. **React Router**

   * Page navigation
   * Public routes
   * Protected routes
   * Dynamic routes like `/posts/:postId`

2. **Axios / Fetch**

   * Calling Spring Boot REST APIs
   * Handling success/error
   * Sending JWT token in header
   * Handling 401/403 errors

3. **Forms**

   * Login form
   * Signup form
   * Add post form
   * Validation

4. **State management**

   * Local state using `useState`
   * Side effects using `useEffect`
   * Shared state using Context API
   * When Redux is required

5. **Component design**

   * Reusable components
   * Layout components
   * Service layer
   * API utility files

---

## C. Experienced Level

For 7-year experience, interviewers expect more than basic UI.

### Learn these

1. **React lifecycle using Hooks**

   * Component mount
   * Re-render
   * Cleanup
   * Dependency array

2. **Performance**

   * Avoid unnecessary re-rendering
   * `React.memo`
   * `useMemo`
   * `useCallback`
   * Lazy loading
   * Code splitting

3. **Security**

   * JWT handling
   * XSS prevention
   * Avoid storing sensitive data carelessly
   * Role-based UI rendering

4. **Testing**

   * Component testing
   * Mock API calls
   * User interaction testing

React Testing Library encourages tests that resemble how users interact with the application, which is useful for interview-ready UI testing explanation. ([Testing Library][3])

5. **Production readiness**

   * Environment variables
   * Build optimization
   * Docker deployment
   * Nginx hosting
   * CI/CD with Jenkins
   * AWS deployment

---

## D. Project Level

For your Spring Boot Blog Application, build:

1. Login / Signup
2. JWT-based authentication
3. View all posts
4. View post details
5. Add comment
6. Add post
7. Category-wise posts
8. Pagination and sorting
9. Admin-only actions
10. Profile page
11. Error handling
12. Loading spinner
13. Protected routes
14. Dockerized frontend
15. Deploy React + Spring Boot on AWS

---

## E. Interview Preparation Level

Prepare answers around:

* Why React?
* How React consumes Spring Boot APIs?
* How JWT works in React?
* How protected routes work?
* How React handles state?
* How React improves user experience?
* How React app is deployed?
* How frontend talks to microservices?
* How you handled errors and performance?

---

# 2. Why React Is Important

## For Java Backend Developer

React helps you understand the **consumer side of your REST APIs**.

As a backend developer, you create endpoints like:

```http
GET /api/posts
POST /api/auth/login
POST /api/posts
GET /api/categories
```

React helps you understand:

* How frontend consumes those APIs
* What kind of response structure frontend needs
* Why DTO design matters
* Why pagination response should be clean
* Why error messages should be standardized
* Why CORS and JWT headers matter

---

## For Senior Java Developer

At senior level, you are expected to understand **end-to-end flow**:

```text
User clicks Login
       ↓
React sends username/password
       ↓
Spring Boot validates credentials
       ↓
JWT token is returned
       ↓
React stores token
       ↓
React sends token in Authorization header
       ↓
Backend validates token
       ↓
Protected data is returned
```

This makes you stronger in system design and project explanation.

---

## For Microservices Developer

In microservices, React usually talks to:

```text
React UI
   ↓
API Gateway
   ↓
user-service / post-service / category-service
   ↓
Database
```

React does not usually call every microservice directly in production. It generally calls an **API Gateway** or **Backend for Frontend** layer.

---

## For Java Full Stack Developer

React is one of the most common skills expected for Java Full Stack roles.

For your profile, React gives you the ability to say:

> “I have worked on backend APIs using Spring Boot and also integrated those APIs with React frontend using JWT authentication, routing, forms, validations, and reusable components.”

That sounds much stronger than saying only “I created REST APIs.”

---

## For Cloud / DevOps-Based Backend Roles

React connects with deployment and DevOps because frontend also needs:

* Docker image
* Nginx static hosting
* Environment-specific backend URL
* Jenkins pipeline
* AWS deployment
* Kubernetes service/ingress
* Monitoring and logging through browser + backend logs

---

# 3. Basic Concepts From Scratch

## What is React?

React is a JavaScript library used to build user interfaces from reusable components. React’s official docs describe this model as building UI from individual pieces called components and combining them into complete screens or apps. ([React][4])

Simple meaning:

> React helps you build frontend screens using small reusable blocks.

Example:

```text
Blog Application UI
   ├── Header
   ├── Sidebar
   ├── PostList
   ├── PostCard
   ├── CommentBox
   └── Footer
```

Each block can be a React component.

---

## Component

A component is a reusable UI block.

```jsx
function PostCard() {
  return (
    <div>
      <h2>Spring Boot Post</h2>
      <p>This is a blog post.</p>
    </div>
  );
}

export default PostCard;
```

Interview line:

> “In React, we divide UI into reusable components. For example, in my blog application, PostCard, Header, LoginForm, CategoryList, and CommentBox can be separate reusable components.”

---

## JSX

JSX allows us to write HTML-like code inside JavaScript.

```jsx
const title = "Spring Boot Blog";

function Header() {
  return <h1>{title}</h1>;
}
```

JSX is not pure HTML. It gets converted into JavaScript internally.

Important differences:

```jsx
className instead of class
htmlFor instead of for
onClick instead of onclick
```

---

## Props

Props are used to pass data from parent component to child component.

```jsx
function PostCard({ title, content }) {
  return (
    <div>
      <h2>{title}</h2>
      <p>{content}</p>
    </div>
  );
}

function PostList() {
  return (
    <PostCard 
      title="JWT in Spring Boot" 
      content="JWT is used for stateless authentication." 
    />
  );
}
```

Interview line:

> “Props are like method parameters in Java. Parent component passes data to child component using props.”

---

## State

State is component-level data that can change.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

Backend analogy:

```text
Java variable changes → business logic output changes
React state changes → UI re-renders
```

---

## Event Handling

```jsx
function LoginButton() {
  const handleLogin = () => {
    console.log("Login clicked");
  };

  return <button onClick={handleLogin}>Login</button>;
}
```

Real project example:

```jsx
<button onClick={handleSubmit}>Submit Post</button>
```

---

## Conditional Rendering

```jsx
function Navbar({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? <button>Logout</button> : <button>Login</button>}
    </div>
  );
}
```

Real use:

```text
If user is logged in → show Dashboard
If not logged in → show Login page
```

---

## List Rendering

```jsx
function PostList({ posts }) {
  return (
    <div>
      {posts.map(post => (
        <PostCard key={post.id} title={post.title} content={post.content} />
      ))}
    </div>
  );
}
```

Important:

> Always use a stable `key` while rendering lists.

---

# 4. Core Concepts Required for Interviews

## 1. Components

### What it is

Reusable building block of UI.

### Why used

To split complex UI into smaller maintainable parts.

### Internal working

React renders components and updates the UI when data/state changes.

### Real project

```text
PostCard, LoginForm, SignupForm, CategoryList, Navbar
```

### Interview answer

> “I designed React UI using reusable components. For example, PostCard was reused in all-posts page, category-posts page, and user-posts page.”

---

## 2. Props

### What it is

Data passed from parent to child.

### Why used

To make components reusable.

### Internal working

Props are read-only. Child should not modify props directly.

### Real project

Parent passes post data to PostCard.

```jsx
<PostCard post={post} />
```

### Interview answer

> “Props are immutable inputs to a component. We used props to pass post details, user details, and category data from parent components to child components.”

---

## 3. State

### What it is

Data managed inside component.

### Why used

For dynamic UI.

### Internal working

When state changes, React schedules re-render.

### Real project

Login form:

```jsx
const [username, setUsername] = useState("");
const [password, setPassword] = useState("");
```

### Interview answer

> “State is used to manage dynamic UI data like form input, selected category, logged-in user, loading flag, and error messages.”

---

## 4. Hooks

Hooks let function components use React features such as state and lifecycle behavior. React’s official reference lists built-in Hooks like `useState`, `useEffect`, `useContext`, `useMemo`, and others. ([React][5])

Common hooks:

```text
useState      → component state
useEffect     → side effects / API calls
useContext    → shared global data
useMemo       → memoized calculated value
useCallback   → memoized function
useRef        → DOM reference / mutable value
```

Interview answer:

> “In modern React, we mostly use functional components with Hooks. I used `useState` for form data, `useEffect` for API calls, `useContext` for authentication state, and `useMemo/useCallback` where performance optimization was required.”

---

## 5. useEffect

### What it is

Hook used for side effects.

Side effects include:

* API call
* subscription
* timer
* manual DOM operation

Example:

```jsx
import { useEffect, useState } from "react";
import axios from "axios";

function Posts() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios.get("http://localhost:8080/api/posts")
      .then(response => setPosts(response.data.content))
      .catch(error => console.error(error));
  }, []);

  return (
    <div>
      {posts.map(post => <h3 key={post.postId}>{post.title}</h3>)}
    </div>
  );
}
```

Dependency array:

```jsx
useEffect(() => {
  // runs after every render
});

useEffect(() => {
  // runs once after initial render
}, []);

useEffect(() => {
  // runs when categoryId changes
}, [categoryId]);
```

Interview answer:

> “I used `useEffect` to call backend APIs when component loads or when filter parameters change, like categoryId, pageNumber, or search text.”

---

## 6. Context API

Context allows parent components to make information available to deeply nested components without manually passing props at every level. React docs describe this as useful when many components need the same information. ([React][6])

Real use:

```text
AuthContext
   ├── logged-in user
   ├── JWT token
   ├── login()
   └── logout()
```

Example:

```jsx
import { createContext, useContext, useState } from "react";

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

Interview answer:

> “I used Context API for authentication state so that Navbar, ProtectedRoute, Login page, and Profile page could access logged-in user details without prop drilling.”

---

## 7. React Router

React Router is used for navigation between pages.

Example pages:

```text
/login
/signup
/posts
/posts/:postId
/categories/:categoryId/posts
/admin/posts/create
```

Modern React Router supports route objects and data-related APIs such as loaders, actions, revalidation, and error boundaries, but for interview preparation you should first master basic routing and protected routes. ([React Router][7])

Example:

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/posts" element={<PostList />} />
        <Route path="/posts/:postId" element={<PostDetails />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

## 8. Protected Route

```jsx
import { Navigate } from "react-router-dom";

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
  path="/admin/posts/create"
  element={
    <ProtectedRoute>
      <CreatePost />
    </ProtectedRoute>
  }
/>
```

Interview answer:

> “ProtectedRoute checks whether JWT token exists or user is authenticated. If not, it redirects user to login page. Backend security is still mandatory because frontend protection can be bypassed.”

---

## 9. Axios Interceptor

Used to attach JWT token automatically.

```jsx
import axios from "axios";

const api = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL
});

api.interceptors.request.use(config => {
  const token = localStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});

export default api;
```

Interview answer:

> “I created a common Axios instance with interceptor so that JWT token is automatically passed in every secured API request.”

---

## 10. Redux / Redux Toolkit

Redux is used for global state management when state becomes complex.

For most small/medium apps:

```text
useState + useContext is enough
```

Use Redux when:

* Many screens share same state
* State update logic is complex
* Need predictable state flow
* Large enterprise application
* Cart, auth, permissions, filters, dashboard state

Redux Toolkit is the official recommended toolset for Redux development and includes utilities for store setup, reducers, immutable update logic, and more. ([Redux Toolkit][8])

Interview line:

> “For my blog application, Context API was enough for authentication. But in a larger enterprise application with complex shared state, I would prefer Redux Toolkit.”

---

## 11. TanStack Query / React Query

TanStack Query is useful for server-state management: fetching, caching, refetching, mutations, retries, pagination, and background updates. Its official docs describe query keys as internally used for caching, refetching, and sharing queries. ([TanStack][9])

Use it for:

* Post list caching
* Pagination
* Auto refetch
* Retry failed API calls
* Loading/error state management
* Optimistic updates

Interview line:

> “For simple API calls I used Axios with useEffect. For production-level server state, TanStack Query is better because it provides caching, retries, refetching, and mutation handling.”

---

# 5. Architecture and Internal Working

## React Application Architecture

```text
Browser
  ↓
index.html
  ↓
main.jsx
  ↓
App.jsx
  ↓
Routes
  ↓
Pages
  ↓
Components
  ↓
Service Layer / API Client
  ↓
Spring Boot REST API / API Gateway
  ↓
Microservices
  ↓
Database
```

---

## React Internal Flow

```text
User action
   ↓
Event handler executes
   ↓
State changes
   ↓
React re-renders component
   ↓
React compares previous UI with new UI
   ↓
Only required DOM updates are applied
```

Example:

```text
User selects category
   ↓
categoryId state changes
   ↓
useEffect runs
   ↓
API call goes to Spring Boot
   ↓
Post list state updates
   ↓
UI displays category-wise posts
```

---

## Backend Integration Flow

```text
React Login Form
   ↓
POST /api/auth/login
   ↓
Spring Security AuthenticationManager
   ↓
JWT token generated
   ↓
React receives token
   ↓
Token stored
   ↓
React sends Authorization: Bearer token
   ↓
Spring Security JWT Filter validates token
   ↓
Protected API response returned
```

---

## Production Flow

```text
User Browser
   ↓
AWS / Load Balancer / CDN
   ↓
Nginx serving React static files
   ↓
React app calls API Gateway
   ↓
API Gateway routes to microservices
   ↓
Services communicate with DB / Kafka / Cache
```

---

# 6. Practical Project-Based Explanation

For your **Spring Boot Blog Application**, React fits as the complete frontend.

## Existing Backend

```text
Spring Boot Blog Backend
   ├── User APIs
   ├── Auth APIs
   ├── Category APIs
   ├── Post APIs
   ├── Comment APIs
   ├── JWT Security
   ├── Pagination
   └── MySQL
```

## React Frontend

```text
React Blog UI
   ├── Login Page
   ├── Signup Page
   ├── Home Page
   ├── Post List
   ├── Post Details
   ├── Add Post
   ├── Add Comment
   ├── Category Filter
   ├── Profile
   └── Admin Dashboard
```

---

## Where React Solves Problems

### Problem 1: Backend APIs exist but no user interface

Solution:

> React provides the UI where users can login, view posts, add comments, and manage content.

---

### Problem 2: JWT needs to be used from frontend

Solution:

> React stores token after login and sends it in Authorization header using Axios interceptor.

---

### Problem 3: Need dynamic post listing

Solution:

> React calls paginated Spring Boot API and displays posts dynamically.

```http
GET /api/posts?pageNumber=0&pageSize=10&sortBy=postId&sortDir=desc
```

---

### Problem 4: Microservices need single UI

Solution:

```text
React UI
   ↓
API Gateway
   ↓
user-service
post-service
category-service
comment-service
```

---

### Problem 5: Deployment

Solution:

```text
React build
   ↓
Docker image with Nginx
   ↓
Kubernetes Deployment
   ↓
Ingress / Load Balancer
   ↓
AWS
```

---

# 7. Real Production Use Cases

## Where companies use React

React is commonly used for:

* Admin dashboards
* Banking portals
* E-commerce applications
* Internal enterprise tools
* SaaS applications
* CRM systems
* Blog/content platforms
* Monitoring dashboards
* Microservices frontend portals

---

## Problems React solves

| Problem                   | React Solution              |
| ------------------------- | --------------------------- |
| Complex UI                | Component-based design      |
| Reusable screens          | Shared components           |
| Dynamic data              | State and props             |
| API-driven frontend       | Axios/fetch                 |
| Authentication UI         | JWT + protected routes      |
| Multiple pages            | React Router                |
| Large app maintainability | Modular structure           |
| Performance               | Memoization, lazy loading   |
| Production deployment     | Static build with Nginx/CDN |

---

## Common Production Scenarios

### Scenario 1: Token expired

Problem:

```text
API returns 401
```

Solution:

```text
Axios interceptor detects 401
Clear token
Redirect to login
Show session expired message
```

---

### Scenario 2: Backend is down

Solution:

```text
Show friendly error page
Retry important API calls
Log error
Avoid blank screen
```

---

### Scenario 3: Slow API

Solution:

```text
Show loading spinner
Use pagination
Use caching
Avoid loading huge data at once
```

---

### Scenario 4: Role-based access

Solution:

```text
Backend controls actual permission
React hides unauthorized menu/buttons
```

Important interview point:

> Frontend role check improves UX, but real security must always be enforced in Spring Security backend.

---

### Scenario 5: XSS risk

OWASP warns that unsafe APIs like React’s `dangerouslySetInnerHTML` without sanitizing HTML can create XSS risk. ([OWASP Cheat Sheet Series][10])

Best practice:

```text
Do not render raw HTML from users
Validate URLs
Sanitize content if HTML rendering is required
Keep dependencies updated
```

---

# 8. Hands-On Practice Plan

## Beginner Exercises

1. Create React app using Vite
2. Create Header, Footer, PostCard components
3. Display static post data
4. Render list of posts
5. Add search box
6. Create login form
7. Handle form state
8. Show validation message

---

## Intermediate Tasks

1. Integrate with Spring Boot `/api/posts`
2. Display paginated posts
3. Create post details page
4. Implement login API
5. Store JWT token
6. Add Axios interceptor
7. Create protected route
8. Implement logout
9. Add comment feature
10. Show backend validation errors

---

## Real Project-Level Tasks

1. Role-based navbar
2. Admin-only post creation
3. Category-wise filtering
4. Pagination and sorting UI
5. Global error handler
6. Loading indicators
7. Toast notifications
8. Environment-based API URL
9. Dockerize React app
10. Deploy on AWS

---

## Strong Project Idea for Interview

Build:

> **Full Stack Blog Management System using React, Spring Boot, JWT, MySQL, Docker, Jenkins, Kubernetes, and AWS**

Features:

```text
User registration/login
JWT authentication
Role-based access
Create/update/delete posts
View posts by category
Pagination and sorting
Comment system
Admin dashboard
React protected routes
Axios interceptor
Dockerized frontend/backend
CI/CD using Jenkins
Deployment on AWS
```

Interview line:

> “This project helped me understand end-to-end full stack development, from React UI to Spring Boot REST APIs, JWT security, database persistence, Dockerization, and cloud deployment.”

---

# 9. Integration With Java / Spring Boot

React does not integrate with Spring Boot like a Maven dependency. React usually runs as a separate frontend application and communicates with Spring Boot through REST APIs.

## Recommended Structure

```text
blog-backend/
   └── Spring Boot application

blog-frontend/
   └── React application
```

---

## React Setup

```bash
npm create vite@latest blog-frontend
cd blog-frontend
npm install
npm install axios react-router-dom react-toastify
npm run dev
```

---

## Environment Configuration

Create `.env`:

```properties
VITE_API_BASE_URL=http://localhost:8080/api
```

Use it:

```jsx
const API_BASE_URL = import.meta.env.VITE_API_BASE_URL;
```

---

## Axios API Client

```jsx
// src/api/apiClient.js
import axios from "axios";

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL
});

apiClient.interceptors.request.use(
  config => {
    const token = localStorage.getItem("token");

    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
  },
  error => Promise.reject(error)
);

apiClient.interceptors.response.use(
  response => response,
  error => {
    if (error.response && error.response.status === 401) {
      localStorage.removeItem("token");
      window.location.href = "/login";
    }

    return Promise.reject(error);
  }
);

export default apiClient;
```

---

## Auth Service

```jsx
// src/services/authService.js
import apiClient from "../api/apiClient";

export const login = async (credentials) => {
  const response = await apiClient.post("/auth/login", credentials);
  return response.data;
};

export const register = async (userData) => {
  const response = await apiClient.post("/auth/register", userData);
  return response.data;
};
```

---

## Post Service

```jsx
// src/services/postService.js
import apiClient from "../api/apiClient";

export const getAllPosts = async (pageNumber = 0, pageSize = 10) => {
  const response = await apiClient.get(
    `/posts?pageNumber=${pageNumber}&pageSize=${pageSize}&sortBy=postId&sortDir=desc`
  );

  return response.data;
};

export const getPostById = async (postId) => {
  const response = await apiClient.get(`/posts/${postId}`);
  return response.data;
};

export const createPost = async (postData) => {
  const response = await apiClient.post("/posts", postData);
  return response.data;
};
```

---

## React Component Calling Spring Boot API

```jsx
import { useEffect, useState } from "react";
import { getAllPosts } from "../services/postService";

function PostList() {
  const [posts, setPosts] = useState([]);
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadPosts();
  }, []);

  const loadPosts = async () => {
    try {
      setLoading(true);
      const data = await getAllPosts(0, 10);
      setPosts(data.content);
    } catch (err) {
      setError("Unable to load posts");
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <p>Loading posts...</p>;
  if (error) return <p>{error}</p>;

  return (
    <div>
      {posts.map(post => (
        <div key={post.postId}>
          <h2>{post.title}</h2>
          <p>{post.content}</p>
        </div>
      ))}
    </div>
  );
}

export default PostList;
```

---

## Spring Boot CORS Configuration

```java
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {

            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                        .allowedOrigins("http://localhost:5173")
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                        .allowedHeaders("*")
                        .allowCredentials(true);
            }
        };
    }
}
```

Interview line:

> “During React and Spring Boot integration, CORS configuration is required because frontend and backend run on different origins during development.”

---

## Dockerfile for React

```dockerfile
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:alpine

COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## Jenkins Pipeline Idea

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-user/blog-frontend.git'
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t blog-frontend:latest .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
```

---

# 10. Common Interview Questions and Answers

## Basic Level

### 1. What is React?

React is a JavaScript library used to build component-based user interfaces. In my project, React was used to build the frontend for consuming Spring Boot REST APIs.

---

### 2. What is a component?

A component is a reusable UI block. For example, Header, LoginForm, PostCard, and CategoryList can be separate React components.

---

### 3. What is JSX?

JSX is a syntax that allows us to write HTML-like markup inside JavaScript. It improves readability and is converted into JavaScript internally.

---

### 4. What are props?

Props are read-only inputs passed from parent component to child component. They make components reusable.

---

### 5. What is state?

State is dynamic data managed inside a component. When state changes, React re-renders the component.

---

### 6. Difference between props and state?

Props are passed from parent and are read-only. State belongs to the component and can be changed using setter functions.

---

### 7. What is `useState`?

`useState` is a Hook used to manage local component state.

---

### 8. What is `useEffect`?

`useEffect` is used for side effects such as API calls, timers, subscriptions, and cleanup logic.

---

### 9. What is conditional rendering?

Conditional rendering means showing UI based on conditions, like showing Login button if user is not logged in and Logout button if user is logged in.

---

### 10. Why do we use `key` in list rendering?

`key` helps React identify which list item changed, added, or removed. It improves rendering accuracy and performance.

---

## Intermediate Level

### 11. How do you call Spring Boot API from React?

I create a service layer using Axios or fetch and call Spring Boot REST endpoints. For secured APIs, I pass JWT token in Authorization header.

---

### 12. How do you handle JWT in React?

After login, React receives JWT token from backend. The token is stored, usually in localStorage or memory, and sent in API requests using Axios interceptor.

---

### 13. What is React Router?

React Router is used for client-side routing. It allows navigation between pages like `/login`, `/posts`, and `/posts/:id`.

---

### 14. What is a protected route?

A protected route allows access only if user is authenticated. If token is missing or invalid, user is redirected to login page.

---

### 15. What is Context API?

Context API is used to share data globally without passing props manually at every level. It is useful for authentication state, theme, and logged-in user details.

---

### 16. When do you use Redux?

I use Redux when application state becomes large and shared across many unrelated components. For small apps, Context API is enough.

---

### 17. What is Axios interceptor?

Axios interceptor allows us to modify requests or responses globally. I use request interceptor to attach JWT token and response interceptor to handle 401 errors.

---

### 18. How do you handle forms in React?

I usually use controlled components, where input values are stored in state and updated through `onChange`.

---

### 19. What are controlled components?

Controlled components are form elements whose value is controlled by React state.

---

### 20. What are uncontrolled components?

Uncontrolled components store form data in the DOM itself and are accessed using refs.

---

## Experienced Level

### 21. How does React rendering work?

When state or props change, React re-renders the affected component and updates the DOM efficiently based on the new UI output.

---

### 22. What causes unnecessary re-rendering?

Unnecessary re-rendering can happen due to changing object references, inline functions, parent re-renders, or improper state placement.

---

### 23. How do you optimize React performance?

Use pagination, lazy loading, memoization, `React.memo`, `useMemo`, `useCallback`, code splitting, and avoid unnecessary state updates.

---

### 24. What is lazy loading?

Lazy loading means loading a component only when needed. It improves initial load time.

---

### 25. What is code splitting?

Code splitting breaks application bundle into smaller chunks so browser loads only required code.

---

### 26. What is `useMemo`?

`useMemo` memoizes expensive calculated values and recalculates only when dependencies change.

---

### 27. What is `useCallback`?

`useCallback` memoizes function references to avoid unnecessary re-rendering of child components.

---

### 28. What is `React.memo`?

`React.memo` prevents a component from re-rendering if its props have not changed.

---

### 29. What is prop drilling?

Prop drilling means passing props through multiple intermediate components unnecessarily. Context API or state management can solve this.

---

### 30. What is error boundary?

Error boundary catches rendering errors in child components and shows fallback UI instead of crashing the entire application.

---

## Scenario-Based Questions

### 31. How did you use React in your project?

I used React to build frontend screens for my Spring Boot Blog Application. It consumed REST APIs for login, posts, categories, and comments. I implemented routing, JWT-based authentication, protected routes, reusable components, and API service layer.

---

### 32. How did you secure React routes?

I created protected routes that check authentication state before allowing access. But actual security was enforced in Spring Boot using Spring Security and JWT filters.

---

### 33. How did you handle API errors?

I handled API errors using try-catch in service calls and Axios response interceptor for global errors like 401. User-friendly messages were shown using toast notifications.

---

### 34. How did you handle token expiry?

When backend returned 401, Axios interceptor cleared token and redirected user to login page with a session-expired message.

---

### 35. How did you manage logged-in user state?

I used Context API to store logged-in user details and authentication status so Navbar, Profile, and ProtectedRoute could access it.

---

### 36. How did React connect with microservices?

React called API Gateway instead of calling individual services directly. API Gateway routed requests to user-service, post-service, and category-service.

---

### 37. How did you handle pagination?

Backend returned paginated response from Spring Boot. React maintained page number and page size in state and called API whenever page changed.

---

### 38. How did you handle role-based UI?

React checked user roles and conditionally displayed admin menus. Backend still validated roles using Spring Security.

---

### 39. How did you handle CORS?

I configured CORS in Spring Boot to allow frontend origin during development. In production, frontend and backend were deployed behind proper domain/gateway configuration.

---

### 40. How did you deploy React?

I created production build using `npm run build`, served it using Nginx, dockerized it, and deployed it using Jenkins/Kubernetes/AWS deployment flow.

---

## Project-Based Questions

### 41. Why did you choose React for your blog application?

React is component-based, easy to integrate with REST APIs, supports routing, handles dynamic UI well, and is widely used in full stack applications.

---

### 42. How did you design your React folder structure?

I separated components, pages, services, context, routes, utilities, and assets to keep the project maintainable.

---

### 43. How did you call secured APIs?

I created a common Axios client and added JWT token in Authorization header using interceptor.

---

### 44. How did you handle validation?

Basic client-side validation was done in React. Final validation was done in Spring Boot using DTO validation annotations.

---

### 45. How did you handle global exceptions?

Backend returned standardized error responses. React displayed those messages in forms or toast notifications.

---

## Production-Level Questions

### 46. What are common React production issues?

Common issues include blank screen due to JS error, token expiry, CORS issue, wrong environment URL, large bundle size, memory leaks, and poor error handling.

---

### 47. How do you prevent XSS in React?

Avoid rendering raw HTML, avoid unsafe `dangerouslySetInnerHTML`, validate URLs, sanitize user-generated content, and keep dependencies updated.

---

### 48. How do you improve React app load time?

Use lazy loading, code splitting, optimized images, caching, CDN, minified build, and avoid unnecessary dependencies.

---

### 49. How do you monitor frontend issues?

Use browser console, network tab, application logs, error tracking tools, backend logs, API gateway logs, and user-reported scenarios.

---

### 50. What should backend developers know about React?

Backend developers should understand API consumption, DTO design, CORS, JWT flow, pagination, error response format, frontend validation, and deployment flow.

---

# 11. Scenario-Based Interview Preparation

## Scenario: How did you use React in your project?

> “In my project, I used React as the frontend layer for my Spring Boot Blog Application. The backend exposed REST APIs for authentication, posts, categories, and comments. React consumed those APIs using Axios. I implemented login, signup, post listing, post details, add comment, category filtering, pagination, and protected routes. For authentication, backend generated JWT token and React sent that token in Authorization header for secured APIs.”

---

## Scenario: Why did you choose React?

> “We chose React because it provides component-based development, reusable UI, strong ecosystem, easy REST API integration, and good support for routing and state management. Since our backend was API-driven using Spring Boot, React was a good fit for building a dynamic frontend.”

---

## Scenario: What issue did you face?

> “One common issue was CORS while calling Spring Boot APIs from React during local development. React was running on a different port and Spring Boot on another port. We solved it by configuring CORS properly in Spring Boot.”

---

## Scenario: How did you debug frontend/backend integration?

> “I used browser developer tools, especially Network tab, to check API URL, request payload, response status, headers, and JWT token. On backend side, I checked Spring Boot logs and security filter logs to verify whether token validation was successful.”

---

## Scenario: How did you improve performance?

> “We avoided loading all posts at once. Backend provided pagination and sorting, and React maintained page state. We also used reusable components, avoided unnecessary API calls, and planned lazy loading for larger modules like admin dashboard.”

---

## Scenario: How did you secure it?

> “Frontend implemented protected routes and role-based UI rendering. But actual security was enforced in Spring Boot using Spring Security and JWT. React only improved user experience; backend remained the source of truth for authorization.”

---

## Scenario: How did you deploy it?

> “React application was built using production build command, served through Nginx, dockerized, and deployed on cloud infrastructure. Backend was deployed separately, and React consumed backend APIs through configured environment-based API URL.”

---

# 12. Live Project Explanation

Use these lines in interviews.

## General Explanation

> “In my project, we used React for building the frontend of our Spring Boot Blog Application. The backend was developed using Spring Boot REST APIs, Spring Security with JWT, JPA/Hibernate, and MySQL. React consumed those APIs and provided screens for login, signup, post listing, post details, comment management, and category-wise filtering.”

---

## Authentication Explanation

> “As per my project experience, after successful login, Spring Boot returned a JWT token. On the React side, we stored the token and passed it in the Authorization header using Axios interceptor. For secured pages, we implemented ProtectedRoute so unauthenticated users were redirected to login.”

---

## Microservices Explanation

> “In our planned microservices architecture, React acts as a single frontend application. Instead of calling each microservice directly, React calls the API Gateway. The gateway then routes requests to user-service, post-service, category-service, or comment-service.”

---

## Production Explanation

> “In production, React helped us separate frontend and backend responsibilities. Backend handled business logic, security, and database operations, while React handled user interaction, routing, forms, validation messages, and API response rendering.”

---

## Problem-Solution Explanation

> “Earlier, we had only backend APIs and testing was mainly through Swagger/Postman. After adding React, we created a real user-facing application where users could login, view posts, add comments, and interact with the system from browser.”

---

# 13. Common Mistakes and Best Practices

## Beginner Mistakes

* Learning Redux before understanding state/props
* Not understanding JavaScript basics
* Calling APIs directly inside every component without service layer
* Not using `key` in list rendering
* Mutating state directly
* Ignoring error handling
* Ignoring loading state

---

## Experienced Developer Mistakes

* Putting all logic in one component
* Storing too much data in global state
* Using Context for frequently changing large data
* Not separating API layer
* Poor folder structure
* Not handling token expiry
* Not using environment variables
* Not testing important user flows

---

## Production Mistakes

* Hardcoding backend URL
* Exposing secrets in frontend
* Treating frontend route protection as actual security
* Not handling 401/403 globally
* Large bundle size
* No error boundary
* No logging/error tracking
* No cache strategy
* No proper Docker/Nginx config

---

## Best Practices

```text
Use functional components
Use Hooks properly
Keep components small
Create service layer for APIs
Use Axios interceptor for token
Use environment variables
Handle loading and error states
Use protected routes
Validate forms
Keep backend as security source of truth
Use pagination for large data
Use lazy loading for large modules
Write tests for important components
```

---

## Security Best Practices

```text
Do not store sensitive information in frontend
Never trust frontend authorization
Avoid dangerouslySetInnerHTML
Validate user input on backend
Use HTTPS in production
Handle token expiry
Use proper CORS configuration
Keep dependencies updated
```

---

# 14. Comparison With Related Technologies

## React vs Angular

| Point            | React            | Angular                        |
| ---------------- | ---------------- | ------------------------------ |
| Type             | Library          | Full framework                 |
| Learning curve   | Easier           | Higher                         |
| Flexibility      | High             | Structured                     |
| State management | External choices | Built-in patterns              |
| Best for         | Flexible UI apps | Enterprise full framework apps |

Use React when you want flexibility and component-based UI.

Use Angular when company wants a complete opinionated framework.

---

## React vs Vue

| Point            | React      | Vue    |
| ---------------- | ---------- | ------ |
| Ecosystem        | Very large | Large  |
| Learning         | Moderate   | Easier |
| Enterprise usage | Very high  | Good   |
| Flexibility      | High       | High   |

---

## React vs Thymeleaf/JSP

| Point                 | React       | JSP/Thymeleaf           |
| --------------------- | ----------- | ----------------------- |
| Rendering             | Client-side | Server-side             |
| UI interaction        | Rich        | Limited compared to SPA |
| API-driven            | Yes         | Less common             |
| Full stack separation | Strong      | Tightly coupled         |

For modern Spring Boot + microservices, React is better because frontend and backend can be deployed independently.

---

## React vs Next.js

| Point    | React              | Next.js          |
| -------- | ------------------ | ---------------- |
| Type     | UI library         | React framework  |
| Routing  | External router    | Built-in routing |
| SSR      | Manual/extra setup | Built-in         |
| SEO      | Needs setup        | Better           |
| Best for | SPA/dashboard      | SEO/product apps |

For your current job switch preparation:

> Learn React first. Learn Next.js later.

---

## When to Use React

Use React when:

* UI is dynamic
* Backend is API-driven
* SPA is required
* Reusable components are needed
* Frontend/backend separation is desired
* Microservices need a single frontend

---

## When Not to Use React

Avoid React when:

* Application is very small/static
* Team has no JavaScript experience
* SEO-heavy application needs SSR and you are not using Next.js
* Simple server-rendered pages are enough

---

# 15. Revision Notes

## Key Definitions

```text
React        → JavaScript library for building UI
Component    → Reusable UI block
JSX          → HTML-like syntax in JavaScript
Props        → Read-only data passed from parent to child
State        → Dynamic data inside component
Hook         → Function that gives React features to functional components
useState     → Manage state
useEffect    → Handle side effects/API calls
Context API  → Share global data
React Router → Client-side routing
ProtectedRoute → Restrict route based on auth
Axios interceptor → Modify request/response globally
Redux        → Global state management
```

---

## Important Commands

```bash
npm create vite@latest blog-ui
cd blog-ui
npm install
npm run dev
npm run build
npm install axios react-router-dom react-toastify
```

---

## Important Interview Points

```text
React is component-based.
React consumes Spring Boot REST APIs.
JWT token is passed from React to backend.
Frontend protected route is only UX; backend security is mandatory.
Context API is good for auth state.
Redux is useful for complex global state.
Axios interceptor helps attach token globally.
useEffect is commonly used for API calls.
Pagination should be handled from backend and displayed in React.
React can be dockerized and deployed using Nginx.
```

---

## 5-Minute Revision Summary

> React is a frontend JavaScript library used to build reusable UI components. In my Spring Boot Blog Application, React works as the frontend layer that consumes REST APIs for login, posts, categories, and comments. I used React Router for navigation, Axios for API calls, JWT token for secured requests, Context API for authentication state, and protected routes for secured pages. In microservices architecture, React calls API Gateway, and Gateway routes requests to services. In production, React is built into static files, served using Nginx, dockerized, and deployed through Jenkins/Kubernetes/AWS. Backend remains responsible for security, validation, and business logic.

---

# 16. 30-Day Learning Plan

## Week 1: React Foundation

### Day 1

Theory:

* What is React?
* SPA vs traditional web app
* React project structure

Hands-on:

* Create Vite React app

---

### Day 2

Theory:

* Components
* JSX
* Import/export

Hands-on:

* Create Header, Footer, PostCard

---

### Day 3

Theory:

* Props
* Component reuse

Hands-on:

* Pass post data to PostCard using props

---

### Day 4

Theory:

* State
* `useState`

Hands-on:

* Create counter
* Create login form state

---

### Day 5

Theory:

* Events
* Conditional rendering

Hands-on:

* Show login/logout button conditionally

---

### Day 6

Theory:

* List rendering
* `map`
* `key`

Hands-on:

* Render list of posts

---

### Day 7

Revision:

* Revise components, props, state, events
* Prepare 10 basic interview questions

---

## Week 2: API Integration With Spring Boot

### Day 8

Theory:

* REST API consumption
* Axios/fetch

Hands-on:

* Install Axios
* Call `/api/posts`

---

### Day 9

Theory:

* `useEffect`
* API loading on page load

Hands-on:

* Load posts from Spring Boot backend

---

### Day 10

Theory:

* Error handling
* Loading state

Hands-on:

* Add loading spinner and error message

---

### Day 11

Theory:

* Forms
* Controlled components

Hands-on:

* Create login form

---

### Day 12

Theory:

* JWT flow

Hands-on:

* Call login API
* Store JWT token

---

### Day 13

Theory:

* Axios interceptor

Hands-on:

* Attach token in Authorization header

---

### Day 14

Revision:

* Explain full login flow
* Prepare API integration interview answers

---

## Week 3: Routing, Auth, Project Features

### Day 15

Theory:

* React Router

Hands-on:

* Create routes: login, signup, posts, post details

---

### Day 16

Theory:

* Dynamic route params

Hands-on:

* Implement `/posts/:postId`

---

### Day 17

Theory:

* ProtectedRoute

Hands-on:

* Protect add-post page

---

### Day 18

Theory:

* Context API

Hands-on:

* Create AuthContext

---

### Day 19

Theory:

* Category filtering

Hands-on:

* Show posts by category

---

### Day 20

Theory:

* Pagination and sorting

Hands-on:

* Integrate backend pagination

---

### Day 21

Revision:

* Explain project architecture
* Prepare project-based interview answer

---

## Week 4: Production, Testing, Deployment, Interview

### Day 22

Theory:

* Folder structure
* Clean architecture

Hands-on:

* Refactor into pages/components/services/context

---

### Day 23

Theory:

* Performance basics

Hands-on:

* Add lazy loading for routes

---

### Day 24

Theory:

* Security best practices

Hands-on:

* Handle 401, logout, invalid token

---

### Day 25

Theory:

* Testing basics

Hands-on:

* Test Login component / PostCard component

---

### Day 26

Theory:

* Environment variables

Hands-on:

* Configure dev/prod API URL

---

### Day 27

Theory:

* Dockerizing React

Hands-on:

* Create Dockerfile with Nginx

---

### Day 28

Theory:

* CI/CD

Hands-on:

* Prepare Jenkins pipeline idea

---

### Day 29

Interview:

* Prepare 50 Q&A
* Practice scenario answers

---

### Day 30

Mock Interview:

* Explain full project:

```text
React UI
↓
API Gateway / Spring Boot REST APIs
↓
JWT Security
↓
Microservices
↓
Database
↓
Docker/Kubernetes/AWS deployment
```

Final speaking line:

> “I can work as a Java Full Stack Developer because I understand both backend API development using Spring Boot and frontend integration using React. I can design REST APIs, secure them using JWT, consume them from React, handle routing and state management, and deploy the full stack application using Docker, Jenkins, Kubernetes, and AWS.”

---

## Best Learning Order for You

Follow this order:

```text
JavaScript basics
   ↓
React components
   ↓
Props/state
   ↓
Events/forms
   ↓
useEffect/API calls
   ↓
React Router
   ↓
JWT integration
   ↓
Context API
   ↓
Project implementation
   ↓
Docker/deployment
   ↓
Redux/TanStack Query later
```

Do **not** start with Redux or advanced hooks. First become strong in React + Spring Boot API integration because that is what interviewers will connect with your profile.

[1]: https://react.dev/learn?utm_source=chatgpt.com "Quick Start"
[2]: https://vite.dev/guide/?utm_source=chatgpt.com "Getting Started"
[3]: https://testing-library.com/docs/react-testing-library/intro/?utm_source=chatgpt.com "React Testing Library"
[4]: https://react.dev/?utm_source=chatgpt.com "React"
[5]: https://react.dev/reference/react/hooks?utm_source=chatgpt.com "Built-in React Hooks"
[6]: https://react.dev/learn/passing-data-deeply-with-context?utm_source=chatgpt.com "Passing Data Deeply with Context"
[7]: https://reactrouter.com/start/data/route-object?utm_source=chatgpt.com "Route Object"
[8]: https://redux-toolkit.js.org/?utm_source=chatgpt.com "Redux Toolkit"
[9]: https://tanstack.com/query/v5/docs/framework/react/guides/queries?utm_source=chatgpt.com "TanStack Query React Docs"
[10]: https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com "Cross Site Scripting Prevention Cheat Sheet"
