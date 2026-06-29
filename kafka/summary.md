I was able to access and analyze your Kafka notes from GitHub. The GitHub page shows the file as a large Kafka note file, and the raw content covers core Kafka, Spring Boot integration, retry/DLQ, idempotency, producer, consumer, topic, partition, broker, offset, end-to-end flow, system design, outbox, transactions, saga, production debugging, performance tuning, schema registry/Avro, and Kafka vs RabbitMQ vs Pulsar. ([GitHub][1])

# 1. High-Level Summary

**Apache Kafka** is a distributed event-streaming platform used to build asynchronous, scalable, fault-tolerant communication between systems. In simple words, Kafka allows one service to publish an event and many other services to consume that event independently.

In microservices, Kafka is useful because direct REST communication can create tight coupling. For example:

```text
Order Service → Payment Service → Notification Service
```

If Payment Service is slow or down, the whole flow can be affected. With Kafka:

```text
Order Service → Kafka Topic → Payment Service
                          → Notification Service
                          → Analytics Service
```

Order Service only publishes an `OrderCreatedEvent`. Payment, notification, and analytics services consume it independently. This improves loose coupling, scalability, retry handling, and failure isolation. Your notes repeatedly focus on this order/payment/notification flow as the main interview-friendly real project example. ([GitHub][2])

For a **7-year Java backend developer**, Kafka should be explained not only as a messaging tool, but as an **event-driven backbone** for microservices. You should be comfortable with producer, consumer, topic, partition, offset, consumer group, replication, ISR, retry, DLQ, idempotency, manual offset commit, outbox pattern, schema registry, monitoring, and production debugging.

# 2. Important Concepts

## 2.1 Producer

A **producer** sends messages/events to Kafka topics. In Spring Boot, we usually use `KafkaTemplate`.

```java
kafkaTemplate.send("order-created", orderId, orderEvent);
```

Important producer concepts:

| Concept     | Meaning                                      |
| ----------- | -------------------------------------------- |
| Serializer  | Converts Java object to bytes                |
| Partitioner | Decides which partition receives the message |
| Key         | Used for partition selection and ordering    |
| ACKS        | Defines producer durability guarantee        |
| Retry       | Retries failed send                          |
| Idempotence | Prevents duplicate writes during retry       |
| Batching    | Groups records for performance               |
| Compression | Reduces network payload                      |

Your notes highlight producer internals as: `send()` → serializer → partitioner → accumulator/batch → sender thread → broker ACK. ([GitHub][2])

## 2.2 Consumer

A **consumer** reads messages from Kafka topics. In Spring Boot, we usually use `@KafkaListener`.

```java
@KafkaListener(topics = "order-created", groupId = "payment-group")
public void consume(OrderEvent event, Acknowledgment ack) {
    processPayment(event);
    ack.acknowledge();
}
```

Important consumer concepts:

| Concept        | Meaning                                          |
| -------------- | ------------------------------------------------ |
| Consumer Group | Multiple consumers share partitions              |
| Offset         | Consumer progress position                       |
| Poll Loop      | Consumer pulls records from Kafka                |
| Manual Commit  | Commit offset after successful processing        |
| Rebalancing    | Partition reassignment when consumers join/leave |
| Consumer Lag   | Pending records not yet processed                |
| Idempotency    | Avoid duplicate processing                       |

Your notes strongly recommend manual commit and committing offset only after successful processing. ([GitHub][2])

## 2.3 Topic

A **topic** is a logical channel where messages are stored.

Example:

```text
order-created
payment-processed
notification-triggered
```

A topic is internally divided into partitions. A topic is not just a simple queue; Kafka stores records based on retention policy, so messages are not deleted immediately after consumption. Your notes clearly mention Kafka as a log where messages remain even after consumption. ([GitHub][2])

## 2.4 Partition

A **partition** is the unit of scalability, ordering, and parallelism in Kafka.

```text
order-created
 ├── partition-0
 ├── partition-1
 └── partition-2
```

Kafka guarantees ordering **only within a partition**, not across the whole topic. If all events for the same order should be processed in sequence, use `orderId` as the key.

```java
kafkaTemplate.send("order-created", orderId, event);
```

Same key goes to the same partition, so order is maintained for that entity. Your notes highlight this as one of the most important interview points. ([GitHub][2])

## 2.5 Broker

A **broker** is a Kafka server. A Kafka cluster contains multiple brokers.

Broker responsibilities:

| Responsibility        | Meaning                                |
| --------------------- | -------------------------------------- |
| Store partitions      | Stores topic partition logs            |
| Handle writes         | Producer writes to leader partition    |
| Handle reads          | Consumer fetches from leader partition |
| Replication           | Keeps follower replicas in sync        |
| Leader election       | Elects new leader when broker fails    |
| Metadata coordination | Earlier ZooKeeper, now KRaft           |

