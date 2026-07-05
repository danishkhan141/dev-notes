## 1. High-Level Summary

**Kubernetes**, also called **K8s**, is a container orchestration platform used to run, manage, scale, and monitor containerized applications in production.

For a Java backend developer, the simple explanation is:

> We package our Spring Boot application as a Docker image, deploy it into Kubernetes, and Kubernetes manages running containers, scaling, service discovery, load balancing, configuration, secrets, restart, rollout, rollback, and production availability.

In a Spring Boot microservices project, Kubernetes helps us manage services like:

```text
api-gateway
user-service
post-service
category-service
auth-service
notification-service
```

Instead of manually starting these services on different servers, Kubernetes provides a standard way to deploy and operate them.

Basic flow:

```text
Spring Boot Code
     ↓
Build JAR
     ↓
Docker Image
     ↓
Push to Docker Registry
     ↓
Kubernetes Deployment
     ↓
Pods run inside Cluster
     ↓
Service exposes Pods
     ↓
Ingress/API Gateway exposes application externally
```

For interviews, remember this line:

> Kubernetes is mainly used to run containerized microservices in a scalable, self-healing, and production-ready way.

---

## 2. Why This Topic Is Important

For **Java Backend / Senior Java Developer / Microservices Developer** roles, Kubernetes is important because most modern Java applications are deployed as containers.

Earlier deployment style:

```text
WAR/JAR → Tomcat/WebLogic/JBoss → VM/server
```

Modern deployment style:

```text
Spring Boot JAR → Docker Image → Kubernetes Pod → Service → Ingress/API Gateway
```

Kubernetes is important because it solves real production problems:

| Problem                             | Kubernetes Solution            |
| ----------------------------------- | ------------------------------ |
| Application crashes                 | Pod restart/self-healing       |
| Need multiple instances             | ReplicaSet/Deployment          |
| Need zero-downtime deployment       | Rolling update                 |
| Need rollback                       | Deployment rollback            |
| Need internal service communication | Kubernetes Service/DNS         |
| Need environment-specific config    | ConfigMap                      |
| Need secure credentials             | Secret                         |
| Need traffic routing                | Service/Ingress                |
| Need horizontal scaling             | HPA                            |
| Need health checks                  | Liveness/Readiness probes      |
| Need resource control               | CPU/memory requests and limits |
| Need CI/CD deployment               | Jenkins + Docker + Kubernetes  |

As a 7-year Java developer, you are not expected to be a Kubernetes administrator, but you should clearly understand how your Spring Boot microservices are deployed, exposed, scaled, monitored, and debugged in Kubernetes.

---

## 3. Core Concepts

### 3.1 Container

A **container** is a lightweight runtime package that contains your application and everything required to run it.

For example, your Spring Boot blog application becomes:

```text
blog-app.jar + JDK runtime + configuration = Docker image
```

Then Kubernetes runs this Docker image inside Pods.

Interview answer:

> In our project, we create Docker images for Spring Boot services and deploy those images into Kubernetes. This makes deployment consistent across development, testing, and production environments.

---

### 3.2 Kubernetes Cluster

A **cluster** is a group of machines where Kubernetes runs applications.

It contains:

```text
Cluster
 ├── Control Plane
 └── Worker Nodes
```

The **Control Plane** manages the cluster.

The **Worker Nodes** run application workloads.

---

### 3.3 Node

A **Node** is a machine where application Pods run.

It can be:

```text
Physical server
Virtual machine
Cloud VM
```

In AWS, Kubernetes nodes usually run on EC2 instances.

---

### 3.4 Pod

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod usually contains one main container.

Example:

```text
user-service Pod
 └── user-service container
```

Important point:

> We do not usually deploy containers directly in Kubernetes. We deploy Pods, and Pods run containers.

Pods are temporary. They can be created, deleted, restarted, or moved to another node.

---

### 3.5 Deployment

A **Deployment** manages Pods.

It ensures that the required number of Pods are always running.

Example:

```text
Deployment: user-service
Replicas: 3

Kubernetes ensures:
3 user-service Pods are always running
```

If one Pod crashes, Deployment creates a new Pod.

This is called **self-healing**.

---

### 3.6 ReplicaSet

A **ReplicaSet** ensures that a specific number of Pod replicas are running.

Usually, we do not create ReplicaSet directly. We create a Deployment, and Kubernetes automatically creates ReplicaSet.

Flow:

```text
Deployment → ReplicaSet → Pods
```

---

### 3.7 Service

A **Service** provides a stable network endpoint for Pods.

Pods are temporary and their IP addresses can change. Service solves this problem by giving a stable name and IP.

Example:

```text
user-service Pods:
10.1.1.5
10.1.1.6
10.1.1.7

Kubernetes Service:
user-service
```

Other services can call:

```text
http://user-service:8081
```

Instead of calling Pod IPs directly.

---

### 3.8 Types of Services

#### ClusterIP

Default service type. Used for internal communication inside the cluster.

Example:

```text
post-service → user-service
```

#### NodePort

Exposes service on a port of every Kubernetes node.

Mostly used for testing or local setup.

#### LoadBalancer

Creates an external load balancer using cloud provider.

Common in AWS, Azure, GCP.

#### ExternalName

Maps a Kubernetes service to an external DNS name.

---

### 3.9 Namespace

A **Namespace** is used to logically separate resources inside a cluster.

Example:

```text
dev namespace
qa namespace
prod namespace
```

In real projects:

```text
blog-dev
blog-qa
blog-prod
```

This keeps environments separate.

---

### 3.10 ConfigMap

A **ConfigMap** stores non-sensitive configuration.

