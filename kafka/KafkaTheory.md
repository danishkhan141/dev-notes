# Kafka Interview Preparation Guide for Java Backend / Full Stack Java Developer

Kafka is one of the most important technologies for **microservices, event-driven architecture, async processing, high-throughput messaging, audit/event logs, real-time pipelines, and scalable backend systems**. For your profile, Kafka should not be learned as a separate tool only. You should connect it with **Spring Boot + REST APIs + Microservices + Docker/Kubernetes + AWS + Jenkins + database transactions**.

Apache Kafka is commonly used for messaging, website activity tracking, metrics, log aggregation, stream processing, event sourcing, and commit-log style distributed systems. The official Kafka documentation also highlights its throughput, partitioning, replication, and fault tolerance as key reasons it is used at large scale. ([Apache Kafka][1])

---

# 1. Complete Learning Roadmap

## A. Beginner Level

Start here first.

### Learn these topics first

| Topic              | Why it matters                                                                  |
| ------------------ | ------------------------------------------------------------------------------- |
| What is Kafka?     | Understand why it is used instead of direct REST calls or only database tables. |
| Producer           | Spring Boot service that sends events/messages.                                 |
| Consumer           | Spring Boot service that reads and processes events/messages.                   |
| Topic              | Logical channel/category of messages.                                           |
| Partition          | Kafka’s way of scaling and maintaining order within a topic.                    |
| Offset             | Message position inside a partition.                                            |
| Broker             | Kafka server that stores topic partitions.                                      |
| Consumer Group     | Multiple consumers working together for parallel processing.                    |
| Basic CLI commands | Create topic, produce message, consume message.                                 |

### Beginner example

In your Blog Application:

When a user creates a post, your REST API saves the post in MySQL. After that, you publish an event:

```text
post-created-event
```

Then another service can consume this event and perform actions like:

```text
send notification
update search index
update analytics
send email
generate audit log
```

Earlier:

```text
Post API → Save DB → Send Email → Update Analytics → Return Response
```

With Kafka:

```text
Post API → Save DB → Publish Event → Return Response

Other services consume event asynchronously.
```

This improves performance and decouples services.

---

## B. Intermediate Level

After basics, learn:

| Topic                         | Purpose                                    |
| ----------------------------- | ------------------------------------------ |
| Partitions and keys           | Ordering and parallelism.                  |
| Replication factor            | High availability.                         |
| Leader and follower replicas  | Kafka internal fault tolerance.            |
| Consumer group rebalancing    | How partitions move between consumers.     |
| Offset commit                 | How Kafka tracks processed messages.       |
| Delivery semantics            | At-most-once, at-least-once, exactly-once. |
| Retry and Dead Letter Topic   | Failure handling.                          |
| Serialization/deserialization | JSON, Avro, String, custom DTOs.           |
| Spring Kafka                  | `KafkaTemplate`, `@KafkaListener`.         |

Kafka topics are divided into ordered partitions, and within a consumer group, each partition is consumed by only one consumer at a time. Kafka tracks consumer progress using offsets, which makes acknowledgement tracking lightweight and also allows consumers to replay old messages if needed. ([Apache Kafka][2])

---

## C. Experienced Level

For 7-year experience, interviewers expect these:

| Topic                  | Why it matters                                |
| ---------------------- | --------------------------------------------- |
| Idempotent producer    | Avoid duplicate writes from producer retries. |
| Consumer idempotency   | Avoid duplicate DB updates.                   |
| Transactions           | Kafka + DB consistency discussion.            |
| Outbox Pattern         | Reliable DB + Kafka publishing.               |
| Schema Registry        | Contract management between services.         |
| Consumer lag           | Monitoring and performance debugging.         |
| Back pressure          | Consumers slower than producers.              |
| Message ordering       | Ordering guarantee only within partition.     |
| Exactly-once semantics | Advanced Kafka processing guarantee.          |
| Security               | SSL, SASL, ACLs.                              |
| KRaft vs ZooKeeper     | Modern Kafka architecture.                    |

In modern Kafka, KRaft mode removes ZooKeeper dependency and integrates metadata management into Kafka itself; brokers handle data requests while controllers manage metadata using the Raft protocol. ([Apache Kafka][3])

---

## D. Project Level

You should implement Kafka in your microservices version like this:

```text
API Gateway
   ↓
post-service
   ↓ publishes
Kafka Topic: post-created-events
   ↓ consumed by
notification-service
analytics-service
audit-service
search-service
```

Project-level tasks:

| Task                                       | What you learn          |
| ------------------------------------------ | ----------------------- |
| Add Kafka producer in post-service         | Event publishing        |
| Add Kafka consumer in notification-service | Async processing        |
| Add retry + DLT                            | Failure handling        |
| Add event DTO                              | Contract design         |
| Add Docker Compose                         | Local Kafka setup       |
| Add monitoring                             | Consumer lag and health |
| Add Jenkins pipeline                       | CI/CD                   |
| Deploy on AWS                              | Production-like setup   |

---

## E. Interview Preparation Level

Prepare answers for:

1. Why Kafka?
2. Kafka vs REST.
3. Kafka vs RabbitMQ.
4. Topic vs partition.
5. Consumer group.
6. Offset commit.
7. Message ordering.
8. Retry and DLT.
9. Duplicate messages.
10. Consumer lag.
11. Kafka in microservices.
12. Kafka production issues.
13. Kafka security.
14. Kafka deployment.
15. Kafka with Spring Boot.

---

## What to learn first, later, and skip initially

### Learn first

```text
Producer → Consumer → Topic → Partition → Offset → Consumer Group → Spring Boot Integration
```

### Learn later

```text
Transactions → Exactly-once → Kafka Streams → Schema Registry → Kafka Connect → KRaft details
```

### Skip initially

Do not start with:

```text
Kafka Streams internals
Kafka Connect connectors
Cluster tuning
Broker-level storage internals
Complex exactly-once transactions
MirrorMaker
Tiered storage
```

These are useful later, not on day one.

---

# 2. Why Kafka Is Important

## For Java Backend Developer

Kafka helps Java backend developers build async, scalable systems.

Example:

```text
User registers → publish user-registered-event → email service sends welcome email
```

Your REST API does not wait for email service.

---

## For Senior Java Developer

A senior developer is expected to design systems, not only write APIs. Kafka helps in:

```text
decoupling services
handling high traffic
building event-driven systems
processing background tasks
improving fault tolerance
reducing direct service dependency
```

---

## For Microservices Developer

Kafka is extremely important in microservices because direct REST-to-REST communication creates tight coupling.

Without Kafka:

```text
order-service → payment-service → invoice-service → notification-service
```

If notification-service is down, the flow may break.

With Kafka:

```text
order-service → Kafka → payment-service
                    → invoice-service
                    → notification-service
```

Each service works independently.

---

## For Java Full Stack Developer

Even if you work with React frontend, Kafka is mostly backend-side. But you can explain flows like:

```text
React creates post
   ↓
Spring Boot REST API
   ↓
MySQL save
   ↓
Kafka event
   ↓
Notification/search/analytics service
```

React does not directly call Kafka. React calls REST APIs. Backend publishes Kafka events.

---

## For Cloud / DevOps Backend Roles

Kafka is used with:

```text
Docker
Kubernetes
AWS MSK / EC2 / ECS / EKS
Prometheus
Grafana
Jenkins
CI/CD
Log monitoring
Consumer lag monitoring
```

In production, Kafka is not only coding. You must understand deployment, monitoring, retries, scaling, partitions, and failure handling.

