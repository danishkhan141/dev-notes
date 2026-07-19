## The most useful mental model: **5–5–6–4**

Do not memorize 20 separate UML diagrams. Remember them in four families:

```text
Design Patterns
│
├── 5 — CREATE objects
├── 5 — CONNECT/WRAP objects
├── 6 — CONTROL behaviour/flow
└── 4 — ORGANIZE application architecture
```

The 20 patterns listed at the end of your file follow exactly this structure.    

---

# 1. Are all patterns just Interface + Implementation classes?

**No.**

Interface + implementations are common because they provide:

* Loose coupling
* Multiple interchangeable behaviours
* Easy testing
* Open/Closed Principle
* Dependency Injection support

But the **interface is not the pattern**. The pattern is the **relationship and flow between objects**.

For example:

```text
Interface + Implementations
```

can represent completely different patterns:

```text
Factory  → Creates an implementation
Strategy → Executes one implementation
Adapter  → Converts another implementation
Proxy    → Controls access to implementation
Decorator→ Adds behaviour around implementation
State    → Changes implementation based on object state
```

Therefore, always ask:

> **What problem is this interface solving?**

---

# 2. Top 20 Design Patterns — One-Line Mental Map

## A. CREATE — Creational Patterns

| Pattern                 | Remember it as                          | Basic structure                                           |
| ----------------------- | --------------------------------------- | --------------------------------------------------------- |
| **1. Singleton**        | Only one shared object                  | Private constructor + static instance                     |
| **2. Factory Method**   | Choose which object to create           | Factory → Interface → Implementations                     |
| **3. Abstract Factory** | Create a family of related objects      | Factory interface → Multiple factories → Product families |
| **4. Builder**          | Construct a complex object step by step | Builder methods → `build()`                               |
| **5. Prototype**        | Copy an existing object                 | Existing object → `clone()`                               |

### Memory trigger

```text
One object       → Singleton
Choose an object → Factory
Choose a family  → Abstract Factory
Many fields      → Builder
Copy an object   → Prototype
```

### Java/Spring examples

```text
Singleton       → Spring singleton beans
Factory         → BeanFactory/ApplicationContext
Builder         → DTO/API response creation
Prototype       → Prototype bean scope/object cloning
```

---

## B. CONNECT — Structural Patterns

| Pattern           | Remember it as                            | Basic structure                                  |
| ----------------- | ----------------------------------------- | ------------------------------------------------ |
| **6. Adapter**    | Convert an incompatible interface         | Application interface → Adapter → External API   |
| **7. Facade**     | Give one simple entry to complex services | Controller → Facade → Multiple services          |
| **8. Proxy**      | Control access before/after real object   | Client → Proxy → Real object                     |
| **9. Decorator**  | Add behaviour around an object            | Decorator wraps same interface                   |
| **10. Composite** | Treat parent and child uniformly          | Component → Leaf + Composite containing children |

### Memory trigger

```text
Convert     → Adapter
Simplify    → Facade
Control     → Proxy
Enhance     → Decorator
Tree        → Composite
```

### Java/Spring examples

```text
Adapter   → Razorpay/AWS/legacy API integration
Facade    → Service layer coordinating many services
Proxy     → @Transactional, @Cacheable, @PreAuthorize
Decorator → Logging, caching, compression
Composite → Folder tree, menus, permissions
```

---

## C. BEHAVE — Behavioural Patterns

| Pattern                         | Remember it as                        | Basic structure                                 |
| ------------------------------- | ------------------------------------- | ----------------------------------------------- |
| **11. Strategy**                | Select one behaviour at runtime       | Context → Strategy → Implementations            |
| **12. Observer**                | One event informs many listeners      | Publisher → Multiple observers                  |
| **13. Template Method**         | Fixed workflow, customizable steps    | Abstract parent → Template method → Child steps |
| **14. Chain of Responsibility** | Request passes through handlers       | Handler → Next handler → Next handler           |
| **15. Command**                 | Convert an action into an object      | Invoker → Command → Receiver                    |
| **16. State**                   | Behaviour changes with internal state | Context → Current state object                  |

### Memory trigger

```text
Choose behaviour        → Strategy
Broadcast event         → Observer
Fixed workflow          → Template Method
Sequential processing   → Chain
Action as an object     → Command
Behaviour by status     → State
```

