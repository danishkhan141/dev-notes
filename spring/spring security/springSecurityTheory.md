# Spring Security Interview Preparation Guide

*For Java Backend / Senior Java / Microservices / Full Stack Java roles*

## 1. High-Level Summary

**Spring Security** is the standard security framework used in Spring-based Java applications. It mainly handles:

1. **Authentication** — Who are you?
   Example: username/password login, JWT token validation, OAuth2 login.

2. **Authorization** — What are you allowed to access?
   Example: only `ADMIN` can create categories, only logged-in users can comment.

3. **Protection against common web attacks** — CSRF, session fixation, secure headers, CORS integration, password hashing, etc. The official Spring Security documentation describes it as a framework for authentication, authorization, and protection against common attacks. ([Home][1])

In a **Spring Boot REST API**, Spring Security usually works like this:

```text
Client / React UI
   |
   |  Login with username/password
   v
Spring Boot API
   |
   |  Validate credentials
   |  Generate JWT
   v
Client stores JWT
   |
   |  Sends JWT in Authorization header
   v
Spring Security Filter Chain
   |
   |  JWT filter validates token
   |  Sets authenticated user in SecurityContext
   v
Controller / Service / DB
```

For your interview, remember this line:

> Spring Security is filter-chain based. Every request goes through security filters before reaching the controller. These filters handle authentication, authorization, exception handling, CSRF, sessions, JWT validation, and security context management.

Spring Security’s servlet architecture uses `SecurityFilterChain` and `FilterChainProxy` to decide which security filters apply to a request. ([Home][2])

---

## 2. Why This Topic Is Important

For your target roles, Spring Security is important because most backend applications expose APIs that need controlled access.

As a **Java Backend Developer**, you should know:

* how login works,
* how passwords are stored securely,
* how API endpoints are protected,
* how JWT authentication works,
* how roles and permissions are applied,
* how to secure REST APIs.

As a **Senior Java Developer**, you should also know:

* Spring Security filter chain,
* custom authentication flow,
* `UserDetailsService`,
* `AuthenticationProvider`,
* `SecurityContextHolder`,
* stateless vs stateful security,
* security exception handling,
* method-level security,
* production-level best practices.

As a **Microservices Developer**, you should know:

* JWT-based service-to-service authentication,
* API Gateway-level authentication,
* resource server pattern,
* OAuth2 / Keycloak / Okta / Cognito basics,
* token propagation between services,
* role-based and claim-based authorization.

In interviews, Spring Security is often asked because it connects multiple areas: Spring Boot, REST APIs, JWT, filters, microservices, production security, and debugging.

---

## 3. Core Concepts

## 3.1 Authentication

Authentication means verifying the identity of the user.

Example:

```text
username: danish@example.com
password: ********
```

Spring Security checks whether the credentials are valid. If valid, the user is considered authenticated.

Common authentication types:

* Basic Authentication
* Form Login
* JWT Authentication
* OAuth2 / OpenID Connect
* LDAP Authentication
* Database-based authentication

In your blog application, authentication is done using:

```text
email / username + password → JWT token
```

Interview answer:

> In my Spring Boot application, I used Spring Security with JWT. During login, user credentials are validated using `AuthenticationManager`. If credentials are correct, we generate a JWT and return it to the client. For subsequent requests, the client sends the JWT in the `Authorization` header, and our custom JWT filter validates it.

---

## 3.2 Authorization

Authorization means checking what the authenticated user is allowed to access.

Example:

```text
GET /api/posts          → public
POST /api/posts         → authenticated user
DELETE /api/posts/{id}  → admin or post owner
POST /api/categories    → admin only
```

Authorization can be configured at:

1. **URL level**

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
```

2. **Method level**

```java
@PreAuthorize("hasRole('ADMIN')")
```

Spring Security supports method-level authorization using annotations on methods, classes, or interfaces. ([Home][3])

---

## 3.3 Security Filter Chain

Spring Security is mainly filter-based.

A request does not directly reach the controller. It first passes through multiple filters.

```text
Client Request
   |
   v
Security Filter Chain
   |
   |-- CORS filter
   |-- CSRF filter
   |-- Authentication filter
   |-- JWT filter
   |-- Authorization filter
   |-- Exception handling filter
   |
   v
Controller
```

In a JWT-based project, we usually add a custom JWT filter before `UsernamePasswordAuthenticationFilter`.

```java
.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class)
```

Interview answer:

> Spring Security works using a chain of servlet filters. In my JWT-based application, I added a custom `OncePerRequestFilter` before `UsernamePasswordAuthenticationFilter`. This filter extracts the JWT from the Authorization header, validates it, loads the user, and sets authentication in `SecurityContextHolder`.

---

## 3.4 SecurityContext and SecurityContextHolder

`SecurityContextHolder` stores the currently authenticated user details for the current request thread. Official Spring Security documentation describes it as the place where Spring Security stores details of who is authenticated. ([Home][4])

Example:

```java
Authentication authentication =
        SecurityContextHolder.getContext().getAuthentication();
