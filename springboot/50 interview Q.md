Below are **Top 50 Spring Boot interview questions** explained in a clean, professional way for your Java backend interview preparation. I’ll keep it practical for your profile: **Java + Spring Boot + REST + JPA + Security + Microservices + AWS/K8s deployment mindset**.

Spring Boot is mainly used to build **standalone, production-ready Spring applications** with minimal configuration; its main powers are **auto-configuration, starters, embedded servers, externalized configuration, Actuator, and testing support**. Official Spring docs describe auto-configuration as configuring the app based on dependencies present on the classpath, externalized config as using properties/YAML/env/command-line arguments for different environments, and Actuator as production-ready monitoring/management support. ([Home][1])

---

# 1. What is Spring Boot?

**Answer:**
Spring Boot is an extension of Spring Framework that helps us create applications quickly with less configuration.

In traditional Spring, we manually configure many things: DispatcherServlet, DataSource, TransactionManager, component scanning, MVC setup, etc. Spring Boot reduces this through **auto-configuration** and **starter dependencies**.

Example:

```java
@SpringBootApplication
public class BlogApplication {
    public static void main(String[] args) {
        SpringApplication.run(BlogApplication.class, args);
    }
}
```

In interview, say:

> Spring Boot is not a replacement for Spring. It is a faster, production-friendly way to build Spring applications with auto-configuration, embedded server, starters, Actuator, and externalized configuration.

---

# 2. What are the main advantages of Spring Boot?

**Answer:**

Main advantages:

1. Less boilerplate configuration.
2. Embedded Tomcat/Jetty/Undertow.
3. Auto-configuration.
4. Starter dependencies.
5. Production-ready Actuator endpoints.
6. Easy external configuration.
7. Easy testing support.
8. Easy microservice development.

Example:

Instead of manually adding many Spring MVC dependencies, we add:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This brings Spring MVC, Jackson, validation support, embedded Tomcat, etc.

---

# 3. What is `@SpringBootApplication`?

**Answer:**
`@SpringBootApplication` is a convenience annotation that combines three annotations:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

Meaning:

```java
@SpringBootApplication
public class Application { }
```

is almost equal to:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
public class Application { }
```

Interview explanation:

> `@SpringBootApplication` marks the main class, enables auto-configuration, and scans components from the current package and sub-packages.

---

# 4. What is auto-configuration in Spring Boot?

**Answer:**
Auto-configuration means Spring Boot automatically creates required beans based on the dependencies available in the classpath.

Example:
If `spring-boot-starter-data-jpa` and MySQL driver are present, Spring Boot tries to configure:

1. DataSource
2. EntityManagerFactory
3. TransactionManager
4. JPA repositories

Official docs say Spring Boot auto-configuration attempts to configure the application based on jar dependencies added to the project. ([Home][2])

Example:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/blog
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
```

With these properties and JPA dependencies, Boot configures JPA automatically.

---

# 5. How can we disable specific auto-configuration?

**Answer:**
We can exclude auto-configuration using `exclude`.

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
public class Application {
}
```

Use case:
If your app does not need a database but has some DB dependency accidentally, Spring Boot may try to configure DataSource. Then you can exclude it.

---

# 6. What are Spring Boot starters?

**Answer:**
Starters are dependency bundles. They simplify dependency management.

Example:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This brings dependencies needed for building REST APIs.

Common starters:

```text
spring-boot-starter-web
spring-boot-starter-data-jpa
spring-boot-starter-security
spring-boot-starter-validation
spring-boot-starter-test
spring-boot-starter-actuator
```

Interview line:

> Starters are opinionated dependency descriptors that reduce manual dependency selection.

---

# 7. What is embedded server in Spring Boot?

**Answer:**
Spring Boot applications can run with embedded servers like Tomcat, Jetty, or Undertow.

Traditional deployment:

```text
WAR file -> External Tomcat
```

Spring Boot deployment:

```text
JAR file -> java -jar app.jar
```

Example:

```bash
java -jar blog-application.jar
```

This is helpful for microservices because each service can run independently.

---

# 8. What is the difference between Spring and Spring Boot?

**Answer:**

| Spring                                                      | Spring Boot                                            |
| ----------------------------------------------------------- | ------------------------------------------------------ |
| Framework for dependency injection, MVC, transactions, etc. | Extension of Spring for faster application development |
| More manual configuration                                   | Auto-configuration                                     |
| External server usually needed                              | Embedded server available                              |
| XML/Java config often required                              | Minimal config                                         |
| Production features manually added                          | Actuator available                                     |

Interview answer:

> Spring Boot sits on top of Spring and simplifies project setup, configuration, dependency management, and production readiness.

---

# 9. What is dependency injection in Spring Boot?

**Answer:**
Dependency Injection means Spring creates and injects object dependencies instead of us manually creating them using `new`.

Bad approach:

```java
UserService userService = new UserService();
```

Spring approach:

```java
@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