### Java/Spring examples

```text
Strategy → Local storage vs AWS S3
Observer → Spring Events/Kafka consumers
Template → JdbcTemplate, TransactionTemplate
Chain    → Spring Security filters
Command  → Batch job, scheduled task, queue operation
State    → DRAFT → REVIEW → PUBLISHED → ARCHIVED
```

---

## D. ORGANIZE — Enterprise/Application Patterns

| Pattern                      | Remember it as                                   | Basic structure                               |
| ---------------------------- | ------------------------------------------------ | --------------------------------------------- |
| **17. Repository**           | Hide database operations                         | Service → Repository → Database               |
| **18. MVC**                  | Separate request, business data and presentation | Controller → Model → View/Response            |
| **19. Front Controller**     | One central entry for all requests               | Request → DispatcherServlet → Controller      |
| **20. Dependency Injection** | Dependencies supplied from outside               | Container → Constructor → Required dependency |

### Memory trigger

```text
Database abstraction → Repository
Layer separation     → MVC
Single request entry → Front Controller
Object wiring        → Dependency Injection
```

Your file correctly connects Repository with the service layer, Front Controller with `DispatcherServlet`, and Dependency Injection with constructor-based wiring. 

---

# 3. Most Important Pattern Relationships

This is more valuable than memorizing twenty definitions.

## Factory + Strategy + Dependency Injection

These three frequently work together:

```text
Factory/Resolver
      ↓ selects
Strategy Implementation
      ↓ injected into
Business Service
```

Example:

```text
FileStorageStrategy
├── LocalStorage
└── S3Storage

Factory selects S3Storage
Spring DI injects it
FileService executes it
```

Remember:

```text
Factory  = Which object?
Strategy = Which behaviour?
DI       = How does the object reach the client?
```

---

## Adapter vs Facade

Both sit between the client and other classes, but:

```text
Adapter → Changes the interface
Facade  → Simplifies the interface
```

Example:

```text
Razorpay API does not match our PaymentService → Adapter

Post creation uses five internal services → Facade
```

---

## Proxy vs Decorator

Their UML may look almost identical because both wrap another object.

```text
Proxy     → Controls access
Decorator → Adds additional behaviour
```

Example:

```text
@Transactional → Proxy controls transaction boundary
Logging wrapper → Decorator adds logging behaviour
```

---

## Strategy vs State

Their class structures are also similar.

```text
Strategy → Client/environment chooses behaviour
State    → Object's internal status chooses behaviour
```

Example:

```text
LOCAL or S3 selected by configuration → Strategy

DRAFT or PUBLISHED controls allowed actions → State
```

---

## Observer vs Chain

```text
Observer → One event goes to many listeners
Chain    → One request goes through handlers sequentially
```

```text
PostCreated → Email + Kafka + Analytics
               Observer

HTTP Request → JWT → Authentication → Authorization
               Chain
```

---

## Template Method vs Strategy

```text
Template Method → Inheritance; fixed flow, override some steps
Strategy        → Composition; replace complete behaviour
```

```text
CSV and Excel follow the same import workflow → Template

Local and S3 have completely different upload logic → Strategy
```

---

## MVC vs Front Controller

```text
MVC              → Overall separation of application layers
Front Controller → Single central entry point for requests
```

In Spring MVC:

```text
Request
   ↓
DispatcherServlet       → Front Controller
   ↓
Controller
   ↓
Service/Model
   ↓
Response                → MVC flow
```

---

# 4. The Universal Structure Behind Most Patterns

Most patterns can be understood through this skeleton:

```text
Client
  ↓
Abstraction / Common Contract
  ↓
Concrete Implementation
```

Sometimes one additional component is added:

```text
Factory     → Selects implementation
Context     → Uses implementation
Proxy       → Controls implementation
Decorator   → Enhances implementation
Adapter     → Translates implementation
Facade      → Coordinates implementations
Publisher   → Notifies implementations
Container   → Injects implementation
```

So when viewing any UML, identify only these four things:

1. **Who is the client?**
2. **What is the common contract?**
3. **What are the concrete classes?**
4. **Who selects, wraps, coordinates or calls them?**

That is enough to understand most mid-range interview diagrams.

---

# 5. Interview Priority for Your Profile

You do not need equal depth in all 20.

