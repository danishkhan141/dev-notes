Below are **Top 50 Kubernetes interview questions** prepared in a clean, professional, Java/Spring Boot backend-oriented way. Kubernetes is used to automate deployment, scaling, and management of containerized applications. Official Kubernetes docs describe a cluster as a combination of a **control plane** and one or more **worker nodes**. ([Kubernetes][1])

---

# Kubernetes Big Picture

Think like this:

**Docker** packages your application into a container.
**Kubernetes** runs, scales, monitors, restarts, exposes, and manages those containers in production.

For a Java Spring Boot application:

```text
Spring Boot App
      ↓
Docker Image
      ↓
Kubernetes Deployment
      ↓
Pods
      ↓
Service
      ↓
Ingress / LoadBalancer
      ↓
Users
```

---

# 1. What is Kubernetes?

**Answer:**
Kubernetes is an open-source container orchestration platform. It helps deploy, scale, expose, restart, and manage containerized applications.

**Example:**
You have a Spring Boot application packaged as a Docker image. Instead of manually running containers on servers, Kubernetes can automatically run 3 replicas, restart failed containers, expose the application, and scale it during high traffic.

---

# 2. Why do we need Kubernetes if Docker already exists?

**Answer:**
Docker creates and runs containers. Kubernetes manages containers at scale.

Docker answers:

```text
How do I package and run my app?
```

Kubernetes answers:

```text
How do I run this app reliably in production?
How do I scale it?
How do I restart it if it fails?
How do I expose it?
How do I roll out a new version safely?
```

---

# 3. What is a Kubernetes cluster?

**Answer:**
A Kubernetes cluster is a group of machines called **nodes**. It has:

```text
Control Plane → manages the cluster
Worker Nodes → run application Pods
```

The control plane manages scheduling, cluster state, API requests, and controllers. Worker nodes run your actual application containers. ([Kubernetes][2])

---

# 4. What are the main Kubernetes control plane components?

**Answer:**

```text
kube-apiserver
etcd
kube-scheduler
kube-controller-manager
cloud-controller-manager
```

**Explanation:**

| Component                | Purpose                                     |
| ------------------------ | ------------------------------------------- |
| kube-apiserver           | Entry point for all Kubernetes API requests |
| etcd                     | Stores cluster state                        |
| kube-scheduler           | Decides which node should run a Pod         |
| kube-controller-manager  | Ensures desired state matches actual state  |
| cloud-controller-manager | Integrates with cloud provider services     |

---

# 5. What are the main worker node components?

**Answer:**

```text
kubelet
kube-proxy
container runtime
```

**Explanation:**

| Component         | Purpose                                         |
| ----------------- | ----------------------------------------------- |
| kubelet           | Talks to control plane and manages Pods on node |
| kube-proxy        | Handles networking and service routing          |
| container runtime | Runs containers, for example containerd         |

---

# 6. What is a Pod?

**Answer:**
A Pod is the smallest deployable unit in Kubernetes. It usually contains one container, but it can contain multiple tightly coupled containers.

**Example:**

```text
Pod
 └── Spring Boot container
```

A Pod has its own IP address and shared network/storage context for containers inside it. Kubernetes API docs define a Pod as a collection of containers that can run on a host. ([Kubernetes][3])

---

# 7. Pod vs Container?

**Answer:**

| Pod                                | Container                    |
| ---------------------------------- | ---------------------------- |
| Kubernetes object                  | Runtime unit                 |
| Can contain one or more containers | Runs one application process |
| Has IP address                     | Shares Pod network           |
| Managed by Kubernetes              | Managed by container runtime |

**Interview line:**
A container runs the application, but Kubernetes manages Pods, not individual containers directly in normal application deployment.

---

# 8. Why should we not usually create Pods directly?

**Answer:**
Because if a directly created Pod dies, Kubernetes will not automatically recreate it.

In production, we use higher-level objects like:

```text
Deployment
StatefulSet
DaemonSet
Job
CronJob
```

For stateless Java applications, we usually use **Deployment**.

---

# 9. What is a Deployment?

