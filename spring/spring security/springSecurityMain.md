# Spring Security — Interview Preparation Guide

For **Java Backend / Senior Java Developer / Microservices Developer / Full Stack Java Developer** roles with around **7 years of experience**

Spring Security is one of the most important topics for your profile because your projects include **Spring Boot REST APIs, JWT authentication, role-based authorization, React frontend integration, Swagger, Actuator, and planned microservices with API Gateway**.

Officially, Spring Security provides **authentication, authorization, and protection against common attacks** for Spring applications. It is the standard security framework for Spring-based backend systems. ([Home][1])

---

## 1. High-Level Summary

**Spring Security is a framework used to secure Java/Spring applications.**

In simple interview language:

> Spring Security helps us control **who can access the application**, **what they can access**, and **how securely they can access it**.

It mainly handles:

| Area                     | Meaning                                                                        |
| ------------------------ | ------------------------------------------------------------------------------ |
| Authentication           | Verifying user identity. Example: username/password or JWT token validation    |
| Authorization            | Checking permissions. Example: USER can create post, ADMIN can delete any post |
| Security Filters         | Intercepting requests before they reach controllers                            |
| Password Encoding        | Storing passwords securely using BCrypt                                        |
| JWT Security             | Stateless token-based authentication for REST APIs                             |
| Method Security          | Securing service methods using annotations like `@PreAuthorize`                |
| CSRF / CORS              | Protecting browser-based apps and allowing frontend-backend communication      |
| Session Management       | Stateful login vs stateless APIs                                               |
| OAuth2 / Resource Server | Validating bearer tokens in modern microservices                               |

In your **Spring Boot Blog Application**, Spring Security is used for:

* User registration and login
* Password encryption
* JWT generation after login
* JWT validation on every secured API request
* Role-based access like `ROLE_USER` and `ROLE_ADMIN`
* Protecting APIs like create post, update post, delete post
* Allowing public APIs like login, register, Swagger, and maybe list posts

---

## 2. Why This Topic Is Important

For a **7-year Java backend developer**, Spring Security is not only a configuration topic. Interviewers expect you to understand **real production security flow**.

It is important because:

1. **Most enterprise Spring Boot applications need authentication and authorization.**
2. **REST APIs must be protected from unauthorized access.**
3. **Microservices need secure service-to-service communication.**
4. **JWT, OAuth2, API Gateway security, and role-based access are common in production.**
5. **Security mistakes can cause data leaks, privilege escalation, and production incidents.**
6. **Senior developers are expected to design secure APIs, not just add annotations.**

A strong answer should cover:

> Authentication, authorization, filter chain, JWT flow, stateless session, password encoding, role-based access, CORS/CSRF, exception handling, and microservices-level security.

---

## 3. Core Concepts

---

### 3.1 Authentication

**Authentication means verifying who the user is.**

Example:

```text
User enters email + password
Spring Security validates credentials
If valid, user is authenticated
Then JWT token is generated
```

In your Blog App:

```text
POST /api/auth/login
email/password -> AuthenticationManager -> UserDetailsService -> DB -> JWT token
```

Important classes:

| Class / Interface           | Purpose                                         |
| --------------------------- | ----------------------------------------------- |
| `AuthenticationManager`     | Main entry point for authentication             |
| `AuthenticationProvider`    | Performs actual authentication logic            |
| `DaoAuthenticationProvider` | Uses `UserDetailsService` and `PasswordEncoder` |
| `UserDetailsService`        | Loads user from database                        |
| `UserDetails`               | Represents authenticated user details           |
| `PasswordEncoder`           | Encodes and verifies passwords                  |

Spring Security stores authenticated user details in `SecurityContextHolder`, which holds the current security context for the request. ([Home][2])

---

### 3.2 Authorization

**Authorization means checking what the authenticated user is allowed to do.**

Example:

```text
Authenticated user can create a post
Only ADMIN can delete any user
Only post owner can update their own post
```

Spring Security supports request-level authorization, such as securing `/admin/**` for admin users. ([Home][3])

Example:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.requestMatchers("/api/posts/**").hasAnyRole("USER", "ADMIN")
.anyRequest().authenticated()
```

---

### 3.3 Security Filter Chain

Spring Security works mainly through a **filter chain**.

Before request reaches your controller:

```text
Client Request
   ↓
Spring Security Filters
   ↓
JWT Filter / Authentication Filter
   ↓
Authorization Filter
   ↓
