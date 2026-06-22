# Index
1. [Intermediate Operations](#intermediate-operations)
2. [Terminal Operations](#terminal-operations-list)
3. [Collector](#collectors)
4. [Comparator](#comparator-for-sorting)
5. [Mathematical Operations](#imp-mathematical-operations)
6. [Random Numbers](#random-number-generations)
7. [Conversions](#conversions)
8. [Example](#example)
9. [Final Note](#final-note)
10. [Full Stream vs Group of Elements](#terminal-vs-downstream-collector)
11. [Comparison](#comparison)

---

# Intermediate Operations

* They return **Stream**
* They are **lazy**
* They don’t execute until a terminal operation is called

---

# ✅ 1. Filtering Operations

### 1️⃣ `filter`

```java
Stream<T> filter(Predicate<? super T> predicate)
```

* Functional Interface: `Predicate<T>`
* Purpose: Select elements based on condition

Example:

```java
stream.filter(x -> x > 10)

List<Integer> numbers = Arrays.asList(5, 10, 15, 20, 25);

List<Integer> result = numbers.stream()
        .filter(x -> x > 10)
        .collect(Collectors.toList());

System.out.println(result);

[15, 20, 25]
```

---

# ✅ 2. Mapping Operations

### 2️⃣ `map`

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper)

List<String> names = Arrays.asList("danish", "java", "spring");

List<String> upper = names.stream()
        .map(String::toUpperCase)
        .collect(Collectors.toList());

System.out.println(upper);
[DANISH, JAVA, SPRING]
```

* Functional Interface: `Function<T, R>`
* Purpose: Transform each element

---

### 3️⃣ `mapToInt`

```java
IntStream mapToInt(ToIntFunction<? super T> mapper)

List<String> names = Arrays.asList("Java", "Spring", "Docker");

int totalLength = names.stream()
        .mapToInt(String::length)
        .sum();

System.out.println(totalLength);
18
```

### 4️⃣ `mapToLong`

```java
LongStream mapToLong(ToLongFunction<? super T> mapper)
```

### 5️⃣ `mapToDouble`

```java
DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper)
```

Used when converting to primitive streams.

---

# ✅ 3. Flat Mapping

### 6️⃣ `flatMap`

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)

List<List<String>> data = Arrays.asList(
        Arrays.asList("Java", "Spring"),
        Arrays.asList("Docker", "Kubernetes")
);

List<String> result = data.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(result);
[Java, Spring, Docker, Kubernetes]
```

Used to flatten nested structures.

---

### 7️⃣ `flatMapToInt`

```java
IntStream flatMapToInt(Function<? super T, ? extends IntStream> mapper)
```

### 8️⃣ `flatMapToLong`

```java
LongStream flatMapToLong(Function<? super T, ? extends LongStream> mapper)
```

### 9️⃣ `flatMapToDouble`

```java
DoubleStream flatMapToDouble(Function<? super T, ? extends DoubleStream> mapper)
```

---

# ✅ 4. Sorting & Distinct

### 🔟 `sorted()`

```java
Stream<T> sorted()

List<Integer> nums = Arrays.asList(5, 1, 9, 3);

nums.stream()
        .sorted()
        .forEach(System.out::println);

1
3
5
9        
```

Requires elements to implement `Comparable`

---

### 1️⃣1️⃣ `sorted(Comparator)`

```java
Stream<T> sorted(Comparator<? super T> comparator)

List<String> names = Arrays.asList("Danish", "Java", "SpringBoot");

names.stream()
        .sorted((a, b) -> b.length() - a.length())
        .forEach(System.out::println);

SpringBoot
Danish
Java        
```

---

### 1️⃣2️⃣ `distinct()`

```java
Stream<T> distinct()

List<Integer> nums = Arrays.asList(1,2,2,3,4,4,5);

nums.stream()
        .distinct()
        .forEach(System.out::println);

1 2 3 4 5        
```

Removes duplicates (uses `equals()`)

---

# ✅ 5. Limiting Operations

### 1️⃣3️⃣ `limit`

```java
Stream<T> limit(long maxSize)

Stream.iterate(1, n -> n + 1)
        .limit(5)
        .forEach(System.out::println);
1 2 3 4 5        
```

---

### 1️⃣4️⃣ `skip`

```java
Stream<T> skip(long n)

List<Integer> nums = Arrays.asList(10,20,30,40,50);

nums.stream()
        .skip(2)
        .forEach(System.out::println);
30 40 50        
```

---

# ✅ 6. Peeking

### 1️⃣5️⃣ `peek`

```java
Stream<T> peek(Consumer<? super T> action)

List<String> names = Arrays.asList("java", "spring");

names.stream()
        .peek(x -> System.out.println("Before: " + x))
        .map(String::toUpperCase)
        .peek(x -> System.out.println("After: " + x))
        .collect(Collectors.toList());
```

* Functional Interface: `Consumer<T>`
* Used mainly for debugging

---

# ✅ 7. Parallel / Sequential (State Change)

### 1️⃣6️⃣ `parallel`

```java
Stream<T> parallel()
```

---

### 1️⃣7️⃣ `sequential`

```java
Stream<T> sequential()
```

---

### 1️⃣8️⃣ `unordered`

```java
Stream<T> unordered()
```

---

# ✅ 8. Java 9+ Intermediate Operations

(Important if interview mentions Java 9+)

### 1️⃣9️⃣ `takeWhile`

```java
Stream<T> takeWhile(Predicate<? super T> predicate)

List<Integer> nums = Arrays.asList(1,2,3,4,1,2);
nums.stream()
        .takeWhile(x -> x < 4)
        .forEach(System.out::println);
1 2 3        
```

---

### 2️⃣0️⃣ `dropWhile`

```java
Stream<T> dropWhile(Predicate<? super T> predicate)

List<Integer> nums = Arrays.asList(1,2,3,4,1,2);
nums.stream()
        .dropWhile(x -> x < 4)
        .forEach(System.out::println);
4 1 2        
```

---

# 🎯 Final Clean List (Quick Revision Table)

| Operation          | Parameter Type        |
| ------------------ | --------------------- |
| filter             | Predicate             |
| map                | Function              |
| mapToInt           | ToIntFunction         |
| mapToLong          | ToLongFunction        |
| mapToDouble        | ToDoubleFunction      |
| flatMap            | Function              |
| flatMapToInt       | Function              |
| flatMapToLong      | Function              |
| flatMapToDouble    | Function              |
| sorted             | Comparator (optional) |
| distinct           | —                     |
| limit              | long                  |
| skip               | long                  |
| peek               | Consumer              |
| parallel           | —                     |
| sequential         | —                     |
| unordered          | —                     |
| takeWhile (Java 9) | Predicate             |
| dropWhile (Java 9) | Predicate             |

---

If you want next level mastery, I can now give you:

* 🔥 All terminal operations list
* 🔥 Which are stateless vs stateful
* 🔥 Interview trick questions
* 🔥 Real-world microservice examples using Streams

Tell me how deep you want to go.

Perfect. If you know terminal operations properly, you control Streams in interviews.

# Terminal operations List

* Trigger execution
* Return a non-stream result
* Close the stream

I’ll give you:

* ✔ Method signature
* ✔ Parameter type
* ✔ Return type
* ✔ Short purpose

---

# ✅ 1️⃣ Iteration / Side-Effect Operations

### 🔹 `forEach`

```java
void forEach(Consumer<? super T> action)

List<String> names = Arrays.asList("Java", "Docker");
names.forEach(System.out::println);
```

* Parameter: `Consumer<T>`
* Return type: `void`
* Use: Perform action on each element

---

### 🔹 `forEachOrdered`

```java
void forEachOrdered(Consumer<? super T> action)
```

* Parameter: `Consumer<T>`
* Return type: `void`
* Use: Maintains encounter order (important in parallel streams)

---

# ✅ 2️⃣ Collecting

### 🔹 `collect` (Most Important)

```java
<R> R collect(Collector<? super T, A, R> collector)

List<String> names = Arrays.asList("Java", "Spring");
List<String> result = names.stream()
        .collect(Collectors.toList());
System.out.println(result);
```

* Parameter: `Collector`
* Return type: `R`
* Use: Convert stream into List, Set, Map, String, etc.

Example:

```java
List<String> list = stream.collect(Collectors.toList());
```

---

### 🔹 `collect` (3-argument version)

```java
<R> R collect(Supplier<R> supplier,
              BiConsumer<R, ? super T> accumulator,
              BiConsumer<R, R> combiner)
```

* Parameters:

  * `Supplier`
  * `BiConsumer`
  * `BiConsumer`
* Return type: `R`
* Use: Custom mutable reduction

Interview tip: Rarely used directly.

---

# ✅ 3️⃣ Reduction Operations

### 🔹 `reduce` (1-arg)

```java
Optional<T> reduce(BinaryOperator<T> accumulator)

List<Integer> nums = Arrays.asList(1,2,3,4);

int sum = nums.stream()
        .reduce(0, Integer::sum);

System.out.println(sum);
10
```

* Parameter: `BinaryOperator<T>`
* Return type: `Optional<T>`

---

### 🔹 `reduce` (2-arg)

```java
T reduce(T identity, BinaryOperator<T> accumulator)
```

* Parameters:

  * Identity value
  * `BinaryOperator`
* Return type: `T`

---

### 🔹 `reduce` (3-arg)

```java
<U> U reduce(U identity,
             BiFunction<U, ? super T, U> accumulator,
             BinaryOperator<U> combiner)
```

* Return type: `U`
* Used mostly in parallel streams

---

# ✅ 4️⃣ Matching Operations

### 🔹 `anyMatch`

```java
boolean anyMatch(Predicate<? super T> predicate)

boolean result = Arrays.asList(1,2,3,4)
        .stream()
        .anyMatch(x -> x > 3);
System.out.println(result);
true
```

---

### 🔹 `allMatch`

```java
boolean allMatch(Predicate<? super T> predicate)

boolean result = Arrays.asList(2,4,6)
        .stream()
        .allMatch(x -> x % 2 == 0);
System.out.println(result);
true
```

---

### 🔹 `noneMatch`

```java
boolean noneMatch(Predicate<? super T> predicate)

boolean result = Arrays.asList(1,3,5)
        .stream()
        .noneMatch(x -> x % 2 == 0);
System.out.println(result);
true
```

All return `boolean`.

---

# ✅ 5️⃣ Finding Operations

### 🔹 `findFirst`

```java
Optional<T> findFirst()

Optional<Integer> result = Arrays.asList(5,10,15)
        .stream()
        .findFirst();
System.out.println(result.get());
5
```

---

### 🔹 `findAny`

```java
Optional<T> findAny()

Optional<Integer> result = Arrays.asList(5,10,15)
        .parallelStream()
        .findAny();
```

Return type: `Optional<T>`

---

# ✅ 6️⃣ Counting

### 🔹 `count`

```java
long count()

long count = Arrays.asList(1,2,3,4)
        .stream()
        .count();
System.out.println(count);
```

Return type: `long`

---

# ✅ 7️⃣ Min / Max

### 🔹 `min`

```java
Optional<T> min(Comparator<? super T> comparator)

List<Integer> nums = Arrays.asList(10,5,30,2);
int min = nums.stream().min(Integer::compare).get();
int max = nums.stream().max(Integer::compare).get();
System.out.println(min);
System.out.println(max);
```

---

### 🔹 `max`

```java
Optional<T> max(Comparator<? super T> comparator)
```

Return type: `Optional<T>`

---

# ✅ 8️⃣ Primitive Stream Terminal Operations

These belong to `IntStream`, `LongStream`, `DoubleStream`.

---

### 🔹 `sum`

```java
int sum()        // IntStream
long sum()       // LongStream
double sum()     // DoubleStream
```

---

### 🔹 `average`

```java
OptionalDouble average()     // IntStream, LongStream, DoubleStream
```

---

### 🔹 `summaryStatistics`

```java
IntSummaryStatistics summaryStatistics()
LongSummaryStatistics summaryStatistics()
DoubleSummaryStatistics summaryStatistics()
```

---

# 🎯 Clean Revision Table

| Method            | Parameter                              | Return Type       |
| ----------------- | -------------------------------------- | ----------------- |
| forEach           | Consumer                               | void              |
| forEachOrdered    | Consumer                               | void              |
| collect           | Collector                              | R                 |
| reduce (1)        | BinaryOperator                         | Optional<T>       |
| reduce (2)        | identity + BinaryOperator              | T                 |
| reduce (3)        | identity + BiFunction + BinaryOperator | U                 |
| anyMatch          | Predicate                              | boolean           |
| allMatch          | Predicate                              | boolean           |
| noneMatch         | Predicate                              | boolean           |
| findFirst         | —                                      | Optional<T>       |
| findAny           | —                                      | Optional<T>       |
| count             | —                                      | long              |
| min               | Comparator                             | Optional<T>       |
| max               | Comparator                             | Optional<T>       |
| sum               | —                                      | primitive         |
| average           | —                                      | OptionalDouble    |
| summaryStatistics | —                                      | SummaryStatistics |

---

If you're preparing for interviews seriously, next you should master:

* 🔥 Difference between `collect()` and `reduce()`
* 🔥 When reduce breaks in parallel
* 🔥 Short-circuit terminal operations
* 🔥 Interview traps with Optional

Tell me your target level — service company or product-based — and I’ll tailor it.

Excellent. This is **very important for interviews**, especially if you're targeting product-based companies.

We’ll cover **Java 8 Collectors methods only** (from `java.util.stream.Collectors`).

I’ll give you:

* ✔ Method signature
* ✔ Parameters
* ✔ Return type
* ✔ What it produces

---
# Collectors

## 🔥 1️⃣ Basic Collection Collectors

---

### 🔹 `toList()`

```java
public static <T> Collector<T, ?, List<T>> toList()
```

* Parameters: None
* Returns: `Collector<T, ?, List<T>>`
* Produces: `List<T>`

---

### 🔹 `toSet()`

```java
public static <T> Collector<T, ?, Set<T>> toSet()
```

* Returns: `Collector<T, ?, Set<T>>`
* Produces: `Set<T>`

---

### 🔹 `toCollection()`

```java
public static <T, C extends Collection<T>>
Collector<T, ?, C> toCollection(Supplier<C> collectionFactory)
```

* Parameter: `Supplier<C>`
* Returns: `Collector<T, ?, C>`
* Produces: Custom collection (e.g., LinkedList)

---

# 🔥 2️⃣ Mapping Collector

---

### 🔹 `mapping()`

```java
public static <T, U, A, R>
Collector<T, ?, R> mapping(Function<? super T, ? extends U> mapper,
                           Collector<? super U, A, R> downstream)
```

* Parameters:

  * `Function`
  * Downstream Collector
* Returns: `Collector<T, ?, R>`

Used inside `groupingBy`.

---

# 🔥 3️⃣ Joining

---

### 🔹 `joining()`

```java
public static Collector<CharSequence, ?, String> joining()

String result = Arrays.asList("Java", "Spring", "Docker")
        .stream()
        .collect(Collectors.joining(","));
System.out.println(result);
Java,Spring,Docker
```

---

### 🔹 `joining(CharSequence delimiter)`

```java
public static Collector<CharSequence, ?, String>
joining(CharSequence delimiter)
```

---

### 🔹 `joining(delimiter, prefix, suffix)`

```java
public static Collector<CharSequence, ?, String>
joining(CharSequence delimiter,
        CharSequence prefix,
        CharSequence suffix)
```

Returns: `Collector<CharSequence, ?, String>`

---

# 🔥 4️⃣ Counting

---

### 🔹 `counting()`

```java
public static <T> Collector<T, ?, Long> counting()
```

Returns: `Collector<T, ?, Long>`

---

# 🔥 5️⃣ Summing Collectors

---

### 🔹 `summingInt`

```java
public static <T> Collector<T, ?, Integer>
summingInt(ToIntFunction<? super T> mapper)
```

---

### 🔹 `summingLong`

```java
public static <T> Collector<T, ?, Long>
summingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `summingDouble`

```java
public static <T> Collector<T, ?, Double>
summingDouble(ToDoubleFunction<? super T> mapper)
```

---

# 🔥 6️⃣ Averaging

---

### 🔹 `averagingInt`

```java
public static <T> Collector<T, ?, Double>
averagingInt(ToIntFunction<? super T> mapper)
```

---

### 🔹 `averagingLong`

```java
public static <T> Collector<T, ?, Double>
averagingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `averagingDouble`

```java
public static <T> Collector<T, ?, Double>
averagingDouble(ToDoubleFunction<? super T> mapper)
```

---

# 🔥 7️⃣ Summary Statistics

---

### 🔹 `summarizingInt`

```java
public static <T> Collector<T, ?, IntSummaryStatistics>
summarizingInt(ToIntFunction<? super T> mapper)

IntSummaryStatistics stats =
Arrays.asList(10,20,30,40)
        .stream()
        .collect(Collectors.summarizingInt(x -> x));
System.out.println(stats.getSum());
System.out.println(stats.getAverage());
System.out.println(stats.getMax());
```

---

### 🔹 `summarizingLong`

```java
public static <T> Collector<T, ?, LongSummaryStatistics>
summarizingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `summarizingDouble`

```java
public static <T> Collector<T, ?, DoubleSummaryStatistics>
summarizingDouble(ToDoubleFunction<? super T> mapper)
```

---

# 🔥 8️⃣ Min / Max Collectors

---

### 🔹 `minBy`

```java
public static <T> Collector<T, ?, Optional<T>>
minBy(Comparator<? super T> comparator)
```

---

### 🔹 `maxBy`

```java
public static <T> Collector<T, ?, Optional<T>>
maxBy(Comparator<? super T> comparator)
```

---

# 🔥 9️⃣ Reducing Collectors

---

### 🔹 `reducing(BinaryOperator)`

```java
public static <T>
Collector<T, ?, Optional<T>>
reducing(BinaryOperator<T> op)
```

---

### 🔹 `reducing(identity, BinaryOperator)`

```java
public static <T>
Collector<T, ?, T>
reducing(T identity, BinaryOperator<T> op)
```

---

### 🔹 `reducing(identity, mapper, BinaryOperator)`

```java
public static <T, U>
Collector<T, ?, U>
reducing(U identity,
         Function<? super T, ? extends U> mapper,
         BinaryOperator<U> op)
```

---

# 🔥 🔟 Grouping Collectors (VERY IMPORTANT)

---

### 🔹 `groupingBy(classifier)`

```java
public static <T, K>
Collector<T, ?, Map<K, List<T>>>
groupingBy(Function<? super T, ? extends K> classifier)

Map<String, List<Employee>> result =
employees.stream()
.collect(Collectors.groupingBy(Employee::getDepartment));
```

---

### 🔹 `groupingBy(classifier, downstream)`

```java
public static <T, K, A, D>
Collector<T, ?, Map<K, D>>
groupingBy(Function<? super T, ? extends K> classifier,
           Collector<? super T, A, D> downstream)
```

---

### 🔹 `groupingBy(classifier, mapFactory, downstream)`

```java
public static <T, K, D, A, M extends Map<K, D>>
Collector<T, ?, M>
groupingBy(Function<? super T, ? extends K> classifier,
           Supplier<M> mapFactory,
           Collector<? super T, A, D> downstream)
```

---

# 🔥 1️⃣1️⃣ Partitioning

---

### 🔹 `partitioningBy(predicate)`

```java
public static <T>
Collector<T, ?, Map<Boolean, List<T>>>
partitioningBy(Predicate<? super T> predicate)

Map<Boolean, List<Integer>> result =
Arrays.asList(1,2,3,4,5,6)
        .stream()
        .collect(Collectors.partitioningBy(x -> x % 2 == 0));
System.out.println(result);
{false=[1, 3, 5], true=[2, 4, 6]}
```

---

### 🔹 `partitioningBy(predicate, downstream)`

```java
public static <T, D, A>
Collector<T, ?, Map<Boolean, D>>
partitioningBy(Predicate<? super T> predicate,
               Collector<? super T, A, D> downstream)
```

---

# 🔥 1️⃣2️⃣ toMap (VERY COMMON)

---

### 🔹 `toMap(keyMapper, valueMapper)`

```java
public static <T, K, U>
Collector<T, ?, Map<K, U>>
toMap(Function<? super T, ? extends K> keyMapper,
      Function<? super T, ? extends U> valueMapper)

Map<Integer, String> map =
Arrays.asList("Java", "Spring", "Docker")
        .stream()
        .collect(Collectors.toMap(
                String::length,
                x -> x,
                (a, b) -> a
        ));
System.out.println(map);      
```

---

### 🔹 `toMap(keyMapper, valueMapper, mergeFunction)`

```java
public static <T, K, U>
Collector<T, ?, Map<K, U>>
toMap(Function<? super T, ? extends K> keyMapper,
      Function<? super T, ? extends U> valueMapper,
      BinaryOperator<U> mergeFunction)
```

---

### 🔹 `toMap(keyMapper, valueMapper, mergeFunction, mapFactory)`

```java
public static <T, K, U, M extends Map<K, U>>
Collector<T, ?, M>
toMap(Function<? super T, ? extends K> keyMapper,
      Function<? super T, ? extends U> valueMapper,
      BinaryOperator<U> mergeFunction,
      Supplier<M> mapFactory)
```

---

# 🎯 Final Interview Insight

The **most asked collectors in interviews**:

* groupingBy
* partitioningBy
* toMap (especially duplicate key handling)
* summarizingInt
* joining
* reducing

---

If you want, I can now give you:

* 🔥 Collector hierarchy explanation (A, R, ? meaning)
* 🔥 Real interview tricky problems using groupingBy
* 🔥 20 advanced stream practice questions
* 🔥 Stream vs parallel stream deep comparison

Tell me your target level.

Good. Comparator in Java 8 is a **very common interview topic**, especially because of static + default methods added in Java 8.

We’ll cover:

* ✔ Static factory methods
* ✔ Default methods
* ✔ Method parameters
* ✔ Return types

All from `java.util.Comparator`

---
# Comparator for sorting

## 🔥 1️⃣ Static Factory Methods (Java 8)

---

### 🔹 `naturalOrder()`

```java
public static <T extends Comparable<? super T>>
Comparator<T> naturalOrder()
```

* Parameters: None
* Return: `Comparator<T>`
* Use: Ascending order based on `Comparable`

---

### 🔹 `reverseOrder()`

```java
public static <T extends Comparable<? super T>>
Comparator<T> reverseOrder()
```

* Return: `Comparator<T>`
* Use: Descending natural order

---

### 🔹 `comparing()`

```java
public static <T, U extends Comparable<? super U>>
Comparator<T> comparing(Function<? super T, ? extends U> keyExtractor)

employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary))
        .forEach(System.out::println);
```
<mark>keyExtractor means Bean me 2 fields hai aur kisi ek field ke basis pe sorting karna hai to wo pass hogi yaha.</mark>

* Parameter: `Function<T, U>`
* Return: `Comparator<T>`
* Use: Compare using extracted key

---

### 🔹 `comparing()` with custom Comparator

```java
public static <T, U>
Comparator<T> comparing(Function<? super T, ? extends U> keyExtractor,
                         Comparator<? super U> keyComparator)
```

* Parameters:

  * `Function`
  * `Comparator`
* Return: `Comparator<T>`

---

### 🔹 `comparingInt()`

```java
public static <T>
Comparator<T> comparingInt(ToIntFunction<? super T> keyExtractor)
```

* Parameter: `ToIntFunction`
* Return: `Comparator<T>`

---

### 🔹 `comparingLong()`

```java
public static <T>
Comparator<T> comparingLong(ToLongFunction<? super T> keyExtractor)
```

---

### 🔹 `comparingDouble()`

```java
public static <T>
Comparator<T> comparingDouble(ToDoubleFunction<? super T> keyExtractor)
```

---

# 🔥 2️⃣ Default Methods (Instance Methods)

---

### 🔹 `reversed()`

```java
default Comparator<T> reversed()
```

* Parameters: None
* Return: `Comparator<T>`
* Use: Reverse current comparator

---

### 🔹 `thenComparing()`

```java
default Comparator<T> thenComparing(Comparator<? super T> other)

employees.stream()
        .sorted(
                Comparator.comparing(Employee::getDepartment)
                .thenComparing(Employee::getSalary)
        )
        .forEach(System.out::println);
```

* Parameter: `Comparator`
* Return: `Comparator<T>`
* Use: Secondary sorting

---

### 🔹 `thenComparing(Function)`

```java
default <U extends Comparable<? super U>>
Comparator<T> thenComparing(Function<? super T, ? extends U> keyExtractor)
```

---

### 🔹 `thenComparing(Function, Comparator)`

```java
default <U>
Comparator<T> thenComparing(Function<? super T, ? extends U> keyExtractor,
                             Comparator<? super U> keyComparator)
```

---

### 🔹 `thenComparingInt()`

```java
default Comparator<T>
thenComparingInt(ToIntFunction<? super T> keyExtractor)
```

---

### 🔹 `thenComparingLong()`

```java
default Comparator<T>
thenComparingLong(ToLongFunction<? super T> keyExtractor)
```

---

### 🔹 `thenComparingDouble()`

```java
default Comparator<T>
thenComparingDouble(ToDoubleFunction<? super T> keyExtractor)
```

---

# 🔥 3️⃣ Instance Method (From Comparator Interface)

---

### 🔹 `compare()`

```java
int compare(T o1, T o2)
```

* Parameters: Two objects
* Return: `int`

  * negative → o1 < o2
  * zero → equal
  * positive → o1 > o2

This is the **only abstract method** (functional interface).

---

# 🎯 Complete Clean Summary Table

| Method                              | Parameter             | Return Type   |
| ----------------------------------- | --------------------- | ------------- |
| compare                             | T, T                  | int           |
| naturalOrder                        | —                     | Comparator<T> |
| reverseOrder                        | —                     | Comparator<T> |
| comparing                           | Function              | Comparator<T> |
| comparing (2 arg)                   | Function + Comparator | Comparator<T> |
| comparingInt                        | ToIntFunction         | Comparator<T> |
| comparingLong                       | ToLongFunction        | Comparator<T> |
| comparingDouble                     | ToDoubleFunction      | Comparator<T> |
| reversed                            | —                     | Comparator<T> |
| thenComparing                       | Comparator            | Comparator<T> |
| thenComparing(Function)             | Function              | Comparator<T> |
| thenComparing(Function, Comparator) | Function + Comparator | Comparator<T> |
| thenComparingInt                    | ToIntFunction         | Comparator<T> |
| thenComparingLong                   | ToLongFunction        | Comparator<T> |
| thenComparingDouble                 | ToDoubleFunction      | Comparator<T> |

---

# 🔥 Interview Power Tips

1. `Comparator` is a **functional interface** (because of `compare()`).
2. Java 8 made it powerful using:

   * Static methods
   * Default methods
3. `comparingInt()` is faster than `comparing()` for primitives (no boxing).

---

If you want, I can now give you:

* 🔥 Comparable vs Comparator deep difference
* 🔥 Real interview sorting problems
* 🔥 How sorting works internally (TimSort)
* 🔥 Advanced chaining examples for product companies

Tell me your preparation level.
Excellent. Now we are talking about **mathematical operations specifically in Java 8 Streams** — mainly from:

* `IntStream`
* `LongStream`
* `DoubleStream`
* `Stream<T>` (via reduce / collectors)

I’ll organize this properly:

1. ✔ Primitive Stream Math Operations
2. ✔ Reduce-based Math
3. ✔ Summary Statistics
4. ✔ Collector-based Math

This is exactly how interviewers think.

---

# Imp Mathematical Operations 

## 🔥 1️⃣ IntStream Mathematical Terminal Operations

From `java.util.stream.IntStream`

---

### 🔹 `sum()`

```java
int sum()

int sum = IntStream.of(1,2,3,4).sum();
System.out.println(sum);
```

Returns: `int`
Adds all elements

---

### 🔹 `average()`

```java
OptionalDouble average()

double avg = IntStream.of(10,20,30)
        .average()
        .getAsDouble();
System.out.println(avg);
```

Returns: `OptionalDouble`

---

### 🔹 `min()`

```java
OptionalInt min()
```

---

### 🔹 `max()`

```java
OptionalInt max()
```

---

### 🔹 `count()`

```java
long count()
```

---

### 🔹 `summaryStatistics()`

```java
IntSummaryStatistics summaryStatistics()
```

Returns: `IntSummaryStatistics`
Contains:

* getCount()
* getSum()
* getMin()
* getMax()
* getAverage()

---

# 🔥 2️⃣ LongStream Mathematical Operations

From `LongStream`

---

### 🔹 `sum()`

```java
long sum()
```

---

### 🔹 `average()`

```java
OptionalDouble average()
```

---

### 🔹 `min()`

```java
OptionalLong min()
```

---

### 🔹 `max()`

```java
OptionalLong max()
```

---

### 🔹 `summaryStatistics()`

```java
LongSummaryStatistics summaryStatistics()
```

---

# 🔥 3️⃣ DoubleStream Mathematical Operations

From `DoubleStream`

---

### 🔹 `sum()`

```java
double sum()
```

---

### 🔹 `average()`

```java
OptionalDouble average()
```

---

### 🔹 `min()`

```java
OptionalDouble min()
```

---

### 🔹 `max()`

```java
OptionalDouble max()
```

---

### 🔹 `summaryStatistics()`

```java
DoubleSummaryStatistics summaryStatistics()
```

---

# 🔥 4️⃣ Reduce-Based Mathematical Operations (Generic Stream)

From `Stream<T>`

---

### 🔹 `reduce(BinaryOperator)`

```java
Optional<T> reduce(BinaryOperator<T> accumulator)
```

Example:

```java
stream.reduce((a, b) -> a + b);
```

---

### 🔹 `reduce(identity, BinaryOperator)`

```java
T reduce(T identity, BinaryOperator<T> accumulator)
```

---

### 🔹 `reduce(identity, accumulator, combiner)`

```java
<U> U reduce(U identity,
             BiFunction<U, ? super T, U> accumulator,
             BinaryOperator<U> combiner)
```

Used mainly in parallel streams.

---

# 🔥 5️⃣ Mathematical Collectors (Java 8)

From `Collectors`

---

### 🔹 `summingInt()`

```java
Collector<T, ?, Integer>
summingInt(ToIntFunction<? super T> mapper)
```

---

### 🔹 `summingLong()`

```java
Collector<T, ?, Long>
summingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `summingDouble()`

```java
Collector<T, ?, Double>
summingDouble(ToDoubleFunction<? super T> mapper)
```

---

### 🔹 `averagingInt()`

```java
Collector<T, ?, Double>
averagingInt(ToIntFunction<? super T> mapper)
```

---

### 🔹 `averagingLong()`

```java
Collector<T, ?, Double>
averagingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `averagingDouble()`

```java
Collector<T, ?, Double>
averagingDouble(ToDoubleFunction<? super T> mapper)
```

---

### 🔹 `counting()`

```java
Collector<T, ?, Long> counting()
```

---

### 🔹 `minBy()`

```java
Collector<T, ?, Optional<T>>
minBy(Comparator<? super T> comparator)
```

---

### 🔹 `maxBy()`

```java
Collector<T, ?, Optional<T>>
maxBy(Comparator<? super T> comparator)
```

---

### 🔹 `summarizingInt()`

```java
Collector<T, ?, IntSummaryStatistics>
summarizingInt(ToIntFunction<? super T> mapper)
```

---

### 🔹 `summarizingLong()`

```java
Collector<T, ?, LongSummaryStatistics>
summarizingLong(ToLongFunction<? super T> mapper)
```

---

### 🔹 `summarizingDouble()`

```java
Collector<T, ?, DoubleSummaryStatistics>
summarizingDouble(ToDoubleFunction<? super T> mapper)
```

---

# 🎯 Final Clean Revision Table

| Operation           | Parameter      | Return Type             |
| ------------------- | -------------- | ----------------------- |
| sum()               | —              | int/long/double         |
| average()           | —              | OptionalDouble          |
| min()               | —              | OptionalInt/Long/Double |
| max()               | —              | OptionalInt/Long/Double |
| count()             | —              | long                    |
| summaryStatistics() | —              | SummaryStatistics       |
| reduce()            | BinaryOperator | Optional<T>             |
| summingInt          | ToIntFunction  | Integer                 |
| averagingInt        | ToIntFunction  | Double                  |
| counting            | —              | Long                    |
| minBy               | Comparator     | Optional<T>             |

---

# 🔥 Interview Insight

Primitive streams (`IntStream`, `LongStream`, `DoubleStream`) are faster than `Stream<Integer>` because:

* No boxing/unboxing
* Specialized mathematical operations
* Optimized internal implementations

---

If you're preparing seriously, next you should understand:

* 🔥 When to use reduce vs sum
* 🔥 Why average returns OptionalDouble
* 🔥 Parallel stream math pitfalls
* 🔥 Real-world microservice examples using streams

Tell me — is this for interview prep or for your YouTube content?
Excellent. Now we’re talking about **random value generation using Java 8 Stream API**.

In Java 8, random streams are mainly created using:

* `java.util.Random`
* `java.util.concurrent.ThreadLocalRandom`
* `java.util.SplittableRandom` (Java 8 addition, very important for parallel)

I’ll give you:

* ✔ Method signature
* ✔ Parameters
* ✔ Return type
* ✔ What it generates

---

# Random Number Generations

## 🔥 1️⃣ Random Class (java.util.Random)

## ✅ A. Infinite Random Streams

---

### 🔹 `ints()`

```java
IntStream ints()

new Random()
        .ints(5, 1, 10)
        .forEach(System.out::println);
```

* Parameters: None
* Return: `IntStream`
* Generates: Infinite random integers

---

### 🔹 `ints(int origin, int bound)`

```java
IntStream ints(int origin, int bound)
```

* Parameters:

  * `origin` (inclusive)
  * `bound` (exclusive)
* Return: `IntStream`

---

### 🔹 `longs()`

```java
LongStream longs()
```

---

### 🔹 `longs(long origin, long bound)`

```java
LongStream longs(long origin, long bound)
```

---

### 🔹 `doubles()`

```java
DoubleStream doubles()
```

* Generates values between 0.0 (inclusive) and 1.0 (exclusive)

---

### 🔹 `doubles(double origin, double bound)`

```java
DoubleStream doubles(double origin, double bound)
```

---

# ✅ B. Finite Random Streams

---

### 🔹 `ints(long streamSize)`

```java
IntStream ints(long streamSize)
```

* Parameter: streamSize
* Return: `IntStream`

---

### 🔹 `ints(long streamSize, int origin, int bound)`

```java
IntStream ints(long streamSize, int origin, int bound)
```

---

### 🔹 `longs(long streamSize)`

```java
LongStream longs(long streamSize)
```

---

### 🔹 `longs(long streamSize, long origin, long bound)`

```java
LongStream longs(long streamSize,
                 long origin,
                 long bound)
```

---

### 🔹 `doubles(long streamSize)`

```java
DoubleStream doubles(long streamSize)
```

---

### 🔹 `doubles(long streamSize, double origin, double bound)`

```java
DoubleStream doubles(long streamSize,
                     double origin,
                     double bound)
```

---

# 🔥 2️⃣ ThreadLocalRandom (Recommended in Multithreaded)

From `java.util.concurrent.ThreadLocalRandom`

All methods are same as `Random`, but accessed via:

```java
ThreadLocalRandom.current()
```

Example:

```java
IntStream ints(int origin, int bound)
```

Return types:

* `IntStream`
* `LongStream`
* `DoubleStream`

⚡ Used in multi-threaded environments.

---

# 🔥 3️⃣ SplittableRandom (Best for Parallel Streams)

From `java.util.SplittableRandom`

Very important for high-performance parallel computation.

---

### 🔹 `ints()`

```java
IntStream ints()
```

---

### 🔹 `ints(long streamSize)`

```java
IntStream ints(long streamSize)
```

---

### 🔹 `ints(int origin, int bound)`

```java
IntStream ints(int origin, int bound)
```

---

### 🔹 `ints(long streamSize, int origin, int bound)`

```java
IntStream ints(long streamSize,
               int origin,
               int bound)
```

---

Same structure for:

* `longs(...) → LongStream`
* `doubles(...) → DoubleStream`

---

# 🔥 4️⃣ Stream.generate()

Generic random generation.

---

### 🔹 `generate()`

```java
static <T> Stream<T> generate(Supplier<T> s)
```

* Parameter: `Supplier<T>`
* Return: `Stream<T>`
* Infinite stream

Example:

```java
Stream.generate(() -> new Random().nextInt())
```

---

# 🔥 5️⃣ Stream.iterate() (Pseudo-Random Logic)

Not truly random, but can be used to generate number sequences.

---

### 🔹 Java 8 version

```java
static <T> Stream<T> iterate(T seed,
                              UnaryOperator<T> f)
```

* Return: `Stream<T>`

---

# 🎯 Complete Summary Table

| Method                          | Parameters          | Return Type  |
| ------------------------------- | ------------------- | ------------ |
| ints()                          | —                   | IntStream    |
| ints(origin, bound)             | int, int            | IntStream    |
| ints(streamSize)                | long                | IntStream    |
| ints(streamSize, origin, bound) | long, int, int      | IntStream    |
| longs(...)                      | similar variants    | LongStream   |
| doubles(...)                    | similar variants    | DoubleStream |
| Stream.generate                 | Supplier            | Stream<T>    |
| Stream.iterate                  | seed, UnaryOperator | Stream<T>    |

---

# 🔥 Interview Insight

* `Random` → basic use
* `ThreadLocalRandom` → multi-threaded
* `SplittableRandom` → parallel streams (best performance)
* Always use `limit()` with infinite streams

Example:

```java
new Random().ints(5, 1, 10)
           .forEach(System.out::println);
```

---
# Conversions
Perfect. These conversions are asked very frequently in Java 8 interviews, especially in:

* Streams
* Collections
* String processing
* Primitive Streams
* Optional
* Arrays
* Map/List conversions

I’ll give you the **most important conversion cheat sheet** in clean interview-notes style.

---

# Java 8 Important Conversions Cheat Sheet

# 1. `Stream<Integer>` → `IntStream`

## Using `mapToInt()`

```java
List<Integer> list = Arrays.asList(1,2,3,4);

IntStream intStream = list.stream()
                          .mapToInt(Integer::intValue);
```

## Real Use

For:

* sum()
* average()
* max()
* min()

---

# 2. `IntStream` → `Stream<Integer>`

## Using `boxed()`

```java
IntStream intStream = IntStream.of(1,2,3);

Stream<Integer> stream = intStream.boxed();
```

## Important

`boxed()` converts primitive → Wrapper Object

---

# 3. `Stream<String>` → `List<String>`

```java
List<String> list = stream.collect(Collectors.toList());
```

---

# 4. `List<String>` → `Stream<String>`

```java
Stream<String> stream = list.stream();
```

---

# 5. `Array` → `Stream`

## Object Array

```java
String[] arr = {"Java", "Spring"};

Stream<String> stream = Arrays.stream(arr);
```

---

# 6. `Stream` → `Array`

```java
String[] arr = stream.toArray(String[]::new);
```

---

# 7. `String` → `Stream<Character>`

## Important Interview Question

```java
String str = "JAVA";

Stream<Character> stream =
        str.chars()
           .mapToObj(c -> (char) c);
```

---

# 8. `String` → `IntStream`

```java
String str = "JAVA";

IntStream stream = str.chars();
```

## Returns ASCII values

Example:

```java
74 65 86 65
```

---

# 9. `IntStream` → `List<Integer>`

```java
List<Integer> list =
        IntStream.of(1,2,3)
                 .boxed()
                 .collect(Collectors.toList());
```

---

# 10. `List<Integer>` → `int[]`

```java
int[] arr = list.stream()
                .mapToInt(Integer::intValue)
                .toArray();
```

---

# 11. `int[]` → `List<Integer>`

```java
int[] arr = {1,2,3};

List<Integer> list =
        Arrays.stream(arr)
              .boxed()
              .collect(Collectors.toList());
```

---

# 12. `List<String>` → `String`

## joining()

```java
String result =
        list.stream()
            .collect(Collectors.joining(","));
```

Output:

```java
Java,Spring,Docker
```

---

# 13. `String` → `List<String>`

## split()

```java
String str = "Java,Spring,Docker";

List<String> list =
        Arrays.asList(str.split(","));
```

---

# 14. `Map` → `Stream`

## Entry Stream

```java
Map<Integer, String> map = new HashMap<>();

Stream<Map.Entry<Integer,String>> stream =
        map.entrySet().stream();
```

---

# 15. `Stream` → `Map`

```java
Map<Integer, String> map =
        list.stream()
            .collect(Collectors.toMap(
                    String::length,
                    x -> x
            ));
```

---

# 16. `Optional<T>` → `T`

```java
Optional<String> opt = Optional.of("Java");

String value = opt.get();
```

## Better Approach

```java
String value = opt.orElse("Default");
```

---

# 17. `T` → `Optional<T>`

```java
Optional<String> opt = Optional.of("Java");
```

Nullable:

```java
Optional<String> opt = Optional.ofNullable(value);
```

---

# 18. `Stream<String>` → `Set<String>`

```java
Set<String> set =
        stream.collect(Collectors.toSet());
```

---

# 19. `Set<String>` → `Stream<String>`

```java
Stream<String> stream = set.stream();
```

---

# 20. `LongStream` → `Stream<Long>`

```java
LongStream stream = LongStream.of(1,2,3);

Stream<Long> result = stream.boxed();
```

---

# 21. `DoubleStream` → `Stream<Double>`

```java
DoubleStream stream = DoubleStream.of(1.1,2.2);

Stream<Double> result = stream.boxed();
```

---

# 22. `Stream<Integer>` → `double`

## average()

```java
double avg =
        list.stream()
            .mapToInt(Integer::intValue)
            .average()
            .orElse(0);
```

---

# 23. `Stream<String>` → `Map<Integer, List<String>>`

## groupingBy()

```java
Map<Integer, List<String>> result =
        list.stream()
            .collect(Collectors.groupingBy(String::length));
```

---

# 24. `Nested List` → Single List

## flatMap()

```java
List<List<String>> nested = Arrays.asList(
        Arrays.asList("Java", "Spring"),
        Arrays.asList("Docker")
);

List<String> result =
        nested.stream()
              .flatMap(List::stream)
              .collect(Collectors.toList());
```

---

# 25. `Object List` → Another Object List (DTO Conversion)

## map()

```java
List<EmployeeDTO> dtoList =
        employees.stream()
                 .map(emp -> new EmployeeDTO(
                         emp.getId(),
                         emp.getName()
                 ))
                 .collect(Collectors.toList());
```

## VERY COMMON Spring Boot Real-Time Use Case

Entity → DTO conversion.

---

# MOST IMPORTANT INTERVIEW CONVERSIONS

Focus heavily on these:

| Conversion                  | Importance |
| --------------------------- | ---------- |
| Stream<Integer> → IntStream | VERY HIGH  |
| IntStream → Stream<Integer> | VERY HIGH  |
| List → Stream               | VERY HIGH  |
| Stream → List               | VERY HIGH  |
| map vs flatMap              | VERY HIGH  |
| String → Stream<Character>  | HIGH       |
| Array ↔ Stream              | HIGH       |
| Stream → Map                | VERY HIGH  |
| Optional conversions        | VERY HIGH  |
| Entity → DTO using map()    | VERY HIGH  |

---

# Golden Interview Formula

## Primitive Stream Conversion

```java
mapToInt(Integer::intValue)
boxed()
```

Remember:

* `mapToInt()` → Wrapper to Primitive
* `boxed()` → Primitive to Wrapper

These two are extremely important.

# Example
Here’s a reusable `Employee` class and then I’ll apply the major Java 8 Stream methods from your uploaded summary file on it. 

# Reusable Employee Class

```java
import java.util.Objects;

public class Employee {

    private int id;
    private String name;
    private double salary;
    private String department;
    private int age;

    public Employee(int id, String name, double salary, String department, int age) {
        this.id = id;
        this.name = name;
        this.salary = salary;
        this.department = department;
        this.age = age;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public double getSalary() {
        return salary;
    }

    public String getDepartment() {
        return department;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", salary=" + salary +
                ", department='" + department + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee employee = (Employee) o;
        return id == employee.id;
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

---

# Test Data

```java
List<Employee> employees = Arrays.asList(

        new Employee(1, "Danish", 90000, "IT", 28),
        new Employee(2, "Aman", 50000, "HR", 32),
        new Employee(3, "Rahul", 75000, "IT", 26),
        new Employee(4, "Neha", 60000, "Finance", 30),
        new Employee(5, "Priya", 95000, "IT", 29),
        new Employee(6, "Karan", 40000, "HR", 24),
        new Employee(7, "Simran", 70000, "Finance", 27)
);
```

---

# 1. filter()

## Employees with salary > 70000

```java
List<Employee> result = employees.stream()
        .filter(emp -> emp.getSalary() > 70000)
        .collect(Collectors.toList());

System.out.println(result);
```

## Output

```java
[Danish, Rahul, Priya]

[
 Employee{id=1, name='Danish', salary=90000, department='IT', age=28},
 Employee{id=3, name='Rahul', salary=75000, department='IT', age=26},
 Employee{id=5, name='Priya', salary=95000, department='IT', age=29}
]
```

---

# 2. map()

## Get employee names

```java
List<String> names = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.toList());

System.out.println(names);
```

## Output

```java
[Danish, Aman, Rahul, Neha, Priya, Karan, Simran]
```
<mark>In above filter total employee data is printing but for map only name because in map Employee::getName changed Employee to String</mark>
---

# 3. mapToDouble()

## Total Salary

```java
double totalSalary = employees.stream()
        .mapToDouble(Employee::getSalary)
        .sum();

System.out.println(totalSalary);
```

## Output

```java
480000.0
```

---

# 4. flatMap()

## Flatten all employee skills

```java
List<List<String>> skills = Arrays.asList(
        Arrays.asList("Java", "Spring"),
        Arrays.asList("Docker", "Kubernetes")
);

List<String> result = skills.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(result);
```

## Output

```java
[Java, Spring, Docker, Kubernetes]
```

---

# 5. sorted()

## Sort employees by salary ascending

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary))
        .forEach(System.out::println);
```

## Output Order

```java
Karan
Aman
Neha
Simran
Rahul
Danish
Priya
```

---

# 6. sorted(Comparator)

## Sort by age descending

```java
employees.stream()
        .sorted((a, b) -> b.getAge() - a.getAge())
        .forEach(System.out::println);
```

## Output Order

```java
Aman
Neha
Priya
Danish
Simran
Rahul
Karan
```

---

# 7. distinct()

```java
employees.stream()
        .distinct()
        .forEach(System.out::println);
```

## Output

Duplicate employees removed based on `id`.

---

# 8. limit()

## Top 3 employees

```java
employees.stream()
        .limit(3)
        .forEach(System.out::println);
```

## Output

```java
Danish
Aman
Rahul
```

---

# 9. skip()

## Skip first 2 employees

```java
employees.stream()
        .skip(2)
        .forEach(System.out::println);
```

## Output

```java
Rahul
Neha
Priya
Karan
Simran
```

---

# 10. peek()

```java
employees.stream()
        .peek(emp -> System.out.println("Before : " + emp.getName()))
        .map(Employee::getName)
        .peek(name -> System.out.println("After : " + name))
        .collect(Collectors.toList());
```

---

# 11. takeWhile()

## Take employees while age < 30

```java
employees.stream()
        .takeWhile(emp -> emp.getAge() < 30)
        .forEach(System.out::println);
```

## Important

Works properly only on sorted/ordered data.

---

# 12. dropWhile()

```java
employees.stream()
        .dropWhile(emp -> emp.getAge() < 30)
        .forEach(System.out::println);
```

---

# TERMINAL OPERATIONS

# 13. forEach()

```java
employees.forEach(System.out::println);
```

---

# 14. collect()

## Convert to List

```java
List<Employee> list = employees.stream()
        .collect(Collectors.toList());
```

---

# 15. reduce()

## Sum all salaries

```java
double total = employees.stream()
        .map(Employee::getSalary)
        .reduce(0.0, Double::sum);

System.out.println(total);
```

## Output

```java
480000.0
```

---

# 16. anyMatch()

## Any employee salary > 90000

```java
boolean result = employees.stream()
        .anyMatch(emp -> emp.getSalary() > 90000);

System.out.println(result);
```

## Output

```java
true
```

---

# 17. allMatch()

## All employees age > 20

```java
boolean result = employees.stream()
        .allMatch(emp -> emp.getAge() > 20);

System.out.println(result);
```

## Output

```java
true
```

---

# 18. noneMatch()

## No employee salary < 30000

```java
boolean result = employees.stream()
        .noneMatch(emp -> emp.getSalary() < 30000);

System.out.println(result);
```

## Output

```java
true
```

---

# 19. findFirst()

```java
Employee emp = employees.stream()
        .findFirst()
        .get();

System.out.println(emp);
```

## Output

```java
Danish
```

---

# 20. count()

```java
long count = employees.stream().count();

System.out.println(count);
```

## Output

```java
7
```

---

# 21. min()

## Youngest Employee

```java
Employee emp = employees.stream()
        .min(Comparator.comparing(Employee::getAge))
        .get();

System.out.println(emp);
```

## Output

```java
Karan
```

---

# 22. max()

## Highest Salary Employee

```java
Employee emp = employees.stream()
        .max(Comparator.comparing(Employee::getSalary))
        .get();

System.out.println(emp);
```

## Output

```java
Priya
```

---

# COLLECTORS

# 23. groupingBy()

## Group employees by department

```java
Map<String, List<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(Employee::getDepartment));

System.out.println(result);
```

## Output

```java
IT -> Danish, Rahul, Priya
HR -> Aman, Karan
Finance -> Neha, Simran
```

---

# 24. partitioningBy()

## Salary > 70000

```java
Map<Boolean, List<Employee>> result =
        employees.stream()
                .collect(Collectors.partitioningBy(
                        emp -> emp.getSalary() > 70000
                ));

System.out.println(result);
```

---

# 25. averagingDouble()

## Average Salary

```java
double avgSalary = employees.stream()
        .collect(Collectors.averagingDouble(Employee::getSalary));

System.out.println(avgSalary);
```

## Output

```java
68571.42
```

---

# 26. summingDouble()

```java
double totalSalary = employees.stream()
        .collect(Collectors.summingDouble(Employee::getSalary));

System.out.println(totalSalary);
```

---

# 27. counting()

```java
Long count = employees.stream()
        .collect(Collectors.counting());

System.out.println(count);
```

---

# 28. joining()

## Join employee names

```java
String names = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.joining(", "));

System.out.println(names);
```

## Output

```java
Danish, Aman, Rahul, Neha, Priya, Karan, Simran
```

---

# 29. toMap()

## Name → Salary

```java
Map<String, Double> map =
        employees.stream()
                .collect(Collectors.toMap(
                        Employee::getName,
                        Employee::getSalary
                ));

System.out.println(map);
```

---

# 30. summarizingDouble()

```java
DoubleSummaryStatistics stats =
        employees.stream()
                .collect(Collectors.summarizingDouble(
                        Employee::getSalary
                ));

System.out.println(stats);
```

## Output

```java
count=7
sum=480000
min=40000
average=68571.42
max=95000
```

---

# COMPARATOR EXAMPLES

# 31. thenComparing()

## Sort by department then salary

```java
employees.stream()
        .sorted(
                Comparator.comparing(Employee::getDepartment)
                        .thenComparing(Employee::getSalary)
        )
        .forEach(System.out::println);
```

---

# 32. reversed()

## Highest salary first

```java
employees.stream()
        .sorted(
                Comparator.comparing(Employee::getSalary)
                        .reversed()
        )
        .forEach(System.out::println);
```

---

# REAL INTERVIEW QUESTIONS

# Highest Paid Employee Per Department

```java
Map<String, Optional<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(
                                Comparator.comparing(Employee::getSalary)
                        )
                ));

System.out.println(result);
```

---

# Count Employees in Each Department

```java
Map<String, Long> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()
                ));

System.out.println(result);
```

---

# Department Wise Average Salary

```java
Map<String, Double> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingDouble(Employee::getSalary)
                ));