```

From this, we can get:

```java
authentication.getName();
authentication.getAuthorities();
authentication.getPrincipal();
```

In your project:

* JWT filter validates token.
* User is loaded from DB.
* `UsernamePasswordAuthenticationToken` is created.
* Authentication object is stored in `SecurityContextHolder`.

---

## 3.5 UserDetails

`UserDetails` represents the authenticated user in Spring Security.

It provides:

```java
getUsername()
getPassword()
getAuthorities()
isAccountNonExpired()
isAccountNonLocked()
isCredentialsNonExpired()
isEnabled()
```

In real projects, we either:

1. make our `User` entity implement `UserDetails`, or
2. create a separate `CustomUserDetails` wrapper.

Better approach for clean design:

```text
User Entity → Business/domain object
CustomUserDetails → Spring Security user object
```

---

## 3.6 UserDetailsService

`UserDetailsService` loads user details from database.

Main method:

```java
UserDetails loadUserByUsername(String username)
```

In your blog app:

```text
username/email → users table → UserDetails → Spring Security authentication
```

Interview answer:

> `UserDetailsService` is used by Spring Security to load user information from the database. In my project, I implemented it to fetch the user by email or username and convert it into a Spring Security `UserDetails` object.

---

## 3.7 PasswordEncoder

Passwords should never be stored in plain text.

Spring Security provides `PasswordEncoder` for secure one-way password transformation. The official docs describe `PasswordEncoder` as a one-way transformation used for securely storing passwords. ([Home][5])

Common encoders:

```java
BCryptPasswordEncoder
Pbkdf2PasswordEncoder
SCryptPasswordEncoder
Argon2PasswordEncoder
DelegatingPasswordEncoder
```

Most common in Spring Boot projects:

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

Why BCrypt?

* one-way hashing,
* salted internally,
* slower by design,
* protects against brute-force attacks better than simple hashes.

Interview line:

> In production, we never store raw passwords. We store BCrypt-hashed passwords using `PasswordEncoder`. During login, Spring Security compares the raw password with the encoded password using `matches()`.

---

## 3.8 AuthenticationManager

`AuthenticationManager` is responsible for authenticating the user.

During login:

```java
authenticationManager.authenticate(
    new UsernamePasswordAuthenticationToken(username, password)
);
```

Flow:

```text
AuthController
   |
   v
AuthenticationManager
   |
   v
AuthenticationProvider
   |
   v
UserDetailsService + PasswordEncoder
   |
   v
Authentication success/failure
```

---

## 3.9 AuthenticationProvider

`AuthenticationProvider` performs actual authentication logic.

Common provider:

```java
DaoAuthenticationProvider
```

It uses:

* `UserDetailsService`
* `PasswordEncoder`

Example:

```java
@Bean
public AuthenticationProvider authenticationProvider() {
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService);
    provider.setPasswordEncoder(passwordEncoder());
    return provider;
}
```

---

## 3.10 JWT Authentication

JWT stands for JSON Web Token.

In Spring Boot REST APIs, JWT is commonly used for stateless authentication.

Basic flow:

```text
1. User logs in with username/password.
2. Server validates credentials.
3. Server generates JWT.
4. Client stores JWT.
5. Client sends JWT in Authorization header.
6. Server validates JWT on each request.
7. If valid, request is allowed.
```

Header:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

JWT usually contains:

```json
{
  "sub": "danish@example.com",
  "roles": ["ROLE_USER"],
  "iat": 1710000000,
  "exp": 1710003600
}
```

Important interview point:

> JWT is not encrypted by default; it is Base64URL encoded and digitally signed. So we should not store sensitive data like passwords, Aadhaar, PAN, or secrets inside JWT claims.

---

## 3.11 Stateful vs Stateless Security

### Stateful

Server stores session.

```text
Login → Server creates session → JSESSIONID cookie
```

Used in traditional MVC apps.

### Stateless

Server does not store session.

```text
Login → JWT returned → Client sends token every time
```

Used in REST APIs and microservices.

Spring Security stores the security context in the HTTP session by default, but session management can be customized, especially for horizontally scalable applications. ([Home][6])

For JWT:

```java
.sessionManagement(session ->
    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
)
```

Interview line:

> In REST APIs, especially microservices, we prefer stateless authentication using JWT because services can scale horizontally without depending on server-side sessions.

---

## 3.12 CSRF

CSRF means Cross-Site Request Forgery.

Spring Security enables CSRF protection by default for unsafe HTTP methods like POST. ([Home][7])

For browser-based session applications, CSRF is important.

For stateless JWT APIs where token is sent in the `Authorization` header, teams commonly disable CSRF:

```java
.csrf(csrf -> csrf.disable())
```

Interview answer:

> In my JWT-based REST API, I disabled CSRF because the backend is stateless and the token is sent explicitly in the Authorization header. But for session-cookie-based web applications, CSRF protection should usually remain enabled.

---

## 3.13 CORS

CORS means Cross-Origin Resource Sharing.

It is important when React frontend and Spring Boot backend run on different origins.

Example:

```text
React:       http://localhost:3000
Backend:    http://localhost:8080
```

Spring Framework describes CORS as a browser mechanism to specify which cross-domain requests are allowed. ([Home][8])

Important point:

> CORS is not authentication. CORS only controls whether a browser allows frontend JavaScript to call another origin.

In your project, CORS is needed because React frontend calls Spring Boot backend.

---

## 3.14 Method-Level Security

Method-level security protects service methods.

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
public CategoryDto createCategory(CategoryDto dto) {
    return categoryService.createCategory(dto);
}
```

Modern Spring Security uses:

```java
@EnableMethodSecurity
```

Older projects may use:

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

---

## 3.15 Roles vs Authorities

In Spring Security:

```text
Role       → high-level access group
Authority  → permission/action
```

Example:

```text
ROLE_ADMIN
ROLE_USER

POST_CREATE
POST_DELETE
CATEGORY_CREATE
```

Important:

```java
hasRole("ADMIN")
```

checks for:

```text
ROLE_ADMIN
```

But:

```java
hasAuthority("ROLE_ADMIN")
```

checks exact authority.

Common mistake:

```java
hasRole("ROLE_ADMIN") // wrong in many cases
```

Correct:

```java
hasRole("ADMIN")
```

---

## 3.16 OAuth2 / Resource Server

In enterprise microservices, instead of building custom JWT login manually, teams often use:

* Keycloak
* Okta
* Auth0
* AWS Cognito
* Azure AD

Then Spring Boot services work as **OAuth2 Resource Servers**.