Controller
```

Spring Security uses `SecurityFilterChain`, and filters are managed by `FilterChainProxy`. ([Home][4])

In interview:

> In Spring Security, every incoming HTTP request passes through a chain of security filters before reaching the controller. These filters handle authentication, authorization, CSRF, CORS, session handling, exception handling, and custom JWT validation.

---

### 3.4 Authentication vs Authorization

| Authentication          | Authorization                  |
| ----------------------- | ------------------------------ |
| Who are you?            | What can you access?           |
| Login validation        | Role/permission validation     |
| Happens first           | Happens after authentication   |
| Example: email/password | Example: ADMIN can delete post |

Interview answer:

> Authentication verifies identity, while authorization verifies access rights. In my project, login with username and password performs authentication, and role-based endpoint access like ADMIN-only APIs performs authorization.

---

### 3.5 UserDetailsService

`UserDetailsService` loads user details from DB.

In your project:

```text
email -> find user in MySQL -> return UserDetails object
```

Spring Security uses `UserDetailsService` for username/password authentication, and `UserDetails` is returned by this service. ([Home][5])

Example:

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    private final UserRepository userRepository;

    public CustomUserDetailsService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String email)
            throws UsernameNotFoundException {

        User user = userRepository.findByEmail(email)
                .orElseThrow(() ->
                        new UsernameNotFoundException("User not found with email: " + email));

        return org.springframework.security.core.userdetails.User
                .withUsername(user.getEmail())
                .password(user.getPassword())
                .roles(user.getRole().replace("ROLE_", ""))
                .build();
    }
}
```

---

### 3.6 PasswordEncoder

Never store plain-text passwords.

Use:

```java
BCryptPasswordEncoder
```

Example:

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

During registration:

```java
user.setPassword(passwordEncoder.encode(registerRequest.getPassword()));
```

During login, Spring Security compares raw password with encoded password internally.

Interview line:

> In production, we never store plain passwords. We store BCrypt-hashed passwords. BCrypt also adds salt internally, which makes password cracking harder.

---

### 3.7 JWT Authentication

JWT means **JSON Web Token**.

For REST APIs, JWT is commonly used because APIs are usually **stateless**.

Flow:

```text
1. User logs in with email/password
2. Server validates credentials
3. Server generates JWT token
4. Client stores token
5. Client sends token in Authorization header
6. Server validates token on every request
7. If token is valid, request is allowed
```

Header:

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

Interview answer:

> In my Spring Boot Blog Application, after successful login, we generate a JWT token and return it to the React frontend. For every secured API call, the frontend sends the token in the Authorization header. A custom JWT filter validates the token and sets authentication in the SecurityContext.

---

### 3.8 Stateless Session

In JWT-based REST APIs:

```java
.sessionManagement(session ->
        session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
```

Meaning:

```text
Server does not store login session
Every request must carry JWT token
Application scales better horizontally
```

This is useful in microservices because any service instance can validate token without depending on server-side session.

---

### 3.9 CSRF

CSRF means **Cross-Site Request Forgery**.

Spring Security enables CSRF protection by default for unsafe HTTP methods like POST. ([Home][6])

For JWT-based stateless REST APIs, CSRF is often disabled:

```java
.csrf(csrf -> csrf.disable())
```

Interview explanation:

> CSRF protection is mainly required for browser-based session/cookie authentication. In stateless REST APIs where JWT is sent in Authorization header and no server-side session is used, we usually disable CSRF. But if authentication is cookie-based, then CSRF protection should be enabled.

---

### 3.10 CORS

CORS is needed when frontend and backend run on different origins.

Example:

```text
React frontend: http://localhost:3000
Spring Boot backend: http://localhost:8080
```

Spring Security documentation notes that CORS must be processed before Spring Security because pre-flight requests do not contain cookies. ([Home][7])

Example:

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("http://localhost:3000"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
    config.setAllowedHeaders(List.of("*"));
    config.setAllowCredentials(true);

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
}
```

---

### 3.11 Role-Based Access Control

Common roles:

```text
ROLE_USER
ROLE_ADMIN
ROLE_MODERATOR
```

Example:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.requestMatchers("/api/posts/**").hasAnyRole("USER", "ADMIN")
```

Important point:

```java
hasRole("ADMIN")
```

internally expects:

```text
ROLE_ADMIN
```

So do not write:

```java
hasRole("ROLE_ADMIN") // usually wrong
```

---

### 3.12 Method-Level Security

Method-level security protects service methods.

Spring Security supports method-level authorization using annotations like `@PreAuthorize`. ([Home][8])

Enable it:

```java
@EnableMethodSecurity
@Configuration
public class MethodSecurityConfig {
}
```

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long userId) {
    userRepository.deleteById(userId);
}
```

Project-based example:

```java
@PreAuthorize("hasRole('ADMIN') or #userId == authentication.principal.id")
public UserDto getUserProfile(Long userId) {
    return userService.getUserProfile(userId);
}
```

Interview line:

> URL-level security protects endpoints, while method-level security protects business logic. In real projects, we use both because sometimes the same service method can be called from multiple controllers.

---

### 3.13 Exception Handling in Spring Security

Common security exceptions:

| Exception                 | Meaning                                  |
| ------------------------- | ---------------------------------------- |
| `AuthenticationException` | User is not authenticated                |
| `AccessDeniedException`   | User is authenticated but not authorized |

Typical HTTP responses:

| Situation                         | Status             |
| --------------------------------- | ------------------ |
| Missing/invalid token             | `401 Unauthorized` |
| Valid token but insufficient role | `403 Forbidden`    |

Example:

```java
.exceptionHandling(ex -> ex
        .authenticationEntryPoint(jwtAuthenticationEntryPoint)
        .accessDeniedHandler(customAccessDeniedHandler)
)
```

Interview answer:

> In production, we customize 401 and 403 responses so frontend receives clean JSON errors instead of default HTML error pages.

---

### 3.14 OAuth2 and Resource Server

In modern microservices, JWT may come from an Authorization Server such as Keycloak, Okta, Auth0, Azure AD, or a custom auth service.

Spring Security supports OAuth2 Resource Server for validating bearer tokens. ([Home][9])

Example:

```java
.oauth2ResourceServer(oauth2 -> oauth2.jwt())
```

Interview line:

> In enterprise microservices, instead of every service handling login, we usually use a centralized Authorization Server. Services behave as Resource Servers and validate JWT tokens.

---

## 4. Interview-Focused Explanation

A strong 7-year experience answer:

> Spring Security is the main framework we use to secure Spring Boot applications. It works using a security filter chain, where every request passes through multiple filters before reaching the controller. In my project, I used Spring Security with JWT for stateless authentication. During login, user credentials are authenticated using AuthenticationManager, UserDetailsService loads user details from the database, PasswordEncoder validates the BCrypt password, and after successful authentication, a JWT token is generated. For each secured API request, a custom JWT filter extracts the Bearer token, validates it, and sets authentication in SecurityContextHolder. Authorization is handled using roles like USER and ADMIN at URL level and can also be handled using method-level security like @PreAuthorize.

This sounds senior because it includes:

* Filter chain
* AuthenticationManager
* UserDetailsService
* PasswordEncoder
* JWT validation
* SecurityContextHolder
* Stateless session
* Role-based authorization
* Method-level security

---

## 5. Project-Based Explanation

### 5.1 Login API

In your Blog App:

```text
POST /api/auth/login
```

Used for:

* Accepting email/password
* Authenticating user
* Returning JWT token

Interview explanation:

> In my Blog Application, login is handled through an authentication API. The credentials are passed to AuthenticationManager. Internally, Spring Security uses UserDetailsService to load the user from MySQL and PasswordEncoder to validate the password. Once authentication is successful, I generate a JWT token and return it to the React frontend.

---

### 5.2 Registration API

```text
POST /api/auth/register
```

Used for:

* Creating new user
* Encoding password
* Assigning default role

Example:

```java
user.setPassword(passwordEncoder.encode(request.getPassword()));
user.setRole("ROLE_USER");
```

Problem solved:

* Prevents storing plain-text password
* Creates user with default permission

---

### 5.3 Create Post API

```text
POST /api/posts
```

Security requirement:

```text
Only logged-in users can create posts
```

Interview answer:

> For creating a blog post, the user must be authenticated. The JWT token identifies the logged-in user, and we associate the post with that user.

---

### 5.4 Delete Post API

```text
DELETE /api/posts/{postId}
```

Security requirement:

```text
Only ADMIN or post owner should delete post
```

Interview answer:

> For delete operations, we usually apply stricter authorization. Admin may delete any post, but a normal user should only delete their own post. This can be handled using service-level checks or method-level security.

---

### 5.5 React Frontend Integration

React sends token:

```javascript
axios.get("/api/posts", {
  headers: {
    Authorization: "Bearer " + token
  }
});
```

Spring Boot validates token.

Problem solved:

* Frontend can access protected backend APIs securely
* Backend remains stateless

---

### 5.6 Microservices Version

Planned services:

```text
api-gateway
user-service
post-service
category-service
```

Recommended security design:

```text
React
  ↓
API Gateway
  ↓ JWT validation / routing
Microservices
  ↓ optional token validation again