## Tier 1 — Must explain with Spring/project examples

```text
1. Singleton
2. Factory
3. Builder
4. Strategy
5. Observer
6. Proxy
7. Adapter
8. Facade
9. Template Method
10. Chain of Responsibility
11. Repository
12. Dependency Injection
```

Your notes also prioritise Singleton, Factory, Builder, Strategy, Observer, Proxy, Adapter, Facade, Template Method and Chain of Responsibility for interviews. 

## Tier 2 — Understand basic structure and differences

```text
13. Abstract Factory
14. Decorator
15. Command
16. State
17. MVC
18. Front Controller
```

## Tier 3 — Definition and one example are enough initially

```text
19. Prototype
20. Composite
```

---

# 6. Final Interview Decision Cheat Sheet

When you see a problem, ask:

```text
Only one shared instance?               → Singleton
Object creation depends on type?        → Factory
Family of related objects?              → Abstract Factory
Too many constructor parameters?        → Builder
Need to copy an existing object?         → Prototype

External interface incompatible?        → Adapter
Complex subsystem needs simple API?     → Facade
Need access control/interception?        → Proxy
Need extra behaviour dynamically?       → Decorator
Tree-like parent-child structure?        → Composite

Multiple interchangeable behaviours?    → Strategy
One event, multiple reactions?           → Observer
Fixed workflow, some variable steps?     → Template Method
Request must pass through stages?        → Chain
Queue/retry/store an operation?          → Command
Behaviour depends on current status?     → State

Hide persistence details?               → Repository
Separate application layers?            → MVC
Single entry for all requests?           → Front Controller
Supply dependencies from outside?        → Dependency Injection
```

## Best sentence to keep in mind

> **Design patterns are not mainly about interfaces and implementation classes. They are about assigning responsibilities—who creates, selects, wraps, coordinates, notifies or executes an object.**

For your interviews, remember this sequence:

```text
CREATE → CONNECT → BEHAVE → ORGANIZE
  5         5          6          4
```

That single map is enough to recall all 20 patterns without separately memorizing every UML.

# Design Patterns — Block Diagram Mental Map

First, remember the complete family:

```text
                         DESIGN PATTERNS
                                │
       ┌────────────────────────┼────────────────────────┐
       │                        │                        │
       ▼                        ▼                        ▼
  CREATIONAL               STRUCTURAL              BEHAVIORAL
  Create objects           Connect objects         Control behaviour
       │                        │                        │
  Singleton                Adapter                  Strategy
  Factory                  Facade                   Observer
  Abstract Factory         Proxy                    Template Method
  Builder                  Decorator                Chain
  Prototype                Composite                Command
                                                     State

                                │
                                ▼
                      ENTERPRISE PATTERNS
                    Organize application layers
                                │
               Repository · MVC · Front Controller · DI
```

The 20 patterns at the end of your notes fit into these four families.    

---

# 1. Universal Structure Behind Most Patterns

Most design patterns begin with this structure:

```text
             CLIENT
                │
                ▼
     INTERFACE / ABSTRACTION
                │
       ┌────────┴────────┐
       ▼                 ▼
 Implementation A   Implementation B
```

For example:

```text
            FileService
                │
                ▼
       FileStorageStrategy
                │
       ┌────────┴────────┐
       ▼                 ▼
  LocalStorage       S3Storage
```

But the pattern depends on **what happens around these implementations**:

```text
Factory    ── selects implementation
Strategy   ── executes implementation
Adapter    ── converts external implementation
Proxy      ── controls implementation
Decorator  ── adds behaviour to implementation
State      ── changes implementation by status
DI         ── supplies implementation
```

Therefore:

> Interface + implementation is only the skeleton.
> The relationship and responsibility determine the pattern.

---

# 2. Factory + Strategy + Dependency Injection

These three are frequently used together.

```text
               Input / Configuration
                 "S3" or "LOCAL"
                        │
                        ▼
               FACTORY / RESOLVER
              Selects correct object
                        │
          ┌─────────────┴─────────────┐
          ▼                           ▼
 LocalStorageStrategy          S3StorageStrategy
          │                           │
          └─────────────┬─────────────┘
                        ▼
              Injected into FileService
                        │
                        ▼
                 uploadFile()
```

### Responsibility division

