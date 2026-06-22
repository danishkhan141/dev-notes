Below are **Top 50 Docker interview questions** prepared for a **Java Backend / Spring Boot / Microservices interview**. Docker is an open platform used for developing, shipping, and running applications in containers, helping separate application code from infrastructure. ([Docker Documentation][1])

---

# Docker Interview Questions — Top 50

## 1. What is Docker?

Docker is a containerization platform used to package an application with its dependencies, configuration, libraries, and runtime so it can run consistently across environments.

**Simple example:**
Your Spring Boot app may run on your laptop but fail on another machine due to Java version, MySQL version, or missing environment variables. Docker solves this by packaging everything needed to run the app.

---

## 2. What is a container?

A container is a lightweight, isolated runtime environment created from a Docker image.

Think of it like this:

```text
Image = Class
Container = Object
```

An image is a blueprint. A container is a running instance of that image.

---

## 3. What is the difference between Docker image and Docker container?

| Image                       | Container                        |
| --------------------------- | -------------------------------- |
| Read-only template          | Running instance of image        |
| Contains app + dependencies | Executes the app                 |
| Built using Dockerfile      | Created using `docker run`       |
| Can be pushed to registry   | Can be started, stopped, removed |

Example:

```bash
docker build -t my-spring-app .
docker run -p 8080:8080 my-spring-app
```

---

## 4. Why do we use Docker?

Docker is used for:

```text
Consistent environment
Fast deployment
Easy scaling
Dependency isolation
CI/CD automation
Microservices deployment
```

In interviews, say:

> Docker solves the “it works on my machine” problem by packaging the application and its dependencies into a portable container.

---

## 5. Docker vs Virtual Machine?

| Docker Container       | Virtual Machine            |
| ---------------------- | -------------------------- |
| Shares host OS kernel  | Has separate guest OS      |
| Lightweight            | Heavy                      |
| Starts in seconds      | Takes minutes              |
| Less resource usage    | More CPU/RAM               |
| Good for microservices | Good for full OS isolation |

Example:

```text
VM = Full house
Container = Separate room inside same building
```

---

## 6. What is Docker Engine?

Docker Engine is the core runtime that builds and runs containers. It includes Docker daemon, REST API, and Docker CLI.

```text
Docker CLI -> Docker Daemon -> containerd -> runc -> container
```

Docker Engine manages containers, images, networks, volumes, logs, and daemon configuration. ([Docker Documentation][2])

---

## 7. What is Docker daemon?

Docker daemon is the background service that listens to Docker API requests and manages Docker objects like images, containers, networks, and volumes.

Example:

```bash
docker ps
```

Here, Docker CLI sends a request to Docker daemon, and daemon returns running containers.

---

## 8. What is Dockerfile?

A Dockerfile is a text file containing instructions to build a Docker image.

Example for Spring Boot:

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/blog-app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 9. What is the purpose of `FROM` in Dockerfile?

`FROM` defines the base image.

Example:

```dockerfile
FROM eclipse-temurin:17-jre
```

This means our application will run on a Java 17 runtime image.

---

## 10. What is the difference between `CMD` and `ENTRYPOINT`?

| ENTRYPOINT                 | CMD                      |
| -------------------------- | ------------------------ |
| Main executable            | Default arguments        |
| Harder to override         | Easy to override         |
| Defines container behavior | Provides default command |

Example:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Better for Spring Boot applications because the container’s main purpose is fixed.

---

## 11. What is the difference between `COPY` and `ADD`?

| COPY                       | ADD                                         |
| -------------------------- | ------------------------------------------- |
| Copies files/directories   | Copies files + extra features               |
| Simple and predictable     | Can extract tar files and fetch remote URLs |
| Recommended for most cases | Use only when extra features are needed     |

Interview answer:

> Prefer `COPY` unless you specifically need `ADD` features.

---

## 12. What is `.dockerignore`?

`.dockerignore` excludes unnecessary files from the Docker build context.

