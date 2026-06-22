Below are the **Top 50 Java 8 Interview Questions** in a clean, interview-friendly way. I have focused on **concept + interview answer + small example wherever required**.

# Java 8 Interview Questions and Answers

## 1. What are the main features introduced in Java 8?

Java 8 introduced many important features to make Java more functional and concise.

Important features:

1. Lambda Expressions
2. Functional Interfaces
3. Stream API
4. Default and static methods in interfaces
5. Method References
6. Optional class
7. New Date and Time API
8. CompletableFuture
9. Collectors API
10. forEach method
11. Nashorn JavaScript Engine
12. Base64 Encoder/Decoder

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

names.stream()
     .filter(name -> name.startsWith("D"))
     .forEach(System.out::println);
```

Output:

```java
Danish
```

---

## 2. What is a Lambda Expression in Java 8?

A lambda expression is an anonymous function. It allows us to write short and clean code, especially when working with functional interfaces.

Syntax:

```java
(parameter) -> expression
```

Example:

```java
Runnable r = () -> System.out.println("Thread is running");
r.run();
```

Before Java 8:

```java
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Thread is running");
    }
};
```

Java 8 reduces boilerplate code.

---

## 3. Why do we use Lambda Expressions?

Lambda expressions are used to:

1. Reduce boilerplate code
2. Improve readability
3. Support functional programming
4. Work easily with collections and streams
5. Pass behavior as an argument

Example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 30);

numbers.forEach(n -> System.out.println(n));
```

---

## 4. What is a Functional Interface?

A functional interface is an interface that contains exactly one abstract method.

Example:

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

Usage:

```java
Calculator calculator = (a, b) -> a + b;

System.out.println(calculator.add(10, 20));
```

Output:

```java
30
```

---

## 5. What is the use of `@FunctionalInterface` annotation?

`@FunctionalInterface` is used to tell the compiler that the interface should have only one abstract method.

Example:

```java
@FunctionalInterface
interface Greeting {
    void sayHello();
}
```

If we add more than one abstract method, compiler will give an error.

Important point:

```java
@FunctionalInterface
interface Test {
    void show();

    default void display() {
        System.out.println("Default method");
    }

    static void print() {
        System.out.println("Static method");
    }
}
```

This is valid because default and static methods are not counted as abstract methods.

---

## 6. Can a functional interface have default and static methods?

Yes. A functional interface can have:

1. One abstract method
2. Multiple default methods
3. Multiple static methods

Example:

```java
@FunctionalInterface
interface Vehicle {
    void start();

    default void stop() {
        System.out.println("Vehicle stopped");
    }

    static void service() {
        System.out.println("Vehicle service");
    }
}
```

---

## 7. What are some predefined functional interfaces in Java 8?

Java 8 provides many predefined functional interfaces in `java.util.function` package.

Important ones:

| Interface           | Method      | Use                          |
| ------------------- | ----------- | ---------------------------- |
| Predicate<T>        | test(T t)   | Condition check              |
| Function<T, R>      | apply(T t)  | Input to output conversion   |
| Consumer<T>         | accept(T t) | Consumes input, no return    |
| Supplier<T>         | get()       | Supplies value               |
| BiFunction<T, U, R> | apply(T, U) | Takes two inputs             |
| UnaryOperator<T>    | apply(T)    | Same input and output type   |
| BinaryOperator<T>   | apply(T, T) | Two inputs, same output type |

---

## 8. What is Predicate in Java 8?

`Predicate` is a functional interface used for condition checking. It returns boolean.

Example:

```java
Predicate<Integer> isEven = n -> n % 2 == 0;

System.out.println(isEven.test(10));
System.out.println(isEven.test(15));
```

Output:

```java
true
false
```

Common use:

```java
List<Integer> numbers = Arrays.asList(10, 15, 20, 25);

numbers.stream()
       .filter(n -> n % 2 == 0)
       .forEach(System.out::println);
```