Your notes explain broker as the storage, processing, and coordination unit of Kafka. ([GitHub][2])

## 2.6 Offset

An **offset** is the position of a message inside a partition.

```text
Partition-0:
Offset 0 → Order A
Offset 1 → Order B
Offset 2 → Order C
```

Offset is **partition-specific**, not global. Consumer groups commit offsets to track progress. Your notes mention `__consumer_offsets` and explain that offset commit decides whether duplicate processing or data loss can happen. ([GitHub][2])

## 2.7 Consumer Group

A **consumer group** is a group of consumers working together.

Rule:

```text
One partition can be assigned to only one consumer within the same group.
```

Example:

```text
Topic has 3 partitions
Consumer Group has 3 consumers

C1 → P0
C2 → P1
C3 → P2
```

Different consumer groups can read the same message independently.

## 2.8 Replication, Leader, Follower, ISR

Kafka partitions are replicated across brokers.

```text
Partition-0:
Leader   → Broker-1
Follower → Broker-2
Follower → Broker-3
```

Producer and consumer interact with the leader. Followers replicate the leader data. ISR means **In-Sync Replicas**, replicas that are caught up with the leader. If the leader fails, Kafka elects a new leader from ISR. ([GitHub][2])

## 2.9 Retention

Kafka does not delete records after a consumer reads them. It deletes based on retention configuration.

```properties
retention.ms=604800000
retention.bytes=1073741824
```

This enables replay, debugging, analytics, and recovery.

## 2.10 Retry and DLQ

In production, failed messages should not be lost. Typical flow:

```text
Message fails → Retry 3 times → Still fails → Send to DLQ
```

Spring Kafka supports `DefaultErrorHandler` and `DeadLetterPublishingRecoverer` for retry and dead-letter-topic handling; the Spring docs also describe DLT publishing and retry/backoff configuration. ([Home][3])

## 2.11 Idempotency

Kafka consumers can receive duplicates because of retry, rebalance, or crash before offset commit. So consumers must be idempotent.

```java
if (processedEventRepository.existsByEventId(event.getEventId())) {
    return;
}
process(event);
processedEventRepository.save(new ProcessedEvent(event.getEventId()));
```

Your notes repeatedly call idempotency a must-have production concept. ([GitHub][2])

## 2.12 Outbox Pattern

Outbox Pattern solves this problem:

```java
saveOrderToDB();     // success
publishToKafka();    // failed
```

Result: DB has order, but Kafka has no event.

Solution: save business data and event in the same DB transaction.

```text
BEGIN
  Save Order
  Save Outbox Event
COMMIT
```

A scheduler or CDC tool publishes the outbox event to Kafka later. Your notes explain polling and CDC/Debezium options. ([GitHub][2])

## 2.13 Transactions and Exactly Once

Kafka exactly-once processing uses idempotent producer, transactions, transaction-aware consumers, and offset-message commit together. But your notes correctly warn that Kafka exactly-once works mainly inside the Kafka ecosystem; for external DBs, you still need idempotency and/or Outbox Pattern. ([GitHub][2])

## 2.14 Saga Pattern

Saga Pattern manages distributed transactions across microservices. Each service performs a local transaction and publishes an event. If a later step fails, compensating actions are triggered.

Example:

```text
Order Created → Payment Success → Inventory Failed
Compensation: Refund Payment → Cancel Order
```

Your notes explain choreography, orchestration, compensation, and why compensation is not the same as rollback. ([GitHub][2])

## 2.15 Schema Registry and Avro

JSON is simple but can become risky in large event-driven systems because schema changes may break consumers. Avro with Schema Registry gives compact messages, schema validation, and schema evolution. Your notes compare JSON vs Avro and explain backward/forward/full compatibility. ([GitHub][2])

# 3. Interview-Focused Explanation

In interviews, explain Kafka like this:

> “Kafka is a distributed event-streaming platform used for asynchronous communication between microservices. In one of my project-style architectures, when an order is created, Order Service publishes an `OrderCreatedEvent` to Kafka. Payment Service consumes it using a consumer group, processes payment, and publishes a `PaymentProcessedEvent`. Notification Service then consumes that event to send email/SMS. We use partitions for parallel processing, `orderId` as key to maintain ordering per order, manual offset commit to avoid data loss, retry and DLQ for failures, and idempotency to avoid duplicate processing. For reliable DB-to-Kafka publishing, we can use Outbox Pattern. We monitor consumer lag, broker health, throughput, and DLQ using Prometheus/Grafana.”

This answer sounds senior because it covers:

1. Why Kafka is used.
2. How Kafka works internally.
3. How you use it with Spring Boot.
4. How production failures are handled.
5. How reliability and consistency are achieved.

# 4. Top Interview Questions and Answers

## Basic Questions

### 1. What is Kafka?