Recommended injection: **constructor injection**.

Why?

1. Dependencies become mandatory.
2. Easier testing.
3. Better immutability.
4. Avoids field injection problems.

---

# 10. Difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?

**Answer:**

| Annotation        | Use                                |
| ----------------- | ---------------------------------- |
| `@Component`      | Generic Spring bean                |
| `@Service`        | Business logic layer               |
| `@Repository`     | DAO/persistence layer              |
| `@Controller`     | MVC controller returning views     |
| `@RestController` | REST controller returning JSON/XML |

Example:

```java
@Service
public class PostService {
}
```

```java
@Repository
public interface PostRepository extends JpaRepository<Post, Long> {
}
```

Interview point:

> Technically, `@Service`, `@Repository`, and `@Controller` are specialized forms of `@Component`, but they improve readability and may add layer-specific behavior.

---

# 11. What is `@RestController`?

**Answer:**
`@RestController` is a combination of:

```java
@Controller
@ResponseBody
```

It is used to create REST APIs.

Example:

```java
@RestController
@RequestMapping("/api/posts")
public class PostController {

    @GetMapping("/{id}")
    public PostDto getPost(@PathVariable Long id) {
        return postService.getPost(id);
    }
}
```

Because of `@ResponseBody`, the returned object is converted into JSON using Jackson.

---

# 12. Difference between `@Controller` and `@RestController`?

**Answer:**

| `@Controller`                  | `@RestController`                   |
| ------------------------------ | ----------------------------------- |
| Used for web pages/views       | Used for REST APIs                  |
| Returns view name              | Returns JSON/XML response           |
| Needs `@ResponseBody` for JSON | `@ResponseBody` included by default |

Example:

```java
@Controller
public class PageController {
    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

```java
@RestController
public class ApiController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello";
    }
}
```

---

# 13. What is `@RequestMapping`?

**Answer:**
`@RequestMapping` maps HTTP requests to controller methods.

Example:

```java
@RequestMapping("/api/users")
@RestController
public class UserController {
}
```

For specific HTTP methods, we use:

```java
@GetMapping
@PostMapping
@PutMapping
@PatchMapping
@DeleteMapping
```

Example:

```java
@PostMapping
public UserDto createUser(@RequestBody UserDto userDto) {
    return userService.createUser(userDto);
}
```

---

# 14. Difference between `@RequestParam` and `@PathVariable`?

**Answer:**

`@PathVariable` is used when value is part of URL path.

```java
@GetMapping("/users/{id}")
public UserDto getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

Request:

```text
GET /users/10
```

`@RequestParam` is used for query parameters.

```java
@GetMapping("/users")
public List<UserDto> searchUsers(@RequestParam String city) {
    return userService.searchByCity(city);
}
```

Request:

```text
GET /users?city=Pune
```

Interview line:

> `@PathVariable` identifies a specific resource, while `@RequestParam` is generally used for filtering, searching, pagination, or optional input.

---

# 15. What is `@RequestBody`?

**Answer:**
`@RequestBody` converts incoming JSON request body into a Java object.

Example JSON:

```json
{
  "title": "Spring Boot",
  "content": "Interview preparation"
}
```

Controller:

```java
@PostMapping("/posts")
public PostDto createPost(@RequestBody PostDto postDto) {
    return postService.createPost(postDto);
}
```

Spring uses HttpMessageConverters, usually Jackson, to convert JSON to Java object.

---

# 16. What is `@ResponseBody`?

**Answer:**
`@ResponseBody` tells Spring to write the method return value directly into the HTTP response body.

Example:

```java
@Controller
public class HelloController {

    @ResponseBody
    @GetMapping("/hello")
    public String hello() {
        return "Hello";
    }
}
```

In `@RestController`, we do not need to write `@ResponseBody` explicitly.

---

# 17. How do you handle validation in Spring Boot?

**Answer:**
We use Bean Validation annotations with `@Valid`.

DTO:

```java
public class UserDto {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email")
    private String email;

    @Size(min = 8, message = "Password must be at least 8 characters")
    private String password;
}
```

Controller:

```java
@PostMapping("/users")
public UserDto createUser(@Valid @RequestBody UserDto userDto) {
    return userService.createUser(userDto);
}
```

Common annotations:

```text
@NotNull
@NotBlank
@NotEmpty
@Email
@Size
@Min
@Max
@Pattern
```

Interview point:

> Validation should usually be applied on DTOs, not directly on entities.

---

# 18. How do you handle exceptions globally in Spring Boot?

**Answer:**
Use `@ControllerAdvice` or `@RestControllerAdvice`.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiResponse> handleResourceNotFound(ResourceNotFoundException ex) {
        ApiResponse response = new ApiResponse(ex.getMessage(), false);
        return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(
            MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<>();

        ex.getBindingResult().getFieldErrors().forEach(error ->
                errors.put(error.getField(), error.getDefaultMessage())
        );

        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }
}
```

Benefits:

1. Centralized error handling.
2. Clean controllers.
3. Consistent API error response.
4. Better maintainability.

---

# 19. What is `ResponseEntity`?

**Answer:**
`ResponseEntity` represents the full HTTP response: body, status code, and headers.

Example:

```java
@GetMapping("/users/{id}")
public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
    UserDto user = userService.getUser(id);
    return ResponseEntity.ok(user);
}
```

For creation:

```java
@PostMapping("/users")
public ResponseEntity<UserDto> createUser(@RequestBody UserDto userDto) {
    UserDto createdUser = userService.createUser(userDto);
    return new ResponseEntity<>(createdUser, HttpStatus.CREATED);
}
```

Interview line:

> `ResponseEntity` gives more control over HTTP response compared to directly returning an object.

---

# 20. What is Spring Data JPA?

**Answer:**
Spring Data JPA simplifies database operations. We only create repository interfaces, and Spring provides implementation automatically.

Example:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

No implementation class is needed.

Spring Data JPA provides:

```text
save()
findById()
findAll()
deleteById()
count()
existsById()
```

Interview line:

> Spring Data JPA reduces boilerplate DAO code and provides repository abstraction over JPA.

---

# 21. Difference between `JpaRepository`, `CrudRepository`, and `PagingAndSortingRepository`?

**Answer:**

| Interface                    | Features                                         |
| ---------------------------- | ------------------------------------------------ |
| `CrudRepository`             | Basic CRUD                                       |
| `PagingAndSortingRepository` | CRUD + pagination + sorting                      |
| `JpaRepository`              | JPA-specific features + batch operations + flush |

Most commonly used:

```java
public interface PostRepository extends JpaRepository<Post, Long> {
}
```

Interview answer:

> In real projects, we generally extend `JpaRepository` because it provides complete JPA-related functionality.

---

# 22. What is the difference between Entity and DTO?

**Answer:**

| Entity                           | DTO                                 |
| -------------------------------- | ----------------------------------- |
| Represents database table        | Represents API request/response     |
| Contains JPA annotations         | Contains validation/response fields |
| Should not be directly exposed   | Safe for client communication       |
| Used in repository/service layer | Used in controller/API layer        |

Example Entity:

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
    private String password;
}
```

