## Java Exception Handling — Top 50 Interview Questions with Professional Answers

Exception Handling means **handling runtime problems gracefully** so that the program does not crash suddenly and can give a proper response, log the issue, or recover.

Example:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Without exception handling, the program stops abruptly. With exception handling, we can control the failure.

---

# 1. What is an exception in Java?

An **exception** is an unwanted or unexpected event that occurs during program execution and disrupts the normal flow of the program.

Example:

```java
int result = 10 / 0; // ArithmeticException
```

Here, division by zero causes an exception.

Common exceptions:

```java
NullPointerException
ArithmeticException
ArrayIndexOutOfBoundsException
NumberFormatException
IOException
SQLException
```

---

# 2. What is exception handling?

Exception handling is a mechanism to handle runtime errors using:

```java
try
catch
finally
throw
throws
try-with-resources
```

Example:

```java
try {
    String name = null;
    System.out.println(name.length());
} catch (NullPointerException e) {
    System.out.println("Name cannot be null");
}
```

---

# 3. Why do we need exception handling?

We need exception handling because:

1. It prevents abnormal program termination.
2. It helps provide meaningful error messages.
3. It separates error-handling code from business logic.
4. It helps with logging and debugging.
5. It allows recovery from errors.

Example:

```java
try {
    int age = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    System.out.println("Invalid age format");
}
```

---

# 4. What is the hierarchy of exceptions in Java?

Java exception hierarchy starts from `Throwable`.

```text
Object
 └── Throwable
      ├── Error
      └── Exception
           ├── Checked Exceptions
           └── RuntimeException
                └── Unchecked Exceptions
```

Example:

```java
Throwable
Exception
RuntimeException
NullPointerException
IOException
SQLException
Error
OutOfMemoryError
StackOverflowError
```

Important point:

`Exception` is usually recoverable.
`Error` usually represents serious JVM-level problems.

---

# 5. What is the difference between Error and Exception?

| Error                        | Exception                                         |
| ---------------------------- | ------------------------------------------------- |
| Serious problem              | Program-level problem                             |
| Usually not recoverable      | Usually recoverable                               |
| Caused by JVM/system         | Caused by application logic or external resources |
| Should not usually be caught | Can be caught and handled                         |

Example of Error:

```java
StackOverflowError
OutOfMemoryError
```

Example of Exception:

```java
IOException
SQLException
NullPointerException
```

---

# 6. What are checked exceptions?

Checked exceptions are exceptions checked at compile time.

The compiler forces you to either:

1. Handle them using `try-catch`
2. Declare them using `throws`

Example:

```java
import java.io.FileReader;
import java.io.IOException;

public class Test {
    public static void main(String[] args) {
        try {
            FileReader reader = new FileReader("test.txt");
        } catch (IOException e) {
            System.out.println("File not found");
        }
    }
}
```

Common checked exceptions:

```java
IOException
SQLException
ClassNotFoundException
FileNotFoundException
InterruptedException
```

---

# 7. What are unchecked exceptions?

Unchecked exceptions are exceptions checked at runtime, not compile time.

They usually occur due to programming mistakes.

Example:

```java
String name = null;
System.out.println(name.length()); // NullPointerException
```

Common unchecked exceptions:

```java
NullPointerException
ArithmeticException
ArrayIndexOutOfBoundsException
NumberFormatException
IllegalArgumentException
IllegalStateException
```

---

# 8. Difference between checked and unchecked exceptions?

| Checked Exception           | Unchecked Exception          |
| --------------------------- | ---------------------------- |
| Checked at compile time     | Checked at runtime           |
| Must be handled or declared | Not mandatory to handle      |
| Usually external problems   | Usually programming mistakes |
| Extends `Exception`         | Extends `RuntimeException`   |

Example checked exception:

```java
FileReader reader = new FileReader("abc.txt");
```

Example unchecked exception:

```java
int result = 10 / 0;
```

---

# 9. What is `try` block?

The `try` block contains risky code that may throw an exception.

Example:

```java
try {
    int result = 10 / 0;
}
```

But `try` alone is not valid. It must be followed by `catch`, `finally`, or both.

Valid:

```java
try {
    // risky code
} catch (Exception e) {
    // handling
}
```

---

# 10. What is `catch` block?

The `catch` block handles the exception thrown from the `try` block.

Example:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Only the matching catch block gets executed.

---

# 11. Can we have multiple catch blocks?

Yes.

Example:

```java
try {
    String value = null;
    System.out.println(value.length());
} catch (ArithmeticException e) {
    System.out.println("Arithmetic problem");
} catch (NullPointerException e) {
    System.out.println("Null value problem");
} catch (Exception e) {
    System.out.println("Generic exception");
}
```

