Below are **Top 50 Kafka Interview Questions** prepared for your **Java Backend / Spring Boot / Microservices interview preparation**.

Kafka is mainly used for **event streaming**, **asynchronous communication**, **real-time data pipelines**, and **microservice decoupling**. In Kafka, producers write events, consumers read events, and both are decoupled, which is one reason Kafka scales well. Kafka also supports strong delivery guarantees including exactly-once processing when correctly configured. ([Apache Kafka][1])

---

# Kafka Interview Questions: Beginner to Advanced

## 1. What is Apache Kafka?

**Apache Kafka** is a distributed event streaming platform used to publish, store, process, and consume streams of records in real time.

In simple words, Kafka works like a **high-performance messaging system** where one service sends events and another service consumes them.

Example:

```text
Order Service  --->  Kafka Topic  --->  Payment Service
```

In microservices, Kafka helps services communicate asynchronously without directly depending on each other.

---

## 2. Why do we use Kafka in microservices?

Kafka is used in microservices for:

1. Asynchronous communication.
2. Loose coupling between services.
3. Event-driven architecture.
4. High throughput message processing.
5. Real-time data streaming.
6. Retry and replay capability.

Example:

```text
Order placed event
   ↓
Kafka
   ↓
Payment Service
Inventory Service
Notification Service
```

Here, Order Service does not directly call all other services. It simply publishes an event.

---

## 3. What is a Kafka topic?

A **topic** is a logical channel where messages are published.

Example:

```text
order-events
payment-events
user-events
email-events
```

Producer writes data into a topic, and consumers read data from a topic.

---

## 4. What is a Kafka partition?

A **partition** is a smaller unit of a topic. Kafka splits a topic into partitions for scalability and parallel processing.

Example:

```text
Topic: order-events

Partition-0
Partition-1
Partition-2
```

If a topic has 3 partitions, multiple consumers can process data in parallel.

---

## 5. Why are partitions important in Kafka?

Partitions are important because they provide:

1. Parallelism.
2. Scalability.
3. High throughput.
4. Ordering within a partition.
5. Load distribution across brokers.

Important interview point:

```text
Kafka guarantees ordering only within a partition, not across the full topic.
```

---

## 6. What is a Kafka broker?

A **broker** is a Kafka server. It stores topic partitions and serves producer/consumer requests.

A Kafka cluster usually has multiple brokers.

Example:

```text
Kafka Cluster
 ├── Broker 1
 ├── Broker 2
 └── Broker 3
```

Each broker can store multiple partitions of different topics.

---

## 7. What is a Kafka cluster?

A **Kafka cluster** is a group of Kafka brokers working together.

Example:

```text
Kafka Cluster = Broker 1 + Broker 2 + Broker 3
```

A cluster provides:

1. Fault tolerance.
2. Scalability.
3. High availability.
4. Distributed storage.

---

## 8. What is a Kafka producer?

A **producer** is an application that sends messages to Kafka topics.

Example:

```text
Order Service produces order-created event to Kafka.
```

Java example:

```java
ProducerRecord<String, String> record =
        new ProducerRecord<>("order-events", "order-101", "Order Created");

producer.send(record);
```

---

## 9. What is a Kafka consumer?

A **consumer** is an application that reads messages from Kafka topics.

Example:

```text
Payment Service consumes order-created event from Kafka.
```

Java example:

```java
ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));

for (ConsumerRecord<String, String> record : records) {
    System.out.println(record.value());
}
```

---

## 10. What is a consumer group?

A **consumer group** is a group of consumers working together to consume messages from a topic.

Example:

```text
Topic has 3 partitions.

Consumer Group: payment-service-group

Consumer-1 -> Partition-0
Consumer-2 -> Partition-1
Consumer-3 -> Partition-2
```

Each partition is consumed by only one consumer inside the same consumer group at a time.

---

## 11. Can multiple consumer groups read the same topic?

Yes.

Different consumer groups can independently read the same topic.

Example:

```text
Topic: order-events

Consumer Group 1: payment-service
Consumer Group 2: inventory-service
Consumer Group 3: notification-service
```

Each group gets its own copy of the messages logically because each group maintains its own offset.

---