---

# 3. Basic Concepts From Scratch

## 3.1 What is Kafka?

Kafka is a distributed event streaming platform.

Simple meaning:

```text
Kafka is a system where one application sends messages/events,
and other applications consume those messages asynchronously.
```

Real-life example:

Imagine a railway announcement system.

```text
Announcement system = Kafka
Announcement = message/event
Platform display = consumer
Mobile app = consumer
Control room = producer
```

One announcement is published once, but many systems can use it.

---

## 3.2 Message / Event

A Kafka message is a piece of data.

Example:

```json
{
  "postId": 101,
  "title": "Kafka in Spring Boot",
  "authorId": 5,
  "eventType": "POST_CREATED",
  "createdAt": "2026-07-04T10:30:00"
}
```

In interviews, say:

> In Kafka, we usually publish business events. For example, in my blog application, when a post is created, I can publish a `PostCreatedEvent` so that other services like notification, analytics, and search can process it asynchronously.

---

## 3.3 Producer

Producer sends messages to Kafka.

Example:

```text
post-service → Kafka topic
```

In Spring Boot, we use:

```java
KafkaTemplate
```

Spring Kafka provides `KafkaTemplate` as a wrapper around the Kafka producer and exposes convenient send methods for sending records to topics. ([Home][4])

---

## 3.4 Consumer

Consumer reads messages from Kafka.

Example:

```text
notification-service ← Kafka topic
```

In Spring Boot, we use:

```java
@KafkaListener
```

---

## 3.5 Topic

A topic is a logical category.

Example topics:

```text
post-created-events
user-registered-events
comment-created-events
email-notification-events
payment-completed-events
```

Think of topic like a table name or channel name.

---

## 3.6 Partition

A topic is divided into partitions.

Example:

```text
Topic: post-created-events

Partition 0
Partition 1
Partition 2
```

Partitions provide:

```text
parallel processing
scalability
ordering within partition
high throughput
```

Important interview point:

> Kafka guarantees order only within a partition, not across the whole topic.

---

## 3.7 Offset

Offset is the position of a message inside a partition.

Example:

```text
Partition 0:
Offset 0 → message A
Offset 1 → message B
Offset 2 → message C
```

Kafka uses offsets to know what a consumer has already processed.

---

## 3.8 Broker

Broker is a Kafka server.

A Kafka cluster can have multiple brokers:

```text
Broker 1
Broker 2
Broker 3
```

Topics and partitions are distributed across brokers.

---

## 3.9 Consumer Group

A consumer group is a group of consumers working together.

Example:

```text
Topic has 3 partitions.

Consumer Group: notification-group

Consumer 1 → Partition 0
Consumer 2 → Partition 1
Consumer 3 → Partition 2
```

If you add a fourth consumer but only three partitions exist, one consumer will remain idle.

---

## 3.10 Replication

Replication means Kafka keeps copies of partition data on multiple brokers.

Example:

```text
Partition 0 leader → Broker 1
Partition 0 replica → Broker 2
Partition 0 replica → Broker 3
```

If Broker 1 fails, another broker can become leader.

---

# 4. Core Concepts Required for Interviews

## 4.1 Topic

### What it is

A topic is a named stream of records.

### Why used

To separate event types.

### Real project use

```text
post-created-events
user-created-events
comment-created-events
```

### Interview answer

> A Kafka topic is a logical channel where producers publish messages and consumers subscribe to messages. In my project, I can create a topic like `post-created-events` so that whenever a post is created, other services can consume that event asynchronously.

---

## 4.2 Partition

### What it is

A topic is split into partitions.

### Why used

For scalability and parallelism.

### Internal working

Each message is written to one partition. If a key is provided, Kafka uses the key to decide the partition.

### Real project example

Use `postId` or `userId` as key.

```text
Key = userId
```

This ensures all events for the same user go to the same partition, preserving order for that user.

### Interview answer

> Partitioning allows Kafka to scale horizontally. Multiple consumers in the same consumer group can process different partitions in parallel. Ordering is guaranteed only within a partition.

---

## 4.3 Producer

### What it is

Application that sends messages to Kafka.

### Why used

To publish events.

### Internal working

Producer sends record to a topic. Kafka decides partition based on key or partition strategy.

### Project usage

```text
post-service publishes PostCreatedEvent
```

### Interview answer

> In Spring Boot, I use `KafkaTemplate` to publish event DTOs to Kafka topics after a successful business operation.

---

## 4.4 Consumer

### What it is

Application that reads messages from Kafka.

### Why used

To process events asynchronously.

### Internal working

Consumer polls Kafka for records and commits offsets after processing.

### Project usage

```text
notification-service consumes PostCreatedEvent
```

### Interview answer

> In Spring Boot, I use `@KafkaListener` to consume messages from a topic. After consuming, the service performs business logic like sending notification, updating analytics, or writing audit logs.

---

## 4.5 Consumer Group

### What it is

A group of consumers sharing topic partitions.

### Why used

To scale processing.

### Internal working

Kafka assigns partitions among consumers in the same group.

### Project usage

```text
notification-service replicas in Kubernetes
```

If you run 3 pods, each pod can consume from different partitions.

### Interview answer

> Consumer group allows horizontal scaling. If I deploy multiple instances of a service, Kafka distributes partitions among those instances.

---

## 4.6 Offset

### What it is

Message position in a partition.

### Why used

To track progress.

### Internal working

Consumer commits offset after processing.

### Project usage

If consumer restarts, it resumes from last committed offset.

### Interview answer

> Offset is like a bookmark. Kafka stores which message a consumer group has processed, so after restart the consumer can continue from the last committed offset.

---

## 4.7 Message Key

### What it is

A key attached to a message.

### Why used

To control partitioning and ordering.

### Example

```java
kafkaTemplate.send("post-created-events", postId.toString(), event);
```

### Interview answer

> If ordering is important for a particular entity, I use the entity ID as the message key so all related events go to the same partition.

---

## 4.8 Serialization and Deserialization

### What it is

Serialization converts Java object to bytes. Deserialization converts bytes back to Java object.

### Why used

Kafka stores messages as bytes.

### Common formats

```text
String
JSON
Avro
Protobuf
```

### Project usage

For your project, start with JSON.

### Interview answer

> In Spring Boot, we usually serialize event DTOs as JSON using JsonSerializer and deserialize them using JsonDeserializer. In larger production systems, Avro or Protobuf with Schema Registry is used for schema evolution.

---

## 4.9 Delivery Semantics

### At-most-once

Message may be lost but not repeated.

### At-least-once

Message will not be lost but may be repeated.

### Exactly-once

Message is processed exactly once in certain Kafka-supported scenarios.

Kafka supports exactly-once delivery in Kafka Streams and uses transactional producers and consumers for exactly-once processing between Kafka topics. ([Confluent Documentation][5])

### Interview answer

> Most Spring Boot business applications practically use at-least-once processing with idempotent consumers. Exactly-once is advanced and mainly applies to Kafka-to-Kafka processing with transactions.

---

## 4.10 Retry

### What it is

Retry means processing a failed message again.

### Why used

Temporary failures can happen.

Example:

```text
email server down
database connection timeout
external API failure
```

Spring Kafka supports retry configuration and allows exception-based retry control using mechanisms like retry topics and error handlers. ([Home][6])

---

## 4.11 Dead Letter Topic

### What it is

A topic where failed messages are sent after retry attempts are exhausted.

Example:

```text
post-created-events.DLT
```

### Why used