Important: Child exception should come before parent exception.

Correct:

```java
catch (NullPointerException e) {}
catch (Exception e) {}
```

Wrong:

```java
catch (Exception e) {}
catch (NullPointerException e) {} // unreachable code
```

---

# 12. What is `finally` block?

The `finally` block is used for cleanup code.

It usually executes whether exception occurs or not.

Example:

```java
try {
    int result = 10 / 2;
} catch (ArithmeticException e) {
    System.out.println("Exception occurred");
} finally {
    System.out.println("Cleanup code executed");
}
```

Common use cases:

```java
Closing file
Closing database connection
Releasing resources
Unlocking locks
```

---

# 13. Is `finally` always executed?

Usually yes, but not always.

`finally` may not execute in these cases:

1. `System.exit()` is called.
2. JVM crashes.
3. Machine shuts down.
4. Infinite loop/deadlock before finally.
5. Process is killed.

Example:

```java
try {
    System.out.println("Try block");
    System.exit(0);
} finally {
    System.out.println("Finally block"); // Will not execute
}
```

---

# 14. Difference between `final`, `finally`, and `finalize()`?

| Keyword/Method | Meaning                                |
| -------------- | -------------------------------------- |
| `final`        | Used to restrict modification          |
| `finally`      | Used for cleanup after try-catch       |
| `finalize()`   | Old GC-related method, not recommended |

Example of `final`:

```java
final int age = 25;
// age = 30; // not allowed
```

Example of `finally`:

```java
try {
    System.out.println("try");
} finally {
    System.out.println("cleanup");
}
```

`finalize()` is deprecated and should not be used in modern Java.

---

# 15. What is `throw` keyword?

`throw` is used to explicitly throw an exception.

Example:

```java
public void validateAge(int age) {
    if (age < 18) {
        throw new IllegalArgumentException("Age must be 18 or above");
    }
}
```

`throw` is used inside the method body.

---

# 16. What is `throws` keyword?

`throws` is used in method declaration to tell the caller that this method may throw an exception.

Example:

```java
public void readFile() throws IOException {
    FileReader reader = new FileReader("test.txt");
}
```

`throws` does not handle the exception.
It only declares the possibility of exception.

---

# 17. Difference between `throw` and `throws`?

| `throw`                            | `throws`                          |
| ---------------------------------- | --------------------------------- |
| Used to throw exception explicitly | Used to declare exception         |
| Used inside method body            | Used in method signature          |
| Throws one exception object        | Can declare multiple exceptions   |
| Followed by exception object       | Followed by exception class names |

Example:

```java
throw new RuntimeException("Something went wrong");
```

```java
public void test() throws IOException, SQLException {
}
```

---

# 18. Can we use try without catch?

Yes, but it must have `finally`.

Example:

```java
try {
    System.out.println("Risky code");
} finally {
    System.out.println("Cleanup");
}
```

Also, with try-with-resources, catch is optional:

```java
try (FileReader reader = new FileReader("test.txt")) {
    System.out.println("Reading file");
}
```

But if checked exception is thrown, method must handle or declare it.

---

# 19. Can we write catch without try?

No.

This is invalid:

```java
catch (Exception e) {
    System.out.println(e.getMessage());
}
```

`catch` must always be associated with a `try`.

---

# 20. What happens if an exception is not handled?

If an exception is not handled, it propagates up the call stack.

If no method handles it, JVM terminates the program and prints the stack trace.

Example:

```java
public class Test {
    public static void main(String[] args) {
        test();
    }

    static void test() {
        int result = 10 / 0;
    }
}
```

Output:

```text
Exception in thread "main" java.lang.ArithmeticException: / by zero
```

---

# 21. What is exception propagation?

Exception propagation means an exception moves from the current method to the caller method until it is handled.

Example:

```java
public class Test {
    static void method1() {
        int result = 10 / 0;
    }

    static void method2() {
        method1();
    }

    public static void main(String[] args) {
        try {
            method2();
        } catch (ArithmeticException e) {
            System.out.println("Exception handled in main");
        }
    }
}
```

Flow:

```text
method1 -> method2 -> main
```

---

# 22. What is stack trace?

A stack trace shows the sequence of method calls that led to the exception.

Example:

```java
e.printStackTrace();
```

Output:

```text
java.lang.ArithmeticException: / by zero
    at Test.method1(Test.java:5)
    at Test.method2(Test.java:9)
    at Test.main(Test.java:14)
```

It helps identify where the exception occurred.

---

# 23. Difference between `getMessage()` and `printStackTrace()`?

