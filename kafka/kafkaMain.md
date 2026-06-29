## 1. High-Level Summary

**Apache Kafka** is a distributed event-streaming platform used to build **asynchronous, scalable, fault-tolerant microservices**.

In simple terms:

**Producer → Kafka Topic → Consumer**

Example:

```text
post-service publishes PostCreatedEvent
        ↓
Kafka topic: post-events
        ↓
notification-service consumes and sends notification
search-service consumes and updates Elasticsearch
audit-service consumes and stores audit logs
```

Kafka is not just a message queue. It stores events in an ordered, durable log. Consumers read events using offsets, and Kafka can retain messages for replay, reprocessing, audit, analytics, and integration between systems.

Kafka was designed for high-throughput real-time data feeds, low-latency delivery, partitioned distributed processing, and fault tolerance in machine failures. ([kafka.apache.org][1])

For interviews, explain Kafka like this:

> Kafka is used when we want services to communicate asynchronously without tight coupling. Instead of one service directly calling many downstream services, it publishes an event to Kafka. Other services consume that event independently. This improves scalability, reliability, and fault isolation in microservices.

---

## 2. Why Kafka Is Important for Java Backend / Microservices Roles

Kafka is important because modern backend systems are not always built only with synchronous REST APIs.

REST is good for request-response communication:

```text
frontend → API Gateway → post-service
```

Kafka is useful for background, asynchronous, event-driven work:

```text
post-service → Kafka → notification-service/search-service/audit-service
```

For a **Senior Java Developer**, Kafka shows that you understand:

| Area                        | Why it matters                                      |
| --------------------------- | --------------------------------------------------- |
| Microservices communication | Decouple services using events                      |
| Scalability                 | Multiple consumers can process data in parallel     |
| Reliability                 | Messages can be retained and replayed               |
| Performance                 | Async processing avoids blocking API responses      |
| Event-driven design         | Services react to business events                   |
| Fault tolerance             | Broker replication and consumer retry mechanisms    |
| Production readiness        | Monitoring lag, retries, DLT, idempotency, ordering |

In interviews, Kafka is commonly asked along with **Spring Boot, microservices, Docker, Kubernetes, AWS, database consistency, retry, idempotency, and observability**.

---

## 3. Core Concepts

### 3.1 Event / Message / Record

A Kafka message is usually called an **event** or **record**.

It contains:

```text
key
value
topic
partition
offset
timestamp
headers
```

Example:

```json
{
  "eventId": "EVT-101",
  "eventType": "POST_CREATED",
  "postId": 501,
  "userId": 10,
  "title": "Spring Boot Kafka Guide",
  "createdAt": "2026-06-29T10:30:00"
}
```

**Interview line:**

> In Kafka, we do not just send raw strings. In real projects, we send business events like UserRegisteredEvent, PostCreatedEvent, PaymentCompletedEvent, or OrderPlacedEvent.

---

### 3.2 Topic

A **topic** is a logical channel where events are published.

Example topics:

```text
user-events
post-events
comment-events
notification-events
audit-events
```

Producer sends data to a topic. Consumer reads data from a topic.

---

### 3.3 Partition

A topic is divided into **partitions**.

Example:

```text
post-events
 ├── partition-0
 ├── partition-1
 └── partition-2
```

Partitions provide:

| Feature      | Explanation                                           |
| ------------ | ----------------------------------------------------- |
| Parallelism  | Multiple consumers can process partitions in parallel |
| Ordering     | Ordering is guaranteed only inside one partition      |
| Scalability  | More partitions allow higher throughput               |
| Distribution | Partitions are spread across brokers                  |

Kafka’s design allows topics to be divided into ordered partitions, and each partition is consumed by one consumer inside a consumer group at a given time. Kafka tracks consumer position using offsets, which makes acknowledgement state lightweight. ([kafka.apache.org][1])

---

### 3.4 Offset

An **offset** is the position of a message inside a partition.

Example:

```text
partition-0
offset 0 → event A
offset 1 → event B
offset 2 → event C
```

Consumer commits offset after processing.

**Important:**

```text
Committed offset = "I have processed messages up to this point"
```

If the consumer fails, it can resume from the last committed offset.

---

### 3.5 Broker

A **broker** is a Kafka server.

A Kafka cluster has multiple brokers:

```text
broker-1
broker-2
broker-3
```

Brokers store partitions and handle producer/consumer requests.

---

### 3.6 Cluster

A Kafka cluster is a group of brokers working together.

In production:

```text
Kafka Cluster
 ├── broker-1
 ├── broker-2
 └── broker-3
```

A topic’s partitions are distributed across brokers.

---

### 3.7 Producer

A **producer** sends events to Kafka topics.

In Spring Boot, we commonly use:

```java
KafkaTemplate<String, Object>
```

Spring Kafka provides `KafkaTemplate` as a high-level abstraction for sending messages, and supports message-driven POJOs for consuming messages. ([Home][2])

Example:

```java
kafkaTemplate.send("post-events", postId, postCreatedEvent);
```

---

### 3.8 Producer Key

The key decides which partition the message goes to.

Example:

```java
kafkaTemplate.send("post-events", String.valueOf(userId), event);
```

If all events for the same `userId` go to the same partition, their order is preserved for that user.

**Interview line:**

> We use keys carefully because Kafka guarantees ordering only within a partition. For user-specific ordering, we can use userId as the message key.

---

### 3.9 Consumer

A **consumer** reads messages from Kafka topics.

In Spring Boot:

```java
@KafkaListener(topics = "post-events", groupId = "notification-service")
public void consume(PostCreatedEvent event) {
    // process event
}
```

---

### 3.10 Consumer Group

A **consumer group** is a group of consumers working together.

Example:

```text
Topic: post-events, 3 partitions

Consumer Group: notification-service
 ├── consumer-1 → partition-0
 ├── consumer-2 → partition-1
 └── consumer-3 → partition-2
```

Each partition is consumed by only one consumer within the same consumer group.

Different consumer groups receive their own copy of the data:

```text
post-events
 ├── notification-service group
 ├── search-service group
 └── audit-service group
```

---

### 3.11 Rebalancing

Rebalancing happens when:

| Trigger                 | Example                     |
| ----------------------- | --------------------------- |
| New consumer joins      | New pod added in Kubernetes |
| Consumer dies           | Pod crash                   |
| Topic partitions change | Partitions increased        |
| Consumer timeout        | Processing too slow         |

