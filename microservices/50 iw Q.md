Below are **Top 50 Microservices Interview Questions** explained in a professional, interview-ready way for your **Java + Spring Boot + Microservices** preparation.

Microservices are mainly about building an application as a group of **small, loosely coupled, independently deployable services**, usually organized around business capabilities. This idea is strongly associated with independent deployment, decentralized data ownership, and small service teams. ([martinfowler.com][1])

---

# Microservices Interview Questions — Top 50

## 1. What are Microservices?

**Answer:**
Microservices is an architectural style where a large application is divided into multiple small services. Each service owns a specific business capability and can be developed, deployed, and scaled independently.

**Example:**
For your blog application:

```text
user-service       -> handles users and authentication
post-service       -> handles posts
category-service   -> handles categories
comment-service    -> handles comments
file-service       -> handles image/file upload
api-gateway        -> single entry point for clients
```

---

## 2. What is the difference between Monolithic and Microservices architecture?

**Answer:**

| Monolithic                 | Microservices                    |
| -------------------------- | -------------------------------- |
| Single large application   | Multiple small services          |
| One codebase               | Multiple codebases               |
| One database usually       | Database per service preferred   |
| Deploy everything together | Deploy services independently    |
| Scaling whole app          | Scale only required service      |
| Simpler initially          | Better for large complex systems |

**Example:**
In monolith, user, post, comment, category modules are inside one Spring Boot app.
In microservices, each becomes a separate Spring Boot application.

---

## 3. Why do companies use Microservices?

**Answer:**
Companies use microservices for:

1. Independent deployment
2. Better scalability
3. Fault isolation
4. Technology flexibility
5. Faster team development
6. Easier maintenance for large systems

**Interview line:**
“Microservices are useful when the application is large, teams are separate, deployment frequency is high, and different modules need independent scaling.”

---

## 4. When should we not use Microservices?

**Answer:**
Microservices should not be used blindly. Avoid them when:

1. Application is small
2. Team is small
3. Domain is not clear
4. Deployment/DevOps maturity is low
5. Strong transaction consistency is required everywhere

**Important:**
Microservices solve scaling and team problems, but they introduce distributed system complexity.

---

## 5. What are the main characteristics of Microservices?

**Answer:**

1. Loosely coupled
2. Independently deployable
3. Business capability focused
4. Own database or data ownership
5. Decentralized governance
6. Fault isolated
7. Observable
8. Automated deployment friendly

---

## 6. What is loose coupling in Microservices?

**Answer:**
Loose coupling means one service should not be tightly dependent on another service’s internal logic, database, or implementation.

**Bad example:**

```text
post-service directly reads user-service database
```

**Good example:**

```text
post-service calls user-service API to fetch user details
```

**Interview line:**
“Services should communicate through well-defined APIs or events, not by sharing internal database tables.”

---

## 7. What is high cohesion in Microservices?

**Answer:**
High cohesion means a service should contain closely related business logic only.

**Example:**
`user-service` should handle:

```text
register user
login user
update profile
manage user roles
```

It should not handle:

```text
create post
upload image
process payment
```

---

## 8. How do you identify Microservice boundaries?

**Answer:**
Service boundaries are usually identified based on business capabilities and domain boundaries.

**Example for blog app:**

```text
User Management       -> user-service
Post Management       -> post-service
Category Management   -> category-service
Comment Management    -> comment-service
File Upload           -> file-service
Notification          -> notification-service
```

**Interview line:**
“I try to create services around business capabilities, not around technical layers like controller-service, repository-service, etc.”

---

## 9. What is Domain-Driven Design in Microservices?

**Answer:**
Domain-Driven Design, or DDD, helps design services based on business domains.

Important terms:

| Term            | Meaning                   |
| --------------- | ------------------------- |
| Domain          | Business area             |
| Subdomain       | Smaller business area     |
| Bounded Context | Clear boundary of a model |
| Entity          | Object with identity      |
| Value Object    | Object without identity   |
| Aggregate       | Group of related objects  |