---

## 9. What is Function in Java 8?

`Function<T, R>` takes one input and returns one output.

Example:

```java
Function<String, Integer> lengthFunction = str -> str.length();

System.out.println(lengthFunction.apply("Java"));
```

Output:

```java
4
```

Another example:

```java
Function<Integer, Integer> square = n -> n * n;

System.out.println(square.apply(5));
```

Output:

```java
25
```

---

## 10. What is Consumer in Java 8?

`Consumer<T>` takes input but does not return anything.

Example:

```java
Consumer<String> printName = name -> System.out.println(name);

printName.accept("Danish");
```

Output:

```java
Danish
```

Common use:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

names.forEach(name -> System.out.println(name));
```

---

## 11. What is Supplier in Java 8?

`Supplier<T>` does not take input but returns a value.

Example:

```java
Supplier<Double> randomValue = () -> Math.random();

System.out.println(randomValue.get());
```

Another example:

```java
Supplier<String> supplier = () -> "Hello Java 8";

System.out.println(supplier.get());
```

---

## 12. What is Method Reference in Java 8?

Method reference is a short form of lambda expression. It is used when lambda expression only calls an existing method.

Syntax:

```java
ClassName::methodName
```

Example with lambda:

```java
names.forEach(name -> System.out.println(name));
```

Using method reference:

```java
names.forEach(System.out::println);
```

---

## 13. What are the types of Method References?

There are four types:

### 1. Static method reference

```java
Function<String, Integer> parser = Integer::parseInt;
```

### 2. Instance method reference of particular object

```java
PrintStream ps = System.out;
Consumer<String> consumer = ps::println;
```

### 3. Instance method reference of arbitrary object

```java
Function<String, String> upper = String::toUpperCase;
```

### 4. Constructor reference

```java
Supplier<List<String>> listSupplier = ArrayList::new;
```

---

## 14. What is Constructor Reference?

Constructor reference is used to refer to a constructor using `ClassName::new`.

Example:

```java
Supplier<List<String>> supplier = ArrayList::new;

List<String> list = supplier.get();
list.add("Java 8");

System.out.println(list);
```

Output:

```java
[Java 8]
```

---

## 15. What is Stream API in Java 8?

Stream API is used to process collections in a functional style.

It helps us perform operations like:

1. filter
2. map
3. sort
4. collect
5. reduce
6. count
7. min/max
8. grouping

Example:

```java
List<Integer> numbers = Arrays.asList(10, 15, 20, 25);

List<Integer> evenNumbers = numbers.stream()
        .filter(n -> n % 2 == 0)
        .collect(Collectors.toList());

System.out.println(evenNumbers);
```

Output:

```java
[10, 20]
```

Important point:

A stream does not store data. It processes data from a source like Collection, Array, or I/O channel.

---

## 16. What is the difference between Collection and Stream?

| Collection         | Stream              |
| ------------------ | ------------------- |
| Stores data        | Does not store data |
| Data structure     | Processing pipeline |
| Can be reused      | Cannot be reused    |
| External iteration | Internal iteration  |
| Eagerly created    | Lazily processed    |

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman");

Stream<String> stream = names.stream();
stream.forEach(System.out::println);

// stream.forEach(System.out::println); // Error: stream already used
```

A stream can be consumed only once.

---

## 17. What are intermediate and terminal operations in Stream?

### Intermediate operations

They return another stream and are lazy.

Examples:

```java
filter()
map()
sorted()
distinct()
limit()
skip()
```

### Terminal operations

They produce final result and start stream processing.

Examples:

```java
collect()
forEach()
count()
reduce()
min()
max()
anyMatch()
allMatch()
noneMatch()
```

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

names.stream()
     .filter(name -> name.length() > 4)   // intermediate
     .forEach(System.out::println);       // terminal
