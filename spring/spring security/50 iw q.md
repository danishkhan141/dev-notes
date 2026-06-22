Below is a **professional Spring Security interview preparation guide** for your Java/Spring Boot backend profile. I am using the modern Spring Security style: `SecurityFilterChain` bean, `authorizeHttpRequests`, method security, JWT/resource server concepts, CSRF/CORS, password encoding, and testing.

Spring Security mainly provides **authentication**, **authorization**, and protection against common attacks. In servlet-based Spring Boot apps, security works through the `springSecurityFilterChain`, internally managed by `FilterChainProxy` and one or more `SecurityFilterChain`s. Filters run in a defined order: exploit protection, authentication, then authorization. ([Home][1]) ([Home][2])

---

# 1. Big Picture of Spring Security

## Request flow

```text
Client Request
   |
   v
Servlet Container
   |
   v
DelegatingFilterProxy
   |
   v
springSecurityFilterChain
   |
   v
FilterChainProxy
   |
   v
Security Filters
   |
   |-- CSRF / CORS / Headers
   |-- Authentication Filter
   |-- Authorization Filter
   |
   v
Controller / API
```

Important objects:

```text
Authentication = current user identity + credentials + authorities
SecurityContext = stores Authentication
SecurityContextHolder = holder of SecurityContext
GrantedAuthority = permission/role/scope
UserDetails = user information used by Spring Security
UserDetailsService = loads user by username
PasswordEncoder = verifies encoded password
```

Spring Security documentation describes `SecurityContextHolder` as the place where Spring Security stores authenticated-user details, and `Authentication` as either the submitted credential object or the current authenticated user inside the `SecurityContext`. ([Home][3])

---