**Answer:**
A Deployment manages stateless application Pods. It provides declarative updates, scaling, rolling updates, and rollback support. Kubernetes docs describe Deployment as an object that manages Pods and ReplicaSets for stateless workloads. ([Kubernetes][4])

**Example:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blog-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: blog-service
  template:
    metadata:
      labels:
        app: blog-service
    spec:
      containers:
        - name: blog-service
          image: danish/blog-service:1.0
          ports:
            - containerPort: 8080
```

---

# 10. What is ReplicaSet?

**Answer:**
ReplicaSet ensures a required number of Pod replicas are always running.

Example:

```text
Desired replicas = 3
Actual replicas = 2
ReplicaSet creates 1 more Pod
```

In real projects, we normally do not create ReplicaSet directly. Deployment creates and manages ReplicaSets internally.

---

# 11. Deployment vs ReplicaSet?

**Answer:**

| Deployment                  | ReplicaSet                    |
| --------------------------- | ----------------------------- |
| Higher-level object         | Lower-level object            |
| Supports rollout/rollback   | Maintains replica count       |
| Manages ReplicaSet          | Manages Pods                  |
| Used directly in production | Usually created by Deployment |

**Interview line:**
Deployment is preferred because it gives version management, rolling updates, and rollback.

---

# 12. What is a Namespace?

**Answer:**
Namespace is a logical partition inside a Kubernetes cluster.

**Example:**

```text
dev namespace
qa namespace
prod namespace
```

Benefits:

```text
Resource separation
Access control
Environment separation
Quota management
```

Command:

```bash
kubectl get pods -n dev
```

---

# 13. What are Labels and Selectors?

**Answer:**
Labels are key-value pairs attached to Kubernetes objects. Selectors are used to find objects based on labels.

**Example:**

```yaml
labels:
  app: blog-service
  env: prod
```

A Service uses selector to find Pods:

```yaml
selector:
  app: blog-service
```

**Interview line:**
Labels and selectors are the backbone of Kubernetes object linking.

---

# 14. What is a Kubernetes Service?

**Answer:**
A Service provides a stable network endpoint to access Pods.

Pods are temporary. Their IPs can change. Service solves this by giving a stable DNS name/IP.

```text
User / App → Service → Pods
```

Kubernetes networking docs describe Service as a way to expose an application behind a single outward-facing endpoint. ([Kubernetes][5])

---

# 15. What are different types of Services?

**Answer:**

| Service Type | Purpose                               |
| ------------ | ------------------------------------- |
| ClusterIP    | Internal access inside cluster        |
| NodePort     | Exposes app on node IP and port       |
| LoadBalancer | Exposes app using cloud load balancer |
| ExternalName | Maps service to external DNS name     |

**Example:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: blog-service
spec:
  type: ClusterIP
  selector:
    app: blog-service
  ports:
    - port: 80
      targetPort: 8080
```

---

# 16. What is ClusterIP?

**Answer:**
ClusterIP is the default Service type. It exposes the application only inside the Kubernetes cluster.

**Example:**
Your `post-service` can call `user-service` internally using:

```text
http://user-service:8080
```

Use ClusterIP for internal microservice communication.

---

# 17. What is NodePort?

**Answer:**
NodePort exposes the application on a port of each worker node.

Example:

```text
http://node-ip:30080
```

It is mostly used for testing or small setups, not preferred for serious production traffic.

---

# 18. What is LoadBalancer Service?

**Answer:**
LoadBalancer exposes the application externally using a cloud provider load balancer.

Example on AWS:

```text
AWS Load Balancer → Kubernetes Service → Pods
```

This is commonly used in cloud environments.

---

# 19. What is Ingress?

**Answer:**
Ingress manages HTTP/HTTPS routing into the cluster.

Instead of creating multiple LoadBalancers for multiple services, we use Ingress.

```text
api.example.com/users → user-service
api.example.com/posts → post-service
```

Kubernetes docs describe Ingress as protocol-aware HTTP/HTTPS routing using hostnames, paths, and URIs. ([Kubernetes][5])

---

# 20. Service vs Ingress?

**Answer:**

