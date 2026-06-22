## REST API Interview Preparation — Top 50 Questions with Examples

REST API is one of the most important topics for Java/Spring Boot backend interviews. As a 7-year Java backend developer, you should not only know definitions, but also explain **why we use REST, how to design APIs, how to secure them, and how to implement them in Spring Boot**.

---

# 1. What is REST API?

**REST** stands for **Representational State Transfer**.

A REST API is an architectural style used to expose backend resources over HTTP.

Example:

```http
GET /api/users/101
```

This means:

> Get the user resource whose id is 101.

In REST, everything is treated as a **resource**.

Examples of resources:

```text
/users
/orders
/products
/posts
/comments
```

---

# 2. What is the difference between REST and RESTful API?

**REST** is the architectural style.

**RESTful API** means an API that follows REST principles properly.

Example of RESTful API:

```http
GET    /users
GET    /users/10
POST   /users
PUT    /users/10
DELETE /users/10
```

Bad API design:

```http
GET /getUserById?id=10
POST /deleteUser
```

This is not clean REST style because actions are used in URL instead of HTTP methods.

---

# 3. What are the main principles of REST?

Important REST principles are:

1. Client-server architecture
2. Stateless communication
3. Resource-based URL
4. Use of HTTP methods
5. Uniform interface
6. Cacheable responses
7. Layered system

Example:

```http
GET /api/products/5
```

The client asks for product resource `5`, and server returns its representation, usually JSON.

---

# 4. What is a resource in REST?

A resource is any object/data that can be accessed through an API.

Examples:

```text
User
Product
Order
Post
Comment
Invoice
```

REST URL should represent resources:

```http
GET /api/orders/1001
```

Here, `orders` is the resource and `1001` is the order id.

---

# 5. What is statelessness in REST?

Stateless means the server does not store client session information between requests.

Every request must contain all required information.

Example:

```http
GET /api/profile
Authorization: Bearer jwt-token
```

The server does not remember the user from the previous request. It identifies the user from the token sent with this request.

**Interview explanation:**

> REST APIs are stateless because every request is independent. This improves scalability because any server instance can handle any request.

---

# 6. Why is statelessness important?

Statelessness helps in:

1. Scalability
2. Load balancing
3. Easy horizontal scaling
4. Better fault tolerance
5. Simpler server design

Example:

If you have 5 backend instances behind a load balancer, any request can go to any server because no server-specific session is required.

---

# 7. What are HTTP methods used in REST?

Common HTTP methods are:

| Method | Purpose                 |
| ------ | ----------------------- |
| GET    | Fetch data              |
| POST   | Create data             |
| PUT    | Update full resource    |
| PATCH  | Update partial resource |
| DELETE | Delete resource         |

Example:

```http
GET /api/users/1
POST /api/users
PUT /api/users/1
PATCH /api/users/1
DELETE /api/users/1
```

---

# 8. Difference between GET and POST?

| GET                                   | POST                      |
| ------------------------------------- | ------------------------- |
| Used to retrieve data                 | Used to create data       |
| Data usually sent in URL/query params | Data sent in request body |
| Should not modify server state        | Modifies server state     |
| Idempotent                            | Not always idempotent     |
| Can be cached                         | Usually not cached        |

Example:

```http
GET /api/users/10
```

```http
POST /api/users
Content-Type: application/json

{
  "name": "Danish",
  "email": "danish@example.com"
}
```

---

# 9. Difference between PUT and PATCH?

**PUT** updates the complete resource.

**PATCH** updates only selected fields.

PUT example:

```http
PUT /api/users/10

{
  "name": "Danish Khan",
  "email": "danish@example.com",
  "city": "Pune"
}
```

PATCH example:

```http
PATCH /api/users/10

{
  "city": "Amsterdam"
}
```

**Interview answer:**

> PUT is used for full replacement of a resource, while PATCH is used for partial modification.

---

# 10. Difference between POST and PUT?

| POST                        | PUT                          |
| --------------------------- | ---------------------------- |
| Creates a new resource      | Updates or replaces resource |
| Server usually generates ID | Client may know resource ID  |
| Not idempotent              | Idempotent                   |

Example:

```http
POST /api/users
```

