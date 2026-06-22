Below are **Top 50 AWS interview questions** for your **Java/Spring Boot Backend Developer profile**, written in a clean interview-preparation style.

AWS interviews usually check 5 things: **core cloud concepts, networking, compute, storage/database, security, deployment, monitoring, and real-world architecture decisions**.

---

# AWS Interview Questions — Top 50

## 1. What is AWS?

**AWS**, Amazon Web Services, is a cloud platform that provides compute, storage, database, networking, security, monitoring, DevOps, AI/ML, and many other services.

Instead of buying physical servers, companies rent resources from AWS.

**Example:**
For a Spring Boot application, you can use:

| Requirement        | AWS Service                            |
| ------------------ | -------------------------------------- |
| Run backend app    | EC2 / ECS / Elastic Beanstalk / Lambda |
| Store database     | RDS MySQL / PostgreSQL                 |
| Store images/files | S3                                     |
| Monitor app        | CloudWatch                             |
| Secure access      | IAM                                    |
| Manage domain      | Route 53                               |
| Load balancing     | ALB                                    |

---

## 2. What is cloud computing?

Cloud computing means using computing resources like servers, storage, databases, and networking over the internet.

### Main benefits

| Benefit           | Meaning                                      |
| ----------------- | -------------------------------------------- |
| Pay as you go     | Pay only for used resources                  |
| Scalability       | Increase/decrease resources based on traffic |
| High availability | Run across multiple data centers             |
| Managed services  | AWS handles infrastructure maintenance       |
| Faster deployment | Create servers/databases in minutes          |

---

## 3. What are AWS Regions and Availability Zones?

A **Region** is a geographical area, like `ap-south-1` for Mumbai or `eu-west-1` for Ireland.

An **Availability Zone**, AZ, is an isolated data center inside a Region.

**Example:**

```text
Region: ap-south-1 Mumbai
AZ 1: ap-south-1a
AZ 2: ap-south-1b
AZ 3: ap-south-1c
```

For production, we deploy applications across **multiple AZs** to avoid downtime if one AZ fails.

---

## 4. What is the AWS Shared Responsibility Model?

AWS and customer both share security responsibility.

| Responsibility                  | Owner    |
| ------------------------------- | -------- |
| Physical data center security   | AWS      |
| Hardware/network infrastructure | AWS      |
| OS patching on EC2              | Customer |
| IAM users and permissions       | Customer |
| Application security            | Customer |
| Data encryption choices         | Customer |

**Interview answer:**
AWS secures the cloud infrastructure, but we secure what we deploy inside the cloud.

---

## 5. What are the six pillars of AWS Well-Architected Framework?

The AWS Well-Architected Framework is based on six pillars: **Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability**. ([AWS Documentation][1])

| Pillar                 | Meaning                          |
| ---------------------- | -------------------------------- |
| Operational Excellence | Run and monitor systems properly |
| Security               | Protect data and access          |
| Reliability            | Recover from failures            |
| Performance Efficiency | Use resources efficiently        |
| Cost Optimization      | Avoid unnecessary cost           |
| Sustainability         | Reduce environmental impact      |

---

# IAM and Security

## 6. What is IAM in AWS?

**IAM**, Identity and Access Management, is used to control **who can access what** in AWS.

Main IAM components:

| Component | Meaning                            |
| --------- | ---------------------------------- |
| User      | Individual AWS identity            |
| Group     | Collection of users                |
| Role      | Temporary permission identity      |
| Policy    | JSON document defining permissions |

AWS IAM identity-based policies are attached to users, groups, or roles and define what that identity can do. ([AWS Documentation][2])

---

## 7. What is the difference between IAM User and IAM Role?

| IAM User                          | IAM Role                                     |
| --------------------------------- | -------------------------------------------- |
| Long-term identity                | Temporary identity                           |
| Has username/password/access keys | Assumed by users/services                    |
| Used by humans or apps            | Used by AWS services or cross-account access |
| Example: Developer login          | Example: EC2 accessing S3                    |