Database
```

Senior-level explanation:

> In microservices, authentication should be centralized. API Gateway can validate JWT token at the edge, but critical downstream services should also validate token or receive trusted identity headers only from the gateway. For enterprise systems, OAuth2 Resource Server with Keycloak or Okta is commonly used.

---

## 6. Important Annotations / Classes / Interfaces / Configuration

### Important Annotations

| Annotation              | Purpose                                           |
| ----------------------- | ------------------------------------------------- |
| `@EnableWebSecurity`    | Enables Spring Security web support               |
| `@EnableMethodSecurity` | Enables method-level security                     |
| `@PreAuthorize`         | Checks authorization before method execution      |
| `@PostAuthorize`        | Checks authorization after method execution       |
| `@Secured`              | Role-based method security                        |
| `@RolesAllowed`         | JSR-250 role-based security                       |
| `@Configuration`        | Defines security config class                     |
| `@Bean`                 | Defines security beans like `SecurityFilterChain` |
| `@RestController`       | Used for auth APIs                                |
| `@RequestMapping`       | Maps auth endpoints                               |

---

### Important Classes / Interfaces

| Class / Interface                     | Purpose                                       |
| ------------------------------------- | --------------------------------------------- |
| `SecurityFilterChain`                 | Defines security rules                        |
| `AuthenticationManager`               | Authenticates credentials                     |
| `AuthenticationProvider`              | Performs authentication                       |
| `DaoAuthenticationProvider`           | DB-based authentication provider              |
| `UserDetailsService`                  | Loads user from DB                            |
| `UserDetails`                         | Represents logged-in user                     |
| `PasswordEncoder`                     | Encodes/verifies passwords                    |
| `BCryptPasswordEncoder`               | Common password encoder                       |
| `UsernamePasswordAuthenticationToken` | Authentication object                         |
| `SecurityContextHolder`               | Stores authenticated user for current request |
| `OncePerRequestFilter`                | Used for custom JWT filter                    |
| `AuthenticationEntryPoint`            | Handles 401 errors                            |
| `AccessDeniedHandler`                 | Handles 403 errors                            |
| `SessionCreationPolicy.STATELESS`     | Disables server-side session                  |

---

## 7. Code Examples

### 7.1 Security Configuration

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final AuthenticationEntryPoint authenticationEntryPoint;
    private final AccessDeniedHandler accessDeniedHandler;

    public SecurityConfig(JwtAuthenticationFilter jwtAuthenticationFilter,
                          AuthenticationEntryPoint authenticationEntryPoint,
                          AccessDeniedHandler accessDeniedHandler) {
        this.jwtAuthenticationFilter = jwtAuthenticationFilter;
        this.authenticationEntryPoint = authenticationEntryPoint;
        this.accessDeniedHandler = accessDeniedHandler;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

        http
            .csrf(csrf -> csrf.disable())
            .cors(cors -> {})
            .sessionManagement(session ->
                    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers(
                            "/api/auth/**",
                            "/v3/api-docs/**",
                            "/swagger-ui/**",
                            "/swagger-ui.html",
                            "/actuator/health"
                    ).permitAll()
                    .requestMatchers("/api/admin/**").hasRole("ADMIN")
                    .requestMatchers("/api/posts/**").hasAnyRole("USER", "ADMIN")
                    .anyRequest().authenticated()
            )
            .exceptionHandling(ex -> ex
                    .authenticationEntryPoint(authenticationEntryPoint)
                    .accessDeniedHandler(accessDeniedHandler)
            );

        http.addFilterBefore(jwtAuthenticationFilter,
                UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

---

### 7.2 Password Encoder

```java
@Configuration
public class PasswordConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

### 7.3 AuthenticationManager Bean

```java
@Configuration
public class AuthManagerConfig {

    @Bean
    public AuthenticationManager authenticationManager(
            AuthenticationConfiguration configuration) throws Exception {
        return configuration.getAuthenticationManager();
    }
}
```

---

### 7.4 Login API

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final JwtTokenService jwtTokenService;

    public AuthController(AuthenticationManager authenticationManager,
                          JwtTokenService jwtTokenService) {
        this.authenticationManager = authenticationManager;
        this.jwtTokenService = jwtTokenService;
    }

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody LoginRequest request) {

        Authentication authentication = authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(
                        request.getEmail(),
                        request.getPassword()
                )
        );

        String token = jwtTokenService.generateToken(authentication);

        return ResponseEntity.ok(new AuthResponse(token));
    }
}
```

---

### 7.5 JWT Filter

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtTokenService jwtTokenService;
    private final CustomUserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtTokenService jwtTokenService,
                                   CustomUserDetailsService userDetailsService) {
        this.jwtTokenService = jwtTokenService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
            throws ServletException, IOException {

        String header = request.getHeader("Authorization");

        if (header == null || !header.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = header.substring(7);
        String email = jwtTokenService.extractUsername(token);

        if (email != null &&
                SecurityContextHolder.getContext().getAuthentication() == null) {

            UserDetails userDetails =
                    userDetailsService.loadUserByUsername(email);

            if (jwtTokenService.isTokenValid(token, userDetails)) {

                UsernamePasswordAuthenticationToken authentication =
                        new UsernamePasswordAuthenticationToken(
                                userDetails,
                                null,
                                userDetails.getAuthorities()
                        );

                authentication.setDetails(
                        new WebAuthenticationDetailsSource()
                                .buildDetails(request)
                );

                SecurityContextHolder.getContext()
                        .setAuthentication(authentication);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

---

### 7.6 Method-Level Security

```java
@Service
public class PostService {

    @PreAuthorize("hasRole('ADMIN') or @postSecurity.isOwner(#postId, authentication)")
    public void deletePost(Long postId) {
        // delete post
    }
}
```

```java
@Component
public class PostSecurity {

    private final PostRepository postRepository;

    public PostSecurity(PostRepository postRepository) {
        this.postRepository = postRepository;
    }

