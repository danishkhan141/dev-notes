# Exception Handling Interview Preparation — 80 Questions

**Focused on your profile:** Java Backend, Spring Boot, REST API, Microservices, Hibernate/JPA, Spring Security, Spring Batch, Kafka, React, Docker, Kubernetes, Jenkins, AWS.

This is **not Core Java exception handling** like `try-catch-finally`, checked/unchecked only. This is **real project-level exception handling**, which interviewers expect from a 7-year Java backend developer.

---

# 1. How to Answer Exception Handling in Interviews

Use this structure:

> “In enterprise applications, exception handling should not only catch errors. It should convert technical exceptions into meaningful API responses, log the root cause, avoid exposing internal details, rollback transactions when required, and support monitoring/debugging through trace ID or correlation ID.”

A good answer should cover:

| Area          | What interviewer expects                               |
| ------------- | ------------------------------------------------------ |
| API layer     | Proper HTTP status code and response body              |
| Service layer | Business exceptions and rollback handling              |
| DB layer      | Constraint, duplicate, timeout, transaction exceptions |
| Microservices | Timeout, retry, fallback, circuit breaker              |
| Security      | 401, 403, JWT expired/invalid                          |
| Batch         | Retry, skip, restartability                            |
| Kafka         | Retry, DLT, poison message handling                    |
| DevOps        | Logs, health checks, alerts                            |

---

# 2. Standard Spring Boot Exception Handling Example

This is the kind of code you should confidently explain.

## Error Response DTO

```java
public class ApiErrorResponse {

    private int status;
    private String error;
    private String message;
    private String path;
    private String timestamp;

    public ApiErrorResponse(int status, String error, String message, String path) {
        this.status = status;
        this.error = error;
        this.message = message;
        this.path = path;
        this.timestamp = java.time.LocalDateTime.now().toString();
    }

    // getters and setters
}
```

## Custom Exception

