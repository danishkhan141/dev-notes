Below are **Top 50 Spring Framework interview questions** in a clean, interview-preparation style. Focus on understanding these answers, then practice explaining them in your own words.

Spring’s official docs define `ApplicationContext` as the Spring IoC container responsible for creating, configuring, and assembling beans from configuration metadata. Spring supports annotation-based, Java-based, XML, and Groovy configuration styles. ([Home][1])

---

# Spring Interview Questions — Top 50

## 1. What is Spring Framework?

**Spring Framework** is a lightweight Java framework used to build enterprise applications. It mainly provides:

* IoC / Dependency Injection
* AOP
* Transaction management
* MVC web framework
* Data access support
* Validation
* Integration with other technologies

**Interview answer:**

> Spring helps us create loosely coupled, testable, maintainable Java applications by managing object creation and dependencies through its IoC container.

---

## 2. What problem does Spring solve?

Without Spring, classes directly create their dependencies.

```java
class OrderService {
    private PaymentService paymentService = new PaymentService();
}
```

This creates **tight coupling**.

With Spring:

```java
@Service
class OrderService {
    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Now Spring injects `PaymentService`, so `OrderService` is loosely coupled.

---

## 3. What is IoC?

**IoC means Inversion of Control.**

Normally, we create objects manually using `new`. In Spring, the control of object creation is given to the Spring container.

```java
PaymentService paymentService = new PaymentService(); // manual
```

In Spring:

```java
@Component
class PaymentService {
}
```

Spring creates and manages this object.

**Interview answer:**

> IoC means object creation and dependency management are handled by the Spring container instead of the application code.

---

## 4. What is Dependency Injection?

**Dependency Injection** means supplying required dependencies from outside the class instead of creating them inside the class.

Types:

1. Constructor injection
2. Setter injection
3. Field injection

Best practice is **constructor injection**.

Spring supports constructor and setter injection using `@Autowired`; if a bean has only one constructor, `@Autowired` is not required on that constructor. ([Home][2])

---

## 5. Why is constructor injection preferred?

Constructor injection is preferred because:

* It makes dependencies mandatory.
* It supports immutable fields using `final`.
* It improves testability.
* It avoids partially initialized objects.
* It makes circular dependency issues visible early.

```java
@Service
class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

**Interview answer:**

> Constructor injection is preferred because it clearly defines mandatory dependencies and makes the class easier to test and maintain.

---

## 6. What is a Spring Bean?

A **Spring Bean** is an object managed by the Spring container.

Example:

```java
@Component
class EmailService {
}
```

Here, `EmailService` becomes a Spring bean because Spring will create and manage its object.

**Simple line:**

> Any object created, configured, and managed by Spring IoC container is called a Spring Bean.

---

## 7. What is ApplicationContext?

`ApplicationContext` is the main Spring container. It creates beans, injects dependencies, manages lifecycle, and provides additional features like events, internationalization, and environment support. Spring docs describe it as the interface representing the Spring IoC container. ([Home][1])

Example:

```java
ApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);

UserService userService = context.getBean(UserService.class);
```

---

## 8. BeanFactory vs ApplicationContext?

`BeanFactory` is the basic container. `ApplicationContext` is an advanced container built on top of BeanFactory.

| BeanFactory               | ApplicationContext                         |
| ------------------------- | ------------------------------------------ |
| Basic IoC container       | Advanced IoC container                     |
| Lazy bean loading mostly  | Eager singleton loading by default         |
| Fewer enterprise features | Events, messages, environment, AOP support |
| Rarely used directly      | Commonly used                              |

**Interview answer:**

> BeanFactory provides basic dependency injection, while ApplicationContext provides enterprise-level features and is used in most real applications.

---

## 9. What is `@Component`?

`@Component` marks a class as a Spring-managed bean.

```java
@Component
public class SmsService {
}
```

Spring detects it during component scanning and registers it as a bean.

---

