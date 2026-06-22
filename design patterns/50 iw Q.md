Below is a **professional interview-ready guide for Top 50 Design Pattern questions** for a Java/Spring Boot backend developer. I’ll explain each concept in a clean way with practical examples and Java code where useful.

# Design Patterns Interview Preparation

## Big Picture

A **Design Pattern** is a reusable solution for a common software design problem.

Design patterns are not ready-made code. They are proven ways of structuring code so that it becomes:

* Easy to maintain
* Easy to extend
* Easy to test
* Loosely coupled
* More readable
* More professional

Example:

Without design pattern:

```java
if (paymentType.equals("UPI")) {
    // UPI logic
} else if (paymentType.equals("CARD")) {
    // Card logic
} else if (paymentType.equals("NETBANKING")) {
    // Netbanking logic
}
```

With design pattern, we can use **Strategy Pattern**, where each payment type has its own class.

---

# Categories of Design Patterns

Design patterns are mainly divided into 3 categories:

| Category            | Purpose                | Examples                    |
| ------------------- | ---------------------- | --------------------------- |
| Creational Patterns | Object creation logic  | Singleton, Factory, Builder |
| Structural Patterns | Object/class structure | Adapter, Decorator, Proxy   |
| Behavioral Patterns | Object communication   | Strategy, Observer, Command |

---

# Top 50 Design Pattern Interview Questions

---

## 1. What are Design Patterns?

Design patterns are reusable solutions to common software design problems.

They help us write code that is:

* Flexible
* Maintainable
* Reusable
* Testable
* Loosely coupled

Example:

In Spring Boot, we commonly use patterns like:

* Singleton
* Factory
* Proxy
* Template Method
* Dependency Injection
* Repository
* MVC

---

## 2. Why do we need Design Patterns?

We need design patterns because real-world applications become complex over time.

Without patterns:

```java
if (type.equals("EMAIL")) {
    // send email
} else if (type.equals("SMS")) {
    // send sms
} else if (type.equals("WHATSAPP")) {
    // send whatsapp
}
```

This code becomes difficult to maintain.

With design patterns, we can create separate classes:

```java
interface NotificationService {
    void send(String message);
}
```

Then:

```java
class EmailNotification implements NotificationService {
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}
```

This makes code cleaner and extensible.

---

## 3. What are the main types of Design Patterns?

There are 3 main types:

### 1. Creational Patterns

Used for object creation.

Examples:

* Singleton
* Factory
* Abstract Factory
* Builder
* Prototype

### 2. Structural Patterns

Used to organize classes and objects.

Examples:

* Adapter
* Decorator
* Proxy
* Facade
* Composite

### 3. Behavioral Patterns

Used to manage communication between objects.

Examples:

* Strategy
* Observer
* Command
* Template Method
* Chain of Responsibility

---

## 4. What is the difference between Design Pattern and Architecture Pattern?

| Design Pattern                        | Architecture Pattern                              |
| ------------------------------------- | ------------------------------------------------- |
| Solves small design-level problems    | Solves application-level structure problems       |
| Used inside modules/classes           | Used for whole system architecture                |
| Example: Factory, Strategy, Singleton | Example: MVC, Microservices, Layered Architecture |

Example:

**Factory Pattern** helps create objects.

**Microservices Pattern** helps divide a large application into small services.

---

## 5. What is Singleton Pattern?

Singleton Pattern ensures that only **one object** of a class is created in the entire application.

Example use cases:

* Logger
* Configuration manager
* Cache manager
* Spring beans by default

Basic example:

```java
public class AppConfig {

    private static AppConfig instance;

    private AppConfig() {
    }

    public static AppConfig getInstance() {
        if (instance == null) {
            instance = new AppConfig();
        }
        return instance;
    }
}
```

Problem: This is not thread-safe.

---

## 6. How do you create a thread-safe Singleton in Java?

Using double-checked locking:

```java
public class AppConfig {

    private static volatile AppConfig instance;

    private AppConfig() {
    }

    public static AppConfig getInstance() {
        if (instance == null) {
            synchronized (AppConfig.class) {
                if (instance == null) {
                    instance = new AppConfig();
                }
            }
        }
        return instance;
    }
}
```

Why `volatile`?