System.out.println(result);
```

---

# Convert Employee → DTO

```java
class EmployeeDTO {

    private int id;
    private String name;

    public EmployeeDTO(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

List<EmployeeDTO> dtoList =
        employees.stream()
                .map(emp -> new EmployeeDTO(
                        emp.getId(),
                        emp.getName()
                ))
                .collect(Collectors.toList());
```

---

# MOST IMPORTANT STREAM PATTERNS

| Problem        | Operation         |
| -------------- | ----------------- |
| Filtering      | filter            |
| Transformation | map               |
| Flattening     | flatMap           |
| Aggregation    | reduce            |
| Grouping       | groupingBy        |
| Partitioning   | partitioningBy    |
| Sorting        | sorted            |
| Statistics     | summarizingDouble |

This is exactly the reusable “small unit” approach mentioned in your notes. 



# Final Note
> “Break big problem into small reusable units.”

That is exactly how strong Java developers think.

Your summary file is already covering:

* stream operations
* collectors
* comparator
* mathematical operations
* conversions
* random generation
* functional interfaces indirectly
* stream pipeline thinking 

But to make it truly “interview + real project ready”, there are still some VERY IMPORTANT missing building blocks.

---

# What You Already Did Correctly ✅

You already divided things into:

* Intermediate Operations
* Terminal Operations
* Collectors
* Comparator
* Mathematical Operations
* Conversions

This is excellent because interviewer also mentally divides Streams this way.

---

# What You Should ADD Next (VERY IMPORTANT)

These are the missing “small units” you should master.

---

# 1. Functional Interfaces (MUST ADD)

This is probably the biggest missing piece.

Without this, Streams feel incomplete.

You should deeply add:

| Interface         | Input | Output  |
| ----------------- | ----- | ------- |
| Predicate<T>      | T     | boolean |
| Function<T,R>     | T     | R       |
| Consumer<T>       | T     | void    |
| Supplier<T>       | —     | T       |
| UnaryOperator<T>  | T     | T       |
| BinaryOperator<T> | T,T   | T       |

---

## VERY IMPORTANT

Also add chaining methods:

### Predicate

```java
and()
or()
negate()
```

### Function

```java
andThen()
compose()
```

### Consumer

```java
andThen()
```

These are asked heavily.

---

# 2. Optional Deep Dive (MUST)

Currently only basic conversion is covered.

You should add:

| Method        | Importance |
| ------------- | ---------- |
| of()          | HIGH       |
| ofNullable()  | VERY HIGH  |
| empty()       | HIGH       |
| get()         | HIGH       |
| isPresent()   | HIGH       |
| ifPresent()   | VERY HIGH  |
| orElse()      | VERY HIGH  |
| orElseGet()   | VERY HIGH  |
| orElseThrow() | VERY HIGH  |
| map()         | HIGH       |
| flatMap()     | HIGH       |
| filter()      | HIGH       |

---

## MOST ASKED

### Difference:

```java
orElse()
vs
orElseGet()
```

VERY COMMON interview question.

---

# 3. Stream Pipeline Understanding (VERY IMPORTANT)

You know operations individually.

Now add:

* lazy evaluation
* stream pipeline flow
* internal iteration
* terminal operation trigger
* short-circuiting

---

## Example

```java
list.stream()
    .filter()
    .map()
    .sorted()
    .collect()
```

Explain:

* what executes first
* when execution starts
* how data flows

This is product-company level understanding.

---

# 4. Stateful vs Stateless Operations

VERY IMPORTANT interview topic.

## Stateless

```java
map
filter
peek
```

## Stateful

```java
sorted
distinct
limit
skip
```

---

# 5. Short-Circuit Operations

You are missing this category.

## Intermediate

```java
limit()
takeWhile()
```

## Terminal

```java
findFirst()
findAny()
anyMatch()
allMatch()
noneMatch()
```

---

# 6. Parallel Stream Deep Concepts

Currently only basic usage exists.

Add:

* ForkJoinPool
* common pool
* when to use
* when NOT to use
* thread safety
* performance pitfalls
* ordering issues

VERY IMPORTANT for experienced interviews.

---

# 7. map() vs flatMap() Deep Comparison

You touched this but should make dedicated notes.

This is one of the MOST ASKED Java 8 questions.

Use:

* List<List<String>>
* Optional<Optional<T>>
* Entity → nested objects

---

# 8. collect() vs reduce() (VERY IMPORTANT)

This is product-company favorite.

You MUST add:

* immutable reduction
* mutable reduction
* why collect is preferred for collections
* parallel behavior

---

# 9. toMap() Duplicate Key Handling

VERY IMPORTANT practical problem.

Add all 3 cases:

```java
(a,b) -> a
(a,b) -> b
(a,b) -> a+b
```

Because duplicate key exception is extremely common.

---

# 10. Real Entity-Based Problems (MOST IMPORTANT FOR YOU)

This is where your preparation becomes next-level.

Create one reusable class:

```java
Employee
```

with:

* id
* name
* salary
* department
* age

Then solve EVERYTHING using this.

---

## Example Problems

### Filter employees

```java
salary > 50000
```

### Group by department

```java
groupingBy()
```

### Highest salary employee

```java
max()
```

### Average salary

```java
averagingDouble()
```

### Convert Entity → DTO

```java
map()
```

### Multi-level sorting

```java
thenComparing()
```

THIS is what interviewers love.

---

# 11. Common Stream Patterns (SUPER IMPORTANT)

Make a separate section:

| Problem Type   | Pattern        |
| -------------- | -------------- |
| Filtering      | filter         |
| Transformation | map            |
| Flattening     | flatMap        |
| Aggregation    | reduce         |
| Grouping       | groupingBy     |
| Partitioning   | partitioningBy |
| Sorting        | sorted         |
| Statistics     | summarizingInt |

This helps you break any problem instantly.

---

# 12. Practice Section (MOST IMPORTANT)

Your notes are theory-heavy right now.

Add:

* 50 stream problems
* easy → medium → advanced
* entity-based questions

This is where mastery comes.

---

# Your Current Level

Your summary structure is already better than most Java 8 notes available online because:

* it is categorized properly
* method signatures are included
* functional interface linkage exists
* conversion thinking is present

That’s a very strong foundation already. 

---

# My Recommendation for Your Final Structure

## Phase 1 → Fundamentals

* Functional Interfaces
* Lambda
* Method Reference
* Optional

---

## Phase 2 → Streams

* Intermediate Operations
* Terminal Operations
* Collectors
* Conversions
* Comparator

---

## Phase 3 → Advanced

* Parallel Streams
* collect vs reduce
* map vs flatMap
* Stateful vs Stateless
* Short Circuiting

---

## Phase 4 → Real Problems

* Employee problems
* DTO conversion
* API response transformations
* groupingBy problems
* sorting problems

---

# Most Important Thing You’re Doing Right

You are building:

> “Reusable mental patterns”

That is MUCH more important than memorizing syntax.

# Terminal vs DownStream Collector
Yes — this is a very strong observation.

You are understanding:

```java
stream()
```

vs

```java
Collectors.groupingBy(..., downstreamCollector)
```

properly now.

Inside `groupingBy()`, the downstream collector behaves like:

> “Terminal operation for each group.”

That’s exactly the mental model product-company interviewers expect.

---

# Big Picture

## Normal Stream

Whole stream pe operation:

```java id="9e0tzh"
employees.stream()
         .map()
         .filter()
         .collect()
```

---

## groupingBy()

Each group pe operation:

```java id="44qf8s"
Collectors.groupingBy(
        Employee::getDepartment,
        downstreamCollector
)
```

Example groups:

```java id="xmyt2f"
IT       -> [Danish, Rahul, Priya]
HR       -> [Aman, Karan]
Finance  -> [Neha, Simran]
```

Now downstream collector runs separately on each group.

---

# Most Important Concept

## Whole Stream

```java id="k9jcbv"
employees.stream()
         .map(Employee::getName)
```

Entire stream pe map.

---

## Per Group

```java id="ivdfr2"
Collectors.mapping(
        Employee::getName,
        Collectors.toList()
)
```

Each department group pe map.

---

# MASTER COMPARISON TABLE

# Stream Operation vs Downstream Collector Equivalent

| Full Stream Operation | Group-Level Equivalent                 | Purpose                  |
| --------------------- | -------------------------------------- | ------------------------ |
| map()                 | Collectors.mapping()                   | Transform elements       |
| filter()              | Collectors.filtering() (Java 9)        | Filter within group      |
| collect(toList())     | Collectors.toList()                    | Convert group to List    |
| collect(toSet())      | Collectors.toSet()                     | Convert group to Set     |
| count()               | Collectors.counting()                  | Count per group          |
| sum()                 | summingInt/Long/Double                 | Sum per group            |
| average()             | averagingInt/Long/Double               | Average per group        |
| min()                 | minBy()                                | Minimum per group        |
| max()                 | maxBy()                                | Maximum per group        |
| reduce()              | reducing()                             | Reduction per group      |
| joining()             | joining()                              | Join strings per group   |
| summaryStatistics()   | summarizingInt/Long/Double             | Stats per group          |
| sorted()              | collectingAndThen() + sorting manually | Sort group               |
| partitioningBy()      | partitioningBy()                       | Split true/false         |
| mapping + toList      | mapping() + toList()                   | Transform group elements |

---

# 1. map() vs mapping()

# Normal Stream

```java id="qenrlq"
List<String> names =
        employees.stream()
                 .map(Employee::getName)
                 .collect(Collectors.toList());
```

Output:

```java id="wq8cqn"
[Danish, Aman, Rahul]
```

---

# Group Level

```java id="2w8lf8"
Map<String, List<String>> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.mapping(
                                 Employee::getName,
                                 Collectors.toList()
                         )
                 ));