Kafka is a distributed event-streaming platform used to publish, store, process, and consume real-time data. It is commonly used in microservices for asynchronous communication, event-driven architecture, logging, analytics, and data pipelines.

Real project use: Order Service publishes `OrderCreatedEvent`, and Payment/Notification services consume it independently.

---

### 2. Why do we use Kafka in microservices?

Kafka helps avoid tight coupling between services. Instead of one service directly calling many services synchronously, it publishes an event to Kafka. Other services consume that event independently.

Example:

```text
Order Service → Kafka → Payment Service
                      → Notification Service
```

Common follow-up: Kafka is not a full replacement for REST. REST is still used for request/response APIs; Kafka is used for async event flow.

---

### 3. What is a Kafka topic?

A topic is a logical channel where events are stored.

Example:

```text
order-created
payment-success
user-registered
```

Internally, a topic is divided into partitions.

---

### 4. What is a Kafka partition?

A partition is an ordered, append-only log inside a topic. Partitions provide scalability and parallelism.

```text
order-topic
 ├── partition-0
 ├── partition-1
 └── partition-2
```

Ordering is guaranteed only inside one partition.

---

### 5. What is an offset?

Offset is the unique position of a message inside a partition.

```text
Partition-0:
offset 0 → message A
offset 1 → message B
```

Consumers commit offsets to track what they have processed.

---

### 6. What is a Kafka broker?

A broker is a Kafka server. It stores topic partitions, serves producer/consumer requests, manages replication, and participates in leader election.

---

### 7. What is a producer?

A producer sends messages to Kafka topics.

```java
kafkaTemplate.send("order-created", orderId, event);
```

Producer decides partition using key and partitioner.

---

### 8. What is a consumer?

A consumer reads messages from Kafka topics.

```java
@KafkaListener(topics = "order-created", groupId = "payment-group")
public void consume(OrderEvent event) {
    // process event
}
```

---

### 9. What is a consumer group?

A consumer group is a group of consumers sharing the load. Each partition is assigned to only one consumer within the same group.

If topic has 4 partitions and group has 2 consumers, each consumer may process 2 partitions.

---

### 10. Can multiple consumers read the same Kafka message?

Yes, if they belong to different consumer groups.

Example:

```text
payment-group      → reads order-created
notification-group → reads order-created
analytics-group    → reads order-created
```

Each group maintains its own offset.

---

## Intermediate Questions

### 11. How does Kafka maintain ordering?

Kafka maintains ordering only within a partition. To maintain order for the same entity, use the same key.

```java
kafkaTemplate.send("order-created", orderId, event);
```

All events for the same `orderId` go to the same partition.

Common mistake: saying Kafka provides global ordering across all partitions.

---

### 12. What happens if producer sends message without key?

If key is null, Kafka distributes messages across partitions, often using sticky/round-robin-style behavior depending on client version/config. This is good for logs but not good when ordering per entity is required.

Use no key for logs. Use `orderId`, `userId`, or `accountId` when ordering matters.

---

### 13. What is replication factor?

Replication factor means how many copies of a partition exist across brokers.

```text
replication.factor=3
```

Means one leader and two followers.

---

### 14. What is ISR?

ISR means In-Sync Replica. These are replicas that are fully caught up with the leader. If leader fails, Kafka elects a new leader from ISR.

---

### 15. What happens if a broker fails?

If a broker holding a partition leader fails, Kafka elects a new leader from ISR. Producers and consumers then communicate with the new leader.

Production requirement: replication factor should be more than 1.

---

### 16. What is consumer lag?

Consumer lag is the difference between latest offset and committed consumer offset.

```text
Lag = End Offset - Committed Offset
```

High lag means consumers are slower than producers.

---

### 17. How do you reduce consumer lag?

Check whether lag is due to traffic spike, slow DB query, external API, rebalance, or insufficient consumers. Then:

1. Optimize business logic.
2. Increase consumers up to partition count.
3. Increase partitions if needed.
4. Use batch processing.
5. Tune DB queries.
6. Avoid blocking calls in listener thread.

Your notes emphasize that production Kafka issues are often caused by DB, external API, network, serialization, or slow business logic, not Kafka alone. ([GitHub][2])

---

### 18. What is auto offset commit?

Auto commit means Kafka commits offsets automatically at intervals.

Problem: offset may be committed before successful processing, causing data loss.

Better approach in critical flows: manual commit after processing.

---

### 19. How do you manually commit offset in Spring Kafka?

Use `Acknowledgment`.

```java
@KafkaListener(topics = "order-created", groupId = "payment-group")
public void consume(OrderEvent event, Acknowledgment ack) {
    processPayment(event);
    ack.acknowledge();
}
```

Spring Kafka supports manual acknowledgment in listener methods when manual AckMode is configured. ([Home][4])

---

### 20. What happens if consumer crashes after processing but before offset commit?

The message will be consumed again after restart, causing duplicate processing.