Creates a new user.

```http
PUT /api/users/10
```

Updates user with id `10`.

---

# 11. What is idempotency?

Idempotency means calling the same API multiple times gives the same final result.

Idempotent methods:

```text
GET
PUT
DELETE
```

Usually non-idempotent:

```text
POST
```

Example:

```http
DELETE /api/users/10
```

Whether you call it once or five times, the final result is the same: user `10` is deleted.

---

# 12. Is DELETE always idempotent?

Conceptually, yes.

First call:

```http
DELETE /api/users/10
```

Response:

```http
204 No Content
```

Second call may return:

```http
404 Not Found
```

But the final state is still the same: user does not exist.

So DELETE is considered idempotent.

---

# 13. What are safe HTTP methods?

Safe methods do not modify server data.

Examples:

```text
GET
HEAD
OPTIONS
```

Example:

```http
GET /api/products
```

This should only read products, not create/update/delete anything.

---

# 14. What are common HTTP status codes?

| Status Code               | Meaning                          |
| ------------------------- | -------------------------------- |
| 200 OK                    | Request successful               |
| 201 Created               | Resource created                 |
| 204 No Content            | Success but no response body     |
| 400 Bad Request           | Invalid request                  |
| 401 Unauthorized          | Authentication required/failed   |
| 403 Forbidden             | No permission                    |
| 404 Not Found             | Resource not found               |
| 409 Conflict              | Duplicate/conflict               |
| 422 Unprocessable Entity  | Validation/business rule failure |
| 500 Internal Server Error | Server-side error                |

---

# 15. Difference between 401 and 403?

**401 Unauthorized** means user is not authenticated.

Example:

```http
GET /api/profile
```

Without token:

```http
401 Unauthorized
```

**403 Forbidden** means user is authenticated but does not have permission.

Example:

A normal user tries to access admin API:

```http
DELETE /api/admin/users/10
```

Response:

```http
403 Forbidden
```

---

# 16. Difference between 400 and 404?

**400 Bad Request** means the client sent invalid data.

Example:

```json
{
  "email": "invalid-email"
}
```

**404 Not Found** means requested resource does not exist.

Example:

```http
GET /api/users/99999
```

If user does not exist:

```http
404 Not Found
```

---

# 17. When should we use 201 Created?

Use `201 Created` when a new resource is successfully created.

Example:

```http
POST /api/users
```

Response:

```http
201 Created
Location: /api/users/101
```

Response body:

```json
{
  "id": 101,
  "name": "Danish Khan",
  "email": "danish@example.com"
}
```

---

# 18. When should we use 204 No Content?

Use `204 No Content` when the operation is successful but there is no response body.

Example:

```http
DELETE /api/users/10
```

Response:

```http
204 No Content
```

This is common for delete operations.

---

# 19. What is the difference between path variable and query parameter?

**Path variable** identifies a specific resource.

```http
GET /api/users/10
```

Here, `10` is a path variable.

**Query parameter** is used for filtering, searching, sorting, or pagination.

```http
GET /api/users?city=Pune&page=0&size=10
```

Spring Boot example:

```java
@GetMapping("/users/{id}")
public UserDto getUser(@PathVariable Long id) {
    return userService.getUser(id);
}

@GetMapping("/users")
public List<UserDto> getUsers(@RequestParam String city) {
    return userService.getUsersByCity(city);
}
```

---

# 20. How should REST URLs be designed?

Good REST URL design:

```http
GET /api/users
GET /api/users/10
GET /api/users/10/orders
POST /api/users
PUT /api/users/10
DELETE /api/users/10
```

Avoid verbs in URLs:

```http
/getUsers
/createUser
/deleteUser
```

REST uses nouns in URL and HTTP methods for actions.

---

# 21. Should REST URLs be singular or plural?

Usually plural nouns are preferred.

Good:

```http
/users
/orders
/products
```

Avoid:

```http
/user
/order
/product
```

Professional APIs generally use plural resource names because `/users` represents a collection of users.

---

# 22. What is request body?

Request body contains data sent by the client to the server.

Example:

```http
POST /api/users
Content-Type: application/json
```

```json
{
  "name": "Danish",
  "email": "danish@example.com"
}
```