```java
public class ResourceNotFoundException extends RuntimeException {

    public ResourceNotFoundException(String resourceName, String fieldName, Object fieldValue) {
        super(resourceName + " not found with " + fieldName + " : " + fieldValue);
    }
}
```

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiErrorResponse> handleResourceNotFound(
            ResourceNotFoundException ex,
            HttpServletRequest request) {

        ApiErrorResponse response = new ApiErrorResponse(
                HttpStatus.NOT_FOUND.value(),
                "Not Found",
                ex.getMessage(),
                request.getRequestURI()
        );

        return new ResponseEntity<ApiErrorResponse>(response, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationException(
            MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<String, String>();

        ex.getBindingResult().getFieldErrors().forEach(error ->
                errors.put(error.getField(), error.getDefaultMessage())
        );

        return new ResponseEntity<Map<String, String>>(errors, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiErrorResponse> handleGenericException(
            Exception ex,
            HttpServletRequest request) {

        ApiErrorResponse response = new ApiErrorResponse(
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                "Internal Server Error",
                "Something went wrong. Please contact support.",
                request.getRequestURI()
        );

        return new ResponseEntity<ApiErrorResponse>(
                response,
                HttpStatus.INTERNAL_SERVER_ERROR
        );
    }
}
```

Interview explanation:

> “I use `@RestControllerAdvice` for centralized exception handling. Controller should not contain repetitive try-catch blocks. Custom exceptions are thrown from service layer and converted into meaningful HTTP responses by global handler.”

---

# 3. REST API + Spring Boot Exception Handling Questions

| No. | Question                                                                   | Interview Answer                                                                                                                                                                               |
| --: | -------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|   1 | What is the best way to handle exceptions in Spring Boot REST APIs?        | Use centralized exception handling with `@RestControllerAdvice` and `@ExceptionHandler`. It avoids duplicate try-catch blocks in controllers and gives consistent API responses.               |
|   2 | What is `@ControllerAdvice`?                                               | It is a global component used to handle exceptions across multiple controllers. For REST APIs, we usually use `@RestControllerAdvice`, which combines `@ControllerAdvice` and `@ResponseBody`. |
|   3 | Difference between `@ControllerAdvice` and `@RestControllerAdvice`?        | `@ControllerAdvice` is generic and may return views or responses. `@RestControllerAdvice` directly returns JSON/XML response bodies, so it is preferred in REST APIs.                          |
|   4 | Why should we avoid try-catch in every controller method?                  | It creates duplicate code, inconsistent responses, and difficult maintenance. Centralized exception handling is cleaner and professional.                                                      |
|   5 | What HTTP status will you return for resource not found?                   | `404 NOT_FOUND`. Example: post ID or user ID does not exist.                                                                                                                                   |
|   6 | What HTTP status for validation failure?                                   | `400 BAD_REQUEST`. Example: email is invalid or required field is missing.                                                                                                                     |
|   7 | What HTTP status for duplicate record?                                     | Usually `409 CONFLICT`. Example: duplicate email during user registration.                                                                                                                     |
|   8 | What HTTP status for unauthorized access?                                  | `401 UNAUTHORIZED`. It means the user is not authenticated or token is invalid.                                                                                                                |
|   9 | What HTTP status for forbidden access?                                     | `403 FORBIDDEN`. It means the user is authenticated but does not have permission.                                                                                                              |
|  10 | What HTTP status for internal server error?                                | `500 INTERNAL_SERVER_ERROR`. But we should not expose stack trace or DB details to the client.                                                                                                 |
|  11 | How do you handle invalid request body?                                    | Handle `HttpMessageNotReadableException`. It occurs when JSON is malformed or request body cannot be parsed.                                                                                   |
|  12 | How do you handle missing request parameter?                               | Handle `MissingServletRequestParameterException`. Return `400 BAD_REQUEST`.                                                                                                                    |
|  13 | How do you handle invalid path variable type?                              | Handle `MethodArgumentTypeMismatchException`. Example: API expects numeric ID but receives string.                                                                                             |
|  14 | How do you handle validation errors in Spring Boot?                        | Use `@Valid` or `@Validated` and handle `MethodArgumentNotValidException` globally.                                                                                                            |
|  15 | How do you create a common error response format?                          | Create an error DTO containing status, error, message, timestamp, path, and optionally traceId.                                                                                                |
|  16 | Why should we not return exception message directly to client?             | It may expose internal technical details like table names, SQL errors, package names, or security-sensitive information.                                                                       |
|  17 | What is the role of logging in exception handling?                         | Client gets clean message, but server logs should contain detailed exception stack trace for debugging.                                                                                        |
|  18 | Should we catch generic `Exception`?                                       | At global handler level yes, as a fallback. But in service logic, avoid catching generic exceptions unless needed.                                                                             |
|  19 | What is the difference between business exception and technical exception? | Business exception means rule violation, like “user already exists”. Technical exception means system failure, like DB down or timeout.                                                        |
|  20 | How do you handle file upload exceptions?                                  | Handle `MaxUploadSizeExceededException`, invalid file type, missing file, and storage failure separately.                                                                                      |

---

# 4. Service Layer Exception Handling Questions

| No. | Question                                                                  | Interview Answer                                                                                                                             |
| --: | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
|  21 | Where should custom business exceptions be thrown?                        | Usually from service layer because business rules live there. Controller should only receive request and return response.                    |
|  22 | Should service layer return null or throw exception if data is not found? | Prefer throwing custom exception like `ResourceNotFoundException`. Returning null may cause `NullPointerException` later.                    |
|  23 | How do you handle duplicate email during registration?                    | First check existence using repository. If found, throw custom `DuplicateResourceException` or handle DB unique constraint exception.        |
|  24 | Should you catch exception inside service and return false?               | Usually no. It hides the real problem. Better throw meaningful exception and handle globally.                                                |
|  25 | What happens if exception occurs inside `@Transactional` method?          | For unchecked exceptions, Spring rolls back the transaction by default. Checked exceptions do not rollback unless configured.                |
|  26 | How to rollback transaction for checked exception?                        | Use `@Transactional(rollbackFor = Exception.class)` or specific checked exception class.                                                     |
|  27 | What is the danger of catching exception inside transactional method?     | If you catch exception and do not rethrow it, Spring may think method completed successfully and transaction may commit.                     |
|  28 | How do you handle partial update failure?                                 | Use transaction boundary properly. Either complete all DB operations successfully or rollback everything.                                    |
|  29 | Should external API calls be inside DB transaction?                       | Generally avoid long external calls inside transaction because it holds DB resources. Prefer separate flow or saga pattern in microservices. |
|  30 | What is idempotency in exception handling?                                | If retry happens due to failure, repeated request should not create duplicate data. This is important in payment, order, Kafka, and APIs.    |

Example:

```java
@Transactional(rollbackFor = Exception.class)
public UserDto registerUser(UserDto userDto) throws Exception {

    if (userRepository.existsByEmail(userDto.getEmail())) {
        throw new DuplicateResourceException("Email already exists");
    }

    User user = modelMapper.map(userDto, User.class);
    User savedUser = userRepository.save(user);

    return modelMapper.map(savedUser, UserDto.class);
}
```

Interview explanation:

> “If a business rule fails, I throw custom exception. If a database operation fails, transaction should rollback. I avoid swallowing exceptions inside transactional methods.”

---

# 5. Hibernate / JPA / Database Exception Handling Questions

| No. | Question                                                                 | Interview Answer                                                                                                                      |
| --: | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
|  31 | What exception occurs when entity is not found?                          | Commonly `EntityNotFoundException` or custom `ResourceNotFoundException` from service layer.                                          |
|  32 | What is `DataIntegrityViolationException`?                               | It is Spring’s wrapper for DB constraint violations like unique key, foreign key, not-null violation.                                 |
|  33 | How do you handle unique constraint violation?                           | Return `409 CONFLICT` with user-friendly message like “Email already exists”.                                                         |
|  34 | What is `LazyInitializationException`?                                   | It occurs when lazy-loaded data is accessed outside Hibernate session.                                                                |
|  35 | How to avoid `LazyInitializationException`?                              | Use proper fetch join, DTO projection, transaction boundary, or initialize required data inside service layer.                        |
|  36 | What is `NonUniqueResultException`?                                      | It occurs when query expects one result but DB returns multiple records.                                                              |
|  37 | What is `OptimisticLockException`?                                       | It occurs when two transactions try to update same record and version conflict happens.                                               |
|  38 | How do you handle optimistic locking failure?                            | Return `409 CONFLICT` and ask user to refresh data or retry.                                                                          |
|  39 | What is `PessimisticLockException`?                                      | It occurs when DB lock cannot be acquired. Usually due to concurrent access or timeout.                                               |
|  40 | What is `QueryTimeoutException`?                                         | It occurs when query takes more time than configured timeout.                                                                         |
|  41 | How do you handle DB connection failure?                                 | Log it, return `503 SERVICE_UNAVAILABLE`, monitor connection pool, and configure retry only where safe.                               |
|  42 | How do you handle `ConstraintViolationException`?                        | For Bean Validation, return `400`. For DB constraint violation, usually `409` or `400` depending on business case.                    |
|  43 | How do you handle DB2/Oracle SQL exceptions professionally?              | Do not expose SQLCODE or ORA error to frontend. Log technical details internally and return business-friendly error response.         |
|  44 | What is the difference between validation error and DB constraint error? | Validation error is caught before DB call. DB constraint error happens when database rejects data. Both should be handled gracefully. |
|  45 | How do you handle transaction deadlock?                                  | Retry may be applied for safe idempotent operations. Also optimize transaction duration and indexing.                                 |

Example for duplicate record:

```java
@ExceptionHandler(DataIntegrityViolationException.class)
public ResponseEntity<ApiErrorResponse> handleDataIntegrityViolation(
        DataIntegrityViolationException ex,
        HttpServletRequest request) {

    ApiErrorResponse response = new ApiErrorResponse(
            HttpStatus.CONFLICT.value(),
            "Conflict",
            "Duplicate or invalid data found.",
            request.getRequestURI()
    );

    return new ResponseEntity<ApiErrorResponse>(response, HttpStatus.CONFLICT);
}
```

---

# 6. Microservices Exception Handling Questions

| No. | Question                                              | Interview Answer                                                                                                                                                          |
| --: | ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  46 | How is exception handling different in microservices? | Failure can happen across network, service dependency, database, message broker, or timeout. We need timeout, retry, fallback, circuit breaker, and graceful degradation. |
|  47 | What if one microservice is down?                     | API should not hang forever. Configure timeout, circuit breaker, fallback response, and proper error status like `503`.                                                   |
|  48 | What is circuit breaker?                              | It prevents repeated calls to failing service. After failures cross threshold, circuit opens and fallback is executed.                                                    |
|  49 | What is fallback method?                              | A backup method executed when actual service call fails. Example: return cached data or user-friendly message.                                                            |
|  50 | When should retry be used?                            | Retry should be used for transient failures like network timeout. Avoid retry for validation errors or duplicate record errors.                                           |
|  51 | What is the danger of retry?                          | Retry can create duplicate requests, increase load, or worsen outage if not controlled.                                                                                   |
|  52 | Difference between retry and circuit breaker?         | Retry attempts same operation again. Circuit breaker stops calling failing service for some time.                                                                         |
|  53 | What is timeout handling?                             | Every external call should have timeout. Without timeout, threads may remain blocked and application can become slow or down.                                             |
|  54 | How do you handle Feign client exceptions?            | Use custom `ErrorDecoder` to convert HTTP errors into meaningful exceptions.                                                                                              |
|  55 | How do you handle WebClient errors?                   | Use `onStatus()` to map HTTP status to custom exceptions.                                                                                                                 |
|  56 | What is bulkhead pattern?                             | It limits resources for a dependency so one failing service does not consume all threads/connections.                                                                     |
|  57 | How do you handle partial failure in microservices?   | Use saga pattern, compensation transaction, event-driven design, or eventual consistency.                                                                                 |
|  58 | What is graceful degradation?                         | Application continues with limited functionality instead of complete failure. Example: if recommendation service is down, show default posts.                             |
|  59 | How do you propagate errors between services?         | Use standard error response with status, message, errorCode, traceId. Avoid sending stack trace.                                                                          |
|  60 | Why is correlation ID important?                      | It helps trace one request across multiple microservices logs.                                                                                                            |

Example with Resilience4j:

```java
@CircuitBreaker(name = "postService", fallbackMethod = "postFallback")
@Retry(name = "postService")
public PostDto getPostById(Long postId) {
    return restTemplate.getForObject(
            "http://post-service/api/posts/" + postId,
            PostDto.class
    );
}

public PostDto postFallback(Long postId, Exception ex) {
    PostDto dto = new PostDto();
    dto.setTitle("Post service is currently unavailable");
    return dto;
}
```

Interview explanation:

> “In microservices, exception handling is not only try-catch. We need timeout, retry, circuit breaker, fallback, proper status code, and traceability.”

---

# 7. Spring Security Exception Handling Questions

| No. | Question                                                                | Interview Answer                                                                                                                                  |
| --: | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
|  61 | Difference between 401 and 403?                                         | `401` means unauthenticated. `403` means authenticated but not authorized.                                                                        |
|  62 | Which component handles authentication failure in Spring Security?      | `AuthenticationEntryPoint`. It is used when user is not authenticated or JWT is invalid.                                                          |
|  63 | Which component handles authorization failure?                          | `AccessDeniedHandler`. It is used when user does not have required role or permission.                                                            |
|  64 | Why does `@ControllerAdvice` not catch some Spring Security exceptions? | Because security filters execute before controller layer. So authentication/authorization exceptions should be handled in Spring Security config. |
|  65 | How do you handle expired JWT token?                                    | Catch JWT expiration in JWT filter and return `401 UNAUTHORIZED` with message like “Token expired”.                                               |
|  66 | How do you handle invalid JWT token?                                    | Return `401 UNAUTHORIZED`. Do not expose parsing details.                                                                                         |
|  67 | How do you handle bad login credentials?                                | Return `401 UNAUTHORIZED` with generic message like “Invalid username or password”.                                                               |
|  68 | Why should login error message be generic?                              | To avoid user enumeration. We should not say “email exists but password wrong”.                                                                   |
|  69 | What happens if user accesses admin API without admin role?             | Return `403 FORBIDDEN`.                                                                                                                           |
|  70 | How do you handle CORS-related errors?                                  | Configure CORS properly in Spring Security and controller. Frontend should handle browser-level CORS failure gracefully.                          |

Example:

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

        response.getWriter().write(
                "{\"status\":401,\"error\":\"Unauthorized\",\"message\":\"Invalid or expired token\"}"
        );
    }
}
```

Interview explanation:

> “Security exceptions happen before controller, so I handle authentication errors using `AuthenticationEntryPoint` and authorization errors using `AccessDeniedHandler`.”

---

# 8. Spring Batch Exception Handling Questions

| No. | Question                                             | Interview Answer                                                                                                  |
| --: | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
|  71 | How do you handle exceptions in Spring Batch?        | Use skip, retry, listeners, transaction management, and restartability.                                           |
|  72 | Difference between skip and retry?                   | Retry tries the same item again. Skip ignores failed item and continues processing.                               |
|  73 | When should retry be used in batch?                  | For temporary failures like DB timeout, network issue, temporary file access issue.                               |
|  74 | When should skip be used?                            | For bad records like invalid data format, missing mandatory fields, or business validation failure.               |
|  75 | What is skip limit?                                  | Maximum number of records allowed to be skipped before job fails.                                                 |
|  76 | What happens when skip limit exceeds?                | Step fails and job status becomes failed.                                                                         |
|  77 | What is `SkipListener`?                              | It is used to log skipped records from read, process, or write phase.                                             |
|  78 | What is `RetryListener`?                             | It is used to track retry attempts and failures.                                                                  |
|  79 | How do you make batch job restartable after failure? | Store metadata in Spring Batch tables and design reader/writer properly so job resumes from last committed chunk. |
|  80 | What is the role of transaction in chunk processing? | Each chunk is processed in a transaction. If write fails, that chunk rolls back.                                  |

Example:

```java
@Bean
public Step importUserStep() {
    return stepBuilderFactory.get("importUserStep")
            .<UserCsvDto, User>chunk(100)
            .reader(userReader())
            .processor(userProcessor())
            .writer(userWriter())
            .faultTolerant()
            .skip(InvalidUserRecordException.class)
            .skipLimit(50)
            .retry(DeadlockLoserDataAccessException.class)
            .retryLimit(3)
            .listener(skipListener())
            .build();
}
```

Interview explanation:

> “In batch jobs, we should not fail the entire job for one bad record. For business-invalid records, I use skip. For temporary failures, I use retry. For serious failures, job should fail and be restartable.”

---

# 9. Kafka Exception Handling Questions

| Question                                          | Interview Answer                                                                                    |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| What happens if Kafka consumer throws exception?  | Message may be retried depending on error handler and offset commit strategy.                       |
| What is poison message?                           | A message that always fails during processing because of bad data or unsupported format.            |
| How do you handle poison messages?                | Retry limited times, then send message to Dead Letter Topic.                                        |
| What is Dead Letter Topic?                        | A separate Kafka topic where failed messages are stored for later analysis or reprocessing.         |
| Should retry be infinite in Kafka?                | No. Infinite retry can block partition processing. Use limited retry and DLT.                       |
| How do you handle deserialization exception?      | Configure deserializer error handling and route bad messages to DLT or error topic.                 |
| What is idempotent consumer?                      | Consumer should process duplicate messages safely without creating duplicate business records.      |
| How do you avoid duplicate processing?            | Use unique event ID, DB constraint, processed-event table, or idempotent logic.                     |
| What if DB is down while consuming Kafka message? | Retry with backoff. If still failing, send to DLT or stop consumer depending on criticality.        |
| Should Kafka listener catch all exceptions?       | Usually let framework error handler manage retry/DLT. Catch only when you can recover meaningfully. |

Example:

```java
@KafkaListener(topics = "user-events", groupId = "user-service")
public void consumeUserEvent(UserEvent event) {

    if (event.getUserId() == null) {
        throw new InvalidEventException("User id is missing");
    }

    userEventService.process(event);
}
```

Professional explanation:

> “For Kafka, exception handling should be designed with retry, backoff, DLT, idempotency, and monitoring. We should not silently consume failed messages because data loss can happen.”

---

# 10. React Frontend Exception Handling Questions

| Question                                               | Interview Answer                                                                  |
| ------------------------------------------------------ | --------------------------------------------------------------------------------- |
| How does React handle API errors?                      | Usually using `try-catch`, Axios interceptors, and user-friendly error messages.  |
| How do you handle 401 in React?                        | Clear token and redirect user to login page.                                      |
| How do you handle 403 in React?                        | Show permission denied page or message.                                           |
| How do you show backend validation errors?             | Display field-level errors returned by backend.                                   |
| What is Error Boundary in React?                       | A component that catches rendering errors in child components.                    |
| Does Error Boundary catch API errors?                  | No. API errors should be handled in promise catch block or async/await try-catch. |
| How do you handle network error?                       | Show message like “Server not reachable, please try again later.”                 |
| How do you handle timeout?                             | Configure Axios timeout and show proper message.                                  |
| How do you handle backend 500 error?                   | Show generic message. Do not show technical stack trace.                          |
| Why should frontend and backend agree on error format? | It makes error handling consistent and easy to display in UI.                     |

Example:

```javascript
import axios from "axios";

const api = axios.create({
  baseURL: "http://localhost:8080/api",
  timeout: 10000
});

api.interceptors.response.use(
  response => response,
  error => {
    if (error.response && error.response.status === 401) {
      localStorage.removeItem("token");
      window.location.href = "/login";
    }

    return Promise.reject(error);
  }
);

export default api;
```

---

# 11. Docker, Kubernetes, Jenkins, AWS Exception Handling Questions

These are not “Java exceptions”, but interviewers ask how your application behaves when failure happens in production.

| Question                                             | Interview Answer                                                                                                    |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| How do you debug exception in Docker container?      | Check container logs using `docker logs`, verify env variables, network, DB connectivity, and application config.   |
| What if Spring Boot app fails inside Docker?         | Check logs, port mapping, profile, DB URL, secrets, memory, and health endpoint.                                    |
| How do you handle application failure in Kubernetes? | Use liveness and readiness probes. Kubernetes can restart unhealthy pods and avoid sending traffic to unready pods. |
| Difference between liveness and readiness probe?     | Liveness checks if app should be restarted. Readiness checks if app is ready to receive traffic.                    |
| How does Kubernetes help in exception handling?      | It does not handle code exception directly, but helps with self-healing, restart, scaling, and traffic routing.     |
| What if application starts but DB is down?           | Readiness probe should fail so traffic is not routed until DB connectivity is available.                            |
| How do you handle logs in Kubernetes?                | Write logs to stdout/stderr and collect using logging tools like ELK, CloudWatch, or Grafana Loki.                  |
| How do you handle Jenkins pipeline failure?          | Fail fast, show stage-level error, archive logs/reports, notify team, and avoid deploying broken build.             |
| How do you handle deployment failure?                | Use rollback strategy, previous image version, health checks, and deployment verification.                          |
| How do you monitor exceptions in AWS?                | Use CloudWatch logs, metrics, alarms, X-Ray tracing, and application-level logging.                                 |

---

# 12. Very Important Scenario-Based Questions

## Scenario 1: User ID not found

**Question:**
Your API `/api/users/10` is called but user does not exist. How will you handle it?

**Answer:**

> Repository returns empty result. Service layer throws `ResourceNotFoundException`. Global exception handler converts it into `404 NOT_FOUND` with clean error response.

```java
public UserDto getUserById(Long userId) {

    User user = userRepository.findById(userId)
            .orElseThrow(() ->
                    new ResourceNotFoundException("User", "id", userId)
            );

    return modelMapper.map(user, UserDto.class);
}
```

---

## Scenario 2: Duplicate email during signup

**Question:**
User tries to register with an already existing email. What will you return?

**Answer:**

> I will return `409 CONFLICT` because the request conflicts with existing data.

```java
if (userRepository.existsByEmail(userDto.getEmail())) {
    throw new DuplicateResourceException("Email already exists");
}
```

---

## Scenario 3: JWT token expired

**Question:**
Your JWT token is expired. Will `@ControllerAdvice` handle it?

**Answer:**

> Usually no, because JWT validation happens in Spring Security filter before controller. I will handle it inside JWT filter or through `AuthenticationEntryPoint` and return `401 UNAUTHORIZED`.

---

## Scenario 4: Downstream service is down

**Question:**
Post service is calling User service but User service is down. What will you do?

**Answer:**

> I will configure timeout, retry for transient errors, circuit breaker to stop repeated failed calls, and fallback method to return graceful response. I will also log the failure with correlation ID.

---

## Scenario 5: Kafka consumer fails while processing message

**Question:**
Kafka consumer gets invalid message and fails again and again. What will you do?

**Answer:**

> I will not retry infinitely. I will configure limited retries with backoff, then send the message to Dead Letter Topic for investigation and reprocessing.

---

## Scenario 6: Spring Batch gets one bad record in CSV

**Question:**
One record in CSV is invalid. Should complete job fail?

**Answer:**

> Not always. If business permits, I will skip that record using skip policy, log it using `SkipListener`, and continue. But if skip limit exceeds, job should fail.

---

# 13. Best Practices You Should Say in Interview

These points make your answer mature:

1. **Do not expose stack trace to client.**
2. **Use centralized exception handling.**
3. **Use meaningful HTTP status codes.**
4. **Create custom business exceptions.**
5. **Log technical details only on server side.**
6. **Return consistent error response format.**
7. **Use validation before DB operation.**
8. **Do not swallow exceptions inside transaction.**
9. **Use rollback rules carefully.**
10. **Use timeout, retry, circuit breaker in microservices.**
11. **Use DLT for Kafka poison messages.**
12. **Use skip/retry/restartability in Spring Batch.**
13. **Handle 401/403 separately in Spring Security.**
14. **Use traceId/correlationId for debugging.**
15. **Monitor exceptions using logs, metrics, alerts.**

---

# 14. Interview-Ready Final Answer

You can speak like this:

> “In my projects, I prefer centralized exception handling using `@RestControllerAdvice`. Controller methods should not have repeated try-catch blocks. Service layer throws custom business exceptions like `ResourceNotFoundException` or `DuplicateResourceException`, and global handler converts them into proper HTTP responses like 404, 400, 409, or 500.
>
> For database-related exceptions, I handle cases like unique constraint violation, lazy loading issue, transaction rollback, and query timeout. For Spring Security, I handle authentication failure using `AuthenticationEntryPoint` and authorization failure using `AccessDeniedHandler` because these exceptions occur before controller.
>
> In microservices, exception handling also includes timeout, retry, circuit breaker, fallback, and correlation ID. For Kafka, I use retry with backoff and Dead Letter Topic for poison messages. For Spring Batch, I use skip, retry, listeners, and restartability.
>
> My goal is to give clean response to client, proper rollback in backend, detailed logs for debugging, and stable production behavior.”

---

# 15. What You Should Revise First

For your profile, revise in this order:

1. `@RestControllerAdvice`, `@ExceptionHandler`
2. Custom exceptions in Spring Boot
3. HTTP status codes: 400, 401, 403, 404, 409, 500, 503
4. Validation exception handling
5. Transaction rollback rules
6. Hibernate/JPA exceptions
7. Spring Security 401/403 handling
8. Microservices retry, timeout, circuit breaker, fallback
9. Kafka retry and Dead Letter Topic
10. Spring Batch skip/retry/restartability

This much is enough to answer Exception Handling professionally in Spring Boot + Microservices interviews.
