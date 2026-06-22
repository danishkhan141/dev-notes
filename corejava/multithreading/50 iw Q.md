Below are **Top 50 Java Multithreading Interview Questions** explained professionally for your interview preparation, especially useful for **7 years Java/Spring Boot backend experience**.

# Java Multithreading Interview Questions

## 1. What is multithreading?

**Multithreading** means executing multiple threads within the same process at the same time.

A **thread** is the smallest unit of execution.

Example: In a web application, multiple users can send requests at the same time. Each request may be handled by a separate thread.

```java
public class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();
    }
}
```

---

## 2. What is the difference between process and thread?

| Process                          | Thread                            |
| -------------------------------- | --------------------------------- |
| Independent program in execution | Lightweight unit inside a process |
| Has separate memory              | Shares process memory             |
| Communication is slower          | Communication is faster           |
| More expensive                   | Less expensive                    |

Example:

Chrome browser is a process. Inside Chrome, tabs, rendering, downloads, and network handling may use multiple threads.

---

## 3. Why do we use multithreading?

We use multithreading for:

1. Better CPU utilization
2. Faster execution of independent tasks
3. Responsive applications
4. Parallel processing
5. Handling multiple users/requests

Example in backend:

```java
ExecutorService executor = Executors.newFixedThreadPool(5);

executor.submit(() -> sendEmail());
executor.submit(() -> generateInvoice());
executor.submit(() -> updateAuditLog());
```

Here, independent tasks can run concurrently.

---

## 4. How can we create a thread in Java?

Main ways:

### 1. Extending Thread

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running");
    }
}
```

### 2. Implementing Runnable

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Running");
    }
}
```

### 3. Using Callable

```java
Callable<Integer> task = () -> 10 + 20;
```

### 4. Using ExecutorService

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
executor.submit(() -> System.out.println("Task running"));
executor.shutdown();
```

---

## 5. Which is better: extending Thread or implementing Runnable?

**Runnable is better** because Java supports single inheritance only.

If we extend `Thread`, we cannot extend another class.

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Task running");
    }
}
```

Runnable separates the **task** from the **thread**.

---

## 6. What is the difference between `start()` and `run()`?

| start()                  | run()                        |
| ------------------------ | ---------------------------- |
| Creates a new thread     | Does not create a new thread |
| JVM calls run internally | Runs like a normal method    |
| Enables multithreading   | No multithreading            |

Example:

```java
Thread t = new Thread(() -> {
    System.out.println(Thread.currentThread().getName());
});

t.run();   // main thread
t.start(); // new thread
```

Interview point: Always call `start()` to execute logic in a separate thread.

---

## 7. What is the lifecycle of a thread?

Thread lifecycle states:

1. **NEW** — Thread object created
2. **RUNNABLE** — Ready to run
3. **RUNNING** — Currently executing
4. **BLOCKED** — Waiting for lock
5. **WAITING** — Waiting indefinitely
6. **TIMED_WAITING** — Waiting for specific time
7. **TERMINATED** — Execution completed

Example:

```java
Thread t = new Thread(() -> System.out.println("Running"));

System.out.println(t.getState()); // NEW
t.start();
System.out.println(t.getState()); // RUNNABLE
```

---

## 8. What is context switching?

**Context switching** means CPU switches from one thread to another.

The CPU saves the current thread state and loads another thread state.

Too many threads can cause too much context switching, which reduces performance.

---

## 9. What is a daemon thread?

A **daemon thread** runs in the background and does not prevent JVM shutdown.

Example:

```java
Thread t = new Thread(() -> {
    while (true) {
        System.out.println("Background task");
    }
});

t.setDaemon(true);
t.start();
```

Examples of daemon threads:

* Garbage Collector thread
* Monitoring threads
* Background cleanup threads

---

## 10. What is the difference between user thread and daemon thread?

| User Thread                  | Daemon Thread             |
| ---------------------------- | ------------------------- |
| Main application thread      | Background helper thread  |
| JVM waits for it to finish   | JVM does not wait         |
| Used for main business logic | Used for supporting tasks |