Spring Boot example:

```java
@PostMapping("/users")
public ResponseEntity<UserDto> createUser(@RequestBody UserDto userDto) {
    UserDto savedUser = userService.createUser(userDto);
    return ResponseEntity.status(HttpStatus.CREATED).body(savedUser);
}
```

---

# 23. What is response body?

Response body contains data returned by the server to the client.

Example:

```json
{
  "id": 10,
  "name": "Danish Khan",
  "email": "danish@example.com"
}
```

In Spring Boot, Java object is automatically converted to JSON using Jackson.

```java
@GetMapping("/users/{id}")
public UserDto getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

---

# 24. What is JSON?

JSON stands for **JavaScript Object Notation**.

It is the most common data format used in REST APIs.

Example:

```json
{
  "id": 1,
  "name": "Danish",
  "skills": ["Java", "Spring Boot", "Microservices"]
}
```

In Java, JSON is mapped to DTO or entity objects.

---

# 25. What is serialization and deserialization?

**Serialization** means converting Java object to JSON.

**Deserialization** means converting JSON to Java object.

Example:

Java object:

```java
UserDto user = new UserDto(1L, "Danish", "danish@example.com");
```

Serialized JSON:

```json
{
  "id": 1,
  "name": "Danish",
  "email": "danish@example.com"
}
```

Spring Boot uses Jackson library internally for this conversion.

---

# 26. What is DTO in REST API?

DTO stands for **Data Transfer Object**.

DTO is used to transfer data between client and server.

Example:

```java
public class UserDto {
    private Long id;
    private String name;
    private String email;

    // getters and setters
}
```

Why use DTO?

1. Avoid exposing entity directly
2. Control API response
3. Hide sensitive fields
4. Add validation
5. Maintain clean architecture

Bad practice:

```java
public UserEntity getUser() {
    return userRepository.findById(id).get();
}
```

Better practice:

```java
public UserDto getUser() {
    User user = userRepository.findById(id).get();
    return modelMapper.map(user, UserDto.class);
}
```

---

# 27. Why should we not expose entity directly in REST API?

Because entity may contain internal database details.

Example entity:

```java
@Entity
public class User {
    private Long id;
    private String name;
    private String email;
    private String password;
}
```

If returned directly, password may accidentally go to client.

Better response DTO:

```java
public class UserResponseDto {
    private Long id;
    private String name;
    private String email;
}
```

**Interview answer:**

> We use DTOs to avoid exposing database structure, prevent sensitive data leakage, and keep API contracts independent from persistence layer.

---

# 28. How do you validate request data in REST API?

Use Bean Validation annotations.

Example:

```java
public class UserRequestDto {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Email should be valid")
    @NotBlank(message = "Email is required")
    private String email;