## 12. What is an offset in Kafka?

An **offset** is the position of a message inside a partition.

Example:

```text
Partition-0:
Offset 0 -> message A
Offset 1 -> message B
Offset 2 -> message C
```

Kafka uses offsets to track how much data a consumer has read.

---

## 13. Where are offsets stored?

Offsets are stored in Kafka’s internal topic:

```text
__consumer_offsets
```

This helps Kafka remember the last consumed message for each consumer group.

---

## 14. What is `auto.offset.reset`?

`auto.offset.reset` controls where a consumer should start reading when there is no previously committed offset.

Common values:

```text
earliest -> start from beginning
latest   -> start from latest message
none     -> throw exception if offset is missing
```

Example:

```properties
auto.offset.reset=earliest
```

This is useful when a new consumer group starts for the first time.

---

## 15. What is `enable.auto.commit`?

`enable.auto.commit=true` means the consumer automatically commits offsets at regular intervals. Kafka’s Java consumer docs describe auto-commit as committing offsets automatically based on `auto.commit.interval.ms`. ([Apache Kafka][2])

Example:

```properties
enable.auto.commit=true
auto.commit.interval.ms=5000
```

In production, many teams prefer manual commit for better control.

---

## 16. What is manual offset commit?

Manual commit means the application commits offset only after successful processing.

Example:

```java
consumer.commitSync();
```

This is useful for avoiding message loss.

Example flow:

```text
1. Read message
2. Process message
3. Save to DB
4. Commit offset
```

If the app crashes before committing, Kafka can reprocess the message.

---

## 17. Difference between `commitSync()` and `commitAsync()`?

| Method          | Meaning                                                     |
| --------------- | ----------------------------------------------------------- |
| `commitSync()`  | Blocking call. Waits for commit response. Safer but slower. |
| `commitAsync()` | Non-blocking call. Faster but less safe.                    |

Interview answer:

Use `commitSync()` when correctness is more important. Use `commitAsync()` when performance is more important and duplicate processing is acceptable.

---

## 18. What is message key in Kafka?

A **message key** decides which partition the message will go to.

Example:

```java
new ProducerRecord<>("order-events", "user-101", "Order Created");
```

Here, `user-101` is the key.

Messages with the same key usually go to the same partition, which helps maintain ordering for that key.

Example:

```text
All orders of user-101 go to Partition-1
```

---

## 19. How does Kafka decide the partition?

Kafka decides partition using:

1. Explicit partition number, if provided.
2. Key hash, if key is provided.
3. Default partitioner, if no key is provided.

Example:

```text
hash(key) % number_of_partitions
```

---

## 20. Does Kafka guarantee message ordering?

Yes, but only **within a partition**.

Example:

```text
Partition-0:
Order-1
Order-2
Order-3
```

These messages will be read in the same order.

But Kafka does not guarantee ordering across multiple partitions.

---

## 21. What is replication in Kafka?

Replication means Kafka keeps copies of partitions on multiple brokers.

Example:

```text
Topic: order-events
Partition-0 replication factor = 3

Leader copy   -> Broker 1
Follower copy -> Broker 2
Follower copy -> Broker 3
```

If Broker 1 fails, one follower can become the new leader.

---

## 22. What is replication factor?

Replication factor defines how many copies of a partition exist.

Example:

```text
replication.factor=3
```

This means each partition has 3 copies.

Production recommendation is usually 3 for important data.

---

## 23. What are leader and follower replicas?

For each partition:

1. One replica is the **leader**.
2. Other replicas are **followers**.

Producer and consumer interact with the leader replica. Followers copy data from the leader.

---

## 24. What is ISR in Kafka?

ISR means **In-Sync Replicas**.

These are replicas that are fully caught up with the leader.

Example:

```text
Leader: Broker 1
ISR: Broker 1, Broker 2, Broker 3
```

If one follower becomes slow, it may be removed from ISR.

---

## 25. What is `acks` in Kafka producer?

`acks` controls how many broker acknowledgments the producer waits for.

Important values:

```text
acks=0   -> Producer does not wait for acknowledgment.
acks=1   -> Leader acknowledges after writing.
acks=all -> Leader waits for all in-sync replicas.
```

