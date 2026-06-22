# Index

[Top 20 Java 8 Stream API Programs-1](#top-20-java-8-stream-api-programs-1)

[Top 20 Java 8 Stream API Programs-2](#top-20-java-8-stream-api-programs-2)

[Top 15 Java 8 Stream API Advanced Programs-2](#top-15-java-8-stream-api-advanced-programs)

[Some Scenario Based Questions](#scenario-based-ques)

---

# Top 20 Java 8 Stream API Programs-1

## 🟢 Level 1 – Basic Stream Operations (Must Master)

1. **Find even numbers from a list**

```java
list.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
```

2. **Find odd numbers**

3. **Find numbers starting with 1**

```java
list.stream()
    .map(String::valueOf)
    .filter(s -> s.startsWith("1"))
    .collect(Collectors.toList());
```

4. **Find duplicate elements in a list**

```java
Set<Integer> seen = new HashSet<>();
list.stream()
    .filter(n -> !seen.add(n))
    .collect(Collectors.toSet());
```

5. **Remove duplicates from a list**

```java
list.stream()
    .distinct()
    .collect(Collectors.toList());
```

6. **Find first element of a list**

```java
list.stream()
    .findFirst();
```

7. **Count total elements**

```java
list.stream().count();
```

## 🟡 Level 2 – Intermediate Operations

8. **Find maximum and minimum number**

```java
list.stream().max(Integer::compareTo);
list.stream().min(Integer::compareTo);
```

9. **Find second highest number**

```java
list.stream()
    .sorted(Comparator.reverseOrder())
    .skip(1)
    .findFirst();
```

10. **Find sum of all numbers**

```java
list.stream()
    .mapToInt(Integer::intValue)
    .sum();
```

11. **Find average of numbers**

```java
list.stream()
    .mapToInt(Integer::intValue)
    .average();
```

12. **Check if list contains a specific element**

```java
list.stream()
    .anyMatch(n -> n == 10);
```

13. **Sort list ascending & descending**

```java
list.stream().sorted().collect(Collectors.toList());
list.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList());
```

---

## 🟠 Level 3 – Advanced / Interview-Oriented

14. **Group elements by frequency**

```java
list.stream()
    .collect(Collectors.groupingBy(
        Function.identity(),
        Collectors.counting()
    ));
```

15. **Find element with highest frequency**

16. **Convert list of strings to uppercase**

```java
list.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

17. **Flatten a list of lists**

```java
listOfLists.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());
```

18. **Partition numbers into even and odd**

```java
list.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

19. **Find longest string in a list**

```java
list.stream()
    .max(Comparator.comparingInt(String::length));
```

20. **Sort employees by salary (Custom Object Stream)**

```java
employees.stream()
    .sorted(Comparator.comparing(Employee::getSalary))
    .collect(Collectors.toList());
```

---

# 🎯 If You Want to Go One Level Higher

Add these real interview ones:

* Find common elements between two lists
* Remove null values from list
* Find kth smallest element
* Find all palindrome strings
* Group employees by department
* Find highest paid employee per department
* Convert list to Map

---

# 🧠 Now Important Part

Don’t just memorize.

For each program:

* First solve using traditional loop
* Then convert to Stream
* Then optimize using mapToInt / groupingBy / partitioningBy

That’s how depth develops.

---

# Top 20 Java 8 Stream API Programs-2

# ✅ 1. Intersection of Two Lists

```java
List<Integer> list1 = Arrays.asList(1,2,3,4,5);
List<Integer> list2 = Arrays.asList(3,4,5,6,7);

List<Integer> result = list1.stream()
        .filter(list2::contains)
        .collect(Collectors.toList());

System.out.println(result); // [3,4,5]
```

---

# ✅ 2. Elements in List1 but not in List2

```java
List<Integer> result = list1.stream()
        .filter(e -> !list2.contains(e))
        .collect(Collectors.toList());
```

---

# ✅ 3. Kth Smallest Element

```java
int k = 3;

Optional<Integer> result = list1.stream()
        .sorted()
        .skip(k - 1)
        .findFirst();
```

---

# ✅ 4. Kth Largest Element

```java
Optional<Integer> result = list1.stream()
        .sorted(Comparator.reverseOrder())
        .skip(k - 1)
        .findFirst();
```

---

# ✅ 5. Find Prime Numbers

```java
List<Integer> primes = list1.stream()
        .filter(n -> n > 1 && IntStream.range(2, n)
                .noneMatch(i -> n % i == 0))
        .collect(Collectors.toList());
```

---

# ✅ 6. Find Palindrome Strings

```java
List<String> words = Arrays.asList("madam","java","level");

List<String> palindromes = words.stream()
        .filter(s -> s.equals(new StringBuilder(s).reverse().toString()))
        .collect(Collectors.toList());
```

---

# ✅ 7. Remove Null Values

```java
List<String> list = Arrays.asList("java", null, "spring");

List<String> result = list.stream()
        .filter(Objects::nonNull)
        .collect(Collectors.toList());
```

---

# ✅ 8. Count Strings Length > 5

```java
long count = words.stream()
        .filter(s -> s.length() > 5)
        .count();
```

---

# ✅ 9. Convert List to Comma Separated String

```java
String result = words.stream()
        .collect(Collectors.joining(","));
```

---

# ✅ 10. Convert List<Integer> to Map (Number → Square)

```java
Map<Integer, Integer> map = list1.stream()
        .collect(Collectors.toMap(
                Function.identity(),
                n -> n * n
        ));
```

---

# ✅ 11. First Non-Repeated Character

```java
String input = "aabccdeff";

Optional<Character> result = input.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                Function.identity(),
                LinkedHashMap::new,
                Collectors.counting()))
        .entrySet().stream()
        .filter(e -> e.getValue() == 1)
        .map(Map.Entry::getKey)
        .findFirst();
```

---

# ✅ 12. First Repeated Character

```java
Optional<Character> result = input.chars()
        .mapToObj(c -> (char) c)
        .filter(c -> input.indexOf(c) != input.lastIndexOf(c))
        .findFirst();
```

---

# ✅ 13. Character Frequency in String

```java
Map<Character, Long> freq = input.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()));
```

---

# ✅ 14. Character with Maximum Frequency

```java
Optional<Map.Entry<Character, Long>> max =
        freq.entrySet().stream()
            .max(Map.Entry.comparingByValue());
```

---

# ✅ 15. Reverse Each Word in Sentence

```java
String sentence = "java stream api";

String result = Arrays.stream(sentence.split(" "))
        .map(word -> new StringBuilder(word).reverse().toString())
        .collect(Collectors.joining(" "));
```

---

# Employee Class

```java
class Employee {
    int id;
    String name;
    String department;
    double salary;
    int age;

    // constructor + getters
}
```

---

# ✅ 16. Group Employees by Department

```java
Map<String, List<Employee>> grouped =
        employees.stream()
                .collect(Collectors.groupingBy(Employee::getDepartment));
```

---

# ✅ 17. Highest Paid Employee per Department

```java
Map<String, Optional<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(Comparator.comparing(Employee::getSalary))
                ));
```

---

# ✅ 18. Average Salary Department-wise

```java
Map<String, Double> avgSalary =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingDouble(Employee::getSalary)
                ));
```

---

# ✅ 19. Youngest Employee

```java
Optional<Employee> youngest =
        employees.stream()
                .min(Comparator.comparing(Employee::getAge));
```

---

# ✅ 20. Sort Employees

### Salary Descending

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary).reversed())
        .collect(Collectors.toList());
```

### Age Ascending

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getAge))
        .collect(Collectors.toList());
```

### Name Alphabetically

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getName))
        .collect(Collectors.toList());