| Service                                | Ingress                      |
| -------------------------------------- | ---------------------------- |
| Exposes Pods                           | Routes HTTP/HTTPS traffic    |
| Works at service level                 | Works at URL/path/host level |
| Can be ClusterIP/NodePort/LoadBalancer | Needs Ingress Controller     |
| Used internally and externally         | Mostly external HTTP routing |

---

# 21. What is ConfigMap?

**Answer:**
ConfigMap stores non-sensitive configuration data as key-value pairs.

Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: blog-config
data:
  SPRING_PROFILES_ACTIVE: prod
  APP_NAME: blog-service
```

Kubernetes docs explain that ConfigMaps store non-confidential data and can be consumed as environment variables, command-line arguments, or mounted files. ([Kubernetes][6])

---

# 22. What is Secret?

**Answer:**
Secret stores sensitive information such as passwords, tokens, and keys.

Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQ=
```

Here `cGFzc3dvcmQ=` is base64 encoded value.

**Important interview point:**
Kubernetes Secrets are not automatically fully secure just because they are called Secrets. Official docs mention that Secrets are stored unencrypted in etcd by default unless encryption at rest is configured. ([Kubernetes][7])

---

# 23. ConfigMap vs Secret?

**Answer:**

| ConfigMap              | Secret                          |
| ---------------------- | ------------------------------- |
| Non-sensitive data     | Sensitive data                  |
| Plain key-value config | Passwords, tokens, certificates |
| Not encoded by default | Base64 encoded                  |
| Example: profile name  | Example: DB password            |

---

# 24. How do you pass environment variables to a Spring Boot app in Kubernetes?

**Answer:**

```yaml
env:
  - name: SPRING_PROFILES_ACTIVE
    valueFrom:
      configMapKeyRef:
        name: blog-config
        key: SPRING_PROFILES_ACTIVE
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: DB_PASSWORD
```

Then in Spring Boot:

```properties
spring.profiles.active=${SPRING_PROFILES_ACTIVE}
spring.datasource.password=${DB_PASSWORD}
```

---

# 25. What is Volume in Kubernetes?

**Answer:**
A Volume provides storage to containers inside a Pod.

Without volume:

```text
Container restarts → data may be lost
```

With volume:

```text
Data can survive container restart depending on volume type
```

Common volume types:

```text
emptyDir
hostPath
configMap
secret
persistentVolumeClaim
```

---

# 26. What is PersistentVolume?

**Answer:**
PersistentVolume, or PV, is storage available in the Kubernetes cluster.

It is independent of Pod lifecycle.

Example:

```text
Pod deleted → PV can still exist
```

Kubernetes docs explain that PersistentVolume abstracts how storage is provided from how it is consumed. ([Kubernetes][8])

---

# 27. What is PersistentVolumeClaim?

**Answer:**
PersistentVolumeClaim, or PVC, is a request for storage by an application.

```text
Pod → PVC → PV → Actual Storage
```

Example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

---

# 28. PV vs PVC?

**Answer:**

| PV                              | PVC                         |
| ------------------------------- | --------------------------- |
| Actual storage resource         | Request for storage         |
| Created by admin or dynamically | Created by application/user |
| Cluster-level resource          | Namespaced resource         |
| Example: 10Gi disk              | Example: claim 5Gi          |

---

# 29. What is StorageClass?

**Answer:**
StorageClass defines the type of storage Kubernetes should dynamically provision.

Example:

```text
fast-ssd
standard
gp3
efs
azure-disk
```

**Interview line:**
PV is actual storage, PVC is the request, and StorageClass controls dynamic provisioning.

---

# 30. What is StatefulSet?

**Answer:**
StatefulSet is used for stateful applications that need stable identity, stable network name, and persistent storage.

Examples:

```text
MySQL
PostgreSQL
Kafka
Zookeeper
MongoDB
```

Official docs describe StatefulSet as a workload object for stateful applications that maintains sticky identity for Pods. ([Kubernetes][9])

---

# 31. Deployment vs StatefulSet?

**Answer:**