Because it prevents JVM instruction reordering and ensures that all threads see the latest value.

---

## 7. What is the best way to create Singleton in Java?

The best and safest way is using `enum`.

```java
public enum AppConfig {
    INSTANCE;

    public void showConfig() {
        System.out.println("Application Configuration");
    }
}
```

Usage:

```java
AppConfig.INSTANCE.showConfig();
```

Why enum singleton is good?

* Thread-safe
* Prevents reflection issue
* Prevents serialization issue
* Simple code

---

## 8. What are the disadvantages of Singleton Pattern?

Singleton has some disadvantages:

* It creates global state
* Difficult to unit test
* Can hide dependencies
* Can violate Single Responsibility Principle
* Overuse makes application tightly coupled

In Spring Boot, we usually don’t create Singleton manually. Spring manages singleton beans by default.

Example:

```java
@Service
public class UserService {
}
```

By default, this bean is singleton in Spring container.

---

## 9. What is Factory Pattern?

Factory Pattern is used when we want to create objects without exposing the object creation logic to the client.

Example:

```java
interface Notification {
    void send(String message);
}
```

```java
class EmailNotification implements Notification {
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}
```

```java
class SmsNotification implements Notification {
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}
```

Factory class:

```java
class NotificationFactory {

    public static Notification getNotification(String type) {
        if (type.equalsIgnoreCase("EMAIL")) {
            return new EmailNotification();
        } else if (type.equalsIgnoreCase("SMS")) {
            return new SmsNotification();
        }
        throw new IllegalArgumentException("Invalid notification type");
    }
}
```

Usage:

```java
Notification notification = NotificationFactory.getNotification("EMAIL");
notification.send("Welcome Danish");
```

---

## 10. What problem does Factory Pattern solve?

Factory Pattern solves the problem of object creation.

Without Factory:

```java
EmailNotification email = new EmailNotification();
```

Client is tightly coupled with concrete class.

With Factory:

```java
Notification notification = NotificationFactory.getNotification("EMAIL");
```

Client depends on interface, not implementation.

This improves loose coupling.

---

## 11. What is the difference between Simple Factory and Factory Method?

| Simple Factory                    | Factory Method                           |
| --------------------------------- | ---------------------------------------- |
| Not an official GoF pattern       | Official GoF pattern                     |
| One factory class creates objects | Subclasses decide which object to create |
| Uses if-else or switch            | Uses inheritance/polymorphism            |
| Simpler                           | More flexible                            |

Simple Factory example:

```java
NotificationFactory.getNotification("EMAIL");
```

Factory Method example:

```java
abstract class NotificationCreator {
    abstract Notification createNotification();
}
```

---

## 12. What is Abstract Factory Pattern?

Abstract Factory Pattern provides a way to create families of related objects without specifying their concrete classes.

Example:

Suppose you have two UI themes:

* Light Theme
* Dark Theme

Each theme has:

* Button
* TextBox

```java
interface Button {
    void render();
}

interface TextBox {
    void render();
}
```

```java
class LightButton implements Button {
    public void render() {
        System.out.println("Light Button");
    }
}

class DarkButton implements Button {
    public void render() {
        System.out.println("Dark Button");
    }
}
```

Abstract factory:

```java
interface UIFactory {
    Button createButton();
    TextBox createTextBox();
}
```

This pattern is useful when objects belong to a family.

---

## 13. Factory Pattern vs Abstract Factory Pattern

| Factory Pattern              | Abstract Factory Pattern                |
| ---------------------------- | --------------------------------------- |
| Creates one type of object   | Creates family of related objects       |
| Simpler                      | More complex                            |
| Example: NotificationFactory | Example: UIFactory for Light/Dark theme |

Example:

Factory:

```java
Create EmailNotification or SmsNotification
```

Abstract Factory:

```java
Create LightButton + LightTextBox together
```

---

## 14. What is Builder Pattern?

Builder Pattern is used to create complex objects step-by-step.

It is useful when a class has many fields, especially optional fields.

Without Builder:

```java
User user = new User("Danish", "Pune", 32, "Java Developer", true);
```

This is hard to read.

With Builder:

```java
User user = new User.Builder()
        .name("Danish")
        .city("Pune")
        .age(32)
        .role("Java Developer")
        .active(true)
        .build();
```

---

## 15. Builder Pattern Java Example

```java
public class User {

    private String name;
    private String city;
    private int age;
    private String role;

    private User(Builder builder) {
        this.name = builder.name;
        this.city = builder.city;
        this.age = builder.age;
        this.role = builder.role;
    }

    public static class Builder {
        private String name;
        private String city;
        private int age;
        private String role;

        public Builder name(String name) {
            this.name = name;
            return this;
        }

        public Builder city(String city) {
            this.city = city;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public Builder role(String role) {
            this.role = role;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}
```

Usage:

```java
User user = new User.Builder()
        .name("Danish")
        .city("Pune")
        .age(32)
        .role("Java Backend Developer")
        .build();
```

---

## 16. When should we use Builder Pattern?

Use Builder Pattern when:

* Object has many fields
* Some fields are optional
* Constructor becomes too long
* You want readable object creation
* Object should be immutable

Real examples:

```java
StringBuilder
```

```java
ResponseEntity.ok().body(response)
```

```java
Lombok @Builder
```

```java
UserDto.builder()
       .name("Danish")
       .email("abc@gmail.com")
       .build();
```

---

## 17. What is Prototype Pattern?

Prototype Pattern is used to create a new object by copying an existing object.

It is useful when object creation is costly.

Example:

```java
class Employee implements Cloneable {

    private String name;
    private String department;

    public Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }

    protected Employee clone() throws CloneNotSupportedException {
        return (Employee) super.clone();
    }
}
```

Usage:

```java
Employee emp1 = new Employee("Danish", "IT");
Employee emp2 = emp1.clone();
```

---

## 18. What is the difference between Shallow Copy and Deep Copy?

| Shallow Copy                  | Deep Copy                        |
| ----------------------------- | -------------------------------- |
| Copies object fields directly | Copies object and nested objects |
| References are shared         | References are not shared        |
| Faster                        | Safer but costlier               |

Example:

If Employee has Address object.

In shallow copy:

```java
emp1.address == emp2.address
```

In deep copy:

```java
emp1.address != emp2.address
```

---

## 19. What is Adapter Pattern?

Adapter Pattern allows two incompatible interfaces to work together.

Real-life example:

Mobile charger adapter.

Software example:

Your application expects this:

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

But third-party gateway has this:

```java
class RazorpayGateway {
    public void makePayment(double amount) {
        System.out.println("Paid using Razorpay: " + amount);
    }
}
```

Adapter:

```java
class RazorpayAdapter implements PaymentProcessor {

    private RazorpayGateway razorpayGateway;

    public RazorpayAdapter(RazorpayGateway razorpayGateway) {
        this.razorpayGateway = razorpayGateway;
    }

    public void pay(double amount) {
        razorpayGateway.makePayment(amount);
    }
}
```

Usage:

```java
PaymentProcessor processor = new RazorpayAdapter(new RazorpayGateway());
processor.pay(5000);
```

---

## 20. When should we use Adapter Pattern?

Use Adapter Pattern when:

* Existing class has incompatible interface
* You want to integrate third-party API
* You do not want to modify existing code
* Legacy system needs to work with new system

Spring example:

Spring MVC uses adapters internally to call different types of controllers.

---

## 21. What is Decorator Pattern?

Decorator Pattern is used to add new behavior to an object dynamically without changing its original class.

Example:

Basic pizza:

```java
interface Pizza {
    String getDescription();
    double getCost();
}
```

```java
class PlainPizza implements Pizza {
    public String getDescription() {
        return "Plain Pizza";
    }

    public double getCost() {
        return 100;
    }
}
```

Decorator:

```java
class CheeseDecorator implements Pizza {

    private Pizza pizza;

    public CheeseDecorator(Pizza pizza) {
        this.pizza = pizza;
    }

    public String getDescription() {
        return pizza.getDescription() + ", Cheese";
    }

    public double getCost() {
        return pizza.getCost() + 40;
    }
}
```

Usage:

```java
Pizza pizza = new CheeseDecorator(new PlainPizza());
System.out.println(pizza.getDescription());
System.out.println(pizza.getCost());
```

---