```

---

# 🎯 If You Can Do These Without Looking…

You are strong in Java 8 Streams.

If you want next:

* 🔥 15 Advanced Combined Problems
* 🎯 Real Interview Mock Round
* 📘 Stream API Cheat Sheet (Complete Summary)
* 🚀 Stream + Optional + Collectors Deep Dive


# Top 15 Java 8 Stream API Advanced Programs

Below are **clean, production-style solutions** for all 15 advanced Stream problems.

Assume this Employee class:

```java
class Employee {
    private int id;
    private String name;
    private String department;
    private double salary;
    private int age;

    // constructor
    public Employee(int id, String name, String department, double salary, int age) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
        this.age = age;
    }

    // getters
    public int getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    public int getAge() { return age; }
}
```

Assume:

```java
List<Employee> employees = // populated list
```

---

# ✅ 1. Second Highest Salary Overall

```java
Optional<Employee> secondHighest = employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary).reversed())
        .skip(1)
        .findFirst();
```

---

# ✅ 2. Second Highest Salary in Each Department

```java
Map<String, Optional<Employee>> result = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.collectingAndThen(
                        Collectors.toList(),
                        list -> list.stream()
                                .sorted(Comparator.comparing(Employee::getSalary).reversed())
                                .skip(1)
                                .findFirst()
                )
        ));
```

---

# ✅ 3. Department with Highest Average Salary

```java
Optional<Map.Entry<String, Double>> highestAvgDept =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingDouble(Employee::getSalary)
                ))
                .entrySet()
                .stream()
                .max(Map.Entry.comparingByValue());