**Example:**
In e-commerce:

```text
Order Context
Payment Context
Inventory Context
Shipping Context
```

Each can become a separate microservice.

---

## 10. What is a Bounded Context?

**Answer:**
A bounded context is a clear boundary where a particular domain model is valid.

**Example:**
The word `User` can mean different things in different services:

```text
auth-service      -> user means login credentials
order-service     -> user means customer placing order
support-service   -> user means person raising ticket
```

Each service can have its own model of `User`.

---

# Communication Between Microservices

Spring Cloud OpenFeign is commonly used in Spring Boot microservices as a declarative REST client: you define an interface, annotate it, and Spring creates the implementation. ([Home][2])

## 11. How do Microservices communicate with each other?

**Answer:**
Microservices communicate mainly in two ways:

### 1. Synchronous Communication

One service waits for another service’s response.

Examples:

```text
REST
gRPC
Feign Client
WebClient
```

### 2. Asynchronous Communication

One service publishes an event, and another service consumes it later.

Examples:

```text
Kafka
RabbitMQ
AWS SQS
```

---

## 12. What is synchronous communication?

**Answer:**
Synchronous communication means the caller waits for the response.

**Example:**

```text
post-service calls user-service to validate user
```

```java
@GetMapping("/posts/{id}")
public PostResponse getPost(@PathVariable Long id) {
    Post post = postRepository.findById(id).orElseThrow();
    UserDto user = userClient.getUser(post.getUserId());
    return new PostResponse(post.getTitle(), user.getName());
}
```

**Problem:**
If `user-service` is down, `post-service` may also fail.

---

## 13. What is asynchronous communication?

**Answer:**
Asynchronous communication means one service sends a message/event and does not wait for immediate response.

**Example:**

```text
order-service publishes OrderCreatedEvent
notification-service consumes event and sends email
```

```java
public record OrderCreatedEvent(Long orderId, String email) {}
```

**Benefits:**

1. Better performance
2. Loose coupling
3. Failure isolation
4. Better scalability

---

## 14. REST vs Messaging — when to use what?

**Answer:**

| REST                       | Messaging                  |
| -------------------------- | -------------------------- |
| Request-response needed    | Event-based communication  |
| Immediate response needed  | Delayed processing allowed |
| Simple to debug            | Better for decoupling      |
| Tighter runtime dependency | Looser dependency          |

**Example:**
Use REST when `post-service` needs user details immediately.
Use Kafka when `order-service` wants to notify other services about order creation.

---

## 15. What is Feign Client?

**Answer:**
Feign Client is a declarative REST client used to call another service easily in Spring Boot.

**Example:**

```java
@FeignClient(name = "user-service", path = "/users")
public interface UserClient {

    @GetMapping("/{id}")
    UserDto getUserById(@PathVariable Long id);
}
```

Usage:

```java
@Service
public class PostService {

    private final UserClient userClient;

    public PostService(UserClient userClient) {
        this.userClient = userClient;
    }

    public UserDto getPostOwner(Long userId) {
        return userClient.getUserById(userId);
    }
}
```

**Interview line:**
“Feign hides low-level HTTP client code and allows us to call other services using Java interfaces.”

---

## 16. What is RestTemplate?

**Answer:**
`RestTemplate` is a synchronous HTTP client in Spring.

```java
UserDto user = restTemplate.getForObject(
    "http://user-service/users/" + userId,
    UserDto.class
);
```

**Note:**
In modern Spring applications, `WebClient` or Feign is usually preferred.

---

## 17. What is WebClient?

**Answer:**
`WebClient` is a non-blocking reactive HTTP client.

```java
UserDto user = webClient.get()
        .uri("http://user-service/users/{id}", userId)
        .retrieve()
        .bodyToMono(UserDto.class)
        .block();
```

**Interview line:**
“WebClient is useful when we need non-blocking I/O or reactive programming.”

---

## 18. What is Service Discovery?