To avoid blocking the main consumer.

### Project example

If notification-service fails to process a bad message, send it to:

```text
post-created-events-dlt
```

Spring Kafka supports Dead Letter Topic handling with `DefaultErrorHandler` and `DeadLetterPublishingRecoverer`. ([Home][7])

---

## 4.12 Consumer Lag

### What it is

Difference between latest message offset and consumer processed offset.

### Why important

It tells whether consumers are falling behind.

Example:

```text
Kafka has offset 10000
Consumer processed offset 8000
Lag = 2000
```

### Interview answer

> Consumer lag means consumers are slower than producers. I would check processing time, DB slowness, partition count, consumer replicas, batch size, and error retries.

---

## 4.13 Rebalancing

### What it is

Kafka reassigns partitions when consumers join or leave a consumer group.

### Why used

To distribute load.

### Problem

During rebalance, consumers may pause briefly.

### Interview answer

> Rebalancing happens when a consumer joins, leaves, crashes, or partitions change. In production, frequent rebalancing can cause processing delays, so we tune heartbeat, session timeout, and avoid unstable consumers.

---

## 4.14 Retention

### What it is

How long Kafka keeps messages.

Example:

```text
7 days
24 hours
30 days
```

### Important point

Kafka does not delete messages immediately after consumption. It deletes based on retention policy.

---

## 4.15 Log Compaction

### What it is

Kafka keeps latest value for each key.

Example:

```text
userId=1, name=Danish
userId=1, name=Danish Khan
```

Kafka can keep latest value for `userId=1`.

### Used for

```text
latest user profile
configuration state
cache state
event sourcing snapshots
```

---

# 5. Architecture and Internal Working

## Kafka High-Level Architecture

```text
                +----------------------+
                |    React Frontend    |
                +----------+-----------+
                           |
                           | REST API
                           ↓
                +----------------------+
                |  Spring Boot Service |
                |    post-service      |
                +----------+-----------+
                           |
                           | Kafka Producer
                           ↓
                +----------------------+
                |     Kafka Cluster    |
                | Broker 1,2,3         |
                | Topics, Partitions   |
                +----------+-----------+
                           |
             +-------------+-------------+
             |                           |
             ↓                           ↓
+------------------------+   +------------------------+
| notification-service   |   | analytics-service      |
| Kafka Consumer         |   | Kafka Consumer         |
+------------------------+   +------------------------+
```

---

## Internal Flow

```text
1. User creates post from React.
2. React calls Spring Boot REST API.
3. post-service validates JWT.
4. post-service saves post in MySQL using JPA.
5. post-service publishes PostCreatedEvent to Kafka.
6. Kafka stores event in topic partition.
7. notification-service consumes event.
8. analytics-service consumes event.
9. search-service consumes event.
10. Each consumer commits offset after successful processing.
```

---

## Producer Flow

```text
Spring Boot Service
   ↓
KafkaTemplate.send()
   ↓
Serializer converts DTO to JSON/bytes
   ↓
Producer chooses partition
   ↓
Kafka broker receives message
   ↓
Leader partition writes message
   ↓
Followers replicate message
   ↓
Producer receives acknowledgment
```

---

## Consumer Flow

```text
Kafka Topic Partition
   ↓
Consumer polls records
   ↓
Deserializer converts JSON to DTO
   ↓
Business logic executes
   ↓
DB/API/file/email processing
   ↓
Offset commit
```

---

## Topic, Partition, Consumer Group Diagram

```text
Topic: post-created-events

Partition 0: M1 M4 M7
Partition 1: M2 M5 M8
Partition 2: M3 M6 M9

Consumer Group: notification-group

Consumer Pod 1 → Partition 0
Consumer Pod 2 → Partition 1
Consumer Pod 3 → Partition 2
```

If you run only one consumer:

```text
Consumer 1 → Partition 0, 1, 2
```

If you run five consumers for three partitions:

```text
Consumer 1 → Partition 0
Consumer 2 → Partition 1
Consumer 3 → Partition 2
Consumer 4 → idle
Consumer 5 → idle
```

---

# 6. Practical Project-Based Explanation

## Where Kafka fits in your Spring Boot Blog Application

Current flow:

```text
React → Spring Boot REST API → MySQL
```

Improved flow:

```text
React → Spring Boot REST API → MySQL → Kafka Event
```

Then other services consume the event.

---

## Use Case 1: Post Created Event

When user creates a post:

```text
post-service saves post
post-service publishes PostCreatedEvent
notification-service sends notification
analytics-service updates post count
audit-service stores audit log
search-service indexes post
```

---

## Use Case 2: User Registered Event

```text
user-service → user-registered-events → email-service
                                  → audit-service
                                  → analytics-service
```

---

## Use Case 3: Comment Created Event

```text
comment-service → comment-created-events → notification-service
                                      → moderation-service
                                      → analytics-service
```

---

## Use Case 4: Audit Logging

Instead of writing audit logs in every API synchronously:

```text
Any Service → audit-events → audit-service → audit_logs table
```

This keeps business APIs fast.

---

## Use Case 5: Search Index Update

When post is created or updated:

```text
post-service → post-events → search-service → Elasticsearch/OpenSearch
```

Even if search-service is temporarily down, Kafka keeps messages until it comes back.

---

## Kafka with JWT Security

JWT is used at REST API layer.

```text
React → API Gateway → post-service
```

Kafka does not directly use user JWT for normal internal service-to-service events.

But you can include user context in event:

```json
{
  "eventId": "uuid",
  "eventType": "POST_CREATED",
  "postId": 101,
  "createdBy": "danish",
  "correlationId": "req-123"
}
```

Do not put sensitive JWT token inside Kafka messages.

---

## Kafka with JPA/Hibernate and MySQL

Important problem:

```text
DB save successful but Kafka publish failed
```

or

```text
Kafka publish successful but DB save failed
```

For production, use **Outbox Pattern**.

```text
1. Save post in posts table.
2. Save event in outbox_events table in same DB transaction.
3. Separate publisher reads outbox table and publishes to Kafka.
4. Mark event as published.
```

This avoids inconsistency between DB and Kafka.

---

## Kafka with Microservices

Your planned architecture:

```text
API Gateway
   ↓
Eureka Service Discovery
   ↓
user-service
post-service
category-service
comment-service
   ↓
Kafka
   ↓
notification-service
audit-service
analytics-service
search-service
```

Kafka is not replacing REST completely.

Use REST for:

```text
request-response
immediate data fetching
login
CRUD APIs
synchronous validation
```

Use Kafka for:

```text
events
background processing
notifications
audit logs
analytics
async communication
decoupling services
```

---

## Kafka with Docker

For local development:

```text
Spring Boot services
Kafka broker
Kafka UI
MySQL
```

Run using Docker Compose.

---

## Kafka with Kubernetes

In Kubernetes:

```text
post-service pod publishes events
notification-service pods consume events
Kafka runs as managed service or StatefulSet
```

For interviews, say:

> In production, I prefer managed Kafka like AWS MSK if available. If Kafka runs inside Kubernetes, it should be deployed carefully using StatefulSets, persistent volumes, proper storage, monitoring, and replication.

---

## Kafka with Jenkins

Jenkins pipeline:

```text
Checkout code
Run unit tests
Build Spring Boot jar
Build Docker image
Push image to registry
Deploy to Kubernetes
Run smoke test
```

Kafka integration tests can use:

```text
Testcontainers Kafka
Embedded Kafka
```

---

## Kafka with AWS

Options:

```text
AWS MSK
Kafka on EC2
Kafka on EKS
Confluent Cloud
```

For your project explanation, AWS MSK or Kafka on EC2/Lightsail can be mentioned depending on project level.

---

# 7. Real Production Use Cases

## Common production use cases

| Use case                   | Example                                     |
| -------------------------- | ------------------------------------------- |
| Async notification         | Order placed → send email/SMS later         |
| Audit logging              | All important actions stored asynchronously |
| Analytics                  | Track user activity, post views, clicks     |
| Event-driven microservices | Order event consumed by payment/inventory   |
| Search indexing            | Product/post update → update search engine  |
| Log aggregation            | Services publish logs/events centrally      |
| Data pipeline              | DB changes sent to warehouse                |
| Fraud detection            | Payment events processed in real time       |
| Inventory updates          | Order event reduces stock                   |
| Saga workflow              | Distributed transaction coordination        |

Kafka is officially described as useful for messaging, activity tracking, metrics, log aggregation, stream processing, event sourcing, and commit-log use cases. ([Apache Kafka][1])

---

## What problems Kafka solves

### 1. Decoupling

Producer does not need to know consumers.

```text
post-service does not need to know notification-service
```

---

### 2. Scalability

Increase partitions and consumers.

```text
More events → more partitions → more consumers
```

---

### 3. Reliability

If consumer is down, Kafka keeps messages based on retention.

---

### 4. Performance

REST API returns quickly after publishing event.

---

### 5. Replayability

Consumers can reprocess old messages by resetting offsets.

---

## Common production failures

| Failure                  | Cause                             | Solution                                    |
| ------------------------ | --------------------------------- | ------------------------------------------- |
| Consumer lag high        | Consumer slow                     | Add partitions/consumers, optimize DB calls |
| Duplicate processing     | Retry or rebalance                | Idempotency                                 |
| Poison message           | Bad data/deserialization issue    | DLT                                         |
| Broker down              | Infra failure                     | Replication                                 |
| Message order issue      | Wrong key or multiple partitions  | Use proper key                              |
| Event lost               | Wrong producer ack config         | Use `acks=all`, retries                     |
| DB + Kafka inconsistency | Separate DB and Kafka transaction | Outbox Pattern                              |
| Frequent rebalancing     | Unstable consumer pods            | Tune heartbeat/session, fix crashes         |
| Large messages           | Bad event design                  | Store file in S3, send reference only       |
| Security gap             | Open Kafka access                 | SSL/SASL/ACL                                |

Kafka supports authorization using ACLs with a pluggable authorization framework; ACLs define which principal can perform which operation on which resource. ([Apache Kafka][8])

---

# 8. Hands-On Practice Plan

## Beginner exercises

1. Start Kafka locally using Docker Compose.
2. Create topic:

```bash
kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
```

3. Produce message using CLI.
4. Consume message using CLI.
5. Create Spring Boot producer.
6. Create Spring Boot consumer.
7. Send String message.
8. Send JSON message.

---

## Intermediate tasks

1. Create `PostCreatedEvent`.
2. Publish event after post creation.
3. Consume event in notification-service.
4. Add consumer group.
5. Run two instances of consumer.
6. Test partition assignment.
7. Add retry.
8. Add DLT.
9. Add manual offset commit.
10. Add idempotency using `eventId`.

---

## Real project-level tasks

1. Add Kafka to post-service.
2. Create notification-service.
3. Add audit-service.
4. Add Docker Compose with Kafka + MySQL.
5. Add Kafka UI.
6. Add Actuator health checks.
7. Add structured logging with correlation ID.
8. Add Jenkins pipeline.
9. Deploy services using Docker/Kubernetes.
10. Add monitoring for consumer lag.

---

## Mini project ideas

| Project                  | Kafka usage                                   |
| ------------------------ | --------------------------------------------- |
| Blog notification system | Post events consumed by notification-service  |
| Audit logging system     | All services publish audit events             |
| Order management system  | Order, payment, inventory events              |
| File processing system   | File upload event consumed by batch processor |
| Real-time analytics      | Track post views and user activity            |

---

## Strong interview project idea

### Event-Driven Blog Microservices Platform

Architecture:

```text
React Frontend
   ↓
API Gateway
   ↓
post-service
user-service
category-service
comment-service
   ↓
Kafka
   ↓
notification-service
audit-service
analytics-service
search-service
```

Events:

```text
UserRegisteredEvent
PostCreatedEvent
PostUpdatedEvent
CommentCreatedEvent
PostViewedEvent
```

Production features:

```text
Retry
DLT
Consumer lag monitoring
Idempotent consumer
Outbox Pattern
Docker Compose
Kubernetes deployment
Jenkins CI/CD
AWS deployment
```

This is a strong project for interviews.

---

# 9. Integration With Java / Spring Boot

## Maven dependency

For Spring Boot, normally use Boot dependency management and add:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

For testing:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka-test</artifactId>
    <scope>test</scope>
</dependency>
```

---

## application.yml

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092

    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      acks: all
      retries: 3
      properties:
        enable.idempotence: true

    consumer:
      group-id: notification-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
      properties:
        spring.json.trusted.packages: "com.danish.blog.events"
```

---

## Event DTO

```java
package com.danish.blog.events;

import java.time.LocalDateTime;

public class PostCreatedEvent {

    private String eventId;
    private Long postId;
    private Long userId;
    private String title;
    private String eventType;
    private LocalDateTime createdAt;

    public PostCreatedEvent() {
    }

    public PostCreatedEvent(String eventId, Long postId, Long userId,
                            String title, String eventType, LocalDateTime createdAt) {
        this.eventId = eventId;
        this.postId = postId;
        this.userId = userId;
        this.title = title;
        this.eventType = eventType;
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

    public String getEventType() {
        return eventType;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
}
```

---

## Kafka Producer Service

```java
package com.danish.blog.kafka;

import com.danish.blog.events.PostCreatedEvent;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Slf4j
@Service
@RequiredArgsConstructor
public class PostEventProducer {

    private static final String TOPIC = "post-created-events";

    private final KafkaTemplate<String, PostCreatedEvent> kafkaTemplate;

    public void publishPostCreatedEvent(PostCreatedEvent event) {
        String key = String.valueOf(event.getPostId());

        kafkaTemplate.send(TOPIC, key, event);

        log.info("PostCreatedEvent published. eventId={}, postId={}",
                event.getEventId(), event.getPostId());
    }
}
```

Interview explanation:

> I use `KafkaTemplate` to publish event DTOs. I pass `postId` as the key so that events related to the same post go to the same partition and maintain order for that post.

---

## Use Producer in Service Layer

```java
@Service
@RequiredArgsConstructor
public class PostServiceImpl implements PostService {

    private final PostRepository postRepository;
    private final PostEventProducer postEventProducer;

    @Override
    @Transactional
    public PostDto createPost(PostDto postDto, Long userId) {

        Post post = new Post();
        post.setTitle(postDto.getTitle());
        post.setContent(postDto.getContent());

        Post savedPost = postRepository.save(post);

        PostCreatedEvent event = new PostCreatedEvent(
                UUID.randomUUID().toString(),
                savedPost.getId(),
                userId,
                savedPost.getTitle(),
                "POST_CREATED",
                LocalDateTime.now()
        );

        postEventProducer.publishPostCreatedEvent(event);

        return mapToDto(savedPost);
    }
}
```

Production note:

For a real production system, improve this using the Outbox Pattern because `@Transactional` DB transaction and Kafka publish are two separate systems.