Examples:

```text
SPRING_PROFILES_ACTIVE=prod
SERVER_PORT=8080
LOG_LEVEL=INFO
EUREKA_URL=http://eureka-server:8761/eureka
```

Instead of hardcoding values in application code, we externalize them.

---

### 3.11 Secret

A **Secret** stores sensitive configuration.

Examples:

```text
DB_USERNAME
DB_PASSWORD
JWT_SECRET
AWS_ACCESS_KEY
```

Important interview point:

> ConfigMap is used for normal configuration, while Secret is used for sensitive values like passwords, tokens, and keys.

---

### 3.12 Volume

A **Volume** provides storage to containers.

By default, container storage is temporary. When a Pod is deleted, data is lost.

Volumes help when an application needs persistent data.

For stateless Spring Boot microservices, we usually avoid storing files inside Pods. We use external storage like:

```text
AWS S3
RDS
EFS
PersistentVolume
```

---

### 3.13 PersistentVolume and PersistentVolumeClaim

**PersistentVolume**, or PV, is actual storage.

**PersistentVolumeClaim**, or PVC, is a request for storage.

Flow:

```text
Pod → PVC → PV → Storage
```

For Java backend applications, databases are usually managed outside the application cluster using services like AWS RDS.

---

### 3.14 Ingress

Ingress exposes HTTP/HTTPS traffic from outside the cluster to services inside the cluster.

Example:

```text
https://api.studywithdanish.com/users
        ↓
Ingress
        ↓
api-gateway-service
        ↓
user-service
```

Ingress is commonly used with:

```text
Nginx Ingress Controller
AWS ALB Ingress Controller
TLS/SSL certificate
Domain routing
```

---

### 3.15 Ingress Controller

Ingress is only a rule. To actually process traffic, we need an **Ingress Controller**.

Examples:

```text
Nginx Ingress Controller
AWS ALB Ingress Controller
Traefik
```

Interview line:

> Ingress defines routing rules, and Ingress Controller implements those rules.

---

### 3.16 Liveness Probe

A **Liveness Probe** checks whether the application is alive.

If liveness fails, Kubernetes restarts the container.

Example:

```text
/actuator/health/liveness
```

Problem it solves:

```text
Application is stuck
Thread deadlock
Container is running but app is not responding
```

---

### 3.17 Readiness Probe

A **Readiness Probe** checks whether the application is ready to receive traffic.

If readiness fails, Kubernetes removes the Pod from service traffic.

Example:

```text
/actuator/health/readiness
```

Problem it solves:

```text
Application started but DB connection is not ready
Kafka consumer is not initialized
Cache is not loaded
```

Important difference:

| Probe     | Purpose                             |
| --------- | ----------------------------------- |
| Liveness  | Should container be restarted?      |
| Readiness | Should traffic be sent to this Pod? |

---

### 3.18 Startup Probe

A **Startup Probe** is useful for applications that take longer to start.

Example:

```text
Large Spring Boot application
Heavy dependency initialization
Slow database connection
```

It prevents Kubernetes from killing the app too early.

---

### 3.19 Resource Requests and Limits

Kubernetes allows us to define CPU and memory requirements.

Example:

```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "1Gi"
    cpu: "500m"
```

Meaning:

| Field   | Meaning                  |
| ------- | ------------------------ |
| Request | Minimum resource needed  |
| Limit   | Maximum resource allowed |

This helps with scheduling, stability, and cost control.

---

### 3.20 Horizontal Pod Autoscaler

**HPA** automatically increases or decreases the number of Pods based on load.

Example:

```text
Minimum Pods: 2
Maximum Pods: 10
CPU threshold: 70%
```

If traffic increases, Kubernetes increases Pods.

If traffic decreases, Kubernetes reduces Pods.

---

### 3.21 Rolling Update

Rolling update deploys a new version gradually without downtime.

Example:

```text
v1 Pods running
Kubernetes starts v2 Pods
Waits until v2 is ready
Then removes v1 Pods gradually
```

This is very important for production deployment.

---

### 3.22 Rollback

If a deployment fails, Kubernetes can rollback to the previous stable version.

Command:

```bash
kubectl rollout undo deployment user-service
```

Interview answer:

> In production, if the new version has issues, we can rollback the Deployment to the previous ReplicaSet.

---

### 3.23 Labels and Selectors

Labels are key-value pairs attached to Kubernetes objects.

Example:

```yaml
labels:
  app: user-service
  env: prod
```

Selectors are used to find resources based on labels.

Example:

```yaml
selector:
  matchLabels:
    app: user-service
```

Service uses selector to find matching Pods.

---

### 3.24 Service Discovery

Kubernetes provides DNS-based service discovery.

Example:

```text
http://user-service:8080
http://post-service:8080
http://category-service:8080
```

In Spring Cloud microservices, if you already use Eureka, then Kubernetes can also provide service discovery at infrastructure level.

Interview point:

> In Kubernetes, internal service discovery is available using Service names and DNS. In some Spring Cloud projects, Eureka is still used for application-level discovery, but Kubernetes Service can also solve many discovery requirements.

---

### 3.25 API Gateway in Kubernetes

In your microservices project, API Gateway can be deployed as a normal Spring Boot service.

Flow:

```text
Client
 ↓
Ingress
 ↓
API Gateway
 ↓
user-service / post-service / category-service
```

Ingress handles external routing.

API Gateway handles application-level routing, authentication, rate limiting, and cross-cutting concerns.

---

### 3.26 StatefulSet

StatefulSet is used for stateful applications.

Examples:

```text
Kafka
MySQL
MongoDB
Elasticsearch
Redis cluster
```