Example: A payment processing thread should not be daemon. A metrics cleanup thread can be daemon.

---

# Synchronization and Locking

## 11. What is thread safety?

A code is **thread-safe** when multiple threads can access it at the same time without causing incorrect results.

Example of non-thread-safe code:

```java
class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }
}
```

`count++` is not atomic. Internally it has three steps:

1. Read value
2. Increment value
3. Write value

Multiple threads can interfere.

---

## 12. What is race condition?

A **race condition** occurs when multiple threads access shared data and final result depends on thread execution order.

Example:

```java
class Counter {
    int count = 0;

    void increment() {
        count++;
    }
}
```

If two threads call `increment()` at the same time, the final value may be wrong.

---

## 13. How can we solve race condition?

Using:

1. `synchronized`
2. `Lock`
3. `AtomicInteger`
4. Concurrent collections
5. Immutable objects

Example using synchronized:

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }
}
```

Example using AtomicInteger:

```java
class Counter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }
}
```

---

## 14. What is synchronized keyword?

`synchronized` is used to allow only one thread at a time to execute a critical section.

```java
public synchronized void withdraw(int amount) {
    balance = balance - amount;
}
```

It provides:

1. Mutual exclusion
2. Visibility guarantee

Meaning: Other threads see updated values after lock release.

---

## 15. What is object-level lock?

When a non-static synchronized method is used, lock is taken on the current object.

```java
public synchronized void method1() {
    // lock on this object
}
```

Same as:

```java
public void method1() {
    synchronized (this) {
        // code
    }
}
```

---

## 16. What is class-level lock?

When a static synchronized method is used, lock is taken on the class object.

```java
public static synchronized void method1() {
    // lock on Class object
}
```

Same as:

```java
synchronized (MyClass.class) {
    // code
}
```

Use case: When shared data belongs to class/static level.

---

## 17. Can two synchronized methods run at the same time?

For the **same object**, two synchronized instance methods cannot run at the same time.

```java
class Service {
    public synchronized void method1() {}
    public synchronized void method2() {}
}
```

If Thread-1 is executing `method1()`, Thread-2 cannot execute `method2()` on the same object.

But for different objects, they can run independently.

---

## 18. What is reentrant lock behavior in synchronized?

Java synchronized lock is **reentrant**.

It means if a thread already holds a lock, it can acquire the same lock again.

```java
class Test {
    public synchronized void method1() {
        method2();
    }

    public synchronized void method2() {
        System.out.println("Allowed");
    }
}
```

This will not deadlock because the same thread can re-enter the lock.

---

## 19. What is volatile keyword?

`volatile` guarantees **visibility**, not atomicity.

When a variable is volatile, changes made by one thread are visible to other threads immediately.

```java
private volatile boolean running = true;

public void stop() {
    running = false;
}
```

Good use case:

```java
while (running) {
    // keep running
}
```

But this is not safe:

```java
volatile int count = 0;
count++;
```

Because `count++` is not atomic.

---

## 20. Difference between synchronized and volatile?

| synchronized                      | volatile                        |
| --------------------------------- | ------------------------------- |
| Provides locking                  | Does not provide locking        |
| Provides atomicity and visibility | Provides only visibility        |
| Used for compound operations      | Used for flags/status variables |
| More expensive                    | Lightweight                     |

Example:

Use `volatile` for:

```java
private volatile boolean shutdown = false;
```

Use `synchronized` for:

```java
balance = balance - amount;
```

---

## 21. What is atomicity?

Atomicity means an operation completes as a single indivisible unit.

Example:

```java
count++;
```

This is not atomic.

Atomic version:

```java
AtomicInteger count = new AtomicInteger();

count.incrementAndGet();
```

---

## 22. What are Atomic classes?

Atomic classes are present in `java.util.concurrent.atomic`.

Common classes:

1. `AtomicInteger`
2. `AtomicLong`
3. `AtomicBoolean`
4. `AtomicReference`

Example:

```java
AtomicInteger counter = new AtomicInteger(0);