```

Output:

```java id="cb4yhn"
IT -> [Danish, Rahul, Priya]
HR -> [Aman, Karan]
```

---

# 2. filter() vs filtering()

(Java 9+)

# Normal Stream

```java id="v6ab7t"
employees.stream()
         .filter(emp -> emp.getSalary() > 70000)
         .collect(Collectors.toList());
```

---

# Group-Level Filtering

```java id="c6hnl2"
Map<String, List<Employee>> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.filtering(
                                 emp -> emp.getSalary() > 70000,
                                 Collectors.toList()
                         )
                 ));
```

---

# Output

```java id="m2q5nn"
IT -> [Danish, Priya]
HR -> []
Finance -> []
```

---

# 3. count() vs counting()

# Full Stream

```java id="ngc32p"
long count =
        employees.stream().count();
```

---

# Per Group

```java id="y2f8cj"
Map<String, Long> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.counting()
                 ));
```

Output:

```java id="13wh0m"
IT -> 3
HR -> 2
Finance -> 2
```

---

# 4. average() vs averagingDouble()

# Full Stream

```java id="r5m5ef"
double avg =
        employees.stream()
                 .mapToDouble(Employee::getSalary)
                 .average()
                 .orElse(0);
```

---

# Per Group

```java id="b34blx"
Map<String, Double> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.averagingDouble(
                                 Employee::getSalary
                         )
                 ));