Example DTO:

```java
public class UserDto {
    private Long id;
    private String name;
    private String email;
}
```

Interview line:

> DTO avoids exposing internal database structure and sensitive fields like password.

---

# 23. What is `@Transactional`?

**Answer:**
`@Transactional` manages database transactions.

Example:

```java
@Transactional
public OrderDto placeOrder(OrderRequest request) {
    Payment payment = paymentService.createPayment(request);
    Order order = orderRepository.save(new Order(request, payment));
    return mapToDto(order);
}
```

If an unchecked exception occurs, transaction is rolled back.

Important points:

1. By default, rollback happens for unchecked exceptions.
2. It works through Spring proxy.
3. Usually placed on service layer.
4. Read-only queries can use `@Transactional(readOnly = true)`.

Example:

```java
@Transactional(readOnly = true)
public UserDto getUser(Long id) {
    return userRepository.findById(id)
            .map(this::toDto)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
}
```

---

# 24. What is lazy loading and eager loading?

**Answer:**
In JPA relationships, loading strategy decides when related data is fetched.

Lazy loading:

```java
@OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
private List<Post> posts;
```

Posts are loaded only when accessed.

Eager loading:

```java
@ManyToOne(fetch = FetchType.EAGER)
private User user;
```

User is loaded immediately with the main entity.

Interview line:

> Lazy loading improves performance but can cause `LazyInitializationException` if accessed outside transaction/session.

---

# 25. What is the N+1 query problem?

**Answer:**
N+1 problem happens when one query fetches parent records and then additional queries fetch child records one by one.

Example:

```text
1 query  -> fetch 100 users
100 queries -> fetch posts for each user
Total = 101 queries
```

Solutions:

1. Use `JOIN FETCH`.
2. Use `@EntityGraph`.
3. Use DTO projections.
4. Optimize fetch strategy.
5. Use pagination carefully.

Example:

```java
@Query("select u from User u join fetch u.posts where u.id = :id")
Optional<User> findUserWithPosts(@Param("id") Long id);
```

---

# 26. How does pagination work in Spring Boot?

**Answer:**
Spring Data JPA provides `Pageable`.

Repository:

```java
Page<Post> findAll(Pageable pageable);
```

Service:

```java
public Page<Post> getPosts(int pageNumber, int pageSize, String sortBy) {
    Pageable pageable = PageRequest.of(
            pageNumber,
            pageSize,
            Sort.by(sortBy).descending()
    );

    return postRepository.findAll(pageable);
}
```

Controller:

```java
@GetMapping("/posts")
public Page<Post> getPosts(
        @RequestParam(defaultValue = "0") int pageNumber,
        @RequestParam(defaultValue = "10") int pageSize,
        @RequestParam(defaultValue = "id") String sortBy) {

    return postService.getPosts(pageNumber, pageSize, sortBy);
}
```

Interview point:

> Spring pagination is zero-based, so page `0` means first page.

---

# 27. What are profiles in Spring Boot?

**Answer:**
Profiles allow different configurations for different environments.

Official docs describe Spring Profiles as a way to segregate application configuration and make it available only in certain environments. ([Home][3])

Example files:

```text
application-dev.properties
application-test.properties
application-prod.properties
```

Activate profile:

```properties
spring.profiles.active=dev
```

Or command line:

```bash
java -jar app.jar --spring.profiles.active=prod
```

Use case:

```text
dev  -> local MySQL
test -> test database
prod -> AWS RDS
```

---

# 28. Difference between `application.properties` and `application.yml`?

**Answer:**

Both are used for configuration.

Properties format:

```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/blog
```

YAML format:

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/blog
```

Interview line:

> Both work. YAML is more readable for hierarchical configuration, while properties is simple and widely used.

Spring Boot supports externalized configuration through properties files, YAML, environment variables, and command-line arguments. ([Home][4])

---

# 29. What is `@Value`?

**Answer:**
`@Value` injects property values into Spring beans.

Example:

```properties
app.jwt.secret=mysecretkey
```

```java
@Component
public class JwtUtil {