Kafka producer docs explain that `acks=all` gives the strongest guarantee because the leader waits for in-sync replicas; idempotence also requires `acks=all`. ([Apache Kafka][3])

---

## 26. What is `min.insync.replicas`?

`min.insync.replicas` defines the minimum number of replicas that must acknowledge a write when `acks=all`.

Example:

```properties
replication.factor=3
min.insync.replicas=2
acks=all
```

This means at least 2 replicas must confirm the write.

Good production combination:

```text
replication.factor=3
min.insync.replicas=2
acks=all
```

---

## 27. What is Kafka producer idempotence?

Idempotence means the producer avoids duplicate messages during retries.

Example:

```properties
enable.idempotence=true
acks=all
retries=2147483647
```

Kafka docs say idempotence ensures exactly one copy of each message is written to the stream; it requires `acks=all`, retries greater than zero, and `max.in.flight.requests.per.connection <= 5`. Idempotence is enabled by default if there are no conflicting configurations. ([Apache Kafka][3])

---

## 28. What is the difference between at-most-once, at-least-once, and exactly-once?

| Delivery Type | Meaning                                        | Risk                 |
| ------------- | ---------------------------------------------- | -------------------- |
| At-most-once  | Message may be lost but not duplicated         | Data loss            |
| At-least-once | Message will not be lost but may be duplicated | Duplicate processing |
| Exactly-once  | Message is processed exactly once              | More complex         |

Practical interview answer:

Most applications use **at-least-once** with idempotent processing.

---

## 29. How to achieve at-least-once in Kafka?

At-least-once can be achieved by committing offset after successful processing.

Example:

```text
1. Read message
2. Process message
3. Commit offset
```

If the app crashes after processing but before committing, the message may be processed again. So your consumer logic should be idempotent.

---

## 30. How to achieve exactly-once in Kafka?

Exactly-once requires:

1. Idempotent producer.
2. Kafka transactions.
3. Proper offset management.
4. Transactional read-process-write pattern.

Kafka supports exactly-once delivery semantics for reliable fault-tolerant applications when correctly configured. ([Confluent Documentation][4])

Example producer properties:

```properties
enable.idempotence=true
acks=all
transactional.id=order-tx-producer-1
```

---

## 31. What are Kafka transactions?

Kafka transactions allow multiple writes to be committed or rolled back together.

Example:

```java
producer.initTransactions();

try {
    producer.beginTransaction();

    producer.send(new ProducerRecord<>("payment-events", "p1", "Payment Done"));
    producer.send(new ProducerRecord<>("invoice-events", "i1", "Invoice Created"));

    producer.commitTransaction();
} catch (Exception e) {
    producer.abortTransaction();
}
```

If anything fails, Kafka can abort the transaction.

---

## 32. What is consumer rebalance?

A **rebalance** happens when partitions are reassigned among consumers in a consumer group.

Rebalance can happen when:

1. New consumer joins.
2. Existing consumer leaves.
3. Consumer crashes.
4. New partitions are added.
5. Consumer becomes too slow.

Example:

```text
Before:
Consumer-1 -> P0, P1
Consumer-2 -> P2

After new consumer joins:
Consumer-1 -> P0
Consumer-2 -> P1
Consumer-3 -> P2
```

---

## 33. Why is rebalance dangerous?

Rebalance can temporarily stop consumption and may cause duplicate processing if offset handling is not proper.

Best practices:

1. Keep processing fast.
2. Tune `max.poll.interval.ms`.
3. Use manual commits carefully.
4. Use cooperative rebalancing where supported.
5. Make consumers idempotent.

---

## 34. What is Kafka consumer lag?

Consumer lag means how far the consumer is behind the latest message.

Formula:

```text
lag = latest offset - committed offset
```

Example:

```text
Latest offset: 1000
Consumer committed offset: 700
Lag: 300
```

High lag means the consumer is not processing fast enough.

---

## 35. How do you reduce Kafka consumer lag?

You can reduce lag by:

1. Increasing number of partitions.
2. Increasing number of consumers in the group.
3. Optimizing consumer processing logic.
4. Using batch processing.
5. Increasing poll records carefully.
6. Scaling downstream DB/API.
7. Avoiding slow external calls inside listener.