```

---

## 18. What is lazy evaluation in Stream?

Streams are lazy because intermediate operations are not executed until a terminal operation is called.

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

names.stream()
     .filter(name -> {
         System.out.println("Filtering: " + name);
         return name.startsWith("D");
     });
```

Nothing will print because there is no terminal operation.

Now add terminal operation:

```java
names.stream()
     .filter(name -> {
         System.out.println("Filtering: " + name);
         return name.startsWith("D");
     })
     .forEach(System.out::println);
```

Now processing starts.

---

## 19. What is the difference between `map()` and `flatMap()`?

### `map()`

Used for one-to-one transformation.

Example:

```java
List<String> names = Arrays.asList("danish", "aman");

List<String> upperNames = names.stream()
        .map(String::toUpperCase)
        .collect(Collectors.toList());

System.out.println(upperNames);
```

Output:

```java
[DANISH, AMAN]
```

### `flatMap()`

Used for one-to-many transformation and flattening nested collections.

Example:

```java
List<List<String>> names = Arrays.asList(
        Arrays.asList("Danish", "Aman"),
        Arrays.asList("Rahul", "Sahil")
);

List<String> flatList = names.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(flatList);
```

Output:

```java
[Danish, Aman, Rahul, Sahil]
```

---

## 20. What is `filter()` in Stream?

`filter()` is used to select elements based on a condition.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 15, 20, 25, 30);

List<Integer> result = numbers.stream()
        .filter(n -> n > 20)
        .collect(Collectors.toList());

System.out.println(result);
```

Output:

```java
[25, 30]
```

---

## 21. What is `distinct()` in Stream?

`distinct()` removes duplicate elements.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 10, 30, 20);

List<Integer> uniqueNumbers = numbers.stream()
        .distinct()
        .collect(Collectors.toList());

System.out.println(uniqueNumbers);
```

Output:

```java
[10, 20, 30]
```

For custom objects, `distinct()` depends on `equals()` and `hashCode()`.

---

## 22. What is `sorted()` in Stream?

`sorted()` is used to sort elements.

Natural sorting:

```java
List<Integer> numbers = Arrays.asList(30, 10, 20);

numbers.stream()
       .sorted()
       .forEach(System.out::println);
```

Custom sorting:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

names.stream()
     .sorted(Comparator.reverseOrder())
     .forEach(System.out::println);
```

---

## 23. How to sort a list of employees using Java 8?

Example:

```java
class Employee {
    private String name;
    private int salary;

    public Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public int getSalary() {
        return salary;
    }
}
```

Sort by salary:

```java
List<Employee> employees = Arrays.asList(
        new Employee("Danish", 70000),
        new Employee("Aman", 50000),
        new Employee("Rahul", 90000)
);

employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary))
        .forEach(e -> System.out.println(e.getName() + " " + e.getSalary()));
```

Sort by salary descending:

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary).reversed())
        .forEach(e -> System.out.println(e.getName()));
```

---

## 24. What is `limit()` in Stream?

`limit()` is used to take only the first given number of elements.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40, 50);

numbers.stream()
       .limit(3)
       .forEach(System.out::println);
```

Output:

```java
10
20
30
```

---

## 25. What is `skip()` in Stream?

`skip()` is used to skip the first given number of elements.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40, 50);

numbers.stream()
       .skip(2)
       .forEach(System.out::println);
```

Output:

```java
30
40
50
```

---

## 26. What is `reduce()` in Java 8?

`reduce()` combines stream elements into a single result.

Example: sum of numbers

```java
List<Integer> numbers = Arrays.asList(10, 20, 30);

int sum = numbers.stream()
        .reduce(0, (a, b) -> a + b);

System.out.println(sum);
```

Output:

```java
60
```

Using method reference:

```java
int sum = numbers.stream()
        .reduce(0, Integer::sum);
```

Example: maximum number

```java
int max = numbers.stream()
        .reduce(Integer.MIN_VALUE, Integer::max);
```