**Best practice:**
For EC2, Lambda, ECS, avoid hardcoding access keys. Use **IAM Role**.

---

## 8. What is an IAM Policy?

IAM policy is a JSON document that defines allowed or denied actions.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

This allows upload and download objects from a specific S3 bucket.

---

## 9. What is the principle of least privilege?

Least privilege means giving only the permissions required to perform a task.

**Bad practice:**

```json
"Action": "*",
"Resource": "*"
```

**Good practice:**

```json
"Action": ["s3:GetObject"],
"Resource": "arn:aws:s3:::my-app-bucket/*"
```

**Interview line:**
In production, we should avoid admin permissions and use fine-grained IAM policies.

---

## 10. What is MFA in AWS?

**MFA**, Multi-Factor Authentication, adds an extra security layer.

Login requires:

```text
Password + OTP/Auth app code
```

It should be enabled especially for:

| Account/User     | Why                   |
| ---------------- | --------------------- |
| Root user        | Most powerful account |
| Admin users      | High-risk access      |
| Production users | Security compliance   |

---

## 11. What is the difference between Security Group and NACL?

| Security Group                       | NACL                                      |
| ------------------------------------ | ----------------------------------------- |
| Works at instance/ENI level          | Works at subnet level                     |
| Stateful                             | Stateless                                 |
| Only allow rules                     | Allow and deny rules                      |
| Return traffic automatically allowed | Return traffic must be explicitly allowed |
| Used for EC2/RDS access control      | Used for subnet-level control             |

**Example:**
For EC2 running Spring Boot on port `8080`, add inbound rule in Security Group:

```text
Type: Custom TCP
Port: 8080
Source: ALB Security Group
```

---

# EC2 and Compute

## 12. What is EC2?

**EC2**, Elastic Compute Cloud, is a virtual server in AWS.

You can install Java, Tomcat, Docker, Jenkins, MySQL client, and deploy Spring Boot apps.

**Example deployment:**

```bash
java -jar blog-app.jar
```

or

```bash
docker run -p 8080:8080 blog-app
```

---

## 13. What is an AMI?

**AMI**, Amazon Machine Image, is a template used to launch EC2 instances.

It contains:

| Item           | Example              |
| -------------- | -------------------- |
| OS             | Ubuntu, Amazon Linux |
| Software       | Java, Docker, Nginx  |
| Configurations | Pre-installed setup  |

**Use case:**
Create one configured EC2 instance, make AMI, and launch multiple identical servers.

---

## 14. What are EC2 instance types?

Instance types define CPU, memory, storage, and networking capacity.

| Type       | Use case           |
| ---------- | ------------------ |
| t3/t4g     | General purpose    |
| m family   | Balanced workloads |
| c family   | CPU-heavy apps     |
| r family   | Memory-heavy apps  |
| g/p family | GPU workloads      |

For Java Spring Boot backend, usually **t3.medium, t3.large, m5.large, m6i.large** are common starting points.

---

## 15. What is the difference between On-Demand, Reserved, and Spot Instances?

| Type      | Use case                          |
| --------- | --------------------------------- |
| On-Demand | Pay per use, flexible             |
| Reserved  | Long-term predictable workloads   |
| Spot      | Very cheap but can be interrupted |

**Example:**
Production Spring Boot app: On-Demand or Reserved.
Batch processing: Spot can be used if interruption is acceptable.

---

## 16. What is Auto Scaling?

Auto Scaling automatically increases or decreases EC2 instances based on demand.

Example:

```text
If CPU > 70%, add new EC2 instance.
If CPU < 30%, remove extra EC2 instance.
```

AWS Auto Scaling groups can launch or terminate EC2 instances based on policies, health checks, and schedules. ([AWS Documentation][3])

---

## 17. What is a Launch Template?

A Launch Template defines EC2 instance configuration for Auto Scaling.

It includes:

| Config         | Example                |
| -------------- | ---------------------- |
| AMI            | Ubuntu image           |
| Instance type  | t3.medium              |
| Security group | app-sg                 |
| Key pair       | app-key                |
| User data      | install Java/start app |

Auto Scaling groups use launch templates as configuration templates for launching EC2 instances. ([AWS Documentation][4])

---

## 18. What is Load Balancer in AWS?

Load Balancer distributes traffic across multiple targets.

Example:

```text
User → ALB → EC2-1 / EC2-2 / EC2-3
```

Benefits:

| Benefit           | Meaning                           |
| ----------------- | --------------------------------- |
| High availability | Traffic goes to healthy instances |
| Scalability       | Supports multiple servers         |
| Fault tolerance   | Failed instance is skipped        |
| SSL termination   | HTTPS can terminate at ALB        |

---

## 19. Difference between ALB, NLB, and CLB?

| Load Balancer | Layer              | Use case                      |
| ------------- | ------------------ | ----------------------------- |
| ALB           | Layer 7 HTTP/HTTPS | Web apps, REST APIs           |
| NLB           | Layer 4 TCP/UDP    | High performance, low latency |
| CLB           | Legacy             | Old applications              |

For Spring Boot REST APIs, mostly **Application Load Balancer** is used.

---

## 20. What is Target Group?

A Target Group is a group of resources behind a load balancer.

Targets can be:

```text
EC2 instances
IP addresses
Lambda functions
ECS tasks
```

ALB forwards requests to healthy targets based on health checks.

Example health check:

```text
Path: /actuator/health
Expected status: 200
```

For Spring Boot, `/actuator/health` is commonly used.

---

# VPC and Networking

## 21. What is VPC?

**VPC**, Virtual Private Cloud, is your private network inside AWS.

It contains:

| Component        | Purpose                          |
| ---------------- | -------------------------------- |
| Subnet           | Divides network                  |
| Route table      | Controls routing                 |
| Internet Gateway | Internet access                  |
| NAT Gateway      | Private subnet outbound internet |
| Security Group   | Instance firewall                |
| NACL             | Subnet firewall                  |

Route tables define which networks a VPC can communicate with, using targets like Internet Gateway, NAT Gateway, VPC peering, or VPN. ([AWS Documentation][5])

---

## 22. What is a public subnet and private subnet?

| Public Subnet                     | Private Subnet                      |
| --------------------------------- | ----------------------------------- |
| Has route to Internet Gateway     | No direct route to Internet Gateway |
| Can host ALB, bastion, public EC2 | Hosts app servers, databases        |
| Public IP possible                | Usually private IP only             |

**Recommended architecture:**

```text
Public Subnet:
- ALB
- NAT Gateway

Private Subnet:
- Spring Boot EC2/ECS
- RDS
```

---

## 23. What is Internet Gateway?

Internet Gateway allows resources in a public subnet to communicate with the internet.

For EC2 to be public:

```text
EC2 has public IP
Subnet route table has 0.0.0.0/0 → Internet Gateway
Security group allows required inbound traffic
```

---

## 24. What is NAT Gateway?

NAT Gateway allows private subnet resources to access the internet **outbound**, without allowing inbound internet access.

Example:

```text
Private EC2 → NAT Gateway → Internet Gateway → Internet
```

Use case:
Private EC2 needs to download packages or call external APIs.

AWS NAT Gateway translates private IPv4 addresses for outbound traffic and maps return traffic back to the original source. ([AWS Documentation][6])

---

## 25. What is Route Table?

Route table controls network traffic routing.

Example public subnet route:

```text
10.0.0.0/16     local
0.0.0.0/0       Internet Gateway
```

Example private subnet route:

```text
10.0.0.0/16     local
0.0.0.0/0       NAT Gateway
```

---

## 26. What is VPC Peering?

VPC Peering connects two VPCs privately.

Example:

```text
VPC-A: Application
VPC-B: Shared services/database
```

Important point:
VPC Peering is **not transitive**.

```text
A ↔ B and B ↔ C does not mean A ↔ C
```