Important:

```text
Number of active consumers in one group cannot exceed number of partitions.
```

If topic has 3 partitions and 5 consumers, only 3 consumers will actively consume.

---

## 36. What is `max.poll.records`?

`max.poll.records` controls how many records a consumer can fetch in one poll.

Example:

```properties
max.poll.records=500
```

If processing is slow, reduce it. If processing is fast, increasing it may improve throughput.

---

## 37. What is `max.poll.interval.ms`?

`max.poll.interval.ms` is the maximum time allowed between two `poll()` calls.

If the consumer takes too long to process records and does not call `poll()` within this time, Kafka considers it unhealthy and triggers rebalance.

Example:

```properties
max.poll.interval.ms=300000
```

Default is commonly 5 minutes.

---

## 38. What is `session.timeout.ms`?

`session.timeout.ms` is the time Kafka waits before considering a consumer dead if it does not receive heartbeats.

Example:

```properties
session.timeout.ms=45000
```

If consumer crashes, Kafka waits for this timeout and then triggers rebalance.

---

## 39. Difference between `session.timeout.ms` and `max.poll.interval.ms`?

| Property               | Purpose                                       |
| ---------------------- | --------------------------------------------- |
| `session.timeout.ms`   | Detects dead consumer using heartbeat failure |
| `max.poll.interval.ms` | Detects slow consumer processing              |

Simple explanation:

```text
session.timeout.ms = Are you alive?
max.poll.interval.ms = Are you processing too slowly?
```

---

## 40. What is log retention in Kafka?

Kafka stores messages for a configured period or size.

Example:

```properties
log.retention.hours=168
```

This means Kafka stores messages for 7 days.

Important:

Kafka does not delete a message immediately after a consumer reads it. Messages stay until retention policy deletes them.

---

## 41. What is log compaction?

Log compaction keeps the latest value for each key.

Example:

```text
Key: user-101, Value: name=A
Key: user-101, Value: name=B
Key: user-101, Value: name=C
```

After compaction, Kafka may keep only the latest value:

```text
Key: user-101, Value: name=C
```

Log compaction is useful for latest-state topics such as user profile, account balance, product price, or configuration. Confluent describes compaction as retaining at least the latest state for each key. ([Confluent Documentation][5])

---

## 42. Difference between retention and compaction?

| Feature  | Retention     | Compaction                |
| -------- | ------------- | ------------------------- |
| Based on | Time/size     | Message key               |
| Deletes  | Old messages  | Older values for same key |
| Use case | Event history | Latest state              |
| Example  | Order events  | User profile state        |

---

## 43. What is a dead letter topic?

A **Dead Letter Topic**, or DLT, stores messages that failed processing even after retries.

Example:

```text
order-events
order-events-dlt
```

If consumer cannot process a message because of invalid data, it can be sent to DLT.

Spring Kafka supports listener/container error handling and dead-letter recovery patterns through components such as `DeadLetterPublishingRecoverer` and common error handlers. ([Home][6])

---

## 44. How do you handle failed messages in Kafka?

Common strategy:

```text
1. Retry temporary failures.
2. Send permanently failed messages to DLT.
3. Alert/monitor DLT.
4. Fix data or bug.
5. Reprocess DLT if needed.
```

Example:

```text
Temporary error: DB timeout -> retry
Permanent error: invalid JSON -> DLT
```

---

## 45. What is a poison pill message?

A **poison pill** is a bad message that always fails during processing.

Example:

```json
{
  "orderId": null,
  "amount": "abc"
}
```

If not handled properly, this message can block the consumer.

Solution:

1. Validate message.
2. Retry limited times.
3. Send to DLT.
4. Continue processing next messages.

---

## 46. Kafka vs RabbitMQ?

| Feature    | Kafka                              | RabbitMQ                             |
| ---------- | ---------------------------------- | ------------------------------------ |
| Main model | Event streaming                    | Message queue                        |
| Storage    | Stores messages based on retention | Usually removes after acknowledgment |
| Replay     | Strong replay support              | Limited compared to Kafka            |
| Throughput | Very high                          | Good but usually lower               |
| Ordering   | Partition-level ordering           | Queue-level ordering                 |
| Use case   | Event streaming, logs, analytics   | Task queues, request processing      |