## 10. Difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?

All four create Spring beans, but their purpose is different.

| Annotation    | Layer                    |
| ------------- | ------------------------ |
| `@Component`  | Generic component        |
| `@Service`    | Business logic layer     |
| `@Repository` | DAO / persistence layer  |
| `@Controller` | Web MVC controller layer |

Example:

```java
@Repository
class UserRepository {
}

@Service
class UserService {
}

@Controller
class UserController {
}
```

**Interview point:**

> Technically they all register beans, but semantically they represent different application layers.

---

## 11. What is component scanning?

Component scanning means Spring scans packages and automatically registers classes annotated with stereotypes like `@Component`, `@Service`, `@Repository`, and `@Controller`.

```java
@ComponentScan(basePackages = "com.example")
@Configuration
class AppConfig {
}
```

In Spring Boot, this is usually handled by `@SpringBootApplication`.

---

## 12. What is `@Bean`?

`@Bean` is used inside a configuration class to manually register a bean.

```java
@Configuration
class AppConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

Use `@Bean` when you do not own the class or need custom object creation logic.

---

## 13. Difference between `@Component` and `@Bean`?

| `@Component`               | `@Bean`                       |
| -------------------------- | ----------------------------- |
| Used on class              | Used on method                |
| Spring auto-detects it     | Developer manually defines it |
| Best for your own classes  | Best for third-party classes  |
| Less control over creation | More control over creation    |

Example:

```java
@Component
class MyService {
}
```

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

---

## 14. What is `@Configuration`?

`@Configuration` marks a class as a source of bean definitions.

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService() {
        return new UserService();
    }
}
```

**Interview answer:**

> `@Configuration` is used to define beans using Java-based configuration.

---

## 15. What is `@Autowired`?

`@Autowired` tells Spring to inject a dependency automatically.

```java
@Service
class OrderService {

    private final PaymentService paymentService;

    @Autowired
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

If only one constructor exists, `@Autowired` can be omitted. ([Home][2])

---

## 16. What happens if multiple beans of same type exist?

Spring gets confused because it does not know which bean to inject.

Example:

```java
@Component
class EmailNotification implements NotificationService {
}

@Component
class SmsNotification implements NotificationService {
}
```

Solution 1: `@Qualifier`

```java
@Service
class AlertService {

    public AlertService(
        @Qualifier("emailNotification") NotificationService service
    ) {
    }
}
```

Solution 2: `@Primary`

```java
@Primary
@Component
class EmailNotification implements NotificationService {
}
```

---

## 17. Difference between `@Qualifier` and `@Primary`?

| `@Primary`                | `@Qualifier`            |
| ------------------------- | ----------------------- |
| Gives default preference  | Selects specific bean   |
| Used on bean class/method | Used at injection point |
| General solution          | More precise solution   |

**Interview answer:**

> `@Primary` is the default choice when multiple beans exist, while `@Qualifier` explicitly tells Spring which bean to inject.

---

## 18. What is bean scope?

Bean scope defines how many instances of a bean Spring creates and how long they live.

Main scopes:

| Scope       | Meaning                          |
| ----------- | -------------------------------- |
| singleton   | One object per Spring container  |
| prototype   | New object every time requested  |
| request     | One object per HTTP request      |
| session     | One object per HTTP session      |
| application | One object per ServletContext    |
| websocket   | One object per WebSocket session |

The default Spring bean scope is **singleton**. Spring’s official bean scope docs also warn that injecting a prototype bean directly into a singleton resolves the prototype only once at singleton creation time. ([Home][3])

---

## 19. Singleton vs Prototype scope?

### Singleton

```java
@Component
@Scope("singleton")
class UserService {
}
```

Spring creates only one object.

### Prototype

```java
@Component
@Scope("prototype")
class ReportGenerator {
}
```

Spring creates a new object every time it is requested.

**Important interview point:**

> Singleton means one bean instance per Spring container, not one object per JVM.

---

## 20. What happens if prototype bean is injected into singleton bean?

If a prototype bean is injected into a singleton bean, Spring injects it only once when the singleton bean is created. After that, the same prototype object is reused inside that singleton. This behavior is specifically highlighted in Spring’s scope documentation. ([Home][3])

Problem:

```java
@Component
class SingletonService {

