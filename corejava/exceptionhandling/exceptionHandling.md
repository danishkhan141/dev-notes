# Index
1. [Inroduction](#exception-handling-in-java--complete-interview-preparation)
2. [Ways to handle Exception in Java](#ways-to-handle-exceptions-in-java--deep-dive)
3. [Throw vs Throws Deep Dive](#understanding-throw-and-throws-properly)
4. [Exception Handling in Spring/SpringBoot Deep Dive](#exception-handling-in-spring--spring-boot--complete-deep-dive)
5. [Transaction + Exception Handling](#transaction--exception-handling-in-spring-boot--complete-deep-dive)
6. [Summary](#summary)

# Exception Handling in Java – Complete Interview Preparation

Exception Handling is one of the most important topics in Java interviews because it directly relates to:

* Application stability
* Debugging
* Production issue handling
* API error responses
* Transaction management
* Logging & monitoring

In real projects, exception handling is not just about `try-catch`.
It is about:

* graceful failure,
* proper logging,
* meaningful error messages,
* system recovery,
* and preventing application crashes.

---

# 1. What is Exception Handling?

Exception Handling is a mechanism to handle runtime errors so that the normal flow of application does not terminate unexpectedly.

Without exception handling:

* application crashes
* user gets bad experience
* transactions may remain incomplete
* APIs return ugly stack traces

---

# 2. Real World Example

Suppose:

* User is transferring money
* Amount deducted from Account A
* While crediting Account B → DB connection fails

Without exception handling:

* money deducted
* not credited
* inconsistent system

With exception handling:

* rollback transaction
* log error
* send proper response

---

# 3. What is Exception?

An Exception is an unwanted event that occurs during program execution and disrupts normal flow.

Examples:

* divide by zero
* file not found
* database connection failure
* null pointer
* array index issue

---

# 4. Exception Hierarchy

Very important interview topic.

```text
Object
   |
Throwable
   |
-------------------------
|                       |
Error               Exception
                        |
             -------------------
             |                 |
 Checked Exception    RuntimeException
                           |
                  Unchecked Exception
```

---

# 5. Error vs Exception

| Feature   | Error                  | Exception           |
| --------- | ---------------------- | ------------------- |
| Type      | Serious problem        | Recoverable problem |
| Handling  | Usually cannot recover | Can recover         |
| Example   | OutOfMemoryError       | IOException         |
| Caused By | JVM/System             | Application         |

Examples of Errors:

* StackOverflowError
* OutOfMemoryError

---

# 6. Checked vs Unchecked Exception

MOST IMPORTANT INTERVIEW QUESTION.

---

## A. Checked Exception

Checked at compile time.

Compiler forces handling.

Examples:

* IOException
* SQLException
* FileNotFoundException

Example:

```java
FileReader fr = new FileReader("abc.txt");
```

Compiler says:
"Handle exception"

Either:

```java
try-catch
```

or

```java
throws
```

---

## B. Unchecked Exception

Occurs at runtime.

Compiler does not force handling.

Examples:

* NullPointerException
* ArithmeticException
* ArrayIndexOutOfBoundsException

Example:

```java
int a = 10 / 0;
```

Runtime crash.

---

# 7. Why Runtime Exceptions Exist?

Because they are mostly programming mistakes.

Example:

* null handling mistake
* wrong array index
* bad logic

Java assumes:
"Developer should fix code."

---

# 8. Exception Handling Keywords

| Keyword | Purpose                  |
| ------- | ------------------------ |
| try     | risky code               |
| catch   | handle exception         |
| finally | always executes          |
| throw   | manually throw exception |
| throws  | declare exception        |

---

# 9. try-catch

Basic structure.

```java
try {
    int result = 10 / 0;
}
catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Flow:

1. try executes
2. exception occurs
3. JVM creates exception object
4. catch handles it

---

# 10. Multiple catch Blocks

```java
try {
    String s = null;
    System.out.println(s.length());
}
catch (ArithmeticException e) {
    System.out.println("Arithmetic Error");
}
catch (NullPointerException e) {
    System.out.println("Null Error");
}
catch (Exception e) {
    System.out.println("General Error");
}
```

IMPORTANT:
Specific → Generic order

Wrong:

```java
catch(Exception e)
catch(NullPointerException e)
```

Compile error.

---

# 11. finally Block

Finally always executes whether exception occurs or not.

Used for:

* closing DB connections
* file closing
* cleanup

Example:

```java
try {
    System.out.println("Try");
}
catch(Exception e) {
    System.out.println("Catch");
}
finally {
    System.out.println("Finally");
}
```

Output:

```text
Try
Finally
```

---

# 12. Important finally Interview Question

Will finally always execute?

NO.

Cases:

* System.exit(0)
* JVM crash
* power failure

Example:

```java
try {
    System.exit(0);
}
finally {
    System.out.println("Will not execute");
}
```

---

# 13. throw Keyword

Used to manually create exception.

```java
public void validateAge(int age) {

    if(age < 18) {
        throw new RuntimeException("Not eligible");
    }

    System.out.println("Eligible");
}
```

---

# 14. throws Keyword

Used to declare exceptions.

```java
public void readFile() throws IOException {
    FileReader fr = new FileReader("abc.txt");
}
```

Meaning:
"Caller must handle it."

---

# 15. throw vs throws

| throw                   | throws                       |
| ----------------------- | ---------------------------- |
| Used to throw exception | Used to declare exception    |
| Inside method           | In method signature          |
| Single exception object | Multiple exceptions possible |

---

# 16. Exception Propagation

Exception travels method-to-method until handled.

Example:

```java
class Test {

    void m3() {
        int x = 10 / 0;
    }

    void m2() {
        m3();
    }

    void m1() {
        m2();
    }

    public static void main(String[] args) {

        Test t = new Test();

        try {
            t.m1();
        }
        catch(Exception e) {
            System.out.println("Handled");
        }
    }
}
```

Flow:
m3 → m2 → m1 → main → catch

---

# 17. Stack Trace

When exception occurs JVM prints:

* exception type
* message
* line number
* call hierarchy

Example:

```text
Exception in thread "main"
java.lang.ArithmeticException: / by zero
at Test.m3(Test.java:5)
at Test.m2(Test.java:9)
at Test.m1(Test.java:13)
```

VERY IMPORTANT for debugging production issues.

---

# 18. Common Runtime Exceptions

| Exception                      | Cause              |
| ------------------------------ | ------------------ |
| NullPointerException           | null access        |
| ArithmeticException            | divide by zero     |
| ArrayIndexOutOfBoundsException | invalid index      |
| NumberFormatException          | invalid conversion |
| ClassCastException             | wrong casting      |

---

# 19. Custom Exception

Very important in enterprise applications.

Used when:
business-specific errors needed.

Example:

* InvalidAmountException
* UserNotFoundException
* InsufficientBalanceException

---

## Creating Custom Checked Exception

```java
class InvalidAgeException extends Exception {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Usage:

```java
public class Test {

    static void vote(int age) throws InvalidAgeException {

        if(age < 18) {
            throw new InvalidAgeException("Age must be >= 18");
        }

        System.out.println("Eligible");
    }

    public static void main(String[] args) {

        try {
            vote(16);
        }
        catch(Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

---

# 20. Best Practices

VERY IMPORTANT for interviews.

---

## 1. Never Use Empty catch

BAD:

```java
catch(Exception e) {
}
```

Why bad?

* hides production issues

---

## 2. Catch Specific Exceptions

BAD:

```java
catch(Exception e)
```

GOOD:

```java
catch(SQLException e)
```

---

## 3. Log Properly

BAD:

```java
System.out.println(e);
```

GOOD:

```java
logger.error("Database failed", e);
```

---

## 4. Never Expose Internal Errors to User

BAD API Response:

```json
java.lang.NullPointerException...
```

GOOD:

```json
{
  "message": "Something went wrong"
}
```

---

## 5. Use Custom Exceptions

Improves readability and debugging.

---

# 21. Exception Handling in Spring Boot

EXTREMELY IMPORTANT.

In real projects:

* we do NOT use try-catch everywhere

Instead:

* Global Exception Handling

---

# 22. @ControllerAdvice

Centralized exception handling.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

---

# 23. Why Global Exception Handling?

Without it:

* repeated try-catch everywhere

With it:

* centralized handling
* cleaner code
* consistent API response

---

# 24. Professional API Error Response

Real projects return structured response.

Example:

```json
{
   "timestamp":"2026-05-07",
   "status":404,
   "message":"User not found",
   "path":"/users/10"
}
```

---

# 25. Exception Handling Flow in Spring Boot

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Exception Occurs
   ↓
@ControllerAdvice
   ↓
Custom Response
```

---

# 26. Checked vs Unchecked in Real Projects

In modern Spring Boot:

* Runtime exceptions used more
* Checked exceptions used less

Why?

Because:

* too much boilerplate
* cleaner architecture with runtime exceptions

---

# 27. Important Interview Questions

## Basic Level

1. What is exception?
2. Difference between error and exception?
3. Checked vs unchecked exception?
4. throw vs throws?
5. finally block use?
6. Can finally block skip execution?

---

## Intermediate Level

7. What is exception propagation?
8. Why custom exceptions?
9. Why catch specific exceptions?
10. Why exception handling important in microservices?

---

## Advanced Level

11. How Spring Boot handles exceptions globally?
12. How do you design API error responses?
13. Difference between @ExceptionHandler and @ControllerAdvice?
14. Why runtime exceptions preferred in Spring Boot?
15. How transaction rollback works with exceptions?

---

# 28. Transaction + Exception Handling

VERY IMPORTANT.

```java
@Transactional
public void transferMoney() {

    debit();

    credit(); // exception here
}
```

If runtime exception occurs:

* transaction rollback

If checked exception:

* rollback may NOT happen automatically

Interviewers LOVE this question.

---

# 29. Exception Handling in Microservices

In microservices:

* services communicate over network
* failures are common

Examples:

* timeout
* service unavailable
* circuit breaker open
* Kafka failure

Need:

* fallback
* retry
* global exception handling
* proper HTTP status codes

---

# 30. HTTP Status Codes Mapping

| Exception         | HTTP Status |
| ----------------- | ----------- |
| User not found    | 404         |
| Validation failed | 400         |
| Unauthorized      | 401         |
| Forbidden         | 403         |
| Internal error    | 500         |

---

# 31. Professional Exception Handling Strategy

In enterprise projects:

```text
Controller
   ↓
Service
   ↓
Repository
   ↓
Custom Exception
   ↓
Global Exception Handler
   ↓
Structured API Response
   ↓
Logging + Monitoring
```

---

# 32. Most Common Production Mistakes

| Mistake                       | Problem              |
| ----------------------------- | -------------------- |
| Empty catch                   | hidden bug           |
| Generic exception everywhere  | hard debugging       |
| No logging                    | impossible debugging |
| Huge try block                | messy code           |
| Returning stack trace to user | security risk        |

---

# 33. Difference Between final, finally, finalize

CLASSIC INTERVIEW QUESTION.

| Keyword  | Purpose              |
| -------- | -------------------- |
| final    | constant/restriction |
| finally  | cleanup block        |
| finalize | GC cleanup method    |

---

# 34. Try-With-Resources (Java 7+)

Automatic resource closing.

BEST PRACTICE.

```java
try (BufferedReader br =
         new BufferedReader(new FileReader("a.txt"))) {

    System.out.println(br.readLine());
}
catch(IOException e) {
    e.printStackTrace();
}
```

Automatically closes file.

---

# 35. Exception Handling Flow Internally

Deep JVM flow:

```text
Exception occurs
      ↓
JVM creates Exception Object
      ↓
Searches matching catch block
      ↓
If found → executes catch
      ↓
Else → propagates upward
      ↓
If nowhere handled
      ↓
JVM terminates thread
```

---

# 36. Most Important Real Project Interview Answer

## "How did you handle exceptions in your project?"

Professional Answer:

> In our Spring Boot microservices project, we used centralized exception handling using `@RestControllerAdvice`. We created custom exceptions for business scenarios like UserNotFoundException and ValidationException. We returned standardized API responses with proper HTTP status codes and logged all critical exceptions using SLF4J. For transactional operations, we used `@Transactional` to ensure rollback during failures. We also handled external service failures using retry and fallback mechanisms.

---

# 37. Quick Revision Notes

## Core Concepts

* Exception = runtime problem
* Throwable is parent
* Error = serious JVM issue
* Exception = recoverable issue

---

## Types

* Checked → compile-time
* Unchecked → runtime

---

## Keywords

* try
* catch
* finally
* throw
* throws

---

## Spring Boot

* @ControllerAdvice
* @ExceptionHandler
* Custom exceptions
* Structured API response

---

# 38. Final Interview Tip

Interviewers usually check:

* conceptual clarity
* real project usage
* debugging understanding
* API handling
* transaction knowledge
* production mindset

So always explain exception handling using:

* real-world examples
* API examples
* Spring Boot examples
* transaction examples

That creates a senior-level impression.

---
# Ways to Handle Exceptions in Java – Deep Dive

When interviewers ask:

> "In how many ways can we handle exceptions in Java?"

Most candidates answer:

* try-catch
* throws

But professionally, exception handling is MUCH bigger than this.

There are multiple levels:

1. JVM Level
2. Method Level
3. Application Level
4. Spring Boot Level
5. Microservice Level
6. Distributed System Level

---

# COMPLETE CLASSIFICATION

```text id="92ckm5"
1. JVM Default Handling
2. try-catch
3. Multiple catch
4. Nested try-catch
5. finally block
6. throw keyword
7. throws keyword
8. Exception propagation
9. Custom exceptions
10. Try-with-resources
11. Global exception handling
12. Transaction rollback handling
13. Retry/Fallback handling
14. Async exception handling
15. Kafka/message exception handling
16. Distributed microservice exception handling
```

---

# 1. JVM DEFAULT EXCEPTION HANDLING

If developer does not handle exception,
JVM handles it.

Example:

```java id="t3bjlwm"
public class Test {

    public static void main(String[] args) {

        int x = 10 / 0;

        System.out.println("End");
    }
}
```

Output:

```text id="a6cy79"
Exception in thread "main"
java.lang.ArithmeticException: / by zero
```

Flow:

```text id="8v7uy0"
Exception Occurs
      ↓
JVM Creates Exception Object
      ↓
JVM Checks Catch Block
      ↓
No Catch Found
      ↓
Default Exception Handler Executes
      ↓
Stack Trace Printed
      ↓
Program Terminates
```

---

# Important Interview Point

JVM handling is NOT actual recovery.

It is:

* termination handling
* crash reporting

Application still stops.

---

# 2. try-catch BLOCK

MOST COMMON.

Used for:

* local exception recovery

Example:

```java id="nv46m7"
try {

    int result = 10 / 0;

}
catch(ArithmeticException e) {

    System.out.println("Cannot divide by zero");
}
```

---

# Internal Flow

```text id="4g9tfj"
try starts
   ↓
Exception occurs
   ↓
Remaining try block skipped
   ↓
Matching catch searched
   ↓
catch executes
   ↓
Program continues
```

---

# Real Project Usage

Used for:

* parsing
* validations
* external calls
* DB operations
* file operations

---

# 3. MULTIPLE CATCH BLOCKS

When multiple exception types possible.

Example:

```java id="2tt3hx"
try {

    String s = null;

    System.out.println(s.length());

}
catch(ArithmeticException e) {

    System.out.println("Arithmetic Error");
}
catch(NullPointerException e) {

    System.out.println("Null Error");
}
catch(Exception e) {

    System.out.println("General Error");
}
```

---

# Deep Internal Logic

JVM checks catch blocks top-to-bottom.

FIRST matching catch executes.

---

# VERY IMPORTANT

```java id="0ij9i9"
catch(Exception e)
catch(NullPointerException e)
```

Compile error.

Why?

Because:

* generic parent already catches child

---

# Best Practice

```text id="wjy5m7"
Specific Exception
       ↓
More Generic
       ↓
Most Generic
```

---

# 4. NESTED try-catch

try inside another try.

Example:

```java id="4mb6zb"
try {

    try {

        int x = 10 / 0;

    }
    catch(ArithmeticException e) {

        System.out.println("Inner Catch");
    }

}
catch(Exception e) {

    System.out.println("Outer Catch");
}
```

---

# Real Usage

Rarely used nowadays.

Can be useful when:

* partial recovery needed
* different recovery levels needed

---

# Why Mostly Avoided?

Creates:

* messy code
* readability issues
* maintenance problems

Modern applications prefer:

* helper methods
* global handlers

---

# 5. finally BLOCK

Used for cleanup logic.

Example:

```java id="36qjlwm"
Connection con = null;

try {

    con = DriverManager.getConnection(...);

}
catch(SQLException e) {

    e.printStackTrace();

}
finally {

    if(con != null) {
        con.close();
    }
}
```

---

# finally Executes In:

| Scenario             | finally Executes? |
| -------------------- | ----------------- |
| Exception occurs     | YES               |
| Exception not occurs | YES               |
| return statement     | YES               |
| System.exit(0)       | NO                |

---

# Real Project Use

Used for:

* DB close
* socket close
* file close
* cleanup

---

# Modern Replacement

Now mostly replaced by:

* try-with-resources

---

# 6. throw KEYWORD

Used to manually throw exception.

---

# Why Needed?

Sometimes JVM does not know business rule.

Example:

* minimum balance
* invalid order
* invalid payment

Developer manually throws exception.

---

# Example

```java id="bwodqo"
public void withdraw(double amount) {

    if(amount > balance) {

        throw new RuntimeException(
                "Insufficient Balance");
    }
}
```

---

# Internal Flow

```text id="r5xtf5"
Condition fails
      ↓
Developer creates exception object
      ↓
JVM propagates exception
```

---

# Real Usage

Used for:

* validations
* business rules
* security checks
* authorization

---

# 7. throws KEYWORD

Used to delegate handling responsibility.

Example:

```java id="9epcfx"
public void readFile()
        throws IOException {

    FileReader fr =
            new FileReader("abc.txt");
}
```

---

# Meaning

```text id="2l2b1l"
"I am not handling exception here.
Caller must handle it."
```

---

# Deep Analysis

`throws` does NOT handle exception.

It only:

* declares possibility
* transfers responsibility

---

# Exception Propagation with throws

```text id="7slq3p"
Method3
   ↓ throws
Method2
   ↓ throws
Method1
   ↓ throws
main()
```

---

# Real Project Use

Mostly used:

* utility methods
* service layers
* repository layers

---

# 8. EXCEPTION PROPAGATION

One of the MOST IMPORTANT concepts.

---

# What is Propagation?

Exception travels upward in call stack until handled.

---

# Example

```java id="2h4zc4"
class Test {

    void m3() {

        int x = 10 / 0;
    }

    void m2() {

        m3();
    }

    void m1() {

        m2();
    }

    public static void main(String[] args) {

        Test t = new Test();

        try {

            t.m1();

        }
        catch(Exception e) {

            System.out.println("Handled");
        }
    }
}
```

---

# Flow

```text id="vcrfj4"
m3()
 ↓
m2()
 ↓
m1()
 ↓
main()
 ↓
catch
```

---

# Important Point

Unchecked exceptions automatically propagate.

Checked exceptions require:

* throws
  OR
* try-catch

---

# 9. CUSTOM EXCEPTION HANDLING

Professional enterprise applications use custom exceptions heavily.

---

# Why Needed?

Instead of:

```java id="5r0rkz"
throw new RuntimeException("Error");
```

Use meaningful exceptions:

```java id="wbbrnp"
throw new UserNotFoundException();
```

---

# Benefits

| Benefit            | Why Important         |
| ------------------ | --------------------- |
| Better readability | easy debugging        |
| Business clarity   | domain-specific       |
| Cleaner APIs       | meaningful responses  |
| Better logging     | faster issue tracking |

---

# Example

```java id="r0gghm"
class InsufficientBalanceException
        extends RuntimeException {

    public InsufficientBalanceException(
            String msg) {

        super(msg);
    }
}
```

Usage:

```java id="j1uc2e"
if(balance < amount) {

    throw new InsufficientBalanceException(
            "Low balance");
}
```

---

# 10. TRY-WITH-RESOURCES

Modern professional handling mechanism.

Introduced in Java 7.

---

# Problem Before Java 7

Manual resource closing.

```java id="i10pt4"
finally {
   br.close();
}
```

Risk:

* memory leak
* resource leak

---

# Modern Solution

```java id="5m4q2n"
try(BufferedReader br =
        new BufferedReader(
                new FileReader("a.txt"))) {

    System.out.println(br.readLine());
}
```

Automatically closes resource.

---

# Internally Uses

```text id="r1e94g"
AutoCloseable Interface
```

---

# Real Project Importance

VERY IMPORTANT in:

* DB connections
* Streams
* Files
* sockets

---

# 11. GLOBAL EXCEPTION HANDLING

Most important in Spring Boot.

---

# Problem

Without global handling:

```java id="ebh00s"
try {
}
catch {
}
```

everywhere.

Code becomes ugly.

---

# Solution

Centralized exception handling.

---

# Using @RestControllerAdvice

```java id="gpp8s4"
@RestControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handle(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());
    }
}
```

---

# Advantages

| Advantage           | Benefit             |
| ------------------- | ------------------- |
| Centralized         | clean code          |
| Consistent response | standard API        |
| Reusable            | less duplication    |
| Easy maintenance    | production friendly |

---

# 12. TRANSACTION EXCEPTION HANDLING

CRITICAL interview topic.

---

# Example

```java id="7ng1db"
@Transactional
public void transfer() {

    debit();

    credit();
}
```

Suppose:

* debit success
* credit fails

Without rollback:

* inconsistent data

---

# Runtime Exception

Automatically rollback.

---

# Checked Exception

May NOT rollback automatically.

Need:

```java id="s9v8g6"
@Transactional(
 rollbackFor = Exception.class
)
```

---

# Very Important Interview Point

Spring rolls back:

* RuntimeException
* Error

by default.

---

# 13. RETRY & FALLBACK HANDLING

Microservices level handling.

---

# Problem

Service B temporarily unavailable.

---

# Retry Mechanism

```text id="q9d5es"
Attempt 1 → fail
Attempt 2 → fail
Attempt 3 → success
```

---

# Used With

* Resilience4j
* RetryTemplate

---

# Fallback Mechanism

If service permanently fails:

* return default response

Example:

* cached data
* "service unavailable"

---

# 14. ASYNC EXCEPTION HANDLING

In multithreading / CompletableFuture.

---

# Problem

Exception occurs in another thread.

Normal try-catch cannot catch it.

---

# Example

```java id="0d3mdh"
CompletableFuture
    .supplyAsync(() -> {

        return 10 / 0;

    })
    .exceptionally(ex -> {

        System.out.println(ex);

        return 0;
    });
```

---

# Real Usage

Important in:

* async APIs
* parallel processing
* Kafka consumers

---

# 15. KAFKA / MESSAGE QUEUE EXCEPTION HANDLING

VERY IMPORTANT for microservices.

---

# Problem

Consumer processing fails.

What to do?

---

# Options

| Strategy      | Meaning             |
| ------------- | ------------------- |
| Retry         | process again       |
| DLQ           | move failed message |
| Ignore        | skip bad message    |
| Stop consumer | critical failure    |

---

# DLQ (Dead Letter Queue)

Failed messages stored separately.

Used for:

* debugging
* reprocessing

---

# 16. DISTRIBUTED MICROSERVICE EXCEPTION HANDLING

Advanced architecture handling.

---

# Problem

In microservices:

* network failure
* timeout
* partial failures
* unavailable services

---

# Handling Techniques

| Technique       | Purpose                |
| --------------- | ---------------------- |
| Retry           | temporary failure      |
| Circuit Breaker | stop cascading failure |
| Fallback        | alternative response   |
| Timeout         | avoid hanging          |
| Bulkhead        | isolate failures       |

---

# Example Flow

```text id="z43qmb"
Service A
   ↓
Service B timeout
   ↓
Circuit Breaker Opens
   ↓
Fallback Response
```

---

# MOST IMPORTANT INTERVIEW UNDERSTANDING

# Exception Handling Exists at 3 Levels

| Level              | Handling              |
| ------------------ | --------------------- |
| Code Level         | try-catch             |
| Framework Level    | global handling       |
| Architecture Level | retry/circuit breaker |

---

# PROFESSIONAL INTERVIEW ANSWER

If interviewer asks:

> "How many ways can we handle exceptions?"

Senior-level answer:

> In Java, exceptions can be handled in multiple ways depending on the layer of application. At core Java level we use try-catch, finally, throw, throws, propagation, custom exceptions, and try-with-resources. In Spring Boot we use global exception handling using @RestControllerAdvice. In microservices architecture we additionally handle exceptions using retry, fallback, circuit breaker, transaction rollback, async exception handling, and dead-letter queues for messaging systems like Kafka.

---
# Understanding `throw` and `throws` Properly

This is one of the MOST misunderstood Java interview topics.

Many developers think:

* `throw` and `throws` are always used together

That is WRONG.

They are completely different concepts.

---

# 1. Core Difference

| Keyword  | Purpose                           |
| -------- | --------------------------------- |
| `throw`  | Actually throws exception object  |
| `throws` | Declares possibility of exception |

---

# 2. Are `throw` and `throws` Required Together?

# NO.

They can be used:

* independently
* together
* or neither

---

# CASE 1: Using ONLY `throw`

Very common with Runtime Exceptions.

```java id="6c61g3"
public class Test {

    static void validate(int age) {

        if(age < 18) {

            throw new RuntimeException(
                    "Not eligible");
        }

        System.out.println("Eligible");
    }
}
```

---

# Why No `throws` Needed?

Because:
`RuntimeException` is unchecked.

Compiler does not force declaration.

---

# Important Internal Concept

All subclasses of:

```text id="m3mxcb"
RuntimeException
```

are:

* unchecked
* optional to handle
* optional to declare

---

# CASE 2: Using ONLY `throws`

Possible.

Example:

```java id="t8z6ux"
public void readFile()
        throws IOException {
}
```

No exception thrown currently.

But method declares:
"I MAY throw this exception."

---

# Real Usage

Mostly:

* interface methods
* abstract methods
* API contracts

---

# CASE 3: Using BOTH `throw` and `throws`

Very common for Checked Exceptions.

```java id="bjs6d5"
public void readFile()
        throws IOException {

    throw new IOException("File missing");
}
```

---

# Why Both Needed Here?

Because:

* `throw` actually throws
* `throws` informs compiler/caller

---

# VERY IMPORTANT RULE

# Checked Exceptions REQUIRE handling OR declaration.

---

# Compiler Rule

For checked exception, Java compiler forces one of these:

```text id="ry5hmm"
1. Handle using try-catch
OR
2. Declare using throws
```

Otherwise:
COMPILE ERROR.

---

# Example

```java id="e4kek7"
FileReader fr =
    new FileReader("abc.txt");
```

Compiler error:

```text id="ydy9bm"
Unhandled exception:
FileNotFoundException
```

---

# Why?

Because:
`FileNotFoundException` is checked.

---

# HOW TO FIX?

---

# OPTION 1 → HANDLE

```java id="g8q0l2"
try {

    FileReader fr =
        new FileReader("abc.txt");

}
catch(FileNotFoundException e) {

    e.printStackTrace();
}
```

---

# OPTION 2 → DECLARE

```java id="83yhgd"
public void read()
        throws FileNotFoundException {

    FileReader fr =
        new FileReader("abc.txt");
}
```

---

# Deep Understanding

# Checked Exceptions = Compiler Wants Safety

Java says:

> "This operation is risky.
> Developer MUST think about failure."

Examples:

* file handling
* DB connection
* networking

---

# Runtime Exceptions = Programming Mistakes

Examples:

* divide by zero
* null pointer
* wrong casting

Compiler assumes:
developer should fix code.

So handling is optional.

---

# MOST IMPORTANT INTERVIEW QUESTION

# "When do we definitely need to handle exception?"

Correct answer:

> Checked exceptions must either be handled using try-catch or declared using throws. Unchecked exceptions are optional to handle.

---

# SIMPLE FORMULA

```text id="c19u5m"
Checked Exception
      ↓
Mandatory:
try-catch
OR
throws
```

---

# Another Important Question

# "Can checked exception be thrown without throws?"

YES — IF handled locally.

Example:

```java id="jlwm9o"
public class Test {

    public static void main(String[] args) {

        try {

            throw new IOException("Error");

        }
        catch(IOException e) {

            System.out.println(e.getMessage());
        }
    }
}
```

No `throws` needed.

Why?

Because already handled.

---

# VERY IMPORTANT CONCEPT

# `throws` does NOT handle exception.

It only passes responsibility upward.

---

# Example

```java id="yccu0u"
void m3() throws IOException {

    throw new IOException();
}

void m2() throws IOException {

    m3();
}

void m1() throws IOException {

    m2();
}
```

---

# Flow

```text id="b2ahou"
m3 throws
   ↓
m2 throws
   ↓
m1 throws
   ↓
caller handles
```

---

# FINAL handling must happen somewhere.

Otherwise JVM terminates program.

---

# REAL PROJECT UNDERSTANDING

In enterprise applications:

---

# Service Layer

Usually propagates exception.

```java id="0c4wtk"
public User getUser(int id) {

    return repo.findById(id)
            .orElseThrow(
                () -> new UserNotFoundException()
            );
}
```

---

# Global Exception Handler

Actually handles.

```java id="o08pq9"
@RestControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handle(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());
    }
}
```

---

# IMPORTANT INTERVIEW POINT

In modern Spring Boot:

* Runtime exceptions preferred
* Checked exceptions used less

Why?

Because:

* less boilerplate
* cleaner code
* centralized handling

---

# THROW vs THROWS Deep Comparison

| Feature                          | throw                    | throws               |
| -------------------------------- | ------------------------ | -------------------- |
| Purpose                          | actually throw exception | declare exception    |
| Used Inside                      | method body              | method signature     |
| Followed By                      | object                   | class name           |
| Multiple allowed                 | NO                       | YES                  |
| Mandatory for checked exception? | NO                       | only if not handling |
| Transfers responsibility?        | NO                       | YES                  |

---

# Examples Table

| Scenario                          | throw    | throws   |
| --------------------------------- | -------- | -------- |
| Runtime exception                 | Optional | Optional |
| Checked exception handled locally | YES      | NO       |
| Checked exception propagated      | YES      | YES      |
| Method contract declaration       | NO       | YES      |

---

# Most Important Professional Understanding

# Exception handling has TWO responsibilities:

| Responsibility                    | Done By  |
| --------------------------------- | -------- |
| Exception creation                | `throw`  |
| Exception responsibility transfer | `throws` |

---

# Senior-Level Interview Answer

If interviewer asks:

> "Is it mandatory to use throw and throws together?"

Best answer:

> No. `throw` and `throws` are independent keywords. `throw` is used to actually throw an exception object, whereas `throws` is used to declare that a method may propagate an exception. For checked exceptions, we must either handle them using try-catch or declare them using throws. Runtime exceptions do not require mandatory handling or declaration.

# 1. If We `throws` Checked Exception But Caller Does NOT Handle It — What Happens?

VERY IMPORTANT core Java concept.

---

# First Understand

Checked exceptions are:

```text id="31ujsp"
Compile-time checked
```

Compiler forces handling.

---

# Example

```java id="j3d6kt"
public void readFile()
        throws IOException {

    FileReader fr =
            new FileReader("a.txt");
}
```

Now caller:

```java id="4q0w2f"
public static void main(String[] args) {

    Test t = new Test();

    t.readFile();
}
```

---

# What Happens?

# COMPILE ERROR

```text id="on8rwm"
Unhandled exception: IOException
```

---

# Why?

Because:

```text id="x6n4v0"
throws
```

means:

> "I am NOT handling it.
> Caller must handle or propagate further."

---

# Compiler Rule

For checked exceptions:

```text id="5drhn9"
HANDLE
OR
PROPAGATE
```

---

# So Caller Has ONLY 2 Options

---

# OPTION 1 → HANDLE

```java id="4sq7b5"
public static void main(String[] args) {

    try {

        Test t = new Test();

        t.readFile();

    }
    catch(IOException e) {

        e.printStackTrace();
    }
}
```

---

# OPTION 2 → PROPAGATE

```java id="cqjlwm"
public static void main(String[] args)
        throws IOException {

    Test t = new Test();

    t.readFile();
}
```

---

# Then Who Will Handle?

Now responsibility moves upward.

---

# Final Case

If exception reaches JVM without handling:

```text id="x5jl1z"
JVM default exception handler executes
```

Application terminates.

---

# VERY IMPORTANT FLOW

```text id="4slx9g"
m3() throws IOException
      ↓
m2() throws IOException
      ↓
m1() throws IOException
      ↓
main() throws IOException
      ↓
JVM handles
```

---

# IMPORTANT DIFFERENCE

| Exception Type    | Mandatory Handling? |
| ----------------- | ------------------- |
| Checked Exception | YES                 |
| Runtime Exception | NO                  |

---

# Runtime Exception Example

```java id="cn4j0l"
public void test() {

    throw new RuntimeException();
}
```

Caller can completely ignore it.

Compiler allows.

# Exception Handling in Spring / Spring Boot – Complete Deep Dive

This is a VERY important interview topic because in real enterprise applications:

* exception handling is centralized
* APIs must return proper responses
* logs must be generated
* transactions must rollback
* microservices must recover gracefully

In Spring Boot, exception handling is MUCH bigger than just `try-catch`.

---

# COMPLETE CLASSIFICATION

```text id="v7d3j8"
1. try-catch handling
2. throws propagation
3. throw custom exceptions
4. ResponseStatusException
5. @ExceptionHandler
6. @ControllerAdvice
7. @RestControllerAdvice
8. Custom exception classes
9. Global exception handling
10. Validation exception handling
11. Spring Security exception handling
12. Transaction rollback handling
13. Async exception handling
14. Kafka exception handling
15. Feign/WebClient/RestTemplate handling
16. Retry & Circuit Breaker handling
17. Filter/Interceptor level handling
18. Reactive exception handling (WebFlux)
```

---

# Understanding Exception Handling Layers in Spring Boot

```text id="ezg0vy"
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database

Exception can occur at ANY layer.
```

Spring provides different handling mechanisms for different layers.

---

# 1. try-catch Handling

Basic Java handling.

Still used in Spring Boot.

---

# Example

```java id="fdl47e"
public User getUser(int id) {

    try {

        return repo.findById(id).get();

    }
    catch(Exception e) {

        logger.error("Error occurred", e);

        return null;
    }
}
```

---

# Where Used?

Used for:

* partial recovery
* external API calls
* file handling
* custom fallback logic

---

# Why Not Used Everywhere?

Because:

* repetitive
* ugly code
* difficult maintenance

Modern Spring Boot prefers:

* centralized handling

---

# 2. throws Propagation

Spring applications often propagate exceptions upward.

---

# Example

```java id="k4cnlf"
public User getUser(int id)
        throws SQLException {

    return repo.fetch(id);
}
```

---

# Flow

```text id="4frx2x"
Repository
   ↓
Service
   ↓
Controller
   ↓
Global Handler
```

---

# Real Usage

Less common with checked exceptions nowadays.

Modern Spring prefers:

* runtime exceptions

---

# 3. Custom Exceptions

MOST IMPORTANT.

Real projects NEVER throw generic exceptions everywhere.

---

# Example

```java id="j25rzi"
public class UserNotFoundException
        extends RuntimeException {

    public UserNotFoundException(
            String msg) {

        super(msg);
    }
}
```

---

# Usage

```java id="l2m8gc"
public User getUser(int id) {

    return repo.findById(id)
            .orElseThrow(() ->
                    new UserNotFoundException(
                            "User not found"));
}
```

---

# Why Important?

| Benefit          | Why               |
| ---------------- | ----------------- |
| Better debugging | meaningful errors |
| Clean APIs       | proper response   |
| Business clarity | domain-specific   |
| Easy monitoring  | better logs       |

---

# 4. ResponseStatusException

Spring Boot built-in exception.

---

# Example

```java id="h7g4zj"
throw new ResponseStatusException(
        HttpStatus.NOT_FOUND,
        "User not found");
```

---

# Output

```json id="vmu17n"
{
  "status":404,
  "message":"User not found"
}
```

---

# When Used?

Quick/simple APIs.

---

# Why Large Projects Avoid It?

Because:

* business logic mixed with HTTP logic
* less reusable

Custom exceptions preferred.

---

# 5. @ExceptionHandler

Handles exceptions for a specific controller.

---

# Example

```java id="5f2n0v"
@RestController
public class UserController {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handle(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());
    }
}
```

---

# Scope

Only for that controller.

---

# Problem

If 50 controllers:

* duplicate code

---

# Solution

`@ControllerAdvice`

---

# 6. @ControllerAdvice

GLOBAL exception handling.

VERY IMPORTANT.

---

# Example

```java id="jlwmx7"
@ControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> handle(
            Exception ex) {

        return ResponseEntity
                .status(500)
                .body("Internal Error");
    }
}
```

---

# Flow

```text id="mjw1ve"
Exception occurs
      ↓
Spring searches handler
      ↓
@ControllerAdvice catches
      ↓
Custom response returned
```

---

# Major Advantages

| Advantage        | Benefit             |
| ---------------- | ------------------- |
| Centralized      | clean code          |
| Reusable         | less duplication    |
| Consistent API   | standard responses  |
| Easy maintenance | production friendly |

---

# 7. @RestControllerAdvice

Most used in REST APIs.

Combination of:

```text id="1gq5u7"
@ControllerAdvice
+
@ResponseBody
```

---

# Example

```java id="2v8v0k"
@RestControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<?> handle(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(404)
                .body(ex.getMessage());
    }
}
```

---

# MOST COMMON IN MICROSERVICES

Because:

* automatically returns JSON

---

# 8. Structured Error Response Handling

Professional APIs return standardized responses.

---

# Example DTO

```java id="f2zxkx"
public class ErrorResponse {

    private String message;
    private int status;
    private LocalDateTime timestamp;
}
```

---

# Response Example

```json id="6y0vgs"
{
   "message":"User not found",
   "status":404,
   "timestamp":"2026-05-09T12:00"
}
```

---

# Enterprise Importance

Helps:

* frontend teams
* API consumers
* debugging
* monitoring systems

---

# 9. Validation Exception Handling

VERY IMPORTANT.

---

# Example

```java id="vszjlwm"
public class UserDTO {

    @NotBlank
    private String name;
}
```

---

# If validation fails:

Spring throws:

```text id="7cngn7"
MethodArgumentNotValidException
```

---

# Handling

```java id="b0s6qt"
@ExceptionHandler(
    MethodArgumentNotValidException.class
)
public ResponseEntity<?> handleValidation(
        MethodArgumentNotValidException ex) {

    return ResponseEntity.badRequest()
            .body("Validation Failed");
}
```

---

# Real Project Usage

VERY common in:

* registration APIs
* request validation
* DTO validation

---

# 10. Spring Security Exception Handling

Critical for authentication systems.

---

# Types

| Exception               | Meaning             |
| ----------------------- | ------------------- |
| AuthenticationException | login failed        |
| AccessDeniedException   | unauthorized access |

---

# Example Flow

```text id="32h48d"
JWT invalid
     ↓
AuthenticationException
     ↓
401 Unauthorized
```

---

# Common Handlers

```text id="htzcx9"
AuthenticationEntryPoint
AccessDeniedHandler
```

---

# VERY IMPORTANT in Interviews

Especially for:

* JWT
* OAuth2
* RBAC

---

# 11. Transaction Exception Handling

EXTREMELY IMPORTANT.

---

# Example

```java id="bmlhqa"
@Transactional
public void transferMoney() {

    debit();

    credit();
}
```

Suppose:

* debit success
* credit fails

Need rollback.

---

# Spring Default Rule

Rollback occurs for:

* RuntimeException
* Error

NOT for checked exceptions.

---

# To Rollback Checked Exception

```java id="p6qvzf"
@Transactional(
   rollbackFor = Exception.class
)
```

---

# Interview Favorite Question

> Why checked exception may not rollback transaction?

Because Spring default rollback only for:

* unchecked exceptions

---

# 12. Async Exception Handling

Used with:

* @Async
* CompletableFuture

---

# Problem

Normal try-catch cannot catch async thread exceptions.

---

# Example

```java id="y7r1sh"
CompletableFuture
    .supplyAsync(() -> {

        return 10 / 0;

    })
    .exceptionally(ex -> {

        return 0;
    });
```

---

# Real Usage

Used in:

* async APIs
* notifications
* background jobs

---

# 13. Kafka Exception Handling

VERY IMPORTANT for microservices.

---

# Problem

Consumer processing failed.

---

# Handling Strategies

| Strategy      | Meaning             |
| ------------- | ------------------- |
| Retry         | process again       |
| DLQ           | move failed message |
| Ignore        | skip bad message    |
| Stop consumer | severe issue        |

---

# Dead Letter Queue (DLQ)

Failed messages stored separately.

Used for:

* debugging
* reprocessing

---

# Spring Kafka Support

```text id="4lqugm"
DefaultErrorHandler
DeadLetterPublishingRecoverer
```

---

# 14. Feign Client Exception Handling

Microservice-to-microservice communication.

---

# Problem

Remote service unavailable.

---

# Exceptions

```text id="hcr4mb"
FeignException
WebClientResponseException
HttpClientErrorException
```

---

# Handling

```java id="h4e4qb"
try {

    client.call();

}
catch(FeignException e) {

    logger.error("Service failed");
}
```

---

# Modern Approach

Use:

* fallback
* circuit breaker

---

# 15. Retry Exception Handling

Used for temporary failures.

---

# Example

```text id="vwjlwm"
Attempt 1 → fail
Attempt 2 → fail
Attempt 3 → success
```

---

# Tools

* Spring Retry
* Resilience4j

---

# Example

```java id="cwmp0r"
@Retryable(maxAttempts = 3)
public void callService() {
}
```

---

# 16. Circuit Breaker Exception Handling

VERY IMPORTANT for microservices.

---

# Problem

One failing service can crash entire system.

---

# Solution

Circuit Breaker.

---

# Flow

```text id="c81qj5"
Service failing repeatedly
        ↓
Circuit opens
        ↓
Further calls blocked
        ↓
Fallback response returned
```

---

# Common Tool

```text id="qj9ek1"
Resilience4j
```

---

# 17. Filter-Level Exception Handling

Used before controller execution.

---

# Example

```text id="uyl6m7"
JWT filter
CORS filter
Security filter
```

---

# Handling Needed Because

ControllerAdvice may NOT catch filter exceptions.

---

# Used For

* authentication
* request validation
* security

---

# 18. Reactive Exception Handling (WebFlux)

Used in reactive applications.

---

# Example

```java id="8wvnlz"
return Mono.just(data)
        .onErrorResume(ex ->
                Mono.just("Fallback"));
```

---

# Used In

* reactive microservices
* high scalability systems

---

# COMPLETE PROFESSIONAL ARCHITECTURE

```text id="b5s24v"
Controller
   ↓
Service
   ↓
Repository
   ↓
Database

Exception Occurs
   ↓
Custom Exception
   ↓
Global Handler
   ↓
Structured Response
   ↓
Logging + Monitoring
   ↓
Retry/Fallback if needed
```

---

# MOST IMPORTANT INTERVIEW UNDERSTANDING

# Spring Exception Handling Exists at Multiple Levels

| Level              | Mechanism             |
| ------------------ | --------------------- |
| Java Level         | try-catch             |
| Spring MVC Level   | @ExceptionHandler     |
| Application Level  | @ControllerAdvice     |
| Transaction Level  | rollback              |
| Security Level     | auth handlers         |
| Microservice Level | retry/circuit breaker |
| Messaging Level    | Kafka DLQ             |
| Reactive Level     | onErrorResume         |

---

# BEST PRACTICES

| Best Practice                      | Why                  |
| ---------------------------------- | -------------------- |
| Use custom exceptions              | clarity              |
| Use global handler                 | clean architecture   |
| Return structured responses        | API consistency      |
| Log exceptions properly            | debugging            |
| Avoid generic Exception everywhere | maintainability      |
| Use retry carefully                | avoid overload       |
| Use DLQ for Kafka                  | prevent message loss |

---

# MOST IMPORTANT REAL PROJECT INTERVIEW ANSWER

If interviewer asks:

> "How did you handle exceptions in your project?"

Professional answer:

> In our Spring Boot microservices architecture, we used centralized exception handling using `@RestControllerAdvice`. We created custom runtime exceptions for business scenarios and returned standardized API error responses with proper HTTP status codes. We logged all critical exceptions using SLF4J. For transactional operations we used `@Transactional` for rollback support. For inter-service communication we handled Feign/WebClient exceptions using retry and circuit breaker patterns with Resilience4j. Kafka consumer failures were handled using retry and Dead Letter Queues.

# How Exception Handling Works in Spring Data JPA?

VERY IMPORTANT FOR INTERVIEWS.

Spring Data JPA has MULTIPLE layers of exception handling.

---

# Architecture

```text id="pjlwm1"
Controller
   ↓
Service
   ↓
Repository (JPA)
   ↓
Hibernate
   ↓
JDBC
   ↓
Database
```

Exception can occur at ANY layer.

---

# Important Understanding

# Spring converts low-level exceptions into Spring exceptions.

This is called:

```text id="jlwmc2"
Exception Translation
```

VERY IMPORTANT interview term.

---

# Example

Database throws:

```text id="4jlwmf"
SQLException
```

Spring converts into:

```text id="8jlwmv"
DataAccessException
```

---

# WHY SPRING DOES THIS?

Because:
every database vendor has different exceptions.

Spring provides:

* unified exception hierarchy

---

# Example

Instead of handling:

```text id="jlwmx3"
OracleException
MySQLException
PostgresException
```

Spring gives:

```text id="bjlwm4"
DataAccessException
```

---

# 3. Common Spring Data JPA Exceptions

VERY IMPORTANT.

| Exception                         | Meaning                 |
| --------------------------------- | ----------------------- |
| DataIntegrityViolationException   | constraint violation    |
| EntityNotFoundException           | entity missing          |
| EmptyResultDataAccessException    | delete/find failed      |
| OptimisticLockingFailureException | concurrent update issue |
| TransactionSystemException        | transaction issue       |
| JpaSystemException                | generic JPA issue       |

---

# Example 1 – Duplicate Key

```java id="jlwmq5"
userRepository.save(user);
```

Suppose email unique constraint exists.

Duplicate email inserted.

---

# Database Throws

```text id="jlwmn6"
SQLIntegrityConstraintViolationException
```

---

# Spring Converts Into

```text id="jlwmw7"
DataIntegrityViolationException
```

---

# Internal Flow

```text id="8jlwmr"
Database Exception
      ↓
Hibernate Exception
      ↓
Spring Exception Translator
      ↓
DataAccessException
```

---

# 4. Why Spring Uses Runtime Exceptions in JPA?

VERY IMPORTANT.

All Spring Data exceptions are mostly:

```text id="wjlwm9"
Unchecked Exceptions
(RuntimeException)
```

---

# Why?

Because:
Spring philosophy says:

```text id="0jlwmk"
Database failures are usually non-recoverable
```

and should propagate automatically.

---

# HUGE BENEFIT

No boilerplate everywhere.

Without this:

```java id="njlwm1"
throws SQLException
```

everywhere.

---

# 5. How Rollback Works in Spring Data JPA?

Suppose:

```java id="sjlwm2"
@Transactional
public void saveUser() {

    repo.save(user);

    int x = 10 / 0;
}
```

---

# What Happens?

```text id="zjlwm3"
save() success
ArithmeticException occurs
transaction rollback
```

User NOT saved.

---

# Why?

Because:

```text id="8jlwm4"
ArithmeticException
      ↓
RuntimeException
```

Spring auto rollback.

---

# 6. Can We Use try-catch Inside Controller/Service Methods?

# YES.

Absolutely.

VERY COMMON in real projects.

But HOW you use it matters.

---

# Using try-catch in Controller

Possible.

Example:

```java id="4jlwm5"
@GetMapping("/{id}")
public ResponseEntity<?> getUser(
        @PathVariable int id) {

    try {

        User user = service.getUser(id);

        return ResponseEntity.ok(user);

    }
    catch(UserNotFoundException e) {

        return ResponseEntity
                .status(404)
                .body(e.getMessage());
    }
}
```

---

# Is This Recommended?

For small projects:
YES.

For enterprise projects:
Usually NO.

---

# Why Not Preferred?

Because:
same try-catch repeated in every controller.

---

# Better Approach

Use:

```text id="7jlwm6"
@RestControllerAdvice
```

---

# Modern Enterprise Approach

```text id="yjlwm7"
Controller
   ↓
Service
   ↓
Exception Thrown
   ↓
Global Exception Handler
```

Cleaner architecture.

---

# 7. Can We Use try-catch in Service Layer?

# YES.

Very common.

---

# Example

```java id="5jlwm8"
public User getUser(int id) {

    try {

        return repo.findById(id)
                .orElseThrow(() ->
                        new UserNotFoundException(
                                "User not found"));

    }
    catch(Exception e) {

        logger.error("DB failed", e);

        throw e;
    }
}
```

---

# VERY IMPORTANT

# NEVER silently swallow exception.

BAD:

```java id="2jlwm9"
catch(Exception e) {

}
```

---

# Why Dangerous?

* hidden production bugs
* transaction may commit
* debugging nightmare

---

# 8. MOST IMPORTANT TRANSACTION + TRY-CATCH ISSUE

Interview favorite question.

---

# Example

```java id="jjlwm0"
@Transactional
public void transfer() {

    try {

        debit();

        int x = 10 / 0;

        credit();

    }
    catch(Exception e) {

        System.out.println("Handled");
    }
}
```

---

# Question

Will rollback happen?

# NO.

---

# Why?

Because exception never reaches Spring transaction proxy.

Spring thinks:
method successful.

So transaction commits.

---

# CORRECT APPROACH

```java id="ljlwm1"
catch(Exception e) {

    logger.error("Error", e);

    throw e;
}
```

---

# IMPORTANT BEST PRACTICE

| Layer                 | Preferred Handling        |
| --------------------- | ------------------------- |
| Controller            | Global handler            |
| Service               | business handling/logging |
| Repository            | rarely try-catch          |
| Transactional methods | rethrow exception         |

---

# 9. Real Project Exception Strategy

Professional architecture:

```text id="pjlwm2"
Controller
   ↓
Service
   ↓
Repository
   ↓
Database Exception
   ↓
Spring Converts Exception
   ↓
Custom Exception
   ↓
Global Handler
   ↓
Structured API Response
```

---

# 10. Most Important Interview Understanding

# Spring Data JPA Exception Handling is Based on:

| Concept               | Purpose             |
| --------------------- | ------------------- |
| Exception Translation | unify DB exceptions |
| Runtime Exceptions    | cleaner code        |
| Transaction Rollback  | consistency         |
| Global Handling       | centralized APIs    |
| Custom Exceptions     | business clarity    |

# 1. Does Spring Convert ALL DB Exceptions into `DataAccessException`?

# YES — mostly correct.

But understand it properly at professional level.

Spring does NOT convert literally every possible DB exception into ONLY one exact exception object.

Instead:

```text id="f12yks"
Spring converts DB-specific exceptions
into a COMMON EXCEPTION HIERARCHY
whose parent is:

DataAccessException
```

---

# VERY IMPORTANT UNDERSTANDING

`DataAccessException` is:

```text id="f8fjlwm"
Parent Exception Class
```

Many Spring DB exceptions extend it.

---

# Hierarchy

```text id="6m4m0d"
DataAccessException
        |
------------------------------------------------
|                |              |              |
DataIntegrity   DuplicateKey   Optimistic     EmptyResult
ViolationEx     Exception      LockingEx      DataAccessEx
```

---

# Why Spring Does This?

Different databases throw different exceptions.

Example:

| Database   | Exception                                  |
| ---------- | ------------------------------------------ |
| MySQL      | MySQLIntegrityConstraintViolationException |
| Oracle     | OracleSQLException                         |
| PostgreSQL | PSQLException                              |

If Spring exposed all these:

* code becomes database dependent

---

# So Spring Creates Unified Abstraction

```text id="k5g0jlwm"
Any DB
   ↓
Hibernate/JDBC Exception
   ↓
Spring Exception Translator
   ↓
DataAccessException hierarchy
```

---

# Internal Mechanism Name

VERY IMPORTANT interview keyword:

```text id="ckzn4y"
Exception Translation
```

---

# Which Class Does This?

Usually:

```text id="8wjlwm"
PersistenceExceptionTranslationPostProcessor
```

and internally Spring ORM/JPA infrastructure.

---

# Example – Duplicate Email

Suppose:

```java id="5jlwm1"
@Column(unique = true)
private String email;
```

---

# Insert Duplicate

```java id="wjlwm2"
repo.save(user);
```

---

# Database Throws

Maybe:

```text id="6jlwm3"
SQLIntegrityConstraintViolationException
```

---

# Hibernate Converts

```text id="3jlwm4"
ConstraintViolationException
```

---

# Spring Converts

```text id="zjlwm5"
DataIntegrityViolationException
```

---

# VERY IMPORTANT

`DataIntegrityViolationException` is:

```text id="4jlwm6"
extends DataAccessException
```

---

# So In Interview You Can Say:

> Spring converts vendor-specific database exceptions into its unified unchecked exception hierarchy called DataAccessException using exception translation.

That is the PROFESSIONAL answer.

---

# IMPORTANT POINT

# All these are Runtime Exceptions

```text id="djlwm7"
DataAccessException
      ↓
RuntimeException
```

---

# Why Runtime?

Spring philosophy:

```text id="7jlwm8"
DB failures are generally non-recoverable
and should propagate automatically.
```

This avoids:

```java id="2jlwm9"
throws SQLException
```

everywhere.

---

# 2. ResponseStatusException Deep Dive

VERY IMPORTANT Spring Boot topic.

---

# What is `ResponseStatusException`?

Spring Boot built-in exception used to:

* throw HTTP status directly
* return custom API error response quickly

---

# Example

```java id="1jlwm0"
throw new ResponseStatusException(
        HttpStatus.NOT_FOUND,
        "User not found"
);
```

---

# Output

```json id="qjlwm1"
{
  "timestamp":"2026-05-09",
  "status":404,
  "error":"Not Found",
  "message":"User not found"
}
```

---

# IMPORTANT UNDERSTANDING

# YES — it is used with `throw`

Because:

```text id="4jlwm2"
ResponseStatusException
       ↓
Exception Object
```

and exceptions are raised using:

```text id="5jlwm3"
throw
```

---

# Full Example

```java id="6jlwm4"
@GetMapping("/{id}")
public User getUser(
        @PathVariable int id) {

    return repo.findById(id)
            .orElseThrow(() ->
                    new ResponseStatusException(
                            HttpStatus.NOT_FOUND,
                            "User not found"
                    ));
}
```

---

# What Happens Internally?

```text id="zjlwm5"
Exception Thrown
      ↓
Spring Boot catches automatically
      ↓
Converts into HTTP response
```

---

# HUGE ADVANTAGE

Quick and simple.

No need:

* custom exception class
* global handler

---

# But Enterprise Projects Usually Prefer:

```text id="9jlwm6"
Custom Exception
        +
@RestControllerAdvice
```

Why?

Because:

* cleaner architecture
* reusable
* consistent responses
* separation of concerns

---

# ResponseStatusException vs Custom Exception

| Feature              | ResponseStatusException | Custom Exception |
| -------------------- | ----------------------- | ---------------- |
| Quick                | YES                     | NO               |
| Reusable             | Less                    | More             |
| Enterprise preferred | Less                    | YES              |
| HTTP tightly coupled | YES                     | NO               |
| Better architecture  | NO                      | YES              |

---

# 3. Can `ResponseStatusException` Be Used Within Same Class Only?

# NO.

Very important misunderstanding.

You can throw it:

* anywhere in Spring-managed flow

---

# Examples

| Layer         | Can Use?   |
| ------------- | ---------- |
| Controller    | YES        |
| Service       | YES        |
| Repository    | YES (rare) |
| Utility class | YES        |

---

# Example in Service Layer

```java id="8jlwm7"
@Service
public class UserService {

    public User getUser(int id) {

        return repo.findById(id)
                .orElseThrow(() ->
                        new ResponseStatusException(
                                HttpStatus.NOT_FOUND,
                                "User not found"
                        ));
    }
}
```

---

# Flow

```text id="djlwm8"
Service throws exception
      ↓
Controller receives
      ↓
Spring catches globally
      ↓
HTTP response returned
```

---

# VERY IMPORTANT

It does NOT matter:

* same class
* different class

What matters is:

```text id="4jlwm9"
Exception propagates through Spring MVC flow
```

---

# 4. Is `ResponseStatusException` a Custom Exception?

# NO.

It is:

```text id="xjlwm0"
Spring-provided built-in exception
```

Package:

```java id="3jlwm1"
org.springframework.web.server
.ResponseStatusException
```

---

# 5. Difference Between `throw new RuntimeException()` vs `ResponseStatusException`

---

# RuntimeException

```java id="7jlwm2"
throw new RuntimeException(
        "User not found");
```

Response:

```text id="zjlwm3"
500 Internal Server Error
```

because Spring doesn't know intended status.

---

# ResponseStatusException

```java id="9jlwm4"
throw new ResponseStatusException(
        HttpStatus.NOT_FOUND,
        "User not found"
);
```

Response:

```text id="2jlwm5"
404 Not Found
```

---

# So Main Benefit

```text id="1jlwm6"
Direct HTTP status control
```

---

# 6. Real Enterprise Recommendation

---

# Small Projects

Often use:

```text id="vjlwm7"
ResponseStatusException
```

---

# Large Enterprise Projects

Prefer:

```text id="pjlwm8"
Custom Exception
      +
Global Exception Handler
```

Example:

```java id="6jlwm9"
throw new UserNotFoundException();
```

then:

```java id="3jlwm0"
@RestControllerAdvice
```

handles all responses centrally.

---

# PROFESSIONAL INTERVIEW ANSWERS

---

# Q1: Does Spring convert all DB exceptions into DataAccessException?

> Spring uses exception translation to convert vendor-specific database exceptions into Spring’s unified unchecked exception hierarchy whose root class is DataAccessException. Specific exceptions like DataIntegrityViolationException or DuplicateKeyException extend DataAccessException.

---

# Q2: What is ResponseStatusException?

> ResponseStatusException is a Spring Boot built-in runtime exception used to directly return HTTP status codes and error messages from APIs. It is thrown using the throw keyword and Spring automatically converts it into an HTTP response.

---

# Q3: Can ResponseStatusException be used only inside same class?

> No. It can be thrown from controller, service, or any Spring-managed layer. As long as the exception propagates through Spring MVC flow, Spring converts it into the corresponding HTTP response automatically.


# Q1: What happens if checked exception propagated but not handled?

> Checked exceptions must either be handled or propagated further using throws. If the final caller also does not handle it, the compiler gives an error unless the method itself declares throws. If it eventually reaches JVM unhandled, JVM default exception handler terminates the application.

---

# Q2: How exception handling works in Spring Data JPA?

> Spring Data JPA uses exception translation. Low-level database exceptions like SQLException are converted into Spring’s unified DataAccessException hierarchy. Most Spring Data exceptions are runtime exceptions, which simplifies transaction rollback and reduces boilerplate code.

---

# Q3: Can we use try-catch inside controller/service?

> Yes, try-catch can be used inside controller or service methods, especially for logging, fallback logic, or partial recovery. However, in enterprise Spring Boot applications, centralized exception handling using @RestControllerAdvice is preferred to avoid repetitive code and maintain consistent API responses.

---
# Transaction + Exception Handling in Spring Boot – Complete Deep Dive

This is one of the MOST IMPORTANT interview topics for:

* Spring Boot
* Microservices
* Banking systems
* E-commerce systems

Interviewers LOVE this because it checks:

* transaction understanding
* exception understanding
* Spring internals
* real project experience

---

# 1. First Understand the Problem

Suppose:

```text id="6ffn4r"
Transfer ₹1000

Account A → deduct
Account B → credit
```

---

# Scenario 1

```text id="g99k2t"
Debit success
Credit failed
```

Without transaction:

```text id="k3h69g"
A = money deducted
B = money not credited
```

System becomes inconsistent.

---

# Solution → TRANSACTION

Transaction ensures:

```text id="lw9zhs"
ALL SUCCESS
OR
ALL FAIL
```

---

# 2. What is Transaction?

A transaction is a group of operations executed as a single unit of work.

---

# ACID Properties

Very important interview topic.

| Property    | Meaning                  |
| ----------- | ------------------------ |
| Atomicity   | all or nothing           |
| Consistency | valid state maintained   |
| Isolation   | concurrent safety        |
| Durability  | committed data permanent |

---

# 3. Spring Transaction Management

Using:

```java id="r9jlwm"
@Transactional
```

---

# Example

```java id="5jk7yq"
@Transactional
public void transfer() {

    debit();

    credit();
}
```

---

# Internal Flow

```text id="yef6sd"
Transaction Starts
       ↓
debit()
       ↓
credit()
       ↓
All success?
   YES → COMMIT
   NO  → ROLLBACK
```

---

# MOST IMPORTANT PART

# Transaction behavior depends on EXCEPTION TYPE.

This is where interviews become deep.

---

# 4. Default Spring Rollback Rules

Spring automatically rolls back ONLY for:

| Exception Type    | Rollback? |
| ----------------- | --------- |
| RuntimeException  | YES       |
| Error             | YES       |
| Checked Exception | NO        |

---

# THIS IS THE MOST IMPORTANT RULE

```text id="h3brvp"
Unchecked Exception
       ↓
Rollback happens automatically

Checked Exception
       ↓
Rollback does NOT happen automatically
```

---

# 5. Scenario 1 – Runtime Exception

VERY IMPORTANT.

---

# Example

```java id="ap6cy9"
@Transactional
public void transfer() {

    debit();

    int x = 10 / 0;

    credit();
}
```

---

# What Happens?

```text id="u7tcv7"
debit() success
ArithmeticException occurs
transaction rollback
```

Final result:

```text id="4s7x9d"
Debit also undone
Credit not executed
```

---

# Why?

Because:

```text id="cbjlwm"
ArithmeticException
      ↓
RuntimeException
```

Spring auto rollback.

---

# 6. Scenario 2 – Checked Exception

VERY IMPORTANT INTERVIEW QUESTION.

---

# Example

```java id="6sg7hy"
@Transactional
public void transfer()
        throws IOException {

    debit();

    throw new IOException();

    // credit();
}
```

---

# What Happens?

SURPRISINGLY:

```text id="r6qjlwm"
Debit COMMITTED
No rollback
```

---

# WHY?

Because:

```text id="9fjlwm"
IOException
    ↓
Checked Exception
```

Spring default rollback does NOT include checked exceptions.

---

# This Confuses Many Developers

Interviewers ask this frequently.

---

# 7. How to Rollback Checked Exception?

Use:

```java id="1mhhmd"
@Transactional(
    rollbackFor = Exception.class
)
```

---

# Example

```java id="vm0o1f"
@Transactional(
    rollbackFor = IOException.class
)
public void transfer()
        throws IOException {

    debit();

    throw new IOException();
}
```

Now rollback occurs.

---

# Internal Logic

```text id="9mjlwm"
Spring Transaction Manager checks:

Is exception RuntimeException?
     YES → rollback

Else check rollbackFor list
     YES → rollback
```

---

# 8. Scenario 3 – Exception Caught Inside Method

VERY VERY IMPORTANT.

---

# Example

```java id="w0v3m7"
@Transactional
public void transfer() {

    try {

        debit();

        int x = 10 / 0;

        credit();

    }
    catch(Exception e) {

        System.out.println("Handled");
    }
}
```

---

# Interview Question

Will rollback happen?

---

# ANSWER

# NO rollback.

---

# Why?

Because exception already handled.

Spring thinks:

```text id="cazjlwm"
Method completed successfully
```

So transaction commits.

---

# Final Result

```text id="vxjlwm"
Debit committed
Credit skipped
```

Dangerous inconsistent state.

---

# MOST IMPORTANT UNDERSTANDING

# Spring rollback happens only when exception reaches transaction proxy.

---

# 9. Scenario 4 – Re-Throwing Exception

Correct approach.

---

# Example

```java id="3csdlt"
@Transactional
public void transfer() {

    try {

        debit();

        int x = 10 / 0;

        credit();

    }
    catch(Exception e) {

        throw e;
    }
}
```

---

# What Happens?

Now exception reaches Spring proxy.

Rollback occurs.

---

# Flow

```text id="ewjlwm"
Exception occurs
      ↓
catch block
      ↓
rethrow exception
      ↓
Spring sees exception
      ↓
rollback
```

---

# 10. Scenario 5 – Checked Exception Wrapped into RuntimeException

Very common in real projects.

---

# Example

```java id="67o5sv"
@Transactional
public void transfer() {

    try {

        riskyOperation();

    }
    catch(IOException e) {

        throw new RuntimeException(e);
    }
}
```

---

# Result

Rollback occurs.

Because:
outer exception is RuntimeException.

---

# 11. Scenario 6 – Multiple DB Operations

---

# Example

```java id="7axhmd"
@Transactional
public void placeOrder() {

    saveOrder();

    updateInventory();

    processPayment();

    createInvoice();
}
```

---

# Suppose

```text id="jlwm7n"
saveOrder success
updateInventory success
processPayment fails
```

Result:

```text id="mjlwm7"
ALL rollback
```

---

# Why Important?

Critical for:

* banking
* e-commerce
* payments
* inventory systems

---

# 12. Scenario 7 – Nested Transactions

Advanced topic.

---

# Example

```java id="9km7av"
@Transactional
public void outer() {

    inner();
}
```

---

# Propagation Matters

Spring transaction propagation controls:

* how nested transactions behave

---

# REQUIRED (Default)

```text id="jlwmmf"
Use same transaction
```

---

# REQUIRES_NEW

```text id="zjlwm7"
Create separate transaction
```

---

# Example

```java id="4k7gxf"
@Transactional
public void outer() {

    saveA();

    inner();

    throw new RuntimeException();
}

@Transactional(
 propagation = Propagation.REQUIRES_NEW
)
public void inner() {

    saveB();
}
```

---

# Result

| Operation | Result    |
| --------- | --------- |
| saveA     | rollback  |
| saveB     | committed |

---

# Why?

Because inner transaction independent.

---

# 13. Scenario 8 – Exception in Finally Block

VERY TRICKY INTERVIEW QUESTION.

---

# Example

```java id="jlwmv8"
@Transactional
public void test() {

    try {

        debit();

    }
    finally {

        throw new RuntimeException();
    }
}
```

---

# Result

Rollback occurs.

Why?

Exception leaves method.

---

# 14. Scenario 9 – Self Invocation Problem

VERY ADVANCED SPRING INTERVIEW QUESTION.

---

# Example

```java id="jlwmc9"
@Service
public class TestService {

    public void method1() {

        method2();
    }

    @Transactional
    public void method2() {

    }
}
```

---

# Question

Will transaction work?

---

# ANSWER

# NO.

---

# Why?

Because Spring transactions use:

* proxy mechanism

Internal self-call bypasses proxy.

---

# VERY IMPORTANT CONCEPT

```text id="hjlwm9"
External Call → proxy → transaction works

Internal Call → direct call → no transaction
```

---

# 15. Scenario 10 – Async + Transaction

Very advanced.

---

# Example

```java id="jlwmf0"
@Async
@Transactional
public void process() {

}
```

---

# Problem

Transaction is thread-bound.

Async creates new thread.

Can create unexpected behavior.

---

# Interview Point

Transactions generally do NOT propagate automatically across threads.

---

# 16. Transaction + Kafka Scenario

VERY IMPORTANT in microservices.

---

# Example

```text id="jlwmza"
DB save success
Kafka publish failed
```

Problem:
inconsistent system.

---

# Solutions

| Solution           | Purpose           |
| ------------------ | ----------------- |
| Outbox Pattern     | consistency       |
| Kafka Transactions | atomic messaging  |
| Retry              | temporary failure |

---

# 17. Transaction Proxy Internal Flow

VERY IMPORTANT INTERNALS.

---

# Spring Uses AOP Proxy

```text id="jlwmxy"
Client
   ↓
Spring Proxy
   ↓
Transaction Start
   ↓
Business Method
   ↓
Exception?
  YES → rollback
  NO  → commit
```

---

# MOST IMPORTANT UNDERSTANDING

# Transaction rollback decision happens AFTER method exits.

---

# 18. What Happens Internally?

---

# Step-by-Step

```text id="zjlwmx"
1. Proxy intercepts method
2. Transaction starts
3. Method executes
4. Exception checked
5. Rollback rules checked
6. Commit or rollback
```

---

# 19. Common Real Project Mistakes

| Mistake                              | Problem             |
| ------------------------------------ | ------------------- |
| Catching exception silently          | transaction commits |
| Using checked exceptions unknowingly | no rollback         |
| Huge transactional methods           | performance issues  |
| External API inside transaction      | long DB locks       |
| Self invocation                      | transaction ignored |

---

# 20. Best Practices

| Best Practice                              | Why                       |
| ------------------------------------------ | ------------------------- |
| Use RuntimeException for business failures | automatic rollback        |
| Keep transaction small                     | better performance        |
| Avoid remote API inside transaction        | prevent long locks        |
| Use custom exceptions                      | clean architecture        |
| Log before rethrowing                      | debugging                 |
| Understand propagation                     | nested transaction safety |

---

# 21. MOST IMPORTANT INTERVIEW QUESTIONS

---

## Basic

1. What is transaction?
2. What is ACID?
3. What does @Transactional do?

---

## Intermediate

4. Which exceptions trigger rollback?
5. Why checked exceptions don't rollback?
6. What happens if exception caught inside method?

---

## Advanced

7. What is propagation?
8. What is REQUIRES_NEW?
9. Why self-invocation breaks transaction?
10. How Spring transaction proxy works?
11. Transaction + async issue?
12. Transaction consistency in microservices?

---

# 22. PROFESSIONAL INTERVIEW ANSWER

If interviewer asks:

> "Explain transaction and exception scenarios."

Strong answer:

> In Spring Boot, transaction rollback behavior depends on exception type and whether the exception reaches the transaction proxy. By default, Spring rolls back only for RuntimeException and Error. Checked exceptions do not trigger rollback unless explicitly configured using rollbackFor. If exceptions are caught and swallowed inside the transactional method, rollback does not occur because Spring assumes method completed successfully. Spring manages transactions using AOP proxies, so transaction behavior can also be affected by self-invocation, propagation settings, async execution, and nested transactions like REQUIRES_NEW.

---
# Summary

# Exception Handling + Spring Boot + Transaction Summary

# 1. What is Exception Handling?

Exception Handling is a mechanism to handle runtime problems gracefully without abruptly terminating the application.

Used for:

* stability
* debugging
* transaction consistency
* proper API responses
* production reliability

---

# 2. Exception Hierarchy

```text id="bqoltc"
Object
   ↓
Throwable
   ↓
--------------------------------
|                              |
Error                      Exception
                                 |
                     ---------------------
                     |                   |
            Checked Exception     RuntimeException
                                        |
                                Unchecked Exception
```

---

# 3. Error vs Exception

| Error                     | Exception                     |
| ------------------------- | ----------------------------- |
| Serious JVM/system issue  | Recoverable application issue |
| Usually cannot recover    | Can recover                   |
| Example: OutOfMemoryError | Example: IOException          |

---

# 4. Checked vs Unchecked Exception

| Checked Exception        | Unchecked Exception           |
| ------------------------ | ----------------------------- |
| Compile-time checked     | Runtime checked               |
| Must handle or propagate | Optional handling             |
| Example: IOException     | Example: NullPointerException |

---

# Rule

```text id="pn4tb0"
Checked Exception
      ↓
Mandatory:
try-catch
OR
throws
```

---

# 5. throw vs throws

| throw                            | throws                         |
| -------------------------------- | ------------------------------ |
| Actually throws exception object | Declares exception possibility |
| Used inside method               | Used in method signature       |
| Creates exception flow           | Transfers responsibility       |

---

# Examples

---

## Only throw

```java id="i1mjlwm"
throw new RuntimeException("Error");
```

---

## Only throws

```java id="kwyjlwm"
public void test() throws IOException {
}
```

---

## Both together

```java id="ukn5i4"
public void test() throws IOException {

    throw new IOException();
}
```

---

# 6. What Happens if Checked Exception Not Handled?

Example:

```java id="njlwm2"
public void test() throws IOException {
}
```

Caller:

```java id="4jlwm3"
test();
```

Result:

```text id="9jlwm4"
Compile Error
Unhandled exception
```

---

# Why?

Because checked exceptions must:

* handle
  OR
* propagate further

---

# 7. Ways to Handle Exceptions in Java

```text id="jlwm5x"
1. JVM default handling
2. try-catch
3. multiple catch
4. nested try-catch
5. finally
6. throw
7. throws
8. propagation
9. custom exception
10. try-with-resources
```

---

# 8. finally Block

Used for cleanup.

Example:

* DB close
* file close
* socket close

---

# finally Executes In

| Scenario             | Executes? |
| -------------------- | --------- |
| Exception occurs     | YES       |
| Exception not occurs | YES       |
| return statement     | YES       |
| System.exit(0)       | NO        |

---

# 9. Custom Exceptions

Used for business-specific failures.

Example:

```java id="2jlwm6"
class UserNotFoundException
        extends RuntimeException {
}
```

Benefits:

* readability
* clean APIs
* easier debugging

---

# 10. Try-With-Resources

Modern resource handling.

```java id="8jlwm7"
try(BufferedReader br =
        new BufferedReader(...)) {

}
```

Automatically closes resources.

Uses:

```text id="1jlwm8"
AutoCloseable
```

---

# 11. Spring Boot Exception Handling Levels

```text id="vjlwm9"
1. try-catch
2. throws propagation
3. custom exceptions
4. ResponseStatusException
5. @ExceptionHandler
6. @ControllerAdvice
7. @RestControllerAdvice
8. validation handling
9. transaction handling
10. async handling
11. Kafka handling
12. retry/fallback handling
13. circuit breaker handling
```

---

# 12. @ExceptionHandler

Handles exception for specific controller.

```java id="6jlwm0"
@ExceptionHandler(UserNotFoundException.class)
```

---

# 13. @ControllerAdvice

Global exception handling.

Used for:

* centralized handling
* clean architecture
* reusable logic

---

# 14. @RestControllerAdvice

Combination of:

```text id="jlwm1a"
@ControllerAdvice
+
@ResponseBody
```

Mostly used in REST APIs/microservices.

---

# 15. ResponseStatusException

Spring Boot built-in exception.

---

# Example

```java id="jlwm2b"
throw new ResponseStatusException(
        HttpStatus.NOT_FOUND,
        "User not found"
);
```

---

# Purpose

Directly return:

* HTTP status
* message

---

# Important Points

| Point                          | Answer |
| ------------------------------ | ------ |
| Uses throw keyword?            | YES    |
| Same class only?               | NO     |
| Can use in service/controller? | YES    |

---

# RuntimeException vs ResponseStatusException

| RuntimeException       | ResponseStatusException    |
| ---------------------- | -------------------------- |
| Usually returns 500    | Returns custom HTTP status |
| No direct HTTP control | Direct HTTP control        |

---

# 16. Spring Data JPA Exception Handling

VERY IMPORTANT.

---

# Internal Flow

```text id="jlwm3c"
Database Exception
      ↓
Hibernate/JPA Exception
      ↓
Spring Exception Translation
      ↓
DataAccessException hierarchy
```

---

# Important Concept

```text id="hjlwm4"
Exception Translation
```

---

# Why Needed?

Different databases throw different exceptions.

Spring converts them into:

* unified Spring exception hierarchy

---

# Parent Exception

```text id="yjlwm5"
DataAccessException
```

---

# Common Exceptions

| Exception                         | Meaning                 |
| --------------------------------- | ----------------------- |
| DataIntegrityViolationException   | constraint violation    |
| DuplicateKeyException             | duplicate key           |
| EmptyResultDataAccessException    | no data found           |
| OptimisticLockingFailureException | concurrent update issue |

---

# Important Point

Most Spring Data JPA exceptions are:

```text id="8jlwm6"
Unchecked Exceptions
(RuntimeException)
```

---

# 17. Transaction Management

Using:

```java id="vjlwm7"
@Transactional
```

---

# Purpose

```text id="qjlwm8"
ALL SUCCESS
OR
ALL FAIL
```

---

# ACID Properties

| Property    | Meaning           |
| ----------- | ----------------- |
| Atomicity   | all or nothing    |
| Consistency | valid state       |
| Isolation   | concurrent safety |
| Durability  | permanent commit  |

---

# 18. MOST IMPORTANT RULE

# Spring Rollback Rules

| Exception Type    | Rollback? |
| ----------------- | --------- |
| RuntimeException  | YES       |
| Error             | YES       |
| Checked Exception | NO        |

---

# Example – Runtime Exception

```java id="1jlwm9"
@Transactional
public void test() {

    save();

    int x = 10 / 0;
}
```

Result:
Rollback occurs.

---

# Example – Checked Exception

```java id="5jlwm0"
@Transactional
public void test()
        throws IOException {

    save();

    throw new IOException();
}
```

Result:
NO rollback by default.

---

# 19. rollbackFor

Used for checked exceptions.

```java id="jlwm1d"
@Transactional(
 rollbackFor = Exception.class
)
```

---

# 20. VERY IMPORTANT try-catch + Transaction Scenario

Example:

```java id="jlwm2e"
@Transactional
public void transfer() {

    try {

        debit();

        int x = 10 / 0;

    }
    catch(Exception e) {

    }
}
```

---

# Result

NO rollback.

Why?

Because exception swallowed.

Spring thinks:
method successful.

---

# Correct Approach

```java id="jlwm3f"
catch(Exception e) {

    throw e;
}
```

---

# 21. Transaction Proxy Flow

Spring transactions use:

* AOP proxy

---

# Internal Flow

```text id="jlwm4g"
Client
   ↓
Spring Proxy
   ↓
Transaction Start
   ↓
Business Method
   ↓
Exception?
 YES → rollback
 NO  → commit
```

---

# 22. Self Invocation Problem

Example:

```java id="jlwm5h"
method1() {
   method2();
}

@Transactional
method2() {
}
```

Transaction does NOT work.

Why?

Internal method call bypasses Spring proxy.

---

# 23. Retry + Circuit Breaker

Used in microservices.

---

# Retry

```text id="jlwm6i"
Temporary failure
      ↓
Retry request
```

---

# Circuit Breaker

```text id="jlwm7j"
Repeated failure
      ↓
Circuit opens
      ↓
Further calls blocked
```

Common Tool:

```text id="jlwm8k"
Resilience4j
```

---

# 24. Kafka Exception Handling

Strategies:

| Strategy      | Purpose               |
| ------------- | --------------------- |
| Retry         | reprocess             |
| DLQ           | store failed messages |
| Ignore        | skip                  |
| Stop consumer | severe failure        |

---

# 25. Can We Use try-catch in Controller/Service?

# YES.

But enterprise applications prefer:

* global exception handling

---

# Best Practice

| Layer      | Preferred Handling        |
| ---------- | ------------------------- |
| Controller | @RestControllerAdvice     |
| Service    | business handling/logging |
| Repository | minimal try-catch         |

---

# 26. Professional Exception Architecture

```text id="jlwm9l"
Controller
   ↓
Service
   ↓
Repository
   ↓
Database

Exception Occurs
   ↓
Spring Exception Translation
   ↓
Custom Exception
   ↓
Global Exception Handler
   ↓
Structured API Response
   ↓
Logging + Monitoring
```

---

# 27. MOST IMPORTANT INTERVIEW POINTS

| Topic                    | Key Understanding                |
| ------------------------ | -------------------------------- |
| Checked Exception        | must handle/propagate            |
| Runtime Exception        | optional handling                |
| throw                    | create exception                 |
| throws                   | propagate responsibility         |
| Spring rollback          | only RuntimeException by default |
| try-catch in transaction | may prevent rollback             |
| Spring Data JPA          | exception translation            |
| ResponseStatusException  | direct HTTP status handling      |
| @RestControllerAdvice    | centralized handling             |
| Self invocation          | bypasses transaction proxy       |

---

# 28. Senior-Level Interview Answer

> In Spring Boot applications, exception handling is implemented at multiple layers. Core Java uses try-catch, throw, throws, propagation, and custom exceptions. Spring Boot adds centralized exception handling using @RestControllerAdvice and exception translation in Spring Data JPA through the DataAccessException hierarchy. Transaction rollback behavior depends on exception type, where RuntimeException triggers rollback by default. In microservices architecture, advanced handling includes retry, circuit breaker, fallback, Kafka DLQ, and structured API error responses.