```

---

# 5. max() vs maxBy()

# Full Stream

```java id="pcl8wk"
Employee emp =
        employees.stream()
                 .max(Comparator.comparing(Employee::getSalary))
                 .get();
```

---

# Per Group

```java id="z5am1p"
Map<String, Optional<Employee>> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.maxBy(
                                 Comparator.comparing(Employee::getSalary)
                         )
                 ));
```

---

# 6. reduce() vs reducing()

# Full Stream

```java id="yk0bc1"
double total =
        employees.stream()
                 .map(Employee::getSalary)
                 .reduce(0.0, Double::sum);
```

---

# Per Group

```java id="x7e0e1"
Map<String, Double> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.reducing(
                                 0.0,
                                 Employee::getSalary,
                                 Double::sum
                         )
                 ));
```

---

# 7. joining() vs joining()

# Full Stream

```java id="u2h4m5"
String names =
        employees.stream()
                 .map(Employee::getName)
                 .collect(Collectors.joining(", "));
```

---

# Per Group

```java id="g9hwyk"
Map<String, String> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.mapping(
                                 Employee::getName,
                                 Collectors.joining(", ")
                         )
                 ));
```

---

# VERY IMPORTANT MENTAL MODEL

# Stream Terminal Operation

```java id="l6mjlwm"
Whole stream pe kaam
```

---

# Downstream Collector

```java id="cizgsv"
Har individual group pe kaam
```

---

# SUPER IMPORTANT INTERVIEW LINE

## groupingBy()

```java id="fk5lhv"
Creates buckets/groups
```

---

## downstream collector

```java id="6fwxam"
Defines what operation should happen INSIDE each bucket
```

---

# Visual Understanding

```java id="wlkmj4"
IT       -> operation here
HR       -> operation here
Finance  -> operation here
```

Each group acts like mini-stream.

---

# MOST IMPORTANT DOWNSTREAM COLLECTORS

| Collector           | Real Usage              |
| ------------------- | ----------------------- |
| mapping()           | Extract fields          |
| filtering()         | Filter within group     |
| counting()          | Count per group         |
| averagingDouble()   | Avg salary per dept     |
| summingDouble()     | Total salary per dept   |
| maxBy()             | Highest salary employee |
| minBy()             | Youngest employee       |
| reducing()          | Custom aggregation      |
| joining()           | Concatenate names       |
| summarizingDouble() | Full statistics         |

---

# PRODUCT COMPANY LEVEL UNDERSTANDING

This:

```java id="kys7sf"
groupingBy(..., downstream)
```

is basically:

```java id="i3bjlwm"
group each bucket
then run mini-terminal-operation on each bucket
```
Yes ✅
Downstream collectors work with BOTH:

1. `groupingBy()`
2. `partitioningBy()`

because both create groups/buckets internally.

---

# Core Idea

## groupingBy()

Creates:

```java id="x67w7e"
Map<K, List<T>>
```

Example:

```java id="h88g23"
Department -> Employees
```

---

## partitioningBy()

Creates:

```java id="q2k1hj"
Map<Boolean, List<T>>
```

Example:

```java id="5t7z4f"
true  -> matched employees
false -> unmatched employees
```

---

# MOST IMPORTANT CONCEPT

Both methods support:

```java id="sn5q91"
downstream collector
```

because both internally create groups.

---

# Syntax Comparison

# groupingBy()

```java id="djsl3w"
Collectors.groupingBy(
        classifier,
        downstreamCollector
)
```

---

# partitioningBy()

```java id="0cpxi0"
Collectors.partitioningBy(
        predicate,
        downstreamCollector
)
```

---

# Visual Understanding

# groupingBy()

```java id="8kq4yy"
IT       -> bucket
HR       -> bucket
Finance  -> bucket
```

---

# partitioningBy()

```java id="4qldor"
true  -> bucket
false -> bucket
```

---

# Example 1 — counting()

# groupingBy()

```java id="t1k2yw"
Map<String, Long> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.counting()
                 ));