For many VPCs, use **Transit Gateway**.

---

## 27. What is VPC Endpoint?

VPC Endpoint allows private connection from VPC to AWS services without using public internet.

Example:

```text
Private EC2 → VPC Endpoint → S3
```

Types:

| Type               | Use case                            |
| ------------------ | ----------------------------------- |
| Gateway Endpoint   | S3, DynamoDB                        |
| Interface Endpoint | Many AWS services using PrivateLink |

---

## 28. What is Route 53?

Route 53 is AWS DNS service.

Common use cases:

| Use case            | Example                             |
| ------------------- | ----------------------------------- |
| Domain registration | example.com                         |
| DNS routing         | api.example.com → ALB               |
| Health checks       | Route only to healthy endpoints     |
| Routing policies    | Simple, weighted, latency, failover |

For your Spring Boot app with domain:

```text
api.yourdomain.com → Application Load Balancer
```

---

# Storage

## 29. What is S3?

**S3**, Simple Storage Service, is object storage.

It stores data as:

```text
Bucket → Object → Key
```

Example:

```text
Bucket: blog-app-images
Object key: posts/101/image.png
```

By default, objects in S3 are stored in the S3 Standard storage class, and AWS offers other storage classes based on access and cost needs. ([AWS Documentation][7])

---

## 30. What is the difference between S3 and EBS?

| S3                            | EBS                      |
| ----------------------------- | ------------------------ |
| Object storage                | Block storage            |
| Used for files/images/backups | Used as EC2 disk         |
| Access via API/HTTP           | Attached to EC2          |
| Highly scalable               | Bound to AZ              |
| Example: image upload         | Example: EC2 root volume |

For Spring Boot file upload, prefer **S3**, not EC2 local disk.

---

## 31. What is S3 Versioning?

S3 Versioning keeps multiple versions of an object.

Example:

```text
resume.pdf version 1
resume.pdf version 2
resume.pdf version 3
```

Benefits:

| Benefit                 | Meaning                    |
| ----------------------- | -------------------------- |
| Recover deleted objects | Restore older version      |
| Audit changes           | Track object history       |
| Protection              | Avoid accidental overwrite |

---

## 32. What is S3 Lifecycle Policy?

S3 Lifecycle moves or deletes objects automatically.

Example:

```text
After 30 days → move to Standard-IA
After 180 days → move to Glacier
After 365 days → delete
```

S3 Lifecycle can transition objects to lower-cost storage classes or delete expired objects. ([AWS Documentation][8])

---

## 33. What are S3 storage classes?

| Storage Class           | Use case                 |
| ----------------------- | ------------------------ |
| S3 Standard             | Frequently accessed data |
| S3 Intelligent-Tiering  | Unknown access pattern   |
| S3 Standard-IA          | Infrequent access        |
| S3 One Zone-IA          | Infrequent, single AZ    |
| S3 Glacier              | Archive                  |
| S3 Glacier Deep Archive | Long-term archive        |

**Interview answer:**
Choose storage class based on access frequency, retrieval time, availability, and cost.

---

## 34. Java example: How to upload file to S3 from Spring Boot?

AWS SDK for Java provides Java APIs for AWS services like S3, EC2, and DynamoDB. AWS SDK for Java 2.x supports Java 8+ and features like non-blocking I/O support. ([AWS Documentation][9])

### Maven dependency

```xml
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
    <version>2.25.60</version>
</dependency>
```

### Java service example

```java
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;
import software.amazon.awssdk.core.sync.RequestBody;
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.PutObjectRequest;

import java.io.IOException;
import java.util.UUID;

@Service
public class S3FileService {

    private final S3Client s3Client = S3Client.create();
    private final String bucketName = "my-blog-app-bucket";

    public String uploadFile(MultipartFile file) throws IOException {
        String key = "uploads/" + UUID.randomUUID() + "-" + file.getOriginalFilename();

        PutObjectRequest request = PutObjectRequest.builder()
                .bucket(bucketName)
                .key(key)
                .contentType(file.getContentType())
                .build();

        s3Client.putObject(
                request,
                RequestBody.fromBytes(file.getBytes())
        );

        return "https://" + bucketName + ".s3.amazonaws.com/" + key;
    }
}
```