    @Value("${app.jwt.secret}")
    private String jwtSecret;
}
```

Problem:

For many related properties, `@ConfigurationProperties` is better.

---

# 30. What is `@ConfigurationProperties`?

**Answer:**
`@ConfigurationProperties` maps grouped properties into a Java class.

Example:

```yaml
app:
  jwt:
    secret: mysecret
    expiration: 3600000
```

Class:

```java
@ConfigurationProperties(prefix = "app.jwt")
@Component
public class JwtProperties {
    private String secret;
    private long expiration;

    // getters and setters
}
```

Better than multiple `@Value` fields because:

1. Type-safe.
2. Cleaner.
3. Good for grouped config.
4. Easier testing.

---

# 31. What is Spring Boot Actuator?

**Answer:**
Actuator provides production-ready monitoring and management endpoints.

Common endpoints:

```text
/actuator/health
/actuator/info
/actuator/metrics
/actuator/env
/actuator/beans
/actuator/loggers
```

Official docs say Actuator endpoints allow us to monitor and interact with the application, and the health endpoint provides application health information. ([Home][5])

Dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Expose endpoints:

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

Interview line:

> In production, we should expose only required actuator endpoints and secure sensitive ones.

---

# 32. What is `/actuator/health`?

**Answer:**
`/actuator/health` shows application health.

Example response:

```json
{
  "status": "UP"
}
```

It can include health of:

```text
Database
Disk
Redis
Kafka
Custom dependencies
```

Useful in Kubernetes:

```text
Readiness probe -> Is app ready to receive traffic?
Liveness probe  -> Is app alive or should it restart?
```

---

# 33. What is logging in Spring Boot?

**Answer:**
Spring Boot uses logging abstraction and commonly Logback by default.

Example:

```java
private static final Logger log = LoggerFactory.getLogger(UserService.class);

public UserDto getUser(Long id) {
    log.info("Fetching user with id: {}", id);
    return userRepository.findById(id)
            .map(this::toDto)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
}
```

Log levels:

```text
TRACE
DEBUG
INFO
WARN
ERROR
```

Configuration:

```properties
logging.level.com.example.blog=DEBUG
logging.file.name=logs/app.log
```

Interview point:

> Never log passwords, tokens, secrets, or sensitive personal data.

---

# 34. What is Spring Security?

**Answer:**
Spring Security is used for authentication, authorization, and protection against common attacks. Official docs describe it as the standard security framework for Spring-based applications. ([Home][6])

Authentication means:

```text
Who are you?
```

Authorization means:

```text
What are you allowed to access?
```

Example:

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers("/api/auth/**").permitAll()
                    .anyRequest().authenticated()
            )
            .build();
}
```

---

# 35. What is Spring Security filter chain?

**Answer:**
Spring Security works using a chain of filters. Every request passes through security filters before reaching the controller.

Flow:

```text
Client Request
   ↓
Security Filters
   ↓
Authentication
   ↓
Authorization
   ↓
Controller
```

Official docs explain that Security filters are registered with `FilterChainProxy`, which is a central entry point for Spring Security servlet support. ([Home][7])

Common filters:

```text
UsernamePasswordAuthenticationFilter
BasicAuthenticationFilter
BearerTokenAuthenticationFilter
ExceptionTranslationFilter
AuthorizationFilter
```

Interview line:

> In JWT authentication, we usually add a custom JWT filter before `UsernamePasswordAuthenticationFilter`.

---

# 36. What is JWT authentication in Spring Boot?

**Answer:**
JWT means JSON Web Token. It is commonly used for stateless authentication.

Flow:

```text
1. User logs in with username/password
2. Server validates credentials
3. Server generates JWT
4. Client sends JWT in Authorization header
5. Server validates token for each request
```

Header:

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

JWT filter example idea:

```java
String header = request.getHeader("Authorization");

if (header != null && header.startsWith("Bearer ")) {
    String token = header.substring(7);
    String username = jwtService.extractUsername(token);

    // validate token and set Authentication in SecurityContext
}
```

Interview line:

> JWT is stateless because the server does not need to store session data for each logged-in user.

---

# 37. Why do we disable CSRF in JWT-based REST APIs?

**Answer:**
CSRF mainly attacks browser-based session/cookie authentication. Official Spring Security docs describe CSRF protection using a token pattern where requests include a secure random token in addition to the session cookie. ([Home][8])

In stateless JWT APIs:

```text
Token is usually sent in Authorization header
Server does not rely on browser session cookie
```

So CSRF is often disabled:

```java
http.csrf(csrf -> csrf.disable());
```

Professional answer:

> We disable CSRF for stateless REST APIs using JWT in Authorization headers because the server is not using cookie-based session authentication. But for browser apps using cookies/session, CSRF should generally remain enabled.

---

# 38. What is `SecurityContextHolder`?

**Answer:**
`SecurityContextHolder` stores the current authenticated user's security details.

Official docs describe `SecurityContextHolder` as the place where Spring Security stores details of who is authenticated. ([Home][9])

Example:

```java
Authentication authentication =
        SecurityContextHolder.getContext().getAuthentication();

String username = authentication.getName();
```

In JWT filter, after token validation, we set authentication:

```java
SecurityContextHolder.getContext().setAuthentication(authentication);
```

---

# 39. What is the difference between authentication and authorization?

**Answer:**

| Concept        | Meaning             | Example                      |
| -------------- | ------------------- | ---------------------------- |
| Authentication | Verifying identity  | Login with username/password |
| Authorization  | Checking permission | Only ADMIN can delete user   |

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/users/{id}")
public void deleteUser(@PathVariable Long id) {
    userService.deleteUser(id);
}
```

Interview line:

> Authentication happens first; authorization happens after successful authentication.

---

# 40. What is CORS?

**Answer:**
CORS means Cross-Origin Resource Sharing.

Example:

Frontend:

```text
http://localhost:3000
```

Backend:

```text
http://localhost:8080
```

Browser treats them as different origins.

Spring Boot config:

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
                        .allowedMethods("GET", "POST", "PUT", "DELETE")
                        .allowedHeaders("*");
            }
        };
    }
}
```

Interview line:

> CORS is enforced by browsers, not by Postman. That is why an API may work in Postman but fail from React/Angular.

---

# 41. How do you test Spring Boot applications?

**Answer:**
Spring Boot provides test support through `spring-boot-test` and `spring-boot-test-autoconfigure`; many projects use `spring-boot-starter-test`, which brings JUnit Jupiter, AssertJ, Hamcrest, and other testing libraries. ([Home][10])

Common annotations:

```text
@SpringBootTest
@WebMvcTest
@DataJpaTest
@MockBean
@AutoConfigureMockMvc
```

Example unit test:

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void shouldReturnUserWhenUserExists() {
        User user = new User(1L, "Danish", "danish@test.com");

        when(userRepository.findById(1L)).thenReturn(Optional.of(user));

        UserDto result = userService.getUser(1L);

        assertEquals("Danish", result.getName());
    }
}
```

---

# 42. Difference between `@SpringBootTest` and `@WebMvcTest`?

**Answer:**

| Annotation        | Use                            |
| ----------------- | ------------------------------ |
| `@SpringBootTest` | Loads full application context |
| `@WebMvcTest`     | Loads only web layer           |
| `@DataJpaTest`    | Loads JPA/repository layer     |

Example:

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
}
```

Use `@WebMvcTest` for controller testing.

Use `@SpringBootTest` for integration testing.

Interview line:

> `@SpringBootTest` is heavier because it loads full context. For faster focused tests, use slice tests like `@WebMvcTest` or `@DataJpaTest`.

---

# 43. What is `MockMvc`?

**Answer:**
`MockMvc` is used to test Spring MVC controllers without starting a real server.

Example:

```java
@WebMvcTest(PostController.class)
class PostControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldReturnPost() throws Exception {
        mockMvc.perform(get("/api/posts/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.title").exists());
    }
}
```

Interview line:

> MockMvc allows us to test request mapping, validation, response status, and JSON response structure.

---

# 44. What is DevTools in Spring Boot?

**Answer:**
DevTools improves developer productivity.

Dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
</dependency>
```

Features:

1. Automatic restart.
2. Live reload support.
3. Developer-friendly defaults.

Interview point:

> DevTools should not be used in production.

---

# 45. How do you create a custom starter in Spring Boot?

**Answer:**
A custom starter is created when we want to reuse common configuration across multiple projects.

Example use case:

```text
company-logging-spring-boot-starter
company-security-spring-boot-starter
company-audit-spring-boot-starter
```

It usually contains:

1. Auto-configuration class.
2. Properties class.
3. Required beans.
4. Metadata file for auto-configuration.

Interview line:

> Custom starters are useful in large organizations where multiple microservices need common logging, tracing, security, or audit configuration.

Spring Boot docs also provide guidance for creating custom auto-configuration and starters. ([Home][11])

---

# 46. How do you connect Spring Boot with a database?

**Answer:**
Add dependencies:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

Add properties:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/blog_app
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

Entity:

```java
@Entity
public class Category {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
}
```

Repository:

```java
public interface CategoryRepository extends JpaRepository<Category, Long> {
}
```

---

# 47. What is `ddl-auto` in Spring Boot JPA?

**Answer:**
`spring.jpa.hibernate.ddl-auto` controls schema generation.

Common values:

| Value         | Meaning                             |
| ------------- | ----------------------------------- |
| `none`        | No schema changes                   |
| `validate`    | Validate schema only                |
| `update`      | Update schema automatically         |
| `create`      | Drop and create schema              |
| `create-drop` | Create at startup, drop at shutdown |

Example:

```properties
spring.jpa.hibernate.ddl-auto=update
```

Interview point:

> In production, avoid `create` or `create-drop`. Prefer migration tools like Flyway or Liquibase.

---

# 48. What is the difference between `CommandLineRunner` and `ApplicationRunner`?

**Answer:**
Both execute code after Spring Boot application starts.

`CommandLineRunner` receives raw string arguments:

```java
@Component
public class DataLoader implements CommandLineRunner {

    @Override
    public void run(String... args) {
        System.out.println("App started");
    }
}
```

`ApplicationRunner` receives parsed application arguments:

```java
@Component
public class AppRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) {
        System.out.println(args.getOptionNames());
    }
}
```

Use cases:

1. Seed initial data.
2. Run startup checks.
3. Print config.
4. Initialize cache.

---

# 49. How do you deploy a Spring Boot application?

**Answer:**
Common deployment options:

```text
1. Run as JAR on VM/EC2/Lightsail
2. Docker container
3. Kubernetes
4. Elastic Beanstalk
5. ECS/EKS
6. Traditional WAR deployment
```

JAR deployment:

```bash
mvn clean package
java -jar target/blog-app.jar
```

Dockerfile:

```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/blog-app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Interview line:

> For microservices, containerized deployment using Docker and Kubernetes is more common because scaling, rollout, rollback, and service discovery become easier.

---

# 50. How is Spring Boot used in microservices?

**Answer:**
Spring Boot is commonly used to build individual microservices.

Example architecture:

```text
API Gateway
   ↓
User Service
Post Service
Category Service
File Service
   ↓
Database / Redis / S3 / Kafka
```

Common microservice components:

```text
Spring Boot REST APIs
Spring Cloud Gateway
Service Discovery
Config Server
Resilience4j
Kafka/SQS
Docker
Kubernetes
Actuator
Prometheus/Grafana
```

Interview answer:

> In microservices, each Spring Boot service owns a specific business capability, has independent deployment, separate database ownership where possible, and communicates with other services through REST, messaging, or event-driven communication.

For your project, you can say:

> My current Spring Boot blog application is monolithic. I can split it into user-service, post-service, category-service, and file-service. API Gateway will route requests, each service can have its own DB or schema, and Actuator can be used for health checks in Kubernetes.

---

# Bonus: Important Spring Boot Code Template for Interview

## REST Controller

```java
@RestController
@RequestMapping("/api/posts")
public class PostController {

    private final PostService postService;

    public PostController(PostService postService) {
        this.postService = postService;
    }

    @PostMapping
    public ResponseEntity<PostDto> createPost(@Valid @RequestBody PostDto postDto) {
        PostDto createdPost = postService.createPost(postDto);
        return new ResponseEntity<>(createdPost, HttpStatus.CREATED);
    }

    @GetMapping("/{id}")
    public ResponseEntity<PostDto> getPost(@PathVariable Long id) {
        return ResponseEntity.ok(postService.getPost(id));
    }
}
```

## Service Layer

```java
@Service
public class PostService {

    private final PostRepository postRepository;

    public PostService(PostRepository postRepository) {
        this.postRepository = postRepository;
    }

    public PostDto getPost(Long id) {
        Post post = postRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("Post not found"));

        return mapToDto(post);
    }

    private PostDto mapToDto(Post post) {
        PostDto dto = new PostDto();
        dto.setId(post.getId());
        dto.setTitle(post.getTitle());
        dto.setContent(post.getContent());
        return dto;
    }
}
```

## Repository

```java
public interface PostRepository extends JpaRepository<Post, Long> {
    List<Post> findByTitleContaining(String keyword);
}
```

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiResponse> handleNotFound(ResourceNotFoundException ex) {
        return new ResponseEntity<>(
                new ApiResponse(ex.getMessage(), false),
                HttpStatus.NOT_FOUND
        );
    }
}
```

---

# Final Interview Revision Strategy

For interviews, revise Spring Boot in this order:

```text
1. Spring Boot basics
2. Auto-configuration and starters
3. REST APIs
4. Validation and exception handling
5. JPA and transactions
6. Profiles and configuration
7. Security + JWT
8. Actuator
9. Testing
10. Deployment and microservices
```

For your 7-year profile, interviewer will not only ask definitions. They may ask:

```text
Why did you use this annotation?
How does it work internally?
What issue did you face?
How did you handle exception/security/pagination?
How will you deploy this?
How will you split it into microservices?
```

So prepare every answer with this structure:

```text
Definition → Why used → How it works → Example from project → Production consideration
```

[1]: https://docs.spring.io/spring-boot/index.html?utm_source=chatgpt.com "Spring Boot"
[2]: https://docs.spring.io/spring-boot/reference/using/auto-configuration.html?utm_source=chatgpt.com "Auto-configuration"
[3]: https://docs.spring.io/spring-boot/reference/features/profiles.html?utm_source=chatgpt.com "Profiles :: Spring Boot"
[4]: https://docs.spring.io/spring-boot/reference/features/external-config.html?utm_source=chatgpt.com "Externalized Configuration :: Spring Boot"
[5]: https://docs.spring.io/spring-boot/reference/actuator/endpoints.html?utm_source=chatgpt.com "Endpoints :: Spring Boot"
[6]: https://docs.spring.io/spring-security/reference/index.html?utm_source=chatgpt.com "Spring Security"
[7]: https://docs.spring.io/spring-security/reference/servlet/architecture.html?utm_source=chatgpt.com "Architecture :: Spring Security"
[8]: https://docs.spring.io/spring-security/reference/features/exploits/csrf.html?utm_source=chatgpt.com "Cross Site Request Forgery (CSRF) :: Spring Security"
[9]: https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html?utm_source=chatgpt.com "Servlet Authentication Architecture :: Spring Security"
[10]: https://docs.spring.io/spring-boot/docs/3.2.5/reference/htmlsingle/?utm_source=chatgpt.com "Spring Boot Reference Documentation"
[11]: https://docs.spring.io/spring-boot/reference/features/developing-auto-configuration.html?utm_source=chatgpt.com "Creating Your Own Auto-configuration"