counter.incrementAndGet();
counter.decrementAndGet();
counter.compareAndSet(1, 2);
```

They internally use CAS.

---

## 23. What is CAS?

CAS means **Compare-And-Swap**.

It works like this:

1. Read current value
2. Compare with expected value
3. If same, update value
4. If not same, retry

Example:

```java
atomicInteger.compareAndSet(10, 20);
```

Meaning: If current value is 10, change it to 20.

CAS is used in atomic classes and concurrent collections.

---

## 24. What is wait, notify, and notifyAll?

These methods are used for inter-thread communication.

They are defined in `Object` class.

```java
wait();
notify();
notifyAll();
```

Important: They must be called inside synchronized block.

```java
synchronized (lock) {
    lock.wait();
}
```

---

## 25. Why wait, notify, notifyAll are in Object class, not Thread class?

Because wait/notify work on object locks.

Every Java object can act as a lock.

```java
synchronized (obj) {
    obj.wait();
}
```

The thread waits on that object’s monitor.

---

## 26. Difference between sleep and wait?

| sleep()                    | wait()                                   |
| -------------------------- | ---------------------------------------- |
| Belongs to Thread class    | Belongs to Object class                  |
| Does not release lock      | Releases lock                            |
| Used for pausing execution | Used for inter-thread communication      |
| Can be called anywhere     | Must be called inside synchronized block |

Example:

```java
Thread.sleep(1000);
```

```java
synchronized(lock) {
    lock.wait();
}
```

---

## 27. Difference between notify and notifyAll?

| notify()                           | notifyAll()               |
| ---------------------------------- | ------------------------- |
| Wakes one waiting thread           | Wakes all waiting threads |
| Less overhead                      | More overhead             |
| Risky if multiple conditions exist | Safer in complex cases    |

In real projects, `notifyAll()` is often safer when multiple threads are waiting on the same lock.

---

## 28. What is producer-consumer problem?

Producer produces data. Consumer consumes data.

A common solution is `BlockingQueue`.

```java
BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);