Example:

```text
target/
.git/
.idea/
*.log
node_modules/
```

Why important?

```text
Faster build
Smaller context
Avoid leaking secrets
Cleaner image build
```

---

## 13. What is Docker image layer?

Each Dockerfile instruction creates a layer.

Example:

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/app.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Each instruction contributes to image layering. Docker reuses unchanged layers using cache, making builds faster.

---

## 14. What is Docker build cache?

Docker build cache reuses previous image layers if instructions and files have not changed.

Example:

```dockerfile
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package
```

This improves Maven build speed because dependencies are downloaded only when `pom.xml` changes.

---

## 15. What is multi-stage build?

Multi-stage build uses multiple `FROM` instructions. One stage can build the application, and another stage can run only the final artifact. This helps reduce final image size and avoids shipping build tools in production images. ([Docker Documentation][3])

Example:

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Interview line:

> Multi-stage build keeps Maven and source code out of the final runtime image.

---

## 16. How do you build a Docker image?

```bash
docker build -t blog-app:1.0 .
```

Explanation:

```text
-t = tag name
blog-app = image name
1.0 = version tag
. = current directory as build context
```

---

## 17. How do you run a container?

```bash
docker run -p 8080:8080 blog-app:1.0
```

This maps:

```text
Host port 8080 -> Container port 8080
```

So the application becomes accessible at:

```text
http://localhost:8080
```

---

## 18. What is port mapping in Docker?

Port mapping exposes a container port to the host machine.

Example:

```bash
docker run -p 9090:8080 blog-app
```

Meaning:

```text
localhost:9090 -> container:8080
```

Useful when multiple containers use the same internal port.

---

## 19. What is detached mode?

Detached mode runs a container in the background.

```bash
docker run -d -p 8080:8080 blog-app
```

`-d` means detached.

To check logs:

```bash
docker logs <container-id>
```

---

## 20. How do you list Docker containers?

Running containers:

```bash
docker ps
```

All containers:

```bash
docker ps -a
```

Images:

```bash
docker images
```

---

## 21. How do you stop, start, and remove a container?

```bash
docker stop container-name
docker start container-name
docker rm container-name
```

Force remove:

```bash
docker rm -f container-name
```

---

## 22. What is the difference between `docker stop` and `docker kill`?

| Command       | Meaning                         |
| ------------- | ------------------------------- |
| `docker stop` | Gracefully stops container      |
| `docker kill` | Forcefully terminates container |

`docker stop` sends `SIGTERM`, then after timeout sends `SIGKILL`.

Interview answer:

> In production, prefer graceful shutdown so Spring Boot can close DB connections, complete requests, and release resources properly.

---

## 23. How do you check container logs?

```bash
docker logs container-name
```

Follow logs:

```bash
docker logs -f container-name
```

Last 100 lines:

```bash
docker logs --tail 100 container-name
```

For Spring Boot debugging, logs are very important.

---

## 24. How do you enter inside a running container?

```bash
docker exec -it container-name sh
```

Or:

```bash
docker exec -it container-name bash
```

Use this for checking files, environment variables, or debugging inside container.

---

## 25. What is Docker volume?

A Docker volume is persistent storage managed by Docker. It keeps data even if the container is deleted. Docker volumes are specifically designed for persistent container data. ([Docker Documentation][4])

Example:

```bash
docker volume create mysql_data
docker run -v mysql_data:/var/lib/mysql mysql:8
```

---

## 26. Why do we need volumes?

Containers are temporary. If a container is removed, its internal data can be lost. Volumes solve this.

Example:

```text
MySQL container without volume -> data lost after deletion
MySQL container with volume -> data preserved
```

---

## 27. Volume vs bind mount?

| Volume                | Bind Mount                 |
| --------------------- | -------------------------- |
| Managed by Docker     | Managed by host filesystem |
| Better for production | Good for local development |
| Stored in Docker area | Any host path              |
| More portable         | Less portable              |