---

## 27. What is `collect()` in Stream?

`collect()` is a terminal operation used to collect stream output into a collection or other result.

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

List<String> result = names.stream()
        .filter(name -> name.length() > 4)
        .collect(Collectors.toList());

System.out.println(result);
```

Output:

```java
[Danish, Rahul]
```

---

## 28. What is Collectors class in Java 8?

`Collectors` is a utility class used with Stream API to collect data.

Common methods:

```java
Collectors.toList()
Collectors.toSet()
Collectors.toMap()
Collectors.groupingBy()
Collectors.partitioningBy()
Collectors.counting()
Collectors.joining()
Collectors.summingInt()
Collectors.averagingInt()
```

Example:

```java
List<String> names = Arrays.asList("Danish", "Aman", "Rahul");

String result = names.stream()
        .collect(Collectors.joining(", "));

System.out.println(result);
```

Output:

```java
Danish, Aman, Rahul
```

---

## 29. What is `groupingBy()` in Java 8?

`groupingBy()` is used to group data based on a condition or field.

Example:

```java
class Employee {
    private String name;
    private String department;

    public Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }

    public String getDepartment() {
        return department;
    }

    public String getName() {
        return name;
    }
}
```

Usage:

```java
List<Employee> employees = Arrays.asList(
        new Employee("Danish", "IT"),
        new Employee("Aman", "HR"),
        new Employee("Rahul", "IT")
);

Map<String, List<Employee>> grouped = employees.stream()
        .collect(Collectors.groupingBy(Employee::getDepartment));

System.out.println(grouped);
```

Output concept:

```java
IT -> Danish, Rahul
HR -> Aman
```

---

## 30. What is `partitioningBy()` in Java 8?

`partitioningBy()` divides data into two groups: `true` and `false`.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 15, 20, 25);

Map<Boolean, List<Integer>> result = numbers.stream()
        .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(result);
```

Output:

```java
{
 true=[10, 20],
 false=[15, 25]
}
```

Difference:

| groupingBy                   | partitioningBy          |
| ---------------------------- | ----------------------- |
| Can create multiple groups   | Creates only two groups |
| Uses classifier function     | Uses Predicate          |
| Example: group by department | Example: even/odd       |

---

## 31. How to convert List to Map using Java 8?

Example:

```java
class Employee {
    private int id;
    private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

Convert list to map:

```java
List<Employee> employees = Arrays.asList(
        new Employee(1, "Danish"),
        new Employee(2, "Aman")
);

Map<Integer, String> map = employees.stream()
        .collect(Collectors.toMap(Employee::getId, Employee::getName));

System.out.println(map);
```

Output:

```java
{1=Danish, 2=Aman}
```

Important interview point:

If duplicate keys exist, `toMap()` throws exception.

Handle duplicate keys:

```java
Map<Integer, String> map = employees.stream()
        .collect(Collectors.toMap(
                Employee::getId,
                Employee::getName,
                (oldValue, newValue) -> oldValue
        ));
```

---

## 32. What is Optional in Java 8?

`Optional` is a container object that may or may not contain a non-null value.

It helps avoid `NullPointerException`.

Example:

```java
Optional<String> name = Optional.ofNullable(null);

System.out.println(name.orElse("Default Name"));
```

Output:

```java
Default Name
```

---

## 33. Difference between `Optional.of()`, `ofNullable()` and `empty()`?

### `Optional.of()`

Use when value is definitely non-null.

```java
Optional<String> name = Optional.of("Danish");
```

If value is null, it throws `NullPointerException`.

### `Optional.ofNullable()`

Use when value may be null.

```java
Optional<String> name = Optional.ofNullable(null);
```

### `Optional.empty()`

Creates empty Optional.

```java
Optional<String> empty = Optional.empty();
```

---

## 34. What are common Optional methods?

Important methods:

```java
isPresent()
ifPresent()
orElse()
orElseGet()
orElseThrow()
map()
flatMap()
filter()
```

Example:

```java
Optional<String> name = Optional.of("Danish");