Runnable producer = () -> {
    try {
        queue.put(1);
        System.out.println("Produced");
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
};

Runnable consumer = () -> {
    try {
        Integer value = queue.take();
        System.out.println("Consumed: " + value);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
};
```

`put()` waits if queue is full.
`take()` waits if queue is empty.

---

# Thread Methods

## 29. What is join method?

`join()` makes one thread wait until another thread completes.

```java
Thread t1 = new Thread(() -> {
    System.out.println("Task completed");
});

t1.start();
t1.join();

System.out.println("Main continues");
```

Here, main thread waits until `t1` completes.

---

## 30. What is yield method?

`yield()` hints to the scheduler that current thread is willing to pause and allow other threads to execute.

```java
Thread.yield();
```

But it is not guaranteed. Thread scheduler may ignore it.

In real projects, `yield()` is rarely used.

---

## 31. What is thread priority?

Java thread priority is a hint to the scheduler.

Range:

```java
Thread.MIN_PRIORITY = 1
Thread.NORM_PRIORITY = 5
Thread.MAX_PRIORITY = 10
```

Example:

```java
Thread t = new Thread(task);
t.setPriority(Thread.MAX_PRIORITY);
```

Important: Priority does not guarantee execution order.

---

## 32. What is interrupt in Java?

Interrupt is a way to request a thread to stop or cancel its work.

```java
Thread t = new Thread(() -> {
    while (!Thread.currentThread().isInterrupted()) {
        System.out.println("Working");
    }
});

t.start();
t.interrupt();
```

Important: Interrupt does not forcibly kill a thread. It only sends a signal.

---

## 33. Difference between interrupted and isInterrupted?

| method                 | Meaning                                     |
| ---------------------- | ------------------------------------------- |
| `isInterrupted()`      | Checks interrupt status without clearing it |
| `Thread.interrupted()` | Checks and clears interrupt status          |

Example:

```java
Thread.currentThread().isInterrupted();
Thread.interrupted();
```

---

## 34. Why stop, suspend, and resume are deprecated?

Because they are unsafe.

`stop()` can kill a thread while it is holding a lock, leaving shared data in inconsistent state.

Better approach:

```java
private volatile boolean running = true;

public void stopTask() {
    running = false;
}
```

Or use interrupt:

```java
thread.interrupt();
```

---

# Executor Framework

## 35. What is ExecutorService?

`ExecutorService` is a higher-level API for managing threads.

Instead of manually creating threads, we submit tasks to a thread pool.

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

executor.submit(() -> {
    System.out.println("Task running");
});

executor.shutdown();
```

Benefits:

1. Reuses threads
2. Controls number of threads
3. Better resource management
4. Supports Callable and Future

---

## 36. What is thread pool?

A thread pool is a collection of reusable threads.

Without thread pool:

```java
new Thread(task).start();
```

This creates a new thread every time.

With thread pool:

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(task);
```

Threads are reused, which improves performance.

---

## 37. Types of thread pools in Java

Common thread pools:

### FixedThreadPool

```java
Executors.newFixedThreadPool(5);
```

Fixed number of threads.

### CachedThreadPool

```java
Executors.newCachedThreadPool();
```

Creates threads as needed. Good for short-lived tasks, but risky if too many tasks come.

### SingleThreadExecutor

```java
Executors.newSingleThreadExecutor();
```

One thread executes tasks sequentially.

### ScheduledThreadPool

```java
Executors.newScheduledThreadPool(2);
```

Used for delayed or periodic tasks.

---

## 38. What is ThreadPoolExecutor?

`ThreadPoolExecutor` gives full control over thread pool configuration.

```java
ExecutorService executor = new ThreadPoolExecutor(
        2,                          // corePoolSize
        5,                          // maximumPoolSize
        60, TimeUnit.SECONDS,        // keepAliveTime
        new ArrayBlockingQueue<>(10) // workQueue
);
```

Main parameters:

| Parameter        | Meaning                      |
| ---------------- | ---------------------------- |
| corePoolSize     | Minimum active threads       |
| maximumPoolSize  | Maximum allowed threads      |
| keepAliveTime    | Extra thread idle time       |
| workQueue        | Queue for waiting tasks      |
| rejectionHandler | What to do when pool is full |

---

## 39. How does ThreadPoolExecutor execute tasks?

Flow:

1. If active threads < corePoolSize, create new thread
2. Else put task into queue
3. If queue is full and active threads < maximumPoolSize, create extra thread
4. If maximum threads reached, reject task

This is very important for senior Java interviews.

---

## 40. What is RejectedExecutionHandler?

It handles tasks that cannot be accepted by thread pool.

Common rejection policies:

| Policy              | Meaning                    |
| ------------------- | -------------------------- |
| AbortPolicy         | Throws exception           |
| CallerRunsPolicy    | Caller thread runs task    |
| DiscardPolicy       | Silently discards task     |
| DiscardOldestPolicy | Removes oldest queued task |

Example:

```java
new ThreadPoolExecutor(
        2,
        4,
        60,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(10),
        new ThreadPoolExecutor.CallerRunsPolicy()
);
```

---

## 41. Difference between execute and submit?

| execute()                                    | submit()                            |
| -------------------------------------------- | ----------------------------------- |
| Accepts Runnable only                        | Accepts Runnable and Callable       |
| Does not return result                       | Returns Future                      |
| Exception goes to uncaught exception handler | Exception is captured inside Future |

Example:

```java
executor.execute(() -> System.out.println("execute"));

Future<Integer> future = executor.submit(() -> 10);
Integer result = future.get();
```

---

## 42. Difference between Runnable and Callable?

| Runnable                                | Callable                    |
| --------------------------------------- | --------------------------- |
| Does not return result                  | Returns result              |
| Cannot throw checked exception directly | Can throw checked exception |
| Method: `run()`                         | Method: `call()`            |

Example:

```java
Callable<Integer> task = () -> {
    return 100;
};
```

---

## 43. What is Future?

`Future` represents the result of an asynchronous task.

```java
Future<Integer> future = executor.submit(() -> {
    return 10 + 20;
});

Integer result = future.get();
```

Important methods:

```java
get()
isDone()
isCancelled()
cancel()
```

Problem: `future.get()` is blocking.

---

## 44. What is CompletableFuture?

`CompletableFuture` is used for asynchronous programming.

It was introduced in Java 8.

```java
CompletableFuture.supplyAsync(() -> {
    return "User data";
}).thenApply(data -> {
    return data.toUpperCase();
}).thenAccept(result -> {
    System.out.println(result);
});
```

Benefits:

1. Non-blocking chaining
2. Combining multiple async tasks
3. Exception handling
4. Better than plain Future

---

## 45. How to handle exceptions in CompletableFuture?

Using `exceptionally`, `handle`, or `whenComplete`.

```java
CompletableFuture.supplyAsync(() -> {
    int result = 10 / 0;
    return result;
}).exceptionally(ex -> {
    System.out.println("Exception: " + ex.getMessage());
    return 0;
});
```

Using `handle`:

```java
CompletableFuture.supplyAsync(() -> "data")
        .handle((result, ex) -> {
            if (ex != null) {
                return "fallback";
            }
            return result;
        });
```

---

# Locks and Concurrent Utilities

## 46. Difference between synchronized and ReentrantLock?

| synchronized                  | ReentrantLock              |
| ----------------------------- | -------------------------- |
| Simple to use                 | More flexible              |
| Lock released automatically   | Need to unlock manually    |
| No tryLock                    | Supports tryLock           |
| No fairness option            | Supports fairness          |
| No interruptible lock waiting | Supports lockInterruptibly |

Example:

```java
Lock lock = new ReentrantLock();

lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}
```

Important: Always unlock inside `finally`.

---

## 47. What is ReadWriteLock?

`ReadWriteLock` allows multiple readers but only one writer.

Useful when reads are frequent and writes are rare.

```java
ReadWriteLock lock = new ReentrantReadWriteLock();

lock.readLock().lock();
try {
    // read data
} finally {
    lock.readLock().unlock();
}

lock.writeLock().lock();
try {
    // update data
} finally {
    lock.writeLock().unlock();
}
```

Example use case: Cache.

---

## 48. What is CountDownLatch?

`CountDownLatch` allows one thread to wait until other threads complete their work.

```java
CountDownLatch latch = new CountDownLatch(3);

Runnable task = () -> {
    System.out.println("Task completed");
    latch.countDown();
};

new Thread(task).start();
new Thread(task).start();
new Thread(task).start();

latch.await();

System.out.println("All tasks completed");
```

Use case: Wait for multiple services to initialize.

---

## 49. Difference between CountDownLatch and CyclicBarrier?

| CountDownLatch                      | CyclicBarrier               |
| ----------------------------------- | --------------------------- |
| One or more threads wait for others | Threads wait for each other |
| Cannot be reused                    | Can be reused               |
| Countdown happens manually          | Barrier trips automatically |
| Good for startup completion         | Good for parallel phases    |

Example:

* CountDownLatch: Main thread waits for 5 workers.
* CyclicBarrier: 5 workers wait for each other before next phase.

---

## 50. What is Semaphore?

Semaphore controls how many threads can access a resource at the same time.

```java
Semaphore semaphore = new Semaphore(3);

Runnable task = () -> {
    try {
        semaphore.acquire();
        System.out.println("Accessing resource");
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } finally {
        semaphore.release();
    }
};
```

Use case: Allow only 3 users to access a limited resource at the same time.

Example: Database connection limit, API rate limiting, file processing slots.

---

# Important Extra Questions for Senior Java Interviews

## 51. What is BlockingQueue?

`BlockingQueue` is a thread-safe queue that supports blocking operations.

Common implementations:

| Queue                 | Use                            |
| --------------------- | ------------------------------ |
| ArrayBlockingQueue    | Fixed-size queue               |
| LinkedBlockingQueue   | Optionally bounded queue       |
| PriorityBlockingQueue | Priority-based queue           |
| DelayQueue            | Delayed task execution         |
| SynchronousQueue      | Direct handoff between threads |

Example:

```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();

queue.put("data");   // waits if full
queue.take();        // waits if empty
```

---

## 52. What is ConcurrentHashMap?

`ConcurrentHashMap` is a thread-safe version of HashMap.

It allows concurrent reads and updates.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

map.put("A", 1);
map.putIfAbsent("B", 2);
map.computeIfAbsent("C", key -> 3);
```

Important points:

1. It does not lock the entire map.
2. Reads are mostly non-blocking.
3. It does not allow null keys or null values.
4. Better than `Collections.synchronizedMap()` for concurrent access.

---

## 53. Why ConcurrentHashMap does not allow null?

Because null creates ambiguity.

Example:

```java
map.get("A")
```

If result is null, it is unclear whether:

1. Key does not exist
2. Key exists with null value

In concurrent environment, this ambiguity is dangerous.

---

## 54. Difference between HashMap, Hashtable, and ConcurrentHashMap?

| HashMap                   | Hashtable                        | ConcurrentHashMap             |
| ------------------------- | -------------------------------- | ----------------------------- |
| Not thread-safe           | Thread-safe                      | Thread-safe                   |
| Allows one null key       | Does not allow null              | Does not allow null           |
| Fast but unsafe           | Slow because full method locking | Better concurrent performance |
| Modern but non-concurrent | Legacy                           | Preferred for concurrency     |

---

## 55. What is CopyOnWriteArrayList?

`CopyOnWriteArrayList` is a thread-safe list where every write creates a new copy of the array.

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();

list.add("A");
list.add("B");
```

Good for read-heavy scenarios.

Example: Listener list, configuration list.

Not good when writes are frequent because copying is expensive.

---

## 56. Difference between fail-fast and fail-safe iterator?

| Fail-fast                              | Fail-safe                                        |
| -------------------------------------- | ------------------------------------------------ |
| Throws ConcurrentModificationException | Does not throw                                   |
| Works on original collection           | Usually works on copy/snapshot                   |
| Example: ArrayList, HashMap            | Example: CopyOnWriteArrayList, ConcurrentHashMap |

Example fail-fast:

```java
List<String> list = new ArrayList<>();
list.add("A");

for (String item : list) {
    list.add("B"); // ConcurrentModificationException
}
```

---

## 57. What is ThreadLocal?

`ThreadLocal` provides a separate copy of a variable for each thread.

```java
ThreadLocal<String> userContext = new ThreadLocal<>();

userContext.set("Danish");
System.out.println(userContext.get());
userContext.remove();
```

Use cases:

1. User context
2. Request context
3. Transaction context
4. Logging trace ID

Important: Always call `remove()` to avoid memory leaks in thread pools.

---

## 58. What is deadlock?

Deadlock happens when two or more threads wait forever for each other’s lock.

Example:

```java
Object lock1 = new Object();
Object lock2 = new Object();

Thread t1 = new Thread(() -> {
    synchronized (lock1) {
        synchronized (lock2) {
            System.out.println("Thread 1");
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized (lock2) {
        synchronized (lock1) {
            System.out.println("Thread 2");
        }
    }
});
```

Thread-1 holds `lock1` and waits for `lock2`.
Thread-2 holds `lock2` and waits for `lock1`.

---

## 59. How can we avoid deadlock?

Ways to avoid deadlock:

1. Always acquire locks in same order
2. Avoid nested locks
3. Use timeout with `tryLock()`
4. Keep synchronized blocks small
5. Avoid calling external services inside locks

Example using lock ordering:

```java
synchronized (lock1) {
    synchronized (lock2) {
        // safe if every thread follows same order
    }
}
```

Example using `tryLock()`:

```java
if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

---

## 60. Difference between deadlock, livelock, and starvation?

| Concept    | Meaning                               |
| ---------- | ------------------------------------- |
| Deadlock   | Threads wait forever                  |
| Livelock   | Threads keep reacting but no progress |
| Starvation | One thread never gets CPU/resource    |

Example:

* Deadlock: A waits for B, B waits for A.
* Livelock: Two people move left-right repeatedly to let each other pass.
* Starvation: Low-priority thread never gets chance.

---

# Real Interview Scenario-Based Questions

## 61. How do you make a singleton thread-safe?

### Approach 1: Enum Singleton

```java
public enum Singleton {
    INSTANCE;
}
```

This is the safest and simplest.

### Approach 2: Double-checked locking

```java
class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

`volatile` is important here to prevent visibility and instruction reordering issues.

---

## 62. What is happens-before relationship?

Happens-before is a Java Memory Model rule that defines visibility between threads.

Examples:

1. Unlock happens-before subsequent lock on same monitor
2. Volatile write happens-before volatile read
3. Thread start happens-before code inside started thread
4. Thread completion happens-before successful join

Example:

```java
volatile boolean flag = false;

Thread t1 = new Thread(() -> flag = true);

Thread t2 = new Thread(() -> {
    if (flag) {
        System.out.println("Visible");
    }
});
```

---

## 63. What is Java Memory Model?

Java Memory Model defines how threads interact through memory.

It explains:

1. Visibility
2. Atomicity
3. Ordering
4. Happens-before rules

Without proper synchronization, one thread may not see the latest value updated by another thread.

---

## 64. Why is immutability useful in multithreading?

Immutable objects are naturally thread-safe because their state cannot change after creation.

Example:

```java
final class Employee {
    private final String name;
    private final int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

Benefits:

1. No synchronization needed
2. Safe sharing between threads
3. Less chance of bugs

---

## 65. What are common problems in multithreading?

Common problems:

1. Race condition
2. Deadlock
3. Starvation
4. Livelock
5. Visibility issue
6. Thread leakage
7. Memory leakage with ThreadLocal
8. Too many threads
9. Incorrect thread pool configuration
10. Blocking calls inside async logic

---

## 66. What is parallel stream? Is it always good?

Parallel stream uses multiple threads internally from ForkJoinPool.

```java
list.parallelStream()
    .map(this::process)
    .collect(Collectors.toList());
```

It is useful for CPU-intensive independent tasks.

But it is not always good.

Avoid parallel stream when:

1. Task is small
2. Task is IO-heavy
3. Shared mutable state exists
4. Ordering is important
5. Application already uses thread pools heavily

---

## 67. What is ForkJoinPool?

`ForkJoinPool` is designed for divide-and-conquer tasks.

Example:

Large task → split into smaller tasks → process in parallel → combine result.

It uses work-stealing algorithm.

Used internally by:

1. Parallel streams
2. RecursiveTask
3. RecursiveAction

---

## 68. What is work stealing?

Work stealing means an idle thread can steal tasks from another busy thread’s queue.

This improves CPU utilization.

Used by ForkJoinPool.

---

## 69. How many threads should we create in a thread pool?

It depends on task type.

### CPU-intensive task

```text
Number of threads ≈ Number of CPU cores
```

### IO-intensive task

```text
Number of threads can be higher than CPU cores
```

Because IO tasks spend time waiting for DB/API/file/network.

Example:

* CPU-heavy image processing: 4 cores → 4 threads
* IO-heavy API calls: 4 cores → maybe 20–50 threads depending on latency and system capacity

---

## 70. What happens if we create too many threads?

Problems:

1. High memory usage
2. High context switching
3. CPU overhead
4. Slow performance
5. OutOfMemoryError
6. System instability

Each thread needs stack memory.

---

## 71. What is thread leakage?

Thread leakage means threads are created but never stopped or returned properly.

Example:

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
executor.submit(task);
// forgot executor.shutdown()
```

In server applications, this can slowly exhaust resources.

---

## 72. Difference between shutdown and shutdownNow?

| shutdown()              | shutdownNow()              |
| ----------------------- | -------------------------- |
| Graceful shutdown       | Immediate shutdown attempt |
| Existing tasks complete | Interrupts running tasks   |
| No new tasks accepted   | No new tasks accepted      |
| Safer                   | More aggressive            |

Example:

```java
executor.shutdown();
```

```java
executor.shutdownNow();
```

Best practice:

```java
executor.shutdown();
try {
    if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
        executor.shutdownNow();
    }
} catch (InterruptedException e) {
    executor.shutdownNow();
    Thread.currentThread().interrupt();
}
```

---

## 73. What is ScheduledExecutorService?

It is used to run tasks after delay or periodically.

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

scheduler.schedule(() -> {
    System.out.println("Runs after 5 seconds");
}, 5, TimeUnit.SECONDS);
```

Periodic task:

```java
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("Runs repeatedly");
}, 0, 10, TimeUnit.SECONDS);
```

---

## 74. Difference between scheduleAtFixedRate and scheduleWithFixedDelay?

| scheduleAtFixedRate                  | scheduleWithFixedDelay                    |
| ------------------------------------ | ----------------------------------------- |
| Runs based on fixed frequency        | Delay starts after previous task finishes |
| Can try to maintain regular schedule | Safer for long-running tasks              |
| Good for heartbeat                   | Good for cleanup jobs                     |

Example:

```java
scheduleAtFixedRate(task, 0, 10, TimeUnit.SECONDS);
```

Runs every 10 seconds based on start time.

```java
scheduleWithFixedDelay(task, 0, 10, TimeUnit.SECONDS);
```

Runs 10 seconds after previous execution finishes.

---

## 75. What is CompletableFuture allOf?

`allOf()` waits for all futures to complete.

```java
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> "User");
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> "Order");

CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2);

all.join();

System.out.println(f1.join());
System.out.println(f2.join());
```

Use case: Call multiple independent APIs in parallel.

---

## Best 50 to Focus First for Interview

Since you asked top 50, focus strongly on these:

1. Thread vs Process
2. Runnable vs Thread
3. start vs run
4. Thread lifecycle
5. Context switching
6. Daemon thread
7. Race condition
8. Thread safety
9. synchronized
10. Object lock
11. Class lock
12. Static synchronized
13. Reentrant behavior
14. volatile
15. AtomicInteger
16. CAS
17. wait/notify/notifyAll
18. sleep vs wait
19. join
20. interrupt
21. Deadlock
22. Deadlock prevention
23. Livelock
24. Starvation
25. ExecutorService
26. Thread pool
27. ThreadPoolExecutor
28. corePoolSize
29. maxPoolSize
30. BlockingQueue
31. Rejection policy
32. execute vs submit
33. Runnable vs Callable
34. Future
35. CompletableFuture
36. CompletableFuture exception handling
37. CountDownLatch
38. CyclicBarrier
39. Semaphore
40. ReentrantLock
41. ReadWriteLock
42. ConcurrentHashMap
43. CopyOnWriteArrayList
44. ThreadLocal
45. ThreadLocal memory leak
46. Java Memory Model
47. happens-before
48. Immutable object
49. Parallel stream
50. ForkJoinPool

# One Strong Interview Answer Example

### Question: How would you explain race condition and solve it?

A race condition occurs when multiple threads access and modify shared data at the same time, and the final result depends on thread execution order.

For example:

```java
class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }
}
```

`count++` is not atomic. It performs read, increment, and write. If two threads execute it together, both may read the same value and overwrite each other’s update.

We can solve it using synchronization:

```java
public synchronized void increment() {
    count++;
}
```

Or using AtomicInteger:

```java
private AtomicInteger count = new AtomicInteger();

public void increment() {
    count.incrementAndGet();
}
```

For simple counters, `AtomicInteger` is better because it avoids traditional locking and uses CAS internally.

# Final Revision Notes

For interviews, remember these points:

* `synchronized` gives atomicity + visibility.
* `volatile` gives visibility only.
* `AtomicInteger` uses CAS.
* `wait()` releases lock, `sleep()` does not.
* `start()` creates a new thread, `run()` does not.
* `ThreadLocal` must be cleaned using `remove()`.
* `ConcurrentHashMap` is better than synchronized HashMap.
* `ThreadPoolExecutor` is very important for senior interviews.
* Always handle `InterruptedException` properly.
* Avoid unbounded thread pools in production.
* Immutable objects are naturally thread-safe.
* Deadlock is mostly prevented by lock ordering and timeout-based locking.