Solution: idempotent consumer.

```java
if (processedEventRepository.existsByEventId(event.getEventId())) {
    return;
}
```

---

### 21. What happens if consumer crashes before processing?

No offset is committed, so Kafka redelivers the message. This is safe, provided processing is idempotent.

---

### 22. What is at-most-once delivery?

At-most-once means message may be lost but will not be processed again. This can happen when offset is committed before processing.

Use case: non-critical metrics where loss is acceptable.

---

### 23. What is at-least-once delivery?

At-least-once means message will not be lost, but it may be processed more than once.

This is the most common production model with manual commit plus idempotency.

---

### 24. What is exactly-once delivery?

Exactly-once means no loss and no duplicate effect. In Kafka, this needs idempotent producer, transactions, transaction-aware consumer, and careful offset handling. For DB operations, you still need idempotency or Outbox Pattern.

---

### 25. How do you handle failed messages in Kafka?

Use retry and DLQ.

```text
Consume → Fail → Retry → Retry → DLQ
```

In Spring Kafka, `DefaultErrorHandler` with `DeadLetterPublishingRecoverer` is commonly used. Spring docs describe this retry + dead-letter publishing pattern. ([Home][3])

---

### 26. What is DLQ?

DLQ means Dead Letter Queue/Topic. It stores messages that failed even after retries.

Example:

```text
order-created → order-created.DLT
```

DLQ helps avoid blocking the main consumer and allows later debugging/reprocessing.

---

### 27. What is a poison message?

A poison message is a bad message that always fails.

Example:

```json
{
  "orderId": "ORD-1",
  "amount": -100
}
```

Without DLQ, it may retry forever and block processing.

---

### 28. What is idempotency in Kafka consumer?

Idempotency means processing the same message multiple times should not create wrong results.

Example: duplicate payment should not happen.

```java
if (processedEventRepository.existsByEventId(event.getEventId())) {
    return;
}
processPayment(event);
processedEventRepository.save(new ProcessedEvent(event.getEventId()));
```

---

### 29. What is Kafka rebalancing?

Rebalancing happens when consumers join or leave a group. Kafka reassigns partitions among consumers.

Problem: during rebalance, processing may pause and duplicate processing can happen.

Solution: idempotency, graceful shutdown, static membership, cooperative rebalancing.

---

### 30. Why should we not have more consumers than partitions?

Maximum parallelism is equal to the number of partitions. If a topic has 3 partitions and 5 consumers in the same group, 2 consumers will remain idle.

---

## Advanced Questions

### 31. What is the Outbox Pattern?

Outbox Pattern ensures DB save and Kafka event creation happen reliably.

Instead of:

```java
orderRepository.save(order);
kafkaTemplate.send("order-created", event);
```

Use:

```java
@Transactional
public void createOrder(Order order) {
    orderRepository.save(order);
    outboxRepository.save(new OutboxEvent("ORDER_CREATED", payload, "NEW"));
}
```

Then a scheduler/CDC publishes the outbox event to Kafka.

Use case: avoid DB success but Kafka publish failure.

---

### 32. Why is Outbox Pattern important?

Because DB transaction and Kafka publish are separate operations. If DB save succeeds but Kafka publish fails, other services never receive the event.

Outbox Pattern stores the event in the same DB transaction, making publishing reliable.

---

### 33. What is Saga Pattern?

Saga Pattern manages distributed transactions in microservices using local transactions and compensating actions.

Example:

```text
Order Created
Payment Success
Inventory Failed
→ Refund Payment
→ Cancel Order
```

Kafka is commonly used as the event backbone for choreography-based Saga. ([GitHub][2])

---

### 34. Choreography vs Orchestration Saga?

| Point         | Choreography | Orchestration       |
| ------------- | ------------ | ------------------- |
| Control       | Distributed  | Central controller  |
| Communication | Events       | Commands/API/events |
| Debugging     | Harder       | Easier              |
| Coupling      | Lower        | Moderate            |
| Best for      | Simple flows | Complex workflows   |

---

### 35. What is Kafka transaction?

Kafka transaction groups multiple Kafka operations into one atomic unit.

Example:

```text
Consume order event
Publish payment event
Commit offset
```

All should succeed or fail together.

---

### 36. Does Kafka exactly-once work with database?

Not automatically. Kafka transactions work well inside Kafka, but if you update MySQL/Oracle/DB2 also, you still need idempotency or Outbox Pattern.

This is a very strong senior-level interview point.

---

### 37. What is `acks` in Kafka producer?

`acks` controls durability.

| acks | Meaning                               |
| ---- | ------------------------------------- |
| 0    | Producer does not wait                |
| 1    | Waits for leader only                 |
| all  | Waits for leader and in-sync replicas |

For critical systems like payment/order, use `acks=all`.

---

### 38. What is `enable.idempotence=true`?