**Answer:**
Service discovery is the mechanism by which services find each other dynamically.

**Problem:**
In cloud/Kubernetes environments, service IPs can change.

**Solution:**
Use service discovery tools:

```text
Eureka
Consul
Zookeeper
Kubernetes Service Discovery
```

---

## 19. What is Eureka Server?

**Answer:**
Eureka Server is a service registry. Microservices register themselves with Eureka, and other services discover them using service names.

**Example:**

```text
user-service registers with Eureka
post-service calls user-service by name
```

```yaml
spring:
  application:
    name: user-service

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

---

## 20. What is an API Gateway?

**Answer:**
API Gateway is a single entry point for all client requests. It routes requests to internal services.

Spring Cloud Gateway supports route matching using predicates, filters, circuit breaker integration, discovery integration, rate limiting, and path rewriting. ([Home][3])

**Example:**

```text
Client -> API Gateway -> user-service
Client -> API Gateway -> post-service
Client -> API Gateway -> comment-service
```

**Responsibilities:**

1. Routing
2. Authentication
3. Rate limiting
4. Logging
5. Request/response transformation
6. Load balancing

---

## 21. Example of Spring Cloud Gateway routing?

**Answer:**

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/users/**
        - id: post-service
          uri: lb://post-service
          predicates:
            - Path=/posts/**
```

**Meaning:**
Requests coming to `/users/**` go to `user-service`.
Requests coming to `/posts/**` go to `post-service`.

---

# Database and Transaction Management

## 22. Should every Microservice have its own database?

**Answer:**
Ideally, yes. Each microservice should own its data.

**Good design:**

```text
user-service       -> user_db
post-service       -> post_db
order-service      -> order_db
payment-service    -> payment_db
```

**Bad design:**

```text
All services directly access same database tables
```

**Reason:**
If multiple services share the same database, they become tightly coupled.

---

## 23. Can two Microservices share the same database?

**Answer:**
Technically yes, but it is not recommended.

**Why?**

1. Tight coupling
2. Schema change affects multiple services
3. Independent deployment becomes difficult
4. Ownership becomes unclear

**Interview line:**
“In migration phase, shared DB may exist temporarily, but final microservice design should move toward database-per-service.”

---

## 24. How do Microservices handle transactions?

**Answer:**
Microservices avoid distributed ACID transactions where possible. They usually use eventual consistency and Saga pattern.

**Example:**
Order placement:

```text
1. order-service creates order
2. payment-service processes payment
3. inventory-service reserves stock
4. notification-service sends message
```

If payment fails, order-service cancels the order.

---

## 25. What is Saga Pattern?

**Answer:**
Saga is a pattern used to manage distributed transactions across microservices.

Types:

### 1. Choreography Saga

Services communicate using events.

```text
OrderCreated -> PaymentProcessed -> InventoryReserved -> OrderConfirmed
```

### 2. Orchestration Saga

A central orchestrator controls the flow.

```text
Order Orchestrator tells payment-service, inventory-service, notification-service what to do
```

---

## 26. Choreography vs Orchestration Saga?

**Answer:**

| Choreography           | Orchestration                |
| ---------------------- | ---------------------------- |
| Event-driven           | Central controller           |
| No central coordinator | One orchestrator             |
| More decoupled         | Easier to track              |
| Can become complex     | Better for complex workflows |

**Interview line:**
“For simple flows I prefer choreography; for complex business workflows I prefer orchestration.”

---

## 27. What is Eventual Consistency?

**Answer:**
Eventual consistency means data may not be immediately consistent across all services, but it will become consistent after some time.

**Example:**

```text
order-service creates order
payment-service updates payment after few seconds
order status becomes CONFIRMED eventually
```

**Interview line:**
“In microservices, we often choose availability and scalability over immediate consistency.”

---

## 28. What is the Outbox Pattern?

**Answer:**
Outbox pattern ensures that database update and event publishing happen reliably.

**Problem:**
What if order is saved in DB but event publishing to Kafka fails?