    @Size(min = 8, message = "Password must have at least 8 characters")
    private String password;
}
```

Controller:

```java
@PostMapping("/users")
public ResponseEntity<UserDto> createUser(
        @Valid @RequestBody UserRequestDto request) {

    UserDto user = userService.createUser(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(user);
}
```

---

# 29. How do you handle exceptions in REST API?

In Spring Boot, use `@RestControllerAdvice`.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiError> handleNotFound(ResourceNotFoundException ex) {
        ApiError error = new ApiError(
                "RESOURCE_NOT_FOUND",
                ex.getMessage()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

Custom error response:

```java
public class ApiError {
    private String errorCode;
    private String message;

    public ApiError(String errorCode, String message) {
        this.errorCode = errorCode;
        this.message = message;
    }

    // getters and setters
}
```

---

# 30. What should a good error response look like?

A good error response should be clear and consistent.

Example:

```json
{
  "timestamp": "2026-06-22T10:30:00",
  "status": 404,
  "error": "RESOURCE_NOT_FOUND",
  "message": "User not found with id 10",
  "path": "/api/users/10"
}
```

Avoid exposing internal stack traces to clients.

Bad response:

```text
NullPointerException at UserService.java:45
```

This is not secure and not professional.

---

# 31. What is API versioning?

API versioning means maintaining different versions of an API without breaking existing clients.

Example:

```http
GET /api/v1/users
GET /api/v2/users
```

Common versioning approaches:

1. URI versioning
2. Header versioning
3. Query parameter versioning
4. Media type versioning

Most common:

```http
/api/v1/users
```

---

# 32. Why is API versioning important?

Suppose your old API response is:

```json
{
  "name": "Danish"
}
```

Later you want to change it to:

```json
{
  "firstName": "Danish",
  "lastName": "Khan"
}
```

This may break existing clients.

So we create a new version:

```http
/api/v2/users
```

Old clients continue using:

```http
/api/v1/users
```

---

# 33. What is pagination in REST API?

Pagination means returning data in small chunks instead of all records at once.

Example:

```http
GET /api/posts?page=0&size=10
```

Spring Boot example:

```java
@GetMapping("/posts")
public Page<PostDto> getPosts(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size) {

    Pageable pageable = PageRequest.of(page, size);
    return postService.getPosts(pageable);
}
```

Why pagination is important:

1. Improves performance
2. Reduces memory usage
3. Avoids large response payload
4. Better user experience

---

# 34. What is sorting in REST API?

Sorting allows clients to order results.

Example:

```http
GET /api/posts?page=0&size=10&sortBy=createdDate&sortDir=desc
```

Spring Boot example:

```java
@GetMapping("/posts")
public Page<PostDto> getPosts(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size,
        @RequestParam(defaultValue = "createdDate") String sortBy,
        @RequestParam(defaultValue = "desc") String sortDir) {

    Sort sort = sortDir.equalsIgnoreCase("asc")
            ? Sort.by(sortBy).ascending()
            : Sort.by(sortBy).descending();

    Pageable pageable = PageRequest.of(page, size, sort);

    return postService.getPosts(pageable);
}
```

---

# 35. What is filtering in REST API?

Filtering means returning only matching records.

Example:

```http
GET /api/products?category=mobile&minPrice=10000&maxPrice=50000
```

Spring Boot example:

```java
@GetMapping("/products")
public List<ProductDto> getProducts(
        @RequestParam String category,
        @RequestParam Double minPrice,
        @RequestParam Double maxPrice) {

    return productService.getProducts(category, minPrice, maxPrice);
}
```

---

# 36. What is content negotiation?

Content negotiation means client tells server what response format it wants.

Example:

```http
Accept: application/json
```

or

```http
Accept: application/xml
```

Most REST APIs use JSON:

```http
Content-Type: application/json
Accept: application/json
```

In Spring Boot, JSON is default if Jackson is present.

---

# 37. Difference between Content-Type and Accept header?

`Content-Type` tells the server what type of data the client is sending.

Example:

```http
Content-Type: application/json
```

`Accept` tells the server what type of response the client expects.

Example:

```http
Accept: application/json
```

Example request:

```http
POST /api/users
Content-Type: application/json
Accept: application/json
```

---

# 38. What is CORS?

CORS stands for **Cross-Origin Resource Sharing**.

It is a browser security mechanism.

Example:

Frontend:

```text
http://localhost:3000
```

Backend:

```text
http://localhost:8080
```

These are different origins, so browser may block the request unless backend allows it.

Spring Boot example:

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
@RequestMapping("/api/users")
public class UserController {
}
```

Global CORS config:

```java
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                        .allowedOrigins("http://localhost:3000")
                        .allowedMethods("GET", "POST", "PUT", "DELETE", "PATCH");
            }
        };
    }
}
```

---

# 39. What is authentication in REST API?

Authentication means verifying who the user is.

Example:

```text
Username + Password
JWT Token
OAuth2 Login
API Key
```

Example:

```http
POST /api/auth/login
```

Request:

```json
{
  "username": "danish",
  "password": "password123"
}
```

Response:

```json
{
  "token": "jwt-token-value"
}
```

After login, client sends token:

```http
Authorization: Bearer jwt-token-value
```

---

# 40. What is authorization in REST API?

Authorization means checking what the authenticated user is allowed to access.

Example:

```text
ROLE_USER
ROLE_ADMIN
ROLE_MANAGER
```

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/users/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.deleteUser(id);
    return ResponseEntity.noContent().build();
}
```

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to do?

---

# 41. What is JWT and why is it used in REST API?

JWT stands for **JSON Web Token**.

It is commonly used for stateless authentication.

Flow:

1. User logs in with username/password
2. Server validates credentials
3. Server generates JWT
4. Client sends JWT in every request
5. Server validates JWT and allows access

Example:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

Why JWT is useful:

1. Stateless
2. Scalable
3. Works well with microservices
4. No server-side session required

---

# 42. What is the structure of JWT?

JWT has three parts:

```text
Header.Payload.Signature
```

Example:

```text
xxxxx.yyyyy.zzzzz
```

1. Header — algorithm and token type
2. Payload — user claims/details
3. Signature — used to verify token integrity

Payload example:

```json
{
  "sub": "danish",
  "roles": ["ROLE_USER"],
  "exp": 1718880000
}
```

Do not store sensitive data like password in JWT payload.

---

# 43. What is CSRF and why is it often disabled in JWT-based REST APIs?

CSRF stands for **Cross-Site Request Forgery**.

It is mainly a risk when authentication is based on browser cookies.

In JWT-based REST APIs, token is usually sent manually in the Authorization header:

```http
Authorization: Bearer token
```

So CSRF protection is often disabled for stateless REST APIs.

Spring Security example:

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
        .csrf(csrf -> csrf.disable())
        .sessionManagement(session ->
            session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
        );

    return http.build();
}
```