name.ifPresent(n -> System.out.println(n.toUpperCase()));
```

Output:

```java
DANISH
```

Example with `orElseThrow()`:

```java
String value = Optional.ofNullable(null)
        .orElseThrow(() -> new RuntimeException("Value not found"));
```

---

## 35. Difference between `orElse()` and `orElseGet()`?

### `orElse()`

Always executes the default value logic.

### `orElseGet()`

Executes supplier only when Optional is empty.

Example:

```java
String name = Optional.of("Danish")
        .orElse(getDefaultName());
```

Here `getDefaultName()` will execute even if value is present.

Better:

```java
String name = Optional.of("Danish")
        .orElseGet(() -> getDefaultName());
```

Use `orElseGet()` when default value creation is expensive.

---

## 36. What are default methods in interface?

Default methods allow interfaces to have method implementation.

Example:

```java
interface Vehicle {
    default void start() {
        System.out.println("Vehicle started");
    }
}

class Car implements Vehicle {
}
```

Usage:

```java
Car car = new Car();
car.start();
```

Output:

```java
Vehicle started
```

Main reason:

Default methods were introduced to add new methods in existing interfaces without breaking old implementation classes.

---

## 37. What are static methods in interface?

Java 8 allows static methods inside interfaces.

Example:

```java
interface Vehicle {
    static void service() {
        System.out.println("Vehicle service");
    }
}
```

Usage:

```java
Vehicle.service();
```

Static interface methods are called using interface name, not object.

---

## 38. What happens if two interfaces have same default method?

If a class implements two interfaces having same default method, the class must override that method.

Example:

```java
interface A {
    default void show() {
        System.out.println("A");
    }
}

interface B {
    default void show() {
        System.out.println("B");
    }
}