## 22. Decorator Pattern vs Inheritance

| Decorator                 | Inheritance                   |
| ------------------------- | ----------------------------- |
| Adds behavior dynamically | Adds behavior at compile time |
| Flexible                  | Less flexible                 |
| Uses composition          | Uses inheritance              |
| Avoids class explosion    | Can create many subclasses    |

Example:

Instead of creating:

```java
CheesePizza
CheeseCornPizza
CheeseCornPaneerPizza
```

Use decorators:

```java
new CheeseDecorator(new CornDecorator(new PlainPizza()));
```

---

## 23. What is Proxy Pattern?

Proxy Pattern provides a placeholder object that controls access to the real object.

Example use cases:

* Security proxy
* Logging proxy
* Lazy loading proxy
* Remote proxy
* Caching proxy

Example:

```java
interface BankAccount {
    void withdraw(double amount);
}
```

```java
class RealBankAccount implements BankAccount {
    public void withdraw(double amount) {
        System.out.println("Withdrawn: " + amount);
    }
}
```

Proxy:

```java
class BankAccountProxy implements BankAccount {

    private RealBankAccount realBankAccount;
    private boolean authenticated;

    public BankAccountProxy(boolean authenticated) {
        this.realBankAccount = new RealBankAccount();
        this.authenticated = authenticated;
    }

    public void withdraw(double amount) {
        if (authenticated) {
            realBankAccount.withdraw(amount);
        } else {
            System.out.println("Access denied");
        }
    }
}
```

---

## 24. Where is Proxy Pattern used in Spring?

Spring uses Proxy Pattern heavily.

Examples:

### 1. Spring AOP

When you use:

```java
@Transactional
```

Spring creates proxy around your service method.

### 2. Security

Spring Security can create proxies for method-level security.

```java
@PreAuthorize("hasRole('ADMIN')")
```

### 3. Lazy Loading in Hibernate

Hibernate uses proxy objects for lazy-loaded entities.

Example:

```java
@ManyToOne(fetch = FetchType.LAZY)
private User user;
```

The actual user object may be loaded only when accessed.

---

## 25. What is Facade Pattern?

Facade Pattern provides a simple interface to a complex subsystem.

Example:

Suppose order placement requires:

* Payment
* Inventory
* Invoice
* Notification

Without Facade:

```java
paymentService.pay();
inventoryService.reduceStock();
invoiceService.generate();
notificationService.send();
```

With Facade:

```java
orderFacade.placeOrder(order);
```

Example:

```java
class OrderFacade {

    private PaymentService paymentService;
    private InventoryService inventoryService;
    private NotificationService notificationService;

    public void placeOrder() {
        paymentService.pay();
        inventoryService.reduceStock();
        notificationService.send();
    }
}
```

Facade hides complexity from client.

---

## 26. What is Composite Pattern?

Composite Pattern is used when we need to represent objects in a tree structure.

Example use cases:

* File system
* Organization hierarchy
* Menu and submenu
* Category and subcategory

Example:

```java
interface Employee {
    void showDetails();
}
```

```java
class Developer implements Employee {
    private String name;

    public Developer(String name) {
        this.name = name;
    }

    public void showDetails() {
        System.out.println("Developer: " + name);
    }
}
```

```java
class Manager implements Employee {

    private List<Employee> employees = new ArrayList<>();

    public void addEmployee(Employee employee) {
        employees.add(employee);
    }

    public void showDetails() {
        for (Employee employee : employees) {
            employee.showDetails();
        }
    }
}
```

---

## 27. What is Bridge Pattern?

Bridge Pattern separates abstraction from implementation so both can vary independently.

Example:

Suppose you have:

* Shape: Circle, Rectangle
* Color: Red, Blue

Without Bridge, classes can explode:

```java
RedCircle
BlueCircle
RedRectangle
BlueRectangle
```

With Bridge:

```java
interface Color {
    void applyColor();
}
```

```java
abstract class Shape {
    protected Color color;

    public Shape(Color color) {
        this.color = color;
    }

    abstract void draw();
}
```

```java
class Circle extends Shape {
    public Circle(Color color) {
        super(color);
    }

    void draw() {
        System.out.print("Circle with ");
        color.applyColor();
    }
}
```