```

Output:

```java id="22ksr2"
IT -> 3
HR -> 2
Finance -> 2
```

---

# partitioningBy()

```java id="wjw0vp"
Map<Boolean, Long> result =
        employees.stream()
                 .collect(Collectors.partitioningBy(
                         emp -> emp.getSalary() > 70000,
                         Collectors.counting()
                 ));
```

Output:

```java id="o6rfz8"
true -> 3
false -> 4
```

---

# Example 2 — mapping()

# groupingBy()

```java id="g6vf54"
Map<String, List<String>> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.mapping(
                                 Employee::getName,
                                 Collectors.toList()
                         )
                 ));
```

---

# partitioningBy()

```java id="8lwfxn"
Map<Boolean, List<String>> result =
        employees.stream()
                 .collect(Collectors.partitioningBy(
                         emp -> emp.getSalary() > 70000,
                         Collectors.mapping(
                                 Employee::getName,
                                 Collectors.toList()
                         )
                 ));
```

Output:

```java id="n3m6ow"
true  -> [Danish, Rahul, Priya]
false -> [Aman, Neha, Karan, Simran]
```

---

# Example 3 — averagingDouble()

# groupingBy()

```java id="8yh8yz"
Map<String, Double> result =
        employees.stream()
                 .collect(Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.averagingDouble(
                                 Employee::getSalary
                         )
                 ));