    private final PrototypeBean prototypeBean;

    public SingletonService(PrototypeBean prototypeBean) {
        this.prototypeBean = prototypeBean;
    }
}
```

Even though `PrototypeBean` is prototype-scoped, this singleton gets only one instance.

Solution:

```java
@Component
class SingletonService {

    private final ObjectProvider<PrototypeBean> provider;

    public SingletonService(ObjectProvider<PrototypeBean> provider) {
        this.provider = provider;
    }

    public void process() {
        PrototypeBean bean = provider.getObject();
    }
}
```

---

## 21. What is bean lifecycle?

Spring bean lifecycle:

1. Bean definition loaded
2. Bean object created
3. Dependencies injected
4. Aware interfaces called
5. BeanPostProcessor before initialization
6. `@PostConstruct`
7. `InitializingBean.afterPropertiesSet()`
8. Custom init method
9. Bean ready to use
10. `@PreDestroy`
11. Destroy method

Example:

```java
@Component
class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("Bean destroyed");
    }
}
```

---

## 22. What is BeanPostProcessor?

`BeanPostProcessor` allows custom logic before and after bean initialization. Spring docs describe it as a container extension point that can modify bean instances and even wrap beans with proxies. ([Home][4])

```java
@Component
class CustomBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        System.out.println("Before init: " + beanName);
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("After init: " + beanName);
        return bean;
    }
}
```

**Interview answer:**

> BeanPostProcessor is used to customize beans before and after initialization. Spring internally uses it for features like proxy creation and annotation processing.

---

## 23. What is lazy initialization?

By default, singleton beans are usually created when the application context starts. Lazy initialization means bean creation is delayed until it is first requested.

```java
@Component
@Lazy
class HeavyService {
}
```

**Use case:**

> Use `@Lazy` when bean creation is expensive and not always required.

---

## 24. What is circular dependency?

Circular dependency means two or more beans depend on each other.

```java
@Service
class A {
    public A(B b) {}
}

@Service
class B {
    public B(A a) {}
}
```

This creates a cycle.

**Better solution:**

* Redesign responsibilities
* Use setter injection carefully
* Use `@Lazy` if unavoidable
* Extract common logic into third service

**Interview answer:**

> Circular dependency usually indicates poor design. The best solution is to refactor the code.

---

## 25. What is `@Value`?

`@Value` injects values from properties, environment variables, or expressions.

```java
@Component
class AppInfo {