Interview answer:

Use Kafka when you need event streaming, replay, high throughput, and multiple consumers. Use RabbitMQ when you need traditional queue-based message routing.

---

## 47. What is Kafka Connect?

Kafka Connect is used to move data between Kafka and external systems.

Example:

```text
MySQL -> Kafka
Kafka -> Elasticsearch
Kafka -> S3
Kafka -> PostgreSQL
```

It reduces the need to write custom producer/consumer code for common integrations.

---

## 48. What is Kafka Streams?

Kafka Streams is a Java library for stream processing.

It is used to transform, aggregate, join, and process Kafka data.

Example:

```text
Input Topic: order-events
Kafka Streams App: calculate total sales
Output Topic: sales-summary
```

Kafka Streams is useful when processing logic is event-driven and continuous.

---

## 49. What is Schema Registry?

Schema Registry is commonly used with Kafka to manage message schemas.

Example formats:

```text
Avro
Protobuf
JSON Schema
```

Why useful?

1. Prevents producer-consumer contract mismatch.
2. Supports schema evolution.
3. Helps avoid breaking changes.

Example:

```text
Producer sends OrderCreated v2
Consumer still understands compatible fields
```

---

## 50. How would you design Kafka for an order processing system?

Good interview answer:

```text
Order Service publishes OrderCreated event to Kafka.
Payment Service consumes it and processes payment.
Inventory Service consumes it and reserves stock.
Notification Service consumes it and sends email/SMS.
```

Possible topics:

```text
order-events
payment-events
inventory-events
notification-events
order-events-dlt
```

Important design points:

1. Use `orderId` as key for ordering.
2. Use replication factor 3.
3. Use `acks=all`.
4. Enable idempotent producer.
5. Use manual offset commit.
6. Make consumers idempotent.
7. Use retry and DLT.
8. Monitor consumer lag.
9. Use schema validation.
10. Secure Kafka using SSL/SASL in production.

---

# Java + Spring Boot Kafka Example

## 1. Producer using `KafkaTemplate`

```java
@Service
public class OrderProducer {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public OrderProducer(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void sendOrderEvent(String orderId, String message) {
        kafkaTemplate.send("order-events", orderId, message);
    }
}
```

Controller:

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    private final OrderProducer orderProducer;

    public OrderController(OrderProducer orderProducer) {
        this.orderProducer = orderProducer;
    }

    @PostMapping("/{orderId}")
    public ResponseEntity<String> createOrder(@PathVariable String orderId) {
        orderProducer.sendOrderEvent(orderId, "Order created for id: " + orderId);
        return ResponseEntity.ok("Order event published");
    }
}
```

---

## 2. Consumer using `@KafkaListener`

```java
@Service
public class OrderConsumer {

    @KafkaListener(topics = "order-events", groupId = "payment-service-group")
    public void consume(String message) {
        System.out.println("Received message: " + message);

        // Business logic:
        // 1. Validate event
        // 2. Process payment
        // 3. Save status in DB
    }
}
```

---

## 3. Manual Acknowledgment Example

```java
@Service
public class OrderConsumer {

    @KafkaListener(topics = "order-events", groupId = "payment-service-group")
    public void consume(String message, Acknowledgment acknowledgment) {
        try {
            System.out.println("Processing: " + message);

            // process message successfully

            acknowledgment.acknowledge();
        } catch (Exception e) {
            System.out.println("Error while processing message: " + e.getMessage());
            throw e;
        }
    }
}
```

Application config:

```yaml
spring:
  kafka:
    consumer:
      bootstrap-servers: localhost:9092
      group-id: payment-service-group
      auto-offset-reset: earliest
      enable-auto-commit: false
    listener:
      ack-mode: manual
```

---

## 4. Producer Reliability Config

```yaml
spring:
  kafka:
    producer:
      bootstrap-servers: localhost:9092
      acks: all
      retries: 10
      properties:
        enable.idempotence: true
        max.in.flight.requests.per.connection: 5