Kafka reassigns partitions among active consumers.

**Production issue:**

If processing takes too long and `max.poll.interval.ms` is exceeded, Kafka considers the consumer failed and triggers rebalance. Kafka’s consumer config defines `max.poll.interval.ms` as the maximum delay between polls before a consumer is considered failed and partitions are reassigned. ([kafka.apache.org][3])

---

### 3.12 Serialization and Deserialization

Kafka stores bytes. Java objects must be converted.

| Direction           | Process         |
| ------------------- | --------------- |
| Java object → Kafka | Serialization   |
| Kafka → Java object | Deserialization |

Common serializers:

```text
StringSerializer
JsonSerializer
ByteArraySerializer
AvroSerializer
```

Common deserializers:

```text
StringDeserializer
JsonDeserializer
ErrorHandlingDeserializer
```

In Spring Boot, JSON is commonly used for project-level examples.

---

### 3.13 Retention

Kafka does not delete a message immediately after consumption.

Retention is time/size based.

Example:

```text
retention.ms = 7 days
```

This allows replay and reprocessing.

Example use case:

> Search indexing failed yesterday. After fixing the bug, we can reset offset and reprocess events.

Kafka’s design intentionally retains messages for some period instead of deleting immediately after consumption, giving flexibility to consumers. ([kafka.apache.org][1])

---

### 3.14 Log Compaction

Log compaction keeps the latest value for a key.

Example topic:

```text
user-profile-events
key=user-10, value=old-email
key=user-10, value=new-email
```

After compaction, Kafka keeps the latest value for `user-10`.

Use cases:

```text
user profile cache
configuration events
latest account status
```

---

### 3.15 Replication

Replication means Kafka keeps copies of partitions on multiple brokers.

Example:

```text
post-events partition-0
leader: broker-1
follower: broker-2
follower: broker-3
```

The leader handles reads/writes. Followers replicate data.

Kafka maintains in-sync replicas, and a committed message is not lost as long as at least one in-sync replica remains alive. ([kafka.apache.org][1])

---

### 3.16 ISR — In-Sync Replicas

ISR means replicas that are fully caught up with the leader.

Important configs:

```properties
replication.factor=3
min.insync.replicas=2
acks=all
```

This means:

```text
Producer gets success only when leader and enough in-sync replicas acknowledge.
```

---

### 3.17 Acknowledgement — `acks`

Producer acknowledgement controls durability.

| Config     | Meaning                                    |
| ---------- | ------------------------------------------ |
| `acks=0`   | Producer does not wait for broker response |
| `acks=1`   | Leader writes and responds                 |
| `acks=all` | Leader waits for all in-sync replicas      |

Kafka documents `acks=all` as the strongest durability guarantee, where the leader waits for the full set of in-sync replicas. ([kafka.apache.org][4])

For production:

```properties
acks=all
enable.idempotence=true
```

---

### 3.18 Delivery Semantics

Kafka has three delivery semantics:

| Type          | Meaning                                                                  |
| ------------- | ------------------------------------------------------------------------ |
| At-most-once  | Message may be lost, but not duplicated                                  |
| At-least-once | Message is not lost, but can be duplicated                               |
| Exactly-once  | Message is processed once and only once in supported transactional flows |

Kafka defines at-most-once, at-least-once, and exactly-once semantics, and explains that consumer offset commit timing determines whether messages may be lost or duplicated. ([kafka.apache.org][1])

In most Spring Boot microservices:

```text
At-least-once + idempotent consumer = practical production approach
```

---

### 3.19 Idempotent Producer

Idempotent producer avoids duplicate writes caused by retries.

Config:

```properties
enable.idempotence=true
acks=all
retries=2147483647
max.in.flight.requests.per.connection=5
```

Kafka’s producer config says idempotence ensures exactly one copy of each message is written, and requires compatible settings such as `acks=all`, retries greater than zero, and limited max in-flight requests. ([kafka.apache.org][4])

---

### 3.20 Idempotent Consumer

Even if Kafka producer is idempotent, consumer processing can still duplicate business actions.

Example duplicate issue:

```text
Consumer reads PostCreatedEvent
Sends email
Crashes before committing offset
Same event is consumed again
Email sent again
```

Solution:

```text
event_id table
unique constraint
processed_events table
business idempotency key
```

Example:

```sql
CREATE TABLE processed_events (
    event_id VARCHAR(100) PRIMARY KEY,
    processed_at TIMESTAMP
);
```

---

### 3.21 Dead Letter Topic — DLT / DLQ

A **Dead Letter Topic** stores failed messages after retries are exhausted.

Example:

```text
post-events
post-events-retry
post-events-dlt
```

Use DLT when:

```text
JSON format is invalid
Required field missing
Database constraint failure
Downstream service unavailable after retries
Business validation failure
```

Spring Kafka supports error handlers and DLT publishing. For batch listeners, Spring Kafka documents using `DefaultErrorHandler` with `DeadLetterPublishingRecoverer`; non-blocking retry via `@RetryableTopic` is not supported with batch listeners. ([Home][5])

---

### 3.22 Retry

Retry is used when failure is temporary.

Retry examples:

| Failure                 | Retry?                         |
| ----------------------- | ------------------------------ |
| DB timeout              | Yes                            |
| Network issue           | Yes                            |
| Temporary API failure   | Yes                            |
| Invalid JSON            | No, send to DLT                |
| Missing mandatory field | No, send to DLT                |
| Duplicate event         | No, ignore/idempotent handling |

Spring Kafka supports non-blocking retries using `@RetryableTopic` on `@KafkaListener`, which can create retry and DLT infrastructure. ([Home][6])

---

### 3.23 Kafka vs REST API

| REST                    | Kafka                              |
| ----------------------- | ---------------------------------- |
| Synchronous             | Asynchronous                       |
| Request-response        | Event-driven                       |
| Caller waits            | Caller does not wait               |
| Tight coupling possible | Loose coupling                     |
| Good for queries        | Good for events                    |
| Failure impacts caller  | Failure isolated through retry/DLT |

Example:

Use REST:

```text
Get post details by postId
Login user
Create post API response
```

Use Kafka:

```text
Send notification after post created
Index post in search
Maintain audit trail
Generate analytics
```

---