    @Value("${app.name}")
    private String appName;
}
```

Property:

```properties
app.name=Order Application
```

Use it for simple values, not for large configuration groups.

---

## 26. What is Spring Profile?

Profiles allow environment-specific beans or configuration.

```java
@Component
@Profile("dev")
class DevPaymentGateway implements PaymentGateway {
}
```

```java
@Component
@Profile("prod")
class ProdPaymentGateway implements PaymentGateway {
}
```

Run with:

```properties
spring.profiles.active=dev
```

**Interview answer:**

> Profiles help separate environment-specific configuration such as dev, test, UAT, and production.

---

# AOP Questions

Spring has its own AOP framework and supports aspect-style programming for common cross-cutting concerns such as logging, security, transactions, and monitoring. ([Home][5])

## 27. What is AOP?

AOP means **Aspect-Oriented Programming**.

It separates cross-cutting concerns from business logic.

Common examples:

* Logging
* Security
* Transaction management
* Auditing
* Performance monitoring

Without AOP, logging is repeated everywhere.

With AOP, we write logging logic once and apply it to multiple methods.

---

## 28. What are cross-cutting concerns?

Cross-cutting concerns are common logic used across many layers.

Example:

```java
public void createOrder() {
    log.info("createOrder started");
    // business logic
    log.info("createOrder completed");
}
```

Logging is not core business logic, but it appears everywhere.

So logging is a cross-cutting concern.

---

## 29. What are Aspect, Advice, Join Point, and Pointcut?

| Term       | Meaning                                        |
| ---------- | ---------------------------------------------- |
| Aspect     | Class containing cross-cutting logic           |
| Advice     | Actual action, like before/after method        |
| Join Point | Point during execution, usually method call    |
| Pointcut   | Expression selecting where advice should apply |
| Weaving    | Applying aspect to target object               |

Example:

```java
@Aspect
@Component
class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore() {
        System.out.println("Method started");
    }
}
```

---

## 30. Types of advice in Spring AOP?

Main advice types:

1. `@Before`
2. `@After`
3. `@AfterReturning`
4. `@AfterThrowing`
5. `@Around`

Example:

```java
@Around("execution(* com.example.service.*.*(..))")
public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
    long start = System.currentTimeMillis();

    Object result = joinPoint.proceed();

    long end = System.currentTimeMillis();
    System.out.println("Time taken: " + (end - start));

    return result;
}
```

---

## 31. What is proxy in Spring?

A proxy is a wrapper object created by Spring around the original bean.

Example:

```text
Controller -> Proxy -> Actual Service
```

Proxy adds extra behavior like transaction, security, logging, etc.

**Interview answer:**

> Spring uses proxies to apply AOP logic around target beans.

---

## 32. JDK dynamic proxy vs CGLIB proxy?

| JDK Dynamic Proxy                    | CGLIB Proxy                |
| ------------------------------------ | -------------------------- |
| Interface-based                      | Class-based                |
| Requires interface                   | Does not require interface |
| Creates proxy implementing interface | Creates subclass proxy     |

**Interview answer:**

> If a bean implements an interface, Spring can use JDK proxy. If not, Spring can use CGLIB class-based proxy.

---

## 33. What is self-invocation problem in Spring AOP?

Self-invocation means one method inside a class calls another method of the same class.

```java
@Service
class OrderService {

    public void placeOrder() {
        saveOrder(); // self-invocation
    }

    @Transactional
    public void saveOrder() {
        // transaction may not apply
    }
}
```

Why?

Because the call does not go through Spring proxy.

**Interview answer:**

> Spring AOP works through proxies. Internal method calls bypass the proxy, so annotations like `@Transactional` may not work as expected.

---

# Transaction Questions

Spring supports declarative and programmatic transaction management, and the official docs recommend declarative transaction management for most users.

## 34. What is `@Transactional`?

`@Transactional` tells Spring to execute a method inside a database transaction.

```java
@Service
class OrderService {

    @Transactional
    public void placeOrder(Order order) {
        orderRepository.save(order);
        paymentRepository.save(order.getPayment());
    }
}
```

If an unchecked exception occurs, Spring rolls back the transaction by default.

---

## 35. Where should we put `@Transactional`?

Usually on the **service layer**, not controller or repository.

```java
@Service
class TransferService {

    @Transactional
    public void transferMoney() {
        debitAccount();
        creditAccount();
    }
}
```

Why service layer?

Because business operation usually involves multiple repository calls.

---

## 36. Does `@Transactional` work on private methods?

Usually no.

Spring transaction management is proxy-based in common usage. The official docs mention that only external method calls coming through the proxy are intercepted. ([Home][6])

So this is problematic:

```java
@Transactional
private void saveData() {
}
```

Use public service methods for transactions.

---

## 37. What are transaction propagation types?

Propagation defines how a transaction behaves when a transactional method calls another transactional method.

Common types:

| Propagation   | Meaning                                                |
| ------------- | ------------------------------------------------------ |
| REQUIRED      | Use existing transaction, create new if none           |
| REQUIRES_NEW  | Always create new transaction                          |
| SUPPORTS      | Use transaction if exists, otherwise non-transactional |
| MANDATORY     | Existing transaction required                          |
| NEVER         | Must run without transaction                           |
| NOT_SUPPORTED | Suspends existing transaction                          |
| NESTED        | Runs inside nested transaction/savepoint               |

Most commonly used: `REQUIRED` and `REQUIRES_NEW`.

---

## 38. REQUIRED vs REQUIRES_NEW?

### REQUIRED

```java
@Transactional(propagation = Propagation.REQUIRED)
public void methodA() {
}
```

Uses existing transaction if available.

### REQUIRES_NEW

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void methodB() {
}
```