| Deployment               | StatefulSet               |
| ------------------------ | ------------------------- |
| Stateless apps           | Stateful apps             |
| Pods are interchangeable | Pods have stable identity |
| Random Pod names         | Ordered Pod names         |
| Easy scaling             | Ordered scaling           |
| Example: Spring Boot API | Example: Kafka, MySQL     |

**Example:**

```text
Deployment Pods:
blog-service-abc123
blog-service-xzy456

StatefulSet Pods:
mysql-0
mysql-1
mysql-2
```

---

# 32. What is DaemonSet?

**Answer:**
DaemonSet ensures one Pod runs on every node or selected nodes.

Use cases:

```text
Log collectors
Monitoring agents
Security agents
Node-level networking agents
```

Example:

```text
One fluent-bit Pod on every worker node
```

---

# 33. What is Job?

**Answer:**
Job runs a task to completion.

Example:

```text
Database migration
One-time report generation
Data cleanup script
```

If the Pod fails, Kubernetes can retry until the Job completes.

---

# 34. What is CronJob?

**Answer:**
CronJob runs Jobs on a schedule.

Example:

```text
Run cleanup every night at 2 AM
Generate daily report
Send scheduled notifications
```

Example:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-cleanup
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cleanup
              image: cleanup-job:1.0
          restartPolicy: OnFailure
```

---

# 35. What are readiness, liveness, and startup probes?

**Answer:**
Probes are health checks performed by kubelet. Kubernetes can restart unhealthy containers or stop sending traffic to containers that are not ready. ([Kubernetes][10])

| Probe           | Purpose                          |
| --------------- | -------------------------------- |
| Readiness probe | Is app ready to receive traffic? |
| Liveness probe  | Is app alive or stuck?           |
| Startup probe   | Has app started successfully?    |

---

# 36. Readiness vs Liveness Probe?

**Answer:**

| Readiness                                     | Liveness                         |
| --------------------------------------------- | -------------------------------- |
| Controls traffic                              | Controls restart                 |
| If failed, Pod removed from Service endpoints | If failed, container restarted   |
| Used during startup or dependency issue       | Used when app is deadlocked/hung |

**Example:**
If Spring Boot app is running but DB is temporarily down, readiness can fail so traffic stops. But liveness should not necessarily fail, otherwise Kubernetes may restart the app repeatedly.

---

# 37. How do you configure probes for Spring Boot?

**Answer:**
With Spring Boot Actuator:

```properties
management.endpoints.web.exposure.include=health,info
management.endpoint.health.probes.enabled=true
management.health.livenessstate.enabled=true
management.health.readinessstate.enabled=true
```

Kubernetes YAML:

```yaml
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 8080
  initialDelaySeconds: 20
  periodSeconds: 10
```

---

# 38. What is Horizontal Pod Autoscaler?

**Answer:**
Horizontal Pod Autoscaler, or HPA, automatically increases or decreases the number of Pods based on metrics like CPU, memory, or custom metrics.

Example:

```text
CPU > 70% → increase Pods
CPU normal → reduce Pods
```

Kubernetes docs explain that HPA scales workload resources such as Deployments or StatefulSets by adding more Pods when load increases. ([Kubernetes][11])

Example:

```bash
kubectl autoscale deployment blog-service \
  --cpu-percent=70 \
  --min=2 \
  --max=10
```

---

# 39. Horizontal scaling vs Vertical scaling?

**Answer:**

| Horizontal Scaling         | Vertical Scaling               |
| -------------------------- | ------------------------------ |
| Add more Pods              | Add more CPU/RAM to same Pod   |
| Example: 3 Pods to 10 Pods | Example: 512Mi to 1Gi memory   |
| Used by HPA                | Used by VPA                    |
| Safer for web apps         | Useful but can require restart |

For Spring Boot microservices, horizontal scaling is usually preferred.

---

# 40. What are resource requests and limits?

**Answer:**
Requests define minimum resources required. Limits define maximum resources allowed.

```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "500m"
  limits:
    memory: "1Gi"
    cpu: "1000m"
