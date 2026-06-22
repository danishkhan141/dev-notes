# Index
1. [What are Design Patterns](#1-what-are-design-patterns)
2. [Singleton Design Patern](#singleton-design-pattern)
3. [Factory Design Patterns](#factory-design-pattern)
4. [Builder Desing Pattern](#builder-design-pattern)
5. [Abstract Factory Design Pattern](#abstract-factory-design-pattern)
6. [Prototype Desing Pattern](#prototype-design-pattern)
7. [Adapter Design Pattern](#adapter-design-pattern)
8. [Decorator Design Pattern](#decorator-design-pattern)
9. [Facade Design Patern](#facade-design-pattern)
10. [Proxy Design Pattern](#proxy-design-pattern)
11. [Observer Design Patern](#observer-design-pattern)
12. [Strategy Design Patern](#strategy-design-pattern)
13. [Command Design Patern](#command-design-pattern)
14. [Template Design Patern](#template-design-pattern)
15. [Composite Design Patern](#composite-design-pattern)
16. [State Design Patern](#state-design-pattern)
17. [Saga Design Patern](#saga-design-pattern)

# 1. What are Design Patterns?

### Definition

A **Design Pattern** is a **reusable solution to a common software design problem**.

It is **not a ready-made code**, but a **standard approach or template** to solve recurring problems.

Example problems:

| Problem                               | Pattern   |
| ------------------------------------- | --------- |
| Only one object should exist          | Singleton |
| Object creation should be flexible    | Factory   |
| Add behavior dynamically              | Decorator |
| One change should notify many objects | Observer  |
| Simplify complex subsystem            | Facade    |

---

### Simple Real Life Example

Think of **Design Patterns like architectural blueprints**.

Example:

| House problem      | Architecture solution |
| ------------------ | --------------------- |
| Building staircase | Spiral design         |
| Building roof      | Dome structure        |

Similarly in software:

| Problem                    | Pattern   |
| -------------------------- | --------- |
| Object creation complexity | Factory   |
| Single instance            | Singleton |
| Event notification         | Observer  |

---

# 2. Why Design Patterns Are Used

### 1️⃣ Reusable solutions

Common problems already solved.

### 2️⃣ Improves code readability

Developers understand pattern immediately.

Example:

```
Oh this is Factory Pattern
```

### 3️⃣ Loose coupling

Systems become easier to maintain.

### 4️⃣ Industry standard

Used heavily in:

* Spring Framework
* Hibernate
* Java libraries
* Microservices architecture

---

# 3. Design Pattern Categories

Design patterns are divided into **3 main categories**.

| Category   | Purpose            |
| ---------- | ------------------ |
| Creational | Object creation    |
| Structural | Object composition |
| Behavioral | Object interaction |

---

# 4. Creational Design Patterns

Focus: **How objects are created**

| Pattern          | Purpose                               |
| ---------------- | ------------------------------------- |
| Singleton        | Only one instance                     |
| Factory          | Create objects without exposing logic |
| Abstract Factory | Factory of factories                  |
| Builder          | Build complex objects step-by-step    |
| Prototype        | Clone objects                         |

---

## 4.1 Singleton Pattern

### Definition

Ensure **only one instance of a class exists**.

---

### Real Example

* Logger
* Configuration manager
* Database connection pool

---

### Java Example

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {

        if(instance == null){
            instance = new Singleton();
        }

        return instance;
    }
}
```

Usage:

```java
Singleton obj1 = Singleton.getInstance();
Singleton obj2 = Singleton.getInstance();
```

Both references point to **same object**.

---

### Thread Safe Singleton

```java
public class Singleton {

    private static Singleton instance;

    private Singleton(){}

    public static synchronized Singleton getInstance(){

        if(instance == null){
            instance = new Singleton();
        }

        return instance;
    }
}
```

---

### Real Usage in Java

```
Runtime.getRuntime()
```

---

## 4.2 Factory Pattern

### Problem

Object creation logic becomes messy.

Example:

```
if(type == CAR)
if(type == BIKE)
if(type == TRUCK)
```

---

### Solution

Create a **Factory class**.

---

### Example

Vehicle interface

```java
interface Vehicle {
    void start();
}
```

---

Car class

```java
class Car implements Vehicle {

    public void start(){
        System.out.println("Car Started");
    }
}
```

---

Bike class

```java
class Bike implements Vehicle {

    public void start(){
        System.out.println("Bike Started");
    }
}
```

---

Factory class

```java
class VehicleFactory {

    public static Vehicle getVehicle(String type){

        if(type.equals("car")){
            return new Car();
        }

        if(type.equals("bike")){
            return new Bike();
        }

        return null;
    }
}
```

---

Usage

```java
Vehicle v = VehicleFactory.getVehicle("car");
v.start();
```

---

### Where used?

Used heavily in **Spring Framework**

Example:

```
BeanFactory
ApplicationContext
```

---

## 4.3 Builder Pattern

Used when **object creation has many parameters**.

Example:

```
User(
 name,
 age,
 email,
 phone,
 address,
 gender
)
```

Constructors become messy.

---

### Builder Pattern Example

```java
class User {

    private String name;
    private int age;

    private User(UserBuilder builder){
        this.name = builder.name;
        this.age = builder.age;
    }

    public static class UserBuilder {

        private String name;
        private int age;

        public UserBuilder setName(String name){
            this.name = name;
            return this;
        }

        public UserBuilder setAge(int age){
            this.age = age;
            return this;
        }

        public User build(){
            return new User(this);
        }
    }
}
```

Usage

```java
User user = new User.UserBuilder()
                .setName("Danish")
                .setAge(30)
                .build();
```

---

# 5. Structural Design Patterns

Focus: **How classes are structured**

| Pattern   | Purpose            |
| --------- | ------------------ |
| Adapter   | Convert interface  |
| Decorator | Add functionality  |
| Facade    | Simplify subsystem |
| Proxy     | Control access     |
| Composite | Tree structure     |

---

## 5.1 Adapter Pattern

Used when **two incompatible interfaces need to work together**.

Example:

```
Laptop charger -> adapter -> wall socket
```

---

### Java Example

Old interface

```java
interface OldCharger {
    void chargeWithRoundPin();
}
```

New interface

```java
interface NewCharger {
    void chargeWithTypeC();
}
```

Adapter

```java
class ChargerAdapter implements NewCharger {

    OldCharger oldCharger;

    public ChargerAdapter(OldCharger oldCharger){
        this.oldCharger = oldCharger;
    }

    public void chargeWithTypeC(){
        oldCharger.chargeWithRoundPin();
    }
}
```

---

## 5.2 Decorator Pattern

Used to **add behavior dynamically**.

Example:

```
Coffee + Milk + Sugar + Chocolate
```

---

Example in Java:

```
BufferedInputStream
BufferedReader
```

They **decorate streams**.

---

## 5.3 Facade Pattern

Used to **hide complex subsystem**.

Example:

```
Computer.start()
```

Internally it does:

```
CPU.start()
RAM.load()
Disk.read()
```

But user calls only:

```
computer.start()
```

---

# 6. Behavioral Design Patterns

Focus: **Communication between objects**

| Pattern  | Purpose                   |
| -------- | ------------------------- |
| Observer | Event notification        |
| Strategy | Multiple algorithms       |
| Command  | Encapsulate request       |
| Template | Define algorithm skeleton |
| State    | Object state change       |

---

## 6.1 Observer Pattern

Used when **one object change should notify many objects**.

Example:

```
YouTube subscriber notifications
```

When channel uploads video → notify subscribers.

---

Example

Subject

```java
interface Subject {
    void addObserver(Observer o);
    void notifyObservers();
}
```

Observer

```java
interface Observer {
    void update();
}
```

---

Real world usage

```
Spring Event Listener
Kafka consumers
GUI listeners
```

---

## 6.2 Strategy Pattern

Used when **multiple algorithms exist for same task**.

Example:

```
Payment
```

Options:

```
CreditCard
UPI
PayPal
```

---

Strategy interface

```java
interface PaymentStrategy {
    void pay(int amount);
}
```

Implementation

```java
class UpiPayment implements PaymentStrategy {

    public void pay(int amount){
        System.out.println("Paid via UPI");
    }
}
```

Usage

```java
PaymentStrategy payment = new UpiPayment();
payment.pay(1000);
```

---

# 7. Design Patterns Used in Spring

Very important for interviews.

| Spring Feature | Pattern   |
| -------------- | --------- |
| Spring Beans   | Singleton |
| BeanFactory    | Factory   |
| Spring AOP     | Proxy     |
| JdbcTemplate   | Template  |
| Spring Events  | Observer  |

---

# 8. Interview Questions

### Q1 What is design pattern?

Reusable solution for common software design problems.

---

### Q2 Difference between Factory and Abstract Factory

Factory → creates **one product**

Abstract Factory → creates **family of products**

---

### Q3 Difference between Strategy and Factory

Factory → object creation
Strategy → algorithm selection

---

### Q4 Why Singleton used?

To ensure **single instance globally**.

---

# 9. Most Important Patterns for Java Interviews

Focus on these first:

1️⃣ Singleton
2️⃣ Factory
3️⃣ Builder
4️⃣ Observer
5️⃣ Strategy
6️⃣ Decorator
7️⃣ Adapter
8️⃣ Facade
9️⃣ Proxy
10️⃣ Template Method

---

# 10. Design Patterns in Microservices

Important real usages:

| Pattern   | Microservices Example |
| --------- | --------------------- |
| Singleton | Config manager        |
| Factory   | Service creation      |
| Strategy  | Payment services      |
| Observer  | Kafka events          |
| Facade    | API Gateway           |

---

Very good question Danish 👍
This is actually **how senior developers learn Design Patterns**.
If you follow the correct **learning framework**, any Design Pattern becomes easy to understand and remember for interviews.

Think of it like a **checklist** you follow every time you study a new DP.

---

# The Correct Way to Learn Any Design Pattern (Professional Approach)

Whenever you learn **any Design Pattern**, you should always cover these **8 points**.

```
1. Problem
2. Intent (Goal of Pattern)
3. Structure
4. Components
5. Implementation
6. Real-world example
7. Advantages & disadvantages
8. Real usage in frameworks
```

If you follow this order, **every pattern becomes crystal clear**.

---

# 1️⃣ Problem (Why this pattern exists)

First question you must ask:

👉 **What problem does this pattern solve?**

If you skip this, pattern becomes **just theory**.

Example:

### Singleton

Problem:

```
Sometimes we want only ONE object of a class.
```

Example:

* Logger
* Configuration manager
* DB connection pool

Without Singleton:

```
new Logger()
new Logger()
new Logger()
```

Multiple instances → **problem**

---

# 2️⃣ Intent (Goal of Pattern)

Intent means **what the pattern tries to achieve**.

This is usually **one sentence**.

Example:

Singleton intent:

```
Ensure a class has only one instance and provide global access to it.
```

Factory intent:

```
Create objects without exposing creation logic.
```

Observer intent:

```
Define one-to-many dependency between objects.
```

Interviewers often ask **intent**.

---

# 3️⃣ Structure (Diagram / Architecture)

Understand **how classes are organized**.

Typical questions:

* Which classes exist?
* Who talks to whom?
* Who creates objects?

Example (Factory pattern structure)

```
Client
   ↓
Factory
   ↓
Product Interface
   ↓
Concrete Products
```

Understanding structure helps you **design the pattern in interviews**.

---

# 4️⃣ Components (Roles in the Pattern)

Every pattern has **specific roles**.

Example: Observer Pattern

Components:

| Component        | Role                |
| ---------------- | ------------------- |
| Subject          | Maintains observers |
| Observer         | Receives updates    |
| ConcreteSubject  | Actual data holder  |
| ConcreteObserver | Actual listener     |

Understanding roles makes **implementation easier**.

---

# 5️⃣ Implementation (Code)

Now understand **actual code implementation**.

Ask these questions:

* Which class creates objects?
* Which interface is used?
* How objects communicate?

Example (Factory)

```
Product interface
Concrete products
Factory class
Client
```

Code flow should be clear.

---

# 6️⃣ Real World Analogy

Patterns become **easy to remember** with analogies.

Example:

| Pattern   | Real World            |
| --------- | --------------------- |
| Singleton | Prime Minister        |
| Factory   | Car factory           |
| Observer  | YouTube subscribers   |
| Adapter   | Phone charger adapter |
| Decorator | Coffee toppings       |
| Facade    | ATM machine           |

If analogy is clear → pattern becomes **permanent memory**.

---

# 7️⃣ Advantages and Disadvantages

Every pattern solves something but also adds complexity.

Example:

### Singleton

Advantages

* Single instance
* Global access

Disadvantages

* Hard to test
* Breaks dependency injection

Example:

### Factory

Advantages

* Loose coupling
* Easy to extend

Disadvantages

* More classes

Interviewers often ask:

```
When NOT to use this pattern?
```

---

# 8️⃣ Real Usage in Frameworks

This is **very important for interviews**.

Always ask:

👉 **Where is this pattern used in real frameworks?**

Example:

| Pattern   | Java / Spring Example |
| --------- | --------------------- |
| Singleton | Spring beans          |
| Factory   | BeanFactory           |
| Proxy     | Spring AOP            |
| Observer  | Spring Events         |
| Decorator | Java IO streams       |
| Template  | JdbcTemplate          |

This shows **industry knowledge**.

---

# The Ultimate Learning Template (Use this for every DP)

Whenever you study a design pattern, follow this template.

```
1. Problem
2. Intent
3. Real World Analogy
4. Structure Diagram
5. Components / Roles
6. Java Implementation
7. Advantages
8. Disadvantages
9. Where used in Java / Spring
10. Interview Questions
```

If you prepare notes like this → **interview ready**.

---

# Example Template (Quick)

### Singleton Pattern

Problem
Need only one object

Intent
Ensure single instance

Analogy
Prime Minister

Structure
Static instance

Components

```
Singleton class
Private constructor
getInstance()
```

Implementation

Java code

Advantages

* memory efficient

Disadvantages

* testing difficulty

Framework usage

Spring Beans

---

# Pro Tip (How Senior Engineers Think)

Instead of memorizing patterns, they think:

```
What problem am I solving?
```

Example:

Problem → Multiple object creation
Solution → Singleton

Problem → Complex object creation
Solution → Builder

Problem → Event notification
Solution → Observer

---
```
Singleton
Factory
Abstract Factory
Builder
Prototype
Adapter
Decorator
Facade
Observer
Strategy
```
---
# Singleton Design pattern
Sure Danish 👍
I’ll explain **Singleton Design Pattern** in a **professional interview-ready format** so that you can easily **make notes and revise quickly**.

---

# Singleton Design Pattern (Creational Pattern)

Singleton ensures that **only one instance of a class exists in the entire application** and provides a **global access point** to that instance.

---

# 1. Problem

In many applications, some objects should **exist only once**.

Examples:

* Configuration manager
* Logging system
* Cache manager
* Thread pools
* Database connection manager

### Problem Scenario

Imagine if every class creates its own Logger.

```
UserService → new Logger()
OrderService → new Logger()
PaymentService → new Logger()
```

Problems:

* Memory waste
* Configuration inconsistency
* Resource duplication

We need:

```
Only ONE Logger instance
shared by all classes
```

This problem is solved by **Singleton Pattern**.

---

# 2. Intent

The **intent of Singleton pattern** is:

> Ensure a class has only one instance and provide a global point of access to it.

Key ideas:

1️⃣ Only **one object** should exist
2️⃣ **Global access point** should be available
3️⃣ Object creation should be **controlled internally**

---

# 3. Real World Analogy

### Example: Prime Minister

In a country:

* There can be **only one Prime Minister**
* Everyone accesses the **same PM**

You cannot create multiple PMs.

```
Country → Prime Minister
          (only one)
```

Another example:

### Printer Spooler

In OS:

```
Multiple Applications
        ↓
   Printer Spooler
        ↓
      Printer
```

Only **one spooler manages the printer queue**.

---

# 4. Structure Diagram

```
          +-------------------+
          |     Singleton     |
          +-------------------+
          | - instance        |  (static)
          +-------------------+
          | - Singleton()     |  (private constructor)
          +-------------------+
          | + getInstance()   |
          +-------------------+
```

Usage:

```
Client → Singleton.getInstance()
```

---

# 5. Components / Roles

### 1️⃣ Singleton Class

Responsibilities:

* Creates **only one instance**
* Stores instance in **static variable**
* Provides **global access method**

Key features:

```
private constructor
static instance variable
public static getInstance()
```

---

### 2️⃣ Client

Any class that uses Singleton.

Example:

```
UserService
OrderService
PaymentService
```

They access instance via:

```
Singleton.getInstance()
```

---

# 6. Java Implementation

There are **multiple ways to implement Singleton**.

---

# 1️⃣ Eager Initialization Singleton

Instance created at **class loading time**.

### Code

```java
public class Singleton {

    private static final Singleton instance = new Singleton();

    private Singleton() {
        System.out.println("Singleton Created");
    }

    public static Singleton getInstance() {
        return instance;
    }
}
```

Usage

```java
public class Test {
    public static void main(String[] args) {

        Singleton s1 = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();

        System.out.println(s1 == s2); // true
    }
}
```

### Pros

* Simple
* Thread safe

### Cons

* Object created even if **not used**

---

# 2️⃣ Lazy Initialization Singleton

Object created **only when needed**.

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {

        if(instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Problem:

❌ **Not thread safe**

Multiple threads may create multiple instances.

---

# 3️⃣ Thread Safe Singleton (Synchronized)

```java
public class Singleton {

    private static Singleton instance;

    private Singleton() {}

    public static synchronized Singleton getInstance() {

        if(instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Problem:

```
Synchronization overhead
slow performance
```

---

# 4️⃣ Double Checked Locking (Best for Interviews)

```java
public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {

        if(instance == null) {

            synchronized (Singleton.class) {

                if(instance == null) {
                    instance = new Singleton();
                }

            }
        }

        return instance;
    }
}
```

### Why volatile?

Prevents **instruction reordering issues in multithreading**.

---

# 5️⃣ Bill Pugh Singleton (Recommended)

Best practice implementation.

```java
public class Singleton {

    private Singleton() {}

    private static class SingletonHelper {

        private static final Singleton INSTANCE = new Singleton();

    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

Advantages:

* Lazy loading
* Thread safe
* No synchronization cost

---

# 6️⃣ Enum Singleton (Most Secure)

```java
public enum Singleton {

    INSTANCE;

    public void showMessage() {
        System.out.println("Hello Singleton");
    }
}
```

Usage:

```java
Singleton.INSTANCE.showMessage();
```

Benefits:

* Thread safe
* Serialization safe
* Reflection safe

---

# 7. Advantages

### 1️⃣ Controlled Instance Creation

Ensures **only one object exists**.

---

### 2️⃣ Memory Efficient

Instead of multiple objects:

```
Service A → Singleton
Service B → Singleton
Service C → Singleton
```

Same instance reused.

---

### 3️⃣ Global Access Point

Accessible anywhere via:

```
Singleton.getInstance()
```

---

### 4️⃣ Useful for Shared Resources

Example:

* Cache
* Configuration
* Logging

---

# 8. Disadvantages

### 1️⃣ Global State

Acts like **global variable**

May lead to tight coupling.

---

### 2️⃣ Harder Unit Testing

Mocking Singleton is difficult.

---

### 3️⃣ Multithreading Complexity

Need proper synchronization.

---

### 4️⃣ Breaks SOLID Principles

Violates:

```
Single Responsibility
Dependency Inversion
```

---

# 9. Where Used in Java / Spring

### In Java

Example:

### Runtime class

```
Runtime runtime = Runtime.getRuntime();
```

Only **one runtime instance** exists.

---

### Logger

```
Logger logger = Logger.getLogger("AppLogger");
```

---

### Desktop

```
Desktop.getDesktop()
```

---

### In Spring Framework

In Spring:

```
Beans are singleton by default
```

Example:

```java
@Service
public class PaymentService {
}
```

Spring container creates **one instance** and shares it.

Scope:

```
singleton (default)
prototype
request
session
```

---

# 10. Interview Questions

### Q1: What is Singleton Pattern?

Ensures **only one instance of a class exists** and provides **global access point**.

---

### Q2: Why constructor is private?

To **prevent object creation using new keyword**.

---

### Q3: How Singleton breaks?

1️⃣ Reflection
2️⃣ Serialization
3️⃣ Cloning

---

### Q4: How to prevent Singleton breaking?

Best solution:

```
Enum Singleton
```

---

### Q5: Difference between Singleton and Static class?

| Singleton                | Static          |
| ------------------------ | --------------- |
| Object exists            | No object       |
| Supports inheritance     | No inheritance  |
| Lazy initialization      | No lazy loading |
| Can implement interfaces | Cannot          |

---

### Q6: Is Spring Singleton same as GoF Singleton?

No.

Spring Singleton:

```
One instance per Spring container
```

GoF Singleton:

```
One instance per JVM
```

---

# Quick Revision Notes (1 minute)

```
Singleton Pattern

Purpose
Ensure only one instance of class

Key Features
private constructor
static instance
public getInstance()

Common Implementations
Eager
Lazy
Synchronized
Double Checked Locking
Bill Pugh
Enum

Best Practice
Enum Singleton

Examples
Runtime.getRuntime()
Spring Beans
Logger
```
---
Good questions Danish 👍 — these are exactly the **depth-level questions interviewers ask after the basic Singleton explanation**.
I’ll answer them **clearly with practical examples and Java code** so you can use them in interviews and notes.

---

# 1️⃣ Singleton Example based on Configuration

### Problem

In real applications, configuration values should be **loaded once and shared everywhere**.

Example config file:

```properties
db.url=jdbc:mysql://localhost:3306/app
db.username=root
db.password=123
```

If every class reads the config file separately:

```
UserService → read file
OrderService → read file
PaymentService → read file
```

Problems:

* File I/O multiple times
* Performance issue
* Duplicate configuration loading

Solution → **Singleton Configuration Manager**

---

## Java Example

```java
import java.util.Properties;
import java.io.FileInputStream;

public class ConfigManager {

    private static ConfigManager instance;
    private Properties properties;

    private ConfigManager() {

        properties = new Properties();

        try {
            FileInputStream fis = new FileInputStream("config.properties");
            properties.load(fis);
        } catch(Exception e) {
            e.printStackTrace();
        }
    }

    public static ConfigManager getInstance() {

        if(instance == null) {
            instance = new ConfigManager();
        }

        return instance;
    }

    public String getProperty(String key) {
        return properties.getProperty(key);
    }
}
```

---

## Usage

```java
public class Test {

    public static void main(String[] args) {

        ConfigManager config = ConfigManager.getInstance();

        String dbUrl = config.getProperty("db.url");

        System.out.println(dbUrl);
    }
}
```

---

### Architecture

```
               +------------------+
               |  ConfigManager   |
               |   (Singleton)    |
               +------------------+
                    ↑
        ------------------------------
        ↑             ↑             ↑
   UserService   OrderService   PaymentService
```

All services use **same configuration instance**.

---

# 2️⃣ Singleton Example based on Cache

Cache is a **very common real-world Singleton example**.

### Problem

If each service creates its own cache:

```
UserService → Cache
OrderService → Cache
PaymentService → Cache
```

Memory waste and inconsistency.

Instead:

```
            Shared Cache
                ↑
      All services access same cache
```

---

## Java Cache Singleton

```java
import java.util.HashMap;
import java.util.Map;

public class CacheManager {

    private static CacheManager instance;

    private Map<String, Object> cache;

    private CacheManager() {
        cache = new HashMap<>();
    }

    public static CacheManager getInstance() {

        if(instance == null) {
            instance = new CacheManager();
        }

        return instance;
    }

    public void put(String key, Object value) {
        cache.put(key, value);
    }

    public Object get(String key) {
        return cache.get(key);
    }
}
```

---

## Usage

```java
public class Test {

    public static void main(String[] args) {

        CacheManager cache = CacheManager.getInstance();

        cache.put("user1", "Danish");

        System.out.println(cache.get("user1"));
    }
}
```

---

### Real World Example

```
Netflix / Amazon
        ↓
Shared Cache
        ↓
All microservices use same cached data
```

---

# 3️⃣ How ENUM Singleton Works

This confuses many developers.

### Example

```java
public enum Singleton {

    INSTANCE;

    public void showMessage() {
        System.out.println("Hello Singleton");
    }
}
```

Usage:

```java
Singleton s = Singleton.INSTANCE;
s.showMessage();
```

---

## How constructor exists in Enum?

Actually **Java automatically creates the constructor**.

Internally Java converts it to something like this:

```java
final class Singleton extends Enum<Singleton> {

    public static final Singleton INSTANCE;

    static {
        INSTANCE = new Singleton();
    }

    private Singleton() {
        super("INSTANCE", 0);
    }
}
```

So:

```
INSTANCE → created once by JVM
```

---

### Why Enum Singleton is Best

Enum protects against:

| Problem        | Normal Singleton | Enum   |
| -------------- | ---------------- | ------ |
| Reflection     | ❌ broken         | ✅ safe |
| Serialization  | ❌ broken         | ✅ safe |
| Multithreading | ❌ tricky         | ✅ safe |

Because **JVM guarantees single instance of enum constant**.

---

### Real Usage

```java
public enum Logger {

    INSTANCE;

    public void log(String message) {
        System.out.println(message);
    }
}
```

Usage

```java
Logger.INSTANCE.log("Application Started");
```

---

# 4️⃣ How Singleton Breaks SOLID Principles

Mainly it breaks **2 SOLID principles**.

---

# 1️⃣ Single Responsibility Principle (SRP)

SRP says:

> A class should have only one responsibility.

But Singleton does **two responsibilities**:

```
1 → Business logic
2 → Object lifecycle management
```

Example

```java
class Logger {

   private static Logger instance;

   public static Logger getInstance() { ... }

   public void log() { ... }

}
```

This class handles:

```
logging logic
+
object creation
```

Two responsibilities.

---

# 2️⃣ Dependency Inversion Principle (DIP)

DIP says:

> Depend on abstraction, not concrete class.

But Singleton forces:

```
Service → Singleton.getInstance()
```

Example

```java
UserService {

   Logger logger = Logger.getInstance();

}
```

Now:

```
UserService tightly coupled to Logger
```

Better approach (Spring style):

```
Dependency Injection
```

---

# 5️⃣ What is meant by "Singleton Breaking"

Singleton breaking means:

> Creating multiple objects even though the pattern intends only one instance.

There are **3 major ways Singleton can break**.

---

# 1️⃣ Reflection Breaking

Reflection can access private constructor.

Example

```java
Constructor<Singleton> constructor =
Singleton.class.getDeclaredConstructor();

constructor.setAccessible(true);

Singleton s1 = Singleton.getInstance();
Singleton s2 = constructor.newInstance();

System.out.println(s1 == s2); // false
```

Now **two instances exist**.

---

# 2️⃣ Serialization Breaking

When Singleton is serialized and deserialized.

Example

```
Singleton → serialized → file
file → deserialized → new object
```

Two instances created.

Solution:

```
readResolve()
```

---

# 3️⃣ Cloning Breaking

If Singleton implements Cloneable.

```
clone() → creates new object
```

Now multiple instances.

---

# How Enum fixes all these

Enum Singleton is safe because:

```
JVM prevents
Reflection creation
Serialization duplication
Cloning
```

That’s why Joshua Bloch (Effective Java) recommends:

> **Enum Singleton is the best way to implement Singleton in Java.**

---

# ⭐ Interview Tip (Very Important)

When interviewer asks:

**Best Singleton implementation?**

Answer:

```
Enum Singleton
or
Bill Pugh Singleton
```

---

# ⭐ Quick Interview Summary

```
Singleton Pattern

Purpose
Only one instance of class

Common Use Cases
Configuration Manager
Cache Manager
Logger
Thread Pool
Database Connection

Best Implementation
Enum Singleton

Breaking Methods
Reflection
Serialization
Cloning

SOLID Violations
SRP
DIP
```
---
# Factory Design Pattern
Factory Pattern interview mein **bahut frequently pucha jata hai**, especially Java, Spring aur Microservices roles ke liye.
Main ise **structured notes format** mein explain kar raha hoon so that aap easily **interview notes bana sakein**.

---

# Factory Design Pattern (Creational Pattern)

## 1. Problem

Suppose aapke paas ek system hai jisme **multiple object types create karne padte hain**.

Example:

```java
if(type.equals("CAR")){
    return new Car();
}
else if(type.equals("BIKE")){
    return new Bike();
}
else if(type.equals("TRUCK")){
    return new Truck();
}
```

Problems:

1. **Object creation logic har jagah repeat hoga**
2. **Code tightly coupled ho jayega**
3. **New class add karna mushkil hoga**
4. **OCP (Open Closed Principle) violate hota hai**

Matlab:

Client ko **object kaise create ho raha hai ye pata nahi hona chahiye**.

Is problem ko solve karta hai **Factory Pattern**.

---

# 2. Intent

Factory Pattern ka main purpose:

> **Object creation logic ko centralize karna aur client ko actual class se decouple karna**

Simple words:

Client sirf bolega:

```
Mujhe ek Vehicle chahiye
```

Factory decide karegi:

```
Car dena hai
Bike dena hai
Truck dena hai
```

Client ko **new keyword use karna hi nahi padega**.

---

# 3. Real World Analogy

### Restaurant Example

Restaurant mein:

Customer bolta hai:

```
Mujhe Pizza chahiye
```

Customer kitchen mein jaake pizza nahi banata.

Kitchen decide karegi:

* Veg Pizza
* Cheese Pizza
* Paneer Pizza

Kitchen = **Factory**

Pizza = **Product**

Customer = **Client**

---

# 4. Structure Diagram

```
           Product (Interface)
                 |
        --------------------
        |        |        |
       Car      Bike     Truck
        |        |        |
        -------------------
                 |
           VehicleFactory
                 |
               Client
```

Flow:

```
Client → Factory → Concrete Object
```

---

# 5. Components / Roles

### 1. Product (Interface)

Common interface for all objects.

Example:

```
Vehicle
```

---

### 2. Concrete Products

Actual implementations.

```
Car
Bike
Truck
```

---

### 3. Factory Class

Responsible for creating objects.

```
VehicleFactory
```

---

### 4. Client

Jo object use karta hai.

```
Main class / Service class
```

---

# 6. Java Implementation

## Step 1 — Product Interface

```java
public interface Vehicle {

    void start();

}
```

---

## Step 2 — Concrete Products

### Car

```java
public class Car implements Vehicle {

    @Override
    public void start() {
        System.out.println("Car is starting");
    }

}
```

---

### Bike

```java
public class Bike implements Vehicle {

    @Override
    public void start() {
        System.out.println("Bike is starting");
    }

}
```

---

### Truck

```java
public class Truck implements Vehicle {

    @Override
    public void start() {
        System.out.println("Truck is starting");
    }

}
```

---

## Step 3 — Factory Class

```java
public class VehicleFactory {

    public static Vehicle getVehicle(String type) {

        if(type.equalsIgnoreCase("car")) {
            return new Car();
        }

        else if(type.equalsIgnoreCase("bike")) {
            return new Bike();
        }

        else if(type.equalsIgnoreCase("truck")) {
            return new Truck();
        }

        return null;
    }
}
```

---

## Step 4 — Client

```java
public class Main {

    public static void main(String[] args) {

        Vehicle vehicle = VehicleFactory.getVehicle("car");

        vehicle.start();

    }
}
```

Output

```
Car is starting
```

---

# 7. Advantages

### 1. Loose Coupling

Client ko actual class ka pata nahi hota.

```
Vehicle v = VehicleFactory.getVehicle("car");
```

Client ko nahi pata:

```
new Car()
```

---

### 2. Centralized Object Creation

Object creation ek hi place par hota hai.

---

### 3. Easy Maintenance

Agar object creation logic change ho jaye toh sirf **Factory class update hogi**.

---

### 4. Supports Open Closed Principle

New classes add kar sakte hain without changing client code.

---

### 5. Cleaner Code

Multiple `new` keywords avoid ho jate hain.

---

# 8. Disadvantages

### 1. Factory Class Complex ho sakti hai

Agar bahut types ho gaye.

```
if
else if
else if
else if
```

---

### 2. New Type Add Karne par Factory Modify Karni Padti Hai

Which slightly violates OCP.

Isliye **Factory Method Pattern** aur **Abstract Factory Pattern** exist karte hain.

---

# 9. Where Used in Java / Spring / Microservices

### 1. Java Calendar API

```java
Calendar cal = Calendar.getInstance();
```

Internally:

```
CalendarFactory decide karta hai
```

---

### 2. Logger

```java
Logger logger = LoggerFactory.getLogger(MyClass.class);
```

Yaha **LoggerFactory** ek factory hai.

---

### 3. JDBC DriverManager

```java
Connection con = DriverManager.getConnection(url);
```

DriverManager internally **connection object create karta hai**.

---

### 4. Spring Framework

Spring IoC container is basically a **Super Factory**.

Example:

```java
ApplicationContext context = new ClassPathXmlApplicationContext("config.xml");

UserService service = context.getBean(UserService.class);
```

Yaha:

```
Spring container = Factory
```

---

### 5. Microservices Example

Payment service:

```
PaymentFactory.getPaymentMethod("UPI")
PaymentFactory.getPaymentMethod("CARD")
PaymentFactory.getPaymentMethod("NETBANKING")
```

Factory decide karegi:

```
UPIPayment
CardPayment
NetBankingPayment
```

---

# 10. Interview Questions

### Q1 What is Factory Design Pattern?

Factory Pattern ek **Creational Design Pattern** hai jo **object creation logic ko encapsulate karta hai aur client ko concrete classes se decouple karta hai**.

---

### Q2 Difference Between Factory and Constructor

Constructor:

```
Car car = new Car();
```

Factory:

```
Vehicle car = VehicleFactory.getVehicle("car");
```

Factory hides object creation logic.

---

### Q3 Why Factory Pattern is used?

* Loose coupling
* Centralized object creation
* Cleaner code
* Maintainability

---

### Q4 Difference Between Simple Factory and Factory Method

Simple Factory:

```
One factory class
```

Factory Method:

```
Multiple factory subclasses
```

---

### Q5 Is Spring a Factory?

Yes.

Spring **IoC Container** acts like a **super factory** that creates and manages beans.

---

### Q6 What SOLID principle it supports?

Supports:

```
Dependency Inversion Principle
Open Closed Principle
```

---

# Short Interview Definition (Important)

If interviewer asks:

**Explain Factory Pattern in one line**

Answer:

> Factory Pattern is a creational design pattern that provides a way to create objects without exposing the instantiation logic to the client.

---

# Builder Design Pattern
Danish, Builder Design Pattern **interview mein bahut commonly pucha jaata hai**, especially Java / Spring Boot developers ke liye. Main isko **notes-friendly + interview-ready structure** mein explain kar raha hoon so that you can **directly convert this into your notes or YouTube explanation later**.

---

# Builder Design Pattern (Creational Design Pattern)

Builder pattern **complex objects ko step-by-step create karne ke liye use hota hai**, especially jab object mein **bahut saare optional parameters ho**.

---

# 1. Problem

Suppose hume ek **User object** create karna hai.

Example fields:

```
User
- id
- name
- email
- phone
- address
- age
- country
```

### Traditional Constructor Approach

```java
User user = new User(1, "Danish", "danish@gmail.com",
                     "9999999999", "Delhi", 30, "India");
```

Problems:

1️⃣ **Constructor Overloading Explosion**

```
User(int id, String name)
User(int id, String name, String email)
User(int id, String name, String email, String phone)
User(int id, String name, String email, String phone, String address)
```

Constructors **bohot zyada ho jaate hain**.

---

2️⃣ **Readability Poor**

```
new User(1,"Danish","danish@gmail.com","9999","Delhi",30,"India")
```

Samajhna mushkil hai **kaunsa parameter kya represent karta hai**.

---

3️⃣ **Optional Parameters Problem**

Agar kuch parameters optional ho:

```
phone = null
address = null
```

Code messy ho jata hai.

---

# 2. Intent

Builder Pattern ka goal hai:

> **Construct a complex object step-by-step and separate the construction from its representation.**

Simple words:

👉 Complex objects ko **step-by-step build karo**
👉 Object creation **readable aur flexible banao**

---

# 3. Real World Analogy

### 🍔 Burger Shop Example

Burger order karte time:

You choose:

```
Bread
Patty
Cheese
Sauce
Veggies
```

Customer bolta hai:

```
Burger burger = Burger.builder()
                      .bread("Wheat")
                      .patty("Chicken")
                      .cheese(true)
                      .sauce("BBQ")
                      .build();
```

Burger **step-by-step build hota hai**.

Yahi Builder Pattern hai.

---

# 4. Structure Diagram

```
        +------------------+
        |     Director     |
        +------------------+
                 |
                 v
        +------------------+
        |      Builder     |  (Interface)
        +------------------+
        | buildPartA()     |
        | buildPartB()     |
        | getResult()      |
        +------------------+
                 ^
                 |
        +------------------+
        | ConcreteBuilder  |
        +------------------+
        | builds Product   |
        +------------------+
                 |
                 v
           +-----------+
           |  Product  |
           +-----------+
```

Simple version (Java world):

```
Client
   |
   v
Builder  ----> Product
```

---

# 5. Components / Roles

### 1️⃣ Product

Final object jo build hoga.

Example:

```
User
Car
Computer
House
```

---

### 2️⃣ Builder

Object build karne ke steps define karta hai.

Example methods:

```
setName()
setEmail()
setPhone()
build()
```

---

### 3️⃣ Concrete Builder

Actual implementation of builder.

Example:

```
UserBuilder
CarBuilder
```

---

### 4️⃣ Director (Optional)

Director **building process control karta hai**.

Example:

```
CarDirector
BurgerDirector
```

Lekin Java mein mostly **director use nahi hota**.

---

# 6. Java Implementation

## Step 1 — Product Class

```java
public class User {

    private int id;
    private String name;
    private String email;
    private String phone;

    private User(UserBuilder builder) {
        this.id = builder.id;
        this.name = builder.name;
        this.email = builder.email;
        this.phone = builder.phone;
    }

    public static class UserBuilder {

        private int id;
        private String name;
        private String email;
        private String phone;

        public UserBuilder setId(int id) {
            this.id = id;
            return this;
        }

        public UserBuilder setName(String name) {
            this.name = name;
            return this;
        }

        public UserBuilder setEmail(String email) {
            this.email = email;
            return this;
        }

        public UserBuilder setPhone(String phone) {
            this.phone = phone;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}
```

---

## Step 2 — Usage

```java
User user = new User.UserBuilder()
                .setId(1)
                .setName("Danish")
                .setEmail("danish@gmail.com")
                .setPhone("9999999999")
                .build();
```

Readable + Clean.

---

# 7. Advantages

### 1️⃣ Improves Readability

```
.setName("Danish")
.setEmail("danish@gmail.com")
```

Clear and understandable.

---

### 2️⃣ Handles Optional Parameters

Koi field nahi chahiye to simply skip kar do.

---

### 3️⃣ Immutable Objects Support

Object fields **final** bana sakte ho.

---

### 4️⃣ Constructor Overloading Avoid

No constructor explosion.

---

### 5️⃣ Step-by-Step Construction

Complex objects easily build ho jate hain.

---

# 8. Disadvantages

### 1️⃣ More Code

Extra Builder class likhni padti hai.

---

### 2️⃣ Slight Complexity

Simple objects ke liye unnecessary ho sakta hai.

---

### 3️⃣ Boilerplate Code

Getters + Builder methods zyada ho jaate hain.

(Lombok solve karta hai)

---

# 9. Where Used in Java / Spring / Microservices

### 1️⃣ Java StringBuilder

```
StringBuilder sb = new StringBuilder()
                        .append("Hello ")
                        .append("World");
```

---

### 2️⃣ Lombok Builder

Very common in Spring Boot.

```
@Builder
@Data
public class User {

   private int id;
   private String name;
   private String email;

}
```

Usage:

```
User user = User.builder()
                .id(1)
                .name("Danish")
                .email("abc@gmail.com")
                .build();
```

---

### 3️⃣ Spring Security

```
HttpSecurity
    .authorizeRequests()
    .anyRequest()
    .authenticated()
```

Fluent API style = Builder pattern concept.

---

### 4️⃣ Microservices DTO Creation

Example:

```
OrderResponse.builder()
             .orderId(101)
             .status("SUCCESS")
             .amount(500)
             .build();
```

---

### 5️⃣ REST API Response Objects

```
ApiResponse.builder()
           .status("SUCCESS")
           .message("User created")
           .data(user)
           .build();
```

---

# 10. Common Interview Questions

### Q1 What problem does Builder Pattern solve?

It solves:

```
Telescoping Constructor Problem
```

Many constructors with different parameter combinations.

---

### Q2 Difference between Builder and Factory?

| Builder                   | Factory                          |
| ------------------------- | -------------------------------- |
| Object step-by-step build | Object creation decide karta hai |
| Complex objects           | Simple object creation           |
| Readable construction     | Encapsulated creation            |

---

### Q3 When should we use Builder?

Use when:

```
Object has many optional parameters
Object construction is complex
Need immutable objects
Need readable object creation
```

---

### Q4 Why Builder often used with Immutable classes?

Because:

```
Object build hone ke baad change nahi hota
```

Example:

```
String
LocalDate
DTO classes
```

---

### Q5 What is Telescoping Constructor Problem?

Example:

```
User(int id)
User(int id, String name)
User(int id, String name, String email)
User(int id, String name, String email, String phone)
```

Constructors **explode**.

Builder solves it.

---

# One Line Interview Definition

> **Builder Pattern is a creational design pattern used to construct complex objects step-by-step while keeping the construction process separate from the object representation.**

---

# Abstract Factory Design Pattern
I’ll explain **Abstract Factory Design Pattern** in a **clean interview-ready format** so you can easily convert it into **notes for interviews and teaching (future YouTube as well)**.
Since you prefer **structured learning**, this explanation will follow a **logical engineering flow**.

---

# Abstract Factory Design Pattern

## 1. Problem (Why Abstract Factory?)

Imagine you are building a **UI library** that must support **multiple operating systems**.

Example:

```
Application supports:

Windows
Mac
Linux
```

Each OS needs **different UI components**:

| Component | Windows         | Mac         |
| --------- | --------------- | ----------- |
| Button    | WindowsButton   | MacButton   |
| Checkbox  | WindowsCheckbox | MacCheckbox |

If we write code like this:

```java
if(os == "Windows"){
   button = new WindowsButton();
   checkbox = new WindowsCheckbox();
}
else if(os == "Mac"){
   button = new MacButton();
   checkbox = new MacCheckbox();
}
```

Problems:

• Tight coupling with concrete classes
• Difficult to add new OS (Linux)
• Code becomes messy
• Violates **Open Closed Principle**

We need a solution where:

✔ Client code does NOT depend on concrete classes
✔ Families of related objects are created together
✔ New families can be added easily

This leads to the **Abstract Factory Pattern**.

---

# 2. Intent

**Abstract Factory Pattern**

> Provides an interface for creating **families of related or dependent objects without specifying their concrete classes.**

Key idea:

```
Factory creates multiple related objects
but client does not know which implementation is used
```

Instead of creating objects directly:

```
new WindowsButton()
new MacButton()
```

Client asks:

```
factory.createButton()
factory.createCheckbox()
```

---

# 3. Real World Analogy

### Furniture Factory Example

Suppose you buy **complete furniture sets**.

Furniture styles:

```
Modern Style
Victorian Style
```

Each style contains:

```
Chair
Table
Sofa
```

Modern factory produces:

```
ModernChair
ModernTable
ModernSofa
```

Victorian factory produces:

```
VictorianChair
VictorianTable
VictorianSofa
```

Customer chooses **factory type**, not individual items.

```
FurnitureFactory factory = new ModernFurnitureFactory();
```

Then everything comes from the same family.

```
factory.createChair()
factory.createTable()
factory.createSofa()
```

This guarantees **compatible products**.

---

# 4. Structure Diagram

```
                AbstractFactory
                       |
       --------------------------------
       |                              |
ConcreteFactory1              ConcreteFactory2
 (WindowsFactory)              (MacFactory)

       |                              |
  -----------                    -----------
  |         |                    |         |
Button   Checkbox            Button    Checkbox
  |         |                    |         |
WinBtn   WinCheck            MacBtn    MacCheck
```

Flow:

```
Client
   |
   v
Abstract Factory
   |
Concrete Factory
   |
Creates Product Families
```

---

# 5. Components / Roles

### 1️⃣ Abstract Factory

Interface declaring creation methods.

Example:

```
createButton()
createCheckbox()
```

---

### 2️⃣ Concrete Factory

Implements Abstract Factory.

Examples:

```
WindowsFactory
MacFactory
```

Responsible for creating **specific product family**.

---

### 3️⃣ Abstract Product

Interfaces for product types.

Example:

```
Button
Checkbox
```

---

### 4️⃣ Concrete Products

Actual implementations.

Example:

```
WindowsButton
MacButton
WindowsCheckbox
MacCheckbox
```

---

### 5️⃣ Client

Uses **factory interface**, not concrete classes.

```
factory.createButton()
```

---

# 6. Java Implementation

## Step 1 — Abstract Products

```java
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}
```

---

## Step 2 — Concrete Products

### Windows Products

```java
class WindowsButton implements Button {

    public void paint() {
        System.out.println("Rendering Windows Button");
    }
}

class WindowsCheckbox implements Checkbox {

    public void paint() {
        System.out.println("Rendering Windows Checkbox");
    }
}
```

---

### Mac Products

```java
class MacButton implements Button {

    public void paint() {
        System.out.println("Rendering Mac Button");
    }
}

class MacCheckbox implements Checkbox {

    public void paint() {
        System.out.println("Rendering Mac Checkbox");
    }
}
```

---

## Step 3 — Abstract Factory

```java
interface GUIFactory {

    Button createButton();

    Checkbox createCheckbox();
}
```

---

## Step 4 — Concrete Factories

### Windows Factory

```java
class WindowsFactory implements GUIFactory {

    public Button createButton() {
        return new WindowsButton();
    }

    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}
```

---

### Mac Factory

```java
class MacFactory implements GUIFactory {

    public Button createButton() {
        return new MacButton();
    }

    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}
```

---

## Step 5 — Client

```java
class Application {

    private Button button;
    private Checkbox checkbox;

    public Application(GUIFactory factory) {

        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void paintUI() {

        button.paint();
        checkbox.paint();
    }
}
```

---

## Step 6 — Main Class

```java
public class Main {

    public static void main(String[] args) {

        GUIFactory factory;

        String os = "Windows";

        if(os.equals("Windows")) {
            factory = new WindowsFactory();
        }
        else {
            factory = new MacFactory();
        }

        Application app = new Application(factory);

        app.paintUI();
    }
}
```

---

# 7. Advantages

### 1️⃣ Ensures Product Compatibility

Products from same family work together.

Example:

```
WindowsButton + WindowsCheckbox
```

---

### 2️⃣ Loose Coupling

Client depends only on:

```
Interfaces
```

Not concrete classes.

---

### 3️⃣ Easy to Add New Families

Add new factory:

```
LinuxFactory
```

No client change required.

---

### 4️⃣ Follows SOLID Principles

Supports:

```
Open Closed Principle
Dependency Inversion Principle
```

---

# 8. Disadvantages

### 1️⃣ Large Number of Classes

Many classes required.

Example:

```
Products
Factories
Interfaces
```

---

### 2️⃣ Hard to Add New Product Type

If we add new product:

```
Slider
```

We must update:

```
All factories
```

---

### 3️⃣ Complexity

Overkill for simple systems.

---

# 9. Where Used in Java / Spring / Microservices

## Java Examples

### 1️⃣ JDBC

Driver factories.

Example:

```
DriverManager.getConnection()
```

Creates different DB drivers.

```
MySQL
PostgreSQL
Oracle
```

---

### 2️⃣ javax.xml.parsers

```
DocumentBuilderFactory
TransformerFactory
```

These create families of XML parsers.

---

## Spring Framework

### BeanFactory / ApplicationContext

Spring internally creates different types of beans.

Example:

```
XmlBeanFactory
AnnotationConfigApplicationContext
```

---

### Spring Data

Repository implementations created dynamically.

---

## Microservices

Used in:

### 1️⃣ Cloud Provider Factories

```
AWSServiceFactory
AzureServiceFactory
GCPServiceFactory
```

Each produces:

```
Storage
Queue
Compute
```

---

### 2️⃣ Messaging Systems

Factory for:

```
KafkaProducer
RabbitProducer
```

---

# 10. Interview Questions

### Q1 What is Abstract Factory Pattern?

Creates **families of related objects without specifying concrete classes**.

---

### Q2 Difference between Factory Method and Abstract Factory?

| Factory Method          | Abstract Factory               |
| ----------------------- | ------------------------------ |
| Creates single product  | Creates family of products     |
| Subclass decides object | Factory decides product family |
| Less complex            | More complex                   |

---

### Q3 When should we use Abstract Factory?

When:

```
System must work with multiple families of products
Products must be used together
Creation logic must be hidden
```

---

### Q4 What problem does it solve?

```
Object creation for related products
without tight coupling
```

---

### Q5 Does it follow SOLID?

Yes.

Supports:

```
Open Closed Principle
Dependency Inversion
```

---

# Simple Interview Summary

**Abstract Factory Pattern**

```
Purpose:
Create families of related objects without specifying concrete classes.

Example:
Windows UI factory
Mac UI factory

Key Benefit:
Ensures compatible product creation.
```
---
Good question 👍
Understanding the **complete flow of calling** is what interviewers usually expect after you explain the theory.

Below is the **full block diagram + call flow** using the **Windows / Mac GUI example**, and I will clearly mark **Interfaces vs Classes**.

---

# 1. Complete Abstract Factory Block Diagram

```
                        CLIENT
                     (Application)
                         |
                         |
                         v
                 ------------------
                 |  GUIFactory     |   << Interface >>
                 ------------------
                 | +createButton() |
                 | +createCheckbox()|
                 ------------------
                   /              \
                  /                \
                 v                  v
        -----------------      ----------------
        | WindowsFactory |      |  MacFactory  |
        |    <<Class>>   |      |   <<Class>>  |
        -----------------      ----------------
        | createButton() |      | createButton() |
        | createCheckbox()|     | createCheckbox()|
        ------------------      -------------------
            |        |              |        |
            |        |              |        |
            v        v              v        v

      ------------  ------------   ------------  ------------
      | Button   |  | Checkbox |   | Button   |  | Checkbox |
      |Interface |  |Interface |   |Interface |  |Interface |
      ------------  ------------   ------------  ------------

            |             |             |            |
            |             |             |            |
            v             v             v            v

   ----------------   ----------------   ----------------  ----------------
   |WindowsButton |   |WindowsCheckbox|   |MacButton     |  |MacCheckbox  |
   |   <<Class>>  |   |   <<Class>>   |   |   <<Class>>  |  |   <<Class>> |
   ----------------   ----------------   ----------------  ----------------
```

---

# 2. Interfaces vs Classes

### Interfaces

```
GUIFactory
Button
Checkbox
```

### Concrete Classes

```
WindowsFactory
MacFactory

WindowsButton
MacButton

WindowsCheckbox
MacCheckbox

Application (Client)
```

---

# 3. Call Flow (Step-by-Step)

Now let's see **how the calling actually happens**.

---

## Step 1 — Main decides Factory

Client decides which factory to use.

```java
GUIFactory factory;

if(os.equals("Windows")){
    factory = new WindowsFactory();
}
else{
    factory = new MacFactory();
}
```

So object becomes:

```
factory → WindowsFactory
```

---

## Step 2 — Factory passed to Client

```
Application app = new Application(factory);
```

Now:

```
Application
     |
     v
GUIFactory (interface reference)
     |
     v
WindowsFactory (actual object)
```

---

## Step 3 — Client asks Factory for Products

Inside **Application constructor**:

```java
button = factory.createButton();
checkbox = factory.createCheckbox();
```

Call flow becomes:

```
Application
    |
    | createButton()
    v
GUIFactory (interface)
    |
    v
WindowsFactory (implementation)
    |
    v
new WindowsButton()
```

---

# 4. Full Execution Flow Diagram

```
Main
 |
 | creates
 v
WindowsFactory
 |
 | passed to
 v
Application
 |
 |---- factory.createButton()
 |         |
 |         v
 |   WindowsFactory.createButton()
 |         |
 |         v
 |    new WindowsButton()
 |
 |
 |---- factory.createCheckbox()
           |
           v
    WindowsFactory.createCheckbox()
           |
           v
      new WindowsCheckbox()
```

So finally **Application gets**

```
Button → WindowsButton
Checkbox → WindowsCheckbox
```

---

# 5. Runtime Object Relationship

At runtime objects look like this:

```
Application
   |
   | uses
   v
GUIFactory reference
   |
   v
WindowsFactory object
   |
   | creates
   |
   |---- WindowsButton
   |
   |---- WindowsCheckbox
```

Client **never directly creates**:

```
new WindowsButton()
new MacButton()
```

Everything is created through **factory**.

---

# 6. Why This Pattern Is Powerful

Because client code becomes **independent of concrete classes**.

Client code:

```
factory.createButton()
```

instead of

```
new WindowsButton()
new MacButton()
```

So if tomorrow we add:

```
LinuxFactory
LinuxButton
LinuxCheckbox
```

We **only change factory selection**, not client logic.

---

# 7. Ultra-Short Interview Explanation (Very Important)

If interviewer asks:

**"Explain call flow of Abstract Factory."**

You can answer:

> Client selects a concrete factory (like WindowsFactory).
> The factory implements the AbstractFactory interface.
> Client calls factory methods like createButton() and createCheckbox().
> The concrete factory creates the corresponding concrete products (WindowsButton, WindowsCheckbox) and returns them as abstract product interfaces.

---
Great 👍
Let’s look at the **most important comparison** interviewers ask:

# Factory Method vs Abstract Factory (Visual + Concept)

Many candidates confuse these two patterns.
The **easiest way to understand** is through **structure diagrams**.

---

# 1. Factory Method Pattern (Structure)

Factory Method creates **ONE product**.

### Diagram

```id="1z8lra"
                 Creator (Abstract Class)
                -------------------------
                |  factoryMethod()      |
                -------------------------
                       /        \
                      /          \
                     v            v

         ------------------   ------------------
         | WindowsCreator  |   |  MacCreator    |
         |    <<Class>>    |   |    <<Class>>   |
         ------------------   ------------------
         | factoryMethod() |   | factoryMethod()|
         ------------------   ------------------
                 |                      |
                 | creates              | creates
                 v                      v

              ---------             ---------
              |Button |             |Button |
              |Interface|           |Interface|
              ---------             ---------
                 |                      |
                 v                      v
          ---------------        ---------------
          |WindowsButton |      | MacButton   |
          |   <<Class>>  |      |  <<Class>>  |
          ---------------        ---------------
```

### Key Point

Factory Method produces **one product type**.

Example:

```id="9nb0nb"
createButton()
```

---

# 2. Abstract Factory Pattern (Structure)

Abstract Factory creates **MULTIPLE RELATED PRODUCTS**.

### Diagram

```id="2t0c1s"
                       AbstractFactory
                        (Interface)
                    --------------------
                    | createButton()   |
                    | createCheckbox() |
                    --------------------
                      /              \
                     /                \
                    v                  v

           ------------------     ------------------
           | WindowsFactory  |     |   MacFactory   |
           |     <<Class>>   |     |    <<Class>>   |
           ------------------     ------------------
           |createButton()   |     |createButton()  |
           |createCheckbox() |     |createCheckbox()|
           ------------------     ------------------
               |        |             |        |
               v        v             v        v

        ----------  ----------   ----------  ----------
        | Button |  |Checkbox|   | Button |  |Checkbox|
        |Interface| |Interface|  |Interface| |Interface|
        ----------  ----------   ----------  ----------

            |            |            |           |
            v            v            v           v

     --------------  --------------  ------------  -------------
     |WindowsButton| |WindowsCheck | |MacButton | |MacCheckbox |
     |   <<Class>> | |  <<Class>>  | | <<Class>>| |  <<Class>> |
     --------------  --------------  ------------  -------------
```

---

# 3. Core Difference (Very Important)

| Feature    | Factory Method  | Abstract Factory                 |
| ---------- | --------------- | -------------------------------- |
| Creates    | One product     | Family of products               |
| Focus      | Object creation | Product families                 |
| Complexity | Simpler         | More complex                     |
| Example    | createButton()  | createButton(), createCheckbox() |

---

# 4. Real Example

### Factory Method

Restaurant example:

```id="2jo7pj"
createBurger()
```

Types:

```id="lq80rs"
VegBurger
ChickenBurger
```

Only **one product type**.

---

### Abstract Factory

Restaurant combo:

```id="p2n0qh"
createBurger()
createDrink()
createFries()
```

Family of related products.

Example combo:

```id="1r9m7y"
VegBurger
Coke
Fries
```

Another combo:

```id="9p72vo"
ChickenBurger
Pepsi
Fries
```

---

# 5. Interview Trick (Very Important)

Most interviewers ask:

**How are these two related?**

Correct answer:

> Abstract Factory is basically **a factory of factories** and internally it usually uses **Factory Method pattern**.

Example:

```id="p2jlhq"
WindowsFactory.createButton()
```

is actually a **Factory Method**.

---

# 6. Real Example in Spring

Spring internally behaves like **Abstract Factory**.

Example:

```id="efn6mx"
ApplicationContext
```

creates multiple related beans:

```id="w9i3k5"
Controllers
Services
Repositories
```

All belonging to same **application configuration family**.

---

# 7. One-Line Memory Trick

This trick helps in interviews.

```id="bn0lf3"
Factory Method → ONE product

Abstract Factory → FAMILY of products
```

---

# 8. Interview Level Example (Microservices)

Imagine multi-cloud support:

```id="ej72bg"
AWS
Azure
GCP
```

Abstract factory:

```id="pzrwgt"
CloudFactory
```

Methods:

```id="8m7p2h"
createStorage()
createQueue()
createCompute()
```

AWS implementation:

```id="9mvz3j"
S3
SQS
EC2
```

Azure implementation:

```id="1y03o1"
BlobStorage
ServiceBus
VM
```

---

# 9. One Diagram That Makes Everything Clear

```id="1ikvrx"
Factory Method

Client
  |
  v
Creator
  |
  v
createProduct()
  |
  v
ConcreteProduct



Abstract Factory

Client
  |
  v
AbstractFactory
  |
  v
createProductA()
createProductB()
  |
  v
ConcreteProductA
ConcreteProductB
```
---

# Prototype Design Pattern
I’ll explain **Prototype Design Pattern** in a **clean interview-ready format** so you can directly convert it into **notes for interviews and teaching later (YouTube as well)**.

Prototype is one of the **Creational Design Patterns** along with Singleton, Factory, Builder etc.

---

# Prototype Design Pattern

## 1. Problem

In many applications, **creating an object is expensive** because:

* Object creation requires **database calls**
* Object requires **complex initialization**
* Object contains **large nested structures**
* Object creation involves **network/API calls**

Example:

```text
Suppose we need to create 1000 objects of a complex configuration.
If every time we create a new object using `new`, it becomes slow.
```

Example scenario:

```
Employee emp1 = new Employee();
emp1.loadFromDatabase();

Employee emp2 = new Employee();
emp2.loadFromDatabase();

Employee emp3 = new Employee();
emp3.loadFromDatabase();
```

Here:

* DB call happens **3 times**
* Performance becomes **slow**

But if we could **clone the first object**, we avoid expensive initialization.

```
Employee emp2 = emp1.clone();
Employee emp3 = emp1.clone();
```

So instead of **creating object from scratch**, we **copy an existing object**.

This leads to the **Prototype Pattern**.

---

# 2. Intent

**Definition (Interview Ready)**

> Prototype Pattern creates new objects by **copying (cloning) an existing object**, instead of creating a new instance from scratch.

In short:

```
Create new objects by cloning existing objects.
```

Purpose:

* Improve performance
* Avoid expensive object creation
* Reduce repeated initialization

---

# 3. Real World Analogy

### Example: Photocopy Machine

Imagine you have a **document**.

If you want **10 copies**, you do NOT rewrite the document.

Instead you:

```
Original Document → Photocopy Machine → Multiple Copies
```

Mapping to Prototype Pattern:

| Real World        | Software         |
| ----------------- | ---------------- |
| Original Document | Prototype Object |
| Photocopy         | Clone            |
| Copies            | New Objects      |

So instead of creating objects again:

```
Original Object → Clone → New Objects
```

---

# 4. Structure Diagram

```
                +------------------+
                |   Prototype      |
                |------------------|
                | clone()          |
                +--------+---------+
                         |
                         |
                +--------v---------+
                | ConcretePrototype|
                |------------------|
                | fields           |
                | clone()          |
                +------------------+

                         |

                +------------------+
                |     Client       |
                +------------------+
```

Flow:

```
Client → asks Prototype → clone()
Prototype → returns copy
```

---

# 5. Components / Roles

### 1. Prototype

Interface or abstract class that defines:

```
clone()
```

Example:

```java
interface Prototype {
    Prototype clone();
}
```

---

### 2. Concrete Prototype

Actual object that implements cloning logic.

Example:

```
Employee
Car
Document
Configuration
```

---

### 3. Client

Client uses **clone()** to create objects instead of `new`.

---

# 6. Java Implementation

### Step 1 — Prototype Interface

```java
public interface Prototype {
    Prototype clone();
}
```

---

### Step 2 — Concrete Prototype

```java
public class Employee implements Prototype {

    private String name;
    private String department;

    public Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }

    @Override
    public Prototype clone() {
        return new Employee(this.name, this.department);
    }

    public void showDetails() {
        System.out.println("Name: " + name + ", Dept: " + department);
    }
}
```

---

### Step 3 — Client

```java
public class PrototypeDemo {

    public static void main(String[] args) {

        Employee emp1 = new Employee("Danish", "Backend");

        Employee emp2 = (Employee) emp1.clone();
        Employee emp3 = (Employee) emp1.clone();

        emp1.showDetails();
        emp2.showDetails();
        emp3.showDetails();
    }
}
```

Output:

```
Name: Danish, Dept: Backend
Name: Danish, Dept: Backend
Name: Danish, Dept: Backend
```

Here:

```
emp1 → original
emp2 → clone
emp3 → clone
```

---

# Shallow Copy vs Deep Copy (Very Important)

Interviewers often ask this.

### 1. Shallow Copy

Copies **primitive values** but **references remain same**.

Example:

```
Employee
   |
   |---- Address object
```

If cloned shallowly:

```
emp1.address == emp2.address
```

Both share same object.

---

### 2. Deep Copy

Entire object graph is copied.

```
emp1.address != emp2.address
```

Each clone has its own object.

---

Example Deep Copy:

```java
@Override
public Prototype clone() {
    Address newAddress = new Address(this.address.getCity());
    return new Employee(this.name, newAddress);
}
```

---

# 7. Advantages

### 1. Faster Object Creation

Cloning is **faster than new object creation**.

---

### 2. Avoids Expensive Initialization

Example:

```
DB Calls
File loading
Configuration loading
```

---

### 3. Reduces Subclass Explosion

You don't need multiple subclasses.

---

### 4. Dynamic Object Creation

Objects can be cloned at runtime.

---

### 5. Cleaner Code

No repeated initialization logic.

---

# 8. Disadvantages

### 1. Complex Cloning Logic

Deep copying can become complex.

---

### 2. Circular References

Cloning objects with circular references is difficult.

---

### 3. Hard to Maintain

When object structure changes, cloning logic must be updated.

---

### 4. Hidden Copying

Sometimes developers don't realize objects are being copied.

---

# 9. Where Used in Java / Spring / Microservices

## 1. Java `Cloneable`

Java provides built-in prototype support.

```
java.lang.Cloneable
```

Example:

```java
class Employee implements Cloneable {

    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

---

## 2. Spring Bean Scopes

Spring supports **prototype scope**.

```
@Scope("prototype")
```

Example:

```java
@Component
@Scope("prototype")
class UserService {
}
```

Meaning:

```
Every request → new object
```

Different from singleton.

---

## 3. Object Templates

Used when creating many similar objects.

Example:

```
Email templates
Configuration objects
DTO templates
```

---

## 4. Game Development

Games clone objects like:

```
Enemies
Bullets
Characters
```

---

## 5. Microservices

Useful for:

### Configuration Templates

```
Service Config
API Request Templates
Response Templates
```

Example:

```
BaseRequest → clone → modify fields
```

---

# 10. Interview Questions

### Q1 What is Prototype Design Pattern?

**Answer**

Prototype pattern creates new objects by **cloning existing objects instead of creating new instances**.

---

### Q2 Why use Prototype Pattern?

* Expensive object creation
* Performance optimization
* Avoid repeated initialization

---

### Q3 Difference between Factory and Prototype?

| Feature       | Factory           | Prototype       |
| ------------- | ----------------- | --------------- |
| Creation      | Uses constructors | Uses cloning    |
| Object Source | New object        | Existing object |
| Performance   | Slower            | Faster          |
| Flexibility   | Medium            | High            |

---

### Q4 What is Shallow Copy?

Copies primitive values but **reference objects remain shared**.

---

### Q5 What is Deep Copy?

Entire object graph is copied so objects become **independent**.

---

### Q6 Does Java support Prototype Pattern?

Yes via:

```
Cloneable
Object.clone()
```

---

### Q7 When should we avoid Prototype Pattern?

* When objects are simple
* When cloning logic becomes complex
* When deep copy is required frequently

---

# Final Summary (Best Notes Format)

```
Prototype Pattern

Category:
Creational Design Pattern

Definition:
Create new objects by cloning existing objects.

Problem:
Object creation is expensive.

Solution:
Clone existing objects instead of creating new ones.

Key Method:
clone()

Key Benefit:
Improves performance.

Key Concept:
Shallow Copy vs Deep Copy.

Common Usage:
Java Cloneable
Spring prototype beans
Configuration templates
Game development
```

---

If you want, I can also explain **2 very important interview things next**:

1️⃣ **Prototype Registry Pattern (used in real projects)**
2️⃣ **Deep Copy Implementation in Java (best practice for interviews)**

These two questions are **very frequently asked in senior Java interviews**.
Great question — and honestly, this is where most candidates (and even devs) get confused. Let’s clear it **properly + interview-ready clarity** 👇

---

# 🔴 1. Does Spring use Prototype Pattern internally?

👉 **Short Answer: NO (not exactly cloning)**

What you said:

> “Spring prototype scope me object clone karta hai?”

❌ **Wrong assumption**

✔️ **Reality:**
When you use:

```java
@Scope("prototype")
```

Spring does **NOT clone an existing object**.

Instead:

```text
Every time → Spring calls constructor → creates a NEW object
```

So internally:

```text
prototype scope ≠ clone()
prototype scope = new object creation every time
```

---

## 💡 Why confusion happens?

Because:

* Name = "prototype"
* Pattern = "Prototype Design Pattern"

👉 But **both are different concepts**

| Spring Prototype Scope             | Prototype Design Pattern   |
| ---------------------------------- | -------------------------- |
| Creates new object via constructor | Creates object via cloning |
| Managed by Spring container        | Manual design pattern      |
| No `clone()` involved              | `clone()` is core          |

---

## 🎯 Interview Line (IMPORTANT)

> “Spring prototype scope does not use Prototype Design Pattern internally. It simply creates a new instance every time using constructor, not cloning.”

---

# 🔴 2. Then where is Prototype Pattern used in REAL projects?

Now this is the **actual developer POV** 🔥

You’re right:

> “Maine project me clone() use nahi kiya”

✔️ That’s normal.

👉 Because in **enterprise apps**, we rarely use `clone()` directly.

But the **concept of prototype IS used indirectly**.

---

# 🔥 REAL Developer Use Cases (VERY IMPORTANT)

## ✅ 1. Request/Response Template Copy

### Scenario (Microservices / APIs)

You have a **base request object**:

```java
BaseRequest request = new BaseRequest();
request.setHeaders(commonHeaders);
request.setTimeout(5000);
```

Now for multiple APIs:

```java
BaseRequest req1 = request.copy();
req1.setEndpoint("/user");

BaseRequest req2 = request.copy();
req2.setEndpoint("/order");
```

👉 Instead of creating object again:

```text
Reuse base object → copy → modify
```

✔️ This is **Prototype Pattern concept**

---

## ✅ 2. Configuration Objects (VERY COMMON)

### Scenario

You load config once:

```java
Config config = loadFromFile(); // expensive
```

Now instead of reloading:

```java
Config config1 = config.copy();
Config config2 = config.copy();
```

👉 Used in:

* Microservices configs
* Kafka producer configs
* HTTP client configs

---

## ✅ 3. DTO Copying (VERY PRACTICAL)

### Scenario

```java
UserDTO base = new UserDTO();
base.setRole("USER");
base.setActive(true);
```

Now:

```java
UserDTO u1 = base.copy();
u1.setName("Danish");

UserDTO u2 = base.copy();
u2.setName("Ali");
```

✔️ Instead of repeating common fields

---

## ✅ 4. Object Mapping (Hidden Prototype Usage)

In real projects we use:

* ModelMapper
* MapStruct

These internally:

```text
Take existing object → copy values → create new object
```

👉 This is **prototype concept without clone()**

---

## ✅ 5. Caching Systems

### Scenario

```java
User user = cache.get("user1");
```

Instead of returning same object:

```java
return user.copy();
```

👉 Why?

To avoid:

```text
Shared object mutation bug
```

---

## ✅ 6. Game Dev / Simulation (classic but real)

* Clone enemies
* Clone bullets
* Clone templates

---

# 🔥 Why you NEVER used clone() directly?

Because:

## ❌ Problems with Java `clone()`

1. Confusing API
2. Shallow vs deep copy issues
3. Requires `Cloneable`
4. Not clean design

👉 Even Joshua Bloch said:

> Avoid using clone()

---

## ✔️ What developers use instead

Instead of `clone()`:

### 1. Copy Constructor

```java
public User(User other) {
    this.name = other.name;
    this.role = other.role;
}
```

---

### 2. Builder Pattern Copy

```java
User user2 = User.builder()
    .from(user1)
    .name("Danish")
    .build();
```

---

### 3. Utility Copy

```java
BeanUtils.copyProperties(source, target);
```

(Spring)

---

# 🎯 Final Clarity (VERY IMPORTANT)

## 💥 Key Takeaway

```text
Prototype Pattern ≠ clone() method

Prototype Pattern = concept of copying existing object
```

---

## 💥 In Real Projects

You are already using it indirectly via:

* DTO copying
* Mapping frameworks
* Config reuse
* Builder patterns

---

## 🎯 Interview Answer (Golden Line)

> “In real-world applications, we rarely use Java’s clone() directly. Instead, we implement the Prototype concept using copy constructors, builders, or mapping frameworks to duplicate objects efficiently.”

---

# Adapter Design Pattern
Great — Adapter is a **very important interview pattern** (especially in integration & microservices). I’ll explain it in a **clean, notes-ready + interview-focused** format 👇

---

# 🔷 Adapter Design Pattern

---

## 1. 🔴 Problem

👉 You have an **existing class (legacy / third-party)**
👉 But its **interface is incompatible** with what your system expects

### Example Problem:

```java
// Existing class (incompatible)
class OldPaymentGateway {
    void makePaymentInUSD(double amount) {
        System.out.println("Paid " + amount + " USD");
    }
}

// Your system expects:
interface PaymentProcessor {
    void pay(double amount);
}
```

❌ Problem:

* You **cannot modify OldPaymentGateway**
* But your system expects `PaymentProcessor`

👉 So how do you connect them?

---

## 2. 🎯 Intent

> Convert the interface of a class into another interface that clients expect.

✔ Acts as a **bridge between incompatible interfaces**
✔ Allows reuse of existing/legacy code
✔ Avoids modifying original class

---

## 3. 🌍 Real World Analogy

### 🔌 Mobile Charger Adapter

![Image](https://m.media-amazon.com/images/I/71GCNWfA6pL.jpg)

![Image](https://m.media-amazon.com/images/I/611RCy1XjsL.jpg)

![Image](https://blog.rishabhrkaushik.com/content/images/2024/07/image_2024-07-28_133922890.png)

![Image](https://images.bhaskarassets.com/web2images/1884/2025/12/22/slide2_1766386994.jpg)

👉 You have:

* Indian plug (existing system)
* US socket (expected interface)

👉 You use:

* Adapter (converter)

✔ Plug doesn’t change
✔ Socket doesn’t change
✔ Adapter makes them compatible

---

## 4. 🧩 Structure Diagram

```
Client → Target Interface → Adapter → Adaptee (Existing Class)
```

### Flow:

```
Client → pay() → Adapter → makePaymentInUSD()
```

---

## 5. 🧱 Components / Roles

| Component   | Description                  |
| ----------- | ---------------------------- |
| **Target**  | Interface expected by client |
| **Adaptee** | Existing incompatible class  |
| **Adapter** | Converts Target → Adaptee    |
| **Client**  | Uses Target interface        |

---

## 6. 💻 Java Implementation

### Step 1: Target Interface

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

---

### Step 2: Adaptee (Existing Class)

```java
class OldPaymentGateway {
    void makePaymentInUSD(double amount) {
        System.out.println("Paid " + amount + " USD via old gateway");
    }
}
```

---

### Step 3: Adapter Class

```java
class PaymentAdapter implements PaymentProcessor {

    private OldPaymentGateway oldGateway;

    public PaymentAdapter(OldPaymentGateway oldGateway) {
        this.oldGateway = oldGateway;
    }

    @Override
    public void pay(double amount) {
        // Convert INR → USD (example logic)
        double usd = amount / 80;
        oldGateway.makePaymentInUSD(usd);
    }
}
```

---

### Step 4: Client Code

```java
public class Main {
    public static void main(String[] args) {
        OldPaymentGateway oldGateway = new OldPaymentGateway();

        PaymentProcessor processor = new PaymentAdapter(oldGateway);
        processor.pay(8000); // INR
    }
}
```

---

## 🔄 Flow Summary

```
Client → pay(8000 INR)
       → Adapter converts to USD
       → OldPaymentGateway executes
```

---

## 7. ✅ Advantages

✔ Reuse legacy / third-party code
✔ No need to modify existing class
✔ Follows **Open/Closed Principle**
✔ Clean separation of concerns
✔ Improves maintainability

---

## 8. ❌ Disadvantages

❌ Adds extra layer (complexity)
❌ Too many adapters → messy code
❌ Performance overhead (minor)
❌ Harder debugging sometimes

---

## 9. 🚀 Where Used (Java / Spring / Microservices)

### 🔹 Java

* `java.util.Arrays.asList()` → array → List adapter
* `InputStreamReader` → byte stream → character stream
* `Collections.unmodifiableList()`

---

### 🔹 Spring Framework

* **Spring MVC**

  * Controllers adapt HTTP request → Java methods

* **Spring Security**

  * Adapts different authentication providers

---

### 🔹 Microservices (VERY IMPORTANT 🔥)

👉 Real Developer Use Cases:

### 1. Third-party API Integration

```text
Your Service → Adapter → Stripe / Razorpay API
```

✔ Convert your request → third-party format
✔ Convert response → internal model

---

### 2. Legacy System Integration

```text
New Microservice → Adapter → Old Monolith API
```

---

### 3. Different Data Formats

```text
JSON ↔ XML ↔ Protobuf Adapter
```

---

### 4. Kafka / Messaging

```text
Event → Adapter → Domain Object
```

---

## 🔥 Developer-Level Real Use Case (IMPORTANT)

👉 Suppose:

* Your system uses `UserDTO`
* Third-party API returns `UserResponse`

```java
class UserAdapter {
    public static UserDTO convert(UserResponse response) {
        UserDTO dto = new UserDTO();
        dto.setName(response.fullName);
        dto.setEmail(response.emailAddress);
        return dto;
    }
}
```

✔ This is Adapter in real projects
✔ Very common in microservices

---

## 10. 🎯 Interview Questions

### ⭐ Basic

1. What is Adapter Pattern?
2. When do you use it?
3. Difference between Adapter & Decorator?

---

### ⭐ Intermediate

4. What are types of Adapter?

   * Object Adapter (composition) ✅
   * Class Adapter (inheritance)

5. Does Adapter violate SOLID?
   👉 No, it supports Open/Closed Principle

---

### ⭐ Advanced (🔥 Important)

6. Real-time use case in your project?
   👉 Answer:

* Third-party API integration
* DTO conversion
* Legacy system integration

---

7. Adapter vs Facade?
   | Adapter | Facade |
   |--------|--------|
   | Converts interface | Simplifies interface |
   | Compatibility | Usability |

---

8. Can Adapter be bi-directional?
   👉 Yes, but rarely used

---

# 🧠 Final One-Line Summary

> Adapter Pattern = **"Compatibility layer between two incompatible systems"**

---

## 🔥 Pro Tip (Interview)

When asked:

👉 Don’t say only “charger example”
👉 Say:

> "In my project, we used Adapter to integrate third-party APIs and convert their response into our internal DTOs."

---
# Decorator Design Pattern
Great—Decorator is one of the **most important patterns** for interviews (especially Spring & microservices). I’ll explain it in a **clean, notes-ready + developer mindset** way.

---

# 🔶 1. Problem

Suppose you have a base object:

👉 `Coffee`

Now requirements keep growing:

* Add Milk
* Add Sugar
* Add Whipped Cream
* Add Chocolate

❌ Without Decorator:

* You will create multiple subclasses:

  * `MilkCoffee`
  * `SugarCoffee`
  * `MilkSugarCoffee`
  * `MilkSugarChocolateCoffee`

👉 This leads to **class explosion** ❌

---

# 🔶 2. Intent

👉 **Decorator Pattern allows you to add behavior dynamically at runtime without modifying existing code.**

💡 Key Idea:

> Wrap the original object with another object that adds extra functionality.

👉 Instead of inheritance → use **composition + wrapping**

---

# 🔶 3. Real World Analogy ☕

![Image](https://people.csail.mit.edu/zoya/IndividualPreds/0561/plot_individual_user_maps_05.png)

![Image](https://assets3.thrillist.com/v1/image/962353/414x310/crop%3Bwebp%3Dauto%3Bjpeg_quality%3D60%3Bprogressive.jpg)

![Image](https://support.epson.net/fun/assets/article84/images/i1_02.jpg)

![Image](https://m.media-amazon.com/images/I/61Obax03vZL._AC_UF350%2C350_QL80_.jpg)

Think of:

👉 **Coffee Order**

* Base: Espresso
* Add Milk
* Add Sugar
* Add Chocolate

Each topping **wraps over previous one**

💡 Final object = Layered combination

---

# 🔶 4. Structure Diagram

```
        Component (Interface)
                ↑
      ----------------------
      |                    |
ConcreteComponent     Decorator (Abstract)
                          ↑
                 -------------------
                 |        |        |
         MilkDecorator SugarDecorator ChocolateDecorator
```

💡 Flow:

```
Coffee coffee =
    new MilkDecorator(
        new SugarDecorator(
            new BasicCoffee()
        )
    );
```

---

# 🔶 5. Components / Roles

### 1. Component (Interface)

👉 Common interface

```java
interface Coffee {
    String getDescription();
    double cost();
}
```

---

### 2. Concrete Component

👉 Base object

```java
class BasicCoffee implements Coffee {
    public String getDescription() {
        return "Basic Coffee";
    }

    public double cost() {
        return 50;
    }
}
```

---

### 3. Decorator (Abstract Class)

👉 Wraps component

```java
abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;

    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }
}
```

---

### 4. Concrete Decorators

👉 Add new behavior

```java
class MilkDecorator extends CoffeeDecorator {

    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }

    public double cost() {
        return coffee.cost() + 20;
    }
}
```

---

```java
class SugarDecorator extends CoffeeDecorator {

    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    public String getDescription() {
        return coffee.getDescription() + ", Sugar";
    }

    public double cost() {
        return coffee.cost() + 10;
    }
}
```

---

# 🔶 6. Java Implementation (Full Flow)

```java
public class Main {
    public static void main(String[] args) {

        Coffee coffee = new BasicCoffee();

        coffee = new MilkDecorator(coffee);
        coffee = new SugarDecorator(coffee);

        System.out.println(coffee.getDescription());
        System.out.println(coffee.cost());
    }
}
```

### ✅ Output:

```
Basic Coffee, Milk, Sugar
80
```

---

# 🔶 7. Advantages

✅ **Open/Closed Principle**

* Extend behavior without modifying existing code

✅ **No class explosion**

* Avoids too many subclasses

✅ **Runtime flexibility**

* Add/remove features dynamically

✅ **Single Responsibility**

* Each decorator = one responsibility

---

# 🔶 8. Disadvantages

❌ Too many small classes
❌ Debugging becomes tricky (nested wrapping)
❌ Order of decorators matters (Milk → Sugar ≠ Sugar → Milk sometimes)

---

# 🔶 9. Where used in Java / Spring / Microservices

## 🔹 Java

👉 Most famous real example:

### 🔸 `java.io` package

```java
InputStream is = new FileInputStream("file.txt");
BufferedInputStream bis = new BufferedInputStream(is);
```

👉 Here:

* `FileInputStream` = base
* `BufferedInputStream` = decorator

---

## 🔹 Spring Framework

### 🔸 AOP (Aspect-Oriented Programming)

👉 Example:

* Logging
* Security
* Transactions

💡 Spring wraps your object with proxy

```
Original Service → Proxy (Decorator)
```

---

## 🔹 Microservices (Real Developer Use Case 🔥)

### 1. Logging Wrapper

* Add logs without touching business code

### 2. Security Layer

* JWT validation wrapper

### 3. Retry Mechanism

* Wrap API call with retry logic

### 4. Circuit Breaker (Resilience4j)

* Decorates service calls

---

# 🔶 10. Interview Questions (Must Prepare)

### ⭐ Basic

**Q1. What problem does Decorator solve?**
👉 Avoid subclass explosion + dynamic behavior addition

---

**Q2. Decorator vs Inheritance?**

| Decorator   | Inheritance  |
| ----------- | ------------ |
| Runtime     | Compile-time |
| Flexible    | Rigid        |
| Composition | IS-A         |

---

**Q3. Decorator vs Proxy?**

| Decorator          | Proxy           |
| ------------------ | --------------- |
| Adds behavior      | Controls access |
| Focus: enhancement | Focus: control  |

---

### ⭐ Medium

**Q4. Why composition over inheritance?**
👉 Loose coupling + flexibility

---

**Q5. Can decorator change behavior completely?**
👉 Yes, but usually extends behavior

---

### ⭐ Advanced (VERY IMPORTANT)

**Q6. How Spring AOP uses Decorator?**
👉 Proxy wraps original bean → adds behavior (logging, tx, security)

---

**Q7. Real project use case?**

👉 Example:

```
OrderService
   ↓
LoggingDecorator
   ↓
RetryDecorator
   ↓
CircuitBreakerDecorator
```

---

# 🔥 Final Interview One-Liner

> **Decorator Pattern dynamically adds behavior to objects using wrapping instead of subclassing.**

---
Short answer: **Yes — but not always directly.**
You *do use the Decorator pattern in real projects*, but mostly **implicitly via frameworks (like Spring)** rather than writing “Coffee-style” decorators yourself.

Let’s break this properly 👇

---

# 🔶 1. Are we really using Decorator in real projects?

## ✅ YES — but in 2 ways:

### 🔹 1. Indirectly (MOST COMMON ✅)

You use it via frameworks:

* Spring AOP
* Spring Security
* Logging frameworks
* Resilience libraries (Retry, Circuit Breaker)

👉 You are using Decorator **without realizing it**

---

### 🔹 2. Directly (RARE but IMPORTANT 🔥)

You write Decorator manually when:

* You want **dynamic behavior stacking**
* You want to avoid **if-else / flags**
* You want **clean extension without modifying code**

---

## 🔥 Real Developer Example

### ❌ Without Decorator

```java
if (loggingEnabled) log();
if (retryEnabled) retry();
if (securityEnabled) validate();
```

👉 Messy + tightly coupled ❌

---

### ✅ With Decorator

```java
Service service = new CoreService();

service = new LoggingDecorator(service);
service = new RetryDecorator(service);
service = new SecurityDecorator(service);
```

👉 Clean + scalable + interview gold 🔥

---

# 🔶 2. Is Spring AOP using Decorator?

## 👉 YES — Conceptually 100% YES

But technically it uses **Proxy + Decorator combination**

Let’s understand clearly 👇

---

## 🔹 What happens in Spring AOP?

When you write:

```java
@Transactional
public void placeOrder() {}
```

Spring does NOT modify your class.

Instead:

👉 It creates a **proxy object** around your original bean

---

## 🔹 Flow

```text
Client
   ↓
Proxy (adds transaction/logging/security)
   ↓
Original Bean (your business logic)
```

---

## 🔹 Why this is Decorator?

Because:

👉 Proxy is **wrapping your object and adding behavior**

That is EXACTLY:

> ✔ Decorator Pattern (wrapping + behavior addition)

---

## 🔹 Why also Proxy?

Because:

👉 It also controls access (before/after execution)

So:

| Pattern   | Role            |
| --------- | --------------- |
| Decorator | Adds behavior   |
| Proxy     | Controls access |

👉 Spring AOP = **Hybrid (Decorator + Proxy)**

---

# 🔶 3. Real Example in Spring

### Code

```java
@Service
public class OrderService {
    public void placeOrder() {
        System.out.println("Order placed");
    }
}
```

---

### With AOP

```java
@Aspect
@Component
class LoggingAspect {

    @Before("execution(* OrderService.placeOrder(..))")
    public void log() {
        System.out.println("Logging...");
    }
}
```

---

## 🔥 Actual Runtime Flow

```text
Client calls → Proxy
            → LoggingAspect (before)
            → OrderService.placeOrder()
            → LoggingAspect (after)
```

👉 Your object is **wrapped dynamically**

---

# 🔶 4. Where YOU will use it in real projects

## 🔹 1. API Client Wrapping

```text
Base API Call
   ↓
RetryDecorator
   ↓
LoggingDecorator
   ↓
CircuitBreakerDecorator
```

👉 Used in:

* Payment systems
* External API calls
* Microservices communication

---

## 🔹 2. Input Validation Layers

* Add validation without touching core logic

---

## 🔹 3. Feature Toggles

* Add/remove features dynamically

---

## 🔹 4. Caching Layer

* Wrap service with caching behavior

---

# 🔥 5. Interview Truth (VERY IMPORTANT)

If interviewer asks:

👉 **"Do you use Decorator in real projects?"**

### ✅ Best Answer:

> Yes, mostly indirectly through frameworks like Spring AOP, where proxies wrap the original bean and add behavior like logging, transactions, and security. In some cases, we also implement it directly for API wrappers like retry, logging, or circuit breaker patterns.

---

# 🔥 Final Clarity

👉 You are already using Decorator if you use:

* `@Transactional`
* `@Retry`
* `@Cacheable`
* Logging interceptors

💡 You just don’t see it — because Spring hides it.

---
# Facade Design Pattern
Great choice Danish — **Facade Design Pattern** is *very practical* and frequently used in real projects (especially in microservices & Spring). Let’s break it down in a **clean, interview-ready + notes-friendly format** 👇

---

# ✅ Facade Design Pattern

---

# 1. 🧩 Problem

In real systems, we often deal with **complex subsystems**.

👉 Example:

* Payment system involves:

  * Bank API
  * Fraud check
  * Notification service
  * Ledger update

Now client code has to:

```java
bankService.debit();
fraudService.check();
ledgerService.update();
notificationService.send();
```

❌ Problems:

* Too many dependencies
* Tight coupling with internal system
* Hard to maintain / understand
* Client needs to know *internal flow*

---

# 2. 🎯 Intent

👉 **Provide a simplified interface to a complex subsystem**

✔ Hide internal complexity
✔ Expose only what client needs
✔ Reduce coupling

📌 One-line definition:

> Facade acts as a **single entry point** to a complex system.

---

# 3. 🌍 Real World Analogy

### 🏦 Bank Counter

Instead of going to:

* cashier
* loan officer
* account manager

👉 You go to a **single counter (Facade)**

Clerk internally:

* checks balance
* processes request
* updates system

✔ You don’t deal with internal complexity

---

# 4. 🧱 Structure Diagram (Conceptual)

```
Client
   |
   v
Facade  ------------------------
   |                           |
   v                           v
Subsystem A     Subsystem B     Subsystem C
```

👉 Client talks ONLY to Facade
👉 Facade coordinates subsystems

---

# 5. 🧑‍💻 Components / Roles

### 1. Facade

* Main entry point
* Simplifies interaction
* Calls multiple subsystems

### 2. Subsystems

* Actual business logic
* Independent components
* Not aware of facade

### 3. Client

* Uses facade only
* No direct interaction with subsystems

---

# 6. ☕ Java Implementation

### 🎯 Use Case: Order Processing System

---

## Step 1: Subsystems

```java
class PaymentService {
    public void processPayment() {
        System.out.println("Payment processed");
    }
}

class InventoryService {
    public void updateStock() {
        System.out.println("Stock updated");
    }
}

class NotificationService {
    public void sendNotification() {
        System.out.println("Notification sent");
    }
}
```

---

## Step 2: Facade

```java
class OrderFacade {

    private PaymentService paymentService;
    private InventoryService inventoryService;
    private NotificationService notificationService;

    public OrderFacade() {
        this.paymentService = new PaymentService();
        this.inventoryService = new InventoryService();
        this.notificationService = new NotificationService();
    }

    public void placeOrder() {
        paymentService.processPayment();
        inventoryService.updateStock();
        notificationService.sendNotification();
    }
}
```

---

## Step 3: Client

```java
public class Main {
    public static void main(String[] args) {
        OrderFacade orderFacade = new OrderFacade();
        orderFacade.placeOrder();
    }
}
```

---

✅ Client doesn’t know internal flow
✅ Clean & simple usage

---

# 7. ✅ Advantages

✔ **Simplifies complex systems**
✔ **Reduces coupling** (client ↔ subsystem)
✔ Improves **readability & maintainability**
✔ Better **layered architecture**
✔ Easy to modify internal logic

---

# 8. ❌ Disadvantages

❌ Facade can become **God class** if overloaded
❌ Might hide useful functionality
❌ Extra abstraction layer (sometimes unnecessary)

---

# 9. 🚀 Where Used in Java / Spring / Microservices

This is where things get **real-world important** 👇

---

## 🔹 1. Spring Boot Service Layer

```java
@RestController
class OrderController {

    @Autowired
    private OrderFacade orderFacade;

    @PostMapping("/order")
    public String placeOrder() {
        orderFacade.placeOrder();
        return "Order placed";
    }
}
```

👉 `OrderFacade` acts as:

* Orchestrator
* Single entry point

---

## 🔹 2. Microservices Aggregator Pattern

👉 API Gateway / Aggregator service acts as Facade

Example:

* `/user-dashboard` API

  * calls:

    * user-service
    * order-service
    * payment-service

✔ Client sees ONE API
✔ Internally multiple services

---

## 🔹 3. Java Libraries

### Example:

* `java.util.logging.Logger`
* `javax.faces.context.FacesContext`

👉 They simplify complex internal APIs

---

## 🔹 4. JDBC (Simplified DB Access)

👉 Instead of raw DB calls:

* `JdbcTemplate` (Spring)

Acts like a **Facade over JDBC complexity**

---

## 🔹 5. Third-party SDKs

Example:

* AWS SDK
* Payment gateways (Razorpay, Stripe)

👉 Provide simple APIs hiding complex workflows

---

# 🔥 Real Developer Insight (Important for YOU)

👉 In real projects:

* You may not explicitly name it "Facade"
* But you **DO implement it**

### Common pattern you already use:

```java
OrderService.placeOrder()
```

Inside:

* DB calls
* Kafka event
* Notification
* Validation

👉 That service = **Facade**

---

# 10. 🎤 Interview Questions

---

### ❓ Q1: What is Facade Pattern?

👉 Simplifies complex subsystems by providing a unified interface.

---

### ❓ Q2: Difference between Facade and Adapter?

| Facade             | Adapter                |
| ------------------ | ---------------------- |
| Simplifies system  | Converts interface     |
| New interface      | Existing interface     |
| Focus on usability | Focus on compatibility |

---

### ❓ Q3: Is Facade tightly coupled?

👉 No
It reduces coupling between client and subsystems.

---

### ❓ Q4: Can subsystems be used directly?

👉 Yes
Facade is optional layer (not mandatory)

---

### ❓ Q5: Facade vs Service Layer?

👉 Often same in real projects
Service layer acts as Facade

---

### ❓ Q6: Real-time example?

👉 API Gateway in microservices
👉 Spring Service class

---

# 🧠 Final Summary (Quick Revision)

* Facade = **Simplifier**
* Hides complexity
* Provides **single entry point**
* Used heavily in:

  * Spring Boot services
  * Microservices aggregation
  * SDKs

---

# 🔥 One-line Memory Trick

👉 **"Facade = Front Door of System"**

---
Good observation Danish 👀 — **but this is where interviews trap people.**

👉 **Facade ≠ Bridge**

They may *look similar superficially*, but their **intent and purpose are completely different**.

Let’s break it cleanly so you never confuse them again 👇

---

# 🔥 Core Difference (1-line)

* **Facade → hides complexity (simplification)**
* **Bridge → separates abstraction from implementation (flexibility)**

---

# 🧠 Why you felt they look similar?

Because both:

* Hide internal details
* Use composition
* Reduce direct dependency

👉 BUT reason (intent) is different.

---

# ⚔️ Facade vs Bridge (Interview Table)

| Feature       | Facade                  | Bridge                                         |
| ------------- | ----------------------- | ---------------------------------------------- |
| Purpose       | Simplify system         | Decouple abstraction & implementation          |
| Focus         | Usability               | Flexibility & extensibility                    |
| Client sees   | Simple interface        | Abstract hierarchy                             |
| Subsystems    | Hidden                  | Exposed via interface                          |
| Change impact | Internal changes hidden | Both abstraction & impl can vary independently |

---

# 🏗️ Conceptual Difference

## 🔹 Facade Thinking

👉 “System bahut complex hai, ek simple entry bana do”

```text
Client → Facade → (Multiple Systems)
```

✔ Hides complexity
✔ One entry point

---

## 🔹 Bridge Thinking

👉 “Abstraction aur implementation alag karo, taaki dono independently change ho sake”

```text
Abstraction → Implementation Interface → Concrete Implementations
```

✔ Flexible design
✔ Runtime swapping possible

---

# ☕ Code-Level Difference

---

## ✅ Facade Example (what we saw)

```java
class OrderFacade {
    public void placeOrder() {
        payment.process();
        inventory.update();
        notification.send();
    }
}
```

👉 Client calls **one method**
👉 Doesn’t know anything inside

---

## ✅ Bridge Example

```java
// Implementation
interface PaymentProcessor {
    void process();
}

class UpiPayment implements PaymentProcessor {
    public void process() {
        System.out.println("UPI Payment");
    }
}

class CardPayment implements PaymentProcessor {
    public void process() {
        System.out.println("Card Payment");
    }
}

// Abstraction
abstract class Payment {
    protected PaymentProcessor processor;

    public Payment(PaymentProcessor processor) {
        this.processor = processor;
    }

    abstract void makePayment();
}

// Refined abstraction
class OnlinePayment extends Payment {
    public OnlinePayment(PaymentProcessor processor) {
        super(processor);
    }

    public void makePayment() {
        processor.process();
    }
}
```

👉 You can mix:

```java
new OnlinePayment(new UpiPayment());
new OnlinePayment(new CardPayment());
```

✔ Runtime flexibility
✔ Independent variation

---

# 🎯 Real Project Understanding (VERY IMPORTANT)

---

## 🔥 Facade in your project

```java
OrderService.placeOrder()
```

Inside:

* DB call
* Kafka event
* Email service

👉 ✔ You are hiding complexity
👉 ✔ This is **Facade**

---

## 🔥 Bridge in real systems

Used when:

* Multiple abstractions + multiple implementations

Example:

* Notification system:

  * Abstraction: Notification (Email/SMS)
  * Implementation: Provider (Twilio, AWS SNS)

👉 You can mix:

* SMS via Twilio
* SMS via AWS
* Email via SendGrid

✔ This is **Bridge**

---

# 🚨 Interview Trap Question

👉 *“Facade hides implementation, so is it same as Bridge?”*

✅ Correct Answer:

> No.
> Facade hides complexity and provides a simplified interface,
> while Bridge separates abstraction and implementation so both can evolve independently.

---

# 🧠 Memory Trick

👉 **Facade = Simplifier (Entry Gate)**
👉 **Bridge = Connector (Flexibility Layer)**

---

# 💥 One Powerful Line (Use in Interview)

> "Facade is about making things easy for the client, while Bridge is about making the system flexible for developers."

---
# Proxy Design Pattern
Great choice Danish 👍 — **Proxy Design Pattern** is *very important* for interviews (especially for Spring + Microservices). I’ll explain it in a **clean, notes-friendly + developer-focused way**.

---

# 🔷 Proxy Design Pattern

---

## 1. Problem

Sometimes you **don’t want direct access to an object**, because:

* Object is **heavy (expensive to create)**
* Needs **security / access control**
* Needs **logging / monitoring**
* Object is **remote (API / microservice)**
* Want **lazy loading**

👉 Direct access causes:

* Performance issues
* Security risks
* Tight coupling

---

## 2. Intent

👉 Provide a **surrogate / placeholder object (Proxy)**
that **controls access to the real object**

✔ Proxy acts as a **middle layer**

👉 Client → Proxy → Real Object

---

## 3. Real World Analogy

### 🏦 Bank Locker System

![Image](https://www.hindustantimes.com/ht-img/img/2023/08/03/400x225/-p-Bank-officer-arrested-for--58L-theft-from-custo_1691087973349.jpg)

![Image](https://www.projectsof8051.com/project-photos/1645-sms-based-bank-locker-security-system-using-gsm-technology-21.jpg)

![Image](https://bankofbaroda.bank.in/-/media/project/bob/countrywebsites/india/personal-banking/accounts/safe-deposit-lockers/spotlight/safe-deposit-lockers-spotlightbanner.jpg?h=400\&hash=3CF510EC2DEEB034EE9592BA659619B7\&iar=0\&w=1080)

![Image](https://wpblogassets.paytm.com/paytmblog/uploads/2026/01/image-2026-01-28T114244.264.jpg)

* You cannot directly access locker
* You go through **security guard (Proxy)**

✔ Guard:

* Verifies identity (Security Proxy)
* Allows access (Control)
* Logs entry (Logging)

👉 Locker = Real Object
👉 Guard = Proxy

---

## 4. Structure Diagram

```
        Client
           |
           v
        Proxy  ----->  RealSubject
           |
    (Controls Access)
```

### Flow:

1. Client calls Proxy
2. Proxy decides:

   * Allow / Deny / Modify
3. Proxy forwards to Real Object

---

## 5. Components / Roles

### 1. Subject (Interface)

* Common interface for Proxy & Real Object

### 2. RealSubject

* Actual business logic

### 3. Proxy

* Controls access
* Adds extra behavior

### 4. Client

* Uses Subject interface

---

## 6. Java Implementation

### Step 1: Subject Interface

```java
public interface Image {
    void display();
}
```

---

### Step 2: Real Object (Heavy Object)

```java
public class RealImage implements Image {
    private String fileName;

    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk(); // heavy operation
    }

    private void loadFromDisk() {
        System.out.println("Loading image from disk: " + fileName);
    }

    public void display() {
        System.out.println("Displaying image: " + fileName);
    }
}
```

---

### Step 3: Proxy Class

```java
public class ProxyImage implements Image {
    private RealImage realImage;
    private String fileName;

    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }

    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName); // Lazy loading
        }
        realImage.display();
    }
}
```

---

### Step 4: Client

```java
public class Main {
    public static void main(String[] args) {
        Image image = new ProxyImage("photo.jpg");

        image.display(); // loads + displays
        image.display(); // only displays (no loading)
    }
}
```

---

### ✅ Key Insight

👉 First call → Object created
👉 Second call → Reused

✔ This is **Virtual Proxy (Lazy Loading)**

---

## 7. Advantages

✔ **Lazy Initialization**

* Object created only when needed

✔ **Security Control**

* Access validation possible

✔ **Performance Optimization**

* Avoid heavy object creation

✔ **Logging / Monitoring**

* Track usage

✔ **Remote Access Handling**

* Hide network complexity

---

## 8. Disadvantages

❌ Adds extra layer (complexity)
❌ Slight performance overhead
❌ More classes to maintain
❌ Can make debugging harder

---

## 9. Where Used in Java / Spring / Microservices

---

### 🔹 1. Spring AOP (VERY IMPORTANT 🔥)

👉 Spring uses **Proxy internally**

Example:

```java
@Transactional
public void transferMoney() {}
```

✔ Spring creates **proxy object**
✔ Adds:

* Transaction management
* Logging
* Security

👉 Internally uses:

* JDK Dynamic Proxy
* CGLIB Proxy

---

### 🔹 2. Hibernate / JPA

👉 Lazy Loading:

```java
User user = userRepository.findById(1);
```

✔ `user.getOrders()` → loads later

👉 That `user` is actually a **Proxy object**

---

### 🔹 3. Remote Proxy (Microservices)

👉 Feign Client / REST Client

```java
@FeignClient("payment-service")
```

✔ Proxy handles:

* HTTP calls
* Serialization
* Retry

👉 Looks like local method call
👉 Actually remote API call

---

### 🔹 4. Security Proxy

👉 Spring Security

* Authentication check before method execution

---

### 🔹 5. Caching Proxy

👉 Redis / Cache Layer

* Proxy checks cache first
* Calls DB only if needed

---

## 🔥 Types of Proxy (Interview Bonus)

1. **Virtual Proxy** → Lazy loading
2. **Remote Proxy** → Remote calls (Microservices)
3. **Protection Proxy** → Access control
4. **Caching Proxy** → Performance optimization
5. **Logging Proxy** → Monitoring

---

## 10. Interview Questions

---

### ❓ Q1: What is Proxy Design Pattern?

👉 A structural pattern that provides a **placeholder object to control access to real object**

---

### ❓ Q2: Difference between Proxy vs Decorator?

| Proxy                | Decorator                |
| -------------------- | ------------------------ |
| Controls access      | Adds behavior            |
| Focus on **control** | Focus on **enhancement** |
| May restrict access  | Never restricts          |

---

### ❓ Q3: Where is Proxy used in Spring?

👉

* Spring AOP
* @Transactional
* Security
* Hibernate Lazy Loading

---

### ❓ Q4: What is Dynamic Proxy?

👉 Proxy created **at runtime**

* JDK Proxy (Interfaces)
* CGLIB (Classes)

---

### ❓ Q5: Is Feign Client a Proxy?

👉 YES ✅
✔ It is a **Remote Proxy**

---

### ❓ Q6: Real-world example in project?

👉

* API Gateway (Proxy for services)
* Cache layer
* Authentication layer

---

## 🧠 Final Interview Summary (One-Liner)

👉 **Proxy = Control access to object without changing its original code**

---

## 🔥 Pro Tip (Important for YOU)

Since you're working in **Java + Microservices**:

👉 Remember this mapping:

| Concept                | Proxy Type       |
| ---------------------- | ---------------- |
| Spring AOP             | Dynamic Proxy    |
| Hibernate Lazy Loading | Virtual Proxy    |
| Feign Client           | Remote Proxy     |
| API Gateway            | Protection Proxy |

---
# Observer Design Pattern
Great choice—**Observer Design Pattern** is one of the most asked patterns in interviews and *very important for real-world systems (especially event-driven architectures, Spring, Kafka, etc.)*.

I’ll explain it in a **clean, note-making + interview-ready format** 👇

---

# 🔵 Observer Design Pattern

---

## 1. Problem

In many applications, we have **one object (Subject)** whose state changes, and **multiple other objects (Observers)** need to be notified automatically.

### ❌ Without Observer Pattern:

* Tight coupling between objects
* Subject must know all dependent classes
* Hard to scale (adding/removing listeners is difficult)

👉 Example problem:

```
WeatherStation changes → MobileApp, Website, LED Display need updates
```

You don’t want:

```
weatherStation.updateMobile();
weatherStation.updateWebsite();
weatherStation.updateLED();
```

---

## 2. Intent

> Define a **one-to-many dependency** so that when one object changes state, all its dependents are notified and updated automatically.

---

## 3. Real World Analogy

### 📺 YouTube Subscription Model

* You = Subscriber (Observer)
* YouTube Channel = Subject

When a creator uploads video:

* All subscribers get notification automatically

👉 You don’t need to manually check every time
👉 System notifies you

---

## 4. Structure Diagram

![Image](https://cdn-images.visual-paradigm.com/tutorials/observerdesignpattern_screenshots/29_select_concrete_observer.png)

![Image](https://www.cs.mcgill.ca/~hv/classes/CS400/01.hchen/doc/observer/observer.gif)

![Image](https://www.tutorialspoint.com/design_pattern/images/observer_pattern_uml_diagram.jpg)

![Image](https://www.ionos.com/digitalguide/fileadmin/_processed_/2/1/csm_uml-diagram-of-the-observer-pattern_678577b8ca.webp)

### Key Flow:

```
Subject → maintains list of Observers
Observer → registers/unregisters
Subject → notifyObservers()
Observer → update()
```

---

## 5. Components / Roles

### 1. Subject (Publisher)

* Maintains list of observers
* Methods:

  * register()
  * remove()
  * notifyObservers()

### 2. Observer (Subscriber)

* Interface with `update()` method

### 3. ConcreteSubject

* Holds actual state
* Notifies observers on change

### 4. ConcreteObserver

* Implements update()
* Reacts to state changes

---

## 6. Java Implementation

### Step-by-step clean implementation 👇

### 🔹 Observer Interface

```java
interface Observer {
    void update(String message);
}
```

---

### 🔹 Subject Interface

```java
interface Subject {
    void subscribe(Observer o);
    void unsubscribe(Observer o);
    void notifyObservers();
}
```

---

### 🔹 Concrete Subject

```java
import java.util.*;

class YouTubeChannel implements Subject {

    private List<Observer> subscribers = new ArrayList<>();
    private String video;

    public void subscribe(Observer o) {
        subscribers.add(o);
    }

    public void unsubscribe(Observer o) {
        subscribers.remove(o);
    }

    public void notifyObservers() {
        for (Observer o : subscribers) {
            o.update(video);
        }
    }

    public void uploadVideo(String video) {
        this.video = video;
        notifyObservers(); // 🔥 automatic notification
    }
}
```

---

### 🔹 Concrete Observer

```java
class Subscriber implements Observer {

    private String name;

    public Subscriber(String name) {
        this.name = name;
    }

    public void update(String video) {
        System.out.println(name + " received notification: " + video);
    }
}
```

---

### 🔹 Client Code

```java
public class Main {
    public static void main(String[] args) {

        YouTubeChannel channel = new YouTubeChannel();

        Observer user1 = new Subscriber("Danish");
        Observer user2 = new Subscriber("Rahul");

        channel.subscribe(user1);
        channel.subscribe(user2);

        channel.uploadVideo("Observer Pattern Explained!");
    }
}
```

---

### ✅ Output:

```
Danish received notification: Observer Pattern Explained!
Rahul received notification: Observer Pattern Explained!
```

---

## 7. Advantages

✅ Loose coupling between Subject & Observers
✅ Easy to add/remove observers at runtime
✅ Supports event-driven systems
✅ Follows Open/Closed Principle

---

## 8. Disadvantages

❌ Can lead to memory leaks if observers not removed
❌ Unexpected updates (hard to debug sometimes)
❌ Performance issue if too many observers
❌ No guarantee of notification order

---

## 9. Where Used in Java / Spring / Microservices

This is the **MOST IMPORTANT INTERVIEW PART** 👇

---

### 🔹 Core Java

1. **Java Event Listener Model**

   * `EventListener`, `EventObject`

2. **java.util.Observer (Deprecated)**

   * Old implementation of Observer pattern

---

### 🔹 Spring Framework

1. **Spring ApplicationEventPublisher**

   * Publisher = Subject
   * Listeners = Observers

```java
applicationEventPublisher.publishEvent(new OrderCreatedEvent());
```

2. `@EventListener` → Observer

---

### 🔹 Microservices (VERY IMPORTANT 🔥)

Observer pattern = **Event-Driven Architecture**

Examples:

* **Apache Kafka**
* **RabbitMQ**

👉 Producer = Subject
👉 Consumer = Observer

---

### 🔹 Real Use Cases

* Notification systems
* Order status updates
* Stock price updates
* Email/SMS triggers
* Logging & monitoring systems

---

## 10. Interview Questions

### ⭐ Basic

1. What is Observer Pattern?
2. Difference between Observer vs Pub-Sub?
3. Push vs Pull model?

---

### ⭐ Intermediate

4. How does Spring implement Observer pattern?
5. Why is `java.util.Observer` deprecated?
6. How to avoid memory leaks in Observer?

---

### ⭐ Advanced

7. Observer vs Event-driven architecture?
8. How Kafka implements Observer pattern?
9. How to handle failure in observers?
10. How to scale Observer pattern in distributed systems?

---

# 🎯 Quick Revision (1-Min)

* One-to-Many relationship
* Subject notifies observers automatically
* Loose coupling
* Used in Spring events & Kafka
* Core concept of Event-Driven Systems

---
# Strategy Design Pattern
Perfect Danish — this is one of the **MOST important design patterns for interviews + real projects**. I’ll explain it in your **notes-friendly + interview-ready format** 👇

---

# 🧠 Strategy Design Pattern (Behavioral Pattern)

---

## 1. 🔴 Problem

Sometimes we have **multiple ways to perform the same task**, and the behavior needs to change **dynamically at runtime**.

### ❌ Without Strategy Pattern:

```java
class PaymentService {
    public void pay(String type) {
        if(type.equals("UPI")) {
            // UPI logic
        } else if(type.equals("CARD")) {
            // Card logic
        } else if(type.equals("NETBANKING")) {
            // Netbanking logic
        }
    }
}
```

### 🚨 Problems:

* Violates **Open/Closed Principle**
* Hard to maintain (if-else jungle)
* Difficult to extend (new payment method = modify existing code)

---

## 2. 🎯 Intent

👉 **Define a family of algorithms, encapsulate each one, and make them interchangeable.**

👉 Strategy lets the algorithm vary **independently from clients that use it**.

---

## 3. 🌍 Real World Analogy

### 🚗 Google Maps Navigation

* You choose:

  * 🚶 Walking
  * 🚗 Car
  * 🚴 Bike

Each is a **different strategy** to reach the same destination.

👉 You can switch strategy **at runtime**

---

## 4. 🧩 Structure Diagram

![Image](https://www.ionos.com/digitalguide/fileadmin/_processed_/1/6/csm_strategy-pattern-in-uml_040eb33a1e.webp)

![Image](https://journaldev.nyc3.cdn.digitaloceanspaces.com/2013/07/Strategy-Pattern-450x261.png)

![Image](https://d3373sevsv1jc.cloudfront.net/uploads/communities_production/article_block/72626/4B8079A8516C4CFA800F920379B71CD4.jpeg)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2Almwy4xyR7AID30FjlEoQ9A.png)

### Flow:

```
Client → Context → Strategy Interface → Concrete Strategies
```

---

## 5. 🧱 Components / Roles

### 1. Strategy (Interface)

* Defines common behavior

```java
interface PaymentStrategy {
    void pay(int amount);
}
```

---

### 2. Concrete Strategies

* Different implementations

```java
class UpiPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid via UPI: " + amount);
    }
}

class CardPayment implements PaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid via Card: " + amount);
    }
}
```

---

### 3. Context

* Uses a strategy object

```java
class PaymentContext {
    private PaymentStrategy strategy;

    public PaymentContext(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void execute(int amount) {
        strategy.pay(amount);
    }
}
```

---

### 4. Client

* Chooses strategy

```java
public class Main {
    public static void main(String[] args) {

        PaymentStrategy strategy = new UpiPayment();
        PaymentContext context = new PaymentContext(strategy);

        context.execute(1000);

        // Change strategy at runtime
        context = new PaymentContext(new CardPayment());
        context.execute(2000);
    }
}
```

---

## 6. 💻 Java Implementation (Clean Version)

```java
// Step 1: Strategy Interface
interface DriveStrategy {
    void drive();
}

// Step 2: Concrete Strategies
class NormalDrive implements DriveStrategy {
    public void drive() {
        System.out.println("Normal Driving");
    }
}

class SportsDrive implements DriveStrategy {
    public void drive() {
        System.out.println("Sports Driving");
    }
}

// Step 3: Context
class Vehicle {
    private DriveStrategy driveStrategy;

    public Vehicle(DriveStrategy driveStrategy) {
        this.driveStrategy = driveStrategy;
    }

    public void drive() {
        driveStrategy.drive();
    }
}

// Step 4: Usage
public class Main {
    public static void main(String[] args) {

        Vehicle normalCar = new Vehicle(new NormalDrive());
        normalCar.drive();

        Vehicle sportsCar = new Vehicle(new SportsDrive());
        sportsCar.drive();
    }
}
```

---

## 7. ✅ Advantages

✔ Eliminates **if-else conditions**
✔ Follows **Open/Closed Principle**
✔ Easy to add new strategies
✔ Promotes **composition over inheritance**
✔ Runtime behavior change possible

---

## 8. ❌ Disadvantages

❌ Increases number of classes
❌ Client must know different strategies
❌ Slightly more complex than simple logic

---

## 9. ⚙️ Where Used (Java / Spring / Microservices)

### 🔹 Core Java

* `Comparator` in sorting

```java
Collections.sort(list, new CustomComparator());
```

---

### 🔹 Spring Framework

* **Spring Boot Payment / Discount logic**
* Different **authentication strategies**
* Different **bean implementations using @Qualifier**

---

### 🔹 Microservices (VERY IMPORTANT 🔥)

👉 Real-world use cases:

* Payment systems (UPI, Card, Wallet)
* Notification systems (Email, SMS, Push)
* Pricing strategies (Discount, Coupon, Surge pricing)
* Retry strategies (Exponential backoff, Fixed retry)

---

### 🔹 Spring Example

```java
@Autowired
@Qualifier("upiPayment")
private PaymentStrategy paymentStrategy;
```

👉 Spring internally uses Strategy Pattern via **dependency injection**

---

## 10. 🎯 Interview Questions

### ⭐ Basic

**Q1: What problem does Strategy Pattern solve?**
👉 Multiple algorithms for same task, avoid if-else

---

**Q2: Strategy vs State Pattern?**

* Strategy → behavior chosen by client
* State → behavior changes internally

---

**Q3: Why better than inheritance?**
👉 Avoids subclass explosion
👉 Promotes composition

---

### ⭐ Intermediate

**Q4: Can we change strategy at runtime?**
👉 YES (core feature)

---

**Q5: Real project example?**
👉 Payment system / Notification service

---

### ⭐ Advanced

**Q6: How is Strategy used in Spring?**
👉 Via `@Qualifier` and multiple bean implementations

---

**Q7: Difference between Strategy and Factory?**

* Strategy → behavior selection
* Factory → object creation

---

# 🧠 Final Summary (Revision Shortcut)

👉 Strategy = **Replace if-else with polymorphism**

👉 Key idea:

```
Encapsulate behavior → Make interchangeable → Use at runtime
```

---

# 💡 Pro Tip (Interview GOLD 🔥)

If interviewer asks:

👉 *"Where have you used this?"*

Say:

> "In payment systems, notification services, and pricing modules where behavior changes dynamically. In Spring Boot, we achieve this using multiple implementations + @Qualifier which is internally based on Strategy Pattern."

---
# Command Design Pattern
Perfect Danish — this is a **very important pattern for interviews**, especially when they ask about **decoupling, undo/redo, queues, or event-driven systems**.

I’ll explain it in a **clean, notes-ready + interview-focused format** 👇

---

# ✅ Command Design Pattern (Interview Guide)

---

# 1. 🔴 Problem

In many applications:

* You have a **request sender (Invoker)** and a **request executor (Receiver)**
* But they are **tightly coupled**

👉 Example:

```java
button.click() → light.turnOn()
```

Problems:

* UI directly depends on business logic
* Hard to:

  * Add new operations
  * Implement undo/redo
  * Queue or log requests
* Violates **Open/Closed Principle**

---

# 2. 🟢 Intent

> Convert a request into an object so that you can:

* Parameterize clients with different requests
* Queue or log requests
* Support undo/redo operations

👉 In short:
**Encapsulate a request as an object**

---

# 3. 🌍 Real World Analogy

### 🍽️ Restaurant Ordering System

* Customer → gives order
* Waiter → takes order (Invoker)
* Chef → prepares food (Receiver)
* Order Slip → Command object

👉 Key Idea:
Waiter doesn't cook → just passes command

---

# 4. 🏗️ Structure Diagram (Textual)

```
Client
   ↓
Command (interface)
   ↑
ConcreteCommand
   ↓
Receiver (actual logic)

Invoker → triggers command.execute()
```

Flow:

```
Client → sets command → Invoker → calls execute() → Receiver does work
```

---

# 5. 🧩 Components / Roles

### 1. Command (Interface)

* Declares `execute()`

```java
interface Command {
    void execute();
}
```

---

### 2. ConcreteCommand

* Implements Command
* Binds Receiver

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

---

### 3. Receiver

* Actual business logic

```java
class Light {
    void turnOn() {
        System.out.println("Light ON");
    }
}
```

---

### 4. Invoker

* Triggers command

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

### 5. Client

* Creates and connects everything

```java
public class Main {
    public static void main(String[] args) {
        Light light = new Light();
        Command command = new LightOnCommand(light);

        RemoteControl remote = new RemoteControl();
        remote.setCommand(command);
        remote.pressButton();
    }
}
```

---

# 6. 💻 Java Implementation (Full Clean Version)

```java
// Command
interface Command {
    void execute();
}

// Receiver
class Light {
    void turnOn() {
        System.out.println("Light is ON");
    }

    void turnOff() {
        System.out.println("Light is OFF");
    }
}

// Concrete Commands
class LightOnCommand implements Command {
    private Light light;

    LightOnCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.turnOn();
    }
}

class LightOffCommand implements Command {
    private Light light;

    LightOffCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.turnOff();
    }
}

// Invoker
class RemoteControl {
    private Command command;

    void setCommand(Command command) {
        this.command = command;
    }

    void pressButton() {
        command.execute();
    }
}

// Client
public class Main {
    public static void main(String[] args) {
        Light light = new Light();

        Command onCommand = new LightOnCommand(light);
        Command offCommand = new LightOffCommand(light);

        RemoteControl remote = new RemoteControl();

        remote.setCommand(onCommand);
        remote.pressButton();

        remote.setCommand(offCommand);
        remote.pressButton();
    }
}
```

---

# 7. ✅ Advantages

✔ Decouples sender and receiver
✔ Easy to add new commands (OCP)
✔ Supports:

* Undo / Redo
* Logging
* Queueing
  ✔ Clean separation of concerns
  ✔ Great for event-driven systems

---

# 8. ❌ Disadvantages

❌ Too many classes (class explosion)
❌ Extra abstraction → overkill for small systems
❌ Hard to understand initially

---

# 9. 🚀 Where Used (Java / Spring / Microservices)

## 🔹 Java

* `Runnable` → Command pattern
* `Thread` execution
* `ExecutorService`

👉 Example:

```java
executor.submit(() -> doWork());
```

---

## 🔹 Spring Framework

### 1. Spring AOP (Indirectly)

* Encapsulates method calls as actions

### 2. Spring Events

```java
ApplicationEventPublisher.publishEvent()
```

👉 Commands as events

---

## 🔹 Microservices

### 🔥 VERY IMPORTANT (Interview)

Used in:

#### 1. Event-Driven Architecture

* Kafka messages = Commands
* Producer sends command
* Consumer executes

#### 2. Task Queues

* RabbitMQ / Kafka
* Async processing

#### 3. CQRS Pattern

* Commands vs Queries separation

---

# 10. 🎯 Interview Questions

---

### ❓ Q1: What problem does Command pattern solve?

👉 Converts request into object → decouples sender & receiver

---

### ❓ Q2: Difference between Strategy vs Command?

| Strategy          | Command             |
| ----------------- | ------------------- |
| Select algorithm  | Encapsulate request |
| Focus on behavior | Focus on action     |
| Client chooses    | Invoker triggers    |

---

### ❓ Q3: Where is it used in real projects?

👉 Kafka, RabbitMQ, Executors, Undo systems

---

### ❓ Q4: How undo works?

👉 Store command history + add `undo()` method

---

### ❓ Q5: Runnable is which pattern?

👉 Command Pattern

---

# 🧠 Final Summary (1 Line)

> Command Pattern = **"Request ko object bana do"** so you can decouple, queue, log, and undo it.

---

# 🔥 Pro Tip (For Your Interviews)

When interviewer asks:

👉 “Where have you used this?”

Say confidently:

> “In microservices, Kafka messages act like commands where producer sends action and consumer executes it asynchronously.”

---
# Template Design Pattern
Great choice Danish — **Template Design Pattern** is *very important for interviews* and also *very practical in real projects*, especially in Spring.

Let’s break it down in a **clean, notes-friendly + interview-ready format** 👇

---

# 🔷 Template Method Design Pattern

---

## 1. Problem

In many applications, we have:

* Same **overall process / workflow**
* But **some steps differ**

👉 Example:

* Payment processing (steps same, implementation differs)
* Data processing (read → process → save)

### ❌ Problem Without Template Pattern

* Code duplication
* Hard to maintain
* Violates DRY principle

---

## 2. Intent

👉 **Define the skeleton of an algorithm in a base class, but allow subclasses to override specific steps without changing the overall structure.**

✔ Fixed flow
✔ Customizable steps

---

## 3. Real World Analogy ☕

### Making Tea vs Coffee

![Image](https://cdn.shopify.com/s/files/1/0379/3413/7481/files/how_to_make_a_cup_of_tea.jpg?v=1742894431)

![Image](https://images.twinkl.co.uk/tw1n/image/private/t_630_eco/image_repo/1e/b0/t2-p-602-making-a-cup-of-coffee-sequencing-cards-_ver_1.jpg)

![Image](https://www.tallengestore.com/cdn/shop/products/Coffee_vs_Tea_Comparison_Poster_-_Art_For_Kitchen_Dining_Cafes_Retaurants_16502837-b589-4a4d-ade3-3ca90eb11123_large.jpg?v=1544782545)

![Image](https://cdn.shopify.com/s/files/1/0800/4858/7043/files/Chart_15.jpg?v=1734581576)

### Common Steps:

1. Boil water
2. Add main ingredient
3. Pour into cup

### Variable Steps:

* Tea → add tea leaves + milk
* Coffee → add coffee powder

👉 Same process, different implementation = Template Pattern

---

## 4. Structure Diagram

![Image](https://reactiveprogramming.io/_next/image?q=75\&url=%2Fbooks%2Fpatterns%2Fimg%2Fpatterns-articles%2Ftemplete-method-diagram.png\&w=3840)

![Image](https://dotnettrickscloud.blob.core.windows.net/article/design%20patterns/3720240913123928.webp)

![Image](https://www.tutorialspoint.com/design_pattern/images/template_pattern_uml_diagram.jpg)

![Image](https://sourcemaking.com/files/v2/content/patterns/Template_method_example.png?id=d5f4969ed9a502093a8139da0f613479)

### Flow:

```
AbstractClass
 ├── templateMethod()   (final)
 ├── step1()            (common)
 ├── step2()            (abstract)
 ├── step3()            (abstract)

ConcreteClassA
ConcreteClassB
```

---

## 5. Components / Roles

### 1. Abstract Class (Template Owner)

* Defines **template method**
* Contains:

  * Fixed steps
  * Abstract steps
  * Optional hooks

👉 Most important piece

---

### 2. Template Method

* Defines algorithm structure
* Usually marked `final`

---

### 3. Concrete Classes

* Implement variable steps

---

### 4. Hook Methods (Optional)

* Default implementation
* Can be overridden if needed

---

## 6. Java Implementation 💻

### Step 1: Abstract Class

```java
abstract class DataProcessor {

    // Template Method (final)
    public final void process() {
        readData();
        processData();
        saveData();
    }

    protected void readData() {
        System.out.println("Reading data from source...");
    }

    protected abstract void processData();

    protected void saveData() {
        System.out.println("Saving data to DB...");
    }
}
```

---

### Step 2: Concrete Classes

```java
class CSVDataProcessor extends DataProcessor {

    @Override
    protected void processData() {
        System.out.println("Processing CSV data...");
    }
}

class JSONDataProcessor extends DataProcessor {

    @Override
    protected void processData() {
        System.out.println("Processing JSON data...");
    }
}
```

---

### Step 3: Client

```java
public class Main {
    public static void main(String[] args) {

        DataProcessor csv = new CSVDataProcessor();
        csv.process();

        System.out.println();

        DataProcessor json = new JSONDataProcessor();
        json.process();
    }
}
```

---

### Output

```
Reading data from source...
Processing CSV data...
Saving data to DB...

Reading data from source...
Processing JSON data...
Saving data to DB...
```

---

## 7. Advantages ✅

✔ Eliminates code duplication
✔ Enforces consistent workflow
✔ Promotes reuse
✔ Follows Open/Closed Principle
✔ Easy to extend new variations

---

## 8. Disadvantages ❌

❌ Tight coupling with base class
❌ Inheritance-heavy (less flexible than composition)
❌ Difficult to modify algorithm later
❌ Can lead to many subclasses

---

## 9. Where Used in Java / Spring / Microservices 🚀

### 🔹 Core Java

* `AbstractList`, `AbstractMap`
* `InputStream` / `OutputStream`

👉 Define structure, subclasses override behavior

---

### 🔹 Spring Framework (VERY IMPORTANT)

#### 1. JdbcTemplate

👉 **Classic Template Pattern**

```java
jdbcTemplate.query(...)
```

* Connection handling → fixed
* Query logic → customizable

---

#### 2. RestTemplate

* Request execution → fixed
* Response handling → customizable

---

#### 3. TransactionTemplate

* Transaction flow fixed
* Business logic inside callback

---

### 🔹 Microservices

* API processing pipelines
* Request handling frameworks
* Event processing flows

👉 Example:

```
validate → transform → process → respond
```

Only **transform/process** changes

---

## 🔥 Important Insight (Interview Gold)

👉 Template Pattern = **Inheritance-based reuse**

👉 Strategy Pattern = **Composition-based reuse**

💡 If interviewer asks difference:

* Template → flow fixed, steps override
* Strategy → algorithm interchangeable

---

## 10. Interview Questions 🎯

### Basic

1. What is Template Method Pattern?
2. When do you use it?
3. Why is template method usually `final`?

---

### Intermediate

4. Difference between Template and Strategy Pattern?
5. What are hook methods?
6. Can we violate Open/Closed Principle here?

---

### Advanced

7. How is `JdbcTemplate` an example of Template Pattern?
8. Why is Template Pattern less flexible than Strategy?
9. How would you refactor Template Pattern using composition?

---

## 🧠 Quick Revision (1-Min Summary)

* Same flow, different steps → Template Pattern
* Base class controls flow
* Child class customizes behavior
* Uses inheritance
* Widely used in Spring (`JdbcTemplate`)

---
# Composite Design Pattern
Great choice Danish — **Composite Design Pattern** is *very important* for interviews, especially for system design + low-level design.

I’ll explain it in a **clean, notes-friendly + interview-ready format** 👇

---

# 🔷 Composite Design Pattern

---

## 1. ❗ Problem

When working with **tree-like hierarchical structures**, we often face this problem:

👉 You have:

* **Individual objects (Leaf)**
* **Group of objects (Composite)**

👉 But client code has to treat them **differently**

### Example:

```text
File (leaf)
Folder (contains files + folders)
```

Without Composite:

```java
if (object is File) → do this
if (object is Folder) → loop children
```

❌ Problem:

* Complex client code
* Multiple condition checks
* Hard to extend

---

## 2. 🎯 Intent

👉 **Treat individual objects and group of objects uniformly**

> "Part-Whole Hierarchy ko same way me treat karna"

---

## 3. 🌍 Real World Analogy

### 📁 File System

```text
Folder
 ├── File1
 ├── File2
 └── Folder2
      ├── File3
```

👉 You can:

* Get size of a **file**
* Get size of a **folder (sum of all files)**

💡 But client calls same method:

```java
getSize()
```

---

## 4. 🏗️ Structure Diagram (Textual)

```
        Component (interface)
           /         \
      Leaf         Composite
                      |
                 List<Component>
```

👉 Key idea:

* Both Leaf & Composite implement same interface

---

## 5. 🧩 Components / Roles

### 1. Component (Interface / Abstract)

* Common operations

```java
interface FileSystem {
    void showDetails();
}
```

---

### 2. Leaf

* Represents individual object
* No children

```java
class File implements FileSystem
```

---

### 3. Composite

* Contains children (list of components)

```java
class Folder implements FileSystem {
    List<FileSystem> children;
}
```

---

### 4. Client

* Works with Component interface
* Doesn't care Leaf or Composite

---

## 6. 💻 Java Implementation (Clean & Interview Ready)

### Step 1: Component

```java
interface FileSystem {
    void showDetails();
}
```

---

### Step 2: Leaf

```java
class File implements FileSystem {
    private String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void showDetails() {
        System.out.println("File: " + name);
    }
}
```

---

### Step 3: Composite

```java
import java.util.ArrayList;
import java.util.List;

class Folder implements FileSystem {
    private String name;
    private List<FileSystem> children = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(FileSystem component) {
        children.add(component);
    }

    public void remove(FileSystem component) {
        children.remove(component);
    }

    @Override
    public void showDetails() {
        System.out.println("Folder: " + name);

        for (FileSystem component : children) {
            component.showDetails(); // recursive call
        }
    }
}
```

---

### Step 4: Client Code

```java
public class Main {
    public static void main(String[] args) {

        File file1 = new File("file1.txt");
        File file2 = new File("file2.txt");

        Folder folder1 = new Folder("Folder1");
        folder1.add(file1);
        folder1.add(file2);

        File file3 = new File("file3.txt");

        Folder root = new Folder("Root");
        root.add(folder1);
        root.add(file3);

        root.showDetails();
    }
}
```

---

### 🧠 Output

```text
Folder: Root
Folder: Folder1
File: file1.txt
File: file2.txt
File: file3.txt
```

---

## 7. ✅ Advantages

✔ Uniform treatment (Leaf + Composite same interface)
✔ Cleaner client code (no if-else)
✔ Easy to extend (Open/Closed Principle)
✔ Natural fit for tree structures
✔ Supports recursion elegantly

---

## 8. ❌ Disadvantages

❌ Hard to restrict components (Leaf vs Composite rules)
❌ Can make design overly generic
❌ Debugging recursive structures can be tricky
❌ Sometimes overkill for simple use cases

---

## 9. 🚀 Where Used (Java / Spring / Microservices)

### 🔹 Java

1. **java.awt.Container**

   * Component hierarchy (UI elements)

2. **javax.swing**

   * Buttons, Panels, Frames

3. **File API**

   * File & Directory structure

---

### 🔹 Spring Framework

1. **Spring Security Filter Chain**

   * Multiple filters combined as chain

2. **Bean Definition Trees**

   * Parent-child bean relationships

---

### 🔹 Microservices (VERY IMPORTANT 🔥)

👉 Real-world usage:

### 1. API Gateway Aggregation

```text
Response =
    User Service +
    Order Service +
    Payment Service
```

👉 Composite response built from multiple services

---

### 2. Organization Hierarchy

```text
Company
 ├── Department
 │    ├── Team
 │    │    ├── Employee
```

---

### 3. Menu / Permission System

```text
Menu
 ├── SubMenu
 │    ├── Items
```

---

### 🔥 Interview Punch Line:

> "Whenever you see **tree structure + recursive processing**, think Composite Pattern"

---

## 10. 🎤 Interview Questions

### ⭐ Basic

👉 What is Composite Pattern?
👉 Difference between Leaf & Composite?
👉 When to use it?

---

### ⭐ Intermediate

👉 How does it follow Open/Closed Principle?
👉 Why recursion is important here?
👉 Can Leaf have children? Why/why not?

---

### ⭐ Advanced

👉 Composite vs Decorator?

👉 Composite vs Flyweight?

👉 How would you design:

* File system?
* Organization hierarchy?
* UI components?

---

### 💥 Trick Question

👉 Why not just use inheritance?

Answer:

> Because we need **tree structure + uniform handling**, not just hierarchy.

---

# 🔥 Final Summary (1-Liner for Interview)

> Composite Pattern allows you to treat individual objects and compositions of objects uniformly by organizing them into a tree structure.

---
Short answer: **YES — conceptually similar, but not the same thing.**
Let’s make this crystal clear for interview 👇

---

# 🔥 DSA Trees vs Composite Design Pattern

## ✅ Core Similarity (IMPORTANT)

👉 Both are based on **Tree Structure (Hierarchy)**

```text
       Root
      /    \
   Node    Node
   /  \       \
 Leaf Leaf    Leaf
```

👉 In **Composite Pattern**:

* Composite = Internal Node
* Leaf = Leaf Node

👉 In **DSA Tree**:

* Same structure → Nodes + Children

✔ So structurally → **100% similar**

---

## ❗ Key Difference (VERY IMPORTANT FOR INTERVIEW)

| Aspect   | DSA Tree              | Composite Pattern  |
| -------- | --------------------- | ------------------ |
| Purpose  | Data structure        | Design pattern     |
| Focus    | Algorithms (DFS, BFS) | Object composition |
| Behavior | Data traversal        | Uniform behavior   |
| Example  | Binary Tree           | File System        |

---

## 🧠 How to Think (Golden Rule)

👉 **DSA Tree = Structure**
👉 **Composite Pattern = Design on top of that structure**

---

## 🌍 Real Mapping Example

### 📁 File System

👉 DSA View:

```text
Tree of nodes (Folder/File)
```

👉 Composite View:

```text
Common interface → showDetails()
```

✔ Same structure
✔ Different intention

---

## 💻 Code-Level Understanding

### DSA Tree Node

```java
class Node {
    int data;
    Node left, right;
}
```

👉 Focus = traversal (DFS, BFS)

---

### Composite Pattern

```java
interface Component {
    void operation();
}
```

👉 Focus = behavior uniformity

---

## 🔥 Interview-Level Answer (Best Line)

If interviewer asks:

👉 *"Is Composite related to Tree?"*

💬 Answer:

> "Yes, Composite Pattern is conceptually based on tree structures used in DSA. The difference is that DSA trees focus on data storage and traversal, while Composite Pattern focuses on treating individual and group objects uniformly using a common interface."

---

## ⚠️ Common Mistake (Avoid This)

❌ Saying:

> "Composite = Tree"

✔ Correct:

> "Composite **uses tree-like structure**, but it’s a design pattern, not a data structure."

---

## 🚀 When Both Come Together (Real Project Insight)

In real systems:

👉 You use:

* **DSA Tree internally**
* **Composite Pattern for design**

Example:

* UI components (Swing)
* File systems
* Organization hierarchy

---

## 🧠 Final Memory Trick

👉 If question is about:

* Traversal → **DSA**
* Structure + behavior → **Composite**

---
# State Design Pattern
Here’s a **clean, interview-ready explanation of the State Design Pattern** — structured exactly for notes + deep understanding.

---

# 🔷 State Design Pattern (Behavioral)

---

## 1. Problem

In many applications, an object behaves differently based on its **current state**.

👉 Typical implementation (bad approach):

```java
if(state == "NEW") { ... }
else if(state == "PROCESSING") { ... }
else if(state == "SHIPPED") { ... }
```

### ❌ Issues:

* Too many `if-else` / `switch` conditions
* Hard to maintain when states increase
* Violates **Open/Closed Principle**
* Logic becomes messy and tightly coupled

---

## 2. Intent

👉 **"Allow an object to alter its behavior when its internal state changes."**

* Object appears to **change its class dynamically**
* Encapsulate each state as a separate class

---

## 3. Real World Analogy

### 🚦 Traffic Signal System

![Image](https://assets3.thrillist.com/v1/image/1797521/414x310/crop%3Bwebp%3Dauto%3Bjpeg_quality%3D60%3Bprogressive.jpg)

![Image](https://www.researchgate.net/publication/306110984/figure/fig3/AS%3A667624656601095%401536185530149/Sequence-diagram-of-the-traffic-light-adjustment-task.png)

![Image](https://upload.wikimedia.org/wikipedia/commons/thumb/2/24/Traffic_lights_4_states.svg/1280px-Traffic_lights_4_states.svg.png)

![Image](https://png.pngtree.com/png-clipart/20221006/original/pngtree-three-state-traffic-light-illustration-png-image_8660889.png)

* Red → Stop
* Yellow → Ready
* Green → Go

👉 Behavior changes based on **current state**

No `if-else` needed — each light defines its behavior.

---

## 4. Structure Diagram

### 📊 UML Structure

![Image](https://www.tutorialspoint.com/design_pattern/images/state_pattern_uml_diagram.jpg)

![Image](https://refactoring.guru/images/patterns/diagrams/state/structure-en.png?id=caac48afbc4b2d95829cd7c7eb0dcacf)

![Image](https://reactiveprogramming.io/_next/image?q=75\&url=%2Fbooks%2Fpatterns%2Fimg%2Fpatterns-articles%2Fstate-diagram.png\&w=3840)

![Image](https://refactoring.guru/images/patterns/diagrams/state/structure-en-indexed.png?id=df120eb1fa260d0c86e3187410d98f20)

---

### 🔑 Structure Understanding:

```
Context → State (interface)
              ↑
     --------------------
     |        |         |
ConcreteStateA  ConcreteStateB ...
```

---

## 5. Components / Roles

### 1. Context

* Maintains current state
* Delegates behavior to state object

### 2. State (Interface)

* Defines behavior for all states

### 3. Concrete States

* Implement behavior specific to each state
* Can switch context’s state

---

## 6. Java Implementation

### 🎯 Example: Order Processing System

---

### Step 1: State Interface

```java
interface OrderState {
    void next(OrderContext context);
    void prev(OrderContext context);
    void printStatus();
}
```

---

### Step 2: Concrete States

```java
class NewState implements OrderState {

    public void next(OrderContext context) {
        context.setState(new ProcessingState());
    }

    public void prev(OrderContext context) {
        System.out.println("Order is in initial state");
    }

    public void printStatus() {
        System.out.println("Order placed, waiting for processing");
    }
}
```

---

```java
class ProcessingState implements OrderState {

    public void next(OrderContext context) {
        context.setState(new ShippedState());
    }

    public void prev(OrderContext context) {
        context.setState(new NewState());
    }

    public void printStatus() {
        System.out.println("Order is being processed");
    }
}
```

---

```java
class ShippedState implements OrderState {

    public void next(OrderContext context) {
        System.out.println("Order already shipped");
    }

    public void prev(OrderContext context) {
        context.setState(new ProcessingState());
    }

    public void printStatus() {
        System.out.println("Order shipped");
    }
}
```

---

### Step 3: Context

```java
class OrderContext {

    private OrderState state;

    public OrderContext() {
        state = new NewState();
    }

    public void setState(OrderState state) {
        this.state = state;
    }

    public void next() {
        state.next(this);
    }

    public void prev() {
        state.prev(this);
    }

    public void printStatus() {
        state.printStatus();
    }
}
```

---

### Step 4: Client

```java
public class Main {
    public static void main(String[] args) {

        OrderContext order = new OrderContext();

        order.printStatus(); // New
        order.next();

        order.printStatus(); // Processing
        order.next();

        order.printStatus(); // Shipped
    }
}
```

---

## 7. Advantages

✅ Eliminates complex `if-else` logic
✅ Follows **Open/Closed Principle**
✅ Easy to add new states
✅ Improves readability & maintainability
✅ Each state has single responsibility

---

## 8. Disadvantages

❌ Too many classes (one per state)
❌ Slightly complex structure for beginners
❌ State transition logic may become scattered
❌ Overkill for simple logic

---

## 9. Where Used in Java / Spring / Microservices

### ☕ Java

* `Thread.State` (NEW, RUNNABLE, BLOCKED, etc.)
* `javax.faces.lifecycle` (JSF lifecycle)
* Workflow engines

---

### 🌱 Spring Framework

* Spring State Machine (very important 🔥)
* Bean lifecycle management
* AOP proxy behavior (indirect relation)

---

### ☁️ Microservices

* Order lifecycle (NEW → PAID → SHIPPED → DELIVERED)
* Payment processing systems
* Saga pattern (state transitions across services)
* Workflow engines (Camunda, Temporal)

---

## 10. Interview Questions

### ⭐ Basic

**Q1. What problem does State Pattern solve?**
👉 Eliminates conditional logic by delegating behavior to state objects

---

**Q2. Difference between State vs Strategy?**

| State                        | Strategy                 |
| ---------------------------- | ------------------------ |
| Changes behavior dynamically | Chooses algorithm        |
| State controls transition    | Client controls strategy |
| Internal behavior change     | External selection       |

---

**Q3. Who controls state transition?**
👉 Usually **Concrete States**

---

### ⭐ Intermediate

**Q4. When to use State Pattern?**

* When object behavior depends on state
* When there are many conditional branches
* When states change dynamically

---

**Q5. Can states be shared?**
👉 Yes (stateless states can be reused as singleton)

---

### ⭐ Advanced

**Q6. How is it different from finite state machine (FSM)?**
👉 State pattern is **OOP implementation of FSM**

---

**Q7. How does Spring State Machine relate?**
👉 It’s a **production-ready implementation of State Pattern**

---

# 🔥 Quick Revision (1-minute)

* Replace `if-else` → **State classes**
* Context delegates → State object
* Each state = separate class
* State controls transition
* Real-world = traffic light / order lifecycle

---
Short answer: **No ❌ — Saga pattern is NOT the same as State Design Pattern.**
But… they are **related conceptually**. Let’s break it clearly (interview-ready 👇)

---

# 🔥 Core Difference (1 line)

👉 **State Pattern = Design Pattern (code-level behavior change)**
👉 **Saga Pattern = Distributed Architecture Pattern (microservices transaction management)**

---

# 🧠 1. What is State Pattern?

* Behavioral design pattern (GoF)
* Works **inside a single application**
* Changes object behavior based on state

👉 Example:

```
Order → NEW → PROCESSING → SHIPPED
```

Each state = separate class

---

# 🌐 2. What is Saga Pattern?

👉 In microservices, we don’t have a single DB transaction

So we use **Saga Pattern** to manage distributed transactions.

Two types:

* **Orchestration Saga**
* **Choreography Saga**

👉 Example flow:

```
Order Service → Payment Service → Inventory Service → Shipping Service
```

If something fails → rollback using **compensating transactions**

---

# 🔗 3. Where the Confusion Comes From

Because Saga also has **states internally**.

### Example:

Order Saga states:

* CREATED
* PAYMENT_PENDING
* PAYMENT_COMPLETED
* FAILED

👉 This looks like State Pattern… but:

⚠️ Difference:

* Saga = manages **workflow across services**
* State Pattern = manages **behavior inside an object**

---

# 🧩 4. Relationship (Important Interview Insight)

👉 **Saga internally can USE State Pattern**

So:

```
Saga Pattern (High-Level)
        ↓
State Pattern (Internal Implementation)
```

---

# 📦 Real Example (Microservices)

### Order Service:

```java
class Order {
    OrderState state; // State Pattern
}
```

### But overall system:

* Order Service
* Payment Service
* Inventory Service

👉 Coordination = **Saga Pattern**

---

# 🎯 Real World Analogy

### 🏦 Bank Transfer

![Image](https://strapi.decentro.tech/uploads/Debit_Card_Processing_How_dc1e8f9212.jpg)

![Image](https://microservices.io/i/sagas/Create_Order_Saga_Orchestration.png)

![Image](https://microservices.io/i/sagas/From_2PC_To_Saga.png)

![Image](https://substackcdn.com/image/fetch/%24s_%21_mI0%21%2Cf_auto%2Cq_auto%3Agood%2Cfl_progressive%3Asteep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F423873e0-2789-4635-a8ae-d3dfd64edd8f_2021x1270.png)

* Debit account
* Credit account
* If credit fails → rollback debit

👉 That’s Saga
👉 Each step internally may have states → State Pattern

---

# ⚔️ 5. Direct Comparison

| Feature | State Pattern       | Saga Pattern                   |
| ------- | ------------------- | ------------------------------ |
| Type    | Design Pattern      | Architectural Pattern          |
| Scope   | Single object / app | Multiple microservices         |
| Purpose | Behavior change     | Distributed transaction        |
| Level   | Low-level (code)    | High-level (system design)     |
| Example | Order state         | Order workflow across services |

---

# 🧠 Interview Answer (Perfect)

👉 If interviewer asks:

**“Is Saga same as State Pattern?”**

Answer:

> “No, Saga is a distributed transaction pattern used in microservices, while State Pattern is a behavioral design pattern used to manage object behavior based on state. However, Saga workflows often internally use state transitions, so State Pattern can be used as part of a Saga implementation.”

---

# 🔥 Final Understanding

* ❌ Not same
* ✅ Related
* ✅ Often used together

---
# Saga Design Pattern
Perfect — this is a **very important pattern for Microservices interviews** 🔥
Let’s break **Saga Design Pattern** in a **clean, notes-friendly, interview-ready format**.

---

# 🔷 Saga Design Pattern

---

## 1. 🧩 Problem

In **Microservices Architecture**, one business transaction often spans **multiple services**.

👉 Example:

* Order Service
* Payment Service
* Inventory Service

### ❌ Problem:

* No **global transaction (like DB transaction)** across services
* If one service fails → system becomes **inconsistent**

👉 Example failure:

* Order created ✅
* Payment done ✅
* Inventory update ❌

Now system is broken → money taken but no product reserved

---

## 2. 🎯 Intent

👉 **Manage distributed transactions across multiple services**

✔ Ensure:

* **Data consistency**
* **Failure handling**
* **Rollback using compensating actions**

👉 Instead of 1 big transaction → break into **multiple local transactions**

---

## 3. 🌍 Real World Analogy

### 🛫 Flight Booking System

1. Book Flight ✈️
2. Book Hotel 🏨
3. Book Cab 🚕

👉 If Cab booking fails:

* Cancel Hotel ❌
* Cancel Flight ❌

👉 Each step has a **reverse action (compensation)**

---

## 4. 🏗️ Structure Diagram

### 🔹 Choreography-Based Saga

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AZDszFKdY4MFLJN9z7uA7LA.png)

![Image](https://miro.medium.com/1%2AZkToAgyvsEeNVvxHKXJU2Q.png)

![Image](https://akfpartners.com//uploads/blog/Saga_Choreographer_Example.jpg)

👉 Services communicate via **events**

---

### 🔹 Orchestration-Based Saga

![Image](https://microservices.io/i/sagas/Create_Order_Saga_Orchestration.png)

![Image](https://substackcdn.com/image/fetch/%24s_%217zCy%21%2Cf_auto%2Cq_auto%3Agood%2Cfl_progressive%3Asteep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F7759c44e-bbf9-4d5d-8853-afcfcd3af215_1600x995.png)

👉 One **central orchestrator** controls flow

---

## 5. 🧱 Components / Roles

### 1. Saga

* Represents the **entire business transaction**

---

### 2. Local Transactions

* Each service executes its own DB transaction

---

### 3. Compensating Transactions

* Undo operations if failure occurs

👉 Example:

* `createOrder()` → `cancelOrder()`

---

### 4. Communication Mechanism

#### a) Choreography

* Event-based (Kafka, RabbitMQ)
* No central control

#### b) Orchestration

* Central controller (Saga Orchestrator)

---

## 6. 💻 Java Implementation (Simplified)

### 🔹 Example: Order → Payment → Inventory

---

### Step 1: Order Service

```java
public class OrderService {

    public void createOrder(Order order) {
        // Save order
        System.out.println("Order Created");

        // Trigger next step
        eventPublisher.publish("ORDER_CREATED", order);
    }

    public void cancelOrder(Order order) {
        System.out.println("Order Cancelled");
    }
}
```

---

### Step 2: Payment Service

```java
public class PaymentService {

    public void processPayment(Order order) {
        System.out.println("Payment Processed");

        eventPublisher.publish("PAYMENT_SUCCESS", order);
    }

    public void refundPayment(Order order) {
        System.out.println("Payment Refunded");
    }
}
```

---

### Step 3: Inventory Service

```java
public class InventoryService {

    public void reserveInventory(Order order) {
        if(false) { // simulate failure
            throw new RuntimeException("Inventory Failed");
        }

        System.out.println("Inventory Reserved");
    }
}
```

---

### Step 4: Compensation Flow

```java
try {
    orderService.createOrder(order);
    paymentService.processPayment(order);
    inventoryService.reserveInventory(order);
} catch (Exception e) {
    paymentService.refundPayment(order);
    orderService.cancelOrder(order);
}
```

---

## 7. ✅ Advantages

✔ No need for distributed DB transactions (2PC avoided)
✔ Scales well in microservices
✔ Loose coupling between services
✔ Supports eventual consistency
✔ Fault-tolerant

---

## 8. ❌ Disadvantages

❌ Complex to implement
❌ Hard to debug (especially choreography)
❌ No immediate consistency (eventual consistency only)
❌ Requires careful design of compensating actions
❌ Risk of partial failures

---

## 9. 🚀 Where Used in Java / Spring / Microservices

### 🔹 In Real Projects

Used in:

* E-commerce systems
* Banking transactions
* Booking systems (flight, hotel)

---

### 🔹 In Spring Ecosystem

👉 Common tools:

* **Spring Boot** → service implementation
* **Spring Cloud** → orchestration support
* **Kafka / RabbitMQ** → event-driven saga

---

### 🔹 Frameworks supporting Saga

* Axon Framework
* Eventuate Tram
* Camunda / Temporal (workflow orchestration)

---

### 🔹 Example Flow (Real World)

```
Order Service → Kafka Event → Payment Service → Kafka Event → Inventory Service
```

---

## 10. 🎤 Interview Questions

---

### ❓ Q1: What is Saga Pattern?

👉 A design pattern to manage **distributed transactions using local transactions + compensating actions**

---

### ❓ Q2: Saga vs 2PC (Two Phase Commit)?

| Saga                 | 2PC                |
| -------------------- | ------------------ |
| Eventual consistency | Strong consistency |
| Scalable             | Not scalable       |
| No locking           | Uses locking       |
| Complex logic        | Simpler logic      |

---

### ❓ Q3: Choreography vs Orchestration?

| Choreography          | Orchestration      |
| --------------------- | ------------------ |
| No central controller | Central controller |
| Event-driven          | Command-driven     |
| Hard to debug         | Easier to debug    |

---

### ❓ Q4: What is Compensating Transaction?

👉 A rollback operation to undo a previous step

---

### ❓ Q5: When NOT to use Saga?

👉 When:

* Strong consistency is mandatory
* Simple monolith works better

---

# 🧠 Final Interview Summary (1-Liner)

👉
**Saga Pattern = Distributed transaction management using a sequence of local transactions with compensating rollback actions.**
