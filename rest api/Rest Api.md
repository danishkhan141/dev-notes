# Content
1. [Rest API Overview](#-rest-api--complete-interview-notes-neat--clean)
2. [Resource vs Method](#-core-rule-very-important)
3. [Absolute vs Relative Path](#3️⃣-absolute-path-vs-relative-path-important-concept)
4. [URI vs URL](#1️⃣-uri-vs-url-in-rest-api-context)
5. [Http Status Code](#2️⃣-http-status-codes--which-code-when)
6. [REST Api Request And Response structor](#3️⃣-deep-dive-rest-api-request--response-structure)
7. [How Tomcat Parse HTTP internally & content length](#1️⃣-how-tomcat-parses-http-request-internally)
8. [REST Uri naming Rules Singular vs Plural](#rest-uri-naming-rules-plural-vs-singular)
9. [@PathVariable vs @RequestParam](#pathvariable-vs-requestparam--deep-dive-rest--spring-boot)
10. [Put vs Patch ,Idempotency, PAgination & Sorting](#1️⃣-put-vs-patch--deep-dive-most-confusing-topic)
11. [Http Spec](#what-is-meant-by-http-specs)
12. [Etag, IfMatch, @Version, Optimistic Locking](#etag-vs-if-match-how-rest-handles-concurrency)
13. [REST API Versioning](#what-is-meant-by-rest-api-versioning)

## 🌐 REST API — Complete Interview Notes (Neat & Clean)

![Image](https://media.licdn.com/dms/image/v2/C4D12AQHh6l0xkbhTPg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1622931040032?e=2147483647\&t=KP6s82dMHXyOTcVsNCkKypBjIlQSWYCqI4ucfTrzTJg\&v=beta)

![Image](https://images.ctfassets.net/vwq10xzbe6iz/5sBH4Agl614xM7exeLsTo7/9e84dce01735f155911e611c42c9793f/rest-api.png)

![Image](https://www.techtarget.com/rms/onlineimages/http_methods_purpose_and_properties-f_mobile.png)

![Image](https://restfulapi.net/wp-content/uploads/Stateless-REST-API.png)

---

## 1️⃣ What is an API? (Foundation)

**API (Application Programming Interface)**
→ A contract that allows **two software systems to communicate** with each other.

**Simple line (interview):**

> API is a set of rules that defines how one application can request data or services from another application.

**Real-world analogy:**

* Restaurant menu = API
* You (client) place an order
* Kitchen (server) prepares response
* You don’t know internal cooking logic

---

## 2️⃣ What is REST?

**REST = Representational State Transfer**

* An **architectural style**
* Proposed by **Roy Fielding (2000)**
* Used for designing **scalable, stateless web services**

**Important:**
❌ REST is **not** a protocol
✅ REST uses **HTTP protocol**

---

## 3️⃣ What is a REST API?

> A REST API is an API that follows REST architectural principles and uses HTTP methods to perform operations on resources.

**In simple words:**
REST API exposes **resources** over HTTP and allows clients to **Create, Read, Update, Delete** them.

---

## 4️⃣ Key Concepts of REST (VERY IMPORTANT)

### 🔹 Resource

* Any data/object exposed by server
* Identified by **URI**

**Examples:**

```
/users
/users/101
/orders/5001
```

👉 Resource = **Noun**, not verb

❌ `/getUsers`
✅ `/users`

---

### 🔹 HTTP Methods (CRUD Mapping)

| HTTP Method | Purpose         | CRUD   |
| ----------- | --------------- | ------ |
| GET         | Fetch data      | Read   |
| POST        | Create new data | Create |
| PUT         | Full update     | Update |
| PATCH       | Partial update  | Update |
| DELETE      | Remove data     | Delete |

**Interview line:**

> REST uses standard HTTP methods to operate on resources.

---

## 5️⃣ REST Architectural Principles (6 Constraints)

### 1️⃣ Client–Server

* Client & server are independent
* Client → UI
* Server → Business logic + DB

✅ Improves scalability and separation of concerns

---

### 2️⃣ Stateless ⭐ (Most Asked)

* Each request is **independent**
* Server does **not store client session**

**Means:**

* Every request must contain:

  * Authentication info
  * Required parameters

**Interview line:**

> REST APIs are stateless, meaning the server does not remember previous requests.

---

### 3️⃣ Cacheable

* Responses can be cached
* Improves performance

**Example:**

```http
Cache-Control: max-age=3600
```

---

### 4️⃣ Uniform Interface ⭐

Ensures consistency across APIs.

Includes:

* Resource-based URIs
* HTTP methods
* Standard status codes
* JSON/XML response formats

---

### 5️⃣ Layered System

* Client doesn’t know if it talks to:

  * Load balancer
  * API Gateway
  * Actual service

---

### 6️⃣ Code on Demand (Optional)

* Server can send executable code (rarely used)

---

## 6️⃣ REST API Request Structure

![Image](https://www.aisangam.com/blog/wp-content/uploads/2019/10/HTTPRequestMessageFormat.png)

![Image](https://resize.latenode.com/cdn-cgi/image/width%3D960%2Cformat%3Dauto%2Cfit%3Dscale-down/https%3A//cdn.prod.website-files.com/62c40e4513da320b60f32941/65f18825d8630182d7166288_bjc7Qmq9sGd-RPOi6CniHynbVjxqXFBSWyoLLacrG-0BxOurti9UUNv9ewqM1Tw4sGyfyJ2r9eT_X5mQvVP94K_pYgK-cUCNTCyW_kTy3ibSwASF7IBOrWko7zCQ23Pq1frNPgx3v2Uk82-WMA53v0Y.jpeg)

![Image](https://imagedelivery.net/PVooPtpJE-25QaNkbEuXvw/images/soapui/docs/rest_response_json_view_os_1.png/public)

### Request Contains:

1. **URL**
2. **HTTP Method**
3. **Headers**
4. **Body (optional)**

**Example:**

```http
POST /users
Content-Type: application/json
Authorization: Bearer token123

{
  "name": "Danish",
  "email": "danish@gmail.com"
}
```

---

## 7️⃣ REST API Response Structure

### Response Contains:

1. **Status Code**
2. **Headers**
3. **Body**

**Example:**

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 101,
  "name": "Danish"
}
```

---

## 8️⃣ HTTP Status Codes (Interview Favorite)

| Code | Meaning               |
| ---- | --------------------- |
| 200  | OK                    |
| 201  | Created               |
| 204  | No Content            |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 409  | Conflict              |
| 500  | Internal Server Error |

**Golden rule:**

> Always return meaningful HTTP status codes.

---

## 9️⃣ REST API Data Formats

* **JSON** ✅ (most common)
* XML (older)
* YAML (rare)

**Why JSON?**

* Lightweight
* Language independent
* Easy to parse

---

## 🔟 REST vs SOAP (One-liner Difference)

| REST         | SOAP     |
| ------------ | -------- |
| Lightweight  | Heavy    |
| Uses HTTP    | Uses XML |
| Stateless    | Stateful |
| Faster       | Slower   |
| JSON support | XML only |

---

## 1️⃣1️⃣ REST API Security

Common approaches:

* **JWT Token**
* OAuth2
* API Keys
* HTTPS (mandatory)

**Interview line:**

> REST APIs are typically secured using JWT or OAuth with HTTPS.

---

## 1️⃣2️⃣ REST API Versioning

Ways:

```
/api/v1/users
/api/v2/users
```

OR

```
Header: Accept-Version: v1
```

---

## 1️⃣3️⃣ Best Practices (Very Important)

✅ Use nouns in URLs
✅ Use correct HTTP methods
✅ Use proper status codes
✅ Validate input
✅ Version your APIs
✅ Document APIs (Swagger/OpenAPI)

---

## 1️⃣4️⃣ Common Interview Traps

❌ Using verbs in URL
❌ Using GET for update
❌ Returning 200 for errors
❌ Maintaining session on server
❌ No versioning

---

## 1️⃣5️⃣ REST API – One Paragraph Interview Answer ⭐

> REST API is an architectural style for designing web services that expose resources over HTTP. It follows principles like statelessness, client-server separation, and uniform interface. REST APIs use standard HTTP methods such as GET, POST, PUT, PATCH, and DELETE to perform operations on resources and typically exchange data in JSON format. They are scalable, lightweight, and widely used in modern microservices-based systems.

---

## 🧠 Final Summary (Quick Revision)

* REST = Architectural style
* Resource-based URLs
* Stateless communication
* HTTP methods = CRUD
* JSON + HTTP status codes
* Secure using JWT/OAuth
* Backbone of microservices

---

If you want next:

* **REST vs GraphQL**
* **REST API in Spring Boot**
* **End-to-end REST API flow with controller → service → repo**
* **REST interview Q&A**

Just tell me 👍

```
you mean @GetMapping("/users")---here users is resource.....
method name in controller should be verb like getUsers but URI should be noun only?
 @GetMapping("/users")
public UserEntity getUser(){}
```
Yes 👍 — **you are thinking in the right direction**, bas thoda sa clarity missing hai.
Let me **lock this concept permanently** (interview + real-world coding dono ke liye).

---

## 🔑 Core Rule (Very Important)

> **URI = Resource = NOUN**
> **Controller Method Name = Action = VERB**

---

## 1️⃣ What is a *Resource* in REST?

A **resource** is **data** managed by the server.

Examples of resources:

* User
* Order
* Product
* Payment

👉 These are **nouns**, not actions.

---

## 2️⃣ Your Example — Explained Properly

```java
@GetMapping("/users")
public UserEntity getUser() {
}
```

### 🔍 Breakdown

| Part          | Meaning                            |
| ------------- | ---------------------------------- |
| `/users`      | ✅ **Resource name (noun)**         |
| `@GetMapping` | ✅ HTTP method tells the **action** |
| `getUser()`   | ✅ Java method name (verb)          |

✔️ This is **100% correct REST design**

---

## 3️⃣ Why URI Should NOT Be a Verb?

### ❌ Wrong (Not RESTful)

```http
/getUsers
/createUser
/deleteUser
```

Why wrong?

* Action is already conveyed by **HTTP method**
* Redundant and non-standard

---

### ✅ Correct (RESTful)

```http
GET    /users
POST   /users
GET    /users/{id}
PUT    /users/{id}
DELETE /users/{id}
```

➡️ **Same resource**, different HTTP methods = different operations

---

## 4️⃣ Controller Method Naming (Java Side)

Controller methods are **internal implementation**, REST does NOT care about their names.

You can write:

```java
public UserEntity getUser()
public UserEntity fetchUser()
public UserEntity findUser()
```

All are fine.

👉 Interview line:

> REST enforces rules on **URI design**, not on method names inside the controller.

---

## 5️⃣ Why Method Name Is Verb?

Because:

* Java method represents **action**
* Improves code readability
* Helps developers understand logic

Example:

```java
@GetMapping("/users/{id}")
public UserEntity getUserById(@PathVariable Long id) {
}
```

✔️ Perfect design

---

## 6️⃣ Multiple Operations on SAME Resource

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public List<UserEntity> getUsers() {}

    @PostMapping
    public UserEntity createUser() {}

    @PutMapping("/{id}")
    public UserEntity updateUser() {}

    @DeleteMapping("/{id}")
    public void deleteUser() {}
}
```

### Key Observation ⭐

* `/users` → **resource**
* HTTP method → **operation**
* Method name → **verb (internal)**

---

## 7️⃣ One-Line Interview Answer (Golden)

> In REST, **URIs represent resources and must be nouns**, while **actions are represented by HTTP methods**. Controller method names can be verbs since they are internal to the application.

---

## 8️⃣ Common Confusion You Just Cleared (Good Sign)

❓ *“Should method name also be noun?”*
❌ No

❓ *“Is `/users` resource?”*
✅ Yes

❓ *“Action comes from where?”*
👉 From HTTP method (`GET`, `POST`, etc.)

---

## 🧠 Final Memory Hook

> **URI = WHAT (noun)**
> **HTTP method = HOW (action)**
> **Controller method name = IMPLEMENTATION (verb)**

---

If you want, next I can explain:

* `/users` vs `/users/{id}`
* Why plural nouns are preferred
* Why GET must not change data
* How REST design is asked in interviews (real questions)

Just say 👍
```
resource should always start with / why? @RequestMapping("/users")
```
## 🔑 Short Direct Answer (Interview Ready)

> **Yes, a resource path starts with `/` because it represents an absolute URI path in the HTTP URL hierarchy.**

Now let’s **prove this**, not just state it.

---

## 1️⃣ Big Picture: What `/users` Actually Is

When you write:

```java
@RequestMapping("/users")
```

You are **NOT just writing a string**.
You are defining the **path part of an HTTP URL**.

Example full URL:

```
https://api.myapp.com/users
```

Breakdown:

```
https://        → protocol
api.myapp.com   → host
/users          → path (resource)
```

👉 `/users` is the **path component of a URI**.

---

## 2️⃣ Why Path Must Start With `/` (Core Reason)

### Because HTTP follows **URI path syntax**

According to URI standards (RFC 3986):

```
URI = scheme://host:port/path
```

And **path ALWAYS starts with `/`**.

✔️ Correct:

```
/users
/orders/101
/api/v1/customers
```

❌ Invalid:

```
users
orders/101
```

👉 Without `/`, it is **not a valid absolute path**.

---

## 3️⃣ Absolute Path vs Relative Path (Important Concept)

### Absolute Path ✅

```http
/users
```

* Starts from root of the server
* Independent of current location
* REST APIs always use **absolute paths**

---

### Relative Path ❌

```http
users
```

* Relative to current path
* Ambiguous
* NOT used in REST endpoint definitions

---

## 4️⃣ How Spring Interprets `@RequestMapping("/users")`

Spring maps the request like this:

```
Incoming HTTP request:
GET /users
```

Spring sees:

```java
@RequestMapping("/users")
```

✔️ Match found → method executed

---

### What if you write this?

```java
@RequestMapping("users") // no slash
```

Spring internally **normalizes it** to:

```
/users
```

⚠️ But:

* It is **not recommended**
* Looks unprofessional
* Interviewers may flag it

---

## 5️⃣ REST Perspective (Not Just Spring)

REST APIs are **resource-oriented**.

Resources exist in a **hierarchical structure**, starting from root `/`.

Example:

```
/users
/users/101
/users/101/orders
```

👉 `/` represents the **root of all resources**

---

## 6️⃣ Why REST Resources Are Hierarchical

Because real-world data is hierarchical:

* User → Orders
* Order → Items
* Item → Payments

Which maps naturally to:

```
/users/{id}/orders
/orders/{id}/items
```

This hierarchy **must start from `/`**

---

## 7️⃣ One-Line Interview Answer ⭐

> REST resource paths start with `/` because they represent absolute URI paths as per HTTP standards, starting from the root of the server.

---

## 8️⃣ Very Common Interview Trap

❓ *Is `/` specific to Spring?*
❌ No

❓ *Is it mandatory in Java method names?*
❌ No

❓ *Is it mandatory in REST URLs?*
✅ Yes (URI path rule)

---

## 9️⃣ Mental Model (Remember Forever)

> `/` = root of server
> everything in REST grows **from root → resource → sub-resource**

---

## 🔁 Quick Comparison

| Concept      | Example             |
| ------------ | ------------------- |
| Root         | `/`                 |
| Resource     | `/users`            |
| Sub-resource | `/users/101`        |
| Nested       | `/users/101/orders` |

---

## 🧠 Final Summary

* `/` is not decoration
* It represents **absolute path**
* Mandatory as per HTTP/URI rules
* Ensures clarity, consistency, and correctness
* Spring follows HTTP standards

---

If you want next (high-value topics):

* Why plural resources (`/users`) are preferred
* `/users/{id}` vs `/user?id=`
* REST URI design mistakes interviewers catch
* How API Gateway uses `/` paths internally

Just tell me 👍
```
/ is for Absolute path, I got but if user passed @GetMapping("/users") vs @GetMapping("users") for both the cases what spring will understand what is the path and @GetMapping("users") is allowed or not?
```
## 🔑 Short, Precise Answer (Interview-Ready)

> In Spring MVC / Spring Boot, **both `@GetMapping("/users")` and `@GetMapping("users")` are allowed**, and Spring will interpret **both as the same absolute path `/users`**.
> However, **`"/users"` is the recommended and professional way**.

---

## 1️⃣ What Spring Actually Does Internally

Spring **normalizes mapping paths**.

When Spring starts:

* It reads all `@RequestMapping`, `@GetMapping`, etc.
* It **adds a leading `/` if missing**
* Then registers the endpoint

### Internally:

```java
@GetMapping("users")
```

➡️ Spring converts it to:

```
/users
```

Same as:

```java
@GetMapping("/users")
```

---

## 2️⃣ Proof by Example (How Requests Are Matched)

Incoming HTTP request:

```
GET /users
```

Spring mapping table internally:

```
/users  → Controller method
```

Both annotations match:

```java
@GetMapping("/users")
@GetMapping("users")
```

✔️ Result: **Method is called**

---

## 3️⃣ Why Does Spring Allow This?

Because Spring is **developer-friendly**:

* Avoids breaking apps due to small mistakes
* Ensures backward compatibility
* Auto-corrects path format

---

## 4️⃣ But Then Why Everyone Uses `/users`?

Because of **clarity, standards, and team discipline**.

### Problems with `"users"` (no slash):

| Issue                | Explanation                   |
| -------------------- | ----------------------------- |
| Not self-explanatory | Looks like relative path      |
| Confuses beginners   | REST paths are absolute       |
| Fails code review    | Considered sloppy             |
| Interview red flag   | Shows weak REST understanding |

---

## 5️⃣ Class-Level + Method-Level Mapping (IMPORTANT)

```java
@RestController
@RequestMapping("/api")
public class UserController {

    @GetMapping("users")
    public List<User> getUsers() {}
}
```

### Spring combines paths as:

```
/api + /users → /api/users
```

If method had `/users`:

```
/api + /users → /api/users
```

Same result again.

---

## 6️⃣ Edge Case: Double Slash

```java
@RequestMapping("/api/")
@GetMapping("/users")
```

Spring normalizes:

```
/api/users
```

✔️ No issue

---

## 7️⃣ Is `"users"` Ever Recommended?

❌ No (for REST APIs)

✔️ Allowed
❌ Recommended
❌ Best practice

---

## 8️⃣ One-Line Interview Answer ⭐

> Spring allows both `@GetMapping("users")` and `@GetMapping("/users")`, and internally normalizes them to the same absolute path. However, using `"/users"` is recommended as it clearly represents an absolute REST resource path.

---

## 9️⃣ Final Rule to Remember

> **Spring is flexible, REST design should not be.**

---

## 🧠 Final Summary

* `"users"` → allowed
* `"/users"` → best practice
* Both resolve to `/users`
* Always use `/users` in production code

---

If you want next:

* Path normalization rules in Spring
* Trailing slash (`/users/`) behavior
* Ambiguous mapping errors
* PathVariable vs RequestParam

Just say 👍

Got it 👍
I’ll explain **ALL the 4 topics one-by-one**, **deep but clean**, exactly like **interview + real project clarity**.
No skipping, no shortcuts.

---

# 1️⃣ Path Normalization Rules in Spring

![Image](https://vkuzel.com/storage/article/18-05/request_mapping_on_demand_graph.png)

![Image](https://i2.wp.com/springframework.guru/wp-content/uploads/2017/09/mvc.png)

![Image](https://terasolunaorg.github.io/guideline/5.2.0.RELEASE/en/_images/RequestLifecycle.png)

### What is Path Normalization?

Path normalization means **Spring internally cleans and standardizes URLs** before matching them to controller methods.

### What Spring Normalizes:

| Input You Write      | Spring Converts Internally |
| -------------------- | -------------------------- |
| `"users"`            | `/users`                   |
| `"/users"`           | `/users`                   |
| `"//users"`          | `/users`                   |
| `"/api//users"`      | `/api/users`               |
| `"/api/" + "/users"` | `/api/users`               |

### Why Spring Does This?

* Avoid duplicate mappings
* Avoid developer mistakes
* Ensure consistent request matching

### Interview Line ⭐

> Spring normalizes request mapping paths by adding missing slashes and removing redundant slashes before registering handler mappings.

---

# 2️⃣ Trailing Slash (`/users` vs `/users/`)

This is **VERY IMPORTANT** and often misunderstood.

### Case 1: Default Spring Boot Behavior

```java
@GetMapping("/users")
public List<User> getUsers() {}
```

#### Incoming Requests:

| Request   | Result             |
| --------- | ------------------ |
| `/users`  | ✅ Works            |
| `/users/` | ❌ 404 (by default) |

Why?

* Spring treats `/users` and `/users/` as **different paths**
* REST considers **resource identity strict**

---

### Case 2: Allow Trailing Slash (Optional Config)

```properties
spring.mvc.trailing-slash.matching=true
```

Now:

| Request   | Result |
| --------- | ------ |
| `/users`  | ✅      |
| `/users/` | ✅      |

⚠️ **But REST best practice:**
👉 Prefer **one canonical URL** (no trailing slash).

### Interview Line ⭐

> By default, Spring treats trailing slashes as different paths. REST APIs usually avoid trailing slashes to maintain a single canonical resource URL.

---

# 3️⃣ Ambiguous Mapping Errors (Real Production Issue)

### What Is Ambiguous Mapping?

When **Spring cannot decide which controller method should handle a request**.

### ❌ Example (Wrong)

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable String id) {}

@GetMapping("/users/{name}")
public User getUserByName(@PathVariable String name) {}
```

❌ Spring Error:

```
Ambiguous handler methods mapped for '/users/{...}'
```

Why?

* `{id}` and `{name}` are both `String`
* Same path pattern

---

### ✅ Correct Solutions

#### Option 1: Different Paths

```java
@GetMapping("/users/id/{id}")
@GetMapping("/users/name/{name}")
```

#### Option 2: Regex Constraint

```java
@GetMapping("/users/{id:\\d+}")
```

---

### Interview Line ⭐

> Ambiguous mapping occurs when multiple controller methods match the same request path. Spring fails fast during startup to avoid runtime uncertainty.

---

# 4️⃣ `@PathVariable` vs `@RequestParam` (Most Asked)

## 🔹 `@PathVariable`

Used when the value **IDENTIFIES the resource**.

```java
GET /users/101
```

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {}
```

✔️ Resource-centric
✔️ REST-recommended

---

## 🔹 `@RequestParam`

Used for **filters, options, metadata**.

```java
GET /users?role=admin&page=1
```

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam String role,
    @RequestParam int page
) {}
```

✔️ Query-based
✔️ Optional parameters

---

### Side-by-Side Comparison

| Feature    | PathVariable      | RequestParam    |
| ---------- | ----------------- | --------------- |
| Purpose    | Identify resource | Filter/control  |
| Mandatory  | Usually yes       | Often optional  |
| REST style | Strong            | Secondary       |
| URL        | `/users/101`      | `/users?page=1` |

---

### Interview Line ⭐

> Path variables identify a specific resource, while request parameters are used for filtering, sorting, or pagination.

---

# 🔥 Final Mental Model (Lock This)

```
/users           → Resource collection
/users/101       → Specific resource
/users?role=HR   → Filtered view
```

---

# 🧠 Final Summary (Revision Ready)

* Spring auto-normalizes paths
* `"users"` and `"/users"` → same internally
* Trailing slash is strict by default
* Ambiguous mappings fail at startup
* PathVariable = identity
* RequestParam = filtering

---

If you want next (even deeper):

* REST URI naming rules (plural vs singular)
* Why GET must be idempotent
* PUT vs PATCH confusion
* REST API design interview traps (real questions)

```
1. Difference between URI and URL in context of REST API
2. Which HTTP status codes is used at which condition (eg. when input validation failed, when business validation failed etc.)
3. Deep Dive Rest API Request and Response structure?
```

# 1️⃣ URI vs URL (In REST API Context)

![Image](https://prateekvjoshi.com/wp-content/uploads/2014/02/uri-vs-url-vs-urn.jpg)

![Image](https://www.hostinger.com/tutorials/wp-content/uploads/sites/2/2024/05/url-vs-uri.jpg)

![Image](https://www.hostinger.com/ph/tutorials/wp-content/uploads/sites/2/2022/07/the-structure-of-a-url.png)

## 🔹 Simple Definitions

### URI (Uniform Resource Identifier)

> Identifies **a resource**

### URL (Uniform Resource Locator)

> Identifies **a resource AND how to access it**

---

## 🔹 Relationship Between URI & URL

```
All URLs are URIs
BUT
Not all URIs are URLs
```

---

## 🔹 REST Context (Most Important)

In REST:

* **Resource is identified by URI**
* **Client accesses resource using URL**

### Example:

```
https://api.myapp.com/users/101
```

| Part                              | Meaning                       |
| --------------------------------- | ----------------------------- |
| `/users/101`                      | **URI (resource identifier)** |
| `https://api.myapp.com/users/101` | **URL (access location)**     |

---

## 🔹 Interview One-Liner ⭐

> In REST, a URI identifies the resource, while a URL specifies the location and protocol used to access that resource.

---

## 🔹 Why REST Talks About URI More Than URL?

Because REST is **resource-centric**, not transport-centric.

* REST design → URI
* Network access → URL

---

# 2️⃣ HTTP Status Codes — Which Code When?

![Image](https://restfulapi.net/wp-content/uploads/HTTP-Error-Codes.jpg)

![Image](https://cdn.prod.website-files.com/62796ab9647626cbab663f42/671124657e500c1aa0737fe2_6711242c69ef42275511fbb9_API%2520response%2520code%2520categories%2520%284%29.webp)

![Image](https://josipmisko.com/img/rest-api/http-status-code-vs-error-code.webp)

This is **VERY IMPORTANT for interviews**.

---

## 🔹 Status Code Categories

| Range | Meaning      |
| ----- | ------------ |
| 2xx   | Success      |
| 3xx   | Redirection  |
| 4xx   | Client error |
| 5xx   | Server error |

---

## 🔥 Most Asked Scenarios (REAL LIFE)

---

## ✅ 1. Successful Requests

| Scenario                 | Status             |
| ------------------------ | ------------------ |
| GET success              | **200 OK**         |
| POST success             | **201 Created**    |
| DELETE success (no body) | **204 No Content** |

---

## ❌ 2. Input Validation Failed (Client mistake)

### Example:

* Missing required field
* Invalid email format
* String length exceeded

```json
{
  "email": "invalidEmail"
}
```

### ✅ Correct Status:

**400 Bad Request**

📌 Why?

* Request structure or data is invalid

### Interview Line ⭐

> Input validation errors should return 400 Bad Request because the client sent invalid data.

---

## ❌ 3. Business Validation Failed (Rules violated)

### Example:

* Insufficient balance
* User already exists
* Order cannot be cancelled after shipment

### Correct Options:

| Status                       | When to Use                            |
| ---------------------------- | -------------------------------------- |
| **409 Conflict**             | State conflict                         |
| **422 Unprocessable Entity** | Valid request but business rule failed |

📌 Preferred in REST:
**409 Conflict**

### Interview Line ⭐

> Business validation failures usually return 409 Conflict because the request conflicts with the current state of the resource.

---

## ❌ 4. Authentication & Authorization

| Scenario                      | Status               |
| ----------------------------- | -------------------- |
| Not logged in / invalid token | **401 Unauthorized** |
| Logged in but no permission   | **403 Forbidden**    |

---

## ❌ 5. Resource Not Found

| Scenario               | Status            |
| ---------------------- | ----------------- |
| User ID does not exist | **404 Not Found** |

---

## ❌ 6. Server Failures

| Scenario                  | Status                        |
| ------------------------- | ----------------------------- |
| Null pointer / DB down    | **500 Internal Server Error** |
| Downstream service failed | **502 Bad Gateway**           |
| Service unavailable       | **503 Service Unavailable**   |

---

## 🔥 Golden Interview Mapping Table

| Situation             | Status |
| --------------------- | ------ |
| Input validation fail | 400    |
| Business rule fail    | 409    |
| Unauthorized          | 401    |
| Forbidden             | 403    |
| Resource not found    | 404    |
| Success create        | 201    |
| Server crash          | 500    |

---

# 3️⃣ Deep Dive: REST API Request & Response Structure

![Image](https://requestly.com/wp-content/uploads/2023/07/pika-1704454485908-2x.svg)

![Image](https://www.researchgate.net/publication/369336081/figure/fig3/AS%3A11431281129405338%401679524682508/REST-API-request-response-flow.jpg)

![Image](https://docs.trafficserver.apache.org/en/7.1.x/_images/http_header_struct.jpg)

---

## 🔹 REST API Request Structure

### 1️⃣ Request Line

```
POST /users HTTP/1.1
```

| Part     | Meaning      |
| -------- | ------------ |
| POST     | HTTP method  |
| /users   | Resource URI |
| HTTP/1.1 | Protocol     |

---

### 2️⃣ Headers (Metadata)

```http
Content-Type: application/json
Authorization: Bearer eyJhbGci...
Accept: application/json
```

| Header        | Purpose                  |
| ------------- | ------------------------ |
| Content-Type  | Request body format      |
| Accept        | Expected response format |
| Authorization | Security token           |

---

### 3️⃣ Request Body (Optional)

Used in:

* POST
* PUT
* PATCH

```json
{
  "name": "Danish",
  "email": "danish@gmail.com"
}
```

---

## 🔹 REST API Response Structure

### 1️⃣ Status Line

```
HTTP/1.1 201 Created
```

---

### 2️⃣ Response Headers

```http
Content-Type: application/json
Cache-Control: no-cache
```

---

### 3️⃣ Response Body

```json
{
  "id": 101,
  "name": "Danish"
}
```

---

## 🔹 Standard Error Response (Best Practice)

```json
{
  "timestamp": "2025-01-01T10:15:30",
  "status": 400,
  "error": "Bad Request",
  "message": "Email format is invalid",
  "path": "/users"
}
```

---

## 🔥 Request–Response Lifecycle (Step-by-Step)

1. Client sends HTTP request
2. API Gateway / Filter / Security
3. Controller
4. Service (business logic)
5. Repository / DB
6. Response created
7. HTTP status + body returned

---

## 🧠 Final Interview-Ready Summary

* URI identifies **resource**
* URL identifies **location**
* Input validation → 400
* Business validation → 409
* REST request = method + URI + headers + body
* REST response = status + headers + body
* Proper status codes = professional API

---

If you want next (high-value topics):

* PUT vs PATCH (deep confusion resolver)
* Idempotency in REST
* Pagination & sorting design
* REST API versioning strategies
* REST vs GraphQL (interview POV)

```
when we send request then how request line and headers are setting set as part of the request because as a request body i am using the resource url and body if request but no where using rl and headers...so how it is getting set?
```
# Big Picture (Answer in One Line)

> **The request line and headers are NOT written by you manually in code most of the time; they are automatically constructed by the HTTP client (browser, Postman, frontend library, or Java HTTP client) based on how you make the request.**

Now let’s **open the black box**.

---

## 1️⃣ Who Actually Creates the HTTP Request?

You **never talk to the server directly**.

There is always an **HTTP client** in between:

| You Use                       | Actual HTTP Client       |
| ----------------------------- | ------------------------ |
| Browser                       | Browser’s HTTP engine    |
| Postman                       | Postman HTTP client      |
| Frontend (fetch/axios)        | Browser networking stack |
| Java (RestTemplate/WebClient) | Java HTTP client         |

👉 **HTTP client creates the request line & headers**

---

## 2️⃣ Example: Browser / Postman (No Code)

When you enter in Postman:

* URL: `http://localhost:8080/users`
* Method: `POST`
* Body: JSON

You think you sent only:

```
URL + Body
```

But **Postman actually sends this full HTTP request 👇**

```
POST /users HTTP/1.1           ← Request Line
Host: localhost:8080           ← Header (mandatory)
Content-Type: application/json
Accept: application/json
User-Agent: PostmanRuntime/7.36
Content-Length: 45

{
  "name": "Danish"
}
```

✔️ You didn’t write it
✔️ HTTP client generated it

---

## 3️⃣ How Request Line Is Formed

### You provide:

```
POST http://localhost:8080/users
```

### HTTP client splits it:

| Part     | Comes From              |
| -------- | ----------------------- |
| POST     | Method you selected     |
| /users   | Path extracted from URL |
| HTTP/1.1 | Default protocol        |

👉 **This becomes the request line**

```
POST /users HTTP/1.1
```

---

## 4️⃣ How Headers Are Set Automatically

### Some headers are **mandatory by HTTP spec**

| Header         | Who sets it |
| -------------- | ----------- |
| Host           | HTTP client |
| Content-Length | HTTP client |
| User-Agent     | HTTP client |

---

### Some headers are **set by you (implicitly or explicitly)**

#### Example (Postman / Browser):

* You choose **JSON body**
* HTTP client auto adds:

```
Content-Type: application/json
```

---

### Some headers are **added by application logic**

| Header        | Source                |
| ------------- | --------------------- |
| Authorization | Frontend / API client |
| Accept        | Client preference     |
| Cookie        | Browser               |

---

## 5️⃣ Frontend Code Example (fetch API)

```js
fetch("http://localhost:8080/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer xyz"
  },
  body: JSON.stringify({
    name: "Danish"
  })
});
```

### Browser converts this to:

```
POST /users HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Authorization: Bearer xyz
Content-Length: 35
```

✔️ Browser creates request line
✔️ Browser creates headers

---

## 6️⃣ Java Side Example (Spring Client)

### Code You Write:

```java
restTemplate.postForObject(
    "http://localhost:8080/users",
    requestBody,
    User.class
);
```

### Java HTTP Client Sends:

```
POST /users HTTP/1.1
Host: localhost:8080
Content-Type: application/json
```

✔️ Java HTTP client builds it

---

## 7️⃣ What About Spring Controller? (Server Side)

Spring **does NOT create request line or headers**.

Spring:

* Receives **raw HTTP request**
* Parses it using **Servlet container (Tomcat)**
* Maps it to controller method

### Example:

```java
@PostMapping("/users")
public User createUser(
    @RequestHeader("Authorization") String token,
    @RequestBody User user
) {}
```

| Annotation     | Reads From   |
| -------------- | ------------ |
| @RequestHeader | HTTP headers |
| @RequestBody   | HTTP body    |
| @PathVariable  | URI          |
| @RequestParam  | Query string |

---

## 8️⃣ Mental Model (Remember Forever)

```
YOU → HTTP CLIENT → NETWORK → SERVER
```

* You provide **intent**
* HTTP client creates **protocol-compliant request**
* Server parses it

---

## 9️⃣ Why This Design Exists (Important Insight)

If developers had to manually write:

```
POST /users HTTP/1.1
Host: localhost
Content-Length: ...
```

➡️ Development would be **error-prone and painful**

So HTTP clients **abstract protocol details**

---

## 🔥 One-Line Interview Answer

> Request line and headers are automatically constructed by the HTTP client based on the URL, HTTP method, body, and configuration provided by the user.

---

## 🧠 Final Summary

* You don’t manually write request line
* HTTP client builds it
* Headers come from:

  * HTTP spec
  * Client defaults
  * Your configuration
* Spring only **reads** request

---

If you want next:

* How Tomcat parses HTTP internally
* Why Content-Length is important
* Chunked transfer encoding
* How filters & interceptors read headers

Just say **next** 👍
Perfect 👍
Now I’ll explain **ALL the 4 topics you said “all” for**, **one by one**, **deep but practical**, exactly at **HTTP + Spring Boot internal level**.
This will **close the black box completely**.

---

# 1️⃣ How Tomcat Parses HTTP Request Internally

![Image](https://tomcat.apache.org/tomcat-9.0-doc/architecture/requestProcess/41_basic.png)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20250205142106489023/State-of-sevlet-life-cycle.webp)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AY8U7vrFsKYbywdUIMoRUyw.png)

## 🔹 What Actually Happens When Request Reaches Server?

When a request hits your Spring Boot app, **Spring does NOT see raw bytes directly**.

### Flow:

```
Client → Network → Tomcat → Servlet API → Spring MVC
```

---

## 🔹 Step-by-Step Internals

### Step 1: Raw Bytes Arrive

Tomcat receives raw TCP data:

```
POST /users HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Content-Length: 35
```

---

### Step 2: Tomcat HTTP Parser

Tomcat:

* Reads request line
* Extracts:

  * Method → `POST`
  * URI → `/users`
  * Protocol → `HTTP/1.1`
* Reads headers into a **map-like structure**
* Reads body using `Content-Length`

---

### Step 3: Servlet Request Object Creation

Tomcat converts everything into:

```java
HttpServletRequest
```

This object contains:

* request.getMethod()
* request.getRequestURI()
* request.getHeader("Authorization")
* request.getInputStream()

---

### Step 4: Spring Uses Servlet API

Spring MVC **wraps** `HttpServletRequest` and:

* Maps `/users` → controller
* Converts JSON body → Java object
* Injects headers into parameters

---

### Interview Line ⭐

> Tomcat parses raw HTTP bytes and converts them into a HttpServletRequest, which Spring MVC uses for request mapping and data binding.

---

# 2️⃣ Why `Content-Length` Is Important

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20191024100905/1031.png)

![Image](https://mdn.github.io/shared-assets/images/diagrams/http/messages/request-headers.svg)

![Image](https://v1.richardhnguyen.com/static/f82be20b3e331959809c69586c609ed7/546f3/content-length-versus-chunked-transfer-encoding-thumbnail.png)

## 🔹 What Is Content-Length?

```
Content-Length: 35
```

👉 Tells server **how many bytes to read from body**

---

## 🔹 Why Server Needs It?

Without Content-Length:

* Server doesn’t know where request body ends
* Next request may get mixed
* Connection breaks

---

## 🔹 What Happens If It’s Missing?

| Case                   | Result         |
| ---------------------- | -------------- |
| Content-Length present | Normal         |
| Chunked encoding       | Works          |
| Both missing           | ❌ Server error |

---

## 🔹 Who Sets Content-Length?

* HTTP client
* Automatically calculated
* NEVER manually set by developer

---

### Interview Line ⭐

> Content-Length tells the server how many bytes to read from the request body, ensuring proper request parsing.

---

# 3️⃣ Chunked Transfer Encoding (Advanced but Important)

![Image](https://sahansera.dev/static/5716de2818d51f9c882d46d6471a115e/5a190/understanding-chunked-transfer-encoding-3.png)

![Image](https://bunnyacademy.b-cdn.net/vgGCR-What-Is-HTTP-Chunked-Encoding.png)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20191029112629/1091.png)

## 🔹 What Is Chunked Encoding?

Used when:

* Request size is unknown
* Streaming large data
* File upload
* Real-time data

### Header:

```
Transfer-Encoding: chunked
```

---

## 🔹 How It Works

Instead of Content-Length:

```
4\r\n
Wiki\r\n
5\r\n
pedia\r\n
0\r\n
```

* Each chunk has size
* Ends with `0`

---

## 🔹 Why Needed?

| Scenario      | Reason       |
| ------------- | ------------ |
| Streaming     | Size unknown |
| Large uploads | Memory safe  |
| Live data     | Continuous   |

---

## 🔹 Spring & Tomcat Handling

* Tomcat automatically assembles chunks
* Spring sees **normal InputStream**
* Developer doesn’t worry

---

### Interview Line ⭐

> Chunked transfer encoding allows sending data in chunks when the total size is not known upfront.

---

# 4️⃣ How Filters & Interceptors Read Headers (Very Important)

![Image](https://www.baeldung.com/wp-content/uploads/2021/05/filters_vs_interceptors.jpg)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AgT3YBFy1ODso2n6-Whbi7g.jpeg)

![Image](https://i.sstatic.net/qGjte.jpg)

---

## 🔹 Execution Order (REAL FLOW)

```
Client
 ↓
Servlet Filter
 ↓
Spring Interceptor
 ↓
Controller
 ↓
Service
 ↓
Response
```

---

## 🔹 Servlet Filter (Low Level)

* Part of Servlet API
* Executes **before Spring**
* Used by Spring Security

```java
request.getHeader("Authorization");
```

Use cases:

* Authentication
* Logging
* CORS

---

## 🔹 Spring Interceptor (Spring Level)

* Works with Spring MVC
* Executes **before & after controller**

```java
preHandle()
postHandle()
afterCompletion()
```

Use cases:

* Request logging
* User context
* Performance tracking

---

## 🔹 Controller (Final Stage)

```java
@RequestHeader("Authorization") String token
```

Spring injects header value automatically.

---

### Interview Line ⭐

> Filters work at servlet level, interceptors work at Spring MVC level, and both can read HTTP headers from the request.

---

# 🔥 Final End-to-End Mental Model (Lock This)

```
Client
 ↓
HTTP Client builds request
 ↓
Tomcat parses HTTP
 ↓
HttpServletRequest created
 ↓
Filter
 ↓
Interceptor
 ↓
Controller
```

---

# 🧠 Final Summary (Interview-Ready)

* HTTP client creates request line & headers
* Tomcat parses raw bytes
* Content-Length tells body size
* Chunked encoding used when size unknown (multimedia, image, video, bulk data)
* Filters run before Spring
* Interceptors run inside Spring

---

If you want next (even deeper):

* How Spring converts JSON → Object
* MessageConverters deep dive
* How exceptions become HTTP responses
* How Spring Security intercepts requests

Just say **next** 👍

Excellent topic 👍
This is a **classic interview + API design question** and often used to judge **REST maturity**.

Below is a **clean, professional, rule-based explanation** you can **directly convert into notes**.

---

# REST URI Naming Rules (Plural vs Singular)

![Image](https://miro.medium.com/1%2AXZ_C8CgA3v9HCZFLsQiyMQ.png)

![Image](https://cdn.prod.website-files.com/6193387d9bbc32538eaa3d6d/638eda99fd83795ba077b8a5_nEuYRyQRaFDd33f4sLAleu66RIdwY3802yX_LFGJUITtoHQRid_67DHdyffXhNM2o_0GkVKXZvAcbq8zidZbOlzeHkmwaDjyhiuTbQ58tR_H8Y2aacf635ogxRFf-68yWJmQYVpOx77AF__Ry_iwUbyJ6rIUOrb6rpY_lM9XvJpOpUsJpTMhS9ckqOLxSQ.png)

![Image](https://blog.xapihub.io/img/posts/HierarchyandResourceRelationshipsinRESTfulAPIs.png)

---

## 1️⃣ Core REST Principle (Foundation)

> **URI represents a resource, not an action**

* Resource = **noun**
* URI should never describe an operation

---

## 2️⃣ Why Plural Nouns Are Preferred ⭐

### ✅ Recommended (Plural)

```http
/users
/orders
/products
```

### ❌ Not Recommended (Singular)

```http
/user
/order
/product
```

---

### 🔍 Reason #1: Represents a Collection

```http
/users
```

Means:

> Collection of user resources

From this collection:

```http
/users/101
```

Means:

> One specific user from users collection

👉 This hierarchy **naturally fits plural naming**

---

### 🔍 Reason #2: HTTP Method Already Defines Action

| HTTP Method | URI          |
| ----------- | ------------ |
| GET         | `/users`     |
| POST        | `/users`     |
| GET         | `/users/101` |
| DELETE      | `/users/101` |

Plural noun stays **consistent** across operations.

---

### 🔍 Reason #3: Avoids Confusion

If singular:

```http
GET /user
GET /user/101
```

Confusing:

* Is `/user` one user?
* Or all users?

Plural removes ambiguity.

---

## 3️⃣ Collection vs Single Resource (Golden Rule)

| Resource Type   | URI           |
| --------------- | ------------- |
| Collection      | `/users`      |
| Single resource | `/users/{id}` |

---

### Interview Line ⭐

> Plural nouns are preferred in REST URIs because they represent resource collections, while individual resources are accessed using identifiers.

---

## 4️⃣ Sub-Resources (Hierarchy Matters)

### Example:

```http
/users/101/orders
/users/101/orders/5001
```

Interpretation:

* User **101**
* Has many **orders**
* Order **5001** belongs to that user

👉 Plural naming scales well with nesting.

---

## 5️⃣ When Singular Can Be Used (Rare & Special)

### ✅ Acceptable Singular Cases

#### 1️⃣ Singleton Resources

A resource that **exists only once per context**

Examples:

```http
/profile
/settings
/health
/status
```

Why singular?

* There is **only one instance**
* No collection exists

---

#### 2️⃣ Auth / Utility Endpoints (Not Pure REST)

```http
/login
/logout
/register
```

⚠️ These are **actions**, not resources
But widely accepted in real projects.

---

## 6️⃣ Mixed Naming — Why It’s Bad ❌

```http
/users
/user/{id}
```

Problems:

* Inconsistent
* Confusing
* Fails API review

---

## 7️⃣ Plural vs Singular — Comparison Table

| Aspect             | Plural          | Singular   |
| ------------------ | --------------- | ---------- |
| REST Style         | ✅ Best practice | ⚠️ Limited |
| Collection clarity | Clear           | Ambiguous  |
| Scalability        | High            | Low        |
| Interview safe     | Yes             | Risky      |

---

## 8️⃣ Best Practice Naming Rules (Summary)

✅ Use **plural nouns** for resources
✅ Use **lowercase**
✅ Use **hyphens** if needed (`order-items`)
❌ Avoid verbs
❌ Avoid file extensions (`.json`)
❌ Avoid mixed singular/plural

---

## 9️⃣ One-Paragraph Interview Answer ⭐

> In REST APIs, URIs should represent resources and are typically named using plural nouns to indicate collections, such as `/users` or `/orders`. Individual resources are accessed using identifiers like `/users/{id}`. Plural naming avoids ambiguity, aligns naturally with HTTP methods, and provides a scalable and consistent API structure. Singular nouns are used only for singleton resources like `/profile` or `/health`.

---

## 🔐 Memory Hook (Never Forget)

```
Collection → plural
Single item → plural + id
```

```
/users
/users/101
```

---

If you want next:

* REST URI design mistakes interviewers catch
* Why verbs in URI are wrong
* HATEOAS explained simply
* REST vs GraphQL URI style

Just say **next** 👍

Perfect 👍
This is a **very high-value interview topic**. I’ll do a **true deep dive** — concept, rules, edge cases, real REST design, Spring behavior, and interview traps.

---

# `@PathVariable` vs `@RequestParam` — Deep Dive (REST + Spring Boot)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AFrQu4KhCItxRmVs68ZHcfA.png)

![Image](https://substackcdn.com/image/fetch/%24s_%21WkMN%21%2Cf_auto%2Cq_auto%3Agood%2Cfl_progressive%3Asteep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F97f90aae-dbb0-4752-a421-84d0284cb8b0_640x465.webp)

![Image](https://www.theserverside.com/rms/editorial/020619_TSS_RESTful-URL_half_column_mobile.png)

---

## 1️⃣ Big Picture (1-Line Difference)

> **`@PathVariable` identifies a resource**,
> **`@RequestParam` modifies or filters a resource representation**.

If you remember only ONE line, remember this.

---

## 2️⃣ Conceptual Meaning (MOST IMPORTANT)

### 🔹 `@PathVariable` → **WHO / WHICH resource**

### 🔹 `@RequestParam` → **HOW / UNDER WHAT CONDITIONS**

---

## 3️⃣ `@PathVariable` — Deep Understanding

### What It Represents

* A **mandatory part of the URI**
* Defines **resource identity**
* Without it, resource **cannot be uniquely identified**

### Example

```http
GET /users/101
```

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
}
```

### Interpretation

> Give me **user whose ID is 101**

---

### Key Properties

| Property            | Value |
| ------------------- | ----- |
| Part of URI         | ✅     |
| Mandatory           | ✅     |
| Identifies resource | ✅     |
| REST-preferred      | ✅     |
| Cache friendly      | ✅     |

---

### Multiple Path Variables

```http
GET /users/101/orders/5001
```

```java
@GetMapping("/users/{userId}/orders/{orderId}")
public Order getOrder(
    @PathVariable Long userId,
    @PathVariable Long orderId
) {}
```

👉 This expresses **resource hierarchy**
(User → Orders → Order)

---

## 4️⃣ `@RequestParam` — Deep Understanding

### What It Represents

* **Optional parameters**
* Used for:

  * Filtering
  * Sorting
  * Pagination
  * Searching
  * Flags

### Example

```http
GET /users?role=ADMIN&page=1&size=10
```

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam String role,
    @RequestParam int page,
    @RequestParam int size
) {}
```

### Interpretation

> Give me **users**, filtered by role, paginated

---

### Key Properties

| Property            | Value                |
| ------------------- | -------------------- |
| Part of URI         | ❌ (query string)     |
| Mandatory           | ❌ (usually optional) |
| Identifies resource | ❌                    |
| Filters/modifies    | ✅                    |
| Cache friendly      | ⚠️ Depends           |

---

## 5️⃣ Mandatory vs Optional Behavior (VERY IMPORTANT)

### `@PathVariable`

```java
@GetMapping("/users/{id}")
```

* MUST be present
* Otherwise → **404 Not Found**

---

### `@RequestParam`

```java
@RequestParam(required = false)
```

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(required = false) String role
) {}
```

* Optional
* Missing param does NOT break request

---

## 6️⃣ Default Values in `@RequestParam`

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size
) {}
```

This is **NOT possible** with `@PathVariable`.

---

## 7️⃣ REST Semantics (INTERVIEW GOLD)

### ❌ Wrong REST Design

```http
GET /users?id=101
```

Why wrong?

* ID is **resource identity**
* Should be in path, not query

---

### ✅ Correct REST Design

```http
GET /users/101
```

---

## 8️⃣ When to Use Which (Decision Table)

| Scenario                 | Use             |
| ------------------------ | --------------- |
| Fetch specific resource  | `@PathVariable` |
| Update specific resource | `@PathVariable` |
| Delete specific resource | `@PathVariable` |
| Pagination               | `@RequestParam` |
| Sorting                  | `@RequestParam` |
| Searching                | `@RequestParam` |
| Optional filters         | `@RequestParam` |

---

## 9️⃣ HTTP Method Interaction (IMPORTANT)

### GET

* PathVariable → **identity**
* RequestParam → **filters**

### POST

```http
POST /users?notify=true
```

```java
@RequestParam boolean notify
```

✔️ Action flag, not identity

---

### PUT / PATCH

```http
PUT /users/101
```

```java
@PathVariable Long id
```

✔️ Updating specific resource

---

## 🔟 Error Handling Difference

### Missing PathVariable

```
GET /users/
→ 404 Not Found
```

### Missing RequestParam

```
GET /users
→ 200 OK
```

(if `required=false`)

---

## 1️⃣1️⃣ Can Both Be Used Together? (YES)

```http
GET /users/101/orders?status=DELIVERED
```

```java
@GetMapping("/users/{id}/orders")
public List<Order> getOrders(
    @PathVariable Long id,
    @RequestParam String status
) {}
```

This is **perfect REST design**.

---

## 1️⃣2️⃣ Common Interview Traps ❌

| Mistake                         | Why Wrong               |
| ------------------------------- | ----------------------- |
| Using RequestParam for ID       | Breaks REST semantics   |
| Using PathVariable for filter   | Wrong resource modeling |
| Making PathVariable optional    | Conceptually wrong      |
| Putting actions in PathVariable | REST anti-pattern       |

---

## 1️⃣3️⃣ One-Paragraph Interview Answer ⭐

> Path variables are used to identify a specific resource and form part of the URI, such as `/users/{id}`, whereas request parameters are used to filter, sort, paginate, or control the representation of a resource and are passed as query parameters. Path variables are mandatory and define resource identity, while request parameters are optional and modify the response without changing the resource identity.

---

## 🔐 Final Memory Lock (Never Forget)

```
@PathVariable  → Identity (WHO)
@RequestParam → Conditions (HOW)
```

```
/users/101            → PathVariable
/users?page=1&size=10 → RequestParam
```

---

If you want next (very logical continuation):

* PUT vs PATCH (deep confusion solver)
* Idempotency in REST
* Pagination & sorting best practices
* REST interview trick questions

Just say **next** 👍

Perfect 👍
Below is **the complete logical continuation**, explained **one-by-one**, **deep but clean**, exactly at **REST + interview + real-project level**.
This will **finish REST fundamentals end-to-end**.

---

# 1️⃣ PUT vs PATCH — Deep Dive (Most Confusing Topic)

![Image](https://d8it4huxumps7.cloudfront.net/bites/wp-content/banners/2025/7/6878d7d1e0dd1_kais_banner__fi__creatives___9_.jpg)

![Image](https://javacodehouse.com/assets/img/thumb/PUT-vs-PATCH.svg)

![Image](https://s3-us-west-2.amazonaws.com/assertible/blog/swagger-petstore-endpoint-methods.png)

## 🔹 Core Difference (One Line)

> **PUT replaces the entire resource**,
> **PATCH partially updates the resource**.

---

## 🔹 PUT (Full Replacement)

### Meaning

* Client sends **complete representation**
* Missing fields → **removed / overwritten**

### Example

```http
PUT /users/101
```

```json
{
  "name": "Danish",
  "email": "danish@gmail.com"
}
```

### What Server Does

* Deletes old user state
* Creates new state from request

---

### PUT Characteristics

| Property       | Value   |
| -------------- | ------- |
| Update type    | Full    |
| Idempotent     | ✅       |
| Safe           | ❌       |
| Missing fields | Removed |

---

## 🔹 PATCH (Partial Update)

### Meaning

* Client sends **only changed fields**
* Rest remains untouched

### Example

```http
PATCH /users/101
```

```json
{
  "email": "new@gmail.com"
}
```

---

### PATCH Characteristics

| Property       | Value      |
| -------------- | ---------- |
| Update type    | Partial    |
| Idempotent     | ⚠️ Depends |
| Safe           | ❌          |
| Missing fields | Preserved  |

---

## 🔹 PUT vs PATCH — Interview Table ⭐

| Aspect       | PUT              | PATCH   |
| ------------ | ---------------- | ------- |
| Update scope | Full             | Partial |
| Payload size | Large            | Small   |
| Risk         | High (overwrite) | Low     |
| Usage        | Rare             | Common  |

---

### Interview Line ⭐

> PUT replaces the complete resource representation, while PATCH updates only specific fields.

---

# 2️⃣ Idempotency in REST (VERY IMPORTANT)

![Image](https://nordicapis.com/wp-content/uploads/Understanding-Idempotency-and-Safety-in-API-Design.png)

![Image](https://www.theserverside.com/rms/onlineimages/http_methods_unsafe_vs_idempotent-h_half_column_mobile.png)

## 🔹 What is Idempotency?

> **Calling the same request multiple times produces the same result**.

---

## 🔹 HTTP Methods & Idempotency

| Method | Idempotent |
| ------ | ---------- |
| GET    | ✅          |
| PUT    | ✅          |
| DELETE | ✅          |
| POST   | ❌          |
| PATCH  | ⚠️         |

---

### Example

```http
DELETE /users/101
```

* First call → deletes user
* Second call → user already deleted
* Final state = same

✔️ Idempotent

---

### POST Example (Non-Idempotent)

```http
POST /users
```

* Each call → new user created
* Final state changes

❌ Not idempotent

---

### Interview Line ⭐

> Idempotency ensures that repeated requests do not change the final state of the resource.

---

# 3️⃣ Pagination & Sorting — REST Best Practices

![Image](https://datakuity.com/wp-content/uploads/2022/06/image-2-915x576.png)

![Image](https://blog.moesif.com/images/posts/technical/api-design/Filtering-Sorting-and-Pagination.png)

![Image](https://cdn.prod.website-files.com/5e0b01877436086c8beecd1a/5ff9147f57384b1d24f082be_9hrt8jSN0oU4p4bdQ0nr8Mz4KtAPmW0mgP7CBozENT7brYLMC9-CyixZL0wrL8bqEQ_A09Ew1U6h9iug3kI39sbUMIlvwNAX28jw2LOI0RzVhQqh7BvdiL8WQaPSSP1PnN7v0M9g.png)

## 🔹 Why Pagination?

* Prevent large payloads
* Improve performance
* Avoid DB overload

---

## 🔹 Recommended Design

```http
GET /users?page=0&size=10&sort=name,asc
```

### Why `RequestParam`?

* Pagination does **not identify resource**
* It **modifies response view**

---

## 🔹 Spring Example

```java
@GetMapping("/users")
public List<User> getUsers(
    @RequestParam(defaultValue="0") int page,
    @RequestParam(defaultValue="10") int size,
    @RequestParam(defaultValue="id,asc") String sort
) {}
```

---

## 🔹 Pagination Response (Best Practice)

```json
{
  "data": [...],
  "page": 0,
  "size": 10,
  "totalElements": 250,
  "totalPages": 25
}
```

---

### Interview Line ⭐

> Pagination and sorting should be implemented using query parameters since they affect representation, not resource identity.

---

# 4️⃣ REST Interview Trick Questions (VERY IMPORTANT)

## ❓ Q1: Can GET have a body?

**Technically yes, practically NO.**

✔️ HTTP spec allows
❌ Servers ignore it
❌ REST discourages it

---

## ❓ Q2: Why GET should not change data?

Because:

* Caching
* Idempotency
* Browser prefetch
* Safe retry

---

## ❓ Q3: Is DELETE always physical delete?

❌ No
✔️ Can be **soft delete**

REST cares about **semantics**, not DB logic.

---

## ❓ Q4: Can POST be idempotent?

❌ By default
✔️ With idempotency keys (advanced systems)

---

## ❓ Q5: Why PATCH is risky?

* Partial updates
* Concurrency issues
* Requires validation logic

---

## 🔥 REST Golden Rules (Interview Favorite)

```
GET    → Read (safe, idempotent)
POST   → Create (non-idempotent)
PUT    → Replace (idempotent)
PATCH  → Modify (partial)
DELETE → Remove (idempotent)
```

---

## 🧠 FINAL MASTER SUMMARY (Lock This)

* PUT = full replace
* PATCH = partial update
* Idempotency = same result on retries
* Pagination = RequestParam
* REST is about **resource semantics**
* HTTP method defines **action**
* URI defines **identity**

---

If you want next (next natural level):

* REST API versioning strategies
* HATEOAS (explained simply)
* REST vs GraphQL (interview POV)
* Designing real microservice REST APIs

Just say **next** 👍
```
how put is idempotent but not patch?
in real projects are we using put in place of patch?
what is meant by http spec allows in case of GET? 
in security if we are sending token so is it not part of body?
```

## 1️⃣ How **PUT is idempotent** but **PATCH is not**?

![Image](https://media.licdn.com/dms/image/v2/D4D22AQEPN3YoPX11Xg/feedshare-shrink_2048_1536/feedshare-shrink_2048_1536/0/1726625452574?e=2147483647\&t=vevt68N0cqeWD8duLJhOgvBrcmdTnRtW4Xzbxg6IWQc\&v=beta)

![Image](https://nordicapis.com/wp-content/uploads/Understanding-Idempotency-and-Safety-in-API-Design.png)

![Image](https://d8it4huxumps7.cloudfront.net/bites/wp-content/banners/2025/7/6878d7d1e0dd1_kais_banner__fi__creatives___9_.jpg)

### 🔹 Definition Reminder

> **Idempotent** = calling the same request multiple times results in the **same final state**.

---

### ✅ PUT — Why Idempotent?

PUT means **FULL REPLACEMENT** of resource.

#### Example

```http
PUT /users/101
{
  "name": "Danish",
  "email": "dk@gmail.com",
  "age": 25
}
```

#### First call:

User becomes exactly this.

#### Second call (same request):

User becomes exactly this again.

➡️ **Final state is SAME**

✔️ Therefore, **PUT is idempotent**.

---

### ❌ PATCH — Why NOT guaranteed idempotent?

PATCH means **PARTIAL UPDATE**, and behavior depends on logic.

#### Example 1 (Non-idempotent)

```http
PATCH /users/101
{
  "loginCount": "+1"
}
```

Each call:

* loginCount increases

❌ Final state changes every time
❌ NOT idempotent

---

#### Example 2 (Idempotent PATCH)

```http
PATCH /users/101
{
  "email": "new@gmail.com"
}
```

This **can be idempotent**, BUT:

👉 HTTP spec **does NOT guarantee** idempotency for PATCH
👉 It depends on **business logic**

---

### 🔑 Final Rule (Interview Gold)

> PUT is **always idempotent by definition**,
> PATCH **may or may not be idempotent**, depending on implementation.

---

## 2️⃣ In Real Projects — Do We Use PUT or PATCH?

### 🔥 Real Industry Truth

✔️ **PATCH is used MORE than PUT**
❌ PUT is rarely used in real projects

---

### Why PATCH is Preferred in Real Projects

| Reason             | Explanation                 |
| ------------------ | --------------------------- |
| Safer              | No accidental overwrite     |
| Smaller payload    | Better performance          |
| UI driven          | Frontend updates few fields |
| Partial validation | Easier                      |

---

### When PUT is Used (Rare but Valid)

* Internal APIs
* Full object sync
* Admin bulk replacement
* Config management APIs

---

### Interview-Safe Answer ⭐

> In real-world applications, PATCH is more commonly used because updates are usually partial. PUT is used when the client can reliably send the complete resource representation.

---

## 3️⃣ What Does “HTTP Spec Allows GET Body” Mean?

This confuses many people — let’s fix it permanently.

---

### 🔹 HTTP Specification Reality

✔️ HTTP spec **does NOT forbid** GET having a body
❌ But it **does NOT define semantics** for it

Meaning:

* Spec says: *“I won’t stop you”*
* But also says: *“I won’t guarantee behavior”*

---

### Practical Reality

| Layer      | Behavior        |
| ---------- | --------------- |
| Browsers   | Ignore GET body |
| Proxies    | Drop it         |
| Servers    | Ignore it       |
| Frameworks | Not supported   |

---

### Example

```http
GET /users
{
  "role": "ADMIN"
}
```

This body will most likely:

* Be ignored
* Or dropped
* Or cause undefined behavior

---

### Correct Way

```http
GET /users?role=ADMIN
```

---

### Interview Line ⭐

> Although HTTP specification does not forbid a GET request body, it is not supported or standardized, so REST APIs should never rely on GET request bodies.

---

## 4️⃣ Security Token — Is It Part of Body?

### 🔥 Short Answer

> ❌ NO — **tokens should NOT be in body** (for REST APIs)

---

### Correct Way (Industry Standard)

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

📌 Token is sent in **HTTP HEADER**, not body.

---

### Why NOT in Body?

| Reason           | Explanation              |
| ---------------- | ------------------------ |
| Caching          | Headers handled properly |
| Security filters | Expect token in header   |
| Logging          | Body may be logged       |
| Standards        | OAuth2 / JWT spec        |

---

### When Token in Body Is Used (Rare / Legacy)

* Login endpoint:

```http
POST /login
{
  "username": "dk",
  "password": "123"
}
```

➡️ Token is **returned in response body**, not request body.

---

### Interview Line ⭐

> Authentication tokens are sent in HTTP headers, typically in the Authorization header, not in the request body.

---

## 🔥 Combined Interview Trap Question (with Answer)

**Q:** Why not send token in GET body since HTTP allows it?
**A:** Because GET bodies are not reliably supported and headers are the standardized, secure way to transmit authentication credentials.

---

## 🧠 Final Mental Lock (Never Forget)

```
PUT   → full replace → idempotent
PATCH → partial update → may change behavior
GET body → allowed by spec, ignored in practice
Token → header, NOT body
```

---

If you want next (advanced, interview-heavy):

* REST concurrency & ETag
* Optimistic locking with PATCH/PUT
* Why POST is non-idempotent
* REST security best practices (JWT flow)

Just say **next** 👍
Great question 👍
This is a **fundamental concept** and once you get it, **many REST + HTTP confusions automatically disappear**.

---

# What is meant by **HTTP Specs**?

![Image](https://personal.ntu.edu.sg/ehchua/programming/webprogramming/images/HTTP_Steps.png)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20210905094321/StructureOfAHTTPResponse-660x374.png)

![Image](https://miro.medium.com/v2/da%3Atrue/resize%3Afit%3A600/0%2Aih3RDKNrcls4hswU.gif)

---

## 1️⃣ Short & Accurate Meaning (Interview-Ready)

> **HTTP specs are the official written rules that define how the HTTP protocol must behave, written as RFC (Request For Comments) documents and maintained by standards bodies.**

In simple words:
➡️ **HTTP specs = rulebook of HTTP**

---

## 2️⃣ Who Writes HTTP Specs?

HTTP specs are written and maintained by:

* **IETF** (Internet Engineering Task Force)
* Published as **RFC documents**

Examples:

* RFC 7230 – Message syntax
* RFC 7231 – Semantics (GET, POST, PUT, etc.)
* RFC 9110 – Modern consolidated HTTP semantics

📌 These documents are **public, formal, and authoritative**.

---

## 3️⃣ Why Do HTTP Specs Exist?

Imagine if:

* Browser A behaves differently
* Server B interprets requests differently

➡️ Internet would break.

So HTTP specs ensure:

* A browser in India
* A server in the US
* A proxy in Europe

👉 **All speak the same language**

---

## 4️⃣ What Exactly Do HTTP Specs Define?

HTTP specs define **WHAT is allowed, WHAT is required, and WHAT is undefined**.

### They define rules for:

| Area               | Defined by HTTP Specs |
| ------------------ | --------------------- |
| Request line       | ✅                     |
| Headers            | ✅                     |
| Status codes       | ✅                     |
| HTTP methods       | ✅                     |
| Message body rules | ✅                     |
| Caching            | ✅                     |
| Idempotency        | ✅                     |
| Safety             | ✅                     |

---

## 5️⃣ Important Concept: **Allowed vs Defined vs Forbidden**

This is where most confusion comes from.

### 1️⃣ Allowed

➡️ Spec does not prohibit it

### 2️⃣ Defined

➡️ Spec clearly explains behavior

### 3️⃣ Forbidden

➡️ Spec explicitly disallows it

---

## 6️⃣ Example: GET Request Body (Your Earlier Doubt)

### What HTTP Spec Says:

* ❌ Does NOT forbid GET body
* ❌ Does NOT define meaning of GET body

👉 Meaning:

> Servers may ignore it, drop it, or behave unpredictably.

That’s why we say:

> **“HTTP spec allows GET body, but does not define its semantics.”**

---

## 7️⃣ Another Example: Idempotency

### HTTP Spec Defines:

* GET → Safe & idempotent
* PUT → Idempotent
* DELETE → Idempotent
* POST → Not idempotent
* PATCH → Semantics undefined (depends on implementation)

➡️ That’s why PATCH **may or may not** be idempotent.

---

## 8️⃣ Spec vs Framework (VERY IMPORTANT)

| HTTP Spec           | Spring / Browser    |
| ------------------- | ------------------- |
| Defines rules       | Implements rules    |
| Technology-agnostic | Technology-specific |
| Cannot change       | Can choose behavior |

### Example:

* HTTP spec allows GET body
* Spring **chooses not to support it**

✔️ Spring follows **safe + predictable behavior**

---

## 9️⃣ Why Interviewers Care About “HTTP Spec”?

Because it shows:

* You understand **standards**
* You don’t blindly follow frameworks
* You know **why** rules exist

---

## 🔥 One-Paragraph Interview Answer ⭐

> HTTP specifications are official standards published as RFC documents by the IETF that define how the HTTP protocol works, including request and response formats, methods, headers, status codes, and their semantics. They ensure consistent communication between clients and servers across the internet. Frameworks like Spring implement these specifications and may impose additional constraints for safety and consistency.

---

## 🧠 Memory Lock (Never Forget)

```
HTTP Spec = Rulebook
Framework = Implementation
```

```
Allowed ≠ Defined ≠ Recommended
```

---

## ✅ Final Takeaway

* HTTP specs are **not opinions**
* They are **formal standards**
* REST design is built **on top of HTTP specs**
* Knowing specs = senior-level understanding

---

If you want next (very powerful topics):

* HTTP safety vs idempotency (deep)
* Why POST is unsafe
* ETag & If-Match headers
* How REST handles concurrency

Just say **next** 👍
# Etag vs If-Match, How REST handles concurrency?
Excellent 👌
This is a **senior-level REST topic**. If you explain this well in interviews, it clearly shows **real production experience**.

I’ll explain in **3 clean layers**:

1. The **problem (concurrency)**
2. **ETag & If-Match** (solution)
3. **How REST handles concurrency end-to-end**

---

# 1️⃣ The Problem: Concurrency in REST APIs

![Image](https://www.mscharhag.com/files/2021/rest-concurrent-updates.jpg)

![Image](https://blog.4psa.com/wp-content/uploads/opt-cond-rest.png)

## 🔹 What is the concurrency problem?

> **Multiple clients try to update the same resource at the same time.**

### Example (Lost Update Problem)

1. User A fetches `/users/101`
2. User B fetches `/users/101`
3. User A updates name → saves
4. User B updates email → saves

🚨 **User A’s change is LOST**

This is called:

> **Lost update / write conflict**

---

## 2️⃣ How REST Solves This (High Level)

REST itself is **stateless**, so:

* Server does not track client sessions
* Client must prove it is updating the **latest version**

👉 REST uses **Optimistic Locking**

And the tool for that is:

> **ETag + If-Match**

---

# 2️⃣ What is an ETag?

![Image](https://ik.imagekit.io/taw2awb5ntf/wp-content/uploads/2021/04/http-etag.png)

![Image](https://community.sap.com/legacyfs/online/storage/blog_attachments/2023/05/et1.png)

![Image](https://sookocheff.com/post/api/optimistic-locking-in-a-rest-api/update_resource.png)

## 🔹 Definition

> **ETag (Entity Tag)** is a unique identifier representing the **current version** of a resource.

Think of it as:

* Version
* Hash
* Fingerprint of resource state

---

### Example Response

```http
GET /users/101
HTTP/1.1 200 OK
ETag: "v3"
```

```json
{
  "id": 101,
  "name": "Danish",
  "email": "dk@gmail.com"
}
```

👉 `"v3"` means:

> This is **version 3** of the user resource.

---

## 3️⃣ What is `If-Match`?

## 🔹 Definition

> **If-Match** is a request header used by the client to say:
> *“Update this resource ONLY if its current ETag matches what I last read.”*

---

### Example Update Request

```http
PUT /users/101
If-Match: "v3"
```

```json
{
  "name": "Danish Khan",
  "email": "dk@gmail.com"
}
```

---

## 4️⃣ ETag vs If-Match (Clear Difference)

| Header       | Direction | Purpose                           |
| ------------ | --------- | --------------------------------- |
| **ETag**     | Response  | Tells client current version      |
| **If-Match** | Request   | Ensures update is on same version |

👉 **ETag = server → client**
👉 **If-Match = client → server**

---

# 3️⃣ How REST Handles Concurrency (Step-by-Step)

![Image](https://systemdesignschool.io/blog/optimistic-locking/optimistic-locking-conflict.png)

![Image](https://codeopinion.com/wp-content/uploads/2021/12/3-2.png)

![Image](https://miro.medium.com/0%2A2e0rRYv2_joc9WwD.png)

### Step 1: Client reads resource

```http
GET /users/101
```

Server responds:

```http
ETag: "v3"
```

---

### Step 2: Another client updates resource

```http
PUT /users/101
If-Match: "v3"
```

Server updates → version becomes `"v4"`

---

### Step 3: First client tries to update with old version

```http
PUT /users/101
If-Match: "v3"
```

❌ Server detects mismatch
❌ Rejects request

Response:

```http
HTTP/1.1 412 Precondition Failed
```

---

## 🔥 This is concurrency control

> **Only clients working on the latest version can update the resource**

---

## 5️⃣ Why 412 Precondition Failed?

Because:

* `If-Match` is a **precondition**
* Condition failed → request rejected

### Interview Line ⭐

> REST APIs use ETag and If-Match headers for optimistic locking, returning 412 Precondition Failed when concurrent updates occur.

---

## 6️⃣ Why This Is Called *Optimistic Locking*

| Pessimistic   | Optimistic            |
| ------------- | --------------------- |
| Lock row      | No lock               |
| Blocks others | Allows parallel reads |
| Slower        | Faster & scalable     |

REST favors:

> **Optimistic locking** (web scale, stateless)

---

## 7️⃣ PUT vs PATCH with ETag

### Works with BOTH:

```http
PUT /users/101
PATCH /users/101
```

As long as:

```http
If-Match: "v3"
```

👉 ETag works **independent of HTTP method**

---

## 8️⃣ What If Client Doesn’t Send If-Match?

Two common server strategies:

| Strategy             | Behavior                              |
| -------------------- | ------------------------------------- |
| Lenient              | Update anyway                         |
| Strict (recommended) | Reject with 428 Precondition Required |

```http
HTTP/1.1 428 Precondition Required
```

---

## 9️⃣ Strong vs Weak ETags (Bonus, Interview+)

```http
ETag: "v3"     → Strong
ETag: W/"v3"   → Weak
```

| Type   | Meaning                                  |
| ------ | ---------------------------------------- |
| Strong | Exact match required                     |
| Weak   | Semantically same but not byte-identical |

Concurrency control uses:

> **Strong ETags**

---

## 🔥 Real Project Reality

* **High-quality APIs** → use ETag + If-Match
* **Microservices** → common in public APIs
* **Spring Boot** → often implemented via DB `version` field

Example:

```java
@Version
private Long version;
```

Mapped to:

```http
ETag: "v5"
```

---

## 🧠 Final Mental Model (Never Forget)

```
ETag     → version from server
If-Match → version from client
Mismatch → 412 error
```

---

## 🎯 One-Paragraph Interview Answer ⭐

> REST handles concurrency using optimistic locking with ETag and If-Match headers. The server returns an ETag representing the current version of a resource, and the client includes this value in the If-Match header during updates. If the resource has been modified by another client, the ETag will differ and the server rejects the request with a 412 Precondition Failed response, preventing lost updates.

---

## ✅ Final Takeaway

* REST is stateless → client participates in concurrency
* ETag = version
* If-Match = safety check
* 412 = concurrency conflict
* This scales extremely well

---

If you want next (natural continuation):

* Optimistic vs pessimistic locking (DB vs REST)
* ETag vs version field
* Retry strategies after 412
* How PATCH + ETag works in practice

Just say **next** 👍
```
so this Etag vs If-Match is by default enabled or we have to enable it for REST api?
```
## 🔑 Short, Interview-Ready Answer

> **ETag / If-Match concurrency control is NOT enabled by default in REST APIs.**
> You must **explicitly implement and enforce it** in your API (framework may provide helpers, but it’s not automatic).

Now let’s open this properly 👇

---

## 1️⃣ Is ETag / If-Match enabled by default?

### ❌ NO — by default

* REST itself does **not auto-enable** concurrency control
* HTTP **defines the headers**, but:

  * Server must **generate ETag**
  * Server must **check If-Match**
  * Server must **reject stale updates**

👉 All this is **application responsibility**

---

## 2️⃣ Why HTTP Doesn’t Enable It Automatically

Because HTTP is:

* **Generic**
* **Transport-level**
* **Stateless**

HTTP spec says:

> “Here are headers (ETag, If-Match).
> Use them if you need concurrency control.”

It does **not** say:

> “Every update must use ETag.”

---

## 3️⃣ What Happens by Default (Without ETag)

### Typical default behavior:

```
Client A reads resource
Client B reads resource
Client A updates
Client B updates
```

❌ Last write wins
❌ No conflict detection
❌ Lost update problem

This is the **default** unless you add protection.

---

## 4️⃣ Who Is Responsible for Enabling It?

| Layer              | Responsibility         |
| ------------------ | ---------------------- |
| HTTP Spec          | Defines headers        |
| Framework (Spring) | Provides support       |
| **Your REST API**  | **Must enforce logic** |

👉 **YOU** decide:

* Whether to generate ETag
* Whether to require If-Match
* What to do on mismatch

---

## 5️⃣ Spring Boot — Default Behavior

### ❌ By default, Spring Boot:

* Does **NOT** generate ETag automatically
* Does **NOT** reject updates without If-Match
* Does **NOT** protect from concurrent updates

Spring will happily do this:

```http
PUT /users/101
```

even if the resource is stale.

---

## 6️⃣ How ETag Is Commonly Implemented (Real Projects)

![Image](https://community.sap.com/legacyfs/online/storage/blog_attachments/2023/05/et1.png)

![Image](https://enterprisecraftsmanship.com/images/2017/2017-09-18-1.png)

![Image](https://sookocheff.com/post/api/optimistic-locking-in-a-rest-api/update_resource.png)

### ✅ Typical real-world setup

#### 1️⃣ Database has a version field

```java
@Version
private Long version;
```

#### 2️⃣ Server sends ETag

```http
ETag: "v5"
```

#### 3️⃣ Client sends If-Match on update

```http
If-Match: "v5"
```

#### 4️⃣ Server compares

* Match → update allowed
* Mismatch → ❌ reject

```http
412 Precondition Failed
```

---

## 7️⃣ Can Frameworks Help?

### ✔️ Yes, but still NOT automatic

#### Spring tools that help:

* `@Version` (JPA optimistic locking)
* `ResponseEntity.eTag()`
* Filters / interceptors
* API Gateway policies

But:

> You still must **connect the dots**.

---

## 8️⃣ What About Public APIs (Strict Mode)?

High-quality APIs do this:

| Scenario                 | Response                    |
| ------------------------ | --------------------------- |
| No If-Match on PUT/PATCH | `428 Precondition Required` |
| Stale If-Match           | `412 Precondition Failed`   |

This is **explicit enforcement**, not default behavior.

---

## 9️⃣ Why Many Internal APIs Don’t Use ETag

Reality check:

| Reason                             |
| ---------------------------------- |
| Single UI client                   |
| Low concurrency                    |
| DB optimistic locking already used |
| Time constraints                   |

So they rely on:

* DB `@Version`
* Last-write-wins

But **public APIs & distributed systems SHOULD use ETag**.

---

## 🔥 One-Paragraph Interview Answer ⭐

> ETag and If-Match based concurrency control is not enabled by default in REST APIs. HTTP specifications define these headers, but the server application must explicitly generate ETags, require If-Match headers on update requests, and reject stale updates. Frameworks like Spring provide support utilities, but enforcing optimistic locking at the REST layer is an application-level responsibility.

---

## 🧠 Final Memory Lock

```
HTTP defines ETag
Framework supports ETag
Application ENFORCES ETag
```

```
Default REST → No concurrency protection
Explicit ETag → Safe updates
```

---

If you want next (very strong continuation):

* DB optimistic locking vs REST ETag (difference)
* Why DB @Version alone is not enough
* Retry strategy after 412
* API Gateway based concurrency enforcement

Just say **next** 👍
```
so we have to use @version annotation on the entity?
can you give me the example how to enable and use eTag and if-match
DB optimistic locking vs REST ETag (difference)
Why DB @Version alone is not enough
```
# 1️⃣ Do we have to use `@Version` annotation on the entity?

## 🔑 Short Answer

> **Yes, in most Spring + JPA projects, `@Version` is the foundation for optimistic locking**, and it is commonly used to back REST ETag logic — **but `@Version` alone does NOT enable REST concurrency control**.

---

## 🔹 What `@Version` Does (DB Level)

```java
@Entity
public class User {

    @Id
    private Long id;

    private String name;

    private String email;

    @Version
    private Long version;
}
```

### What happens internally:

* JPA adds `version` column in DB
* On UPDATE:

  ```sql
  UPDATE users 
  SET name=?, email=?, version=version+1 
  WHERE id=? AND version=?
  ```
* If version mismatches → **OptimisticLockException**

📌 This protects **DB consistency**, not REST clients.

---

## 🔥 Important Interview Point

> `@Version` protects the database from concurrent writes, but REST clients are still unaware of version changes unless exposed via ETag.

---

# 2️⃣ How to Enable and Use **ETag + If-Match** (Step-by-Step)

This is the **most important section**.

---

## 🔹 Step 1: Use `@Version` in Entity (DB safety)

```java
@Version
private Long version;
```

---

## 🔹 Step 2: Expose ETag in GET Response

```java
@GetMapping("/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {

    User user = userService.getUser(id);

    String eTag = "\"v" + user.getVersion() + "\"";

    return ResponseEntity.ok()
            .eTag(eTag)
            .body(user);
}
```

### Response:

```http
200 OK
ETag: "v5"
```

Client now knows:

> This is version 5 of the resource

---

## 🔹 Step 3: Require `If-Match` in UPDATE

```java
@PutMapping("/users/{id}")
public ResponseEntity<User> updateUser(
        @PathVariable Long id,
        @RequestHeader("If-Match") String ifMatch,
        @RequestBody User updatedUser) {

    User existing = userService.getUser(id);

    String currentETag = "\"v" + existing.getVersion() + "\"";

    if (!currentETag.equals(ifMatch)) {
        return ResponseEntity.status(HttpStatus.PRECONDITION_FAILED).build();
    }

    User saved = userService.update(id, updatedUser);
    return ResponseEntity.ok(saved);
}
```

---

## 🔹 Step 4: Client Flow (Very Important)

### Client reads:

```http
GET /users/101
ETag: "v5"
```

### Client updates:

```http
PUT /users/101
If-Match: "v5"
```

### If resource changed meanwhile:

```http
412 Precondition Failed
```

✅ **Concurrency safely handled**

---

## 🔥 Optional (Strict Mode – Best Practice)

Reject updates without `If-Match`:

```java
if (ifMatch == null) {
    return ResponseEntity.status(428).build(); // Precondition Required
}
```

---

# 3️⃣ DB Optimistic Locking vs REST ETag (CLEAR DIFFERENCE)

![Image](https://codeopinion.com/wp-content/uploads/2021/12/3-2.png)

![Image](https://miro.medium.com/1%2AgH0fDaTwSOg3IWX9Svu-Rw.png)

![Image](https://systemdesignschool.io/blog/optimistic-locking/optimistic-locking-conflict.png)

| Aspect                | DB `@Version` | REST ETag      |
| --------------------- | ------------- | -------------- |
| Scope                 | Database      | HTTP / API     |
| Protects              | DB rows       | Client updates |
| Client aware?         | ❌ No          | ✅ Yes          |
| Prevents lost updates | ⚠️ Partially  | ✅ Fully        |
| Works across services | ❌ No          | ✅ Yes          |
| REST compliant        | ❌             | ✅              |

---

## 🔑 Key Insight (Interview Gold)

> DB optimistic locking prevents concurrent writes at the database level, while REST ETag prevents stale updates at the API boundary.

---

# 4️⃣ Why `@Version` Alone Is NOT Enough

This is **very frequently asked**.

---

## 🔴 Problem Scenario (Without ETag)

1. Client A reads user (version 5)
2. Client B reads user (version 5)
3. Client A updates → version becomes 6
4. Client B updates → version becomes 7

❌ **Client A’s update is lost**
❌ DB is consistent, but **business state is wrong**

Why?

* DB sees updates as valid
* Client never told DB which version it edited

---

## 🔴 What `@Version` Protects vs What It Doesn’t

| Protects                   | Doesn’t Protect       |
| -------------------------- | --------------------- |
| Concurrent DB updates      | Stale client updates  |
| Same transaction conflicts | API-level concurrency |
| Single service             | Distributed systems   |

---

## 🔥 REST Perspective (Important)

REST is:

* Stateless
* Distributed
* Multi-client

So:

> **Concurrency must be handled at the API boundary, not just in the database**

---

# 🧠 Final Mental Model (Never Forget)

```
@Version → DB safety
ETag     → API safety
If-Match → Client responsibility
```

```
DB locking ≠ REST concurrency control
```

---

# 🎯 One-Paragraph Interview Answer ⭐

> While `@Version` provides optimistic locking at the database level, it does not prevent stale updates from REST clients. To handle concurrency properly in REST APIs, the server must expose the resource version using ETag headers and require clients to include that version in If-Match headers during updates. This ensures that only clients working on the latest resource state can modify it, preventing lost updates in distributed systems.

---

## ✅ Final Takeaway

* Yes, use `@Version`
* But **never stop there**
* ETag + If-Match = real REST concurrency
* DB locking alone is insufficient in distributed APIs

---

If you want next (very advanced, but powerful):

* Retry strategies after 412
* PATCH + ETag best practices
* API Gateway enforcing If-Match
* How big APIs (Amazon/GitHub) do this

Just say **next** 👍
```
so you are saying if we are using only @version at entity then it provides db locking?
```
Yes — **exactly that** 👍
But let me phrase it **precisely**, because this sentence is very important in interviews.

---

## 🔑 Correct, Precise Statement

> **Using `@Version` on an entity provides optimistic locking at the DATABASE level, not at the REST/API level.**

Now let’s **lock this understanding permanently**.

---

## 1️⃣ What `@Version` ACTUALLY Does

```java
@Entity
public class User {

    @Id
    private Long id;

    private String name;

    @Version
    private Long version;
}
```

### What JPA/Hibernate does internally

When you update an entity, Hibernate generates SQL like:

```sql
UPDATE users
SET name = ?, version = version + 1
WHERE id = ? AND version = ?
```

### Two outcomes:

#### ✅ Version matches

* Row updated
* Version incremented

#### ❌ Version mismatch

* `0 rows updated`
* Hibernate throws `OptimisticLockException`

➡️ **This is DB-level optimistic locking**

---

## 2️⃣ Why This Is Called “Optimistic Locking”

* No lock is taken in advance
* Multiple transactions can read same row
* Conflict detected **only at commit time**

This is **NOT**:

* `SELECT ... FOR UPDATE`
* No blocking
* No DB lock held

---

## 3️⃣ What `@Version` Protects (And What It Doesn’t)

### ✅ What it DOES protect

| Protection                    | Yes/No |
| ----------------------------- | ------ |
| Concurrent DB updates         | ✅      |
| Two threads updating same row | ✅      |
| Transaction-level consistency | ✅      |

---

### ❌ What it DOES NOT protect

| Scenario                  | Protected? |
| ------------------------- | ---------- |
| Stale REST client updates | ❌          |
| Multiple users via API    | ❌          |
| Distributed microservices | ❌          |
| API retry conflicts       | ❌          |

---

## 4️⃣ Common Misunderstanding (Very Important)

Many developers think:

> “I have `@Version`, so concurrency is handled.”

🚫 **This is incorrect at REST level.**

### Why?

Because:

* REST clients do **not know** the version
* Clients do **not send** the version back
* DB only sees final update attempt

---

## 5️⃣ Real Problem Example (WITHOUT ETag)

1. Client A fetches user (version 5)
2. Client B fetches user (version 5)
3. Client A updates → version becomes 6
4. Client B updates → version becomes 7

❌ No DB error
❌ No optimistic lock exception
❌ Client A’s update silently lost

👉 **DB is consistent, business logic is not**

---

## 6️⃣ Why DB Optimistic Locking Alone Is Insufficient for REST

Because REST is:

* Stateless
* Multi-client
* Distributed

Concurrency must be:

> **Explicit and visible at the API boundary**

---

## 7️⃣ Proper Layered Concurrency Strategy (BEST PRACTICE)

```
DB Layer        → @Version (data safety)
REST Layer      → ETag + If-Match (API safety)
Client          → Sends If-Match
```

All three together = **robust concurrency control**

---

## 🔥 Interview-Perfect One-Liner

> `@Version` provides optimistic locking at the database level by preventing concurrent updates within transactions, but it does not protect REST APIs from stale client updates unless combined with ETag and If-Match headers.

---

## 🧠 Final Memory Lock

```
@Version ≠ REST concurrency
@Version = DB optimistic locking
```

---

## ✅ Final Answer to Your Question

> **Yes. Using only `@Version` provides database-level optimistic locking, not REST-level concurrency control.**

---

If you want next (natural follow-ups):

* When DB optimistic locking DOES throw exception
* How REST ETag maps to DB version
* How to handle OptimisticLockException globally
* Retry vs reject strategies

Just say **next** 👍
```
but in this example when client a updates then version becomes 6 and when b will update it will check version is 5 or not but it is 6 so it will not? my understanding is wrong?
UPDATE users
SET name = ?, version = version + 1
WHERE id = ? AND version = ?
```
Great catch 👌
**Your understanding is logically correct**, and you’ve pinpointed the **exact place where most explanations become misleading**.
Let me **resolve this cleanly**, and also **correct the earlier “WITHOUT ETag” example with the missing nuance**.

---

## Short Answer (Very Important)

> **Your understanding is NOT wrong.**
> If the **version value that Client B sends to the database is still `5`**, then the update **WILL FAIL** with optimistic locking.
>
> **BUT** in most REST applications, **Client B never sends the version at all**, and that’s why the update succeeds and overwrites data.

The difference is **WHERE the version comes from**.

---

## Let’s Break This Precisely

You quoted the SQL:

```sql
UPDATE users
SET name = ?, version = version + 1
WHERE id = ? AND version = ?
```

This SQL is **100% correct**.

Now the key question is:

> ❓ **What value is used for `version = ?` in the WHERE clause?**

That depends on **how your REST update is implemented**.

---

## Case 1️⃣ (Your Understanding – CORRECT CASE)

### When the client **sends the version back**

Flow:

1. Client A GET → version = 5
2. Client B GET → version = 5
3. Client A UPDATE (version=5) → DB version becomes 6
4. Client B UPDATE (still version=5)

Hibernate executes:

```sql
UPDATE users
SET ..., version = version + 1
WHERE id = 101 AND version = 5
```

👉 No rows updated
👉 Hibernate throws `OptimisticLockException`
👉 **Client B fails**

✅ **Your understanding is 100% correct here**

---

## Case 2️⃣ (What Happens in MOST REST APIs – THE REAL PROBLEM)

This is the **important missing detail**.

### Typical REST update code (very common)

```java
@PutMapping("/users/{id}")
public User update(@PathVariable Long id,
                   @RequestBody UserDto dto) {

    User user = repository.findById(id).get(); // version = 6 (latest)
    user.setName(dto.getName());
    return repository.save(user);
}
```

### What happened?

* Client B **never sent version**
* Server loaded **latest entity** from DB
* Hibernate used **version = 6**, not 5

So SQL becomes:

```sql
UPDATE users
SET name=?, version=version+1
WHERE id=101 AND version=6
```

✔️ Update succeeds
✔️ Version becomes 7
❌ Client A’s change may be overwritten

---

## 🔥 THIS Is the Real Problem (Key Insight)

> **`@Version` only protects the database if the client’s version participates in the update.**

In most REST APIs:

* DTO does NOT contain version
* Client does NOT send version
* Server always loads latest entity
* Optimistic locking **never triggers**

---

## Why ETag + If-Match Fixes This

ETag forces this rule:

> “You are only allowed to update the resource version you originally read.”

With ETag:

1. Client B read version 5 → ETag: "v5"
2. Client A updates → version becomes 6
3. Client B sends:

   ```http
   If-Match: "v5"
   ```
4. Server compares:

   ```
   current = v6
   if-match = v5 ❌
   ```
5. Server rejects request **BEFORE DB UPDATE**

```http
412 Precondition Failed
```

---

## Why @Version Alone Is Often Not Enough (Now Clear)

| Scenario                     | Result                      |
| ---------------------------- | --------------------------- |
| Client sends version         | DB optimistic locking works |
| Client does NOT send version | Lost update possible        |
| REST without ETag            | Client version ignored      |
| REST with ETag               | Client version enforced     |

---

## Corrected & Precise Statement (Interview-Ready)

> `@Version` provides optimistic locking at the database level, but in REST APIs it only works if the client’s version is included in the update. Since most REST APIs load the latest entity on the server, stale client updates can still overwrite data unless ETag and If-Match are enforced at the API boundary.

---

## Mental Model (Lock This)

```
@Version works ONLY if version participates in UPDATE
REST APIs usually hide version → problem
ETag exposes version → solution
```

---

## Final Answer to Your Question

> **Your understanding is correct.**
> The confusion arises because in most REST APIs, the client’s version never reaches the database. Hibernate then uses the latest version, so the update succeeds. That’s why DB optimistic locking alone is not sufficient for REST concurrency control.

If you want, next we can:

* Rewrite a **bad REST update → correct REST update**
* Show **DTO with version vs ETag approach**
* Discuss **why ETag is preferred over version in body**

Just say 👍
Great question 👍
This is a **core REST design concept** and very common in interviews.

---

# What is meant by **REST API Versioning**?

![Image](https://wojtek.cloud/content/images/size/w960/2022/12/Untitled-Diagram.drawio-1-.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AnD99Zq9vUV4N6DDHH6eV5g.jpeg)

![Image](https://json-server.dev/_astro/rest-api-versioning.a6544e0b.png)

---

## 1️⃣ Simple & Accurate Meaning (Interview-Ready)

> **REST API versioning is the practice of managing changes to an API without breaking existing clients by exposing different versions of the API.**

In short:
➡️ **Change API safely without breaking users**

---

## 2️⃣ Why REST API Versioning is Needed (VERY IMPORTANT)

### Real-world problem

Imagine:

* Mobile app is using your API
* Frontend is deployed
* Third-party clients are integrated

Now you:

* Rename a field
* Remove a field
* Change response structure
* Change business logic

❌ Existing clients break

👉 **Versioning prevents this**

---

## 3️⃣ What Changes REQUIRE Versioning?

### 🚨 Breaking changes (must version)

* Removing a field
* Renaming a field
* Changing data type
* Changing response structure
* Changing validation rules
* Changing semantics

### ✅ Non-breaking changes (no version needed)

* Adding optional fields
* Performance improvements
* Internal refactoring

---

## 4️⃣ Common REST API Versioning Strategies

### 1️⃣ URI Versioning (Most Common & Interview-Safe)

```http
/api/v1/users
/api/v2/users
```

#### Pros

* Simple
* Visible
* Easy to route

#### Cons

* Pollutes URI

👉 **Most widely used in industry**

---

### 2️⃣ Header Versioning

```http
GET /users
Accept-Version: v1
```

or

```http
Accept: application/vnd.myapp.v1+json
```

#### Pros

* Clean URIs
* REST-pure

#### Cons

* Hard to debug
* Less visible

---

### 3️⃣ Query Parameter Versioning (Not Recommended)

```http
/users?version=1
```

❌ Avoid — mixes version with filtering

---

### 4️⃣ Media Type Versioning (Advanced / Enterprise)

```http
Accept: application/vnd.company.users.v1+json
```

✔️ Used by GitHub, Stripe
❌ Overkill for most systems

---

## 5️⃣ Which Versioning Strategy Should You Use?

### Interview-Safe Answer ⭐

> URI versioning (`/api/v1`) is the most commonly used approach because it is simple, explicit, and easy to maintain.

---

## 6️⃣ Versioning vs Resource Version (IMPORTANT CONFUSION)

❌ **API Versioning ≠ ETag / resource version**

| Concept         | Purpose              |
| --------------- | -------------------- |
| API Versioning  | Handle API evolution |
| ETag / If-Match | Handle concurrency   |
| DB @Version     | DB consistency       |

---

## 7️⃣ Example: Without Versioning (Bad)

```json
{
  "name": "Danish"
}
```

Later changed to:

```json
{
  "fullName": "Danish Khan"
}
```

❌ Old clients break

---

## 8️⃣ With Versioning (Correct)

### v1

```http
/api/v1/users/101
```

```json
{
  "name": "Danish"
}
```

### v2

```http
/api/v2/users/101
```

```json
{
  "fullName": "Danish Khan",
  "email": "dk@gmail.com"
}
```

✔️ Both clients work

---

## 9️⃣ When Should You Introduce a New Version?

Introduce a new version when:

* You cannot maintain backward compatibility
* Contract changes are unavoidable
* Old clients cannot be upgraded immediately

---

## 🔟 Best Practices for REST API Versioning

✅ Version only when needed
✅ Keep versions stable
✅ Deprecate old versions gradually
✅ Document version changes clearly
❌ Avoid frequent version bumps
❌ Don’t version for small changes

---

## 🔥 One-Paragraph Interview Answer ⭐

> REST API versioning is a strategy to evolve APIs without breaking existing clients by exposing multiple versions of the API. It is required when making breaking changes such as modifying response structures or business rules. Common versioning approaches include URI versioning, header-based versioning, and media-type versioning, with URI versioning being the most widely adopted due to its simplicity and clarity.

---

## 🧠 Memory Lock (Never Forget)

```
ETag        → concurrency
@Version    → DB safety
API version → contract change
```

---

If you want next (natural continuation):

* API versioning in Spring Boot (code example)
* When NOT to version an API
* How long to support old versions
* API deprecation strategies

Just say **next** 👍
Perfect 👍
Below is a **clean, production-style explanation of API versioning in Spring Boot with code examples**, exactly what interviewers and real projects expect.

I’ll cover **step-by-step**, starting from **most used → advanced**, and clearly tell **what to use in real life**.

---

# REST API Versioning in Spring Boot (Code Examples)

![Image](https://i.sstatic.net/IwYn7.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AdyfW0gtvR2owATn5E1uNnQ.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AzKU6TwXUaxbGSa7_dfIpEg.png)

---

## 1️⃣ URI Versioning (MOST COMMON & INTERVIEW-SAFE)

### ✅ Recommended for most projects

### URL Structure

```http
/api/v1/users
/api/v2/users
```

---

### Spring Boot Code

#### Version 1 Controller

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserControllerV1 {

    @GetMapping("/{id}")
    public UserV1 getUser(@PathVariable Long id) {
        return new UserV1(id, "Danish");
    }
}
```

```java
public class UserV1 {
    private Long id;
    private String name;
}
```

---

#### Version 2 Controller

```java
@RestController
@RequestMapping("/api/v2/users")
public class UserControllerV2 {

    @GetMapping("/{id}")
    public UserV2 getUser(@PathVariable Long id) {
        return new UserV2(id, "Danish Khan", "dk@gmail.com");
    }
}
```

```java
public class UserV2 {
    private Long id;
    private String fullName;
    private String email;
}
```

---

### ✅ Why This Is Preferred

* Easy to understand
* Visible in URL
* Easy routing in API Gateway
* Interview-friendly

### 🔥 Interview Line

> URI versioning is the most widely used approach because it is explicit, simple, and easy to manage.

---

## 2️⃣ Header Versioning (Cleaner but Less Visible)

### URL

```http
GET /api/users
```

### Header

```http
X-API-VERSION: 1
```

---

### Spring Boot Code

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping(value = "/{id}", headers = "X-API-VERSION=1")
    public UserV1 getUserV1(@PathVariable Long id) {
        return new UserV1(id, "Danish");
    }

    @GetMapping(value = "/{id}", headers = "X-API-VERSION=2")
    public UserV2 getUserV2(@PathVariable Long id) {
        return new UserV2(id, "Danish Khan", "dk@gmail.com");
    }
}
```

---

### Pros & Cons

| Pros       | Cons                |
| ---------- | ------------------- |
| Clean URLs | Hard to debug       |
| REST-pure  | Version not visible |
| Flexible   | Tooling complexity  |

---

## 3️⃣ Accept Header / Media Type Versioning (ADVANCED)

Used by **GitHub, Stripe, large public APIs**.

---

### Request

```http
GET /users/101
Accept: application/vnd.myapp.v1+json
```

---

### Spring Boot Code

```java
@GetMapping(
    value = "/users/{id}",
    produces = "application/vnd.myapp.v1+json"
)
public UserV1 getUserV1(@PathVariable Long id) {
    return new UserV1(id, "Danish");
}

@GetMapping(
    value = "/users/{id}",
    produces = "application/vnd.myapp.v2+json"
)
public UserV2 getUserV2(@PathVariable Long id) {
    return new UserV2(id, "Danish Khan", "dk@gmail.com");
}
```

---

### When to Use

* Public APIs
* Long-term backward compatibility
* External consumers

---

## 4️⃣ ❌ Query Parameter Versioning (NOT Recommended)

```http
/users?version=1
```

### Why Avoid

* Version ≠ filter
* Breaks REST semantics
* Hard to cache

---

## 5️⃣ Which Versioning Strategy Should YOU Use?

### Interview-Perfect Answer ⭐

> In Spring Boot applications, URI versioning (`/api/v1`) is the most commonly used approach due to its simplicity, clarity, and ease of routing. Header or media-type versioning is preferred for large public APIs requiring cleaner URLs.

---

## 6️⃣ Versioning Best Practices (VERY IMPORTANT)

✅ Version only when breaking changes occur
✅ Do NOT version for small changes
✅ Support old versions for some time
✅ Deprecate old versions gracefully
❌ Avoid frequent version bumps

---

## 7️⃣ Versioning vs ETag (Important Difference)

| Feature       | API Versioning | ETag        |
| ------------- | -------------- | ----------- |
| Purpose       | API evolution  | Concurrency |
| Scope         | Contract       | Resource    |
| Client impact | Major          | Minor       |
| Header based  | Optional       | Mandatory   |

---

## 8️⃣ Common Interview Traps ❌

* ❌ Versioning every small change
* ❌ Mixing versioning styles
* ❌ Removing old versions abruptly
* ❌ Confusing API version with resource version

---

## 🔥 One-Paragraph Interview Answer (FINAL)

> API versioning in Spring Boot is used to manage breaking changes without impacting existing clients. The most common approach is URI versioning using paths like `/api/v1` and `/api/v2`. Spring Boot supports versioning via URL paths, headers, or media types. URI versioning is preferred in most applications due to its simplicity and visibility, while header or media-type versioning is used in large public APIs.

---

## 🧠 Final Memory Lock

```
/api/v1 → contract change
ETag    → concurrency
@Version → DB safety
```

---

If you want next (natural follow-ups):

* API deprecation strategy
* How long to support v1
* Versioning with Swagger/OpenAPI
* Migrating clients from v1 → v2

Just say **next** 👍
```
so for point 2,3 how version will be sent in headers we have to add it manually?
and for 4 as we have to add manually as a query parameter?
```
Yes 👍 — **your understanding is correct**.
Let me confirm it **clearly**, then explain **who adds what, where, and how in real projects**.

---

## ✅ Short Direct Answer (Lock This First)

| Versioning Style                   | Is version sent manually? | Who sends it? |
| ---------------------------------- | ------------------------- | ------------- |
| **2️⃣ Header versioning**          | ✅ Yes                     | Client        |
| **3️⃣ Media-type (Accept header)** | ✅ Yes                     | Client        |
| **4️⃣ Query param versioning**     | ✅ Yes                     | Client        |

👉 **Server does NOT auto-add API version**
👉 **Client must explicitly send it in every request**

---

## 1️⃣ Header Versioning — How version is sent

### Example

```http
GET /api/users/101
X-API-VERSION: 2
```

### ✔️ Who adds this?

* Frontend (React / Angular)
* Mobile app
* Postman
* Any API consumer

### Spring side

```java
@GetMapping(value = "/users/{id}", headers = "X-API-VERSION=2")
public UserV2 getUserV2(@PathVariable Long id) { }
```

### 🔑 Important

* If header is missing → mapping **won’t match**
* Spring will return **404 / 406**

---

## 2️⃣ Media-Type (Accept Header) Versioning — How version is sent

### Example

```http
GET /users/101
Accept: application/vnd.myapp.v2+json
```

### ✔️ Who adds this?

* Client (explicitly)
* Often via HTTP client config

### Spring side

```java
@GetMapping(
  value = "/users/{id}",
  produces = "application/vnd.myapp.v2+json"
)
public UserV2 getUserV2(@PathVariable Long id) { }
```

### 🔑 Why client must send it?

Because:

* Server doesn’t know which version client expects
* Accept header = **content negotiation**

---

## 3️⃣ Query Parameter Versioning — How version is sent

### Example

```http
GET /users/101?version=2
```

### ✔️ Who adds this?

* Client (manually)

### Spring side

```java
@GetMapping("/users/{id}")
public User getUser(
    @PathVariable Long id,
    @RequestParam int version
) { }
```

### ❌ Why this is discouraged

* Version ≠ filter
* Hard to cache
* Easy to misuse

---

## 4️⃣ Why Server Never Auto-Adds Version

Because:

* HTTP is **request-driven**
* Server responds based on request metadata
* Server cannot assume client capability

👉 **Versioning is a contract chosen by the client**

---

## 5️⃣ Real Project Reality (Very Important)

### Internal APIs

* Mostly **URI versioning**
* No headers needed

### Public / External APIs

* Header or media-type versioning
* Client SDK sets headers automatically

Example (frontend):

```js
axios.get("/users/101", {
  headers: {
    "X-API-VERSION": "2"
  }
});
```

---

## 🔥 Interview-Perfect One-Liner

> In header, media-type, and query-parameter versioning, the API version must be explicitly sent by the client in every request; the server only routes the request based on the version information provided.

---

## 🧠 Final Memory Lock

```
URI versioning    → visible in URL
Header versioning → client sends header
Media type        → client sends Accept
Query param       → client sends ?version=
```

---

### ✅ Final Confirmation to Your Question

✔️ **Yes**, for point **2 & 3**, version must be **manually added in headers**
✔️ **Yes**, for point **4**, version must be **manually added as query param**

---

If you want next:

* How API Gateway handles versioning
* Default version fallback strategy
* How Swagger/OpenAPI documents versions
* Client backward-compatibility strategies

Just say **next** 👍