Bridge avoids class explosion.

---

## 28. What is Flyweight Pattern?

Flyweight Pattern is used to reduce memory usage by sharing common objects.

Example use cases:

* Caching
* String pool
* Game objects
* Large number of similar objects

Java example:

```java
String s1 = "Java";
String s2 = "Java";
```

Both point to the same string object in string pool.

That is a flyweight-like concept.

---

## 29. What is Strategy Pattern?

Strategy Pattern allows us to define multiple algorithms and choose one at runtime.

Example:

Payment strategy:

```java
interface PaymentStrategy {
    void pay(double amount);
}
```

```java
class UpiPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid using UPI: " + amount);
    }
}
```

```java
class CreditCardPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid using Credit Card: " + amount);
    }
}
```

Context:

```java
class PaymentContext {

    private PaymentStrategy paymentStrategy;

    public PaymentContext(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void makePayment(double amount) {
        paymentStrategy.pay(amount);
    }
}
```

Usage:

```java
PaymentContext context = new PaymentContext(new UpiPayment());
context.makePayment(1000);
```

---

## 30. When should we use Strategy Pattern?

Use Strategy Pattern when:

* You have multiple algorithms
* You want to avoid large if-else/switch
* Behavior should be selected at runtime
* Each behavior should be independently testable

Real examples:

* Payment mode selection
* Sorting logic
* Discount calculation
* Notification channel
* Authentication provider

---

## 31. Strategy Pattern vs Factory Pattern

| Strategy Pattern                | Factory Pattern                         |
| ------------------------------- | --------------------------------------- |
| Focuses on behavior/algorithm   | Focuses on object creation              |
| Chooses how to perform action   | Chooses which object to create          |
| Example: UPI/Card payment logic | Example: Create UPI/Card payment object |

In real projects, both are often used together.

Factory creates the strategy object, then Strategy executes the behavior.

---

## 32. What is Observer Pattern?

Observer Pattern is used when one object changes state and multiple dependent objects need to be notified.

Example:

YouTube channel and subscribers.

When channel uploads a video, all subscribers get notification.

```java
interface Observer {
    void update(String message);
}
```

```java
class Subscriber implements Observer {

    private String name;

    public Subscriber(String name) {
        this.name = name;
    }

    public void update(String message) {
        System.out.println(name + " received: " + message);
    }
}
```

Subject:

```java
class YouTubeChannel {

    private List<Observer> subscribers = new ArrayList<>();

    public void subscribe(Observer observer) {
        subscribers.add(observer);
    }

    public void uploadVideo(String title) {
        for (Observer observer : subscribers) {
            observer.update("New video uploaded: " + title);
        }
    }
}
```

Usage:

```java
YouTubeChannel channel = new YouTubeChannel();
channel.subscribe(new Subscriber("Danish"));
channel.uploadVideo("Design Patterns in Java");
```

---

## 33. Where is Observer Pattern used?

Observer Pattern is used in:

* Event listeners
* Messaging systems
* Notification systems
* Spring Application Events
* Kafka consumer event processing
* UI frameworks

Spring example:

```java
@Component
public class OrderEventListener {

    @EventListener
    public void handleOrderCreatedEvent(OrderCreatedEvent event) {
        System.out.println("Order created: " + event.getOrderId());
    }
}
```

---

## 34. What are the disadvantages of Observer Pattern?

Disadvantages:

* Difficult to debug when many observers exist
* Notification order may be unclear
* Memory leaks can happen if observers are not removed
* Too many notifications can affect performance

In distributed systems, Kafka/RabbitMQ can be considered an event-driven alternative.

---

## 35. What is Template Method Pattern?

Template Method Pattern defines the skeleton of an algorithm in a parent class but allows subclasses to customize certain steps.

Example:

```java
abstract class DataProcessor {

    public final void process() {
        readData();
        validateData();
        saveData();
    }

    abstract void readData();

    void validateData() {
        System.out.println("Validating data");
    }

    abstract void saveData();
}
```

```java
class CsvDataProcessor extends DataProcessor {

    void readData() {
        System.out.println("Reading CSV data");
    }

    void saveData() {
        System.out.println("Saving CSV data");
    }
}
```

Usage:

```java
DataProcessor processor = new CsvDataProcessor();
processor.process();
```

---

## 36. Where is Template Method Pattern used in Spring?

Spring uses Template Method Pattern in:

```java
JdbcTemplate
RestTemplate
TransactionTemplate
KafkaTemplate
RedisTemplate
```

Example:

```java
jdbcTemplate.queryForObject(sql, Integer.class);
```

Spring handles:

* Connection creation
* Statement creation
* Exception handling
* Resource closing

Developer only provides query and mapping logic.

---

## 37. What is Command Pattern?

Command Pattern converts a request into an object.

It is useful for:

* Undo/redo
* Queue processing
* Task scheduling
* Logging requests
* Button click actions

Example:

```java
interface Command {
    void execute();
}
```

```java
class Light {
    void turnOn() {
        System.out.println("Light ON");
    }
}
```

```java
class LightOnCommand implements Command {

    private Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.turnOn();
    }
}
```

Invoker:

```java
class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        command.execute();
    }
}
```

---

## 38. Command Pattern vs Strategy Pattern

| Command Pattern                  | Strategy Pattern                    |
| -------------------------------- | ----------------------------------- |
| Encapsulates a request/action    | Encapsulates an algorithm           |
| Used for undo, queue, logging    | Used for behavior selection         |
| Example: Execute payment command | Example: UPI/Card payment algorithm |

Command says: **what action to execute**.

Strategy says: **how to execute behavior**.

---

## 39. What is Chain of Responsibility Pattern?

Chain of Responsibility passes a request through a chain of handlers.

Each handler decides whether to process the request or pass it to the next handler.

Example use cases:

* Servlet filters
* Spring Security filter chain
* Logging
* Approval workflow
* Exception handling

Example:

```java
abstract class SupportHandler {

    protected SupportHandler next;

    public void setNext(SupportHandler next) {
        this.next = next;
    }

    public abstract void handle(String issue);
}
```

```java
class LevelOneSupport extends SupportHandler {

    public void handle(String issue) {
        if (issue.equals("basic")) {
            System.out.println("Handled by Level 1");
        } else if (next != null) {
            next.handle(issue);
        }
    }
}
```

---

## 40. Where is Chain of Responsibility used in Spring Security?

Spring Security uses a chain of filters.

Example filters:

* Authentication filter
* Authorization filter
* CSRF filter
* CORS filter
* JWT filter
* Exception translation filter

Request flow:

```text
Client Request
   ↓
Security Filter 1
   ↓
Security Filter 2
   ↓
JWT Filter
   ↓
Controller
```

Your custom JWT filter also becomes part of this chain.

---

## 41. What is State Pattern?

State Pattern allows an object to change its behavior when its internal state changes.

Example:

Order states:

* Placed
* Shipped
* Delivered
* Cancelled

```java
interface OrderState {
    void next(Order order);
}
```

```java
class PlacedState implements OrderState {
    public void next(Order order) {
        System.out.println("Order shipped");
        order.setState(new ShippedState());
    }
}
```

```java
class ShippedState implements OrderState {
    public void next(Order order) {
        System.out.println("Order delivered");
    }
}
```

```java
class Order {
    private OrderState state;

    public Order(OrderState state) {
        this.state = state;
    }

    public void setState(OrderState state) {
        this.state = state;
    }

    public void next() {
        state.next(this);
    }
}
```

---

## 42. State Pattern vs Strategy Pattern

| State Pattern                            | Strategy Pattern                             |
| ---------------------------------------- | -------------------------------------------- |
| Behavior changes based on internal state | Behavior changes based on selected algorithm |
| State transition is important            | Algorithm selection is important             |
| Example: Order lifecycle                 | Example: Payment method                      |

State Pattern is useful when object has lifecycle.

Strategy Pattern is useful when object has multiple ways to perform an operation.

---

## 43. What is Iterator Pattern?

Iterator Pattern provides a way to access elements of a collection without exposing internal structure.

Java examples:

```java
List<String> names = List.of("Danish", "Rahul", "Amit");

Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

Enhanced for-loop internally uses iterator:

```java
for (String name : names) {
    System.out.println(name);
}
```

---

## 44. What is Mediator Pattern?

Mediator Pattern reduces direct communication between objects by introducing a mediator object.

Example:

Instead of every service talking directly to every other service, they communicate through a mediator.

Use cases:

* Chat room
* Air traffic control
* UI components
* Event bus

Example:

```java
class ChatRoom {
    public void sendMessage(String user, String message) {
        System.out.println(user + ": " + message);
    }
}
```

```java
class User {
    private String name;
    private ChatRoom chatRoom;

    public User(String name, ChatRoom chatRoom) {
        this.name = name;
        this.chatRoom = chatRoom;
    }

    public void send(String message) {
        chatRoom.sendMessage(name, message);
    }
}
```

---

## 45. What is Memento Pattern?

Memento Pattern is used to save and restore an object’s previous state.

Use cases:

* Undo/redo
* Text editor
* Game save
* Transaction rollback concept

Example:

```java
class EditorMemento {
    private String content;

    public EditorMemento(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}
```

```java
class Editor {
    private String content;

    public void setContent(String content) {
        this.content = content;
    }

    public EditorMemento save() {
        return new EditorMemento(content);
    }