### 3.24 Kafka vs RabbitMQ

| Kafka                                 | RabbitMQ                          |
| ------------------------------------- | --------------------------------- |
| Event streaming platform              | Message broker                    |
| Retains messages                      | Usually removes after consumption |
| Replay supported                      | Replay not primary use case       |
| High-throughput logs                  | Task/message routing              |
| Partition-based scalability           | Queue/exchange routing            |
| Strong for event-driven microservices | Strong for command/task queues    |

---

### 3.25 Kafka Streams

Kafka Streams is a Java library for stream processing.

Use cases:

```text
real-time aggregation
event transformation
joining streams
counting post views
trending posts
```

Example:

```text
post-view-events → aggregate count → trending-posts
```

For your interview, you can mention Kafka Streams but do not overclaim unless you have implemented it.

---

### 3.26 Kafka Connect

Kafka Connect is used to connect Kafka with external systems.

Examples:

```text
MySQL → Kafka
Kafka → Elasticsearch
Kafka → S3
Kafka → JDBC sink
```

For your blog app:

```text
post-events → Elasticsearch sink
audit-events → S3 sink
```

---

### 3.27 Schema Registry

In real systems, event structure evolves.

Example:

```json
PostCreatedEvent v1:
{
  "postId": 10,
  "title": "Kafka"
}

PostCreatedEvent v2:
{
  "postId": 10,
  "title": "Kafka",
  "categoryId": 5
}
```

Schema Registry helps maintain compatibility when using Avro/Protobuf/JSON schema.

---

### 3.28 Security

Kafka security usually includes:

```text
SSL/TLS encryption
SASL authentication
ACL authorization
```

Official Kafka security docs cover SSL encryption/authentication, SASL authentication, and ACL authorization. ([kafka.apache.org][7])

In interview:

> In production, Kafka is secured using SSL/SASL and ACLs. Services are allowed to produce or consume only specific topics.

---

### 3.29 Monitoring

Important Kafka monitoring metrics:

```text
consumer lag
broker health
under-replicated partitions
failed messages
retry count
DLT count
producer error rate
consumer processing time
rebalance count
```

For Spring Boot:

```text
Actuator + Micrometer + Prometheus + Grafana
```

---

## 4. Interview-Focused Explanation

As a 7-year Java backend developer, do not explain Kafka only theoretically. Explain like this:

> In microservices, I use Kafka for asynchronous communication between services. For example, when a post is created, the post-service publishes a PostCreatedEvent to Kafka. Notification-service, search-service, and audit-service consume the same event using different consumer groups. This avoids tight coupling because post-service does not need to directly call all downstream services.

Then add reliability:

> In production, I would configure producer durability using `acks=all`, replication factor, and idempotent producer. On the consumer side, I would use manual offset acknowledgement or controlled commit strategy, retries for transient failures, DLT for poison messages, and idempotency to avoid duplicate business processing.

Then add operations:

> I would monitor consumer lag, failed records, DLT topics, processing latency, and rebalance events using Actuator/Micrometer/Prometheus/Grafana.

This sounds senior because it covers:

```text
design
coding
reliability
failure handling
production monitoring
```

---

## 5. Project-Based Explanation

### 5.1 Kafka in Your Spring Boot Blog Application

Your current monolith has:

```text
User
Post
Category
Comment
JWT Security
MySQL
Swagger
Actuator
AWS deployment
```

Kafka can be introduced for background tasks.

#### Use case 1: Post created event

When user creates a post:

```text
POST /api/posts
        ↓
post saved in MySQL
        ↓
PostCreatedEvent published to Kafka
        ↓
notification-service sends notification
search-service updates index
audit-service stores audit log
```

**Problem solved:**

Without Kafka:

```text
post-service directly calls notification + search + audit
```

If notification service is down, post creation may become slow/fail.

With Kafka:

```text
post-service publishes event and returns response
downstream services process independently
```

**Interview answer:**

> In my blog application, when a post is created, I can publish a PostCreatedEvent to Kafka. Multiple services can consume it independently. This helps me decouple post creation from notification, search indexing, and audit logging.

---

### 5.2 Kafka in Your Microservices Version

Planned services:

```text
user-service
post-service
category-service
comment-service
notification-service
search-service
audit-service
api-gateway
eureka
```

Kafka topics:

```text
user-events
post-events
comment-events
audit-events
notification-events
```

Events:

```text
UserRegisteredEvent
PostCreatedEvent
PostUpdatedEvent
PostDeletedEvent
CommentAddedEvent
CategoryCreatedEvent
```

Example design:

```text
user-service
  publishes UserRegisteredEvent

post-service
  publishes PostCreatedEvent

notification-service
  consumes UserRegisteredEvent, PostCreatedEvent

search-service
  consumes PostCreatedEvent, PostUpdatedEvent, PostDeletedEvent

audit-service
  consumes all important events
```

---

### 5.3 Kafka with API Gateway and Eureka

API Gateway and Eureka are for synchronous service discovery/routing:

```text
client → api-gateway → post-service
```

Kafka is for asynchronous event communication:

```text
post-service → Kafka → notification-service
```

**Interview line:**

> Eureka and API Gateway help in synchronous microservice communication, while Kafka helps in asynchronous event-driven communication. Both solve different problems.

---

### 5.4 Kafka with Docker/Kubernetes

In Kubernetes:

```text
post-service pod
notification-service pod
Kafka broker/statefulset
Zookeeper/KRaft controller
```

Consumer scaling:

```text
kubectl scale deployment notification-service --replicas=3
```

If topic has 3 partitions, 3 consumer pods can process in parallel.

**Important:**

```text
Number of active consumers in same group <= number of partitions
```

If topic has 3 partitions and 5 consumers in same group:

```text
3 consumers active
2 consumers idle
```

---

### 5.5 Kafka with Jenkins CI/CD

Jenkins pipeline can:

```text
build service
run tests
build Docker image
push image
deploy to Kubernetes
```

Kafka-related checks:

```text
integration tests with EmbeddedKafka/Testcontainers
config validation
environment-specific bootstrap servers
secret-based SASL credentials
```

---

### 5.6 Kafka with AWS

AWS options:

```text
Self-managed Kafka on EC2
Amazon MSK
Confluent Cloud
```

For interviews, say:

> In AWS, for production-grade Kafka, we can use Amazon MSK or managed Kafka platforms. For learning or small projects, Docker-based Kafka setup is enough.