    public boolean isOwner(Long postId, Authentication authentication) {
        return postRepository.findById(postId)
                .map(post -> post.getUser().getEmail()
                        .equals(authentication.getName()))
                .orElse(false);
    }
}
```

---

### 7.7 Custom 401 Response

```java
@Component
public class JwtAuthenticationEntryPoint implements AuthenticationEntryPoint {

    @Override
    public void commence(HttpServletRequest request,
                         HttpServletResponse response,
                         AuthenticationException authException)
            throws IOException {

        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        response.setContentType("application/json");

        response.getWriter().write("""
            {
              "status": 401,
              "message": "Invalid or missing authentication token"
            }
        """);
    }
}
```

---

### 7.8 Custom 403 Response

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
              "status": 403,
              "message": "You do not have permission to access this resource"
            }
        """);
    }
}
```

---

## 8. Common Real-Time Scenarios

### Scenario 1: Login works but secured API returns 401

Possible reasons:

* JWT token not sent in `Authorization` header
* Header does not start with `Bearer `
* Token expired
* Wrong JWT secret
* JWT filter not registered before `UsernamePasswordAuthenticationFilter`
* User not found in database

Interview answer:

> I would first check whether the frontend is sending the Authorization header correctly. Then I would debug the JWT filter, validate token parsing, check expiry, and confirm that SecurityContextHolder is being populated.

---

### Scenario 2: User is logged in but gets 403

Possible reasons:

* User is authenticated but does not have required role
* Role stored as `ADMIN` but Spring expects `ROLE_ADMIN`
* Wrong `hasRole()` or `hasAuthority()` usage
* Method-level security restriction failed

---

### Scenario 3: Swagger not opening after adding Spring Security

Solution:

```java
.requestMatchers(
    "/v3/api-docs/**",
    "/swagger-ui/**",
    "/swagger-ui.html"
).permitAll()
```

---

### Scenario 4: React frontend blocked by CORS

Solution:

* Add CORS config in Spring Boot
* Allow frontend origin
* Allow required methods and headers
* Ensure OPTIONS preflight is allowed

---

### Scenario 5: JWT token expired

Production approach:

* Short-lived access token
* Refresh token flow
* Re-login if refresh token expires
* Store refresh token securely
* Rotate refresh tokens for better security

---

### Scenario 6: Logout in JWT-based system

Because JWT is stateless, logout is not automatic like session invalidation.

Possible approaches:

* Delete token from frontend storage
* Keep short token expiry
* Use refresh token blacklist
* Maintain token denylist in Redis for critical systems

---

### Scenario 7: Multiple microservices need security

Approach:

```text
API Gateway validates token
Downstream services validate token again or trust gateway headers
Use OAuth2 Resource Server in services
Use service-to-service authentication for internal APIs
```

---

### Scenario 8: Password security issue

Bad:

```text
Store plain password
Use MD5/SHA1
Log password
Return password in API response
```

Good:

```text
Use BCrypt
Never expose password
Never log credentials
Use DTOs
```

---

### Scenario 9: Admin API accidentally public

Reason:

```java
.anyRequest().permitAll()
```

or wrong matcher ordering.

Best practice:

```java
Specific rules first
Public APIs next
anyRequest().authenticated()
```

---

### Scenario 10: CSRF confusion

Interview answer:

> For browser session-based applications, CSRF should generally remain enabled. For stateless REST APIs using JWT in Authorization header, we usually disable CSRF because the server is not relying on cookies/session for authentication.

---

## 9. 50 Interview Questions and Answers

---

# Basic Level

### 1. What is Spring Security?

Spring Security is a framework used to secure Spring applications. It provides authentication, authorization, password encoding, request filtering, CSRF protection, CORS support, session management, OAuth2 support, and method-level security.

---

### 2. What is authentication?

Authentication is the process of verifying user identity. Example: validating email and password during login.

---

### 3. What is authorization?

Authorization checks whether an authenticated user has permission to access a resource. Example: only ADMIN can delete users.

---

### 4. Difference between authentication and authorization?

Authentication answers **who are you**, while authorization answers **what are you allowed to access**.

---

### 5. What is `SecurityFilterChain`?

`SecurityFilterChain` defines security rules for HTTP requests. It configures public APIs, protected APIs, JWT filter, CSRF, CORS, session policy, and exception handling.

---

### 6. What is `UserDetailsService`?

`UserDetailsService` loads user details from database during authentication. It returns a `UserDetails` object used by Spring Security.

---

### 7. What is `UserDetails`?

`UserDetails` represents the authenticated user information such as username, password, roles, account status, and authorities.

---

### 8. What is `PasswordEncoder`?

`PasswordEncoder` is used to encode and verify passwords. In production, we commonly use `BCryptPasswordEncoder`.

---

### 9. Why should passwords be encoded?

Plain-text passwords are dangerous. If the database is leaked, all user passwords are exposed. Encoding passwords protects user credentials.

---

### 10. What is BCrypt?

BCrypt is a strong password hashing algorithm. It adds salt internally and is slower by design, making brute-force attacks harder.

---

# Intermediate Level