For normal Spring Boot services, we usually use Deployment.

---

### 3.27 DaemonSet

DaemonSet ensures that one Pod runs on every node.

Used for infrastructure-level agents.

Examples:

```text
Logging agent
Monitoring agent
Security agent
Node exporter
```

---

### 3.28 Job and CronJob

**Job** runs a task once.

**CronJob** runs a task on schedule.

For Spring Batch:

```text
Spring Batch job can be deployed as Kubernetes Job or CronJob
```

Example:

```text
Daily report generation
Monthly data processing
Bulk file processing
```

---

### 3.29 Helm

Helm is a package manager for Kubernetes.

It helps manage complex Kubernetes YAML files using templates.

Instead of maintaining many duplicate YAML files, Helm allows:

```text
values-dev.yaml
values-qa.yaml
values-prod.yaml
```

---

### 3.30 kubectl

`kubectl` is the command-line tool used to interact with Kubernetes.

Common commands:

```bash
kubectl get pods
kubectl get svc
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- sh
kubectl apply -f deployment.yaml
kubectl rollout status deployment user-service
kubectl rollout undo deployment user-service
```

---

## 4. Interview-Focused Explanation

As a 7-year Java backend developer, you can explain Kubernetes like this:

> In my project, we use Spring Boot for building REST-based microservices. Each service is packaged as a Docker image and deployed into Kubernetes. Kubernetes manages the lifecycle of these services using Deployments, Pods, Services, ConfigMaps, Secrets, probes, and autoscaling. For example, user-service, post-service, and category-service run as separate Deployments. Each Deployment manages multiple Pods. Kubernetes Service provides stable internal communication between these services. ConfigMaps and Secrets externalize environment-specific configuration. Readiness and liveness probes are integrated with Spring Boot Actuator to ensure proper health checks. In production, Kubernetes helps us achieve scalability, zero-downtime deployment, rollback, and better availability.

This answer sounds practical because it connects:

```text
Spring Boot
Docker
Microservices
Kubernetes Deployment
Service Discovery
ConfigMap/Secret
Actuator Health Check
CI/CD
Production Deployment
```

---

## 5. Project-Based Explanation

Let’s connect Kubernetes with your **Spring Boot Blog Application** and planned **microservices version**.

### 5.1 Monolithic Blog Application

Current application:

```text
Spring Boot Blog App
 ├── Users
 ├── Posts
 ├── Categories
 ├── Comments
 ├── JWT Security
 ├── MySQL
 └── React frontend
```

Deployment in Kubernetes:

```text
React frontend → frontend Deployment
Spring Boot backend → backend Deployment
MySQL → external DB/RDS
```

Kubernetes objects:

```text
blog-backend-deployment.yaml
blog-backend-service.yaml
blog-configmap.yaml
blog-secret.yaml
blog-ingress.yaml
```

How to explain:

> In my blog application, the Spring Boot backend can be containerized and deployed as a Kubernetes Deployment. The backend Pods are exposed internally using a Service. Environment-specific values like database URL and active profile can be managed using ConfigMap, while database password and JWT secret can be managed using Secret.

---

### 5.2 Microservices Version

Planned microservices:

```text
api-gateway
eureka-server
user-service
post-service
category-service
file-service
```

Kubernetes deployment:

```text
Client
 ↓
Ingress
 ↓
api-gateway-service
 ↓
user-service
post-service
category-service
file-service
 ↓
MySQL/RDS/S3
```

Each microservice has:

```text
Deployment
Service
ConfigMap
Secret
Health probes
Resource limits
HPA
```

---

### 5.3 API Gateway

Where used:

```text
Single entry point for frontend/client
```

Why needed:

```text
Central routing
Authentication
Rate limiting
Logging
Request filtering
```

Kubernetes mapping:

```text
Ingress → API Gateway Service → API Gateway Pods
```

Interview explanation:

> In Kubernetes, I would expose only the API Gateway externally using Ingress. Internal microservices like user-service and post-service would remain private using ClusterIP services.

---

### 5.4 User Service

Where used:

```text
User registration
Login
Profile management
JWT authentication
```

Kubernetes objects:

```text
user-service Deployment
user-service ClusterIP Service
ConfigMap for profile/config
Secret for DB password/JWT secret
```

Problem solved:

```text
Independent deployment
Independent scaling
Failure isolation
```

Interview explanation:

> If user-service has high traffic during login, we can scale only user-service Pods without scaling the whole application.

---

### 5.5 Post Service

Where used:

```text
Create post
Update post
Get posts with pagination and sorting
```

Kubernetes benefit:

```text
Scale post-service separately
Rolling updates for post-service only
Service discovery through post-service DNS
```

Interview explanation:

> In my blog project, post-service may receive more read traffic. Kubernetes allows us to run multiple replicas and use Service-based load balancing.

---

### 5.6 Category Service

Where used:

```text
Manage blog categories
Fetch posts by category
```

Kubernetes benefit:

```text
Small independent service
Can be deployed separately
Can have separate resource limits
```

---

### 5.7 ConfigMap in Your Project

Example values:

```text
SPRING_PROFILES_ACTIVE=prod
SERVER_PORT=8080
EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka
```

Why needed:

```text
Avoid hardcoding
Environment-specific configuration
Easy update without rebuilding image
```

Interview explanation:

> We use ConfigMap to externalize normal configuration like active profile, service URLs, and logging level.

---

### 5.8 Secret in Your Project

Example values:

```text
DB_USERNAME
DB_PASSWORD
JWT_SECRET
AWS_ACCESS_KEY
```

Why needed:

```text
Sensitive data should not be stored in code or plain config files
```

Interview explanation:

> For sensitive values like database credentials and JWT secret, we use Kubernetes Secrets and inject them as environment variables into Spring Boot containers.

---

### 5.9 Probes with Spring Boot Actuator

Spring Boot Actuator endpoints:

```text
/actuator/health
/actuator/health/liveness
/actuator/health/readiness
```

Why needed:

```text
Kubernetes can check whether app is alive and ready
```

Interview explanation:

> We integrate Kubernetes probes with Spring Boot Actuator. Readiness ensures traffic is sent only when the application is ready. Liveness restarts the container if the application becomes unhealthy.

---

### 5.10 CI/CD with Jenkins

Flow:

```text
Developer commits code
 ↓
Jenkins pipeline starts
 ↓
Maven build
 ↓
Run tests
 ↓
Build Docker image
 ↓
Push image to Docker Hub/ECR
 ↓
kubectl apply / Helm upgrade
 ↓
Kubernetes rolling update
```

Interview explanation:

> In CI/CD, Jenkins builds the Spring Boot JAR, creates a Docker image, pushes it to registry, and updates the Kubernetes Deployment. Kubernetes then performs rolling deployment.

---

## 6. Important Annotations / Classes / Interfaces / Configuration

Kubernetes itself does not use Java annotations, but Spring Boot applications need specific configurations to work properly in Kubernetes.

### 6.1 Spring Boot Actuator Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Used for health endpoints.

---

### 6.2 application.yml for Kubernetes Health Probes

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
  endpoint:
    health:
      probes:
        enabled: true
      show-details: always
  health:
    livenessstate:
      enabled: true
    readinessstate:
      enabled: true
```

This enables:

```text
/actuator/health/liveness
/actuator/health/readiness
```

---

### 6.3 Spring Profile Configuration

```yaml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}

  datasource:
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

Kubernetes injects environment variables.

---

### 6.4 Dockerfile for Spring Boot

```dockerfile
FROM eclipse-temurin:17-jdk-alpine

WORKDIR /app

COPY target/blog-service.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Interview explanation:

> We create a Docker image for each Spring Boot service using a Dockerfile. This image is deployed into Kubernetes Pods.

---

### 6.5 ConfigMap YAML

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: blog-config
data:
  SPRING_PROFILES_ACTIVE: "prod"
  SERVER_PORT: "8080"
  LOG_LEVEL: "INFO"
```

---

### 6.6 Secret YAML

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: blog-secret
type: Opaque
stringData:
  DB_USERNAME: "root"
  DB_PASSWORD: "password"
  JWT_SECRET: "my-secret-key"
```

In real production, secrets should be managed carefully using secure secret managers.

---

### 6.7 Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blog-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: blog-backend
  template:
    metadata:
      labels:
        app: blog-backend
    spec:
      containers:
        - name: blog-backend
          image: danishkhan141/blog-backend:1.0.0
          ports:
            - containerPort: 8080

          envFrom:
            - configMapRef:
                name: blog-config
            - secretRef:
                name: blog-secret

          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10

          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            initialDelaySeconds: 60
            periodSeconds: 15

          resources:
            requests:
              memory: "512Mi"
              cpu: "250m"
            limits:
              memory: "1Gi"
              cpu: "500m"
```

---

### 6.8 Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: blog-backend-service
spec:
  type: ClusterIP
  selector:
    app: blog-backend
  ports:
    - port: 8080
      targetPort: 8080
```

Other services can call:

```text
http://blog-backend-service:8080
```

---

### 6.9 Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: blog-ingress
spec:
  rules:
    - host: api.studywithdanish.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: blog-backend-service
                port:
                  number: 8080
```

---

### 6.10 HPA YAML

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: blog-backend-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: blog-backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

---

## 7. Code Examples

### 7.1 Spring Boot Health Controller

Usually Actuator is enough, but sometimes projects create a custom health endpoint.

```java
@RestController
@RequestMapping("/api/health")
public class HealthController {

    @GetMapping
    public ResponseEntity<String> health() {
        return ResponseEntity.ok("Blog service is running");
    }
}
```

However, for Kubernetes, prefer Actuator:

```text
/actuator/health/readiness
/actuator/health/liveness
```

---

### 7.2 Reading ConfigMap and Secret Values in Spring Boot

```java
@Component
public class AppConfigReader {

    @Value("${spring.profiles.active}")
    private String activeProfile;

    @Value("${jwt.secret}")
    private String jwtSecret;

    public void printConfig() {
        System.out.println("Active profile: " + activeProfile);
    }
}
```

application.yml:

```yaml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}

jwt:
  secret: ${JWT_SECRET}
```

---

### 7.3 Database Configuration Using Environment Variables

```yaml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}

  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false
```

Kubernetes injects:

```yaml
env:
  - name: DB_URL
    value: jdbc:mysql://mysql-service:3306/blogdb

  - name: DB_USERNAME
    valueFrom:
      secretKeyRef:
        name: blog-secret
        key: DB_USERNAME

  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: blog-secret
        key: DB_PASSWORD
```

---

### 7.4 Graceful Shutdown in Spring Boot

Important during rolling deployment.

```yaml
server:
  shutdown: graceful

spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s
```

Why needed:

> During rolling update, Kubernetes may terminate old Pods. Graceful shutdown allows ongoing requests to complete before the container stops.

---

### 7.5 Spring Boot Application Name for Microservices

```yaml
spring:
  application:
    name: user-service
```

In Kubernetes:

```yaml
metadata:
  name: user-service
```

Service DNS:

```text
http://user-service:8080
```