Interview answer:

> In stateless JWT-based REST APIs, we usually disable CSRF because we are not using server-side sessions or cookie-based authentication.

---

# 44. What is rate limiting?

Rate limiting controls how many requests a client can make in a given time.

Example:

```text
100 requests per minute per user
```

If limit is crossed:

```http
429 Too Many Requests
```

Why needed:

1. Prevent API abuse
2. Protect server resources
3. Avoid brute-force attacks
4. Improve system stability

In microservices, rate limiting is often implemented at API Gateway level.

---

# 45. What is caching in REST API?

Caching stores API responses temporarily to improve performance.

Example:

```http
GET /api/products/10
```

If product does not change frequently, response can be cached.

Common cache headers:

```http
Cache-Control: max-age=3600
ETag: "abc123"
```

Benefits:

1. Faster response
2. Less database load
3. Less network traffic
4. Better scalability

---

# 46. What is ETag?

ETag is used for HTTP caching and checking whether a resource has changed.

Example:

First request:

```http
GET /api/products/10
```

Response:

```http
200 OK
ETag: "v1"
```

Next request:

```http
GET /api/products/10
If-None-Match: "v1"
```

If resource has not changed:

```http
304 Not Modified
```

This saves bandwidth because server does not send the full response again.

---

# 47. What is HATEOAS?

HATEOAS stands for **Hypermedia As The Engine Of Application State**.

It means API response contains links to related actions/resources.

Example:

```json
{
  "id": 10,
  "name": "Danish",
  "links": [
    {
      "rel": "self",
      "href": "/api/users/10"
    },
    {
      "rel": "orders",
      "href": "/api/users/10/orders"
    }
  ]
}
```

In most real-world projects, HATEOAS is less commonly used than normal REST, but it is still a REST maturity concept.

---

# 48. What is the Richardson Maturity Model?

It defines REST API maturity levels.

| Level   | Meaning                                     |
| ------- | ------------------------------------------- |
| Level 0 | Single endpoint, usually RPC style          |
| Level 1 | Resource-based URLs                         |
| Level 2 | Uses HTTP methods and status codes properly |
| Level 3 | Uses HATEOAS                                |

Most practical REST APIs are Level 2.

Example Level 2 API:

```http
GET /users/10
POST /users
PUT /users/10
DELETE /users/10
```

---

# 49. How do you secure REST APIs?

Common REST API security practices:

1. Use HTTPS
2. Use authentication and authorization
3. Use JWT/OAuth2
4. Validate input
5. Never expose stack traces
6. Use rate limiting
7. Use CORS carefully
8. Do not expose sensitive fields
9. Store passwords using hashing
10. Use proper status codes

Example password hashing:

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

Never store plain text password.

Bad:

```text
password123
```

Good:

```text
$2a$10$encryptedHashValue...
```

---

# 50. How do you design a production-ready REST API in Spring Boot?