It prevents duplicate messages from producer retries. Kafka uses producer ID and sequence numbers to detect duplicate sends.

Use it in critical event flows.

---

### 39. What is batching in Kafka producer?

Batching groups multiple messages into fewer network calls.

```properties
batch.size=16384
linger.ms=5
```

Higher batching improves throughput but may increase latency.

---

### 40. What is compression in Kafka?

Compression reduces message size and network usage.

Common options:

```properties
compression.type=snappy
compression.type=lz4
compression.type=zstd
```

Use compression for high-throughput systems.

---

### 41. What is Schema Registry?

Schema Registry is a central place to manage message schemas. It helps producers and consumers agree on event structure.

Use case: many teams, many services, different event versions.

---

### 42. Why use Avro instead of JSON?

JSON is readable but larger and weaker in schema validation. Avro is compact, fast, and supports schema evolution with Schema Registry.

Use JSON for simple apps. Use Avro/Protobuf for enterprise-scale event streaming.

---

### 43. What is schema evolution?

Schema evolution means changing event structure safely without breaking consumers.

Example: adding a new field with default value is usually safe.

```json
{
  "name": "currency",
  "type": "string",
  "default": "INR"
}
```

---

### 44. What are dangerous schema changes?

Dangerous changes include removing required fields or changing data type from `int` to `string`. These can break consumers.

---

### 45. What is Kafka retention?

Retention controls how long Kafka keeps messages.

```properties
retention.ms=604800000
retention.bytes=1073741824
```

Kafka deletes data based on retention, not after consumption.

---

### 46. What is log compaction?

Log compaction keeps the latest value for each key.

Use case:

```text
userId=1, name=A
userId=1, name=B
```

After compaction, Kafka keeps latest value for `userId=1`.

Useful for user profiles, cache state, configuration updates.

---

### 47. Kafka vs REST?

| Point            | REST                    | Kafka                   |
| ---------------- | ----------------------- | ----------------------- |
| Communication    | Synchronous             | Asynchronous            |
| Coupling         | Tighter                 | Looser                  |
| Response         | Immediate               | Eventual                |
| Failure handling | Timeout/circuit breaker | Retry/DLQ/idempotency   |
| Use case         | Query/request-response  | Event-driven processing |

Use both in real projects.

---

### 48. Kafka vs RabbitMQ?

Kafka is best for event streaming, replay, analytics, and high throughput. RabbitMQ is best for task queues, short-lived jobs, and low-latency message dispatch.

Interview line:

> “RabbitMQ is good for ‘do this task’; Kafka is good for ‘this event happened’.”

Your notes also explain this exact difference. ([GitHub][2])

---

### 49. Kafka vs Pulsar?

Kafka is a mature distributed log/event streaming platform. Pulsar separates compute and storage using BookKeeper and has strong multi-tenancy/geo-replication support. Kafka is more common in enterprise event streaming; Pulsar is strong in cloud-native, multi-tenant systems.

---

### 50. How do you monitor Kafka in production?

Monitor:

| Metric            | Why               |
| ----------------- | ----------------- |
| Consumer lag      | Pending messages  |
| Throughput        | Messages/sec      |
| Error rate        | Failed processing |
| DLQ count         | Bad messages      |
| ISR count         | Replica health    |
| Broker CPU/memory | Broker load       |
| Disk usage        | Storage risk      |
| Rebalance count   | Stability issue   |

Your notes focus on Prometheus and Grafana for monitoring. ([GitHub][2])

# 5. Scenario-Based Questions

## Scenario 1: Payment Service is down. What happens to Kafka messages?

Kafka stores the messages in the topic. When Payment Service comes back, it resumes from the last committed offset. If retention has not expired, messages are still available.

Answer like this:

> “Kafka decouples producer and consumer availability. Order Service can continue publishing events, and Payment Service can consume later. I will monitor consumer lag and ensure retention is sufficient.”

---

## Scenario 2: Consumer processed payment but crashed before offset commit.

The same message will be consumed again. This may cause duplicate payment.

Solution:

```java
if (paymentRepository.existsByEventId(event.getEventId())) {
    return;
}
processPayment(event);
```

Use idempotency.

---

## Scenario 3: Message fails because payload is invalid.

Do not retry forever. Validation errors are usually non-retryable. Send to DLQ directly.

```java
errorHandler.addNotRetryableExceptions(ValidationException.class);
```

---

## Scenario 4: Kafka lag suddenly increases.

Debug approach:

1. Check producer traffic.
2. Check consumer logs.
3. Check DB/external API latency.
4. Check rebalance events.
5. Check broker health.
6. Check partition-wise lag.
7. Scale consumers if partitions allow.
8. Optimize slow processing.

Your notes give almost this exact production answer. ([GitHub][2])

---

## Scenario 5: All messages are going to one partition.

Reason: bad key distribution.

Example mistake:

```java
kafkaTemplate.send("orders", "constant-key", event);
```

Solution: use a better key like `orderId`, `userId`, or composite key.

---

## Scenario 6: Order saved in DB but event not published to Kafka.

Use Outbox Pattern.

```java
@Transactional
public void createOrder(Order order) {
    orderRepository.save(order);
    outboxRepository.save(new OutboxEvent("ORDER_CREATED", payload, "NEW"));
}
```

---

## Scenario 7: Inventory failed after payment success.

Use Saga Pattern with compensation.

```text
InventoryFailedEvent
→ Payment Service refunds
→ Order Service cancels order
```

---

## Scenario 8: Notification email sent twice.

Possible reasons:

1. Duplicate Kafka message.
2. Consumer crash before commit.
3. Retry after timeout.
4. Rebalance.

Solution: idempotency with `eventId` or business key.

---

## Scenario 9: Large file/image needs to be sent through Kafka.

Do not send large payload. Store file in S3/object storage and send reference.

```json
{
  "eventId": "E1",
  "fileUrl": "s3://bucket/file.png"
}
```

---

## Scenario 10: You deployed more consumers but lag did not reduce.

Check partition count. If topic has 3 partitions and you run 10 consumers in same group, only 3 consumers can actively consume.

---

# 6. Code Examples

## 6.1 Event Class

```java
public class OrderEvent {

    private String eventId;
    private String orderId;
    private String status;
    private BigDecimal amount;
    private LocalDateTime eventTime;

    public OrderEvent() {
    }

    public OrderEvent(String eventId, String orderId, String status,
                      BigDecimal amount, LocalDateTime eventTime) {
        this.eventId = eventId;
        this.orderId = orderId;
        this.status = status;
        this.amount = amount;
        this.eventTime = eventTime;
    }

    public String getEventId() {
        return eventId;
    }

    public String getOrderId() {
        return orderId;
    }

    public String getStatus() {
        return status;
    }

    public BigDecimal getAmount() {
        return amount;
    }

    public LocalDateTime getEventTime() {
        return eventTime;
    }
}
```

## 6.2 Producer

```java
@Service
public class OrderEventProducer {

    private final KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public OrderEventProducer(KafkaTemplate<String, OrderEvent> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void publishOrderCreated(OrderEvent event) {
        kafkaTemplate.send("order-created", event.getOrderId(), event);
    }
}
```

Interview point: `orderId` is used as key to maintain ordering per order.

## 6.3 Consumer with Manual Ack

```java
@Service
public class PaymentConsumer {

    private final PaymentService paymentService;

    public PaymentConsumer(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    @KafkaListener(topics = "order-created", groupId = "payment-group")
    public void consume(OrderEvent event, Acknowledgment ack) {
        paymentService.processPayment(event);
        ack.acknowledge();
    }
}
```

## 6.4 Idempotent Consumer

```java
@Service
public class PaymentService {

    private final ProcessedEventRepository processedEventRepository;

    public PaymentService(ProcessedEventRepository processedEventRepository) {
        this.processedEventRepository = processedEventRepository;
    }

    @Transactional
    public void processPayment(OrderEvent event) {

        if (processedEventRepository.existsByEventId(event.getEventId())) {
            return;
        }

        // business logic: debit account / call payment gateway
        // paymentRepository.save(...)

        processedEventRepository.save(new ProcessedEvent(event.getEventId()));
    }
}
```

## 6.5 Retry + DLQ

```java
@Configuration
public class KafkaErrorHandlerConfig {

    @Bean
    public DefaultErrorHandler defaultErrorHandler(KafkaTemplate<Object, Object> template) {

        DeadLetterPublishingRecoverer recoverer =
                new DeadLetterPublishingRecoverer(template,
                        (record, exception) ->
                                new TopicPartition(record.topic() + ".DLT", record.partition()));

        DefaultErrorHandler errorHandler =
                new DefaultErrorHandler(recoverer, new FixedBackOff(2000L, 3));

        errorHandler.addNotRetryableExceptions(
                IllegalArgumentException.class,
                ValidationException.class
        );

        return errorHandler;
    }
}
```

## 6.6 Outbox Pattern

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final OutboxRepository outboxRepository;
    private final ObjectMapper objectMapper;

    public OrderService(OrderRepository orderRepository,
                        OutboxRepository outboxRepository,
                        ObjectMapper objectMapper) {
        this.orderRepository = orderRepository;
        this.outboxRepository = outboxRepository;
        this.objectMapper = objectMapper;
    }

    @Transactional
    public void createOrder(Order order) throws JsonProcessingException {

        orderRepository.save(order);

        OrderEvent event = new OrderEvent(
                UUID.randomUUID().toString(),
                order.getOrderId(),
                "CREATED",
                order.getAmount(),
                LocalDateTime.now()
        );

        OutboxEvent outboxEvent = new OutboxEvent();
        outboxEvent.setEventType("ORDER_CREATED");
        outboxEvent.setPayload(objectMapper.writeValueAsString(event));
        outboxEvent.setStatus("NEW");
        outboxEvent.setCreatedAt(LocalDateTime.now());

        outboxRepository.save(outboxEvent);
    }
}
```

## 6.7 Outbox Publisher

```java
@Component
public class OutboxPublisher {