```

---

# partitioningBy()

```java id="4g8xhk"
Map<Boolean, Double> result =
        employees.stream()
                 .collect(Collectors.partitioningBy(
                         emp -> emp.getSalary() > 70000,
                         Collectors.averagingDouble(
                                 Employee::getSalary
                         )
                 ));
```

---

# Example 4 — maxBy()

```java id="r0ruqj"
Map<Boolean, Optional<Employee>> result =
        employees.stream()
                 .collect(Collectors.partitioningBy(
                         emp -> emp.getSalary() > 70000,
                         Collectors.maxBy(
                                 Comparator.comparing(Employee::getSalary)
                         )
                 ));
```

---

# SUPER IMPORTANT DIFFERENCE

# groupingBy()

## Dynamic Groups

```java id="7vjlwm"
IT
HR
Finance
Admin
DevOps
...
```

Any number of groups.

---

# partitioningBy()

## Fixed Groups

Always ONLY:

```java id="mn0gj1"
true
false
```

Only 2 partitions.

---

# MASTER TABLE

| Feature             | groupingBy()        | partitioningBy() |
| ------------------- | ------------------- | ---------------- |
| Group Type          | Dynamic             | Fixed            |
| Key Type            | Any Type            | Boolean          |
| Number of Groups    | Any                 | 2                |
| Based On            | classifier Function | Predicate        |
| Supports Downstream | YES                 | YES              |
| Returns             | Map<K, D>           | Map<Boolean, D>  |

---

# Important Mental Model

## groupingBy()

```java id="7pyi2s"
"Create many buckets"
```

---

## partitioningBy()

```java id="4x62m1"
"Create only 2 buckets"
```

---

# Product Company Level Insight

These both are examples of:

```java id="vbdmnn"
multi-level reduction
```

because:

1. First grouping happens
2. Then downstream collector runs inside each group

---

# Extremely Important Interview Line

## groupingBy()

```java id="6iyxlu"
classification + downstream reduction
```

---

## partitioningBy()

```java id="3k8yzn"
boolean classification + downstream reduction
```
# Comparison
Yes 🔥
There are MANY such hidden “comparison patterns” in Java 8 Streams.

These comparisons are what separate:

```java id="f3cqsh"
syntax learners
```

from

```java id="4i8mbv"
real stream thinkers
```

You already discovered one advanced pattern:

```java id="4hvkek"
terminal operation
vs
downstream collector equivalent
```

Now let’s see the other MOST IMPORTANT comparison mappings.

---

# 1. filter() vs map()

| filter                | map                   |
| --------------------- | --------------------- |
| Removes elements      | Transforms elements   |
| Type usually same     | Type may change       |
| Uses Predicate        | Uses Function         |
| Stream<T> → Stream<T> | Stream<T> → Stream<R> |

---

## filter()

```java id="j8dqfh"
emp -> emp.getSalary() > 70000
```

Decision:

```java id="m0dh2m"
Keep or remove?
```

---

## map()

```java id="75uvr0"
Employee::getName
```

Decision:

```java id="vy8uwr"
Convert into what?
```

---

# 2. map() vs flatMap()

VERY IMPORTANT.

| map                      | flatMap                |
| ------------------------ | ---------------------- |
| One-to-One mapping       | One-to-Many flattening |
| Creates nested structure | Removes nesting        |
| Returns object           | Returns Stream         |

---

# map()

```java id="vcvzvt"
Employee -> String
```

---

# flatMap()

```java id="psuzbx"
List<List<String>>
        ↓