Example volume:

```bash
docker run -v mysql_data:/var/lib/mysql mysql:8
```

Example bind mount:

```bash
docker run -v /home/user/app:/app my-image
```

---

## 28. What is Docker network?

Docker network allows containers to communicate with each other.

Common network types:

```text
bridge
host
none
overlay
```

Default network is usually bridge.

---

## 29. What is bridge network?

Bridge network is the default network for containers on a single Docker host.

Example:

```bash
docker network create blog-network
docker run --network blog-network --name mysql mysql:8
docker run --network blog-network --name blog-app blog-app
```

Inside `blog-app`, MySQL can be accessed using container name:

```text
mysql:3306
```

---

## 30. How do two containers communicate?

Containers communicate through Docker network using container names as DNS names.

Example:

```yaml
spring.datasource.url=jdbc:mysql://mysql-db:3306/blogdb
```

Here `mysql-db` is the MySQL container/service name.

---

## 31. What is Docker Compose?

Docker Compose is used to define and run multi-container applications using a YAML file. It is very useful when your application needs Spring Boot, MySQL, Redis, Kafka, etc. ([Docker Documentation][5])

---

## 32. Why use Docker Compose?

Without Compose:

```bash
docker network create app-net
docker run mysql...
docker run redis...
docker run springboot...
```

With Compose:

```bash
docker compose up
```

It starts all services together.

---

## 33. Example Docker Compose for Spring Boot + MySQL

```yaml
services:
  mysql-db:
    image: mysql:8
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: blogdb
    ports:
      - "3307:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  blog-app:
    build: .
    container_name: blog-app
    ports:
      - "8080:8080"
    depends_on:
      - mysql-db
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql-db:3306/blogdb
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: root

volumes:
  mysql_data:
```

Run:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

## 34. What is `depends_on` in Docker Compose?

`depends_on` controls startup order.

Example:

```yaml
depends_on:
  - mysql-db
```

But important interview point:

> `depends_on` does not guarantee MySQL is fully ready. It only ensures the MySQL container starts before the Spring Boot container.

For real readiness, use health checks or retry logic in application.

---

## 35. What is health check in Docker?

Health check tells Docker whether the containerized application is healthy.

Example:

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
CMD curl -f http://localhost:8080/actuator/health || exit 1
```

For Spring Boot, expose actuator health endpoint:

```properties
management.endpoints.web.exposure.include=health,info
```

---

## 36. What is Docker registry?

A Docker registry stores Docker images.

Examples:

```text
Docker Hub
AWS ECR
Azure Container Registry
Google Artifact Registry
Private registry
```

Commands:

```bash
docker tag blog-app:1.0 username/blog-app:1.0
docker push username/blog-app:1.0
docker pull username/blog-app:1.0
```

---

## 37. What is Docker Hub?

Docker Hub is a public container registry where you can find and publish Docker images.

Example:

```bash
docker pull mysql:8
docker pull redis:latest
docker pull nginx
```

---

## 38. What is image tag?

A tag is a version label for an image.

Example:

```bash
blog-app:1.0
blog-app:2.0
blog-app:latest
```

Interview point:

> Avoid using `latest` in production because it is not predictable. Use fixed version tags.

---

## 39. What is the difference between `docker save` and `docker export`?

| Command         | Works On  | Output                        |
| --------------- | --------- | ----------------------------- |
| `docker save`   | Image     | Image tar with layers/history |
| `docker export` | Container | Container filesystem tar      |

Example:

```bash
docker save -o app-image.tar blog-app:1.0
docker export -o app-container.tar container-name
```

---

## 40. What is the difference between `docker run`, `docker start`, and `docker exec`?

| Command        | Meaning                               |
| -------------- | ------------------------------------- |
| `docker run`   | Creates and starts a new container    |
| `docker start` | Starts existing stopped container     |
| `docker exec`  | Runs command inside running container |

Example:

```bash
docker run nginx
docker start nginx-container
docker exec -it nginx-container sh
```

---

## 41. What is the use of environment variables in Docker?

Environment variables externalize configuration.

Example:

```bash
docker run -e SPRING_PROFILES_ACTIVE=prod blog-app
```

Spring Boot example:

```properties
spring.profiles.active=${SPRING_PROFILES_ACTIVE:dev}
```

Interview line:

> We should not hardcode environment-specific values like DB URL, username, password, or profile inside the image.

---

## 42. How do you pass Spring Boot profile in Docker?

Using command:

```bash
docker run -e SPRING_PROFILES_ACTIVE=prod blog-app
```

Using Compose:

```yaml
environment:
  SPRING_PROFILES_ACTIVE: prod