---

## 8. Common Real-Time Scenarios

### Scenario 1: Pod is crashing repeatedly

Command:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

Common reasons:

```text
Wrong DB credentials
Wrong image name
Missing environment variables
Application startup failure
Port mismatch
Memory limit exceeded
```

Interview answer:

> First I check Pod status, then describe the Pod for events, then check application logs using kubectl logs. For Spring Boot, I verify profile, DB connection, secrets, and Actuator health.

---

### Scenario 2: Application is running but not accessible

Check:

```bash
kubectl get svc
kubectl get ingress
kubectl describe ingress <ingress-name>
```

Common reasons:

```text
Service selector mismatch
Wrong targetPort
Ingress rule issue
Application not ready
Firewall/security group issue
```

---

### Scenario 3: Service cannot call another service

Check:

```bash
kubectl exec -it <pod-name> -- sh
curl http://user-service:8080/actuator/health
```

Common reasons:

```text
Wrong service name
Different namespace
Service not created
Network policy blocking traffic
Application port mismatch
```

---

### Scenario 4: New deployment failed

Check:

```bash
kubectl rollout status deployment user-service
kubectl describe deployment user-service
kubectl logs <new-pod>
```

Rollback:

```bash
kubectl rollout undo deployment user-service
```

---

### Scenario 5: Memory issue

Symptoms:

```text
OOMKilled
Pod restart
Slow response
```

Check:

```bash
kubectl describe pod <pod-name>
kubectl top pod
```

Fix:

```text
Increase memory limit
Tune JVM options
Check memory leak
Set proper -Xms and -Xmx
```

Example:

```yaml
env:
  - name: JAVA_TOOL_OPTIONS
    value: "-Xms256m -Xmx768m"
```

---

### Scenario 6: Readiness probe failing

Common reasons:

```text
DB is down
Application startup is slow
Wrong actuator endpoint
Security blocking actuator endpoint
```

Fix:

```text
Expose health endpoint
Allow actuator health without authentication
Increase initialDelaySeconds
Check DB connectivity
```

---

### Scenario 7: Rolling deployment with zero downtime

Best practices:

```text
Use readiness probe
Use multiple replicas
Use graceful shutdown
Set maxUnavailable and maxSurge
```

Example:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

---

### Scenario 8: JWT Secret changed accidentally

Problem:

```text
Existing tokens become invalid
Users get unauthorized errors
```

Best practice:

```text
Manage JWT secret using Secret
Avoid accidental changes
Use proper secret rotation strategy
```

---

### Scenario 9: ConfigMap changed but app did not pick changes

Reason:

```text
Environment variables from ConfigMap are loaded at container startup
```

Fix:

```text
Restart Pods
Use rolling restart
```

Command:

```bash
kubectl rollout restart deployment blog-backend
```

---

### Scenario 10: Logs in Kubernetes

Command:

```bash
kubectl logs <pod-name>
kubectl logs -f <pod-name>
kubectl logs <pod-name> --previous
```

Production tools:

```text
ELK
EFK
CloudWatch
Prometheus
Grafana
Loki
```

---

## 9. Beginner to Experienced-Level Interview Questions and Answers

### Basic Level

#### 1. What is Kubernetes?

Kubernetes is a container orchestration platform used to deploy, manage, scale, and monitor containerized applications. In Java microservices, we use it to run Spring Boot services as Docker containers in a production-ready way.

---

#### 2. What problem does Kubernetes solve?

It solves problems like container scheduling, service discovery, scaling, load balancing, self-healing, configuration management, secret management, rolling deployment, and rollback.

---

#### 3. What is a Pod?

A Pod is the smallest deployable unit in Kubernetes. It contains one or more containers. In Spring Boot projects, usually one Pod contains one Spring Boot application container.

---

#### 4. What is a Node?

A Node is a worker machine where Kubernetes runs Pods. It can be a VM or physical server.

---

#### 5. What is a Cluster?

A Cluster is a group of nodes managed by Kubernetes. It contains the control plane and worker nodes.

---

#### 6. What is a Deployment?

A Deployment manages Pods and ReplicaSets. It ensures that the desired number of replicas are running and supports rolling updates and rollbacks.

---

#### 7. What is a ReplicaSet?

ReplicaSet ensures that a specific number of Pod replicas are running. Usually, Deployment creates and manages ReplicaSet automatically.

---

#### 8. What is a Service in Kubernetes?

A Service provides a stable endpoint to access Pods. Since Pod IPs can change, Service gives a stable DNS name and load balances traffic to matching Pods.

---

#### 9. What is ClusterIP?

ClusterIP is the default Service type used for internal communication inside the Kubernetes cluster.

---

#### 10. What is NodePort?

NodePort exposes a Service on a port of each node. It is commonly used for testing or local Kubernetes environments.

---

#### 11. What is LoadBalancer Service?

LoadBalancer exposes a Kubernetes Service externally using a cloud provider load balancer, such as AWS ELB or ALB.

---

#### 12. What is Namespace?

Namespace is used to logically separate Kubernetes resources. For example, we can have dev, qa, and prod namespaces.

---

#### 13. What is ConfigMap?

ConfigMap stores non-sensitive configuration such as active profile, service URLs, logging level, and feature flags.

---

#### 14. What is Secret?

Secret stores sensitive data like database password, JWT secret, API keys, and tokens.

---

#### 15. What is Ingress?

Ingress manages external HTTP/HTTPS access to services inside the cluster. It is commonly used for domain-based and path-based routing.

---

### Intermediate Level

#### 16. Difference between Pod and Container?