    private final OutboxRepository outboxRepository;
    private final KafkaTemplate<String, String> kafkaTemplate;

    public OutboxPublisher(OutboxRepository outboxRepository,
                           KafkaTemplate<String, String> kafkaTemplate) {
        this.outboxRepository = outboxRepository;
        this.kafkaTemplate = kafkaTemplate;
    }

    @Scheduled(fixedDelay = 5000)
    @Transactional
    public void publishPendingEvents() {

        List<OutboxEvent> events = outboxRepository.findByStatus("NEW");

        for (OutboxEvent event : events) {
            kafkaTemplate.send("order-created", event.getPayload());
            event.setStatus("SENT");
            outboxRepository.save(event);
        }
    }
}
```

# 7. Difference Between / Comparison Questions

| Comparison                          | Main Difference                                 |
| ----------------------------------- | ----------------------------------------------- |
| Kafka vs REST                       | Kafka async, REST sync                          |
| Kafka vs RabbitMQ                   | Kafka event streaming, RabbitMQ task queue      |
| Topic vs Partition                  | Topic logical, partition physical log           |
| Offset vs Consumer Lag              | Offset is position, lag is pending difference   |
| Producer vs Consumer                | Producer writes, consumer reads                 |
| Consumer Group vs Consumer          | Group shares load, consumer is one instance     |
| Leader vs Follower                  | Leader handles read/write, follower replicates  |
| Auto Commit vs Manual Commit        | Auto can cause data loss, manual gives control  |
| Retry vs DLQ                        | Retry attempts again, DLQ stores failed message |
| At-most-once vs At-least-once       | At-most can lose, at-least can duplicate        |
| At-least-once vs Exactly-once       | Exactly-once needs transactions/idempotency     |
| JSON vs Avro                        | JSON simple/readable, Avro compact/schema-safe  |
| Choreography vs Orchestration Saga  | Event-driven distributed vs central coordinator |
| Outbox vs Direct Publish            | Outbox reliable, direct publish can lose events |
| More Partitions vs Fewer Partitions | More parallelism but more overhead              |

# 8. Common Production Issues

## 8.1 Consumer Lag

Cause: slow consumer, DB latency, external API delay, low consumer count, rebalance.

Fix: optimize logic, scale consumers, increase partitions, batch processing, monitor lag.

## 8.2 Duplicate Messages

Cause: retry, crash before commit, rebalance.

Fix: idempotency using `eventId`.

## 8.3 Poison Message

Cause: bad payload or validation issue.

Fix: non-retryable exception + DLQ.

## 8.4 Rebalancing Storm

Cause: frequent pod restarts, long GC pause, slow poll loop.

Fix: static membership, cooperative rebalancing, graceful shutdown, reduce blocking in listener.

## 8.5 Broker Disk Full

Cause: retention misconfiguration, high traffic.

Fix: monitor disk, configure `retention.ms` and `retention.bytes`, add brokers/storage.

## 8.6 Partition Hotspot

Cause: bad key.

Fix: better key distribution or composite key.

## 8.7 Serialization Error

Cause: producer and consumer payload mismatch.

Fix: schema validation, ErrorHandlingDeserializer, Avro/Schema Registry.

## 8.8 DLQ Growing Continuously

Cause: real business bug, bad deployment, invalid data, schema mismatch.

Fix: inspect DLQ headers, replay after fixing, add alerting.

## 8.9 Message Ordering Broken

Cause: wrong key or partition count changed.

Fix: use stable key; avoid blindly increasing partitions for ordered flows.

## 8.10 Kafka Is Fine but System Slow

Cause is often DB, external API, network, or heavy business logic. Your notes specifically emphasize this production debugging mindset. ([GitHub][2])

# 9. Best Practices

1. Use meaningful topic names: `order-created`, `payment-processed`.
2. Use event IDs in every message.
3. Use `orderId`/`userId` as key when ordering matters.
4. Do not send huge payloads; send S3/reference URL.
5. Use manual offset commit for critical processing.
6. Commit offset only after successful processing.
7. Always design consumers as idempotent.
8. Configure retry and DLQ.
9. Do not retry validation errors forever.
10. Monitor consumer lag.
11. Monitor DLQ count.
12. Use replication factor 3 in production where possible.
13. Use `acks=all` for critical events.
14. Enable idempotent producer for critical flows.
15. Use Outbox Pattern for DB + Kafka consistency.
16. Use Saga Pattern for distributed transactions.
17. Use Avro/Schema Registry for large enterprise systems.
18. Avoid too many partitions without reason.
19. Use Prometheus/Grafana alerts.
20. Test failure scenarios before production.

# 10. Quick Revision Notes

```text
Kafka = Distributed event streaming platform.