# Basic Example Configuration

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // usually disabled for stateless JWT APIs
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/swagger-ui/**", "/v3/api-docs/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());

        return http.build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

Official docs show that when using `HttpSecurity`, you should declare authorization rules, commonly through `authorizeHttpRequests`, and by default Spring Security expects requests to be authenticated unless configured otherwise. ([Home][4])

---

# Top 50 Spring Security Interview Questions

---

## 1. What is Spring Security?

**Answer:**
Spring Security is a framework used to secure Spring applications. It handles:

```text
1. Authentication - Who are you?
2. Authorization - What are you allowed to access?
3. Protection - CSRF, CORS, security headers, session attacks, etc.
```

Example:
Login with username/password is authentication. Checking whether the logged-in user can access `/admin/users` is authorization.

---

## 2. What is the difference between authentication and authorization?

**Authentication** verifies identity.

```text
Username: danish
Password: ****
```

**Authorization** checks permission.

```text
Can Danish access /admin/users?
```

Example:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.anyRequest().authenticated()
```

Here, `.authenticated()` checks login, while `.hasRole("ADMIN")` checks authority.

---

## 3. What is `SecurityFilterChain`?

**Answer:**
`SecurityFilterChain` defines the security rules and filters for HTTP requests.

Example:

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(auth -> auth
        .requestMatchers("/public/**").permitAll()
        .anyRequest().authenticated()
    );
    return http.build();
}
```

In modern Spring Security, this bean replaces the older `WebSecurityConfigurerAdapter`, which is no longer the preferred style.

---

## 4. What is `FilterChainProxy`?

**Answer:**
`FilterChainProxy` is the central Spring Security filter that delegates a request to the correct `SecurityFilterChain`.

Think of it like this:

```text
FilterChainProxy
   |
   |-- SecurityFilterChain for /api/**
   |-- SecurityFilterChain for /admin/**
   |-- SecurityFilterChain for /public/**
```

Spring Security filters are registered with `FilterChainProxy`, and the filters are executed in a specific order so authentication happens before authorization. ([Home][2])

---

## 5. What is `DelegatingFilterProxy`?

**Answer:**
`DelegatingFilterProxy` is a servlet filter provided by Spring. It delegates filtering work from the servlet container to a Spring-managed bean, usually `springSecurityFilterChain`.

Flow:

```text
Servlet Container Filter
        |
        v
DelegatingFilterProxy
        |
        v
Spring Bean: springSecurityFilterChain
```

---

## 6. What is `SecurityContextHolder`?

**Answer:**
`SecurityContextHolder` stores the current user’s security information.

Example:

```java
Authentication authentication =
        SecurityContextHolder.getContext().getAuthentication();

String username = authentication.getName();
```

It is commonly used when you need the logged-in user inside service or controller logic.

---

## 7. What is `SecurityContext`?

**Answer:**
`SecurityContext` contains the `Authentication` object of the currently logged-in user.

```text
SecurityContextHolder
        |
        v
SecurityContext
        |
        v
Authentication
        |
        v
Principal + Authorities
```

---

## 8. What is `Authentication` in Spring Security?

**Answer:**
`Authentication` represents either:

1. The login request before authentication.
2. The authenticated user after successful authentication.

It contains:

```text
principal     -> user details
credentials   -> password/token
authorities   -> roles/permissions
authenticated -> true/false
```

Example:

```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();

System.out.println(auth.getName());
System.out.println(auth.getAuthorities());
```

---

## 9. What is `GrantedAuthority`?

**Answer:**
`GrantedAuthority` represents a permission or role assigned to the user.

Example:

```text
ROLE_USER
ROLE_ADMIN
READ_PRIVILEGE
WRITE_PRIVILEGE
SCOPE_orders.read
```

Example in code:

```java
new SimpleGrantedAuthority("ROLE_ADMIN");
```

---

## 10. What is the difference between role and authority?

**Answer:**
An **authority** is any permission string.

```text
READ_USER
DELETE_POST
ROLE_ADMIN
```

A **role** is a special authority usually prefixed with `ROLE_`.

```java
.hasRole("ADMIN")
```

Internally expects:

```text
ROLE_ADMIN
```

So:

```java
.hasRole("ADMIN")       // checks ROLE_ADMIN
.hasAuthority("ADMIN")  // checks exactly ADMIN
.hasAuthority("ROLE_ADMIN") // checks exactly ROLE_ADMIN
```

---

## 11. What is `UserDetails`?

**Answer:**
`UserDetails` represents the user information required by Spring Security.

It provides:

```text
username
password
authorities
accountNonExpired
accountNonLocked
credentialsNonExpired
enabled
```

Example:

```java
public class CustomUserDetails implements UserDetails {

    private final User user;

    public CustomUserDetails(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority("ROLE_" + user.getRole()));
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getEmail();
    }
}
```

---

## 12. Why do we implement `UserDetails`?

**Answer:**
We implement `UserDetails` when our database user entity does not directly match what Spring Security needs.

For example, your table may have:

```text
id
name
email
password
role
status
```

But Spring Security expects:

```text
username
password
authorities
enabled
accountNonLocked
```

So `UserDetails` acts as an adapter between your user table and Spring Security.

---

## 13. What is `UserDetailsService`?

**Answer:**
`UserDetailsService` is used to load user details from database by username.

Main method:

```java
UserDetails loadUserByUsername(String username)
        throws UsernameNotFoundException;
```

Example:

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
            .orElseThrow(() -> new UsernameNotFoundException("User not found"));

        return new CustomUserDetails(user);
    }
}
```

---

## 14. What is `AuthenticationManager`?

**Answer:**
`AuthenticationManager` is responsible for authenticating a user.

Example in login API:

```java
Authentication authentication = authenticationManager.authenticate(
    new UsernamePasswordAuthenticationToken(email, password)
);
```

If credentials are valid, it returns an authenticated `Authentication` object. If invalid, it throws an exception.

---

## 15. What is `AuthenticationProvider`?

**Answer:**
`AuthenticationProvider` performs actual authentication logic.

Example:
`DaoAuthenticationProvider` uses:

```text
UserDetailsService + PasswordEncoder
```

Flow:

```text
AuthenticationManager
        |
        v
AuthenticationProvider
        |
        v
UserDetailsService loads user
        |
        v
PasswordEncoder verifies password
```

---

## 16. What is `DaoAuthenticationProvider`?

**Answer:**
`DaoAuthenticationProvider` is a built-in provider that authenticates users using database-backed user details.

It needs:

```text
UserDetailsService
PasswordEncoder
```

Example:

```java
@Bean
DaoAuthenticationProvider authenticationProvider(
        UserDetailsService userDetailsService,
        PasswordEncoder passwordEncoder) {

    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService);
    provider.setPasswordEncoder(passwordEncoder);
    return provider;
}
```

---

## 17. What is `PasswordEncoder`?

**Answer:**
`PasswordEncoder` is used to encode and verify passwords.

Never store plain-text passwords.

Example:

```java
@Bean
PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

Usage:

```java
String encodedPassword = passwordEncoder.encode("myPassword");

boolean matches = passwordEncoder.matches("myPassword", encodedPassword);
```

Spring Security uses `DelegatingPasswordEncoder` by default and recommends secure password encoders instead of insecure/no-op encoders. ([Home][5])

---

## 18. Why is BCrypt commonly used?

**Answer:**
BCrypt is commonly used because it is a one-way hashing algorithm with salt and configurable strength.

Plain password:

```text
danish123
```

Stored password:

```text
$2a$10$7qDU...
```

Even if two users have the same password, BCrypt generates different hashes because of salt.

---

## 19. What is the difference between encoding and encryption?

**Answer:**

| Concept    | Meaning                | Reversible?   |
| ---------- | ---------------------- | ------------- |
| Encoding   | Converts data format   | Yes           |
| Encryption | Secures data using key | Yes, with key |
| Hashing    | One-way transformation | No            |

Passwords should be **hashed**, not encrypted.

Correct:

```java
passwordEncoder.encode(rawPassword);
```

Wrong:

```java
store raw password directly
```

---

## 20. What happens during form login authentication?

**Answer:**

```text
1. User submits username/password.
2. UsernamePasswordAuthenticationFilter intercepts request.
3. AuthenticationManager authenticates.
4. AuthenticationProvider loads user.
5. PasswordEncoder checks password.
6. SecurityContext is updated.
7. User is redirected or response is returned.
```

For traditional MVC apps, form login is useful. For REST APIs, JWT-based stateless authentication is more common.

---

## 21. What is HTTP Basic authentication?

**Answer:**
HTTP Basic sends username and password in the `Authorization` header.

Example:

```http
Authorization: Basic base64(username:password)
```

It is simple but should only be used over HTTPS.

Example config:

```java
http.httpBasic(Customizer.withDefaults());
```

Spring Security’s Basic Authentication support sends `WWW-Authenticate` when unauthenticated access is denied. ([Home][6])

---

## 22. What is JWT?

**Answer:**
JWT means JSON Web Token. It is a compact token used to represent claims about a user.

JWT structure:

```text
header.payload.signature
```

Example payload:

```json
{
  "sub": "danish@gmail.com",
  "roles": ["ROLE_USER"],
  "iat": 1710000000,
  "exp": 1710003600
}
```

JWT is commonly used in stateless REST APIs.

---

## 23. Why is JWT called stateless?

**Answer:**
JWT is stateless because the server does not need to store session data.

Flow:

```text
Login success -> server returns JWT
Next request -> client sends JWT
Server validates JWT signature and expiry
No HTTP session required
```

Example header:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

Spring Security’s OAuth2 Resource Server supports protecting endpoints using bearer tokens such as JWT and opaque tokens. ([Home][7])

---

## 24. What are the benefits of stateless JWT authentication?

**Answer:**

```text
1. Scales well across multiple servers.
2. No server-side session required.
3. Useful for microservices.
4. Mobile/frontend clients can store and send token.
5. Works well with API Gateway architecture.
```

Example:

```text
React frontend -> sends JWT -> Spring Boot API
```

---

## 25. What are the disadvantages of JWT?

**Answer:**

```text
1. Token revocation is difficult.
2. Stolen token can be used until expiry.
3. Large token increases request size.
4. Sensitive data should not be stored in token.
5. Requires careful secret/key management.
```

Good practice:

```text
Use short expiry + refresh token + HTTPS.
```

---

## 26. What is JWT secret?

**Answer:**
JWT secret is the key used to sign and verify JWT tokens.

Example:

```text
signature = HMACSHA256(base64Url(header) + "." + base64Url(payload), secret)
```

If someone knows your secret, they can create fake valid tokens.

Bad:

```java
private String secret = "abc123";
```

Better:

```properties
jwt.secret=${JWT_SECRET}
```

Store it in environment variables, Vault, AWS Secrets Manager, or Kubernetes Secret.

---

## 27. How do you validate JWT in Spring Security?

**Answer:**
Usually by adding a custom JWT filter before `UsernamePasswordAuthenticationFilter`.

Simplified example:

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService,
                                   UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
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
        String username = jwtService.extractUsername(token);

        if (username != null &&
            SecurityContextHolder.getContext().getAuthentication() == null) {

            UserDetails userDetails =
                    userDetailsService.loadUserByUsername(username);

            if (jwtService.isTokenValid(token, userDetails)) {
                UsernamePasswordAuthenticationToken authToken =
                        new UsernamePasswordAuthenticationToken(
                                userDetails,
                                null,
                                userDetails.getAuthorities()
                        );

                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

Filter registration:

```java
http.addFilterBefore(jwtAuthenticationFilter,
        UsernamePasswordAuthenticationFilter.class);
```

---

## 28. What is `OncePerRequestFilter`?

**Answer:**
`OncePerRequestFilter` ensures a filter runs only once per request.

It is commonly used for JWT authentication because we want to validate the token once per request.

Example:

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(...) {
        // JWT validation logic
    }
}
```

---

## 29. Why do we disable CSRF in JWT APIs?

**Answer:**
CSRF mainly matters when the browser automatically sends authentication credentials like cookies. In stateless JWT APIs, the token is usually sent manually in the `Authorization` header.

Example:

```java
.csrf(csrf -> csrf.disable())
```

However, if JWT is stored in cookies, CSRF protection may still be required.

Spring Security protects against CSRF by default for unsafe HTTP methods like POST, so disabling it should be a conscious decision, usually for stateless non-cookie APIs. ([Home][8])

---

## 30. What is `SessionCreationPolicy.STATELESS`?

**Answer:**
It tells Spring Security not to create or use HTTP sessions.

Example:

```java
.sessionManagement(session ->
    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
)
```

Use it for JWT-based REST APIs.

```text
STATELESS = no server-side session
IF_REQUIRED = create session only if required
ALWAYS = always create session
NEVER = never create, but use existing session
```

Spring Security stores the security context in the HTTP session by default, but this can be customized for stateless or horizontally scaled systems. ([Home][9])

---

## 31. What is authorization in Spring Security?

**Answer:**
Authorization decides whether an authenticated user can access a resource.

Example:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.requestMatchers("/api/users/**").hasAnyRole("USER", "ADMIN")
.anyRequest().authenticated()
```

Spring Security supports authorization at request level and method level. Request-level authorization lets you define rules like `/admin` requiring a specific authority. ([Home][4])

---

## 32. What is `permitAll()`?

**Answer:**
`permitAll()` allows access without authentication.

Example:

```java
.requestMatchers("/api/auth/login", "/api/auth/register").permitAll()
```

Use it for:

```text
login
register
swagger
health check
public APIs
```

---

## 33. What is `authenticated()`?

**Answer:**
`authenticated()` allows access only if the user is logged in.

Example:

```java
.anyRequest().authenticated()
```

This means every request except explicitly permitted ones must have valid authentication.

---

## 34. What is `hasRole()`?

**Answer:**
`hasRole()` checks if the user has a role.

Example:

```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
```

Internally it checks:

```text
ROLE_ADMIN
```

So database authority should usually be stored as:

```text
ROLE_ADMIN
```

or converted to this format.

---

## 35. What is `hasAuthority()`?

**Answer:**
`hasAuthority()` checks exact authority string.

Example:

```java
.requestMatchers("/api/report/**").hasAuthority("REPORT_READ")
```

Difference:

```java
.hasRole("ADMIN")              // checks ROLE_ADMIN
.hasAuthority("ROLE_ADMIN")    // checks ROLE_ADMIN
.hasAuthority("ADMIN")         // checks ADMIN
```

---

## 36. What is method-level security?

**Answer:**
Method-level security secures service methods using annotations.

Example:

```java
@Service
public class PostService {

    @PreAuthorize("hasRole('ADMIN')")
    public void deletePost(Long postId) {
        // delete post
    }
}
```

Enable it:

```java
@EnableMethodSecurity
@Configuration
public class SecurityConfig {
}
```

Spring Security’s main method-level authorization style is annotation-based, using annotations on methods, classes, or interfaces. ([Home][10])

---

## 37. What is `@PreAuthorize`?

**Answer:**
`@PreAuthorize` checks permission before method execution.

Example:

```java
@PreAuthorize("hasRole('ADMIN')")
public List<UserDto> getAllUsers() {
    return userService.getAllUsers();
}
```

Another example:

```java
@PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
public UserDto getUser(Long userId) {
    return userService.getUser(userId);
}
```

---

## 38. What is `@PostAuthorize`?

**Answer:**
`@PostAuthorize` checks permission after method execution.

Example:

```java
@PostAuthorize("returnObject.email == authentication.name")
public UserDto getProfile(Long id) {
    return userService.getProfile(id);
}
```

Use case: when authorization depends on returned object.

---

## 39. What is CSRF?

**Answer:**
CSRF means Cross-Site Request Forgery.

Example attack:

```text
User is logged into bank.com
User opens malicious.com
malicious.com sends POST request to bank.com
Browser automatically sends bank cookies
```

Spring Security enables CSRF protection by default for unsafe methods such as POST. ([Home][8])

---

## 40. What is CORS?

**Answer:**
CORS means Cross-Origin Resource Sharing. It controls whether frontend from one origin can call backend from another origin.

Example:

```text
Frontend: http://localhost:3000
Backend : http://localhost:8080
```

CORS configuration:

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("http://localhost:3000"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    config.setAllowCredentials(true);

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
}
```

Security config:

```java
http.cors(Customizer.withDefaults());
```

CORS should be processed before Spring Security because preflight requests usually do not contain cookies like `JSESSIONID`; otherwise Spring Security may reject the request as unauthenticated. ([Home][11])

---

## 41. What is the difference between CSRF and CORS?

| Topic      | CSRF                                    | CORS                              |
| ---------- | --------------------------------------- | --------------------------------- |
| Full form  | Cross-Site Request Forgery              | Cross-Origin Resource Sharing     |
| Purpose    | Prevent unwanted state-changing request | Control cross-origin access       |
| Related to | Cookies/session                         | Browser origin policy             |
| Example    | Malicious POST using existing login     | React app calling Spring Boot API |
| Protection | CSRF token                              | CORS headers                      |

Simple memory trick:

```text
CSRF = who is sending unwanted request?
CORS = which frontend origin is allowed?
```

---

## 42. What are security headers?

**Answer:**
Security headers protect web applications from browser-based attacks.

Examples:

```text
X-Content-Type-Options
X-Frame-Options
Content-Security-Policy
Strict-Transport-Security
```

Spring Security supports security HTTP response headers to improve web application security. ([Home][12])

Example:

```java
http.headers(headers -> headers
    .frameOptions(frame -> frame.sameOrigin())
);
```

---

## 43. What is clickjacking protection?

**Answer:**
Clickjacking is an attack where your page is loaded inside another site’s iframe to trick the user.

Protection:

```http
X-Frame-Options: DENY
```

or:

```java
http.headers(headers -> headers
    .frameOptions(frame -> frame.deny())
);
```

For H2 console during development:

```java
http.headers(headers -> headers
    .frameOptions(frame -> frame.sameOrigin())
);
```

---

## 44. What is logout in Spring Security?

**Answer:**
Logout clears authentication and invalidates session in stateful applications.

Example:

```java
http.logout(logout -> logout
    .logoutUrl("/logout")
    .logoutSuccessUrl("/login?logout")
);
```

For JWT stateless apps, logout is different because the server usually does not store the token.

JWT logout options:

```text
1. Delete token on frontend.
2. Use short token expiry.
3. Maintain token blacklist.
4. Use refresh-token rotation.
```

---

## 45. What is session fixation?

**Answer:**
Session fixation is an attack where attacker forces user to use a known session ID, then hijacks that session after login.

Spring Security provides session-management support to protect against session-related attacks.

Example:

```java
http.sessionManagement(session -> session
    .sessionFixation(sessionFixation -> sessionFixation.migrateSession())
);
```

For JWT stateless APIs, session fixation is usually not applicable because server sessions are not used.

---

## 46. What is exception handling in Spring Security?

**Answer:**
Spring Security has two important exception handling concepts:

```text
AuthenticationEntryPoint -> handles unauthenticated users
AccessDeniedHandler     -> handles authenticated users without permission
```

Example:

```text
401 Unauthorized = user not logged in / invalid token
403 Forbidden    = user logged in but no permission
```

Example:

```java
http.exceptionHandling(ex -> ex
    .authenticationEntryPoint((request, response, authException) ->
        response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Unauthorized")
    )
    .accessDeniedHandler((request, response, accessDeniedException) ->
        response.sendError(HttpServletResponse.SC_FORBIDDEN, "Forbidden")
    )
);
```

---

## 47. What is the difference between 401 and 403?

| Status           | Meaning                       | Example                        |
| ---------------- | ----------------------------- | ------------------------------ |
| 401 Unauthorized | Not authenticated             | Missing/invalid JWT            |
| 403 Forbidden    | Authenticated but not allowed | USER tries to access ADMIN API |

Example:

```text
No token -> 401
Valid USER token but accessing /admin -> 403
```

---

## 48. How do you test Spring Security APIs?

**Answer:**
Use `spring-security-test` with MockMvc.

Example:

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    void shouldAllowAdmin() throws Exception {
        mockMvc.perform(get("/api/admin/users")
                .with(user("admin").roles("ADMIN")))
            .andExpect(status().isOk());
    }
}
```

For POST with CSRF:

```java
mockMvc.perform(post("/api/posts")
        .with(user("danish").roles("USER"))
        .with(csrf()))
    .andExpect(status().isCreated());
```

Spring Security test support integrates with Spring MVC Test through `springSecurity()`, request post-processors like `user()`, and CSRF helpers. ([Home][13]) ([Home][14])

---

## 49. How do you secure Swagger in Spring Security?

**Answer:**
In development, you may allow Swagger publicly:

```java
.requestMatchers(
    "/swagger-ui/**",
    "/v3/api-docs/**"
).permitAll()
```

In production, better options are:

```text
1. Disable Swagger.
2. Protect Swagger with ADMIN role.
3. Expose Swagger only inside VPN/internal network.
```

Example:

```java
.requestMatchers("/swagger-ui/**", "/v3/api-docs/**").hasRole("ADMIN")
```

---

## 50. What are Spring Security best practices?

**Answer:**

```text
1. Store passwords using BCrypt/Argon2/PBKDF2, never plain text.
2. Use HTTPS in production.
3. Keep JWT expiry short.
4. Do not store sensitive data inside JWT.
5. Store JWT secret/private key securely.
6. Use role + permission-based authorization.
7. Use method-level security for business rules.
8. Configure CORS strictly.
9. Disable CSRF only when truly stateless and non-cookie based.
10. Return proper 401/403 responses.
11. Do not expose actuator endpoints publicly.
12. Protect Swagger in production.
13. Add security tests.
14. Follow least privilege principle.
15. Keep Spring Boot/Spring Security dependencies updated.
```

---

# Most Important Interview Code: JWT-Based Security Config

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final UserDetailsService userDetailsService;

    public SecurityConfig(JwtAuthenticationFilter jwtAuthenticationFilter,
                          UserDetailsService userDetailsService) {
        this.jwtAuthenticationFilter = jwtAuthenticationFilter;
        this.userDetailsService = userDetailsService;
    }

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .cors(Customizer.withDefaults())
            .sessionManagement(session ->
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .authenticationProvider(authenticationProvider())
            .addFilterBefore(jwtAuthenticationFilter,
                    UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    AuthenticationProvider authenticationProvider() {
        DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
        provider.setUserDetailsService(userDetailsService);
        provider.setPasswordEncoder(passwordEncoder());
        return provider;
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

# Interview Explanation: JWT Login Flow

```text
1. User sends email/password to /api/auth/login.
2. Controller creates UsernamePasswordAuthenticationToken.
3. AuthenticationManager authenticates it.
4. UserDetailsService loads user from DB.
5. PasswordEncoder verifies password.
6. If valid, JWT is generated.
7. Client stores JWT.
8. For next request, client sends:
   Authorization: Bearer <token>
9. JwtAuthenticationFilter validates token.
10. SecurityContextHolder is populated.
11. Authorization rules are checked.
12. Controller is executed.
```

---

# Very Common Interview Tricky Questions

## Why do we pass `null` as credentials in JWT authentication?

```java
new UsernamePasswordAuthenticationToken(
    userDetails,
    null,
    userDetails.getAuthorities()
);
```

Because after JWT validation, password is not required. The user is already authenticated through the token.

---

## Why should JWT not contain password?

Because JWT payload can be decoded easily. It is signed, not encrypted by default.

Bad:

```json
{
  "email": "user@gmail.com",
  "password": "secret"
}
```

Good:

```json
{
  "sub": "user@gmail.com",
  "roles": ["ROLE_USER"],
  "exp": 1710003600
}
```

---

## Why is `csrf().disable()` common in REST APIs?

Because stateless REST APIs usually do not depend on browser cookies for authentication. They use `Authorization: Bearer <token>`. But if your app uses cookies, disabling CSRF can be risky.

---

## Why do we use `@EnableMethodSecurity`?

Because URL-level security is not always enough.

Example:

```java
@PreAuthorize("#postOwnerEmail == authentication.name or hasRole('ADMIN')")
public void updatePost(String postOwnerEmail, PostDto postDto) {
}
```

This protects business logic even if another controller accidentally calls the method.

---

## Why is `hasRole("ADMIN")` not same as `hasAuthority("ADMIN")`?

Because:

```java
hasRole("ADMIN")
```

checks:

```text
ROLE_ADMIN
```

But:

```java
hasAuthority("ADMIN")
```

checks exactly:

```text
ADMIN
```

---

# 2-Minute Revision Summary

```text
Spring Security = authentication + authorization + protection.

Main flow:
Request -> FilterChainProxy -> SecurityFilterChain -> Filters -> Controller

Important classes:
SecurityContextHolder -> stores current user
Authentication -> user identity + authorities
UserDetails -> user info for Spring Security
UserDetailsService -> loads user from DB
PasswordEncoder -> validates password hash
AuthenticationManager -> authenticates login request
AuthenticationProvider -> actual authentication logic

JWT flow:
Login -> authenticate username/password -> generate token
Next request -> JWT filter validates token -> set SecurityContext
Then authorization rules are checked.

For REST APIs:
Use STATELESS session
Usually disable CSRF if token is in Authorization header
Configure CORS properly
Use BCrypt
Protect Swagger/Actuator in production
Use @PreAuthorize for service-level security
```

---

# What you should prepare deeply for interview

For your profile, focus especially on these 15:

```text
1. Spring Security filter chain
2. SecurityContextHolder
3. Authentication vs Authorization
4. UserDetails and UserDetailsService
5. AuthenticationManager and AuthenticationProvider
6. PasswordEncoder and BCrypt
7. JWT login flow
8. Stateless authentication
9. CSRF disable reason
10. CORS issue with React frontend
11. 401 vs 403
12. hasRole vs hasAuthority
13. Method-level security
14. Custom JWT filter
15. Security best practices in production
```

Master these properly and you can confidently explain Spring Security in a real backend interview.

[1]: https://docs.spring.io/spring-security/reference/index.html "Spring Security :: Spring Security"
[2]: https://docs.spring.io/spring-security/reference/servlet/architecture.html "Architecture :: Spring Security"
[3]: https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html "Servlet Authentication Architecture :: Spring Security"
[4]: https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html "Authorize HttpServletRequests :: Spring Security"
[5]: https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html?utm_source=chatgpt.com "Password Storage :: Spring Security"
[6]: https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/basic.html "Basic Authentication :: Spring Security"
[7]: https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html "OAuth 2.0 Resource Server :: Spring Security"
[8]: https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html?utm_source=chatgpt.com "Cross Site Request Forgery (CSRF)"
[9]: https://docs.spring.io/spring-security/reference/servlet/authentication/session-management.html "Authentication Persistence and Session Management :: Spring Security"
[10]: https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html?utm_source=chatgpt.com "Method Security"
[11]: https://docs.spring.io/spring-security/reference/servlet/integrations/cors.html?utm_source=chatgpt.com "CORS :: Spring Security"
[12]: https://docs.spring.io/spring-security/reference/servlet/exploits/headers.html?utm_source=chatgpt.com "Security HTTP Response Headers"
[13]: https://docs.spring.io/spring-security/reference/servlet/test/mockmvc/setup.html?utm_source=chatgpt.com "Setting Up MockMvc and Spring Security"
[14]: https://docs.spring.io/spring-security/reference/servlet/test/mockmvc/csrf.html?utm_source=chatgpt.com "Testing with CSRF Protection"