### 11. What is JWT?

JWT is a token format used to securely transfer user identity and claims between client and server. In REST APIs, JWT is commonly used for stateless authentication.

---

### 12. How does JWT authentication work?

User logs in with credentials. Server validates them and generates JWT. Client sends JWT in the Authorization header for future requests. Server validates JWT and allows access.

---

### 13. What is stateless authentication?

Stateless authentication means the server does not store user session. Every request carries authentication information, usually in the form of JWT.

---

### 14. Why use stateless session in REST APIs?

It improves scalability because any server instance can process the request without depending on server-side session storage.

---

### 15. What is `SecurityContextHolder`?

`SecurityContextHolder` stores the security context of the current request, including authenticated user details.

---

### 16. What is `AuthenticationManager`?

`AuthenticationManager` is responsible for authenticating credentials. It delegates actual authentication to one or more authentication providers.

---

### 17. What is `DaoAuthenticationProvider`?

`DaoAuthenticationProvider` authenticates username/password using `UserDetailsService` and `PasswordEncoder`.

---

### 18. What is the difference between `hasRole()` and `hasAuthority()`?

`hasRole("ADMIN")` usually expects authority as `ROLE_ADMIN`.
`hasAuthority("ADMIN")` expects exact authority value `ADMIN`.

---

### 19. Why do we disable CSRF in JWT REST APIs?

Because JWT REST APIs usually do not use server-side sessions or cookies for authentication. The token is sent in the Authorization header, so CSRF risk is different from cookie-based login.

---

### 20. What is CORS?

CORS allows frontend applications from different origins to access backend APIs. Example: React on port 3000 calling Spring Boot on port 8080.

---

# Experienced Level

### 21. Explain Spring Security filter chain.

Every incoming request passes through Spring Security filters before reaching controller. These filters handle authentication, authorization, CORS, CSRF, session management, exception handling, and custom JWT validation.

---

### 22. Where do you place a JWT filter?

Usually before `UsernamePasswordAuthenticationFilter`:

```java
http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
```

This ensures JWT is validated before authorization decisions are made.

---

### 23. What happens after JWT is validated?

After JWT validation, we create an `Authentication` object and set it in `SecurityContextHolder`. Then Spring Security treats the user as authenticated.

---

### 24. What is method-level security?

Method-level security protects service methods using annotations like `@PreAuthorize`. It is useful for business-level authorization.

---

### 25. Why use method-level security if URL security already exists?

URL security protects endpoints, but method security protects business logic. The same service method may be called from multiple controllers or internal flows.

---

### 26. Difference between 401 and 403?

`401 Unauthorized` means user is not authenticated.
`403 Forbidden` means user is authenticated but does not have required permission.

---

### 27. How do you handle security exceptions?

Use `AuthenticationEntryPoint` for 401 and `AccessDeniedHandler` for 403. In REST APIs, return clean JSON responses.

---

### 28. How do you secure Actuator endpoints?

Expose only required endpoints, keep sensitive endpoints protected, and allow public access only to harmless endpoints like `/actuator/health`.

---

### 29. How do you secure Swagger?

Swagger can be public in dev, but in production it should be restricted or disabled. Required Swagger paths should be added to `permitAll()` only if acceptable.

---

### 30. How do you implement role-based access?

Define roles in database, map them to `GrantedAuthority`, and configure endpoint or method rules using `hasRole`, `hasAuthority`, or `@PreAuthorize`.

---

# Project-Based Questions

### 31. How did you use Spring Security in your Blog App?

As per my project experience, I used Spring Security with JWT for securing REST APIs. Login authenticates the user, generates JWT, and the frontend sends that token in the Authorization header for secured APIs.

---

### 32. How does login work in your project?

The login API accepts email and password. AuthenticationManager authenticates credentials. UserDetailsService loads user from MySQL. PasswordEncoder validates the password. If valid, JWT token is generated.

---

### 33. How do you protect create post API?

Create post API requires authenticated users. The JWT token identifies the logged-in user, and the post is associated with that user.

---

### 34. How do you protect admin APIs?