class Test implements A, B {
    @Override
    public void show() {
        A.super.show();
    }
}
```

This avoids ambiguity.

---

## 39. Can we override default methods?

Yes, default methods can be overridden.

Example:

```java
interface Vehicle {
    default void start() {
        System.out.println("Vehicle started");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
}
```

---

## 40. What is the difference between abstract class and interface after Java 8?

| Abstract Class                         | Interface                                     |
| -------------------------------------- | --------------------------------------------- |
| Can have constructor                   | Cannot have constructor                       |
| Can have instance variables            | Cannot have normal instance variables         |
| Supports state                         | Does not support object state                 |
| Single inheritance                     | Multiple interface implementation             |
| Can have abstract and concrete methods | Can have abstract, default and static methods |

Interview answer:

Even after Java 8, abstract class is used when we need shared state/common fields. Interface is used for contract and multiple behavior support.

---

## 41. What is new Date and Time API in Java 8?

Java 8 introduced a new Date-Time API in `java.time` package.

Important classes:

| Class             | Use                      |
| ----------------- | ------------------------ |
| LocalDate         | Date only                |
| LocalTime         | Time only                |
| LocalDateTime     | Date and time            |
| ZonedDateTime     | Date-time with timezone  |
| Period            | Difference between dates |
| Duration          | Difference between times |
| DateTimeFormatter | Formatting and parsing   |

Example:

```java
LocalDate today = LocalDate.now();
LocalDate joiningDate = LocalDate.of(2026, 6, 22);

System.out.println(today);
System.out.println(joiningDate);
```

---

## 42. Why was new Date-Time API introduced?

Old classes like `Date` and `Calendar` had problems:

1. They were mutable
2. Poor readability
3. Confusing month handling
4. Thread-safety issues
5. Poor timezone handling

New Java 8 Date-Time API is:

1. Immutable
2. Thread-safe
3. More readable
4. Better timezone support
5. Inspired by Joda-Time

Example:

```java
LocalDate date = LocalDate.now();
LocalDate nextWeek = date.plusDays(7);

System.out.println(nextWeek);
```

---

## 43. Difference between LocalDate, LocalTime and LocalDateTime?

### LocalDate

Stores only date.

```java
LocalDate date = LocalDate.now();
```

Example output:

```java
2026-06-22
```

### LocalTime

Stores only time.

```java
LocalTime time = LocalTime.now();
```

Example output:

```java
10:30:15
```

### LocalDateTime

Stores date and time.

```java
LocalDateTime dateTime = LocalDateTime.now();
```

Example output:

```java
2026-06-22T10:30:15
```

---

## 44. What is DateTimeFormatter?

`DateTimeFormatter` is used to format and parse date-time objects.

Example:

```java
LocalDate date = LocalDate.now();

DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

String formattedDate = date.format(formatter);

System.out.println(formattedDate);
```

Output:

```java
22-06-2026
```

Parse String to Date:

```java
String dateStr = "22-06-2026";

LocalDate date = LocalDate.parse(dateStr, DateTimeFormatter.ofPattern("dd-MM-yyyy"));
```

---

## 45. What is CompletableFuture in Java 8?

`CompletableFuture` is used for asynchronous programming.

It allows us to run tasks in the background and combine multiple async operations.

Example:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "Hello Java 8";
});

System.out.println(future.get());
```

Output:

```java
Hello Java 8
```

Common methods:

```java
supplyAsync()
runAsync()
thenApply()
thenAccept()
thenRun()
thenCombine()
exceptionally()
handle()
```

---

## 46. Difference between `thenApply()`, `thenAccept()` and `thenRun()`?

### `thenApply()`

Takes input and returns output.

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Java")
        .thenApply(value -> value + " 8");

System.out.println(future.get());
```

Output:

```java
Java 8
```

### `thenAccept()`

Takes input but returns nothing.

```java
CompletableFuture.supplyAsync(() -> "Java 8")
        .thenAccept(value -> System.out.println(value));
```

### `thenRun()`

Does not take input and does not return output.

```java
CompletableFuture.supplyAsync(() -> "Java 8")
        .thenRun(() -> System.out.println("Task completed"));
```

---

## 47. Difference between `runAsync()` and `supplyAsync()`?

| runAsync()                      | supplyAsync()                |
| ------------------------------- | ---------------------------- |
| Does not return result          | Returns result               |
| Uses Runnable                   | Uses Supplier                |
| Returns CompletableFuture<Void> | Returns CompletableFuture<T> |

Example:

```java
CompletableFuture<Void> future1 = CompletableFuture.runAsync(() -> {
    System.out.println("Task running");
});
```

```java
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    return "Task result";
});
```

---

## 48. What is Parallel Stream?

Parallel stream divides the task into multiple sub-tasks and processes them using multiple threads.

Example:

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40, 50);

numbers.parallelStream()
       .forEach(System.out::println);
```

Important interview point:

Parallel stream uses the common `ForkJoinPool`.

Use parallel stream carefully. It is useful for CPU-intensive tasks and large data sets, but not always faster.

Avoid parallel stream when:

1. Data size is small
2. Task is I/O-heavy
3. Shared mutable state is involved
4. Order is important
5. Database/API calls are involved

---

## 49. Difference between Stream and Parallel Stream?

| Stream                     | Parallel Stream                     |
| -------------------------- | ----------------------------------- |
| Sequential processing      | Parallel processing                 |
| Uses single thread         | Uses multiple threads               |
| Predictable order          | Order may vary                      |
| Good for simple operations | Good for large CPU-heavy operations |
| Less overhead              | More overhead                       |

Example:

```java
numbers.stream()
       .forEach(System.out::println);
```

```java
numbers.parallelStream()
       .forEach(System.out::println);
```

For ordered output in parallel stream:

```java
numbers.parallelStream()
       .forEachOrdered(System.out::println);
```

---