**Best practice:**
Do not store AWS access key/secret key in code. Use IAM Role, environment variables, or AWS Secrets Manager.

---

# Database

## 35. What is RDS?

**RDS**, Relational Database Service, is managed relational database service.

Supported engines include:

```text
MySQL
PostgreSQL
MariaDB
Oracle
SQL Server
Amazon Aurora
```

Amazon RDS helps set up, operate, and scale relational databases in AWS and manages common database administration tasks. ([AWS Documentation][10])

---

## 36. What is the difference between RDS Multi-AZ and Read Replica?

| Feature                 | Multi-AZ                          | Read Replica             |
| ----------------------- | --------------------------------- | ------------------------ |
| Purpose                 | High availability                 | Read scaling             |
| Used for                | Failover                          | Heavy read traffic       |
| Sync/Async              | Usually synchronous standby       | Asynchronous replication |
| Can serve read traffic? | No, standby is not used for reads | Yes                      |
| Example                 | Production DB failover            | Reporting/search APIs    |

**Interview answer:**
Multi-AZ is for availability. Read Replica is for performance/read scaling.

---

## 37. What is Amazon Aurora?

Amazon Aurora is AWS’s cloud-optimized relational database compatible with MySQL and PostgreSQL.

Benefits:

| Benefit              | Meaning                                                  |
| -------------------- | -------------------------------------------------------- |
| High performance     | Better than traditional MySQL/Postgres in many workloads |
| Auto storage scaling | Storage grows automatically                              |
| Replication          | Multiple replicas                                        |
| Managed              | Backups, patching, monitoring                            |

Use Aurora for high-scale production workloads.

---

## 38. What is DynamoDB?

DynamoDB is a fully managed NoSQL key-value and document database.

Common terms:

| Term          | Meaning                  |
| ------------- | ------------------------ |
| Table         | Collection of items      |
| Item          | Row/document             |
| Partition key | Primary distribution key |
| Sort key      | Optional ordering key    |
| GSI           | Global Secondary Index   |

Use DynamoDB when you need high-scale, low-latency key-value access.

Example use case:

```text
User sessions
Shopping cart
Order status
Feature flags
API rate-limit counters
```

---

## 39. RDS vs DynamoDB?

| RDS                     | DynamoDB                   |
| ----------------------- | -------------------------- |
| Relational SQL database | NoSQL database             |
| Supports joins          | No joins                   |
| Schema-based            | Flexible schema            |
| Vertical + read scaling | Massive horizontal scaling |
| Good for transactions   | Good for key-value access  |

**For Spring Boot enterprise apps:**
If business data has relationships like users, orders, payments, use **RDS**.
If access pattern is key-based and massive scale, use **DynamoDB**.

---

## 40. What is ElastiCache?

ElastiCache is managed Redis or Memcached.

Use cases:

| Use case         | Example                  |
| ---------------- | ------------------------ |
| Caching          | Cache product details    |
| Session storage  | Store login/session data |
| Rate limiting    | API request counter      |
| Leaderboard      | Redis sorted set         |
| Distributed lock | Redis lock               |

Example Spring Boot use:

```text
Spring Boot → Redis ElastiCache → RDS
```

This reduces DB load.

---

# Messaging and Event-Driven Architecture

## 41. What is SQS?

**SQS**, Simple Queue Service, is a managed message queue used to decouple applications.

Example:

```text
Order Service → SQS Queue → Payment Service
```

Amazon SQS provides a secure, durable, available hosted queue to integrate and decouple distributed software systems. ([AWS Documentation][11])

---

## 42. What is SQS Visibility Timeout?

Visibility timeout is the time during which a consumed message is hidden from other consumers.

Example:

```text
Consumer receives message
Message hidden for 30 seconds
Consumer processes and deletes it
```