| Method              | Meaning                        |
| ------------------- | ------------------------------ |
| `getMessage()`      | Returns only exception message |
| `printStackTrace()` | Prints full error trace        |

Example:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println(e.getMessage());
    e.printStackTrace();
}
```

`getMessage()` output:

```text
/ by zero
```

`printStackTrace()` gives full method call details.

---

# 24. What is multi-catch block?

Multi-catch allows handling multiple exceptions in one catch block.

Introduced in Java 7.

Example:

```java
try {
    String value = "abc";
    int number = Integer.parseInt(value);
} catch (NumberFormatException | NullPointerException e) {
    System.out.println("Invalid input");
}
```

Important:

The exception variable in multi-catch is effectively final.

---

# 25. Can we catch parent and child exception in same multi-catch?

No.

Invalid:

```java
catch (Exception | IOException e) {
}
```

Because `IOException` is already a child of `Exception`.

Correct:

```java
catch (IOException | SQLException e) {
}
```

---

# 26. What is try-with-resources?

Try-with-resources automatically closes resources.

Resource must implement `AutoCloseable` or `Closeable`.

Example:

```java
try (FileReader reader = new FileReader("test.txt")) {
    System.out.println("File opened");
} catch (IOException e) {
    System.out.println("File error");
}
```

No need to close manually:

```java
reader.close();
```

Try-with-resources is cleaner and safer than finally-based closing.

---

# 27. Difference between `Closeable` and `AutoCloseable`?

| AutoCloseable                | Closeable                      |
| ---------------------------- | ------------------------------ |
| Introduced in Java 7         | Older interface                |
| Parent interface             | Child interface                |
| `close()` throws `Exception` | `close()` throws `IOException` |
| Used for general resources   | Mostly IO resources            |

Example:

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

```java
public interface Closeable extends AutoCloseable {
    void close() throws IOException;
}
```

---

# 28. What are suppressed exceptions?

Suppressed exceptions occur in try-with-resources when both:

1. Try block throws exception.
2. Resource closing also throws exception.

The main exception is thrown, and closing exception is added as suppressed.

Example:

```java
try {
    // risky code
} catch (Exception e) {
    Throwable[] suppressed = e.getSuppressed();
}
```

This is useful for debugging resource-closing failures.

---

# 29. Can finally override exception from try block?

Yes.

If `finally` throws an exception, it can suppress the original exception from `try`.

Example:

```java
try {
    throw new RuntimeException("Exception from try");
} finally {
    throw new RuntimeException("Exception from finally");
}
```

Output will show:

```text
Exception from finally
```

This is why throwing exceptions from `finally` is dangerous.

---

# 30. What happens if return is used in try, catch, and finally?

`finally` can override return value from try or catch.

Example:

```java
public static int test() {
    try {
        return 10;
    } catch (Exception e) {
        return 20;
    } finally {
        return 30;
    }
}
```

Output:

```java
30
```

Interview point:

Avoid returning from `finally` because it makes code confusing and can hide exceptions.

---

# 31. What is custom exception?

A custom exception is a user-defined exception created for business-specific error scenarios.

Example:

```java
class InsufficientBalanceException extends RuntimeException {
    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Usage:

```java
public void withdraw(double balance, double amount) {
    if (amount > balance) {
        throw new InsufficientBalanceException("Insufficient balance");
    }
}
```

Custom exceptions improve readability and business clarity.

---

# 32. When should we create custom checked exception?

Create custom checked exception when the caller is expected to recover from the problem.

Example:

```java
class InvalidFileFormatException extends Exception {
    public InvalidFileFormatException(String message) {
        super(message);
    }
}
```

Usage:

```java
public void uploadFile(String fileName) throws InvalidFileFormatException {
    if (!fileName.endsWith(".csv")) {
        throw new InvalidFileFormatException("Only CSV files are allowed");
    }
}
```

---

# 33. When should we create custom unchecked exception?

Create custom unchecked exception when the problem is due to invalid input, invalid state, or business rule violation.

Example:

```java
class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```

Usage:

```java
public User findUser(Long id) {
    return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found with id: " + id));
}
```

This is very common in Spring Boot applications.

---

# 34. What is exception wrapping?

Exception wrapping means catching one exception and throwing another meaningful exception.

Example:

```java
try {
    FileReader reader = new FileReader("data.txt");
} catch (IOException e) {
    throw new RuntimeException("Unable to read data file", e);
}
```

Here, original exception is preserved as the cause.

This helps in debugging.

---

# 35. What is exception chaining?

Exception chaining means one exception contains another exception as its cause.

Example:

```java
throw new RuntimeException("Service failed", originalException);
```

To get the cause:

```java
e.getCause();
```

Example:

```java
try {
    int number = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    throw new IllegalArgumentException("Invalid number format", e);
}
```

---

# 36. What is `NullPointerException`?

`NullPointerException` occurs when we try to access a method or field on a null object reference.

Example:

```java
String name = null;
System.out.println(name.length());
```

Fix:

```java
if (name != null) {
    System.out.println(name.length());
}
```

Better modern approach:

```java
Optional.ofNullable(name)
        .ifPresent(n -> System.out.println(n.length()));
```

Interview note:

Avoid unnecessary `Optional` for fields and method parameters unless design requires it.

---

# 37. What is `ClassCastException`?

`ClassCastException` occurs when an object is cast to an incompatible type.

Example:

```java
Object obj = "Hello";
Integer number = (Integer) obj; // ClassCastException
```

Safe approach:

```java
if (obj instanceof Integer) {
    Integer number = (Integer) obj;
}
```

Modern pattern matching style:

```java
if (obj instanceof Integer number) {
    System.out.println(number);
}
```

---

# 38. What is `NumberFormatException`?

`NumberFormatException` occurs when we try to convert an invalid string into a number.

Example:

```java
int number = Integer.parseInt("abc");
```

Handling:

```java
try {
    int number = Integer.parseInt("123");
} catch (NumberFormatException e) {
    System.out.println("Invalid number");
}
```

---

# 39. What is `ArrayIndexOutOfBoundsException`?

It occurs when we try to access an invalid array index.

Example:

```java
int[] numbers = {10, 20, 30};
System.out.println(numbers[5]);
```

Valid indexes are:

```text
0, 1, 2
```

Fix:

```java
if (index >= 0 && index < numbers.length) {
    System.out.println(numbers[index]);
}
```

---

# 40. What is `IllegalArgumentException`?

`IllegalArgumentException` is thrown when a method receives invalid input.

Example:

```java
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

Use it when method arguments are incorrect.

---

# 41. What is `IllegalStateException`?

`IllegalStateException` is thrown when an object is in an invalid state for the requested operation.

Example:

```java
class Order {
    private boolean paid = false;