```

Using Dockerfile default:

```dockerfile
ENTRYPOINT ["java", "-Dspring.profiles.active=prod", "-jar", "app.jar"]
```

Best answer:

> Prefer passing profile from outside using environment variables instead of hardcoding it inside Dockerfile.

---

## 43. How do you dockerize a Spring Boot application?

Steps:

```text
1. Build JAR using Maven
2. Create Dockerfile
3. Build Docker image
4. Run container
5. Pass DB/config using environment variables
6. Push image to registry if needed
```

Example:

```bash
mvn clean package -DskipTests
docker build -t blog-app:1.0 .
docker run -p 8080:8080 blog-app:1.0
```

---

## 44. What are best practices for Dockerfile?

Important practices:

```text
Use small base image
Use multi-stage build
Avoid running as root
Use .dockerignore
Pin image versions
Reduce layers
Do not store secrets in image
Use COPY instead of ADD unless needed
Keep one main process per container
```

Docker’s build best practices also recommend multi-stage builds to reduce final image size and separate build output from runtime output. ([Docker Documentation][6])

---

## 45. How do you run container as non-root user?

Example:

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app

RUN addgroup --system appgroup && adduser --system appuser --ingroup appgroup

COPY target/app.jar app.jar

USER appuser

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Why?

```text
Improves security
Limits damage if application is compromised
Avoids unnecessary root privileges
```

Docker also supports rootless mode, where the Docker daemon and containers can run as a non-root user to reduce certain daemon/runtime risks. ([Docker Documentation][7])

---

## 46. How do you reduce Docker image size?

Use:

```text
Multi-stage build
Smaller base image
Remove unnecessary tools
Use .dockerignore
Avoid copying source code into final image
Clean package manager cache
```

Bad:

```dockerfile
FROM maven:3.9-eclipse-temurin-17
COPY . .
RUN mvn package
CMD ["java", "-jar", "target/app.jar"]
```

Good:

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 47. How do you debug a Docker container issue?

Common commands:

```bash
docker ps -a
docker logs container-name
docker inspect container-name
docker exec -it container-name sh
docker stats
docker network ls
docker volume ls
```

Debugging checklist:

```text
Is container running?
Are ports mapped correctly?
Are environment variables correct?
Can app connect to DB?
Is DB container ready?
Are logs showing startup failure?
Is correct image tag used?
```

---

## 48. What is `docker inspect`?

`docker inspect` gives detailed JSON information about a container/image/network/volume.

Example:

```bash
docker inspect blog-app
```

Useful for checking:

```text
IP address
Environment variables
Mounts
Network
Port bindings
Image ID
Restart policy
```

---

## 49. What is `docker stats`?

`docker stats` shows live resource usage of containers.

```bash
docker stats
```

It displays:

```text
CPU usage
Memory usage
Network I/O
Block I/O
PIDs
```

Useful for performance debugging.

---

## 50. How is Docker used in CI/CD?

Typical Jenkins pipeline flow:

```text
1. Pull code from Git
2. Run unit tests
3. Build JAR
4. Build Docker image
5. Scan image
6. Push image to registry
7. Deploy to server or Kubernetes
```

Example Jenkins pipeline idea:

```groovy
pipeline {
    agent any

    stages {
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t blog-app:1.0 .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8080:8080 --name blog-app blog-app:1.0'
            }
        }
    }
}
```

Interview answer:

> Docker makes CI/CD predictable because the same image built in the pipeline can be deployed to testing, staging, and production.

---

# Very Important Scenario-Based Docker Questions

## Scenario 1: Your Spring Boot container starts but exits immediately. What will you check?

Check:

```bash
docker ps -a
docker logs container-name
```

Possible reasons:

```text
JAR missing
Wrong ENTRYPOINT
Port conflict
Application startup error
DB connection failure
Invalid environment variable
Java version mismatch
```

---

## Scenario 2: Spring Boot app cannot connect to MySQL container. What could be wrong?

Common mistakes:

```text
Using localhost inside container
Containers are on different networks
MySQL is not ready
Wrong DB username/password
Wrong database name
Port confusion
```

Important point:

Inside Docker network, do not use:

```properties
jdbc:mysql://localhost:3306/blogdb
```

Use service/container name:

```properties
jdbc:mysql://mysql-db:3306/blogdb
```

---

## Scenario 3: App works locally but not inside Docker. Why?

Possible reasons:

```text
Different Java version
Missing environment variables
Wrong profile
External file path issue
Container cannot access host localhost
Port not exposed/mapped
Database not reachable
```

---

## Scenario 4: Docker image is very large. How will you optimize?

Answer:

```text
Use multi-stage build
Use JRE instead of JDK in final image
Remove Maven from runtime image
Use .dockerignore
Avoid copying unnecessary files
Use slim/distroless images if suitable
```

---

## Scenario 5: How will you deploy microservices using Docker?

Example:

```text
user-service container
post-service container
category-service container
api-gateway container
mysql container
redis container
eureka-server container
```

Each service has its own Dockerfile. Docker Compose can be used locally. Kubernetes is preferred for production orchestration.

---

# Docker Commands Cheat Sheet

```bash
docker --version
docker images
docker ps
docker ps -a
docker build -t app:1.0 .
docker run -p 8080:8080 app:1.0
docker run -d --name app -p 8080:8080 app:1.0
docker logs -f app
docker exec -it app sh
docker stop app
docker start app
docker rm app
docker rmi app:1.0
docker network ls
docker network create app-net
docker volume ls
docker volume create mysql_data
docker inspect app
docker stats
docker system prune
docker compose up -d
docker compose down
```

---

# Strong Interview Closing Answer

You can say this in interviews:

> In my Spring Boot project, I use Docker to package the application into an image using a Dockerfile. For local multi-container setup, I use Docker Compose to run Spring Boot with MySQL or other dependencies. I pass environment-specific values like DB URL, username, password, and Spring profile through environment variables. For production readiness, I prefer multi-stage builds, smaller runtime images, non-root users, health checks, logs, and fixed image tags. In CI/CD, Jenkins can build the JAR, create Docker image, push it to Docker Hub or ECR, and deploy it to a server or Kubernetes.

This answer connects Docker with your actual Java backend work and sounds professional.

[1]: https://docs.docker.com/get-started/get-docker/?utm_source=chatgpt.com "Get Docker"
[2]: https://docs.docker.com/engine/?utm_source=chatgpt.com "Docker Engine"
[3]: https://docs.docker.com/build/building/multi-stage/?utm_source=chatgpt.com "Multi-stage builds"
[4]: https://docs.docker.com/engine/storage/volumes/?utm_source=chatgpt.com "Volumes"
[5]: https://docs.docker.com/compose/?utm_source=chatgpt.com "Docker Compose"
[6]: https://docs.docker.com/build/building/best-practices/?utm_source=chatgpt.com "Building best practices"
[7]: https://docs.docker.com/engine/security/rootless/?utm_source=chatgpt.com "Rootless mode"