```

Kubernetes uses resource requests for scheduling decisions, while kubelet enforces limits so containers do not exceed allowed resources. ([Kubernetes][12])

---

# 41. What happens if a Pod exceeds memory limit?

**Answer:**
If a container exceeds its memory limit, Kubernetes can terminate it with:

```text
OOMKilled
```

You can check using:

```bash
kubectl describe pod <pod-name>
```

For Java apps, this is important because JVM heap should be configured according to container memory.

Example:

```bash
JAVA_TOOL_OPTIONS="-Xms256m -Xmx768m"
```

---

# 42. What is RBAC in Kubernetes?

**Answer:**
RBAC stands for Role-Based Access Control. It controls who can perform what action on which Kubernetes resources.

RBAC uses:

```text
Role
ClusterRole
RoleBinding
ClusterRoleBinding
```

Official docs describe RBAC as a method of regulating access based on roles of users or workloads. ([Kubernetes][13])

---

# 43. Role vs ClusterRole?

**Answer:**

| Role                        | ClusterRole               |
| --------------------------- | ------------------------- |
| Namespace-level permissions | Cluster-level permissions |
| Applies to one namespace    | Applies across cluster    |
| Example: read Pods in dev   | Example: list nodes       |

Example Role:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```

---

# 44. What is ServiceAccount?

**Answer:**
ServiceAccount gives identity to a Pod or workload.

Example:

```text
Spring Boot Pod needs to read ConfigMaps
Pod uses ServiceAccount
ServiceAccount gets permission through RBAC
```

Kubernetes docs describe ServiceAccount as a non-human account that gives distinct identity to workloads inside the cluster. ([Kubernetes][14])

---

# 45. What is SecurityContext?

**Answer:**
SecurityContext defines privilege and access control settings for a Pod or container.

Example:

```yaml
securityContext:
  runAsUser: 1000
  runAsNonRoot: true
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false
```

Kubernetes docs explain that SecurityContext can control user ID, group ID, privileged mode, Linux capabilities, AppArmor, Seccomp, and read-only root filesystem. ([Kubernetes][15])

---

# 46. What is NetworkPolicy?

**Answer:**
NetworkPolicy controls traffic between Pods.

Example:

```text
Only api-service can call user-service
Only backend can call database
Block all other traffic
```

Without NetworkPolicy, many clusters allow broad Pod-to-Pod communication by default, depending on the network plugin.

**Interview line:**
NetworkPolicy is used for microservice-level network security.

---

# 47. What is rolling update?

**Answer:**
Rolling update gradually replaces old Pods with new Pods.

Example:

```text
Version 1 Pods → slowly replaced by Version 2 Pods
```

Command:

```bash
kubectl set image deployment/blog-service \
  blog-service=danish/blog-service:2.0
```

Check rollout:

```bash
kubectl rollout status deployment/blog-service
```

Rollback:

```bash
kubectl rollout undo deployment/blog-service
```

Deployment supports controlled updates and rollback through ReplicaSets. ([Kubernetes][4])

---

# 48. What is CrashLoopBackOff?

**Answer:**
CrashLoopBackOff means the container starts, crashes, restarts, crashes again, and Kubernetes waits before restarting it again.

Common reasons:

```text
Wrong application config
DB connection failure
Missing environment variable
Port issue
Application exception
Wrong command/entrypoint
```

Debug commands:

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl describe pod <pod-name>
```

For Java app, check:

```text
Spring profile
DB URL
DB password
Port
Stack trace
Memory issue
```

---

# 49. What is ImagePullBackOff?

**Answer:**
ImagePullBackOff means Kubernetes cannot pull the container image.

Common reasons:

```text
Wrong image name
Wrong tag
Private registry authentication issue
Image does not exist
Docker registry unavailable
```

Debug:

```bash
kubectl describe pod <pod-name>
```

Fix examples:

```yaml
image: danish/blog-service:1.0
imagePullSecrets:
  - name: dockerhub-secret