Suspends existing transaction and creates a new one.

**Example:**

Order creation and audit logging:

```java
@Transactional
public void createOrder() {
    orderRepository.save(order);
    auditService.saveAuditLog(); // REQUIRES_NEW
}
```

Even if order transaction fails, audit log can still be committed if designed that way.

---

## 39. What is transaction isolation?

Isolation defines how one transaction is isolated from another transaction.

Common levels:

| Isolation        | Meaning                         |
| ---------------- | ------------------------------- |
| READ_UNCOMMITTED | Can read uncommitted data       |
| READ_COMMITTED   | Reads only committed data       |
| REPEATABLE_READ  | Same row read gives same result |
| SERIALIZABLE     | Highest isolation, slowest      |

Example:

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void processPayment() {
}
```

---

## 40. Checked exception vs unchecked exception in transaction rollback?

By default:

* RuntimeException → rollback
* Error → rollback
* Checked exception → no rollback by default

For checked exception rollback:

```java
@Transactional(rollbackFor = Exception.class)
public void process() throws Exception {
    throw new Exception("Checked exception");
}
```

**Interview answer:**

> By default, Spring rolls back on unchecked exceptions. For checked exceptions, we need to configure `rollbackFor`.

---

# Spring MVC / REST Questions

Spring Web MVC is Spring’s Servlet API-based web framework and has been part of Spring Framework from the beginning. ([Home][7])

## 41. What is Spring MVC?

Spring MVC is a web framework based on the Model-View-Controller pattern.

Main components:

* Model: data
* View: UI
* Controller: handles request

For REST APIs, the view is usually JSON response.

---

## 42. What is DispatcherServlet?

`DispatcherServlet` is the front controller in Spring MVC.

Request flow:

```text
Client
 -> DispatcherServlet
 -> HandlerMapping
 -> Controller
 -> Service
 -> Repository
 -> Response
```

**Interview answer:**

> DispatcherServlet receives every request and dispatches it to the correct controller method.

---

## 43. What is `@Controller`?

`@Controller` marks a class as a Spring MVC controller.

```java
@Controller
class PageController {

    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

This usually returns a view name.

---

## 44. What is `@RestController`?

`@RestController` is used to create REST APIs.

```java
@RestController
@RequestMapping("/users")
class UserController {

    @GetMapping("/{id}")
    public UserDto getUser(@PathVariable Long id) {
        return new UserDto(id, "Danish");
    }
}
```

`@RestController = @Controller + @ResponseBody`

So returned object is written directly as JSON/XML response.

---

## 45. Difference between `@RequestMapping`, `@GetMapping`, and `@PostMapping`?

`@RequestMapping` is a generic mapping annotation. It can map URL, HTTP method, params, headers, and media types. Spring also provides shortcut annotations like `@GetMapping` and `@PostMapping`. ([Home][8])

```java
@RequestMapping(value = "/users", method = RequestMethod.GET)
public List<User> getUsers() {
    return users;
}
```

Shortcut:

```java
@GetMapping("/users")
public List<User> getUsers() {
    return users;
}
```

---

## 46. `@RequestParam` vs `@PathVariable`?

### `@RequestParam`

Used for query parameters.

```java
@GetMapping("/users")
public User getUser(@RequestParam Long id) {
    return userService.getUser(id);
}
```

URL:

```text
/users?id=10
```

Spring docs describe `@RequestParam` as binding Servlet request parameters, such as query parameters or form data, to method arguments. ([Home][9])

### `@PathVariable`

Used for path values.

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

URL:

```text
/users/10
```

---

## 47. `@RequestBody` vs `@ResponseBody`?

### `@RequestBody`

Converts incoming JSON into Java object.

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return userService.save(user);
}
```

JSON request:

```json
{
  "name": "Danish",
  "email": "danish@example.com"
}
```

### `@ResponseBody`

Converts Java object into JSON response.

```java
@ResponseBody
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