A container runs the application process. A Pod is a Kubernetes object that wraps one or more containers and provides networking and lifecycle management.

---

#### 17. Difference between Deployment and Pod?

A Pod runs the container, but it is temporary. Deployment manages Pods, ensures replicas are running, and supports rollout and rollback.

---

#### 18. Difference between ConfigMap and Secret?

ConfigMap stores normal configuration. Secret stores sensitive configuration. For example, `SPRING_PROFILES_ACTIVE` can go into ConfigMap, while `DB_PASSWORD` should go into Secret.

---

#### 19. Difference between Liveness and Readiness Probe?

Liveness checks whether the application should be restarted. Readiness checks whether the application is ready to receive traffic.

---

#### 20. Why are readiness probes important?

Readiness probes prevent traffic from going to a Pod that has started but is not yet ready. This avoids failed requests during startup or deployment.

---

#### 21. How does Kubernetes provide service discovery?

Kubernetes provides DNS-based service discovery. A service can call another service using its Kubernetes Service name, such as `http://user-service:8080`.

---

#### 22. What happens if a Pod crashes?

Kubernetes automatically restarts or recreates the Pod through Deployment/ReplicaSet, depending on the configuration.

---

#### 23. How does rolling update work?

Kubernetes gradually creates new version Pods and removes old version Pods only after new Pods become ready. This helps achieve zero-downtime deployment.

---

#### 24. How do you rollback a failed deployment?

Using:

```bash
kubectl rollout undo deployment <deployment-name>
```

Kubernetes rolls back to the previous ReplicaSet.

---

#### 25. What is HPA?

Horizontal Pod Autoscaler automatically scales the number of Pods based on metrics like CPU or memory usage.

---

#### 26. What are resource requests and limits?

Requests define minimum required CPU/memory. Limits define maximum allowed CPU/memory. They help Kubernetes schedule Pods and prevent one service from consuming too many resources.

---

#### 27. What is the role of labels and selectors?

Labels identify Kubernetes resources. Selectors are used by Services and Deployments to find matching Pods.

---

#### 28. How does a Service know which Pods to route traffic to?

Service uses selectors. If the Pod labels match the Service selector, the Service routes traffic to those Pods.

---

#### 29. What is a rolling restart?

A rolling restart restarts Pods gradually without changing the image version.

Command:

```bash
kubectl rollout restart deployment <deployment-name>
```

---

#### 30. What is Helm?

Helm is a package manager for Kubernetes. It helps manage Kubernetes YAML files using reusable templates and environment-specific values.

---

### Experienced Level

#### 31. How would you deploy a Spring Boot microservice in Kubernetes?

I would build the Spring Boot JAR, create a Docker image, push it to a registry, create Kubernetes Deployment and Service YAML files, configure ConfigMap and Secret, add readiness/liveness probes using Actuator, define resource limits, and deploy using Jenkins or Helm.

---

#### 32. How do you handle environment-specific configuration?

We externalize configuration using ConfigMaps and Secrets. For example, dev, qa, and prod can have different database URLs, active profiles, and logging levels without rebuilding the Docker image.

---

#### 33. How do you secure sensitive data in Kubernetes?

Sensitive values like DB password, JWT secret, and API keys should be stored in Kubernetes Secrets or external secret managers. They should not be committed into Git.

---

#### 34. How do you achieve zero-downtime deployment?

Use multiple replicas, readiness probes, rolling update strategy, graceful shutdown, and proper resource limits. Kubernetes only sends traffic to ready Pods.

---

#### 35. How do you debug a failing Pod?

I check:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

Then I verify image, environment variables, secrets, DB connectivity, ports, probes, and memory issues.

---

#### 36. How do you tune JVM memory in Kubernetes?

Set container memory limits and configure JVM heap using `JAVA_TOOL_OPTIONS`, such as:

```text
-Xms256m -Xmx768m
```

This prevents the JVM from consuming more memory than the container limit.

---

#### 37. What is OOMKilled?

OOMKilled means the container used more memory than its configured limit, so Kubernetes killed it. To fix it, we check memory usage, tune JVM heap, increase memory limit, or fix memory leaks.

---

#### 38. How do you expose only API Gateway externally?

Use Ingress to expose API Gateway. Keep internal services as ClusterIP. External clients call API Gateway, and API Gateway routes requests to internal microservices.

---

#### 39. How does Kubernetes fit with Eureka?

Kubernetes already provides service discovery using Services and DNS. Eureka is application-level discovery. In some Spring Cloud projects, Eureka is still used, but in Kubernetes-native architecture, Kubernetes Service discovery can replace many Eureka use cases.

---

#### 40. How do you manage logs in Kubernetes?

For basic debugging, use `kubectl logs`. In production, logs are usually centralized using tools like ELK, EFK, Loki, Grafana, or CloudWatch.

---

### Project-Based Questions

#### 41. How would you deploy your Spring Boot Blog Application on Kubernetes?

I would create a Docker image for the blog backend, deploy it using Deployment, expose it using Service, configure database and JWT values using ConfigMap and Secret, expose the backend through Ingress or API Gateway, and use Actuator endpoints for health checks.

---

#### 42. How would you deploy your React frontend with Spring Boot backend?

React frontend can be containerized separately and deployed as a frontend Deployment and Service. Backend runs as a separate Spring Boot Deployment. Ingress can route frontend traffic to React and API traffic to backend/API Gateway.

---

#### 43. How would you deploy user-service, post-service, and category-service?

Each service would have its own Docker image, Deployment, Service, ConfigMap, Secret, and health probes. API Gateway routes traffic to them internally using Kubernetes service names.

---