Admin APIs are protected using role-based authorization like:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
```

---

### 35. How do you handle invalid JWT token?

Invalid token results in 401 Unauthorized. In production, we use custom `AuthenticationEntryPoint` to return a proper JSON response.

---

### 36. How does React frontend call secured APIs?

React stores the JWT token after login and sends it in the Authorization header:

```text
Authorization: Bearer token
```

---

### 37. How do you secure delete post functionality?

Delete post should be allowed only for ADMIN or the post owner. This can be handled through service-level ownership checks or `@PreAuthorize`.

---

### 38. How did you handle password security?

During registration, I encoded passwords using BCryptPasswordEncoder. During login, Spring Security validates the raw password against the encoded password.

---

### 39. How did you configure public and private APIs?

Login, register, Swagger, and health check APIs are public. Business APIs like create post, update post, delete post, and admin APIs are protected.

---

### 40. How will you secure your microservices version?

I would validate JWT at API Gateway and also secure internal services as Resource Servers. For enterprise systems, I would use OAuth2 with Keycloak, Okta, or another Authorization Server.

---

# Scenario-Based Questions

### 41. Login success but API returns 401. What will you check?

I will check Authorization header, Bearer prefix, token expiry, JWT secret, JWT filter registration, user existence, and whether SecurityContextHolder is being populated.

---

### 42. User gets 403 after login. Why?

The user is authenticated but does not have the required role or authority. I would check role mapping and endpoint authorization rules.

---

### 43. Swagger shows 401. How will you fix it?

I will permit Swagger URLs in security config:

```java
.requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
```

---

### 44. React app gets CORS error. What will you do?

I will configure CORS in backend, allow frontend origin, allow required methods, allow Authorization header, and ensure preflight OPTIONS requests are handled.

---

### 45. JWT token expired. What should happen?

The API should return 401. The frontend can redirect user to login or use refresh token flow to get a new access token.

---

### 46. How do you implement logout with JWT?

For basic apps, remove token from frontend. For stronger security, use short-lived access tokens and maintain refresh token or token blacklist in Redis.

---

### 47. How do you prevent users from updating other users’ data?

Check ownership. The authenticated user ID from token should match the resource owner ID, or the user must have ADMIN role.

---

### 48. How do you secure service-to-service calls?

Use internal authentication like mTLS, OAuth2 client credentials, signed tokens, or restrict internal APIs at network/security group level.

---

### 49. What security issues can happen due to wrong matcher order?

If broad rules like `permitAll()` are placed incorrectly, secured APIs may become public. Specific rules should be configured carefully, and fallback should usually be `authenticated()`.

---

### 50. What are production best practices for JWT?

Use strong secret/private key, short expiry, HTTPS, refresh token rotation, avoid storing sensitive data in token, validate issuer/audience, and never log tokens.

---

## 10. Common Mistakes

| Mistake                         | Why Bad                     | Better Approach                      |
| ------------------------------- | --------------------------- | ------------------------------------ |
| Storing plain passwords         | Data leak risk              | Use BCrypt                           |
| Using weak JWT secret           | Token can be forged         | Use strong secret or asymmetric keys |
| Putting sensitive data in JWT   | JWT can be decoded          | Store only required claims           |
| Not handling 401/403 properly   | Poor frontend experience    | Custom JSON error handlers           |
| Disabling CSRF blindly          | Risk in cookie/session apps | Disable only for stateless JWT APIs  |
| Allowing all APIs in CORS       | Security risk               | Allow only required origins          |
| Exposing all Actuator endpoints | Production risk             | Expose minimum endpoints             |
| Wrong role naming               | Authorization failure       | Understand `ROLE_` prefix            |
| Not validating token expiry     | Expired tokens accepted     | Always validate expiry               |
| Trusting frontend role          | Easy to manipulate          | Always validate role on backend      |
| Logging JWT token               | Security leak               | Mask sensitive headers               |
| Using only gateway security     | Internal service risk       | Secure critical services too         |

---

## 11. Best Practices

1. Use `SecurityFilterChain` based configuration.
2. Use BCrypt for password hashing.
3. Use stateless authentication for REST APIs.
4. Keep access tokens short-lived.
5. Use refresh token flow for better user experience.
6. Never store passwords or secrets in Git.
7. Use environment variables or secret managers.
8. Validate JWT expiry, signature, issuer, and audience.
9. Use HTTPS in production.
10. Configure CORS with specific origins.
11. Return proper 401 and 403 JSON responses.
12. Protect admin APIs separately.
13. Use method-level security for business rules.
14. Avoid exposing sensitive Actuator endpoints.
15. Use API Gateway security in microservices.
16. For enterprise systems, prefer OAuth2/OIDC with Authorization Server.
17. Do not put sensitive user data inside JWT.
18. Test security rules with integration tests.
19. Keep public APIs explicitly listed.
20. Use least privilege principle.

---

## 12. How to Explain in Interview

### Answer 1: General Spring Security

> As per my project experience, Spring Security is used to secure Spring Boot REST APIs by handling authentication and authorization. In my Blog Application, I used Spring Security with JWT. Login authenticates the user, generates a JWT token, and every secured API validates that token before allowing access.

---

### Answer 2: JWT Flow

> In our Spring Boot application, once the user logs in successfully, we generate a JWT token and return it to the React frontend. The frontend sends this token in the Authorization header for every secured API call. A custom JWT filter validates the token, extracts the username, loads user details, and sets authentication in SecurityContextHolder.

---

### Answer 3: Authentication Flow

> Authentication starts from AuthenticationManager. It delegates to AuthenticationProvider. In DB-based authentication, DaoAuthenticationProvider uses UserDetailsService to load user details and PasswordEncoder to validate the password. If authentication succeeds, Spring Security creates an authenticated Authentication object.

---

### Answer 4: Authorization

> Authorization is handled using roles and authorities. In my project, normal users can create and manage their own posts, while admin users can access admin APIs. We can configure this at URL level using SecurityFilterChain or at method level using @PreAuthorize.

---

### Answer 5: Stateless Security

> In production REST APIs, we usually keep authentication stateless using JWT. The server does not store session information. Each request carries the token, which improves scalability and works well in cloud and microservices environments.

---

### Answer 6: Microservices Security

> In microservices architecture, security is usually centralized at API Gateway, where the JWT token is validated before routing requests. However, critical downstream services should also validate tokens or accept identity only from trusted gateway headers. In enterprise systems, OAuth2 Resource Server with Keycloak or Okta is commonly used.

---

### Answer 7: CSRF

> In Spring Security, CSRF is enabled by default for unsafe HTTP methods. For session-based web applications, CSRF protection is important. But for stateless REST APIs using JWT in Authorization header, we usually disable CSRF because we are not relying on browser cookies for authentication.

---

### Answer 8: CORS

> In my React and Spring Boot integration, CORS is required because frontend and backend run on different origins. We configure allowed origins, methods, and headers so that React can call secured backend APIs with Authorization header.

---

### Answer 9: 401 vs 403

> In production, 401 means the user is not authenticated or token is invalid. 403 means the user is authenticated but does not have permission. We handle these using AuthenticationEntryPoint and AccessDeniedHandler to return proper JSON responses.

---

### Answer 10: Senior-Level Security Design

> As a senior developer, I do not treat security as only configuration. I consider password hashing, JWT expiry, refresh token strategy, role-based access, method-level authorization, CORS, CSRF, secure headers, API Gateway security, service-to-service security, and production monitoring.

---

## 13. Revision Notes

### Spring Security One-Line Definition

Spring Security secures Spring applications by providing **authentication, authorization, filter-based request security, password encoding, CSRF/CORS support, JWT/OAuth2 support, and method-level security**.

### Must-Remember Flow

```text
Request
 ↓