---

## 6. Important Annotations / Classes / Interfaces / Configuration

### 6.1 Maven Dependency

For Spring Boot:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

For tests:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka-test</artifactId>
    <scope>test</scope>
</dependency>
```

---

### 6.2 `KafkaTemplate`

Used to publish messages.

```java
@Autowired
private KafkaTemplate<String, PostCreatedEvent> kafkaTemplate;
```

Send message:

```java
kafkaTemplate.send("post-events", String.valueOf(event.getPostId()), event);
```

Spring Kafka’s `KafkaTemplate` wraps a producer and provides convenient send methods returning `CompletableFuture<SendResult<K,V>>`. ([Home][8])

---

### 6.3 `@KafkaListener`

Used to consume messages.

```java
@KafkaListener(topics = "post-events", groupId = "notification-service")
public void consume(PostCreatedEvent event) {
    // process event
}
```

Spring Kafka supports `@KafkaListener` with topics, groupId, topic partitions, concurrency, metadata headers, batch listeners, and manual acknowledgements. ([Home][9])

---

### 6.4 `NewTopic`

Used to create topic programmatically.

```java
@Bean
public NewTopic postEventsTopic() {
    return TopicBuilder.name("post-events")
            .partitions(3)
            .replicas(1)
            .build();
}
```

For local development, replica can be `1`. For production, use `3`.

---

### 6.5 `ProducerFactory`

Creates Kafka producer instances.

```java
ProducerFactory<String, Object>
```

Usually Spring Boot auto-configures it using `spring.kafka.*` properties.

Spring Boot maps Kafka configuration under `spring.kafka` using `KafkaProperties`. ([Home][10])

---

### 6.6 `ConsumerFactory`

Creates Kafka consumer instances.

```java
ConsumerFactory<String, Object>
```

Used by listener container factory.

---

### 6.7 `ConcurrentKafkaListenerContainerFactory`

Configures listener behavior.

Used for:

```text
concurrency
manual ack
error handler
batch listener
record filter
retry/DLT
```

Example:

```java
@Bean
public ConcurrentKafkaListenerContainerFactory<String, PostCreatedEvent>
postKafkaListenerContainerFactory(
        ConsumerFactory<String, PostCreatedEvent> consumerFactory) {

    ConcurrentKafkaListenerContainerFactory<String, PostCreatedEvent> factory =
            new ConcurrentKafkaListenerContainerFactory<>();

    factory.setConsumerFactory(consumerFactory);
    factory.setConcurrency(3);

    return factory;
}
```

---

### 6.8 `Acknowledgment`

Used for manual offset commit.

```java
@KafkaListener(topics = "post-events", groupId = "notification-service")
public void consume(PostCreatedEvent event, Acknowledgment ack) {
    process(event);
    ack.acknowledge();
}
```

Manual acknowledgement is useful when you want to commit offset only after successful business processing. Spring Kafka documents that manual `AckMode` allows the listener to call `acknowledge()` explicitly. ([Home][11])

---

### 6.9 `@RetryableTopic`

Used for retry topic mechanism.

```java
@RetryableTopic(
        attempts = "3",
        backoff = @Backoff(delay = 2000),
        dltTopicSuffix = "-dlt"
)
@KafkaListener(topics = "post-events", groupId = "notification-service")
public void consume(PostCreatedEvent event) {
    notificationService.sendNotification(event);
}
```

---

### 6.10 `@DltHandler`

Handles DLT messages.

```java
@DltHandler
public void handleDlt(PostCreatedEvent event) {
    log.error("Message moved to DLT: {}", event);
}
```

---

### 6.11 `DefaultErrorHandler`

Used for blocking retry and DLT publishing.

```java
DefaultErrorHandler errorHandler =
        new DefaultErrorHandler(recoverer, new FixedBackOff(1000L, 3L));
```

---

### 6.12 `DeadLetterPublishingRecoverer`

Publishes failed records to DLT.

```java
DeadLetterPublishingRecoverer recoverer =
        new DeadLetterPublishingRecoverer(kafkaTemplate);
```

---

### 6.13 Important Configurations

#### Producer configs

```yaml
spring:
  kafka:
    producer:
      acks: all
      retries: 3
      properties:
        enable.idempotence: true
        max.in.flight.requests.per.connection: 5
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
```

#### Consumer configs

```yaml
spring:
  kafka:
    consumer:
      group-id: notification-service
      auto-offset-reset: earliest
      enable-auto-commit: false
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
      properties:
        spring.json.trusted.packages: "com.danish.blog.events"
```

Kafka’s `enable.auto.commit` controls whether offsets are periodically committed in the background, and `auto.offset.reset` decides what happens when no committed offset exists. ([kafka.apache.org][3])

---

## 7. Code Examples

### 7.1 Event Class

```java
package com.danish.blog.events;

import java.time.LocalDateTime;

public class PostCreatedEvent {

    private String eventId;
    private Long postId;
    private Long userId;
    private String title;
    private LocalDateTime createdAt;

    public PostCreatedEvent() {
    }

    public PostCreatedEvent(String eventId, Long postId, Long userId,
                            String title, LocalDateTime createdAt) {
        this.eventId = eventId;
        this.postId = postId;
        this.userId = userId;
        this.title = title;
        this.createdAt = createdAt;
    }

    public String getEventId() {
        return eventId;
    }

    public Long getPostId() {
        return postId;
    }

    public Long getUserId() {
        return userId;
    }

    public String getTitle() {
        return title;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
}
```

---

### 7.2 Kafka Topic Configuration

```java
package com.danish.blog.config;

import org.apache.kafka.clients.admin.NewTopic;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.TopicBuilder;

@Configuration
public class KafkaTopicConfig {

    @Bean
    public NewTopic postEventsTopic() {
        return TopicBuilder.name("post-events")
                .partitions(3)
                .replicas(1) // use 3 in production cluster
                .build();
    }