```

---

# 50. How would you deploy a Spring Boot microservice on Kubernetes?

**Answer:**
Steps:

```text
1. Build Spring Boot jar
2. Create Docker image
3. Push image to Docker Hub/ECR
4. Create Deployment
5. Create Service
6. Add ConfigMap and Secret
7. Add probes
8. Add resource requests/limits
9. Expose via Ingress or LoadBalancer
10. Monitor logs and metrics
```

Example production-style YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blog-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: blog-service
  template:
    metadata:
      labels:
        app: blog-service
    spec:
      containers:
        - name: blog-service
          image: danish/blog-service:1.0
          ports:
            - containerPort: 8080

          env:
            - name: SPRING_PROFILES_ACTIVE
              value: prod
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: DB_PASSWORD

          resources:
            requests:
              memory: "512Mi"
              cpu: "500m"
            limits:
              memory: "1Gi"
              cpu: "1000m"

          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            initialDelaySeconds: 20
            periodSeconds: 10

          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10

---
apiVersion: v1
kind: Service
metadata:
  name: blog-service
spec:
  type: ClusterIP
  selector:
    app: blog-service
  ports:
    - port: 80
      targetPort: 8080
```

---

# Important Kubernetes Commands for Interview + Practical Work

```bash
kubectl get nodes
kubectl get pods
kubectl get pods -o wide
kubectl get svc
kubectl get deployments
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl exec -it <pod-name> -- /bin/sh
kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl rollout status deployment/<deployment-name>
kubectl rollout history deployment/<deployment-name>
kubectl rollout undo deployment/<deployment-name>
kubectl scale deployment blog-service --replicas=5
kubectl top pods
kubectl top nodes
```

---

# Best Interview Answer Pattern

For Kubernetes answers, use this structure:

```text
Definition
Why it is needed
How it works internally
Real project example
Common issue/debugging point
```

Example for Deployment:

```text
A Deployment is a Kubernetes workload object used to manage stateless application Pods. 
It maintains the desired number of replicas through ReplicaSets and supports rolling updates and rollback. 
In my Spring Boot project, I would package the app as a Docker image, deploy it using Deployment with 3 replicas, expose it using Service, and configure readiness/liveness probes using Spring Boot Actuator endpoints.
```

---

# High-Priority Topics for Your 7-Year Java Backend Interviews

Focus deeply on these:

```text
Pod
Deployment
ReplicaSet
Service
Ingress
ConfigMap
Secret
Probes
HPA
Requests and Limits
PV/PVC
Namespace
RBAC
ServiceAccount
Rolling Update
Troubleshooting
Spring Boot deployment on Kubernetes
Microservices communication inside cluster
```

For backend/product-company interviews, they usually do not expect you to be a Kubernetes administrator, but they expect you to know how to **deploy, expose, scale, debug, secure, and operate your Java microservices** on Kubernetes.

[1]: https://kubernetes.io/?utm_source=chatgpt.com "Kubernetes"
[2]: https://kubernetes.io/docs/concepts/overview/components/ "Kubernetes Components | Kubernetes"
[3]: https://kubernetes.io/docs/reference/kubernetes-api/core/pod-v1/?utm_source=chatgpt.com "Pod"
[4]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/ "Deployments | Kubernetes"
[5]: https://kubernetes.io/docs/concepts/services-networking/ "Services, Load Balancing, and Networking | Kubernetes"
[6]: https://kubernetes.io/docs/concepts/configuration/configmap/?utm_source=chatgpt.com "ConfigMaps"
[7]: https://kubernetes.io/docs/concepts/configuration/secret/?utm_source=chatgpt.com "Secrets"
[8]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Persistent Volumes | Kubernetes"
[9]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/ "StatefulSets | Kubernetes"
[10]: https://kubernetes.io/docs/concepts/workloads/pods/probes/ "Liveness, Readiness, and Startup Probes | Kubernetes"
[11]: https://kubernetes.io/docs/concepts/workloads/autoscaling/horizontal-pod-autoscale/ "Horizontal Pod Autoscaling | Kubernetes"
[12]: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/?utm_source=chatgpt.com "Resource Management for Pods and Containers"
[13]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/?utm_source=chatgpt.com "Using RBAC Authorization"
[14]: https://kubernetes.io/docs/concepts/security/service-accounts/?utm_source=chatgpt.com "Service Accounts"
[15]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ "Configure a Security Context for a Pod or Container | Kubernetes"