**Solution:**
Save event in the same database transaction.

```text
order table
outbox_event table
```

Then a background process publishes events from `outbox_event`.

```java
@Transactional
public void createOrder(OrderRequest request) {
    Order order = orderRepository.save(new Order(request.customerId()));

    OutboxEvent event = new OutboxEvent(
            "OrderCreated",
            order.getId().toString(),
            """
            {"orderId": %d}
            """.formatted(order.getId())
    );

    outboxEventRepository.save(event);
}
```

---

## 29. What is CQRS?

**Answer:**
CQRS means Command Query Responsibility Segregation.

It separates:

```text
Command -> write operation
Query   -> read operation
```

**Example:**

```text
order-command-service -> create/update orders
order-query-service   -> search/read orders
```

**Use case:**
Useful when read and write models are very different or read traffic is very high.

---

## 30. What is Event Sourcing?

**Answer:**
Event Sourcing stores state changes as a sequence of events instead of storing only the latest state.

**Traditional:**

```text
order_id = 1, status = DELIVERED
```

**Event sourcing:**

```text
OrderCreated
PaymentCompleted
OrderShipped
OrderDelivered
```

Current state is rebuilt by replaying events.

---

# Resilience and Fault Tolerance

Resilience4j provides Java fault-tolerance patterns such as CircuitBreaker, Retry, RateLimiter, Bulkhead, TimeLimiter, and related Spring Boot configuration support. ([resilience4j][4])

## 31. What is fault tolerance in Microservices?

**Answer:**
Fault tolerance means the system continues to work even if one or more services fail.

**Example:**
If `recommendation-service` is down, `product-service` should still return product details without recommendations.

---

## 32. What is Circuit Breaker Pattern?

**Answer:**
Circuit Breaker prevents repeated calls to a failing service.

States:

```text
CLOSED      -> calls allowed
OPEN        -> calls blocked
HALF_OPEN   -> limited test calls allowed
```

**Example with Resilience4j:**

```java
@Service
public class UserServiceClient {

    private final UserClient userClient;

    public UserServiceClient(UserClient userClient) {
        this.userClient = userClient;
    }

    @CircuitBreaker(name = "userService", fallbackMethod = "fallbackUser")
    public UserDto getUser(Long userId) {
        return userClient.getUserById(userId);
    }

    public UserDto fallbackUser(Long userId, Throwable ex) {
        return new UserDto(userId, "Unknown User");
    }
}
```

---

## 33. What is Retry Pattern?

**Answer:**
Retry pattern calls a failed operation again.

**Use when:**
Failure is temporary.

Examples:

```text
network timeout
temporary DB connection issue
temporary service unavailability
```

```java
@Retry(name = "paymentService", fallbackMethod = "paymentFallback")
public PaymentResponse callPayment(Long orderId) {
    return paymentClient.pay(orderId);
}
```

**Important:**
Do not retry non-idempotent operations blindly, like duplicate payment deduction.

---

## 34. What is Timeout Pattern?

**Answer:**
Timeout prevents one service from waiting forever for another service.

**Example:**

```text
post-service should wait maximum 2 seconds for user-service
```

If no response comes, return fallback.

**Interview line:**
“Every remote call in microservices should have timeout configured.”

---

## 35. What is Bulkhead Pattern?

**Answer:**
Bulkhead isolates resources so failure in one part does not affect the whole system.

**Example:**

```text
Thread pool for payment-service calls
Thread pool for notification-service calls
Thread pool for inventory-service calls
```

If payment calls are stuck, notification calls still work.

---

## 36. What is Rate Limiting?

**Answer:**
Rate limiting controls how many requests a client can send in a given time.

**Example:**

```text
100 requests per minute per user
```

**Why useful?**

1. Prevent abuse
2. Protect backend services
3. Avoid overload
4. Improve system stability

---

## 37. What is Idempotency?

**Answer:**
Idempotency means repeating the same request multiple times should produce the same result.

**Example:**
Payment API should not deduct money twice if user retries request.