    @Bean
    public NewTopic postEventsDltTopic() {
        return TopicBuilder.name("post-events-dlt")
                .partitions(3)
                .replicas(1)
                .build();
    }
}
```

---

### 7.3 Producer Service

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Service
public class PostEventProducer {

    private static final Logger log = LoggerFactory.getLogger(PostEventProducer.class);

    private final KafkaTemplate<String, PostCreatedEvent> kafkaTemplate;

    public PostEventProducer(KafkaTemplate<String, PostCreatedEvent> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void publishPostCreatedEvent(PostCreatedEvent event) {
        String key = String.valueOf(event.getPostId());

        kafkaTemplate.send("post-events", key, event)
                .whenComplete((result, ex) -> {
                    if (ex != null) {
                        log.error("Failed to publish PostCreatedEvent for postId={}",
                                event.getPostId(), ex);
                    } else {
                        log.info("PostCreatedEvent published. topic={}, partition={}, offset={}",
                                result.getRecordMetadata().topic(),
                                result.getRecordMetadata().partition(),
                                result.getRecordMetadata().offset());
                    }
                });
    }
}
```

---

### 7.4 Calling Producer After Post Creation

```java
package com.danish.blog.service;

import com.danish.blog.events.PostCreatedEvent;
import com.danish.blog.kafka.PostEventProducer;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.UUID;

@Service
public class PostService {

    private final PostEventProducer postEventProducer;

    public PostService(PostEventProducer postEventProducer) {
        this.postEventProducer = postEventProducer;
    }

    public void createPost(Long userId, String title, String content) {
        // 1. Save post in MySQL using JPA repository
        Long savedPostId = 101L; // assume generated after DB save

        // 2. Publish event after successful save
        PostCreatedEvent event = new PostCreatedEvent(
                UUID.randomUUID().toString(),
                savedPostId,
                userId,
                title,
                LocalDateTime.now()
        );

        postEventProducer.publishPostCreatedEvent(event);
    }
}
```

---

### 7.5 Consumer Service

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class NotificationEventConsumer {

    private static final Logger log = LoggerFactory.getLogger(NotificationEventConsumer.class);

    @KafkaListener(
            topics = "post-events",
            groupId = "notification-service"
    )
    public void consume(PostCreatedEvent event) {
        log.info("Received PostCreatedEvent: postId={}, userId={}",
                event.getPostId(), event.getUserId());

        // send notification logic
    }
}
```

---

### 7.6 Manual Acknowledgement Consumer

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.support.Acknowledgment;
import org.springframework.stereotype.Service;

@Service
public class SearchIndexConsumer {

    @KafkaListener(
            topics = "post-events",
            groupId = "search-service",
            containerFactory = "manualAckKafkaListenerContainerFactory"
    )
    public void consume(PostCreatedEvent event, Acknowledgment acknowledgment) {

        // 1. Update Elasticsearch/search index
        updateSearchIndex(event);

        // 2. Commit offset only after successful processing
        acknowledgment.acknowledge();
    }

    private void updateSearchIndex(PostCreatedEvent event) {
        // search indexing logic
    }
}
```

---

### 7.7 Manual Ack Container Factory

```java
package com.danish.blog.config;

import com.danish.blog.events.PostCreatedEvent;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.listener.ContainerProperties;

@Configuration
public class KafkaConsumerConfig {

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, PostCreatedEvent>
    manualAckKafkaListenerContainerFactory(
            ConsumerFactory<String, PostCreatedEvent> consumerFactory) {

        ConcurrentKafkaListenerContainerFactory<String, PostCreatedEvent> factory =
                new ConcurrentKafkaListenerContainerFactory<>();

        factory.setConsumerFactory(consumerFactory);
        factory.setConcurrency(3);
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL);

        return factory;
    }
}
```

---

### 7.8 Retry and DLT with `@RetryableTopic`

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.DltHandler;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.annotation.RetryableTopic;
import org.springframework.retry.annotation.Backoff;
import org.springframework.stereotype.Service;

@Service
public class ReliableNotificationConsumer {

    private static final Logger log =
            LoggerFactory.getLogger(ReliableNotificationConsumer.class);

    @RetryableTopic(
            attempts = "3",
            backoff = @Backoff(delay = 2000),
            dltTopicSuffix = "-dlt"
    )
    @KafkaListener(topics = "post-events", groupId = "notification-service")
    public void consume(PostCreatedEvent event) {
        log.info("Processing eventId={}", event.getEventId());

        // temporary failure example
        sendNotification(event);
    }

    @DltHandler
    public void handleDlt(PostCreatedEvent event) {
        log.error("Event moved to DLT. eventId={}, postId={}",
                event.getEventId(), event.getPostId());
    }