```text
Factory
   │
   └── Which implementation should be selected?

Strategy
   │
   └── Which behaviour should be executed?

Dependency Injection
   │
   └── How does that implementation reach FileService?
```

### Easy memory line

```text
Factory chooses
Strategy performs
DI supplies
```

Example:

```text
Storage type = S3
       │
       ▼
Factory selects S3StorageStrategy
       │
       ▼
Spring injects it into FileService
       │
       ▼
FileService executes upload()
```

---

# 3. Factory vs Strategy

Their structures look similar, but their purposes differ.

## Factory

```text
                Client
                   │
          "Give me EMAIL object"
                   │
                   ▼
         NotificationFactory
                   │
         ┌─────────┴─────────┐
         ▼                   ▼
 EmailNotification     SmsNotification
```

Factory returns an object:

```text
Input → Factory → Object
```

## Strategy

```text
                 Client
                    │
                    ▼
          NotificationService
                    │
             uses Strategy
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
   EmailStrategy         SmsStrategy
          │                   │
          └─────────┬─────────┘
                    ▼
             Behaviour runs
```

Strategy executes behaviour:

```text
Context → Strategy → Behaviour
```

### Difference

```text
Factory  → Which object should be created?
Strategy → Which algorithm/behaviour should run?
```

---

# 4. Adapter vs Facade

Both sit between the client and another system, but solve different problems.

## Adapter — Convert incompatible interface

Suppose your application expects:

```text
PaymentService.pay()
```

but Razorpay provides:

```text
RazorpayClient.makePayment()
```

The adapter converts one into another:

```text
        Our Application
               │
               ▼
        PaymentService
          pay(amount)
               ▲
               │ implements
               │
       RazorpayAdapter
               │ converts
               ▼
       RazorpayClient
      makePayment(amount)
```

### Mental line

```text
Application language
        │
        ▼
     Adapter
        │
        ▼
External API language
```

---

## Facade — Simplify a complex subsystem

```text
              Controller
                   │
          One simple method call
                   ▼
              PostFacade
                   │
       ┌───────────┼───────────┬─────────────┐
       ▼           ▼           ▼             ▼
 UserService  PostService  FileService  EventPublisher
```

Without Facade:

```text
Controller → UserService
Controller → CategoryService
Controller → PostService
Controller → FileService
Controller → NotificationService
```

With Facade:

```text
Controller → PostFacade → All required services
```

### Difference

```text
Adapter → Changes interface
Facade  → Simplifies multiple interfaces
```

---

# 5. Proxy vs Decorator

These two have almost the same block structure:

```text
Client → Wrapper → Real Object
```

But their intention differs.

## Proxy — Control access

```text
              Client
                 │
                 ▼
         Transaction Proxy
                 │
        ┌────────┴────────┐
        ▼                 ▼
Start transaction    Call real service
                          │
                          ▼
                  PostServiceImpl
                          │
                          ▼
                    Return result
                          │
                          ▼
                  Commit / Rollback
```

Spring example:

```text
Client
   │
   ▼
@Transactional Proxy
   │
   ├── Start transaction
   ├── Call service method
   └── Commit/Rollback
```

Proxy controls **when and how** the object is accessed.

---

## Decorator — Add extra behaviour

```text
              Client
                 │
                 ▼
        Logging Decorator
                 │
       Print "Started"
                 │
                 ▼
        Caching Decorator
                 │
          Check cache
                 │
                 ▼
        Real ReportService
```

Multiple decorators can be stacked:

```text
Client
   │
   ▼
LoggingDecorator
   │
   ▼
CachingDecorator
   │
   ▼
CompressionDecorator
   │
   ▼
RealService
```

### Difference

```text
Proxy
  └── Controls access: transaction, security, lazy loading

Decorator
  └── Adds behaviour: logging, caching, compression
```

### Memory trick

```text
Proxy protects
Decorator improves
```

---

# 6. Strategy vs State

Both commonly contain:

```text
Interface + Multiple Implementations + Context
```

But who selects the implementation is different.

## Strategy — External selection

```text
 User / Configuration
          │
     Selects S3
          ▼
      FileService
          │
          ▼
 FileStorageStrategy
          │
          ▼
   S3StorageStrategy
```

```text
Environment chooses behaviour
```

Example:

```text
DEV  → LocalStorageStrategy
PROD → S3StorageStrategy
```