---

## Kafka Consumer

```java
package com.danish.notification.kafka;

import com.danish.blog.events.PostCreatedEvent;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Slf4j
@Service
public class PostCreatedConsumer {

    @KafkaListener(
            topics = "post-created-events",
            groupId = "notification-group"
    )
    public void consume(PostCreatedEvent event) {

        log.info("PostCreatedEvent received. eventId={}, postId={}, title={}",
                event.getEventId(), event.getPostId(), event.getTitle());

        // Business logic:
        // 1. Send notification
        // 2. Send email
        // 3. Save notification record
    }
}
```

Interview explanation:

> I use `@KafkaListener` in the consumer service. The listener receives the event, performs the business logic, and once processing succeeds, the offset is committed.

---

## REST Controller Flow

```java
@RestController
@RequestMapping("/api/posts")
@RequiredArgsConstructor
public class PostController {

    private final PostService postService;

    @PostMapping
    public ResponseEntity<PostDto> createPost(@Valid @RequestBody PostDto postDto,
                                              @RequestHeader("Authorization") String token) {

        PostDto createdPost = postService.createPost(postDto, extractUserId(token));

        return new ResponseEntity<>(createdPost, HttpStatus.CREATED);
    }
}
```

Kafka is not exposed to React. React still calls REST API.

---

## Error Handling with DLT

```java
@Configuration
public class KafkaErrorConfig {

    @Bean
    public DefaultErrorHandler errorHandler(KafkaTemplate<Object, Object> kafkaTemplate) {

        DeadLetterPublishingRecoverer recoverer =
                new DeadLetterPublishingRecoverer(kafkaTemplate,
                        (record, exception) ->
                                new TopicPartition(record.topic() + ".DLT", record.partition()));

        FixedBackOff fixedBackOff = new FixedBackOff(2000L, 3L);

        return new DefaultErrorHandler(recoverer, fixedBackOff);
    }
}
```

Explanation:

```text
Retry 3 times
If still failing, send to post-created-events.DLT
```

Spring Kafka supports retry listeners, backoff handling, and DLT publishing through error handlers. ([Home][7])

---

## Logging Best Practices

Log:

```text
eventId
correlationId
topic
partition
offset
consumer group
business entity id
```

Example:

```java
log.info("Consumed event. eventId={}, topic={}, partition={}, offset={}",
        event.getEventId(), topic, partition, offset);
```

---

## Testing

Use:

```text
spring-kafka-test
EmbeddedKafka
Testcontainers Kafka
```

Test cases:

```text
producer publishes event
consumer receives event
invalid event goes to DLT
duplicate event is ignored
consumer retry works
```

---

## Deployment considerations

For production:

```text
Use replication factor 3
Use multiple brokers
Use proper partition count
Use SSL/SASL/ACL
Monitor consumer lag
Configure retention
Configure DLT
Avoid large messages
Use idempotent consumers
Use schema versioning
```

---

# 10. Common Interview Questions and Answers

## Basic Level

### 1. What is Kafka?

Kafka is a distributed event streaming platform used to publish, store, and consume events asynchronously. In backend systems, it is commonly used for messaging, event-driven microservices, audit logs, analytics, and real-time processing.

---

### 2. Why do we use Kafka?

We use Kafka to decouple services, improve scalability, handle high-throughput events, process data asynchronously, and make systems more fault tolerant.

---

### 3. What is a Kafka topic?

A topic is a logical channel where producers send messages and consumers read messages.

Example:

```text
post-created-events
```

---

### 4. What is a producer?

A producer is an application that sends messages to Kafka.

Example:

```text
post-service publishes PostCreatedEvent
```

---

### 5. What is a consumer?

A consumer is an application that reads messages from Kafka.

Example:

```text
notification-service consumes PostCreatedEvent
```

---

### 6. What is a broker?

A broker is a Kafka server that stores topic partitions and handles producer/consumer requests.

---

### 7. What is a partition?

A partition is a subdivision of a topic. It helps Kafka scale and process messages in parallel.

---

### 8. What is an offset?

Offset is the position of a message inside a partition. Consumers use offsets to track progress.

---

### 9. What is a consumer group?

A consumer group is a group of consumers that share the processing of topic partitions.

---

### 10. Does Kafka guarantee message ordering?

Kafka guarantees ordering only within a single partition, not across the entire topic.

---

## Intermediate Level

### 11. Why are partitions important?

Partitions provide scalability, parallelism, and ordering within each partition.

---

### 12. What happens if consumers are more than partitions?

Extra consumers remain idle because one partition can be assigned to only one consumer within the same consumer group at a time.

---

### 13. What happens if partitions are more than consumers?

One consumer may process multiple partitions.

---

### 14. What is replication factor?

Replication factor means how many copies of a partition Kafka stores across brokers.

---

### 15. What is leader and follower replica?

For each partition, one broker acts as leader. Producers and consumers interact with the leader. Followers replicate data from the leader.

---

### 16. What is consumer lag?

Consumer lag is the difference between latest offset and committed offset. It shows how far the consumer is behind.

---

### 17. What is offset commit?

Offset commit means saving the position up to which a consumer has processed messages.

---

### 18. What is auto offset reset?

`auto.offset.reset` decides where a consumer starts if no committed offset exists.

Common values:

```text
earliest
latest
none
```

---

### 19. What is message key?

Message key is used to decide partition placement. Same key usually goes to the same partition.

---

### 20. Why use key in Kafka?

To preserve ordering for related events.

Example:

```text
userId as key
postId as key
orderId as key
```

---

## Experienced Level

### 21. What is at-least-once delivery?

At-least-once means Kafka ensures the message is processed, but duplicate processing may happen.

---

### 22. What is at-most-once delivery?

At-most-once means the message may be lost but will not be processed twice.

---

### 23. What is exactly-once delivery?

Exactly-once means records are processed once in supported transactional Kafka flows, especially Kafka-to-Kafka processing.

---

### 24. How do you handle duplicate messages?

Use idempotent consumer logic.

Example:

```text
Store eventId in processed_events table.
If eventId already exists, skip processing.
```

---

### 25. What is idempotency?

Idempotency means processing the same message multiple times should not change the final result incorrectly.

---

### 26. What is retry in Kafka consumer?

Retry means trying to process a failed message again after failure.

---

### 27. What is DLT?

DLT stands for Dead Letter Topic. Failed messages are sent there after retries are exhausted.

---

### 28. What is a poison pill message?

A poison pill is a bad message that always fails during processing, such as invalid JSON or missing required fields.

---

### 29. How do you handle poison messages?

Use error handling, retry, DLT, proper deserialization handling, and alerting.

---

### 30. What is rebalancing?

Rebalancing is the process where Kafka reassigns partitions when consumers join, leave, or fail.

---

## Scenario-Based Questions

### 31. Your consumer is processing duplicate messages. What will you do?

I will make the consumer idempotent. I can store `eventId` in a DB table and check before processing. I will also review offset commit timing and retry configuration.

---

### 32. Consumer lag is increasing. What will you check?

I will check:

```text
consumer processing time
DB slowness
external API delay
number of partitions
number of consumer instances
retry failures
consumer logs
broker health
network latency
```

---

### 33. Message order is not maintained. Why?

Kafka maintains order only within a partition. If related messages are going to different partitions because of missing or wrong key, ordering will not be guaranteed.

---

### 34. Kafka producer is successful but consumer is not receiving message. What will you check?

I will check:

```text
topic name
consumer group
offset position
auto.offset.reset
serialization/deserialization
consumer logs
consumer lag
ACL/security
network connectivity
```

---

### 35. Kafka message is failing again and again. What will you do?

I will configure retry with backoff and then route failed messages to DLT. I will also log eventId, topic, partition, offset, and exception details.

---

## Project-Based Questions

### 36. How did you use Kafka in your project?

> In my microservices architecture, we used Kafka for asynchronous communication. For example, when a post was created, post-service published a `PostCreatedEvent` to Kafka. Notification-service, audit-service, and analytics-service consumed that event independently. This reduced direct dependency between services and improved response time.

---

### 37. Why did you choose Kafka instead of REST?

REST is synchronous and creates direct dependency. Kafka is asynchronous and decouples services. For immediate response, we used REST. For background processing like notification, audit, and analytics, Kafka was better.

---

### 38. Where did you publish Kafka events?

After completing the main business operation, such as creating a post or registering a user, we published a business event to Kafka.

---

### 39. What events did you create?

Examples:

```text
PostCreatedEvent
PostUpdatedEvent
UserRegisteredEvent
CommentCreatedEvent
PostViewedEvent
```

---

### 40. How did you secure Kafka?

In production, Kafka can be secured using SSL/TLS for encryption, SASL for authentication, and ACLs for authorization. We also avoid putting sensitive data like JWT tokens or passwords in Kafka messages.

---

## Production-Level Questions

### 41. How do you prevent data loss in Kafka?

Use:

```text
acks=all
replication factor 3
min.insync.replicas
producer retries
idempotent producer
monitoring
DLT
```

---

### 42. How do you handle DB + Kafka consistency?

Use Outbox Pattern.

```text
Save business data and event in same DB transaction.
Publish event later from outbox table.
```

---

### 43. How do you monitor Kafka?

Monitor:

```text
consumer lag
broker health
under-replicated partitions
failed messages
DLT count
producer error rate
consumer processing time
topic throughput
```

---

### 44. How do you scale Kafka consumers?

Increase partitions and run more consumer instances in the same consumer group.

---

### 45. Can Kafka replace Spring Batch?

Not always. Kafka is good for event streaming and continuous processing. Spring Batch is better for scheduled bulk jobs, chunk processing, skip/retry, and large offline file/database processing. Sometimes both are used together.

---

### 46. Can Kafka be used for bulk data transfer?

Yes, Kafka can handle high-volume data streams. But for traditional batch jobs like nightly file processing, reconciliation, or ETL with chunk-based restartability, Spring Batch may still be better.

---

### 47. What is Kafka retention?

Retention defines how long Kafka keeps messages, regardless of whether consumers have consumed them.

---

### 48. What is log compaction?

Log compaction keeps the latest value for each key. It is useful for state-like topics.

---

### 49. How do you deploy Kafka-based apps?

Deploy producers and consumers as Spring Boot services using Docker/Kubernetes. Kafka itself can run as AWS MSK, Confluent Cloud, EC2, or Kubernetes StatefulSet.

---

### 50. What are best practices for Kafka in Spring Boot?

Use event DTOs, meaningful topic names, message keys, retries, DLT, idempotent consumers, proper logging, monitoring, schema versioning, and avoid large messages.

---

# 11. Scenario-Based Interview Preparation

## Scenario 1: How did you use Kafka in your project?

> In my project, we used Kafka for asynchronous communication between microservices. For example, when a user created a blog post, the post-service first saved the post details in MySQL using JPA/Hibernate. After successful creation, it published a `PostCreatedEvent` to Kafka. Other services like notification-service, audit-service, and analytics-service consumed this event independently. This helped us reduce tight coupling and improved API response time.

---

## Scenario 2: Why did you choose Kafka?

> We chose Kafka because our use case required asynchronous communication, high throughput, durability, and multiple consumers for the same event. REST was suitable for synchronous calls, but for background tasks like notification, analytics, and audit logging, Kafka was more scalable and reliable.

---

## Scenario 3: What issue did you face in production?

> One common issue was duplicate message processing during retries or consumer restart. We handled it by making the consumer idempotent. Each event had a unique `eventId`, and before processing, we checked whether the event was already processed. This avoided duplicate database updates or duplicate notifications.

---

## Scenario 4: How did you debug Kafka issues?

> I checked application logs using eventId and correlationId, verified topic name and consumer group, checked consumer lag, reviewed partition assignment, checked DLT messages, and validated serialization/deserialization errors. If the issue was consumer slowness, I checked DB query performance and external API latency.

---

## Scenario 5: How did you improve Kafka performance?

> We improved performance by increasing partitions, running multiple consumer instances, using proper message keys, reducing heavy processing inside consumers, optimizing DB queries, using batch consumption where required, and monitoring consumer lag continuously.

---

## Scenario 6: How did you handle failures?

> We used retry with backoff for temporary failures and DLT for permanent failures. For example, if email service was temporarily down, the consumer retried. If the message still failed after retries, it was moved to a DLT for later analysis.

---

## Scenario 7: How did you secure Kafka?

> We avoided sending sensitive data in Kafka messages. In production, Kafka can be secured with SSL/TLS, SASL authentication, and ACL-based authorization. We also restricted topic-level access so services could only produce or consume from required topics.

---

## Scenario 8: How did you deploy it?

> We containerized Spring Boot producer and consumer services using Docker. In Kubernetes, we deployed multiple replicas of consumer services to scale processing. Kafka was either managed through AWS MSK or deployed separately with persistent storage and monitoring.

---

# 12. Live Project Explanation

Use this in interviews.

## Short version

> In my project, we used Kafka for asynchronous communication between microservices. For example, when a post was created in the blog application, post-service saved the data in MySQL and then published a `PostCreatedEvent` to Kafka. Notification-service consumed this event to send notifications, audit-service consumed it to store audit logs, and analytics-service consumed it to update reporting data. This helped us decouple services, improve response time, and make the system more scalable.

---

## Detailed version

> As per my project experience, earlier our APIs were doing multiple operations synchronously. For example, after creating a post, the service had to update audit logs, trigger notification, and update analytics. This increased response time and created tight coupling between services.

> Later, in our microservices architecture, we introduced Kafka. The main service performed the core transaction, saved data in MySQL using JPA/Hibernate, and then published a business event like `PostCreatedEvent` to Kafka. Other services consumed that event independently.

> In production, this helped us improve scalability and reliability. If notification-service was temporarily down, post creation was not blocked. Kafka retained the message, and the consumer processed it after recovery. We also used retry and Dead Letter Topic for failed messages and idempotency to avoid duplicate processing.

---

## Strong project explanation

> In our microservices architecture, we used Kafka as an event backbone. REST APIs were used for synchronous user actions, while Kafka was used for asynchronous background processing. For example, when a user created a post from the React frontend, the request came through API Gateway to post-service. The service authenticated the request using JWT, validated the DTO, saved the post in MySQL using JPA/Hibernate, and published a `PostCreatedEvent` to Kafka.

> Multiple services consumed the same event. Notification-service sent notifications, audit-service stored audit logs, and analytics-service updated reporting data. We used message keys like `postId` to maintain ordering for related events. For failure handling, we configured retries with backoff and sent failed messages to DLT. We also designed consumers to be idempotent using `eventId`.

> This helped us solve the earlier problem of tight coupling and slow API responses. Kafka improved scalability, reliability, and maintainability of the system.

---

# 13. Common Mistakes and Best Practices

## Beginner mistakes