```

---

# ✅ 4. Count Employees in Each Department

```java
Map<String, Long> countByDept =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()
                ));
```

---

# ✅ 5. Partition Employees Above and Below 30

```java
Map<Boolean, List<Employee>> partitioned =
        employees.stream()
                .collect(Collectors.partitioningBy(e -> e.getAge() > 30));
```

---

# ✅ 6. Get Names of Employees Per Department

```java
Map<String, List<String>> namesByDept =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.mapping(Employee::getName, Collectors.toList())
                ));
```

---

# ✅ 7. Total Salary Expense Per Department

```java
Map<String, Double> totalSalary =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.summingDouble(Employee::getSalary)
                ));
```

---

# ✅ 8. Highest Paid Employee Overall

```java
Optional<Employee> highestPaid =
        employees.stream()
                .max(Comparator.comparing(Employee::getSalary));
```

---

# ✅ 9. Map<Department, Highest Paid Employee>

```java
Map<String, Optional<Employee>> highestByDept =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(Comparator.comparing(Employee::getSalary))
                ));
```

---

# ✅ 10. Sort by Department then Salary Desc

```java
List<Employee> sorted =
        employees.stream()
                .sorted(Comparator.comparing(Employee::getDepartment)
                        .thenComparing(Employee::getSalary, Comparator.reverseOrder()))
                .collect(Collectors.toList());
```

---

# ✅ 11. Find Duplicate Employee Names

```java
List<String> duplicateNames =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getName,
                        Collectors.counting()
                ))
                .entrySet()
                .stream()
                .filter(e -> e.getValue() > 1)
                .map(Map.Entry::getKey)
                .collect(Collectors.toList());
```

---

# ✅ 12. Get Unique Departments

```java
List<String> uniqueDepartments =
        employees.stream()
                .map(Employee::getDepartment)
                .distinct()
                .collect(Collectors.toList());
```

---

# ✅ 13. Average Age of Employees

```java
double averageAge =
        employees.stream()
                .collect(Collectors.averagingInt(Employee::getAge));
```

---

# ✅ 14. Employees Whose Salary > Department Average

```java
Map<String, Double> avgSalaryByDept =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingDouble(Employee::getSalary)
                ));

List<Employee> aboveDeptAverage =
        employees.stream()
                .filter(e -> e.getSalary() > avgSalaryByDept.get(e.getDepartment()))
                .collect(Collectors.toList());
```

---

# ✅ 15. Map<Department, Sorted Employee Names by Salary>

```java
Map<String, List<String>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.toList(),
                                list -> list.stream()
                                        .sorted(Comparator.comparing(Employee::getSalary).reversed())
                                        .map(Employee::getName)
                                        .collect(Collectors.toList())
                        )
                ));
```

---

# 🎯 What You Just Learned

These advanced patterns:

* `groupingBy`
* `mapping`
* `counting`
* `summingDouble`
* `averagingDouble`
* `maxBy`
* `collectingAndThen`
* `partitioningBy`
* `thenComparing`

If you can explain each collector clearly in interview — you are strong.

---

Now I’ll ask you one serious question:

Can you convert problem 14 (salary > dept average) into a **single pipeline** without precomputing the map?

If yes → you’re approaching senior thinking.

If you want, next we can:

* Build a Stream API cheat sheet
* Or do a timed mock interview simulation
* Or break down Stream internals (how it works under the hood)

Your move.

# Scenario Based Ques

### 1) Top 3 expensive orders निकालो

**Question:** `List<Order>` se top-3 सबसे महंगे orders चाहिए.
**Solution:**

```java
List<Order> top3 =
    orders.stream()
          .sorted(Comparator.comparing(Order::getAmount).reversed())
          .limit(3)
          .toList();
```

Sorted + limit.

---

### 2) Characters की frequency map बनाओ ---Need to check

**Question:** `List<String> words` → हर character की frequency (case-insensitive).
**Solution:**

```java
Map<Character, Long> freq =
    words.stream()
         .map(String::toLowerCase)
         .flatMap(s -> s.chars().mapToObj(c -> (char) c))
         .collect(Collectors.groupingBy(c -> c, Collectors.counting()));
```

`groupingBy + counting()`.

---

### 3) Latest login time per user

**Question:** `List<LoginEvent(userId, time)` → हर user का latest time.
**Solution:**

```java
Map<String, Instant> latest =
    events.stream().collect(Collectors.toMap(
        LoginEvent::getUserId,
        LoginEvent::getTime,
        BinaryOperator.maxBy(Comparator.naturalOrder())
    ));