## 50. What is the difference between `findFirst()` and `findAny()`?

### `findFirst()`

Returns the first element from the stream.

```java
List<Integer> numbers = Arrays.asList(10, 20, 30);

Optional<Integer> result = numbers.stream()
        .findFirst();

System.out.println(result.get());
```

Output:

```java
10
```

### `findAny()`

Returns any element, mainly useful in parallel streams.

```java
Optional<Integer> result = numbers.parallelStream()
        .findAny();
```

Difference:

| findFirst()                        | findAny()                 |
| ---------------------------------- | ------------------------- |
| Returns first element              | Returns any element       |
| Maintains order                    | May not maintain order    |
| Useful in sequential stream        | Useful in parallel stream |
| Slightly slower in parallel stream | Faster in parallel stream |

---

# Extra Important Java 8 Coding Questions

## 1. Find even numbers from a list

```java
List<Integer> numbers = Arrays.asList(10, 15, 20, 25, 30);

List<Integer> evenNumbers = numbers.stream()
        .filter(n -> n % 2 == 0)
        .collect(Collectors.toList());

System.out.println(evenNumbers);
```

---

## 2. Find duplicate elements from a list

```java
List<Integer> numbers = Arrays.asList(10, 20, 10, 30, 20, 40);

Set<Integer> set = new HashSet<>();

List<Integer> duplicates = numbers.stream()
        .filter(n -> !set.add(n))
        .collect(Collectors.toList());

System.out.println(duplicates);
```

Output:

```java
[10, 20]
```

---

## 3. Count frequency of each word

```java
List<String> words = Arrays.asList("java", "spring", "java", "boot", "spring");

Map<String, Long> frequency = words.stream()
        .collect(Collectors.groupingBy(word -> word, Collectors.counting()));

System.out.println(frequency);
```

Output:

```java
{spring=2, java=2, boot=1}
```

---

## 4. Find highest salary employee

```java
Employee highestPaidEmployee = employees.stream()
        .max(Comparator.comparing(Employee::getSalary))
        .orElseThrow(() -> new RuntimeException("Employee not found"));
```

---

## 5. Group employees by department

```java
Map<String, List<Employee>> employeesByDept = employees.stream()
        .collect(Collectors.groupingBy(Employee::getDepartment));
```

---

## 6. Get employee names as comma-separated string

```java
String names = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.joining(", "));
```

---

## 7. Find second highest salary

```java
Optional<Integer> secondHighestSalary = employees.stream()
        .map(Employee::getSalary)
        .distinct()
        .sorted(Comparator.reverseOrder())
        .skip(1)
        .findFirst();
```

---

## 8. Sort employees by salary and then by name

```java
List<Employee> sortedEmployees = employees.stream()
        .sorted(
            Comparator.comparing(Employee::getSalary)
                      .thenComparing(Employee::getName)
        )
        .collect(Collectors.toList());
```

---

# Most Important Java 8 Topics to Revise Before Interview

For Java 8 interviews, focus strongly on these areas:

1. Lambda Expression
2. Functional Interface
3. Predicate, Function, Consumer, Supplier
4. Stream API
5. map vs flatMap
6. filter, sorted, distinct, limit, skip
7. collect and Collectors
8. groupingBy and partitioningBy
9. Optional
10. Default and static methods in interface
11. Method Reference
12. Date-Time API
13. CompletableFuture
14. Parallel Stream
15. Stream coding questions

For your experience level, interviewers will not only ask definitions. They may ask:

“How have you used Java 8 in your project?”

A strong answer can be:

> In my project, I used Java 8 Stream API to process collections, filter records, sort data, group records based on category or status, and convert entity lists into DTO lists. I used Optional to handle possible null values safely. I used lambda expressions with functional interfaces to reduce boilerplate code. For asynchronous processing, CompletableFuture can be used when independent tasks need to run in parallel, such as calling multiple services or APIs.