---

## State — Internal status controls behaviour

```text
                 Post
                  │
         Current state = DRAFT
                  │
                  ▼
              DraftState
                  │
          publish() allowed
                  │
                  ▼
          State becomes PUBLISHED
                  │
                  ▼
            PublishedState
```

State transition:

```text
DRAFT
  │ publish()
  ▼
REVIEW
  │ approve()
  ▼
PUBLISHED
  │ archive()
  ▼
ARCHIVED
```

### Difference

```text
Strategy
   └── Behaviour selected by client/configuration

State
   └── Behaviour selected by object's current status
```

### Memory trick

```text
Strategy = Choice
State    = Condition
```

---

# 7. Observer vs Chain of Responsibility

These are easy to confuse because both involve multiple objects.

## Observer — One event goes to many listeners

```text
                   Post Created
                        │
                        ▼
                 Event Publisher
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
 Email Listener   Kafka Listener   Analytics Listener
```

Everyone receives the same event:

```text
                One Event
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
   Observer 1  Observer 2  Observer 3
```

This is **one-to-many** communication.

---

## Chain — One request passes sequentially

```text
HTTP Request
     │
     ▼
 CORS Filter
     │
     ▼
 JWT Filter
     │
     ▼
Authentication Filter
     │
     ▼
Authorization Filter
     │
     ▼
 Controller
```

Each handler decides:

```text
Process request
      │
      ├── Invalid → Stop chain
      │
      └── Valid   → Pass to next
```

This is **one-after-another** processing.

### Difference

```text
Observer → One event, many independent listeners
Chain    → One request, sequential handlers
```

### Memory trick

```text
Observer = Broadcast
Chain    = Pipeline
```

---

# 8. Template Method vs Strategy

Both are used when some behaviour changes.

## Template Method — Fixed flow, some steps vary

```text
              importData()
                   │
          Fixed parent workflow
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
    Read        Validate      Save
       │           │           │
       └──── Some steps overridden ────┘
```

Class structure:

```text
         DataImportTemplate
                  │
      Fixed importData() workflow
                  │
         ┌────────┴────────┐
         ▼                 ▼
   CsvDataImport     ExcelDataImport
```

Example:

```text
CSV Import
Read CSV → Validate → Process CSV → Save

Excel Import
Read Excel → Validate → Process Excel → Save
```

The overall workflow remains fixed.

---

## Strategy — Complete behaviour can be replaced

```text
              FileService
                   │
                   ▼
         FileStorageStrategy
                   │
         ┌─────────┴─────────┐
         ▼                   ▼
 Local complete logic   S3 complete logic
```

### Difference

```text
Template Method
   └── Same workflow, selected steps vary
   └── Usually inheritance

Strategy
   └── Complete behaviour can vary
   └── Usually composition
```

### Memory trick

```text
Template = Fixed recipe
Strategy = Replace recipe
```

---

# 9. Adapter + Facade + Proxy Together

A real project can use all three:

```text
                    Controller
                        │
                        ▼
                    Facade
             Simplifies business flow
                        │
                        ▼
                 PaymentService
                        │
                        ▼
               Security Proxy
            Checks authentication
                        │
                        ▼
                RazorpayAdapter
          Converts our interface to SDK
                        │
                        ▼
                 RazorpayClient
```

Responsibilities:

```text
Facade  → Hides business complexity
Proxy   → Controls access
Adapter → Converts external interface
```

---

# 10. Observer + Strategy Together

Notification systems commonly use both.

```text
                 Order Placed
                      │
                      ▼
                Event Publisher
                      │
                      ▼
            Notification Listener
                      │
                      ▼
          Notification Strategy Resolver
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
 EmailStrategy    SmsStrategy   WhatsAppStrategy
```

Here:

```text
Observer → Reacts to OrderPlaced event
Strategy → Decides how notification is sent
Factory  → May select the required strategy
```

---

# 11. State + Command Together

Useful in workflow systems:

```text
User Action: "Publish Post"
             │
             ▼
       PublishCommand
             │
             ▼
            Post
             │
      Checks current state
             │
       ┌─────┴─────┐
       ▼           ▼
    DRAFT       PUBLISHED
       │           │
  Allow action   Reject action
```

Here:

```text
Command → Represents requested action
State   → Decides whether action is allowed
```