| Mistake                           | Better approach                                        |
| --------------------------------- | ------------------------------------------------------ |
| Thinking Kafka is a database      | Kafka stores event logs, but do not use it like MySQL. |
| Not understanding partitions      | Learn partitioning early.                              |
| Ignoring message key              | Use key when ordering matters.                         |
| Thinking Kafka gives global order | Ordering is only within partition.                     |
| Not using consumer group properly | Use different group IDs for independent consumers.     |

---

## Experienced developer mistakes

| Mistake                           | Better approach                                |
| --------------------------------- | ---------------------------------------------- |
| Publishing event before DB commit | Publish after commit or use Outbox Pattern.    |
| No idempotency                    | Always handle duplicate messages.              |
| No DLT                            | Failed messages can block processing.          |
| Large message payload             | Store file/data externally and send reference. |
| No schema versioning              | Use versioned events.                          |
| Wrong partition count             | Plan based on throughput and scaling.          |

---

## Production mistakes

| Mistake                     | Impact                              |
| --------------------------- | ----------------------------------- |
| Replication factor 1        | Data loss risk                      |
| No monitoring               | Lag and failure unnoticed           |
| No alert on DLT             | Failed events ignored               |
| Sensitive data in messages  | Security risk                       |
| Too many small topics       | Operational complexity              |
| Too many partitions blindly | Broker overhead                     |
| No retry strategy           | Temporary failures become permanent |
| Wrong offset commit         | Duplicate or lost processing        |

---

## Best practices

```text
Use clear topic names.
Use event DTOs.
Use eventId.
Use message key.
Use idempotent consumers.
Use retry + DLT.
Use correlationId for tracing.
Do not put sensitive data.
Do not send huge payloads.
Monitor consumer lag.
Use Outbox Pattern for DB + Kafka reliability.
Use proper retention.
Use schema versioning.
```

---

## Performance best practices

```text
Increase partitions carefully.
Scale consumers based on partitions.
Use batch processing where needed.
Optimize consumer DB queries.
Avoid slow external API calls inside consumer.
Use async processing if required.
Monitor lag.
Compress messages if needed.
Tune producer linger.ms and batch.size carefully.
```

---

## Security best practices

```text
Use SSL/TLS encryption.
Use SASL authentication.
Use ACL authorization.
Do not expose Kafka publicly.
Do not store JWT/password/card data in messages.
Mask sensitive fields.
Restrict producer/consumer topic access.
Rotate credentials.
```

---

# 14. Comparison With Related Technologies

## Kafka vs REST

| Kafka                                     | REST                                 |
| ----------------------------------------- | ------------------------------------ |
| Asynchronous                              | Synchronous                          |
| Event-driven                              | Request-response                     |
| Good for background processing            | Good for immediate response          |
| Multiple consumers can process same event | One request usually has one response |
| Durable event storage                     | No built-in message retention        |

Use REST for:

```text
login
get post by id
create post API response
fetch categories
```

Use Kafka for:

```text
notification
analytics
audit log
search indexing
background processing
```

---

## Kafka vs RabbitMQ

| Kafka                               | RabbitMQ                         |
| ----------------------------------- | -------------------------------- |
| Distributed event streaming         | Traditional message broker       |
| High throughput                     | Strong routing features          |
| Retains messages based on time/size | Usually removes after ack        |
| Replay possible                     | Replay not primary design        |
| Best for event streams              | Best for task queues and routing |

Use Kafka when:

```text
high throughput
event replay
multiple independent consumers
stream processing
event-driven architecture
```

Use RabbitMQ when:

```text
complex routing
traditional queue
short-lived tasks
request/reply messaging
```

Kafka’s own use-case documentation compares Kafka with traditional messaging systems like ActiveMQ and RabbitMQ in messaging scenarios. ([Apache Kafka][1])

---

## Kafka vs Spring Batch

| Kafka                        | Spring Batch                        |
| ---------------------------- | ----------------------------------- |
| Continuous event processing  | Scheduled/bulk batch processing     |
| Event-driven                 | Job/step/chunk driven               |
| Good for real-time pipelines | Good for large file/database jobs   |
| Offset-based processing      | Metadata table-based restartability |
| Consumer groups              | Jobs and steps                      |

Use both together:

```text
Spring Batch processes large file
   ↓
Publishes Kafka event after each chunk/job
   ↓
Other services consume updates
```

---

## Kafka vs SQS

| Kafka                              | AWS SQS                       |
| ---------------------------------- | ----------------------------- |
| Event streaming platform           | Managed queue service         |
| Topic partitions                   | Queues                        |
| Replay possible by offset          | Message retention limited     |
| More operational complexity        | Fully managed and simple      |
| Strong for high-throughput streams | Strong for simple async queue |

Use Kafka when architecture needs event streaming and multiple consumers. Use SQS when you need simple managed queueing.

---

## Kafka vs Database Polling

Without Kafka:

```text
notification-service keeps checking DB every few seconds
```

With Kafka:

```text
post-service publishes event immediately
notification-service consumes event immediately
```

Kafka avoids unnecessary polling and improves real-time behavior.

---

# 15. Revision Notes

## Key definitions

```text
Kafka:
Distributed event streaming platform.

Producer:
Application that sends messages to Kafka.

Consumer:
Application that reads messages from Kafka.

Topic:
Logical channel of messages.

Partition:
Unit of parallelism inside a topic.

Offset:
Position of a message in a partition.

Consumer Group:
Group of consumers sharing partition processing.

Broker:
Kafka server.

Replication Factor:
Number of copies of partition data.

Leader Replica:
Main replica handling reads/writes.

Follower Replica:
Replica that copies data from leader.

DLT:
Dead Letter Topic for failed messages.

Consumer Lag:
Difference between latest offset and processed offset.
```

---

## Common commands

```bash
# List topics
kafka-topics.sh --list --bootstrap-server localhost:9092

# Create topic
kafka-topics.sh --create \
  --topic post-created-events \
  --bootstrap-server localhost:9092 \
  --partitions 3 \
  --replication-factor 1

# Describe topic
kafka-topics.sh --describe \
  --topic post-created-events \
  --bootstrap-server localhost:9092

# Produce message
kafka-console-producer.sh \
  --topic post-created-events \
  --bootstrap-server localhost:9092

# Consume message
kafka-console-consumer.sh \
  --topic post-created-events \
  --from-beginning \
  --bootstrap-server localhost:9092

# Check consumer groups
kafka-consumer-groups.sh \
  --bootstrap-server localhost:9092 \
  --list

# Describe consumer group
kafka-consumer-groups.sh \
  --bootstrap-server localhost:9092 \
  --describe \
  --group notification-group
```

---

## Important Spring classes/annotations

```text
KafkaTemplate
@KafkaListener
ConcurrentKafkaListenerContainerFactory
DefaultErrorHandler
DeadLetterPublishingRecoverer
ProducerRecord
ConsumerRecord
JsonSerializer
JsonDeserializer
NewTopic
EmbeddedKafka
```

---

## Most important interview points

```text
Kafka is used for async event-driven communication.
REST and Kafka solve different problems.
Topic is logical channel.
Partition gives scalability and ordering within partition.
Consumer group gives parallel processing.
Offset tracks progress.
Kafka can replay messages.
Duplicates are possible, so use idempotency.
Use retry and DLT for failure handling.
Use Outbox Pattern for DB + Kafka consistency.
Monitor consumer lag.
Secure Kafka using SSL/SASL/ACL.
```

---

## 5-minute revision summary