```

`toMap` ke merge me `maxBy`.

---

### 4) Names को comma-separated string (distinct, sorted, upper)

**Question:** `List<String> names` → `"ALICE, BOB, CHARLIE"`
**Solution:**

```java
String csv =
    names.stream()
         .map(String::toUpperCase)
         .distinct()
         .sorted()
         .collect(Collectors.joining(", "));
```

---

### 5) Optional + Supplier se default value

**Question:** `User` की `getCity()` null हो sakti है; default lazily compute karo.
**Solution:**

```java
Supplier<String> defaultCity = () -> Config.loadDefaultCity(); // heavy call
String city = Optional.ofNullable(user.getCity())
                      .orElseGet(defaultCity);
```

`orElseGet` me Supplier heavy work ko tabhi chalata hai jab zarurat ho.

---

### 6) Numbers को prime / non-prime partition

**Question:** `List<Integer>` → prime vs non-prime.
**Solution:**

```java
Predicate<Integer> isPrime = n -> n > 1 &&
    IntStream.rangeClosed(2, (int)Math.sqrt(n)).noneMatch(d -> n % d == 0);

Map<Boolean, List<Integer>> parts =
    nums.stream().collect(Collectors.partitioningBy(isPrime));
```

---

### 7) List<List<T>> को flatten karke unique, sorted set

**Question:** Nested list ko set me convert karo (sorted, distinct).
**Solution:**

```java
Set<Integer> result =
    listOfLists.stream()
               .flatMap(List::stream)
               .collect(Collectors.toCollection(() -> new TreeSet<>()));
```

---

### 8) BigDecimal total amount (double pitfalls से बचो)

**Question:** `List<Order>` → total `BigDecimal`.
**Solution:**

```java
BigDecimal total =
    orders.stream()
          .map(Order::getAmount)               // BigDecimal
          .reduce(BigDecimal.ZERO, BigDecimal::add);
```

---

### 9) Status-wise amount sum (downstream collector)

**Question:** Orders ko `status` se group karke amount sum chahiye.
**Solution (BigDecimal-safe):**

```java
Map<Status, BigDecimal> byStatus =
    orders.stream().collect(Collectors.groupingBy(
        Order::getStatus,
        Collectors.reducing(BigDecimal.ZERO, Order::getAmount, BigDecimal::add)
    ));
```

`groupingBy + reducing`.

---

### 10) Retry logic with Supplier (max 3 tries)

**Question:** API/DB call ko 3 बार तक retry karna hai.
**Solution:**

```java
<T> T retry(Supplier<T> op, int max) {
    RuntimeException last = null;
    for (int i = 0; i < max; i++) {
        try { return op.get(); }
        catch (RuntimeException e) { last = e; }
    }
    throw last;
}

// usage
String data = retry(() -> api.fetchData(), 3);
```

---

### 11) Conditional logger as Consumer (debug ON/OFF)

**Question:** Debug flag ke hisaab se logger active ya no-op.
**Solution:**

```java
Consumer<String> logger = debugEnabled
    ? msg -> System.out.println("[DEBUG] " + msg)
    : msg -> {}; // no-op

list.forEach(logger);
```

---

### 12) `toMap` with duplicate keys (merge rule)

**Question:** `List<Employee>` ko `Map<dept, count>`; duplicates handle karo.
**Solution:**

```java
Map<String, Integer> deptCount =
    employees.stream().collect(Collectors.toMap(
        Employee::getDept,
        e -> 1,
        Integer::sum
    ));
```

Merge function: `Integer::sum`.

---

### 13) Multi-level sort (dept asc, salary desc, name asc)

**Question:** Complex comparator chain.
**Solution:**

```java
Comparator<Employee> cmp =
    Comparator.comparing(Employee::getDept)
              .thenComparing(Employee::getSalary, Comparator.reverseOrder())
              .thenComparing(Employee::getName);

List<Employee> sorted = employees.stream().sorted(cmp).toList();
```

---

### 14) `collectingAndThen` – unmodifiable list of top N

**Question:** Top-5 by score, unmodifiable list.
**Solution:**

```java
List<User> top5 =
    users.stream()
         .sorted(Comparator.comparing(User::getScore).reversed())
         .limit(5)
         .collect(Collectors.collectingAndThen(
             Collectors.toList(),
             Collections::unmodifiableList
         ));
```

---

### 15) Map merge API – word count without collectors

**Question:** `String text` → word frequency via `Map.merge`.
**Solution:**

```java
Map<String,Integer> wc = new HashMap<>();
Arrays.stream(text.split("\\W+"))
      .filter(s -> !s.isEmpty())
      .map(String::toLowerCase)
      .forEach(w -> wc.merge(w, 1, Integer::sum));
```