#### 44. How would post-service call user-service in Kubernetes?

post-service can call user-service using Kubernetes DNS:

```text
http://user-service:8080
```

The Service load balances traffic to available user-service Pods.

---

#### 45. Where would you store JWT secret in Kubernetes?

JWT secret should be stored in Kubernetes Secret or a secure external secret manager. Spring Boot can read it using environment variables.

---

#### 46. How would you handle database connection in Kubernetes?

The database URL, username, and password should be externalized. URL can be stored in ConfigMap, while username and password should be stored in Secret. In production, database can be AWS RDS.

---

#### 47. How would you handle file upload in Kubernetes?

I would avoid storing uploaded files inside Pods because Pods are temporary. I would store files in AWS S3 or persistent storage and keep only file metadata in the database.

---

#### 48. How would you scale only post-service?

I would increase replicas for post-service Deployment manually or configure HPA based on CPU/memory/custom metrics.

Example:

```bash
kubectl scale deployment post-service --replicas=5
```

---

#### 49. How would you deploy Spring Batch in Kubernetes?

For scheduled batch processing, I can use Kubernetes CronJob. For one-time processing, I can use Kubernetes Job. The Spring Batch application runs as a container and exits after job completion.

---

#### 50. How would Jenkins deploy your application to Kubernetes?

Jenkins builds the Spring Boot project, runs tests, creates Docker image, pushes image to Docker registry, then updates Kubernetes Deployment using `kubectl apply`, `kubectl set image`, or Helm upgrade.

---

### Scenario-Based Questions

#### 51. Pod is running, but API is not working. What will you check?

I will check Service mapping, targetPort, containerPort, readiness probe, application logs, Spring profile, DB connection, and Ingress routing.

---

#### 52. Pod is in CrashLoopBackOff. What does it mean?

It means the container is starting and crashing repeatedly. I will check logs, environment variables, secrets, DB connectivity, startup exceptions, and memory limits.

---

#### 53. Readiness probe is failing. What can be the reason?

Application may not be fully started, DB may be unavailable, Actuator endpoint may be secured, wrong probe path may be configured, or startup delay may be too short.

---

#### 54. Deployment is successful but old version is still running. What will you check?

I will check rollout status, image tag, imagePullPolicy, ReplicaSet, Pod image version, and whether the new Pods are ready.

---

#### 55. New version has bugs. What will you do?

I will rollback the deployment:

```bash
kubectl rollout undo deployment <deployment-name>
```

Then check logs and deployment history.

---

#### 56. Application is slow in Kubernetes. What will you check?

I will check CPU/memory usage, JVM heap, DB latency, API response time, HPA configuration, resource limits, thread pool, connection pool, and logs.

---

#### 57. Service A cannot connect to Service B. What will you check?

I will check Service name, namespace, DNS resolution, target port, Pod readiness, NetworkPolicy, and application logs.

---

#### 58. How do you prevent downtime during deployment?

Use rolling updates, readiness probes, multiple replicas, graceful shutdown, and proper deployment strategy with `maxUnavailable: 0`.

---

#### 59. How do you handle secrets across environments?

Each environment should have its own Secret. Dev, QA, and Prod credentials should be separate. Secrets should not be committed to Git.

---

#### 60. What is the best deployment unit for a Spring Boot microservice?

A Deployment is the best deployment unit for stateless Spring Boot microservices. For batch jobs, use Job or CronJob. For stateful systems like Kafka or databases, use StatefulSet.

---

## 10. Common Mistakes

### Mistake 1: Storing files inside Pods

Pods are temporary. Files can be lost when Pods restart.

Better approach:

```text
Use AWS S3, EFS, or persistent volume
```

---

### Mistake 2: Hardcoding configuration

Bad:

```yaml
spring.datasource.password=root123
```

Better:

```text
Use ConfigMap and Secret
```

---

### Mistake 3: Not using readiness probe

Without readiness probe, traffic may go to a Pod before it is ready.

---

### Mistake 4: Using liveness probe incorrectly

If liveness probe is too aggressive, Kubernetes may restart healthy but slow-starting applications.

---

### Mistake 5: Not setting resource limits

Without limits, one service can consume too many resources and affect others.

---

### Mistake 6: Using latest image tag in production

Bad:

```yaml
image: blog-backend:latest
```

Better:

```yaml
image: blog-backend:1.0.5
```

---

### Mistake 7: Exposing all services externally

Only API Gateway or required public services should be exposed externally. Internal services should use ClusterIP.

---

### Mistake 8: Not checking namespace

Many debugging mistakes happen because the developer checks the wrong namespace.

---

### Mistake 9: Keeping secrets in Git

Secrets should not be committed to GitHub.

---

### Mistake 10: Not using graceful shutdown

Without graceful shutdown, active requests may fail during deployment.

---

## 11. Best Practices

### 11.1 Use one container per microservice Pod

For normal Spring Boot services, keep one main application container per Pod.

---

### 11.2 Use ConfigMap and Secret

Use ConfigMap for normal configuration and Secret for sensitive data.

---

### 11.3 Use Actuator health checks

Expose:

```text
/actuator/health/liveness
/actuator/health/readiness
```

---

### 11.4 Keep services stateless

Spring Boot microservices should be stateless. Store data in external systems like MySQL, Redis, Kafka, or S3.

---

### 11.5 Use proper image tags

Use versioned image tags:

```text
user-service:1.0.3
post-service:1.2.0
```

Avoid `latest` in production.

---

### 11.6 Use resource requests and limits

Always define CPU and memory requests/limits.

---

### 11.7 Use rolling update strategy

For production, use rolling updates with readiness probes.