```

This config improves reliability by using idempotence and `acks=all`, which aligns with Kafka producer configuration rules. ([Apache Kafka][3])

---

# Very Important Kafka Interview Scenarios

## Scenario 1: Consumer processed message but crashed before offset commit. What happens?

The message will be consumed again after restart.

This is **at-least-once** behavior.

Solution:

Make consumer idempotent.

Example:

```text
Before processing payment, check if payment already exists for orderId.
```

---

## Scenario 2: Consumer commits offset before processing and then crashes. What happens?

Message may be lost.

This is **at-most-once** behavior.

That is why production consumers often process first and commit later.

---

## Scenario 3: Topic has 3 partitions and consumer group has 5 consumers. What happens?

Only 3 consumers will actively consume. Remaining 2 consumers will stay idle.

Reason:

```text
One partition can be assigned to only one consumer inside the same group.
```

---

## Scenario 4: How to maintain order for a user’s events?

Use the same key for all events of that user.

Example:

```java
kafkaTemplate.send("user-events", userId, eventJson);
```

All events for the same `userId` go to the same partition, preserving order for that user.

---

## Scenario 5: What happens if we increase partitions later?

Increasing partitions can improve parallelism, but it can disturb key-to-partition mapping.

Example:

```text
Before: hash(key) % 3
After:  hash(key) % 6
```

The same key may start going to a different partition after partition count changes.

Interview point:

Plan partition count carefully in production.

---

# Kafka Short Revision Sheet

| Concept             | One-line Meaning                              |
| ------------------- | --------------------------------------------- |
| Topic               | Logical stream of messages                    |
| Partition           | Subdivision of topic for parallelism          |
| Broker              | Kafka server                                  |
| Cluster             | Group of brokers                              |
| Producer            | Sends messages                                |
| Consumer            | Reads messages                                |
| Consumer Group      | Group of consumers sharing work               |
| Offset              | Message position in partition                 |
| Replication         | Copies of partition                           |
| Leader              | Main replica handling read/write              |
| Follower            | Replica copying leader                        |
| ISR                 | Healthy synced replicas                       |
| `acks=all`          | Strong producer acknowledgment                |
| Idempotent Producer | Avoids duplicate writes during retry          |
| Retention           | Time/size-based message storage               |
| Compaction          | Keeps latest value per key                    |
| DLT                 | Failed message storage topic                  |
| Lag                 | Difference between latest and consumed offset |

---

# Best Kafka Interview Answer Template

Use this structure when answering Kafka questions:

```text
1. Define the concept.
2. Explain why it is needed.
3. Give real microservice example.
4. Mention failure handling.
5. Mention production configuration if relevant.
```

Example:

```text
Kafka partition is a subdivision of a topic. It is used for scalability and parallelism. 
For example, order-events topic can have 6 partitions so 6 consumers can process orders in parallel. 
Kafka guarantees ordering only inside a partition, so if we need order-level ordering, we should use orderId as the message key.
```

---

For your **7 years Java backend profile**, focus deeply on these Kafka areas:

```text
1. Topic, partition, offset
2. Consumer group and rebalance
3. Producer acks and idempotence
4. Offset commit strategy
5. At-least-once vs exactly-once
6. Retry and DLT
7. Kafka with Spring Boot
8. Kafka in microservices architecture
9. Consumer lag and scaling
10. Real-time order/payment/inventory scenarios
```

[1]: https://kafka.apache.org/documentation/?utm_source=chatgpt.com "Introduction | Apache Kafka"
[2]: https://kafka.apache.org/23/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html?utm_source=chatgpt.com "KafkaConsumer (kafka 2.3.0 API)"
[3]: https://kafka.apache.org/41/configuration/producer-configs/ "Producer Configs | Apache Kafka"
[4]: https://docs.confluent.io/kafka/design/delivery-semantics.html?utm_source=chatgpt.com "Message Delivery Guarantees for Apache Kafka"
[5]: https://docs.confluent.io/kafka/design/log_compaction.html?utm_source=chatgpt.com "Kafka Log Compaction"
[6]: https://docs.spring.io/spring-kafka/reference/kafka/annotation-error-handling.html "Handling Exceptions :: Spring Kafka"