Spring Security supports OAuth2 Resource Server JWT validation, where Spring Boot configuration typically includes dependencies and JWT issuer/JWK configuration. ([Home][9])

Flow:

```text
User logs in with Identity Provider
        |
        v
Receives access token
        |
        v
Calls API Gateway / Microservice
        |
        v
Spring Security validates JWT
```

Interview line:

> In smaller projects, we can implement custom JWT authentication. In enterprise microservices, we usually integrate with an identity provider like Keycloak, Okta, or Cognito and configure services as OAuth2 resource servers.

---

## 3.17 Spring Security 5 vs 6 Important Difference

Older examples used:

```java
extends WebSecurityConfigurerAdapter
```

Modern Spring Security uses component-based configuration with `SecurityFilterChain`.

Spring Security 5.7 deprecated `WebSecurityConfigurerAdapter`, and Spring Security 6 removed it. ([Home][10])

Modern style:

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http
        .csrf(csrf -> csrf.disable())
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/auth/**").permitAll()
            .anyRequest().authenticated()
        )
        .build();
}
```

Interview answer:

> In my recent Spring Boot security configuration, I prefer the modern component-based approach using `SecurityFilterChain` bean instead of extending `WebSecurityConfigurerAdapter`, because that is the recommended style in Spring Security 5.7+ and required in Spring Security 6.

---

# 4. Interview-Focused Explanation

For a 7-year Java backend profile, do not explain Spring Security like a beginner. Explain it through **architecture and project usage**.

You can say:

> As per my project experience, Spring Security is used to secure REST APIs by handling authentication and authorization through a filter chain. In my Spring Boot blog application, I implemented JWT-based stateless authentication. The login API validates username and password using `AuthenticationManager`. After successful authentication, the application generates a JWT. For every subsequent request, a custom JWT filter reads the token from the Authorization header, validates it, loads user details from the database, and sets the authentication object into `SecurityContextHolder`. Then Spring Security applies URL-level or method-level authorization based on roles like `ROLE_USER` and `ROLE_ADMIN`.

For microservices:

> In microservices architecture, authentication is usually centralized. API Gateway or an Auth service validates the user and issues a token. Downstream services either trust the gateway or validate the JWT themselves as resource servers. This avoids sharing session data across services and supports horizontal scaling.

---

# 5. Project-Based Explanation

## 5.1 Spring Boot Blog Application Security Mapping

| Concept             | Where Used in Your Project      | Why Needed                                | Interview Explanation                                            |
| ------------------- | ------------------------------- | ----------------------------------------- | ---------------------------------------------------------------- |
| Login API           | `/api/auth/login`               | Validate user credentials                 | “Login API authenticates user and returns JWT.”                  |
| Register API        | `/api/auth/register`            | Create user with encrypted password       | “Password is encoded using BCrypt before saving.”                |
| JWT Filter          | Every protected API             | Validate token on each request            | “Custom filter runs before controller and sets SecurityContext.” |
| UserDetailsService  | Login and token validation      | Load user from DB                         | “I implemented DB-based user loading.”                           |
| PasswordEncoder     | Registration/login              | Secure password storage                   | “Raw passwords are never stored.”                                |
| SecurityFilterChain | Global security config          | Define public/protected URLs              | “I configured endpoint-level access rules.”                      |
| Role-based access   | Admin/user APIs                 | Restrict sensitive operations             | “Admin APIs are protected using role checks.”                    |
| CORS                | React + Spring Boot integration | Allow frontend calls                      | “Configured allowed origins, methods, and headers.”              |
| CSRF disabled       | REST JWT API                    | Stateless API does not use server session | “Disabled CSRF for token-based stateless APIs.”                  |
| Exception handling  | Invalid token/access denied     | Return proper 401/403                     | “Customized unauthorized and forbidden responses.”               |
| Swagger permitAll   | API docs                        | Allow docs access in dev/test             | “Swagger endpoints are whitelisted carefully.”                   |
| Actuator security   | `/actuator/**`                  | Avoid exposing sensitive info             | “In production, actuator endpoints should be restricted.”        |

---

## 5.2 Microservices Security Mapping

| Concept                    | Microservices Usage                  | Problem Solved                    |
| -------------------------- | ------------------------------------ | --------------------------------- |
| API Gateway authentication | Gateway validates JWT before routing | Central security entry point      |
| Auth/User Service          | Handles login and user management    | Centralized identity              |
| JWT propagation            | Gateway forwards token to services   | User context available downstream |
| Resource server            | Each service validates JWT           | Zero-trust service security       |
| Role/claim authorization   | Services check roles/scopes          | Fine-grained access               |
| Service-to-service auth    | Internal tokens/mTLS/API keys        | Protect internal calls            |
| OAuth2/Keycloak/Cognito    | Enterprise identity provider         | Standardized auth                 |
| Token expiry/refresh       | Short-lived access tokens            | Reduced risk if token leaks       |

Interview line:

> In microservices, I would avoid maintaining session in each service. I would use JWT/OAuth2 based stateless security. API Gateway can validate tokens globally, but critical services should also validate tokens or use resource server configuration for defense in depth.

---

# 6. Important Annotations / Classes / Interfaces / Configuration

## 6.1 Important Annotations

### `@EnableWebSecurity`

Enables Spring Security web security support.

In Spring Boot, security auto-configuration usually works when dependency is added, but we still commonly use a configuration class for custom rules.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
}
```

---

### `@EnableMethodSecurity`

Enables method-level security.

```java
@Configuration
@EnableMethodSecurity
public class MethodSecurityConfig {
}
```

Usage:

```java
@PreAuthorize("hasRole('ADMIN')")
public void deletePost(Long postId) {
}
```

---

### `@PreAuthorize`

Checks authorization before method execution.

```java
@PreAuthorize("hasRole('ADMIN')")
@PostMapping("/categories")
public ResponseEntity<CategoryDto> createCategory(@RequestBody CategoryDto dto) {
    return ResponseEntity.ok(categoryService.createCategory(dto));
}
```

---

### `@PostAuthorize`

Checks authorization after method execution.

Less common, but useful when authorization depends on returned object.

```java
@PostAuthorize("returnObject.author == authentication.name")
public PostDto getPost(Long id) {
    return postService.getPost(id);
}
```

---

### `@Secured`

Older style role-based method security.

```java
@Secured("ROLE_ADMIN")
public void adminOnlyMethod() {
}
```

---

### `@RolesAllowed`

JSR-250 annotation.

```java
@RolesAllowed("ADMIN")
public void createCategory() {
}
```

---

## 6.2 Important Classes and Interfaces

| Class / Interface                     | Purpose                                      |
| ------------------------------------- | -------------------------------------------- |
| `SecurityFilterChain`                 | Defines security rules for HTTP requests     |
| `HttpSecurity`                        | Used to configure web security               |
| `UserDetails`                         | Represents authenticated user                |
| `UserDetailsService`                  | Loads user from DB                           |
| `PasswordEncoder`                     | Encodes and verifies passwords               |
| `BCryptPasswordEncoder`               | Common password encoder                      |
| `AuthenticationManager`               | Main authentication entry point              |
| `AuthenticationProvider`              | Performs actual authentication               |
| `DaoAuthenticationProvider`           | Authenticates using DB user details          |
| `SecurityContextHolder`               | Holds authenticated user for current request |
| `UsernamePasswordAuthenticationToken` | Authentication object for username/password  |
| `OncePerRequestFilter`                | Base class for custom JWT filter             |
| `AuthenticationEntryPoint`            | Handles 401 Unauthorized                     |
| `AccessDeniedHandler`                 | Handles 403 Forbidden                        |
| `GrantedAuthority`                    | Represents role/permission                   |

---

# 7. Code Examples

## 7.1 Maven Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
```

---

## 7.2 User Entity Example

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @Column(unique = true, nullable = false)
    private String email;

    @Column(nullable = false)
    private String password;

    private String role; // ROLE_USER, ROLE_ADMIN

    // getters and setters
}
```

---

## 7.3 Custom UserDetails

```java
public class CustomUserDetails implements UserDetails {

    private final User user;

    public CustomUserDetails(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority(user.getRole()));
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getEmail();
    }

    public Long getUserId() {
        return user.getId();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

---

## 7.4 UserDetailsService Implementation

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public CustomUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String email) {
        User user = userRepository.findByEmail(email)
                .orElseThrow(() ->
                        new UsernameNotFoundException("User not found with email: " + email));

        return new CustomUserDetails(user);
    }
}
```

---

## 7.5 Security Configuration

```java
@Configuration
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final CustomUserDetailsService userDetailsService;

    public SecurityConfig(JwtAuthenticationFilter jwtAuthenticationFilter,
                          CustomUserDetailsService userDetailsService) {
        this.jwtAuthenticationFilter = jwtAuthenticationFilter;
        this.userDetailsService = userDetailsService;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

        return http
                .csrf(csrf -> csrf.disable())
                .cors(Customizer.withDefaults())
                .sessionManagement(session ->
                        session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                )
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers(
                                "/api/auth/**",
                                "/v3/api-docs/**",
                                "/swagger-ui/**",
                                "/swagger-ui.html"
                        ).permitAll()
                        .requestMatchers(HttpMethod.GET, "/api/posts/**").permitAll()
                        .requestMatchers(HttpMethod.GET, "/api/categories/**").permitAll()
                        .requestMatchers("/api/admin/**").hasRole("ADMIN")
                        .anyRequest().authenticated()
                )
                .authenticationProvider(authenticationProvider())
                .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class)
                .build();
    }

    @Bean
    public AuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();

        provider.setUserDetailsService(userDetailsService);
        provider.setPasswordEncoder(passwordEncoder());

        return provider;
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config)
            throws Exception {
        return config.getAuthenticationManager();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

Important role note:

```java
.hasRole("ADMIN")
```

expects authority:

```text
ROLE_ADMIN
```

So DB should contain:

```text
ROLE_ADMIN
ROLE_USER
```

---

## 7.6 JWT Authentication Filter

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final CustomUserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService,
                                   CustomUserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");

        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = authHeader.substring(7);
        String username = jwtService.extractUsername(token);

        if (username != null &&
                SecurityContextHolder.getContext().getAuthentication() == null) {

            UserDetails userDetails = userDetailsService.loadUserByUsername(username);

            if (jwtService.isTokenValid(token, userDetails)) {

                UsernamePasswordAuthenticationToken authenticationToken =
                        new UsernamePasswordAuthenticationToken(
                                userDetails,
                                null,
                                userDetails.getAuthorities()
                        );

                authenticationToken.setDetails(
                        new WebAuthenticationDetailsSource().buildDetails(request)
                );

                SecurityContextHolder.getContext().setAuthentication(authenticationToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

---

## 7.7 JWT Service Example

```java
@Service
public class JwtService {

    private static final String SECRET_KEY =
            "replace-this-with-a-strong-secret-key-minimum-256-bit";

    public String generateToken(UserDetails userDetails) {
        Map<String, Object> claims = new HashMap<>();

        claims.put("roles", userDetails.getAuthorities()
                .stream()
                .map(GrantedAuthority::getAuthority)
                .toList());

        return Jwts.builder()
                .setClaims(claims)
                .setSubject(userDetails.getUsername())
                .setIssuedAt(new Date())
                .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60))
                .signWith(getSigningKey(), SignatureAlgorithm.HS256)
                .compact();
    }

    public String extractUsername(String token) {
        return extractAllClaims(token).getSubject();
    }

    public boolean isTokenValid(String token, UserDetails userDetails) {
        String username = extractUsername(token);
        return username.equals(userDetails.getUsername()) && !isTokenExpired(token);
    }

    private boolean isTokenExpired(String token) {
        return extractAllClaims(token).getExpiration().before(new Date());
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
    }

    private Key getSigningKey() {
        byte[] keyBytes = Decoders.BASE64.decode(
                Base64.getEncoder().encodeToString(SECRET_KEY.getBytes())
        );
        return Keys.hmacShaKeyFor(keyBytes);
    }
}
```

In production, do not hardcode the secret key. Use:

```text
environment variable
AWS Secrets Manager
Kubernetes Secret
Vault
```

---

## 7.8 Login API

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final JwtService jwtService;
    private final CustomUserDetailsService userDetailsService;

    public AuthController(AuthenticationManager authenticationManager,
                          JwtService jwtService,
                          CustomUserDetailsService userDetailsService) {
        this.authenticationManager = authenticationManager;
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody LoginRequest request) {

        authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(
                        request.getEmail(),
                        request.getPassword()
                )
        );

        UserDetails userDetails =
                userDetailsService.loadUserByUsername(request.getEmail());

        String token = jwtService.generateToken(userDetails);

        return ResponseEntity.ok(new AuthResponse(token));
    }
}
```

---

## 7.9 Register API Password Encoding

```java
@PostMapping("/register")
public ResponseEntity<UserDto> register(@RequestBody RegisterRequest request) {

    User user = new User();
    user.setName(request.getName());
    user.setEmail(request.getEmail());
    user.setPassword(passwordEncoder.encode(request.getPassword()));
    user.setRole("ROLE_USER");

    User savedUser = userRepository.save(user);

    return ResponseEntity.status(HttpStatus.CREATED)
            .body(userMapper.toDto(savedUser));
}
```

---

## 7.10 Method-Level Authorization

```java
@Service
public class CategoryService {

    @PreAuthorize("hasRole('ADMIN')")
    public CategoryDto createCategory(CategoryDto categoryDto) {
        // create category
        return categoryDto;
    }
}
```

---

## 7.11 Getting Current Logged-In User

```java
public String getCurrentUsername() {
    Authentication authentication =
            SecurityContextHolder.getContext().getAuthentication();

    return authentication.getName();
}
```

Project usage:

```text
When creating a comment, get logged-in user from SecurityContext.
```

---

## 7.12 CORS Configuration for React

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {

    CorsConfiguration configuration = new CorsConfiguration();

    configuration.setAllowedOrigins(List.of(
            "http://localhost:3000",
            "https://your-frontend-domain.com"
    ));

    configuration.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
    configuration.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    configuration.setAllowCredentials(false);

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();

    source.registerCorsConfiguration("/**", configuration);

    return source;
}
```

---

## 7.13 Custom 401 and 403 Handling

### 401 Unauthorized

Occurs when user is not authenticated.

```java
@Component
public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request,
                         HttpServletResponse response,
                         AuthenticationException authException)
            throws IOException {

        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        response.setContentType("application/json");

        response.getWriter().write("""
            {
              "error": "Unauthorized",
              "message": "Invalid or missing token"
            }
        """);
    }
}
```

### 403 Forbidden

Occurs when user is authenticated but not allowed.

```java
@Component
public class CustomAccessDeniedHandler implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest request,
                       HttpServletResponse response,
                       AccessDeniedException accessDeniedException)
            throws IOException {

        response.setStatus(HttpServletResponse.SC_FORBIDDEN);
        response.setContentType("application/json");

        response.getWriter().write("""
            {
              "error": "Forbidden",
              "message": "You do not have permission to access this resource"
            }
        """);
    }
}
```

---

# 8. Common Real-Time Scenarios

## Scenario 1: JWT Token Is Missing

Request:

```http
GET /api/posts/my-posts
```

No `Authorization` header.

Result:

```text
401 Unauthorized
```

Reason:

```text
User is not authenticated.
```

---

## Scenario 2: JWT Token Is Invalid

Header:

```http
Authorization: Bearer invalid-token
```

Result:

```text
401 Unauthorized
```

Possible reasons:

* token expired,
* token signature invalid,
* wrong secret key,
* malformed token,
* token generated from another environment.

---

## Scenario 3: User Is Authenticated But Not Authorized

Example:

```text
ROLE_USER tries to call ADMIN API
```

Result:

```text
403 Forbidden
```

Interview line:

> 401 means authentication failed or missing. 403 means authentication is successful but the user does not have required permission.

---

## Scenario 4: React Frontend Getting CORS Error

Cause:

```text
Frontend origin is not allowed by backend.
```

Solution:

```text
Configure allowed origins, methods, and headers.
```

Also allow:

```text
Authorization header
OPTIONS method
```

---

## Scenario 5: Login Works, But Protected APIs Return 403

Possible causes:

* JWT filter not registered correctly,
* token not sent in header,
* role mismatch,
* DB has `ADMIN` but code expects `ROLE_ADMIN`,
* `SecurityContextHolder` not set,
* wrong endpoint matcher order.

---

## Scenario 6: Swagger Not Opening After Adding Security

Solution:

Whitelist Swagger endpoints:

```java
.requestMatchers(
    "/v3/api-docs/**",
    "/swagger-ui/**",
    "/swagger-ui.html"
).permitAll()
```

---

## Scenario 7: Actuator Exposed Publicly

Bad practice:

```text
/actuator/env
/actuator/beans
/actuator/configprops
```

These can expose sensitive information.

Best practice:

```text
Expose only health/info publicly.
Secure remaining actuator endpoints.
```

---

## Scenario 8: Token Expiry in Production

Common production approach:

```text
Access Token  → short expiry
Refresh Token → longer expiry
```

Example:

```text
Access token: 15 minutes
Refresh token: 7 days
```

---

## Scenario 9: Microservices Token Propagation

Flow:

```text
React UI → API Gateway → post-service → user-service
```

Options:

1. Gateway validates token and forwards user headers.
2. Each microservice validates JWT.
3. Use OAuth2 resource server in every service.

Best production approach:

```text
Gateway validation + service-level validation for critical services
```

---

## Scenario 10: Password Security

Never do:

```java
user.setPassword(request.getPassword());
```

Always do:

```java
user.setPassword(passwordEncoder.encode(request.getPassword()));
```

---

# 9. Beginner to Experienced-Level Interview Questions and Answers

## Basic Level

### 1. What is Spring Security?

Spring Security is a framework used to secure Spring applications. It provides authentication, authorization, and protection against common security attacks.

---

### 2. What is authentication?

Authentication verifies the identity of a user. Example: validating username and password during login.

---

### 3. What is authorization?

Authorization checks whether an authenticated user has permission to access a resource.

---

### 4. Difference between authentication and authorization?

Authentication answers “Who are you?” Authorization answers “What are you allowed to access?”

---

### 5. What is `UserDetailsService`?

`UserDetailsService` loads user-specific data from a data source, usually a database, using `loadUserByUsername()`.

---

### 6. What is `UserDetails`?

`UserDetails` represents the authenticated user in Spring Security. It contains username, password, authorities, and account status flags.

---

### 7. What is `PasswordEncoder`?

`PasswordEncoder` is used to encode passwords before storing them and verify raw passwords during login.

---

### 8. Why use BCrypt?

BCrypt is a secure one-way password hashing algorithm. It includes salting and is intentionally slow to reduce brute-force attacks.

---

### 9. What is `SecurityContextHolder`?

`SecurityContextHolder` stores the current authenticated user details for the current execution thread.

---

### 10. What is role-based authorization?

Role-based authorization restricts APIs based on user roles like `ROLE_USER` and `ROLE_ADMIN`.

---

## Intermediate Level

### 11. How does Spring Security work internally?

Spring Security works through a chain of servlet filters. Every request passes through the security filter chain before reaching the controller.

---

### 12. What is `SecurityFilterChain`?

`SecurityFilterChain` defines security rules for HTTP requests, such as which endpoints are public, which require authentication, and which require specific roles.

---

### 13. What is `AuthenticationManager`?

`AuthenticationManager` is the main component responsible for authenticating a user.

---

### 14. What is `AuthenticationProvider`?

`AuthenticationProvider` contains the actual authentication logic. For database authentication, `DaoAuthenticationProvider` is commonly used.

---

### 15. What is `DaoAuthenticationProvider`?

`DaoAuthenticationProvider` authenticates users using `UserDetailsService` and `PasswordEncoder`.

---

### 16. What is JWT?

JWT is a signed token used to represent authenticated user information in stateless APIs.

---

### 17. Where is JWT sent in API requests?

JWT is usually sent in the `Authorization` header:

```http
Authorization: Bearer token
```

---

### 18. Why disable CSRF in JWT-based REST APIs?

Because JWT APIs are usually stateless and do not depend on server-side sessions or cookies for authentication. But CSRF should not be disabled blindly in cookie/session-based applications.

---

### 19. What is CORS?

CORS is a browser security mechanism that controls whether a frontend from one origin can call a backend from another origin.

---

### 20. What is the difference between 401 and 403?

401 means the user is not authenticated. 403 means the user is authenticated but does not have required permission.

---

## Experienced Level

### 21. How would you explain Spring Security filter chain?

Every incoming request passes through multiple filters. These filters handle CORS, CSRF, authentication, JWT validation, authorization, exception handling, and finally allow or reject the request.

---

### 22. Where do you place a custom JWT filter?

A JWT filter is commonly placed before `UsernamePasswordAuthenticationFilter`.

```java
.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
```

---

### 23. Why use `OncePerRequestFilter`?

`OncePerRequestFilter` ensures the custom filter executes only once per request, which is useful for JWT validation.

---

### 24. How do you make Spring Security stateless?

By configuring:

```java
.sessionManagement(session ->
    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
)
```

---

### 25. What should JWT contain?

JWT should contain non-sensitive claims like username, user ID, roles, issued time, and expiry. It should not contain passwords or sensitive personal data.

---

### 26. How do you handle token expiry?

Use short-lived access tokens and refresh tokens. When access token expires, client calls refresh API to get a new access token.

---

### 27. How do you secure microservices?

Use API Gateway authentication, JWT/OAuth2, resource server validation, service-to-service authentication, HTTPS, and role/claim-based authorization.

---

### 28. What is OAuth2 Resource Server?

A resource server is an API that validates access tokens issued by an authorization server such as Keycloak, Okta, Cognito, or Azure AD.

---

### 29. What changed in Spring Security 6?

`WebSecurityConfigurerAdapter` was removed. Modern configuration uses `SecurityFilterChain` beans and lambda-based configuration.

---

### 30. How do you secure actuator endpoints?

Expose only required endpoints like `health` and `info`. Protect sensitive endpoints with authentication and roles.

---

## Project-Based Questions

### 31. How did you use Spring Security in your blog application?

In my blog application, I used Spring Security with JWT. Login and registration APIs are public. Protected APIs require JWT. The JWT filter validates the token and sets authentication in `SecurityContextHolder`.

---

### 32. How did you secure create post API?

Create post API requires authenticated users. The JWT token identifies the logged-in user, and the post is mapped to that user.

---

### 33. How did you secure admin APIs?

Admin APIs can be secured using URL-level rules like `.hasRole("ADMIN")` or method-level annotations like `@PreAuthorize("hasRole('ADMIN')")`.

---

### 34. How did you handle password storage?

I used `BCryptPasswordEncoder`. During registration, the raw password is encoded before saving. During login, Spring Security compares the raw password with the encoded password.

---

### 35. How did React frontend work with Spring Security?

React calls login API and receives JWT. It stores the token and sends it in the `Authorization` header for protected backend APIs.

---

### 36. How did you solve CORS issues?

I configured CORS in Spring Security to allow the frontend origin, allowed HTTP methods, and allowed headers like `Authorization` and `Content-Type`.

---

### 37. How did you expose Swagger with security?

I whitelisted Swagger endpoints such as `/v3/api-docs/**` and `/swagger-ui/**` in `SecurityFilterChain`.

---

### 38. How did you handle invalid JWT?

Invalid JWT should return 401 Unauthorized. This can be handled using custom `AuthenticationEntryPoint` or exception handling inside JWT filter.

---

### 39. How did you get current logged-in user?

I used `SecurityContextHolder.getContext().getAuthentication()` to fetch the authenticated principal.

---

### 40. How would you improve your existing custom JWT security?

I would externalize JWT secrets, implement refresh tokens, add token expiry handling, secure actuator endpoints, add proper 401/403 responses, and consider OAuth2/Keycloak for enterprise microservices.

---

## Scenario-Based Questions

### 41. User logs in successfully but protected API returns 401. What will you check?

I would check whether JWT is sent in the `Authorization` header, whether it starts with `Bearer`, whether the token is expired, whether JWT filter is registered, and whether `SecurityContextHolder` is being set.

---

### 42. User gets 403 on admin API. What will you check?

I would check whether the user is authenticated, whether the user has `ROLE_ADMIN`, whether `hasRole("ADMIN")` is used correctly, and whether DB role naming matches Spring Security expectations.

---

### 43. React app gets CORS error. What will you check?

I would check allowed origins, allowed methods, allowed headers, preflight OPTIONS request, and whether Spring Security CORS configuration is enabled.

---

### 44. Why should JWT secret not be hardcoded?

Because if source code is leaked, attackers can generate valid tokens. Secrets should be stored in environment variables, Kubernetes Secrets, AWS Secrets Manager, or Vault.

---

### 45. How do you secure service-to-service communication?

Use internal authentication such as mTLS, service tokens, OAuth2 client credentials flow, API Gateway policies, and network-level restrictions.

---

### 46. Should every microservice validate JWT?

For stronger security, yes. Gateway validation is useful, but critical services should also validate JWT or use resource server configuration.

---

### 47. What happens if token is stolen?

The attacker can access APIs until token expiry. To reduce impact, use short-lived tokens, HTTPS, refresh token rotation, revocation strategy, and avoid storing tokens insecurely.

---

### 48. Where should frontend store JWT?

For simple projects, frontend may store it in memory or storage. For stronger security, teams often prefer secure, HttpOnly cookie-based approaches, but that changes CSRF considerations.

---

### 49. How do you debug Spring Security?

Enable debug logs, check filter chain, check endpoint matchers, verify token, inspect authorities, check `SecurityContextHolder`, and validate 401 vs 403 behavior.

---

### 50. How do you explain Spring Security as a senior developer?

I explain it as a filter-chain-based security framework that integrates authentication, authorization, session management, password encoding, exception handling, CSRF/CORS, JWT, OAuth2, and method-level security for enterprise Java applications.

---

# 10. Common Mistakes

## 10.1 Storing Plain Passwords

Wrong:

```java
user.setPassword(request.getPassword());
```

Correct:

```java
user.setPassword(passwordEncoder.encode(request.getPassword()));
```

---

## 10.2 Wrong Role Naming

Wrong:

```java
.hasRole("ROLE_ADMIN")
```

Better:

```java
.hasRole("ADMIN")
```

With DB authority:

```text
ROLE_ADMIN
```

---

## 10.3 Not Setting SecurityContext

If JWT is valid but you do not set authentication:

```java
SecurityContextHolder.getContext().setAuthentication(authentication);
```

Spring will still treat user as unauthenticated.

---

## 10.4 Hardcoding JWT Secret

Wrong:

```java
private static final String SECRET = "mysecret";
```

Better:

```text
Use environment variable / secret manager.
```

---

## 10.5 Disabling CSRF Without Understanding

CSRF can be disabled for stateless JWT APIs, but not blindly for session-cookie-based applications.

---

## 10.6 Exposing Actuator Publicly

Do not expose sensitive actuator endpoints publicly.

---

## 10.7 Allowing All CORS Origins in Production

Avoid:

```java
configuration.setAllowedOrigins(List.of("*"));
```

Prefer specific frontend domains.

---

## 10.8 Very Long-Lived JWT

Avoid tokens valid for days/months without refresh strategy.

---

## 10.9 Putting Sensitive Data in JWT

Do not put passwords, secrets, PAN, Aadhaar, salary, or private data in JWT claims.

---

## 10.10 Wrong Filter Order

JWT filter should be placed before username/password authentication filter.

---

# 11. Best Practices

1. Use `SecurityFilterChain` instead of `WebSecurityConfigurerAdapter`.
2. Use `BCryptPasswordEncoder` or stronger adaptive password encoders.
3. Use stateless session policy for JWT REST APIs.
4. Keep login/register APIs public; secure business APIs.
5. Use short-lived JWT access tokens.
6. Use refresh tokens carefully.
7. Store secrets outside code.
8. Use HTTPS in production.
9. Restrict CORS to trusted frontend domains.
10. Secure actuator endpoints.
11. Return proper 401 and 403 responses.
12. Use method-level security for business rules.
13. Use role/permission design carefully.
14. Avoid storing sensitive data in JWT.
15. In microservices, prefer OAuth2 resource server with centralized identity provider.
16. Validate authorization in service layer for critical operations.
17. Log security failures carefully, but do not log tokens/passwords.
18. Use API Gateway for centralized authentication and rate limiting.
19. Keep token expiry configurable.
20. Write integration tests for security rules.

---

# 12. How to Explain in Interview

## Answer 1: General Spring Security Explanation

> As per my project experience, Spring Security is mainly used for authentication and authorization. It works through a chain of servlet filters. Every request first passes through the Spring Security filter chain, where authentication, JWT validation, authorization, exception handling, CSRF, and CORS rules are applied before the request reaches the controller.

---

## Answer 2: JWT Authentication in Your Project

> In our Spring Boot application, we implemented JWT-based stateless authentication. During login, the user provides email and password. Spring Security authenticates the credentials using `AuthenticationManager`, `UserDetailsService`, and `PasswordEncoder`. After successful authentication, we generate a JWT and return it to the client. For protected APIs, the client sends the token in the Authorization header, and our JWT filter validates it before allowing access.

---

## Answer 3: Why Stateless Security

> In REST APIs and microservices, we usually prefer stateless authentication because the server does not need to maintain session data. Each request carries its authentication information through JWT. This makes the application easier to scale horizontally.

---

## Answer 4: Security Filter Chain

> Spring Security is filter-chain based. In my project, I configured a `SecurityFilterChain` bean where I defined public endpoints like login, register, Swagger, and public post APIs. All other APIs require authentication. I also added a custom JWT filter before `UsernamePasswordAuthenticationFilter` so that token validation happens before controller execution.

---

## Answer 5: Password Encoding

> In production, we never store plain passwords. In my application, I used `BCryptPasswordEncoder`. During registration, the password is encoded before saving to the database. During login, Spring Security compares the raw password with the stored encoded password using the password encoder.

---

## Answer 6: Role-Based Authorization

> In our Spring Boot application, we used role-based authorization. For example, normal users can read posts and add comments, but admin users can manage categories or delete posts. This can be implemented using URL-level rules in `SecurityFilterChain` or method-level security using `@PreAuthorize`.

---

## Answer 7: Microservices Security

> In microservices architecture, authentication is usually centralized. API Gateway validates the JWT before forwarding the request. Downstream services can also validate the token as OAuth2 resource servers. This avoids maintaining sessions across services and gives better scalability and security.

---

## Answer 8: CSRF and CORS

> In my JWT-based REST API, I disabled CSRF because the API is stateless and does not use server-side sessions. But I configured CORS because my React frontend runs on a different origin from the Spring Boot backend. I allowed specific origins, methods, and headers like Authorization and Content-Type.

---

## Answer 9: 401 vs 403

> In Spring Security, 401 means the user is not authenticated or the token is invalid. 403 means the user is authenticated but does not have the required role or authority. This distinction is very important while debugging secured APIs.

---

## Answer 10: Spring Security 6 Style

> In recent Spring Security versions, I use component-based configuration with `SecurityFilterChain` instead of extending `WebSecurityConfigurerAdapter`. This is the modern approach and is compatible with Spring Security 6.

---

# 13. Revision Notes

## Must-Remember Flow

```text
Request
 → SecurityFilterChain
 → JWT Filter
 → Validate Token
 → Load UserDetails
 → Set SecurityContext
 → Check Authorization
 → Controller
```

## Core Components

```text
SecurityFilterChain     → Defines HTTP security rules
UserDetailsService      → Loads user from DB
UserDetails             → Represents authenticated user
PasswordEncoder         → Encodes/verifies password
AuthenticationManager   → Authenticates credentials
AuthenticationProvider  → Actual authentication logic
SecurityContextHolder   → Stores current user
OncePerRequestFilter    → Custom JWT filter
```

## 401 vs 403

```text
401 Unauthorized → Not logged in / invalid token
403 Forbidden    → Logged in but no permission
```

## JWT Header

```http
Authorization: Bearer <token>
```

## Stateless Config

```java
.sessionManagement(session ->
    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
)
```

## Role Rule

```java
.hasRole("ADMIN")
```

expects:

```text
ROLE_ADMIN
```

## Common Public APIs

```text
/api/auth/login
/api/auth/register
/swagger-ui/**
/v3/api-docs/**
GET /api/posts/**
```

## Common Protected APIs

```text
POST /api/posts
PUT /api/posts/{id}
DELETE /api/posts/{id}
POST /api/categories
/admin/**
```

## Interview Golden Line

> Spring Security is a filter-chain-based framework that secures Spring applications by handling authentication, authorization, password encoding, session management, JWT validation, CSRF/CORS, exception handling, and method-level access control.

For your profile, the most important areas are: **JWT flow, filter chain, `UserDetailsService`, `PasswordEncoder`, `SecurityContextHolder`, stateless authentication, role-based authorization, CORS/CSRF, and microservices OAuth2/resource-server security.**

[1]: https://docs.spring.io/spring-security/reference/index.html?utm_source=chatgpt.com "Spring Security"
[2]: https://docs.spring.io/spring-security/reference/servlet/architecture.html?utm_source=chatgpt.com "Architecture :: Spring Security"
[3]: https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html?utm_source=chatgpt.com "Method Security"
[4]: https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html?utm_source=chatgpt.com "Servlet Authentication Architecture :: Spring Security"
[5]: https://docs.spring.io/spring-security/site/docs/5.5.2/reference/html5/?utm_source=chatgpt.com "Spring Security Reference"
[6]: https://docs.spring.io/spring-security/reference/servlet/authentication/session-management.html?utm_source=chatgpt.com "Authentication Persistence and Session Management"
[7]: https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html?utm_source=chatgpt.com "Cross Site Request Forgery (CSRF)"
[8]: https://docs.spring.io/spring-framework/reference/web/webmvc-cors.html?utm_source=chatgpt.com "CORS :: Spring Framework"
[9]: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/jwt.html?utm_source=chatgpt.com "OAuth 2.0 Resource Server JWT"
[10]: https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter?utm_source=chatgpt.com "Spring Security without the WebSecurityConfigurerAdapter"