---

# 12. MVC vs Front Controller

## MVC — Overall layer separation

```text
                 Client
                    │
                    ▼
               Controller
                    │
                    ▼
                 Service
                    │
                    ▼
               Repository
                    │
                    ▼
                 Model
                    │
                    ▼
              DTO / Response
```

MVC separates responsibilities:

```text
Controller → Handles request/response
Model      → Represents data
View       → Displays response
```

---

## Front Controller — One central request entry

```text
              HTTP Request
                    │
                    ▼
           DispatcherServlet
            Front Controller
                    │
                    ▼
             HandlerMapping
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
 UserController PostController AuthController
```

### Relationship

```text
Spring MVC Application
          │
          ▼
DispatcherServlet        → Front Controller
          │
          ▼
Controllers + Models     → MVC
```

### Difference

```text
MVC              → Organizes the complete application
Front Controller → Centralizes incoming request handling
```

---

# 13. Repository + Proxy + Dependency Injection

Spring Data JPA combines these patterns:

```text
               PostService
                    │
             Constructor DI
                    │
                    ▼
          PostRepository Interface
                    │
                    ▼
       Spring-generated Repository Proxy
                    │
                    ▼
             EntityManager/JPA
                    │
                    ▼
                 Database
```

Pattern responsibilities:

```text
Repository
   └── Hides database operations

Proxy
   └── Spring creates runtime implementation

Dependency Injection
   └── Spring injects repository into service
```

Important point:

```text
You write:
PostRepository interface

Spring creates:
Runtime proxy implementation
```

---

# 14. Complete Spring Boot Design-Pattern Flow

This single diagram connects most important patterns:

```text
                          HTTP REQUEST
                                │
                                ▼
                    Security Filter Chain
                  Chain of Responsibility
                                │
                                ▼
                      DispatcherServlet
                       Front Controller
                                │
                                ▼
                          Controller
                              MVC
                                │
                                ▼
                       Service / Facade
                   Simplifies business flow
                                │
              ┌─────────────────┼───────────────────┐
              │                 │                   │
              ▼                 ▼                   ▼
       Strategy/Factory      Repository         Event Publisher
       Select behaviour      DB abstraction       Observer
              │                 │                   │
      ┌───────┴───────┐         ▼            ┌──────┼──────┐
      ▼               ▼    Repository Proxy   ▼      ▼      ▼
 LocalStorage     S3Storage       │          Email  Kafka Analytics
                                  ▼
                         Transaction Proxy
                         @Transactional
                                  │
                                  ▼
                              Database
```

Spring creates and wires these objects through Dependency Injection:

```text
              Spring Container
                     │
                     ├── Creates beans
                     ├── Selects implementations
                     ├── Injects dependencies
                     └── Creates proxies
```

---

# 15. Final Diagram-Based Decision Map

```text
                     WHAT IS THE PROBLEM?
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
 Object Creation       Object Connection      Behaviour / Flow
        │                     │                     │
        │                     │                     │
 One instance?          External mismatch?      Multiple choices?
 → Singleton            → Adapter               → Strategy
        │                     │                     │
 Select object?         Complex subsystem?      Event to many?
 → Factory              → Facade                → Observer
        │                     │                     │
 Many fields?           Control access?         Sequential handlers?
 → Builder              → Proxy                 → Chain
        │                     │                     │
 Copy object?           Add behaviour?          Fixed workflow?
 → Prototype            → Decorator             → Template Method
        │                     │                     │
 Related families?      Tree structure?         Behaviour by status?
 → Abstract Factory     → Composite             → State
                                                    │
                                               Action as object?
                                               → Command
```

For enterprise structure:

```text
Database access abstraction?  → Repository

Application layer separation? → MVC

One entry for HTTP requests?   → Front Controller

Dependency supplied outside?  → Dependency Injection
```

# One final memory diagram

```text
Factory chooses ───────┐
                       ▼
Strategy performs ← DI supplies

Adapter converts
Facade simplifies
Proxy controls
Decorator enhances

Observer broadcasts
Chain processes sequentially

Template fixes workflow
State changes with condition
Command represents action

Repository hides database
MVC separates layers
Front Controller receives requests
```

This relationship-based understanding is enough for most mid-range Java/Spring Boot design-pattern interview questions.