In `@RestController`, `@ResponseBody` is automatically applied.

---

## 48. How do you handle exceptions globally in Spring MVC?

Use `@ControllerAdvice` with `@ExceptionHandler`.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

**Interview answer:**

> Global exception handling keeps controller code clean and provides consistent API error responses.

---

## 49. How does validation work in Spring?

Spring supports Java Bean Validation. Bean Validation allows constraints to be declared on model properties and validated at runtime. ([Home][10])

DTO:

```java
public class UserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Min(18)
    private int age;
}
```

Controller:

```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody UserRequest request) {
    return ResponseEntity.ok("User created");
}
```

**Interview answer:**

> We use validation annotations on DTO fields and trigger validation using `@Valid` in controller methods.

---

## 50. Difference between Spring and Spring Boot?

| Spring Framework                    | Spring Boot                     |
| ----------------------------------- | ------------------------------- |
| Core framework                      | Built on top of Spring          |
| Requires manual configuration       | Provides auto-configuration     |
| Need to configure server separately | Embedded Tomcat/Jetty supported |
| More boilerplate                    | Less boilerplate                |
| Flexible but more setup             | Faster application development  |

**Interview answer:**

> Spring is the core framework. Spring Boot simplifies Spring application development using auto-configuration, starter dependencies, and embedded servers.

---

# Most Important Questions to Revise First

For interviews, revise these first:

1. IoC and Dependency Injection
2. Bean lifecycle
3. Bean scopes
4. Constructor injection vs field injection
5. `@Component` vs `@Bean`
6. `@Autowired`, `@Qualifier`, `@Primary`
7. AOP basics
8. Proxy concept
9. `@Transactional`
10. Propagation and isolation
11. Spring MVC request flow
12. `@Controller` vs `@RestController`
13. `@RequestParam` vs `@PathVariable`
14. Global exception handling
15. Spring vs Spring Boot

---

# Strong Interview Closing Line

You can say this in interview:

> In my understanding, Spring mainly helps in building loosely coupled applications. The IoC container manages beans and injects dependencies, AOP handles cross-cutting concerns like logging and transactions, and Spring MVC helps expose web or REST endpoints in a clean layered architecture.

[1]: https://docs.spring.io/spring-framework/reference/core/beans/basics.html?utm_source=chatgpt.com "Container Overview :: Spring Framework"
[2]: https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html?utm_source=chatgpt.com "Using @Autowired"
[3]: https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html?utm_source=chatgpt.com "Bean Scopes"
[4]: https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html?utm_source=chatgpt.com "Container Extension Points"
[5]: https://docs.spring.io/spring-framework/reference/core.html?utm_source=chatgpt.com "Core Technologies :: Spring Framework"
[6]: https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html?utm_source=chatgpt.com "Using @Transactional :: Spring Framework"
[7]: https://docs.spring.io/spring-framework/reference/web/webmvc.html?utm_source=chatgpt.com "Spring Web MVC :: Spring Framework"
[8]: https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html?utm_source=chatgpt.com "Mapping Requests :: Spring Framework"
[9]: https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/requestparam.html?utm_source=chatgpt.com "@RequestParam :: Spring Framework"
[10]: https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html?utm_source=chatgpt.com "Java Bean Validation"