A production-ready REST API should have:

1. Clean URL design
2. DTO request/response objects
3. Proper validation
4. Global exception handling
5. Correct HTTP status codes
6. Authentication and authorization
7. Pagination, sorting, filtering
8. Swagger/OpenAPI documentation
9. Logging and monitoring
10. Unit and integration tests

Example controller:

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public ResponseEntity<UserResponseDto> createUser(
            @Valid @RequestBody UserRequestDto request) {

        UserResponseDto response = userService.createUser(request);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(response);
    }

    @GetMapping("/{id}")
    public ResponseEntity<UserResponseDto> getUser(@PathVariable Long id) {
        UserResponseDto response = userService.getUserById(id);
        return ResponseEntity.ok(response);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

DTO:

```java
public class UserRequestDto {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Email must be valid")
    @NotBlank(message = "Email is required")
    private String email;

    @Size(min = 8, message = "Password must contain at least 8 characters")
    private String password;

    // getters and setters
}
```

Response DTO:

```java
public class UserResponseDto {

    private Long id;
    private String name;
    private String email;

    // getters and setters
}
```

Global exception handler:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiError> handleResourceNotFound(ResourceNotFoundException ex) {

        ApiError error = new ApiError(
                LocalDateTime.now(),
                HttpStatus.NOT_FOUND.value(),
                "RESOURCE_NOT_FOUND",
                ex.getMessage()
        );

        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> handleValidation(MethodArgumentNotValidException ex) {

        String message = ex.getBindingResult()
                .getFieldErrors()
                .stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .findFirst()
                .orElse("Validation failed");

        ApiError error = new ApiError(
                LocalDateTime.now(),
                HttpStatus.BAD_REQUEST.value(),
                "VALIDATION_ERROR",
                message
        );

        return ResponseEntity.badRequest().body(error);
    }
}
```

Error DTO:

```java
public class ApiError {

    private LocalDateTime timestamp;
    private int status;
    private String errorCode;
    private String message;

    public ApiError(LocalDateTime timestamp, int status, String errorCode, String message) {
        this.timestamp = timestamp;
        this.status = status;
        this.errorCode = errorCode;
        this.message = message;
    }

    // getters and setters
}
```

---

# Most Important REST API Interview Points to Revise

For interviews, revise these very strongly:

| Topic                        | Importance             |
| ---------------------------- | ---------------------- |
| REST principles              | Very high              |
| Statelessness                | Very high              |
| HTTP methods                 | Very high              |
| Status codes                 | Very high              |
| PUT vs PATCH                 | Very high              |
| POST vs PUT                  | Very high              |
| 401 vs 403                   | Very high              |
| DTO vs Entity                | Very high              |
| Validation                   | Very high              |
| Global exception handling    | Very high              |
| JWT authentication           | Very high              |
| CORS                         | High                   |
| Pagination/sorting/filtering | High                   |
| API versioning               | High                   |
| Caching/ETag                 | Medium-high            |
| Swagger/OpenAPI              | High                   |
| Rate limiting                | High for microservices |
| API Gateway                  | High for microservices |

---

# Strong Interview Answer Template

When interviewer asks any REST API question, answer in this format:

```text
1. Definition
2. Why it is used
3. Small example
4. Real project usage
5. Best practice
```

Example:

**Question: What is statelessness in REST?**

Professional answer:

> Statelessness means the server does not store client session data between requests. Every request must contain all required information, such as JWT token, request body, headers, or query parameters. This makes REST APIs scalable because any server instance can handle any request. In my Spring Boot project, after login, the client sends JWT token in the Authorization header for every secured API call, so the backend does not need server-side session storage.

---

# Real Project Example You Can Say in Interview

You can explain like this:

> In my Spring Boot project, I designed REST APIs for users, posts, categories, and comments. I used proper HTTP methods like GET for fetching, POST for creating, PUT for updating, and DELETE for deleting resources. I used DTOs instead of exposing entities directly, added validation using Bean Validation annotations, handled exceptions globally using `@RestControllerAdvice`, and secured APIs using Spring Security with JWT-based stateless authentication. I also implemented pagination and sorting for post listing APIs to improve performance and user experience.

This answer sounds professional and project-based.