Security Filter Chain
 ↓
JWT Filter
 ↓
Validate Token
 ↓
Load UserDetails
 ↓
Set Authentication in SecurityContextHolder
 ↓
Check Authorization
 ↓
Controller
```

### Authentication Flow

```text
Login API
 ↓
AuthenticationManager
 ↓
AuthenticationProvider
 ↓
UserDetailsService
 ↓
PasswordEncoder
 ↓
JWT Generation
```

### JWT Flow

```text
Login -> Token generated -> Client stores token -> Sends Bearer token -> Backend validates token
```

### 401 vs 403

```text
401 = Not authenticated
403 = Authenticated but not authorized
```

### Important Classes

```text
SecurityFilterChain
AuthenticationManager
UserDetailsService
UserDetails
PasswordEncoder
BCryptPasswordEncoder
OncePerRequestFilter
SecurityContextHolder
AuthenticationEntryPoint
AccessDeniedHandler
```

### Important Annotations

```text
@EnableWebSecurity
@EnableMethodSecurity
@PreAuthorize
@PostAuthorize
@Secured
@RolesAllowed
```

### Best Interview Keywords

Use these words confidently:

```text
Authentication
Authorization
Security Filter Chain
JWT
Bearer Token
Stateless Session
SecurityContextHolder
UserDetailsService
PasswordEncoder
BCrypt
Role-Based Access
Method-Level Security
CSRF
CORS
OAuth2 Resource Server
API Gateway Security
```

### Final Interview Summary

> In my Spring Boot Blog Application, I used Spring Security with JWT to secure REST APIs. Login authenticates the user using AuthenticationManager, UserDetailsService, and PasswordEncoder. After successful authentication, JWT is generated and sent to the React frontend. For each secured request, a JWT filter validates the token and sets authentication in SecurityContextHolder. Authorization is handled using roles like USER and ADMIN at endpoint level and can also be handled using @PreAuthorize at service level. In microservices, I would validate JWT at API Gateway and secure internal services using OAuth2 Resource Server or service-to-service authentication.

[1]: https://docs.spring.io/spring-security/reference/index.html?utm_source=chatgpt.com "Spring Security"
[2]: https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html?utm_source=chatgpt.com "Servlet Authentication Architecture :: Spring Security"
[3]: https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html?utm_source=chatgpt.com "Authorize HttpServletRequests :: Spring Security"
[4]: https://docs.spring.io/spring-security/reference/servlet/architecture.html?utm_source=chatgpt.com "Architecture :: Spring Security"
[5]: https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/user-details.html?utm_source=chatgpt.com "UserDetails :: Spring Security"
[6]: https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html?utm_source=chatgpt.com "Cross Site Request Forgery (CSRF)"
[7]: https://docs.spring.io/spring-security/reference/servlet/integrations/cors.html?utm_source=chatgpt.com "CORS :: Spring Security"
[8]: https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html?utm_source=chatgpt.com "Method Security"
[9]: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html?utm_source=chatgpt.com "OAuth 2.0 Resource Server"