---

### 11.8 Use namespaces

Separate environments:

```text
dev
qa
stage
prod
```

---

### 11.9 Use HPA for high-traffic services

Scale services based on CPU/memory or custom metrics.

---

### 11.10 Use centralized logging and monitoring

Use tools like:

```text
Prometheus
Grafana
ELK
Loki
CloudWatch
```

---

### 11.11 Use CI/CD

Use Jenkins pipeline for:

```text
Build
Test
Docker image creation
Image push
Kubernetes deployment
```

---

### 11.12 Secure external access

Expose only necessary services. Use Ingress, TLS, authentication, and network policies.

---

## 12. How to Explain in Interview

### Answer 1: General Kubernetes Experience

> As per my project experience, Kubernetes is used to deploy and manage containerized Spring Boot applications. We package each service as a Docker image and deploy it using Kubernetes Deployment. Kubernetes manages Pods, scaling, self-healing, service discovery, rolling updates, and rollback. For configuration, we use ConfigMaps and Secrets, and for health checks we integrate Spring Boot Actuator with liveness and readiness probes.

---

### Answer 2: Spring Boot Microservices Deployment

> In our Spring Boot microservices architecture, each service like user-service, post-service, and category-service can run as an independent Kubernetes Deployment. Each service has its own Pods and is exposed internally using a Kubernetes Service. API Gateway is exposed externally through Ingress, while internal services remain private using ClusterIP.

---

### Answer 3: ConfigMap and Secret

> In production, we usually do not hardcode environment-specific values in the application. We use ConfigMap for non-sensitive values like active profile, service URLs, and logging level. For sensitive values like DB password, JWT secret, and API keys, we use Kubernetes Secret. Spring Boot reads these values as environment variables.

---

### Answer 4: Health Checks

> In our Spring Boot application, we can use Actuator health endpoints for Kubernetes probes. Readiness probe tells Kubernetes whether the application is ready to receive traffic, while liveness probe tells whether the container should be restarted. This is very useful during startup, deployment, and failure scenarios.

---

### Answer 5: Zero-Downtime Deployment

> In microservices architecture, Kubernetes helps achieve zero-downtime deployment using rolling updates. During deployment, Kubernetes creates new version Pods, waits until they are ready, and then gradually removes old Pods. With readiness probes and multiple replicas, users do not experience downtime.

---

### Answer 6: Debugging Pod Issues

> In production, if a Pod fails, I first check the Pod status using `kubectl get pods`, then use `kubectl describe pod` to check events, and `kubectl logs` to check application logs. For Spring Boot services, I verify profile, database connection, environment variables, secrets, port mapping, and health endpoints.

---

### Answer 7: Service Discovery

> In Kubernetes, services communicate using Kubernetes Service names. For example, post-service can call user-service using `http://user-service:8080`. Kubernetes DNS resolves the service name and load balances traffic across healthy Pods.

---

### Answer 8: Scaling

> In our microservices setup, each service can be scaled independently. For example, if post-service receives more traffic, we can increase only post-service replicas or configure HPA to scale automatically based on CPU or memory usage.

---

### Answer 9: API Gateway and Ingress

> In production, we usually expose only API Gateway externally. Ingress routes external traffic to API Gateway, and API Gateway routes requests to internal microservices. This keeps internal services secure and avoids exposing every service to the internet.

---

### Answer 10: Spring Batch on Kubernetes

> For Spring Batch workloads, Kubernetes Job or CronJob can be used. If the batch runs once, we can use Job. If it runs on a schedule, like daily report generation or file processing, we can use CronJob. The batch application runs as a container and exits after completion.

---

## 13. Revision Notes

### Kubernetes One-Line Definition

```text
Kubernetes is a container orchestration platform used to deploy, scale, manage, and monitor containerized applications.
```

### Most Important Objects

```text
Pod
Deployment
ReplicaSet
Service
ConfigMap
Secret
Ingress
Namespace
Volume
HPA
Job
CronJob
StatefulSet
DaemonSet
```

### Spring Boot Deployment Flow

```text
Spring Boot Code
 → Maven Build
 → Docker Image
 → Docker Registry
 → Kubernetes Deployment
 → Pod
 → Service
 → Ingress/API Gateway
```

### Pod vs Deployment

```text
Pod runs container.
Deployment manages Pods.
```

### Service Purpose

```text
Service provides stable endpoint and load balancing for Pods.
```

### ConfigMap vs Secret

```text
ConfigMap = normal configuration
Secret = sensitive configuration
```

### Liveness vs Readiness

```text
Liveness = should Kubernetes restart the container?
Readiness = should Kubernetes send traffic to the Pod?
```

### Deployment Strategy

```text
Rolling update = deploy new version gradually without downtime
Rollback = return to previous stable version
```

### Production Debug Commands

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl get svc
kubectl get ingress
kubectl rollout status deployment <deployment-name>
kubectl rollout undo deployment <deployment-name>
```

### For Your Project

```text
React frontend → frontend Deployment
API Gateway → exposed through Ingress
user-service → Deployment + ClusterIP Service
post-service → Deployment + ClusterIP Service
category-service → Deployment + ClusterIP Service
DB credentials → Secret
Application profile → ConfigMap
Health checks → Spring Boot Actuator
Scaling → replicas/HPA
CI/CD → Jenkins + Docker + Kubernetes
```

### Strong Interview Closing Line

> Kubernetes is important in Java microservices because it allows us to run Spring Boot services in a scalable, self-healing, and production-ready environment. It provides deployment automation, service discovery, configuration management, secret management, health checks, autoscaling, rolling updates, and rollback support.