```java
@PostMapping("/payments")
public ResponseEntity<String> makePayment(
        @RequestHeader("Idempotency-Key") String key,
        @RequestBody PaymentRequest request) {

    if (paymentRepository.existsByIdempotencyKey(key)) {
        return ResponseEntity.ok("Payment already processed");
    }

    paymentService.processPayment(key, request);
    return ResponseEntity.ok("Payment successful");
}
```

---

## 38. What is graceful degradation?

**Answer:**
Graceful degradation means the system provides reduced functionality instead of complete failure.

**Example:**

```text
Product page loads
Recommendation section shows: "Recommendations unavailable"
```

The main business functionality remains available.

---

# Security in Microservices

OAuth 2.0 is an authorization framework for giving limited access to HTTP services; in microservice systems, OAuth2/JWT is commonly used so services can validate access tokens without depending on server-side sessions. ([IETF Datatracker][5])

## 39. How do you secure Microservices?

**Answer:**
Common security practices:

1. Use API Gateway authentication
2. Use JWT/OAuth2
3. Use HTTPS
4. Use service-to-service authentication
5. Use role-based access control
6. Validate input
7. Store secrets securely
8. Apply rate limiting

---

## 40. What is JWT in Microservices?

**Answer:**
JWT is a token that carries user identity and claims.

Flow:

```text
User logs in
auth-service generates JWT
client sends JWT in Authorization header
API Gateway validates token
request goes to internal service
```

Header:

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

---

## 41. Should JWT validation happen at API Gateway or each service?

**Answer:**
Usually both can be used:

| Gateway validation                | Service validation                       |
| --------------------------------- | ---------------------------------------- |
| Blocks unauthorized request early | Provides defense in depth                |
| Reduces duplicate work            | Useful if service is accessed internally |
| Centralized security              | Stronger zero-trust model                |

**Best practical answer:**
“Gateway should validate common authentication, and critical services should also validate token/roles when needed.”

---

## 42. What is service-to-service authentication?

**Answer:**
It means one microservice proves its identity to another microservice.

Examples:

```text
mTLS
OAuth2 client credentials
API keys for internal services
service mesh identity
```

**Example:**
`order-service` calling `payment-service` should be authenticated, not blindly trusted.

---

## 43. Where should authorization logic be placed?

**Answer:**
Authentication can be centralized at gateway, but business authorization should usually stay inside the service.

**Example:**

```text
Gateway checks JWT is valid
post-service checks user is owner of the post before update/delete
```

---

# Observability and Monitoring

Spring Boot Actuator provides production-ready capabilities such as HTTP/JMX endpoints, auditing, health, and metrics, while OpenTelemetry provides a standard way to instrument applications and correlate spans into distributed traces. ([Home][6])

## 44. What is observability in Microservices?

**Answer:**
Observability means understanding what is happening inside the system using:

```text
Logs
Metrics
Traces
Health checks
Alerts
Dashboards
```

**Interview line:**
“In microservices, observability is mandatory because one request may travel through multiple services.”

---

## 45. What is distributed tracing?

**Answer:**
Distributed tracing tracks a request across multiple services.

**Example:**

```text
API Gateway -> order-service -> payment-service -> inventory-service
```

A trace shows:

```text
which service was called
how much time each service took
where the error happened
```

Tools:

```text
OpenTelemetry
Jaeger
Zipkin
Grafana Tempo
```

---

## 46. What are logs, metrics, and traces?

**Answer:**

| Type    | Meaning              | Example                       |
| ------- | -------------------- | ----------------------------- |
| Logs    | Event details        | Error stacktrace              |
| Metrics | Numeric measurements | CPU, memory, request count    |
| Traces  | Request journey      | API Gateway → Order → Payment |

**Interview line:**
“Logs tell what happened, metrics tell system health, traces tell where time was spent.”

---

## 47. What is Spring Boot Actuator?

**Answer:**
Spring Boot Actuator provides production-ready endpoints.

Common endpoints:

```text
/actuator/health
/actuator/metrics
/actuator/info
/actuator/prometheus
```

Example:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
```

---

# Deployment, DevOps, and Cloud

Kubernetes is widely used for automating deployment, scaling, and management of containerized applications, which is why it fits naturally with microservice deployments. ([Kubernetes][7])

## 48. How are Microservices deployed?

**Answer:**
Common deployment approach:

```text
1. Build Spring Boot jar
2. Create Docker image
3. Push image to Docker registry
4. Deploy to Kubernetes
5. Expose using Service/Ingress
6. Monitor using logs/metrics/traces
```

Example Dockerfile:

```dockerfile
FROM eclipse-temurin:17-jdk
COPY target/user-service.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

---

## 49. What is the role of Docker in Microservices?

**Answer:**
Docker packages each microservice with its dependencies into a container image.

**Benefits:**

1. Same environment everywhere
2. Easy deployment
3. Easy scaling
4. Isolation
5. Works well with Kubernetes

**Example:**

```text
user-service image
post-service image
category-service image
api-gateway image
```

---

## 50. What is the role of Kubernetes in Microservices?

**Answer:**
Kubernetes manages containerized microservices.

Important Kubernetes objects:

| Object     | Purpose                |
| ---------- | ---------------------- |
| Pod        | Runs container         |
| Deployment | Manages replicas       |
| Service    | Stable access to pods  |
| ConfigMap  | External configuration |
| Secret     | Sensitive data         |
| Ingress    | External HTTP routing  |
| HPA        | Auto scaling           |

**Example:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
        - name: user-service
          image: danish/user-service:1.0
          ports:
            - containerPort: 8081
```

---

# Bonus: Common Microservices System Design Flow

For interviews, answer using this structure:

```text
1. Identify services
2. Define APIs
3. Decide database per service
4. Choose sync/async communication
5. Add API Gateway
6. Add service discovery
7. Add security
8. Add resilience patterns
9. Add observability
10. Add deployment strategy
```

Example for e-commerce:

```text
user-service
product-service
cart-service
order-service
payment-service
inventory-service
notification-service
api-gateway
config-server
discovery-server
```

---

# Short Interview Revision Notes

Remember these lines:

**Microservices are not just splitting code. They are about independent ownership, deployment, scaling, and data responsibility.**

**Do not say:**
“We create microservices because it is the latest trend.”

**Say:**
“We use microservices when the system is large enough that independent scaling, deployment, fault isolation, and team autonomy become more important than the simplicity of a monolith.”

**Most important patterns to revise deeply:**

```text
API Gateway
Service Discovery
Config Server
Circuit Breaker
Retry
Timeout
Bulkhead
Rate Limiting
Saga
Outbox Pattern
Database per Service
Eventual Consistency
Distributed Tracing
JWT/OAuth2 Security
Docker + Kubernetes Deployment
```

For your own project, you can confidently say:

> “Currently my application is monolithic. My plan is to convert it into user-service, post-service, category-service, comment-service, file-service, API Gateway, Eureka/Discovery, Config Server, and later deploy each service independently using Docker and Kubernetes.”

[1]: https://martinfowler.com/articles/microservices.html?utm_source=chatgpt.com "Microservices"
[2]: https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/?utm_source=chatgpt.com "Spring Cloud OpenFeign"
[3]: https://spring.io/projects/spring-cloud-gateway?utm_source=chatgpt.com "Spring Cloud Gateway"
[4]: https://resilience4j.readme.io/docs/getting-started-3?utm_source=chatgpt.com "Getting Started - resilience4j - ReadMe"
[5]: https://datatracker.ietf.org/doc/html/rfc6749?utm_source=chatgpt.com "RFC 6749 - The OAuth 2.0 Authorization Framework"
[6]: https://docs.spring.io/spring-boot/reference/actuator/index.html?utm_source=chatgpt.com "Production-ready Features"
[7]: https://kubernetes.io/?utm_source=chatgpt.com "Kubernetes"