If the consumer does not delete it before timeout, the message becomes visible again. The default visibility timeout is 30 seconds. ([AWS Documentation][12])

---

## 43. What is Dead Letter Queue?

A **DLQ**, Dead Letter Queue, stores messages that failed multiple processing attempts.

Example:

```text
Main Queue → processing fails 5 times → DLQ
```

Use DLQ for:

| Use case       | Meaning                    |
| -------------- | -------------------------- |
| Debugging      | Find bad messages          |
| Retry analysis | Understand failure reason  |
| Data recovery  | Reprocess after fixing bug |

SQS supports DLQs for messages that are not processed successfully. ([AWS Documentation][13])

---

## 44. Difference between SQS and SNS?

| SQS                                  | SNS                                       |
| ------------------------------------ | ----------------------------------------- |
| Queue                                | Pub/sub notification                      |
| One message consumed by one consumer | One message delivered to many subscribers |
| Pull-based                           | Push-based                                |
| Used for async processing            | Used for fanout notification              |

Example:

```text
SNS Topic
 ├── SQS Queue for Email Service
 ├── SQS Queue for SMS Service
 └── Lambda for Audit Service
```

---

## 45. Difference between SNS, SQS, EventBridge, and Kinesis?

| Service     | Best for                            |
| ----------- | ----------------------------------- |
| SQS         | Queue-based async processing        |
| SNS         | Fanout notification                 |
| EventBridge | Event routing between services/apps |
| Kinesis     | Real-time streaming data            |

**Example:**
For order processing:

```text
Order Created → EventBridge → Payment Service / Inventory Service / Email Service
```

For clickstream analytics:

```text
User clicks → Kinesis → Analytics pipeline
```

---

# Serverless

## 46. What is AWS Lambda?

AWS Lambda runs code without managing servers.

You provide:

```text
Code + Runtime + Trigger
```

Example triggers:

| Trigger         | Example          |
| --------------- | ---------------- |
| API Gateway     | REST API         |
| S3              | Image uploaded   |
| SQS             | Message received |
| EventBridge     | Scheduled job    |
| DynamoDB Stream | Data changed     |

Lambda event source mapping reads items from stream or queue-based services and invokes a function with batches of records. ([AWS Documentation][14])

---

## 47. Lambda vs EC2?

| Lambda                    | EC2                               |
| ------------------------- | --------------------------------- |
| Serverless                | You manage server                 |
| Pay per execution         | Pay for running instance          |
| Auto scales automatically | Need Auto Scaling                 |
| Max execution time limit  | Long-running apps possible        |
| Good for events/APIs/jobs | Good for full apps/custom servers |

**Use Lambda for:**
Small event-driven tasks, file processing, scheduled jobs.

**Use EC2/ECS for:**
Long-running Spring Boot microservices.

---

## 48. What is API Gateway?

API Gateway is used to create, publish, secure, monitor, and manage APIs.

Use cases:

```text
Client → API Gateway → Lambda
Client → API Gateway → ECS/EC2 backend
```

API Gateway supports REST, HTTP, and WebSocket APIs at scale. ([AWS Documentation][15])

---

# Monitoring, Logging, DevOps

## 49. What is CloudWatch?

CloudWatch is AWS monitoring and observability service.

It collects:

| Data       | Example              |
| ---------- | -------------------- |
| Metrics    | CPU, memory, latency |
| Logs       | Application logs     |
| Alarms     | CPU > 80%            |
| Dashboards | Visual monitoring    |
| Events     | Trigger actions      |

CloudWatch monitors AWS resources and applications in real time and provides observability for performance, operational health, and resource utilization. ([AWS Documentation][16])

**Spring Boot example:**
Logs from EC2/ECS can be pushed to CloudWatch Logs.

```text
Spring Boot app logs → CloudWatch Logs → Alarm/Dashboard
```

---

## 50. How would you deploy a Spring Boot application on AWS?

A professional architecture:

```text
User
 ↓
Route 53
 ↓
Application Load Balancer
 ↓
Private EC2/ECS running Spring Boot
 ↓
RDS MySQL/PostgreSQL
 ↓
S3 for file storage
 ↓
CloudWatch for logs/metrics
```

### Simple EC2 deployment

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -jar blog-app.jar
```

### Docker deployment

```bash
docker build -t blog-app .
docker run -p 8080:8080 blog-app
```

### Production deployment

| Layer        | AWS Service            |
| ------------ | ---------------------- |
| DNS          | Route 53               |
| HTTPS        | ACM + ALB              |
| Backend      | ECS / EC2 Auto Scaling |
| Database     | RDS Multi-AZ           |
| File storage | S3                     |
| Secrets      | Secrets Manager        |
| Logs         | CloudWatch             |
| CI/CD        | CodePipeline / Jenkins |
| Security     | IAM, SG, KMS           |

**Strong interview answer:**
For production, I would not expose Spring Boot EC2 directly to the internet. I would place ALB in public subnets and application servers in private subnets. RDS should also be private. Static files/images should go to S3, secrets should be stored in Secrets Manager, and logs/metrics should be monitored through CloudWatch.

---

# Bonus: Common Scenario-Based AWS Interview Questions

## 51. Your Spring Boot app is slow. How will you troubleshoot in AWS?

Check:

```text
1. CloudWatch CPU/memory metrics
2. ALB target response time
3. Application logs
4. RDS CPU, connections, slow queries
5. Network latency
6. Thread pool and DB connection pool
7. External API latency
```

For Java app:

```text
Check HikariCP pool
Check JVM heap
Check GC logs
Check slow SQL queries
Check actuator metrics
```

---

## 52. Your EC2 instance is not accessible. What will you check?

Check step by step:

```text
1. Instance state: running?
2. Public IP assigned?
3. Security Group inbound rule open?
4. NACL allows traffic?
5. Route table has internet gateway?
6. Correct key pair?
7. OS firewall blocking?
8. Application running on correct port?
```

For Spring Boot:

```bash
ps -ef | grep java
netstat -tulnp | grep 8080
curl localhost:8080/actuator/health
```

---

## 53. Your application cannot connect to RDS. What will you check?

Check:

```text
1. RDS status available?
2. Correct endpoint and port?
3. RDS Security Group allows app SG?
4. App and RDS in same VPC or connected VPC?
5. DB username/password correct?
6. RDS publicly accessible or private?
7. Subnet route/network ACL issue?
8. Max DB connections reached?
```

Spring Boot config:

```properties
spring.datasource.url=jdbc:mysql://mydb.xxxxxx.ap-south-1.rds.amazonaws.com:3306/blogdb
spring.datasource.username=admin
spring.datasource.password=secret
```

---

## 54. How will you secure a Spring Boot application on AWS?

Use:

```text
1. HTTPS using ACM + ALB
2. Private subnets for app and DB
3. IAM roles instead of access keys
4. Secrets Manager for DB password
5. Security Groups with least access
6. S3 bucket private by default
7. CloudTrail for audit
8. WAF for web attack protection
9. KMS encryption
10. CloudWatch alarms
```

---

## 55. How will you design highly available architecture?

Use:

```text
1. Multi-AZ deployment
2. ALB across public subnets
3. Auto Scaling Group across multiple AZs
4. RDS Multi-AZ
5. S3 for durable file storage
6. CloudWatch alarms
7. Route 53 health checks
8. Backup and disaster recovery strategy
```

---

# Java Backend AWS Interview Code Example: SQS Producer

### Maven dependency

```xml
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>sqs</artifactId>
    <version>2.25.60</version>
</dependency>
```

### Java code

```java
import software.amazon.awssdk.services.sqs.SqsClient;
import software.amazon.awssdk.services.sqs.model.SendMessageRequest;

public class OrderEventPublisher {

    private final SqsClient sqsClient = SqsClient.create();