> Kafka is a distributed event streaming platform used to build asynchronous and scalable microservices. Producers publish events to topics, topics are divided into partitions, and consumers read messages using consumer groups. Kafka tracks progress using offsets. Partitioning provides scalability and ordering within a partition. In Spring Boot, we use `KafkaTemplate` to publish messages and `@KafkaListener` to consume them. In production, we must handle retries, DLT, duplicate messages, consumer lag, security, and DB-Kafka consistency. For my blog microservices project, Kafka can be used for post-created, user-registered, comment-created, audit, notification, analytics, and search indexing events.

---

# 16. 30-Day Kafka Learning Plan

## Week 1: Kafka Basics

### Day 1

Theory:

```text
What is Kafka?
Why Kafka?
Kafka vs REST
Kafka vs RabbitMQ
```

Hands-on:

```text
Install/start Kafka using Docker.
```

---

### Day 2

Theory:

```text
Topic
Partition
Offset
Broker
Producer
Consumer
```

Hands-on:

```text
Create topic using CLI.
Produce and consume messages.
```

---

### Day 3

Theory:

```text
Consumer group
Partition assignment
Ordering
Message key
```

Hands-on:

```text
Create topic with 3 partitions.
Run multiple consumers.
Observe partition assignment.
```

---

### Day 4

Theory:

```text
Replication
Leader/follower
Kafka cluster basics
```

Hands-on:

```text
Describe topic.
Understand leader and replicas.
```

---

### Day 5

Theory:

```text
Offset commit
auto.offset.reset
earliest vs latest
```

Hands-on:

```text
Create new consumer group.
Test earliest/latest behavior.
```

---

### Day 6

Revision:

```text
Revise basic concepts.
Prepare 10 basic interview answers.
```

Hands-on:

```text
CLI practice again without notes.
```

---

### Day 7

Mock:

```text
Explain Kafka from scratch in 5 minutes.
Explain topic/partition/consumer group.
```

---

## Week 2: Spring Boot Kafka Integration

### Day 8

Theory:

```text
Spring Kafka
KafkaTemplate
@KafkaListener
```

Hands-on:

```text
Create Spring Boot producer.
```

---

### Day 9

Hands-on:

```text
Create Spring Boot consumer.
Send String message.
Consume String message.
```

---

### Day 10

Theory:

```text
JSON serialization/deserialization
Event DTO design
```

Hands-on:

```text
Send PostCreatedEvent as JSON.
```

---

### Day 11

Hands-on:

```text
Add Kafka event publishing in Blog post-service.
```

---

### Day 12

Hands-on:

```text
Create notification-service consumer.
Consume PostCreatedEvent.
```

---

### Day 13

Theory:

```text
Logging
correlationId
eventId
consumer group ID
```

Hands-on:

```text
Add structured logs.
```

---

### Day 14

Mock:

```text
Explain how Kafka is integrated with Spring Boot.
Prepare 10 Spring Kafka interview answers.
```

---

## Week 3: Production Concepts

### Day 15

Theory:

```text
Retry
Backoff
DLT
Poison message
```

Hands-on:

```text
Configure retry and DLT.
```

---

### Day 16

Theory:

```text
Duplicate messages
Idempotent consumer
```

Hands-on:

```text
Create processed_events table.
Skip duplicate eventId.
```

---

### Day 17

Theory:

```text
Consumer lag
Scaling consumers
Partitions
```

Hands-on:

```text
Run multiple consumer instances.
Check lag using CLI.
```

---

### Day 18

Theory:

```text
Delivery semantics
At-most-once
At-least-once
Exactly-once
```

Interview:

```text
Prepare answer: how to avoid duplicate processing?
```

---

### Day 19

Theory:

```text
DB + Kafka consistency
Outbox Pattern
```

Hands-on:

```text
Create outbox_events table.
Save event in same transaction.
```

---

### Day 20

Theory:

```text
Security
SSL
SASL
ACL
Sensitive data handling
```

Interview:

```text
Prepare Kafka security answer.
```

---

### Day 21

Mock:

```text
Production issue mock interview:
consumer lag
duplicate messages
DLT
broker failure
```

---

## Week 4: Project + Interview Preparation

### Day 22

Project:

```text
Finalize event-driven Blog architecture.
Create topics:
post-created-events
user-registered-events
comment-created-events
audit-events
```

---

### Day 23

Project:

```text
Implement audit-service consumer.
Save audit logs in MySQL.
```

---

### Day 24

Project:

```text
Implement notification-service consumer.
Add retry and DLT.
```

---

### Day 25

Project:

```text
Dockerize producer and consumer services.
Run Kafka using Docker Compose.
```

---

### Day 26

Project:

```text
Add Jenkins pipeline idea:
build
test
docker image
deploy
```

---

### Day 27

Project:

```text
Prepare Kubernetes deployment explanation.
Run multiple consumer replicas.
```

---

### Day 28

Interview:

```text
Revise 50 Kafka Q&A.
Practice speaking answers.
```

---

### Day 29

Mock interview:

```text
Explain Kafka in your project.
Explain retry/DLT.
Explain consumer lag.
Explain Kafka vs REST.
Explain Kafka vs Spring Batch.
```

---

### Day 30

Final revision:

```text
Revise architecture diagram.
Revise project explanation.
Revise production issues.
Revise commands.
Revise code flow.
```

Final target:

> You should be able to confidently explain Kafka from basics, integrate it with Spring Boot, connect it with your Blog/Microservices project, and answer production-level questions like retry, DLT, consumer lag, duplicates, ordering, and scaling.

---

# Final Interview-Ready Kafka Answer

Use this when interviewer asks: **“Tell me about Kafka and how you used it.”**

> Kafka is a distributed event streaming platform used for asynchronous communication between services. In my microservices project, we used Kafka to decouple services and process background tasks reliably. For example, when a post was created in the blog application, the post-service saved the post in MySQL and published a `PostCreatedEvent` to Kafka. Other services like notification-service, audit-service, analytics-service, and search-service consumed this event independently.

> We used topics to separate event types, partitions for scalability, consumer groups for parallel processing, and offsets to track message progress. For failure handling, we configured retry with backoff and Dead Letter Topic. Since Kafka can deliver duplicate messages in retry scenarios, we designed consumers to be idempotent using a unique `eventId`. For production reliability, we monitored consumer lag, avoided large messages, used proper message keys for ordering, and considered the Outbox Pattern for DB and Kafka consistency.

> REST was still used for synchronous operations like login and CRUD APIs, while Kafka was used for asynchronous event-driven processing. This improved scalability, reduced tight coupling, and made the system more fault tolerant.

[1]: https://kafka.apache.org/uses "Uses | Apache Kafka"
[2]: https://kafka.apache.org/41/design/design/ "Design | Apache Kafka"
[3]: https://kafka.apache.org/40/getting-started/zk2kraft/ "KRaft vs ZooKeeper | Apache Kafka"
[4]: https://docs.spring.io/spring-kafka/reference/kafka/sending-messages.html "Sending Messages :: Spring Kafka"
[5]: https://docs.confluent.io/kafka/design/delivery-semantics.html?utm_source=chatgpt.com "Message Delivery Guarantees for Apache Kafka"
[6]: https://docs.spring.io/spring-kafka/reference/retrytopic/features.html "Features :: Spring Kafka"
[7]: https://docs.spring.io/spring-kafka/reference/kafka/annotation-error-handling.html "Handling Exceptions :: Spring Kafka"
[8]: https://kafka.apache.org/42/security/authorization-and-acls/ "Authorization and ACLs | Apache Kafka"