Producer = Sends events.
Consumer = Reads events.
Topic = Logical channel.
Partition = Physical ordered log.
Offset = Message position.
Broker = Kafka server.
Consumer Group = Load sharing.
Replication = Fault tolerance.
ISR = In-sync replicas.
Retention = Kafka keeps data based on time/size.
Ordering = Only within partition.
Key = Decides partition.
Lag = End offset - committed offset.
Manual commit = Commit after success.
Retry = Temporary failure handling.
DLQ = Failed message storage.
Idempotency = Avoid duplicate effect.
Outbox = Reliable DB + Kafka publishing.
Saga = Distributed transaction with compensation.
Avro + Schema Registry = Schema-safe event communication.
Monitoring = Lag, DLQ, throughput, ISR, disk.
```

# 11. 2-Minute Interview Answer

> “Apache Kafka is a distributed event-streaming platform used for asynchronous communication between microservices. In a real project, instead of Order Service directly calling Payment, Notification, and Analytics services, Order Service publishes an `OrderCreatedEvent` to a Kafka topic. Other services consume that event independently using their own consumer groups.
>
> Kafka topics are divided into partitions, which provide scalability and parallel processing. Ordering is guaranteed only within a partition, so we use keys like `orderId` to keep all events of one order in sequence. Kafka brokers store partitions and use replication with leader/follower replicas for fault tolerance. Consumers track progress using offsets, and in critical systems we use manual offset commit after successful processing.
>
> In production, Kafka consumers may process duplicate messages because of retries, crashes, or rebalancing, so we implement idempotency using `eventId`. For failures, we configure retry and DLQ so bad messages do not block the system. For DB and Kafka consistency, we use Outbox Pattern, and for distributed transactions across services we use Saga Pattern with compensation. We monitor consumer lag, throughput, DLQ, broker health, disk usage, and ISR using Prometheus and Grafana. So Kafka is not just a messaging tool; it is an event-driven backbone for scalable, fault-tolerant microservices.”

# 12. Final Interview Preparation Checklist

## Core Kafka

* [ ] What is Kafka?
* [ ] Topic, partition, offset, broker
* [ ] Producer and consumer flow
* [ ] Consumer group
* [ ] Replication factor
* [ ] Leader/follower/ISR
* [ ] Retention and replay
* [ ] Ordering guarantee

## Spring Boot Kafka

* [ ] `KafkaTemplate`
* [ ] `@KafkaListener`
* [ ] Producer config
* [ ] Consumer config
* [ ] Manual acknowledgment
* [ ] Retry config
* [ ] DLQ config

## Reliability

* [ ] At-most-once
* [ ] At-least-once
* [ ] Exactly-once
* [ ] Idempotent producer
* [ ] Idempotent consumer
* [ ] Kafka transactions
* [ ] Outbox Pattern

## Microservices Design

* [ ] Event-driven architecture
* [ ] Kafka vs REST
* [ ] Kafka + Saga Pattern
* [ ] Choreography vs orchestration
* [ ] Eventual consistency
* [ ] Compensation transaction

## Production Readiness

* [ ] Consumer lag debugging
* [ ] Poison message handling
* [ ] DLQ monitoring
* [ ] Rebalancing issue
* [ ] Partition hotspot
* [ ] Disk full issue
* [ ] Serialization/schema issue
* [ ] Performance tuning

## Advanced

* [ ] Avro + Schema Registry
* [ ] Schema evolution
* [ ] Kafka vs RabbitMQ
* [ ] Kafka vs Pulsar
* [ ] KRaft vs ZooKeeper
* [ ] Kafka on Docker/Kubernetes
* [ ] Prometheus/Grafana monitoring

**Interview-ready statement:**
You are interview-ready in Kafka when you can explain: **Producer → Topic → Partition → Broker → Consumer → Offset Commit → Retry → DLQ → Idempotency → Outbox/Saga → Monitoring** with one real project flow like Order/Payment/Notification.

[1]: https://github.com/danishkhan141/dev-notes/blob/main/kafka/kafka.md "dev-notes/kafka/kafka.md at main · danishkhan141/dev-notes · GitHub"
[2]: https://github.com/danishkhan141/dev-notes/raw/refs/heads/main/kafka/kafka.md "raw.githubusercontent.com"
[3]: https://docs.spring.io/spring-kafka/reference/kafka/annotation-error-handling.html "Handling Exceptions :: Spring Kafka"
[4]: https://docs.spring.io/spring-kafka/docs/2.8.x/reference/html/?utm_source=chatgpt.com "Spring for Apache Kafka"