    public void publishOrderCreatedEvent(String queueUrl, String orderJson) {
        SendMessageRequest request = SendMessageRequest.builder()
                .queueUrl(queueUrl)
                .messageBody(orderJson)
                .build();

        sqsClient.sendMessage(request);
    }
}
```

### Real use case

```text
Order API receives order
 ↓
Save order in RDS
 ↓
Send message to SQS
 ↓
Payment/Email/Inventory service processes asynchronously
```

---

# One Strong AWS Architecture Answer for Interview

You can use this answer when interviewer asks:

**“Explain how you would deploy your Java Spring Boot application on AWS.”**

> I would deploy the application using a secure and highly available architecture. The users would access the application through Route 53 and an Application Load Balancer. The ALB would route traffic to Spring Boot instances running in private subnets, either on EC2 Auto Scaling Group or ECS. The database would be Amazon RDS MySQL/PostgreSQL with Multi-AZ enabled for high availability. File uploads would be stored in S3 instead of local EC2 storage. Secrets like DB password would be stored in Secrets Manager. IAM roles would be used instead of hardcoded access keys. Logs and metrics would be sent to CloudWatch, and alarms would be configured for CPU, memory, error rate, and database connections. For CI/CD, Jenkins or AWS CodePipeline can build, test, dockerize, and deploy the application.

---

# AWS Topics You Should Prioritize for Your Profile

For your **7 years Java backend + Spring Boot + microservices target**, focus in this order:

| Priority | Topic                                    |
| -------- | ---------------------------------------- |
| 1        | IAM, Security Group, VPC basics          |
| 2        | EC2, ALB, Auto Scaling                   |
| 3        | S3 file upload and lifecycle             |
| 4        | RDS, Multi-AZ, Read Replica              |
| 5        | CloudWatch logs/alarms                   |
| 6        | SQS/SNS/EventBridge                      |
| 7        | ECS/EKS basics                           |
| 8        | Secrets Manager, KMS                     |
| 9        | Route 53, ACM, HTTPS                     |
| 10       | Real Spring Boot deployment architecture |

For interviews, your strongest story should be:

```text
I deployed a Spring Boot application on AWS.
I understand EC2/Lightsail basics.
Next, I am improving it with RDS, S3, ALB, Docker, ECS/Kubernetes, CloudWatch, and CI/CD.
```

That sounds practical and believable for your profile.

[1]: https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html?utm_source=chatgpt.com "The pillars of the framework - AWS Well-Architected ..."
[2]: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html?utm_source=chatgpt.com "Identity-based policies and resource ..."
[3]: https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html?utm_source=chatgpt.com "What is Amazon EC2 Auto Scaling?"
[4]: https://docs.aws.amazon.com/autoscaling/ec2/userguide/launch-templates.html?utm_source=chatgpt.com "Auto Scaling launch templates"
[5]: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html?utm_source=chatgpt.com "Configure route tables - Amazon Virtual Private Cloud"
[6]: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html?utm_source=chatgpt.com "NAT gateways - Amazon Virtual Private Cloud"
[7]: https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com "Understanding and managing Amazon S3 storage classes"
[8]: https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html?utm_source=chatgpt.com "Managing the lifecycle of objects"
[9]: https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/home.html?utm_source=chatgpt.com "What is the AWS SDK for Java 2.x"
[10]: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html?utm_source=chatgpt.com "What is Amazon Relational Database Service (Amazon RDS)?"
[11]: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html?utm_source=chatgpt.com "What is Amazon Simple Queue Service?"
[12]: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html?utm_source=chatgpt.com "Amazon SQS visibility timeout"
[13]: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html?utm_source=chatgpt.com "Using dead-letter queues in Amazon SQS"
[14]: https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html?utm_source=chatgpt.com "How Lambda processes records from stream and queue- ..."
[15]: https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html?utm_source=chatgpt.com "Amazon API Gateway - AWS Documentation"
[16]: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html?utm_source=chatgpt.com "What is Amazon CloudWatch? - Amazon CloudWatch"