    public void restore(EditorMemento memento) {
        this.content = memento.getContent();
    }
}
```

---

## 46. What is Visitor Pattern?

Visitor Pattern allows us to add new operations to existing object structures without modifying those classes.

Use case:

Suppose we have different items in shopping cart:

* Book
* Electronics
* Grocery

And we want different operations:

* Calculate discount
* Calculate tax
* Generate invoice

Visitor separates operation from object structure.

This pattern is useful, but it can become complex. It is mostly used in compilers, parsers, and object structure traversal.

---

## 47. What is Dependency Injection Pattern?

Dependency Injection means dependencies are provided from outside instead of being created inside the class.

Bad code:

```java
class UserService {
    private UserRepository userRepository = new UserRepository();
}
```

Problem:

`UserService` is tightly coupled with `UserRepository`.

Good code:

```java
class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

Spring Boot example:

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

Benefits:

* Loose coupling
* Easy testing
* Easy replacement
* Better maintainability

---

## 48. Which Design Patterns are used in Spring Framework?

Spring Framework uses many design patterns internally.

| Spring Feature                 | Design Pattern          |
| ------------------------------ | ----------------------- |
| Spring Bean Scope              | Singleton               |
| BeanFactory/ApplicationContext | Factory                 |
| Dependency Injection           | DI Pattern              |
| JdbcTemplate, RestTemplate     | Template Method         |
| Spring AOP                     | Proxy                   |
| Spring MVC                     | MVC                     |
| Spring Security Filter Chain   | Chain of Responsibility |
| Application Events             | Observer                |
| HandlerAdapter                 | Adapter                 |
| Repository Layer               | Repository Pattern      |

Interview answer:

Spring is itself a pattern-heavy framework. It uses Singleton for beans, Factory for bean creation, Proxy for AOP and transactions, Template Method in JdbcTemplate/RestTemplate, and Chain of Responsibility in Spring Security filters.

---

## 49. Which Design Patterns are commonly used in Microservices?

In Microservices, we use both GoF and architectural patterns.

Common patterns:

| Pattern              | Purpose                             |
| -------------------- | ----------------------------------- |
| API Gateway          | Single entry point                  |
| Service Registry     | Service discovery                   |
| Circuit Breaker      | Fault tolerance                     |
| Retry Pattern        | Retry temporary failures            |
| Saga Pattern         | Distributed transaction handling    |
| CQRS                 | Separate read/write models          |
| Event-Driven Pattern | Async communication                 |
| Database per Service | Loose coupling                      |
| Strangler Pattern    | Monolith to microservices migration |
| Bulkhead Pattern     | Failure isolation                   |

Example:

In your Spring Boot microservices project:

* Eureka = Service Registry
* API Gateway = Gateway Pattern
* Resilience4j = Circuit Breaker/Retry
* Kafka = Event-driven Pattern
* Saga = Distributed transaction pattern

---

## 50. How do you decide which Design Pattern to use?

Do not apply patterns blindly.

Use pattern only when there is a real problem.

| Problem                             | Suitable Pattern        |
| ----------------------------------- | ----------------------- |
| Too many object creation conditions | Factory                 |
| Object has many optional fields     | Builder                 |
| Need only one shared instance       | Singleton               |
| Too many if-else algorithms         | Strategy                |
| Need to notify multiple systems     | Observer                |
| Need to add behavior dynamically    | Decorator               |
| Need to control access              | Proxy                   |
| Complex subsystem needs simple API  | Facade                  |
| Request passes through filters      | Chain of Responsibility |
| Object behavior changes by state    | State                   |

Good interview line:

“I don’t use design patterns just to show complexity. I first identify the design problem, then choose the simplest pattern that improves maintainability, extensibility, and testability.”

---

# Most Important Design Patterns for Java Backend Interviews

For Java/Spring Boot backend interviews, focus deeply on these:

| Priority  | Pattern                 | Why Important                |
| --------- | ----------------------- | ---------------------------- |
| Very High | Singleton               | Spring beans                 |
| Very High | Factory                 | Object creation              |
| Very High | Builder                 | DTO/entity creation          |
| Very High | Strategy                | Avoid if-else                |
| Very High | Proxy                   | Spring AOP, transactions     |
| Very High | Template Method         | JdbcTemplate, RestTemplate   |
| Very High | Chain of Responsibility | Spring Security              |
| High      | Observer                | Events, Kafka                |
| High      | Adapter                 | Third-party integration      |
| High      | Facade                  | Service layer simplification |
| Medium    | Decorator               | Runtime behavior enhancement |
| Medium    | State                   | Order/payment lifecycle      |

---

# Scenario-Based Interview Example

## Question: Design a payment system supporting UPI, Card, and NetBanking. Which pattern will you use?

Best answer:

I would use **Strategy Pattern** because each payment mode has different payment logic. The client should not contain multiple if-else conditions. Each payment mode will implement a common interface.

```java
interface PaymentStrategy {
    void pay(double amount);
}
```

```java
class UpiPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid using UPI: " + amount);
    }
}
```

```java
class CardPayment implements PaymentStrategy {
    public void pay(double amount) {
        System.out.println("Paid using Card: " + amount);
    }
}
```

```java
class PaymentService {

    private final PaymentStrategy paymentStrategy;

    public PaymentService(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void processPayment(double amount) {
        paymentStrategy.pay(amount);
    }
}
```

Usage:

```java
PaymentService service = new PaymentService(new UpiPayment());
service.processPayment(5000);
```

Professional explanation:

This design follows Open/Closed Principle. If tomorrow we add Wallet payment, we only add a new class. We do not modify existing payment logic.

---

# Final Interview Revision Notes

Remember these one-liners:

| Pattern                 | One-line Explanation                         |
| ----------------------- | -------------------------------------------- |
| Singleton               | Only one object in application               |
| Factory                 | Creates object based on input                |
| Abstract Factory        | Creates family of related objects            |
| Builder                 | Builds complex object step-by-step           |
| Prototype               | Creates object by cloning                    |
| Adapter                 | Converts one interface into another          |
| Decorator               | Adds behavior dynamically                    |
| Proxy                   | Controls access to real object               |
| Facade                  | Simplifies complex subsystem                 |
| Composite               | Represents tree structure                    |
| Bridge                  | Separates abstraction and implementation     |
| Flyweight               | Shares objects to save memory                |
| Strategy                | Selects algorithm at runtime                 |
| Observer                | Notifies dependents on state change          |
| Template Method         | Defines algorithm skeleton                   |
| Command                 | Encapsulates request as object               |
| Chain of Responsibility | Passes request through handlers              |
| State                   | Changes behavior based on state              |
| Iterator                | Traverses collection                         |
| Mediator                | Centralizes communication                    |
| Memento                 | Saves/restores state                         |
| Visitor                 | Adds new operation without modifying classes |

---

# Best Closing Answer for Interview

You can say:

“Design patterns help us solve recurring design problems in a clean and reusable way. In Java backend development, I commonly use Singleton through Spring beans, Factory for object creation, Builder for DTOs, Strategy to avoid if-else logic, Proxy for Spring AOP and transactions, Template Method in JdbcTemplate/RestTemplate, and Chain of Responsibility in Spring Security filters. I try to use patterns only when they genuinely improve code maintainability and extensibility.”