    private void sendNotification(PostCreatedEvent event) {
        // notification logic
    }
}
```

---

### 7.9 Idempotent Consumer Example

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import com.danish.blog.repository.ProcessedEventRepository;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class IdempotentPostEventConsumer {

    private final ProcessedEventRepository processedEventRepository;

    public IdempotentPostEventConsumer(ProcessedEventRepository processedEventRepository) {
        this.processedEventRepository = processedEventRepository;
    }

    @Transactional
    @KafkaListener(topics = "post-events", groupId = "audit-service")
    public void consume(PostCreatedEvent event) {

        if (processedEventRepository.existsByEventId(event.getEventId())) {
            return; // duplicate event, safely ignore
        }

        // process event
        saveAuditLog(event);

        // save event id after successful processing
        processedEventRepository.saveEventId(event.getEventId());
    }

    private void saveAuditLog(PostCreatedEvent event) {
        // audit save logic
    }
}
```

---

### 7.10 Docker Compose for Local Kafka

```yaml
version: '3.8'

services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    ports:
      - "2181:2181"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

For newer Kafka setups, you may see KRaft-based Kafka without ZooKeeper. For interviews, focus on core Kafka concepts first.

---

## 8. Common Real-Time Scenarios

### Scenario 1: Consumer is processing duplicate messages

Possible reasons:

```text
consumer processed message but failed before committing offset
rebalance happened
manual ack not called
retry happened
```

Solution:

```text
make consumer idempotent
use eventId
store processed event IDs
commit offset after successful processing
```

---

### Scenario 2: Consumer lag is increasing

Possible reasons:

```text
consumer is slow
not enough partitions
not enough consumer instances
DB/API dependency slow
large messages
batch size too high/low
frequent rebalancing
```

Solution:

```text
scale consumers
increase partitions carefully
optimize DB operations
batch processing
monitor processing time
tune max.poll.records
```

---

### Scenario 3: Messages are not consumed

Check:

```text
topic name correct?
consumer group correct?
auto.offset.reset?
consumer already committed latest offset?
deserializer issue?
consumer application started?
ACL permission?
```

---

### Scenario 4: Message ordering issue

Kafka guarantees ordering only within a partition.

Solution:

```text
use same key for related events
do not randomly distribute events requiring order
avoid too much parallel processing for same key
```

Example:

```java
kafkaTemplate.send("post-events", String.valueOf(postId), event);
```

---

### Scenario 5: Poison message blocks consumer

Poison message means one bad message repeatedly fails.

Solution:

```text
retry limited times
send to DLT
alert support team
fix and replay later
```

---

### Scenario 6: Producer sends message but consumer receives old event structure

Cause:

```text
schema mismatch
old producer version
new consumer expects new field
```

Solution:

```text
schema registry
backward-compatible event design
default values
versioned events
```

---

### Scenario 7: Database update succeeds but Kafka publish fails

This is a classic consistency problem.

Bad flow:

```text
save DB
publish Kafka
Kafka fails
```

DB has data, but event is missing.

Better pattern:

```text
Transactional Outbox Pattern
```

Flow:

```text
save business data + save outbox event in same DB transaction
background publisher reads outbox table
publishes to Kafka
marks event as published
```

**Interview line:**

> For critical business events, I would not directly publish to Kafka inside the same service method after DB save. I would prefer the transactional outbox pattern to avoid DB-Kafka inconsistency.

---

### Scenario 8: Kafka broker down

If Kafka is temporarily down:

```text
producer retries
application logs error
circuit/alerting
outbox prevents event loss
```

---

### Scenario 9: DLT messages increasing

Possible causes:

```text
bad payload
schema mismatch
downstream DB issue
business validation failure
bug in consumer
```

Production action:

```text
monitor DLT
alert team
inspect failed payload
fix issue
reprocess DLT events
```

---

### Scenario 10: Kubernetes scaling issue

If topic has 3 partitions and deployment has 6 pods:

```text
only 3 pods actively consume
3 pods stay idle for that topic/group
```

Solution:

```text
increase partitions if ordering/business design allows
or reduce replicas
```

---

## 9. 50 Interview Questions and Answers

### Basic Level

#### 1. What is Apache Kafka?

Kafka is a distributed event-streaming platform used for publishing, storing, and consuming events in real time. It is commonly used in microservices for asynchronous communication, event-driven architecture, logging, analytics, and data pipelines.

---

#### 2. What is a Kafka topic?

A topic is a logical channel where producers publish messages and consumers read messages. Example: `post-events`, `user-events`, `payment-events`.

---

#### 3. What is a Kafka partition?

A partition is a sub-division of a topic. It provides parallelism, scalability, and ordering within that partition.

---

#### 4. What is an offset?

Offset is the unique position of a message inside a partition. Consumers use offsets to track how much data they have processed.

---

#### 5. What is a Kafka broker?

A broker is a Kafka server responsible for storing topic partitions and handling producer/consumer requests.

---

#### 6. What is a Kafka cluster?

A Kafka cluster is a group of brokers working together to provide scalability and fault tolerance.

---

#### 7. What is a Kafka producer?

A producer is an application that sends messages/events to Kafka topics.

---

#### 8. What is a Kafka consumer?

A consumer is an application that reads messages/events from Kafka topics.

---

#### 9. What is a consumer group?

A consumer group is a group of consumers that share the work of consuming topic partitions. Each partition is assigned to only one consumer in the same group.

---

#### 10. Why do we use Kafka in microservices?

Kafka decouples services. One service publishes an event, and multiple services consume it independently. This improves scalability, fault isolation, and asynchronous processing.

---

### Intermediate Level

#### 11. What is the difference between Kafka and REST?

REST is synchronous request-response communication. Kafka is asynchronous event-driven communication. REST is good for immediate responses, while Kafka is good for background processing, notifications, audit logs, and integration between services.

---

#### 12. What is the difference between Kafka and RabbitMQ?

Kafka is an event-streaming platform with durable logs and replay capability. RabbitMQ is a traditional message broker focused on queue-based message delivery and routing.

---

#### 13. How does Kafka maintain ordering?

Kafka maintains ordering only within a partition. If events with the same key go to the same partition, their order is preserved.

---

#### 14. What is the role of message key?

The key is used to decide the partition. Same key generally goes to the same partition, which helps maintain order for related events.

---

#### 15. What is rebalancing?

Rebalancing is the process of reassigning partitions among consumers when consumers join, leave, crash, or when partitions change.

---

#### 16. What is consumer lag?

Consumer lag is the difference between the latest offset in Kafka and the offset processed by a consumer. High lag means the consumer is falling behind.

---

#### 17. What is `auto.offset.reset`?

It decides what a consumer should do when there is no committed offset. Common values are `earliest` and `latest`.

---

#### 18. What is `enable.auto.commit`?

It controls whether Kafka automatically commits offsets periodically. In production, many teams disable auto commit and commit only after successful processing.

---

#### 19. What is manual acknowledgement?

Manual acknowledgement means the consumer explicitly commits the offset after successful processing. This gives better control and avoids committing failed messages.

---

#### 20. What is a Dead Letter Topic?

A Dead Letter Topic stores messages that failed even after retries. It prevents poison messages from blocking the main consumer flow.

---

### Experienced Level

#### 21. Explain at-most-once, at-least-once, and exactly-once.

At-most-once means messages may be lost but not duplicated. At-least-once means messages are not lost but may be duplicated. Exactly-once means each message is processed once in supported transactional scenarios. In many microservices, we use at-least-once with idempotent consumers.

---

#### 22. How do you avoid duplicate processing?

Use idempotency. Add an `eventId` to each event and store processed event IDs in the database. If the same event is received again, ignore it.

---

#### 23. What is idempotent producer?

An idempotent producer prevents duplicate records caused by producer retries. It is enabled using `enable.idempotence=true`.

---

#### 24. What is idempotent consumer?

An idempotent consumer safely handles the same message multiple times without duplicating business actions.

Example:

```text
do not send same email twice
do not create duplicate audit record
do not update balance twice
```

---

#### 25. What is the transactional outbox pattern?

Transactional outbox pattern solves DB-Kafka consistency. Business data and event data are saved in the same DB transaction. A separate publisher reads the outbox table and publishes events to Kafka.

---

#### 26. Why should we not always publish Kafka event directly after DB save?

Because DB save may succeed and Kafka publish may fail. This creates inconsistency. For critical events, transactional outbox is safer.

---

#### 27. What is ISR?

ISR means in-sync replicas. These are replicas that are fully caught up with the leader. Kafka uses ISR to provide durability.

---

#### 28. What is replication factor?

Replication factor defines how many copies of a partition exist across brokers. In production, replication factor is commonly 3.

---

#### 29. What is `acks=all`?

`acks=all` means the producer waits until all in-sync replicas acknowledge the message. It provides stronger durability.

---

#### 30. How do retries work in Kafka consumer?

If processing fails, the consumer can retry the same message. In Spring Kafka, retries can be configured using `DefaultErrorHandler` or `@RetryableTopic`. After retries are exhausted, the message can go to DLT.

---

### Project-Based Questions

#### 31. How would you use Kafka in your blog application?

I would publish events like `PostCreatedEvent`, `UserRegisteredEvent`, and `CommentAddedEvent`. Notification-service, search-service, and audit-service can consume these events independently.

---

#### 32. In your blog app, when a post is created, what event can you publish?

I can publish `PostCreatedEvent` containing `eventId`, `postId`, `userId`, `title`, and `createdAt`.

---

#### 33. Why use Kafka instead of directly calling notification-service?

Direct REST call tightly couples post-service with notification-service. If notification-service is slow or down, post creation may be impacted. Kafka decouples them and allows notification processing asynchronously.

---

#### 34. How would search indexing work with Kafka?

When a post is created or updated, post-service publishes an event. Search-service consumes the event and updates Elasticsearch/search index.

---

#### 35. How would audit logging work with Kafka?

Important events are published to Kafka. Audit-service consumes those events and stores audit logs in DB or S3.

---

#### 36. How would you secure Kafka in production?

Use SSL/TLS for encryption, SASL for authentication, and ACLs for authorization. Each service should only have access to required topics.

---

#### 37. How would you monitor Kafka in production?

Monitor consumer lag, DLT count, retry count, broker health, under-replicated partitions, producer error rate, and consumer processing time using Prometheus/Grafana.

---

#### 38. How would you test Kafka integration?

Use `spring-kafka-test`, EmbeddedKafka, or Testcontainers. For serious integration tests, Testcontainers is closer to real production behavior.

---

#### 39. How would Kafka fit with Docker and Kubernetes?

Kafka can run locally using Docker Compose. In Kubernetes, microservices run as deployments, consumers can be scaled as pods, and Kafka can run as StatefulSet or managed service.

---

#### 40. How many consumers should you run for a topic with 3 partitions?

Maximum 3 consumers in the same consumer group can actively consume. Extra consumers remain idle for that topic/group.

---

### Scenario-Based Questions

#### 41. Consumer processed message but crashed before committing offset. What happens?

Kafka will redeliver the message after restart/rebalance. This gives at-least-once behavior. The consumer must be idempotent to avoid duplicate business processing.

---

#### 42. Consumer lag is continuously increasing. What will you check?

I will check consumer processing time, DB latency, external API latency, number of partitions, number of consumer instances, `max.poll.records`, error logs, and rebalance frequency.

---

#### 43. A message keeps failing again and again. What will you do?

Use limited retries and then send the message to DLT. Alert the team, inspect the payload, fix the issue, and reprocess later if required.

---

#### 44. Messages are consumed out of order. Why?

Kafka guarantees ordering only within a partition. If related messages are going to different partitions, ordering is not guaranteed. Use a proper key like `postId` or `userId`.

---

#### 45. Producer is getting timeout errors. What will you check?

Check Kafka broker availability, network connectivity, topic existence, authentication/ACLs, `acks`, retries, batch size, request timeout, and broker logs.

---

#### 46. Consumer is not receiving messages. What will you check?

Check topic name, group ID, committed offset, `auto.offset.reset`, deserializer configuration, consumer logs, ACLs, and whether data is actually being produced.

---

#### 47. Why is DLT important?

DLT prevents one bad message from blocking the entire consumer. It gives a safe place to store failed messages for analysis and reprocessing.

---

#### 48. What happens during consumer rebalance?

Kafka pauses partition ownership and reassigns partitions among active consumers. During rebalance, message processing can be temporarily impacted.

---

#### 49. How do you handle schema changes in Kafka events?

Use backward-compatible changes, avoid removing required fields suddenly, use default values, version events if needed, and use Schema Registry for Avro/Protobuf/JSON schema.

---

#### 50. What is your production-ready Kafka checklist?

My checklist includes topic naming, partitions, replication factor, idempotent producer, retries, DLT, idempotent consumer, manual offset strategy, monitoring, security, alerts, schema compatibility, and load testing.

---

## 10. Common Mistakes

| Mistake                                 | Why it is bad                                  | Better approach                              |
| --------------------------------------- | ---------------------------------------------- | -------------------------------------------- |
| Using Kafka for every API call          | Adds unnecessary complexity                    | Use REST for request-response                |
| Ignoring idempotency                    | Duplicate processing possible                  | Store eventId / unique business key          |
| Assuming global ordering                | Kafka orders only per partition                | Use correct message key                      |
| Too many partitions without planning    | Operational overhead                           | Start based on throughput need               |
| No DLT                                  | Poison message blocks flow                     | Retry + DLT                                  |
| Auto-commit blindly enabled             | Offset may commit before successful processing | Manual or controlled commit                  |
| Publishing after DB save without outbox | Event loss possible                            | Use transactional outbox                     |
| No monitoring                           | Production issues hidden                       | Monitor lag, DLT, errors                     |
| Ignoring schema evolution               | Consumer breaks after producer change          | Use compatible event contracts               |
| Putting huge payloads in Kafka          | Performance issue                              | Store file/object externally, send reference |

---

## 11. Best Practices

### Design best practices

```text
Use events for business facts, not commands everywhere
Keep event names meaningful
Use eventId for idempotency
Use correlationId for tracing
Use version field for schema evolution
Do not put sensitive data unnecessarily
```

Example event:

```json
{
  "eventId": "EVT-101",
  "eventType": "POST_CREATED",
  "eventVersion": "1.0",
  "correlationId": "REQ-999",
  "postId": 501,
  "userId": 10
}
```

---

### Producer best practices

```properties
acks=all
enable.idempotence=true
retries=3
compression.type=snappy
```

Use callbacks and logging:

```java
kafkaTemplate.send(topic, key, event)
        .whenComplete((result, ex) -> {
            if (ex != null) {
                log.error("Kafka publish failed", ex);
            }
        });
```

---

### Consumer best practices

```text
Process first, then commit offset
Make consumer idempotent
Use retries only for transient errors
Send poison messages to DLT
Avoid long blocking operations in listener
Monitor processing time
```

---

### Topic best practices

```text
clear topic names
proper partitions
replication factor 3 in production
retention based on business need
DLT per main topic
```

Example naming:

```text
blog.post.events.v1
blog.post.events.v1.dlt
blog.user.events.v1
```

---

### Security best practices

```text
SSL/TLS for encryption
SASL for authentication
ACLs for topic-level access
No secrets in code
Use Kubernetes secrets/AWS Secrets Manager
```

---

### Observability best practices

```text
Log topic, partition, offset
Add eventId and correlationId
Monitor consumer lag
Alert on DLT growth
Track retry count
Use distributed tracing
```

---

## 12. How to Explain in Interview

### Answer 1: General Kafka experience

> As per my project understanding, Kafka is useful in microservices when we want asynchronous and decoupled communication. Instead of one service directly calling multiple downstream services, it publishes an event to Kafka. Other services consume that event independently using their own consumer groups.

---

### Answer 2: Blog project example

> In my Spring Boot blog application, when a post is created, post-service can publish a PostCreatedEvent to Kafka. Notification-service can consume it to notify users, search-service can consume it to update the search index, and audit-service can consume it for audit logging. This makes post-service independent of downstream services.

---

### Answer 3: Kafka vs REST

> In our application, I would use REST when the client needs an immediate response, such as login or fetching post details. I would use Kafka for background event-driven tasks like sending notifications, audit logging, search indexing, and analytics.

---

### Answer 4: Reliability

> In production, we usually configure Kafka producers with `acks=all`, retries, and idempotence. On the consumer side, we handle failures using retries and DLT. We also make consumers idempotent because Kafka commonly works with at-least-once delivery.

---

### Answer 5: Duplicate messages

> Kafka can redeliver messages if a consumer processes a message but crashes before committing the offset. To handle this, I design consumers to be idempotent by storing eventId or using unique business keys.

---

### Answer 6: Ordering

> Kafka guarantees ordering only within a partition. So, if I need all events of a post or user to be processed in order, I use postId or userId as the Kafka key so those events go to the same partition.

---

### Answer 7: DLT

> In production, not every failed message should be retried forever. For transient failures, we retry. For poison messages, after retry exhaustion, we move the message to a Dead Letter Topic so the main consumer flow does not get blocked.

---

### Answer 8: Outbox pattern

> For critical business events, I prefer the transactional outbox pattern. I save the business data and event record in the same database transaction. A separate publisher sends the event to Kafka. This avoids the problem where DB save succeeds but Kafka publish fails.

---

### Answer 9: Consumer lag

> If consumer lag increases, I check whether the consumer processing is slow, whether DB or external APIs are slow, whether partitions are enough, whether consumers are scaled properly, and whether frequent rebalancing is happening.

---

### Answer 10: Senior-level summary

> In microservices architecture, Kafka helps with event-driven communication, scalability, and fault isolation. But to use Kafka properly in production, we need correct partitioning, idempotent processing, retry/DLT handling, monitoring, security, and schema compatibility.

---

## 13. Revision Notes

### Kafka one-line definition

```text
Kafka is a distributed event-streaming platform used for asynchronous, scalable, and reliable communication between services.
```

### Most important concepts

```text
Topic
Partition
Offset
Producer
Consumer
Consumer group
Broker
Replication
ISR
Acks
Retry
DLT
Idempotency
Consumer lag
Rebalancing
Schema evolution
Outbox pattern
```

### Kafka ordering rule

```text
Ordering is guaranteed only within a partition.
```

### Consumer group rule

```text
One partition is consumed by only one consumer in the same consumer group at a time.
```

### Scaling rule

```text
Consumer parallelism depends on number of partitions.
```

### Production reliability formula

```text
acks=all
replication.factor=3
min.insync.replicas=2
enable.idempotence=true
retry + DLT
idempotent consumer
monitor consumer lag
```

### Kafka vs REST

```text
REST = synchronous request-response
Kafka = asynchronous event-driven communication
```

### Practical delivery guarantee

```text
Most microservices use at-least-once delivery + idempotent consumer.
```

### Your project example

```text
PostCreatedEvent → notification-service
PostCreatedEvent → search-service
PostCreatedEvent → audit-service
```

### Best interview closing line

> Kafka is powerful, but in production we must design carefully around ordering, duplicate handling, retries, DLT, offset commits, schema compatibility, and monitoring. That is what makes Kafka reliable in real microservices systems.

[1]: https://kafka.apache.org/43/design/design/ "Design | Apache Kafka"
[2]: https://docs.spring.io/spring-kafka/reference/index.html "Overview :: Spring Kafka"
[3]: https://kafka.apache.org/43/configuration/consumer-configs/ "Consumer and Share Consumer Configs | Apache Kafka"
[4]: https://kafka.apache.org/43/configuration/producer-configs/ "Producer Configs | Apache Kafka"
[5]: https://docs.spring.io/spring-kafka/reference/kafka/annotation-error-handling.html "Handling Exceptions :: Spring Kafka"
[6]: https://docs.spring.io/spring-kafka/reference/retrytopic/retry-config.html "Configuration :: Spring Kafka"
[7]: https://kafka.apache.org/43/security/ "Security | Apache Kafka"
[8]: https://docs.spring.io/spring-kafka/reference/kafka/sending-messages.html?utm_source=chatgpt.com "Sending Messages :: Spring Kafka"
[9]: https://docs.spring.io/spring-kafka/reference/kafka/receiving-messages/listener-annotation.html?utm_source=chatgpt.com "KafkaListener Annotation"
[10]: https://docs.spring.io/spring-boot/api/java/org/springframework/boot/kafka/autoconfigure/KafkaProperties.html "KafkaProperties (Spring Boot 4.1.0 API)"
[11]: https://docs.spring.io/spring-kafka/reference/kafka/receiving-messages/message-listener-container.html?utm_source=chatgpt.com "Message Listener Containers :: Spring Kafka"