    public void ship() {
        if (!paid) {
            throw new IllegalStateException("Order cannot be shipped before payment");
        }
        System.out.println("Order shipped");
    }
}
```

Difference:

```text
IllegalArgumentException -> input is wrong
IllegalStateException -> object state is wrong
```

---

# 42. What is the difference between `NoClassDefFoundError` and `ClassNotFoundException`?

| ClassNotFoundException                                  | NoClassDefFoundError                                       |
| ------------------------------------------------------- | ---------------------------------------------------------- |
| Checked exception                                       | Error                                                      |
| Class not found at runtime using reflection/classloader | Class was available at compile time but missing at runtime |
| Can be handled                                          | Usually dependency/classpath problem                       |

Example:

```java
Class.forName("com.mysql.Driver");
```

If class is not found:

```java
ClassNotFoundException
```

`NoClassDefFoundError` commonly happens when required JAR is missing at runtime.

---

# 43. Can we override a method and throw exceptions?

Yes, but rules apply.

For checked exceptions:

The overriding method cannot throw broader checked exceptions than the parent method.

Example:

```java
class Parent {
    void test() throws IOException {
    }
}

class Child extends Parent {
    @Override
    void test() throws FileNotFoundException {
    }
}
```

This is valid because `FileNotFoundException` is a child of `IOException`.

Invalid:

```java
class Child extends Parent {
    @Override
    void test() throws Exception {
    }
}
```

Because `Exception` is broader than `IOException`.

For unchecked exceptions:

No restriction.

---

# 44. Can constructors throw exceptions?

Yes.

Example:

```java
class FileProcessor {
    public FileProcessor(String fileName) throws IOException {
        if (fileName == null) {
            throw new IOException("File name cannot be null");
        }
    }
}
```

Constructor can throw both checked and unchecked exceptions.

But if constructor throws exception, object creation fails.

---

# 45. Can static block throw exceptions?

Static block cannot directly throw checked exceptions unless handled inside.

Example:

```java
class Test {
    static {
        try {
            Class.forName("com.mysql.Driver");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException("Driver not found", e);
        }
    }
}
```

Unchecked exceptions can be thrown from static block, but it may cause class loading failure.

---

# 46. What is best practice: catch Exception or specific exception?

Best practice is to catch specific exceptions.

Bad:

```java
try {
    int number = Integer.parseInt("abc");
} catch (Exception e) {
    System.out.println("Error");
}
```

Good:

```java
try {
    int number = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    System.out.println("Invalid number format");
}
```

Reason:

Specific exceptions make code more readable, maintainable, and debuggable.

---

# 47. Should we catch `Throwable`?

Usually no.

Bad:

```java
try {
    // code
} catch (Throwable t) {
    // not recommended
}
```

Reason:

`Throwable` catches both `Exception` and `Error`.

It may catch serious JVM errors like:

```java
OutOfMemoryError
StackOverflowError
```

These should generally not be handled by application code.

---

# 48. How to handle exceptions globally in Spring Boot?

In Spring Boot, we commonly use `@RestControllerAdvice` and `@ExceptionHandler`.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGenericException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong");
    }
}
```

Custom exception:

```java
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```

Usage:

```java
throw new UserNotFoundException("User not found with id: " + id);
```

This gives a clean API response instead of raw stack trace.

---

# 49. How does exception handling work with Spring transactions?

By default, Spring rolls back transactions for unchecked exceptions.

Rollback happens for:

```java
RuntimeException
Error
```

By default, rollback does not happen for checked exceptions unless configured.

Example:

```java
@Transactional
public void saveOrder() {
    orderRepository.save(order);
    throw new RuntimeException("Payment failed");
}
```

Transaction will roll back.

For checked exception rollback:

```java
@Transactional(rollbackFor = Exception.class)
public void saveOrder() throws Exception {
    orderRepository.save(order);
    throw new Exception("Checked exception");
}
```

Interview point:

Spring transaction rollback behavior is very important for backend interviews.

---

# 50. What are the best practices for exception handling?

Important best practices:

1. Catch specific exceptions, not generic `Exception`.
2. Do not catch `Throwable`.
3. Do not swallow exceptions silently.
4. Always log meaningful error messages.
5. Do not expose internal stack traces to API users.
6. Use custom exceptions for business errors.
7. Use try-with-resources for closing resources.
8. Avoid return statements inside `finally`.
9. Preserve original exception while wrapping.
10. Use global exception handling in Spring Boot.
11. Fail fast for invalid input.
12. Do not use exceptions for normal business flow.

Bad:

```java
try {
    // risky code
} catch (Exception e) {
    // empty catch block
}
```

Good:

```java
try {
    // risky code
} catch (IOException e) {
    log.error("Failed to read file: {}", fileName, e);
    throw new FileProcessingException("Unable to process file", e);
}
```

---

# Important Java Exception Handling Code Example

This is a clean example combining custom exception, validation, and handling.

```java
class InsufficientBalanceException extends RuntimeException {
    public InsufficientBalanceException(String message) {
        super(message);
    }
}

class BankService {

    public void withdraw(double balance, double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be greater than zero");
        }

        if (amount > balance) {
            throw new InsufficientBalanceException("Insufficient balance");
        }

        System.out.println("Withdrawal successful");
    }
}

public class Main {
    public static void main(String[] args) {
        BankService bankService = new BankService();

        try {
            bankService.withdraw(5000, 7000);
        } catch (InsufficientBalanceException e) {
            System.out.println("Business error: " + e.getMessage());
        } catch (IllegalArgumentException e) {
            System.out.println("Validation error: " + e.getMessage());
        } catch (Exception e) {
            System.out.println("Unexpected error");
        }
    }
}
```

Output:

```text
Business error: Insufficient balance
```

---

# Interview-Level Summary

For interviews, remember this clearly:

```text
Throwable is the parent of Exception and Error.

Exception is recoverable.
Error is usually not recoverable.

Checked exceptions are checked at compile time.
Unchecked exceptions are checked at runtime.

throw is used to throw an exception.
throws is used to declare an exception.

finally is used for cleanup.
try-with-resources is preferred for resource closing.

Custom exceptions are used for business-specific errors.

Spring Boot uses @ControllerAdvice and @ExceptionHandler for global exception handling.

Spring @Transactional rolls back by default for unchecked exceptions.
```

---

# Most Common Interview Tricky Points

| Question                                               | Key Answer                               |
| ------------------------------------------------------ | ---------------------------------------- |
| Can finally override return?                           | Yes                                      |
| Is finally always executed?                            | Not always                               |
| Can we catch multiple exceptions?                      | Yes, using multiple catch or multi-catch |
| Can we throw exception manually?                       | Yes, using `throw`                       |
| Can constructor throw exception?                       | Yes                                      |
| Can overriding method throw broader checked exception? | No                                       |
| Should we catch Throwable?                             | Usually no                               |
| Checked vs unchecked?                                  | Compile-time vs runtime                  |
| Spring rollback for checked exception?                 | Not by default                           |
| Best way to close resources?                           | Try-with-resources                       |