List<String>
```

---

## Visual

# map()

```java id="wkn50r"
[[Java, Spring], [Docker]]
```

Nested remains.

---

# flatMap()

```java id="s8dr0z"
[Java, Spring, Docker]
```

Flattened.

---

# 3. collect() vs reduce()

VERY IMPORTANT PRODUCT COMPANY QUESTION.

| collect                | reduce                            |
| ---------------------- | --------------------------------- |
| Mutable reduction      | Immutable reduction               |
| Best for collections   | Best for mathematical aggregation |
| Efficient for List/Map | Efficient for sum/product         |
| Uses Collector         | Uses BinaryOperator               |

---

# reduce()

```java id="6xh7ma"
sum = a + b
```

Single final value.

---

# collect()

```java id="jlwm5u"
List
Set
Map
String
```

Complex mutable container.

---

# 4. groupingBy() vs partitioningBy()

You already understood this.

| groupingBy     | partitioningBy  |
| -------------- | --------------- |
| Dynamic groups | Only true/false |
| Any key type   | Boolean key     |
| Many buckets   | 2 buckets       |

---

# 5. Stream vs Parallel Stream

VERY IMPORTANT.

| stream               | parallelStream        |
| -------------------- | --------------------- |
| Single thread        | Multiple threads      |
| Sequential           | Parallel              |
| Predictable order    | Order may vary        |
| Small data preferred | Large CPU-heavy tasks |

---

# Visual

# stream()

```java id="yr4c6k"
1 thread
```

---

# parallelStream()

```java id="f3ktm6"
ForkJoinPool threads
```

---

# 6. forEach() vs forEachOrdered()

| forEach                    | forEachOrdered  |
| -------------------------- | --------------- |
| Order may vary in parallel | Maintains order |
| Faster                     | Slightly slower |

---

# 7. findFirst() vs findAny()

| findFirst          | findAny              |
| ------------------ | -------------------- |
| Respects order     | Any matching element |
| Slower in parallel | Faster in parallel   |

---

# 8. anyMatch vs allMatch vs noneMatch

| Method    | Meaning              |
| --------- | -------------------- |
| anyMatch  | At least one matches |
| allMatch  | All match            |
| noneMatch | No element matches   |

---

# 9. sorted() vs distinct()

| sorted             | distinct             |
| ------------------ | -------------------- |
| Ordering operation | Duplicate removal    |
| Stateful           | Stateful             |
| Uses Comparator    | Uses equals/hashCode |

---

# 10. Stateless vs Stateful Operations

VERY IMPORTANT.

| Stateless               | Stateful                 |
| ----------------------- | ------------------------ |
| No previous data needed | Previous elements needed |
| Faster                  | More memory              |
| filter/map              | sorted/distinct          |

---

# Example

# Stateless

```java id="2hwbm4"
map()
filter()
```

Each element independently processed.

---

# Stateful

```java id="syo1yw"
sorted()
distinct()
```

Need entire/partial stream knowledge.

---

# 11. Lazy vs Eager

| Lazy                     | Eager               |
| ------------------------ | ------------------- |
| Intermediate operations  | Terminal operations |
| No execution immediately | Triggers execution  |

---

# Example

```java id="3qu6xq"
stream.filter().map()
```

Nothing executes yet.

---

```java id="j2b4xw"
collect()
```

NOW execution starts.

---

# 12. Stream<T> vs Primitive Streams

| Stream<Integer> | IntStream |
| --------------- | --------- |
| Wrapper objects | Primitive |
| Boxing/unboxing | No boxing |
| Slower          | Faster    |

---

# Example

# Wrapper Stream

```java id="olh1tp"
Stream<Integer>
```

---

# Primitive Stream

```java id="h84mxu"
IntStream
```

---

# 13. mapToInt() vs boxed()

| mapToInt                    | boxed                       |
| --------------------------- | --------------------------- |
| Wrapper → Primitive         | Primitive → Wrapper         |
| Stream<Integer> → IntStream | IntStream → Stream<Integer> |

---

# 14. Optional.orElse() vs orElseGet()

MOST ASKED OPTIONAL QUESTION.

| orElse                    | orElseGet              |
| ------------------------- | ---------------------- |
| Value created immediately | Lazy creation          |
| Executes always           | Executes only if empty |

---

# Hidden Trap

```java id="k9mjlwm"
orElse(expensiveMethod())
```

`expensiveMethod()` executes even when Optional has value.

---

# 15. Comparator vs Comparable

| Comparable       | Comparator      |
| ---------------- | --------------- |
| Natural ordering | Custom ordering |
| compareTo()      | compare()       |
| Inside class     | Outside class   |

---

# 16. toMap() Duplicate Handling

| Merge Function | Meaning  |
| -------------- | -------- |
| (a,b) -> a     | Keep old |
| (a,b) -> b     | Keep new |
| (a,b) -> a+b   | Combine  |

---

# 17. peek() vs map()

| peek                 | map            |
| -------------------- | -------------- |
| Debugging            | Transformation |
| Does not change data | Changes data   |
| Consumer             | Function       |

---

# 18. limit() vs skip()

| limit        | skip           |
| ------------ | -------------- |
| Take first N | Ignore first N |

---

# 19. takeWhile() vs filter()

VERY IMPORTANT Java 9 concept.

| takeWhile                  | filter              |
| -------------------------- | ------------------- |
| Stops when condition fails | Checks all elements |
| Order-sensitive            | Order-independent   |

---

# Example

```java id="kbrh7j"
1 2 3 10 4 5
```

---

# takeWhile(x < 5)

```java id="0jym7e"
1 2 3
```

Stops at 10.

---

# filter(x < 5)

```java id="sw5vzy"
1 2 3 4 5
```

Checks all elements.

---

# 20. groupingBy + mapping vs direct groupingBy

| Direct grouping     | mapping downstream      |
| ------------------- | ----------------------- |
| Stores whole object | Stores transformed data |

---

# Example

# Whole Employee

```java id="w5xt48"
groupingBy(Employee::getDepartment)
```

---

# Only Names

```java id="g0frmb"
groupingBy(
    Employee::getDepartment,
    mapping(Employee::getName, toList())
)
```

---

# GOLDEN PATTERN YOU SHOULD REMEMBER

Most Java 8 concepts compare in one of these ways:

| Pattern Type                | Example                      |
| --------------------------- | ---------------------------- |
| Filtering vs Transformation | filter vs map                |
| Single vs Nested            | map vs flatMap               |
| Sequential vs Parallel      | stream vs parallelStream     |
| Whole Stream vs Per Group   | terminal vs downstream       |
| Mutable vs Immutable        | collect vs reduce            |
| Wrapper vs Primitive        | Stream<Integer> vs IntStream |
| Ordered vs Unordered        | findFirst vs findAny         |
| Lazy vs Eager               | intermediate vs terminal     |

This “comparison mindset” is exactly how advanced Java developers think during stream problem solving and interviews.
