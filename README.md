## **What is Docker?**

Docker is a containerization platform that allows us to package an application and all its dependencies (libraries, runtime, configuration) into a single unit called a *container*. This container can run the same way on any environment—developer machine, testing server, or production.

**Why is Docker used?**

1. Consistency across environments
    
    It solves the problem of “it works on my machine”. Since the container has everything the app needs, it behaves the same everywhere.
    
2. Lightweight and fast
    
    Container share the host OS kernel, so they are much lighter and faster than virtual machines.
    
3. Easy deployment and scaling
    
    You can quickly start, stop, replicate, and scale containers, which is very useful in microservices and cloud environments.
    
4. Isolation
    
    Each container runs independently, so one application does not affect others.
    
5. CI/CD and DevOps friendly
    
    Docker is widely used in automation pipelines to build, test, and deploy applications reliably.
    

**Online interview answer:**

“Docker is a containerization tool that packages an application with all its dependencies into containers so that it can run consistently across different environments, making deployment faster, reliable and scalable”.

## **What is a Container?**

A container is a standard unit of software bundled with dependencies so that applications can be deployed fast and reliably b/w different computing platforms.

A container is a lightweight, isolated environment that packages an application along with all the software it needs to run, such as libraries, dependencies, and configuration.

It uses the host operating system’s kernel but runs as an independent process, so it behaves like a small virtual system.

**Example line for interview:**

“A container is a lightweight, portable runtime environment that contains an application and all its dependencies, allowing it to run consistently on any system.”

**Short comparison:**

- Virtual Machine: Has its own full OS → heavy and slow
- Container: Shares host OS, only includes required libraries → lightweight and fast

<img width="696" height="491" alt="image" src="https://github.com/user-attachments/assets/396fc3d5-8401-49e9-aae9-dd18a7aa2c95" />


## Docker Architecture

### 1. Docker components:

There are three docker components, they are - Docker client, Docker Host, and Docker Registry.

1. **Docker Client**: This component performs “build” and “run” operations for the purpose of opening communication with the docker host. The Docker Client is the tool that users interact with. It is used to run commands such as `docker build`, `docker pull`, and `docker run`. These commands are sent to the Docker Host to perform actions like building images and starting containers.
2. **Docker Host**: This component has the main docker daemon and hosts containers and their associated images. The daemon establishes a connection with the docker registry. 
    
    The Docker Host is the machine where Docker is installed and running. It contains the Docker daemon, which is responsible for:
    
    - Building Docker images
    - Running and managing containers
    - Communicating with the Docker Registry to pull or push images
3. **Docker Registry**: This component stores the docker images. There can be a public registry or a private one. The most famous public registries are Docker Hub and Docker Cloud.
    
    The Docker Registry is a storage system for Docker images. It can be:
    
    - Public (for example, Docker Hub, Docker Cloud)
    - Private (company-specific registries)
    
    The Docker Host pulls images from the registry and pushes newly built images to it.
    

<img width="1038" height="469" alt="image (1)" src="https://github.com/user-attachments/assets/1e79f336-7740-44b0-a6eb-5e08db73151e" />


### 

### 2. Docker Images:

Docker images are executable packages (bundled with application code & dependencies, software packages, etc.) for the purpose of creating containers. Docker images can be deployed to any docker environment and the containers can be spun up there to run the application.

A **Docker image** is a ready-made package that contains everything needed to run an application.

It includes:

- The application code
- The runtime (for example, Java, Node.js, Python)
- Libraries and dependencies
- System tools and settings

In simple words:

> A Docker image is like a template or blueprint from which containers are created.
> 

Think of a Docker image like a **class file** and a container like an **object**.

- Image = Class (definition, read-only)
- Container = Object (running instance)

Or another example:

- Image = Cake recipe
- Container = Actual baked cake

You can bake many cakes (containers) from the same recipe (image).

### Important Features of Docker Images

1. **Read-Only**
    
    Images cannot be changed while running. Any change happens in the container layer.
    
2. **Layered Structure**
    
    Each instruction in a Dockerfile creates a layer.
    
    Example:
    
    - Base OS layer (Ubuntu)
    - Java layer
    - Application layer
3. **Reusable**
    
    The same image can be used on any system with Docker.
    
4. **Portable**
    
    “Build once, run anywhere” — same image runs on laptop, server, or cloud.
    

### 3. DockerFile

A **Dockerfile** is a plain text file that contains a sequence of commands. Docker reads these commands one by one and uses them to build a Docker image.

<img width="1027" height="403" alt="image (2)" src="https://github.com/user-attachments/assets/82d36e99-8a0e-412c-a391-86551b9a0afc" />



## 1. What is a Hypervisor?

A **Hypervisor** is a software that allows you to run multiple virtual machines (VMs) on one physical machine.

Example:

Your laptop (1 physical machine) can run:

- Windows
- Linux
- macOS
    
    at the same time using a hypervisor like VirtualBox or VMware.
    

Types:

- **Type 1 (Bare Metal):** Runs directly on hardware (VMware ESXi, Hyper-V)
- **Type 2 (Hosted):** Runs on OS (VirtualBox, VMware Workstation)

---

## 2. What is Docker Daemon?

**Docker Daemon (dockerd)** is the background service that:

- Builds images
- Runs containers
- Pulls/pushes images
- Manages networks & volumes

When you type:

```bash
docker run hello-world
```

The Docker Client sends this command to the **Docker Daemon**, and the daemon does the actual work.

---

## 3. Important Docker Commands (Organized & Explained)

### 🔹 Image & Container Basics

### Run an image (pulls if not present)

```bash
docker run hello-world
```

Downloads the image and runs a container.

### List running containers

```bash
docker ps
```

### List all containers (running + stopped)

```bash
docker ps -a
```

### List images

```bash
docker images
```

### Pull image from Docker Hub

```bash
docker pull hello-world
```

### Push image to Docker Hub

```bash
docker push mayur/rest-demo:v1
```

### Search image

```bash
docker search hello-world
```

---

### 🔹 Container Lifecycle

### Create container (not start)

```bash
docker create hello-world
```

### Start container

```bash
docker start container_id
```

### Stop container

```bash
docker stop container_id
```

### Remove container

```bash
docker rm 4d9da1494015
```

### Remove image

```bash
docker rmi 05813aedc15f
```

---

### 🔹 Foreground & Background

### Foreground

```bash
docker run -it image_id
```

### Background (Detached)

```bash
docker run -dit image_id
```

---

### 🔹 Execute Command in Running Container

```bash
docker exec affectionate_cori ls /tmp
```

Runs `ls /tmp` inside container.

---

### 🔹 Java Example (Real Use Case)

### Run OpenJDK Container

```bash
docker run -dit openjdk:22-jdk
```

### Copy JAR into container

```bash
docker cp rest-demo.jar affectionate_cori:/tmp
```

### Create Image from Container

```bash
docker commit affectionate_cori mayur/rest-demo:v1
```

### Run Image

```bash
docker run mayur/rest-demo:v1
```

### Add CMD while committing

```bash
docker commit --change='CMD ["java","-jar","/tmp/rest-demo.jar"]' affectionate_cori mayur/rest-demo:v2
```

---

## 4. Dockerfile (Best Practice)

Instead of `docker commit`, use Dockerfile.

### Example Dockerfile

```docker
FROM openjdk:22-jdk
ADD target/rest-demo.jar rest-demo.jar
ENTRYPOINT ["java", "-jar", "/rest-demo.jar"]
```

### Build Image

```bash
docker build -t mayur/rest-demo:v3 .
```

### Run Image

```bash
docker run -dit mayur/rest-demo:v3
```

---

## 5. Summary Flow

1. **Dockerfile** → defines image
2. **docker build** → creates image
3. **docker run** → creates & runs container
4. **docker exec** → run commands inside container
5. **docker stop / rm** → stop & delete container
6. **docker push** → upload image to Docker Hub

---

## What is a Dockerfile?

A **Dockerfile** is a text file that contains step-by-step instructions to build a Docker image.

---

## 1. `FROM` – Base Image

```docker
FROM openjdk:17-jdk-slim
```

Meaning:

Start from an existing image that already has Java 17 installed.

In Spring Boot:

```docker
FROM openjdk:17
```

---

## 2. `WORKDIR` – Working Directory

```docker
WORKDIR /app
```

Creates and sets `/app` as the current directory inside the container.

All next commands run from this path.

---

## 3. `COPY` – Copy Files (Host → Image)

```docker
COPY target/app.jar app.jar
```

Copies JAR from your machine to container.

Difference:

- `COPY` → simple copy
- `ADD` → copy + extract archives + download URLs (less used)

---

## 4. `ADD` – Advanced Copy

```docker
ADD target/app.jar /app/app.jar
```

---

## 5. `RUN` – Execute During Build

```docker
RUN apt-get update && apt-get install -y curl
```

Runs only when image is being built.

---

## 6. `EXPOSE` – Inform Port

```docker
EXPOSE 8080
```

Documents that container listens on port 8080.

---

## 7. `ENV` – Environment Variable

```docker
ENV SPRING_PROFILES_ACTIVE=prod
```

---

## 8. `CMD` – Default Command (Can be Overridden)

```docker
CMD ["java","-jar","app.jar"]
```

---

## 9. `ENTRYPOINT` – Main Command (Cannot be Replaced)

```docker
ENTRYPOINT ["java","-jar","app.jar"]
```

Difference:

| CMD | ENTRYPOINT |
| --- | --- |
| Can be overridden | Fixed main process |

Best Practice:

```docker
ENTRYPOINT ["java","-jar","app.jar"]
```

---

## 10. `ARG` – Build-Time Variable

```docker
ARG JAR_FILE=target/app.jar
COPY ${JAR_FILE} app.jar
```

---

## 11. `VOLUME` – Persistent Storage

```docker
VOLUME /data
```

---

## 12. `USER` – Run as Non-root

```docker
USER spring
```

---

## 13. `HEALTHCHECK` – Container Health

```docker
HEALTHCHECK CMD curl -f http://localhost:8080/actuator/health || exit 1
```

---

## 14. Multi-Stage Build Example (Professional Level)

```docker
# Build stage
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src .
RUN mvn clean package -DskipTests

# Runtime stage
FROM openjdk:17-jre-slim
COPY --from=build /app/target/app.jar app.jar
ENTRYPOINT ["java","-jar","app.jar"]

```

---

## Summary Table

| Command | Purpose |
| --- | --- |
| FROM | Base image |
| WORKDIR | Set working directory |
| COPY / ADD | Copy files |
| RUN | Execute at build time |
| EXPOSE | Document port |
| ENV | Environment variable |
| CMD | Default run command |
| ENTRYPOINT | Main process |
| ARG | Build-time variable |
| VOLUME | Persistent data |
| USER | Non-root user |
| HEALTHCHECK | Health monitoring |

---

## Explanation of Your docker-compose.yml

### Version

```yaml
version:"3.8"
```

This tells Docker Compose which syntax version you are using.

---

### Services Section

```yaml
services:
```

A **service = one container** (or group of same containers).

You have two services:

- `app` → your Spring Boot application
- `postgres` → PostgreSQL database

---

### App Service

```yaml
app:
	build:
		context:.
		dockerfile:Dockerfile
```

Means:

- Build an image from current folder (`.`)
- Use `Dockerfile` to build it

```yaml
ports:
	-"8085:8085"
```

Format: `HOST:CONTAINER`

- Access app from browser: `localhost:8085`
- Inside container app runs on port `8085`

```yaml
networks:
	-mynetwork
```

Connects this container to custom network `mynetwork`.

---

### Postgres Service

```yaml
postgres:
	image:postgres:15
```

Pulls official PostgreSQL version 15 from Docker Hub.

```yaml
environment:
	POSTGRES_USER:mayur
	POSTGRES_PASSWORD:1234
	POSTGRES_DB:student_db
```

These are environment variables used by PostgreSQL at startup.

```yaml
ports:
	-"5433:5432"
```

- Host port: 5433
- Container port: 5432 (default PostgreSQL)

So you connect using:

```
jdbc:postgresql://localhost:5433/student_db
```

```yaml
volumes:
	-postgres-my-data:/var/lib/postgresql/data
```

This stores DB data permanently.

```yaml
networks:
	-mynetwork
```

So app and DB can talk using service name: `postgres`.

## 1. What is Docker Compose?

Docker Compose is a tool to run **multiple containers together** using one YAML file (`docker-compose.yml`).

Instead of running:

```bash
docker run app
docker run postgres
docker network create ...
docker volume create ...
```

You write everything in one file and run:

```bash
docker compose up
```

Imagine your application is not just one program. It has:

- A backend (Spring Boot)
- A database (PostgreSQL)
- Maybe a frontend (React)
- Maybe Redis, Kafka, etc.

Each of these runs in its own container.

Without Docker Compose, you must start each container one by one and connect them manually. This is hard and error-prone.

Docker Compose is like a **manager** that reads one file and says:

> “Create all these containers, connect them, and start them together.”
> 

You write everything in `docker-compose.yml` and run:

```bash
docker compose up
```

Docker Compose will:

1. Build your application image
2. Pull database image
3. Create a private network
4. Create a storage volume
5. Start all containers
6. Connect them so they can talk

So Compose = **one command to start the whole system**.

---

## 2. What is a Service?

In Compose, a *service* means **one container definition**.

In your file:

- `app` = your Spring Boot container
- `postgres` = your PostgreSQL container

Each service becomes a running container.

## 3. Docker Network (mynetwork)

```yaml
networks:
	mynetwork:
		driver:bridge
```

### What is Docker Network?

A Docker network is like a **virtual LAN** for containers.

Benefits:

- Containers can talk using service name
- Isolation from other containers
- DNS resolution inside Docker

Example:

Your Spring Boot app can connect to DB using:

```
jdbc:postgresql://postgres:5432/student_db
```

Here `postgres` = service name, resolved by Docker DNS.

### Why Network is Needed?

Containers are like **small isolated machines**.

By default:

- They don’t know each other
- They have their own IP
- No DNS name resolution

Docker Network provides:

- Private communication
- Automatic DNS
- Isolation
- Load balancing (for same service replicas)

---

### Types of Docker Networks

### 1. Bridge (Most common – used by Compose)

Think of it as:

> A virtual switch inside your laptop.
> 

All containers connected to the same bridge:

- Can talk
- Can resolve names
- Are isolated from outside

Your compose:

```yaml
networks:
	mynetwork:
		driver:bridge
```

So:

```
app  <---->  postgres
     (mynetwork)
```

---

### How DNS Works in Docker Network

Inside container:

```bash
ping postgres
```

Docker’s internal DNS:

```
postgres -> 172.18.0.2
```

So your Spring Boot uses:

```
jdbc:postgresql://postgres:5432/student_db
```

Not `localhost`.

---

### Network vs Port Mapping

| Concept | Purpose |
| --- | --- |
| Network | Container-to-container communication |
| Port Mapping | Host-to-container communication |

Example:

```
Browser-> localhost:8085-> app container
App-> postgres:5432-> DBcontainer (via network)
```

---

## 4. Docker Volume (postgres-my-data)

```yaml
volumes:
	postgres-my-data:
```

### What is Docker Volume?

A Docker Volume is **persistent storage managed by Docker**, used to store data outside the container’s writable layer.

Containers are **ephemeral** by nature:

- When a container is deleted, its filesystem is destroyed.
- Any data written inside the container (like database files, uploads, logs) is lost.
- This is unacceptable for stateful services such as PostgreSQL, MySQL, MongoDB, etc.

Therefore, Docker provides **Volumes** to decouple data from container lifecycle.

---

### Why Docker Volumes are Needed

- Containers are temporary and disposable.
- Database data must survive:
    - Container stop
    - Container restart
    - Container recreation
    - Image upgrades
- Volumes provide **durability and persistence**.

---

### Volume Mapping Example

```yaml
postgres-my-data:/var/lib/postgresql/data

```

Meaning:

| Part | Explanation |
| --- | --- |
| `postgres-my-data` | Name of Docker volume on host |
| `/var/lib/postgresql/data` | Internal PostgreSQL data directory inside container |

So:

- PostgreSQL writes all tables, WAL files, indexes to `/var/lib/postgresql/data`
- That folder is actually backed by Docker Volume
- Docker stores this volume in host filesystem (e.g. `/var/lib/docker/volumes/...`)

---

### Lifecycle Behavior

| Action | Result |
| --- | --- |
| Stop container | Data remains |
| Remove container | Data remains |
| Recreate container | Data is reused |
| Restart Docker | Data remains |
| Rebuild image | Data remains |
| Delete volume | Data is permanently lost |

So:

> Stop container → data remains
> 
> 
> Recreate container → data still there
> 

### Deep Technical View

Docker volume architecture:

```
Application
   |
ContainerFS(/var/lib/postgresql/data)
   |
Docker Volume Mount
   |
HostDisk(managed by Docker)
```

The container only sees a mounted directory, but actual storage is:

```
/var/lib/docker/volumes/postgres-my-data/_data
```

---

### Volume vs Container Filesystem

| Container Layer | Volume |
| --- | --- |
| Ephemeral | Persistent |
| Removed with container | Independent of container |
| Good for temp files | Mandatory for DB |
| Slower for heavy I/O | Optimized for I/O |

---

### Volume vs Bind Mount

| Volume | Bind Mount |
| --- | --- |
| Managed by Docker | Managed by user |
| Portable | Host path dependent |
| Secure permissions | Permission issues |
| Best for production | Mostly dev/testing |

---

### Interview One-Line Definition

> A Docker Volume is a persistent storage mechanism managed by Docker that stores data outside the container lifecycle, ensuring durability of stateful services like databases.
> 

---

### Real Interview Question

**Q:** Why should databases always use Docker volumes?

**A:** Because containers are ephemeral. Without volumes, deleting or recreating a container will destroy all database files. Volumes decouple data from container lifecycle and provide persistent, durable storage.

---

## 5. What is Port Mapping?

```yaml
"8085:8085"
```

Means:

```
YourLaptopPort8085→ContainerPort8085
```

So you open browser and type:

```
http://localhost:8085
```

It goes inside the container.

Same for database:

```
localhost:5433 → postgres container 5432
```

---

## 6. How Everything Works Together

Simple picture in words:

1. Docker creates a private network
2. Docker creates a volume for DB
3. Docker starts PostgreSQL
4. Docker starts Spring Boot app
5. Both are in same network
6. App connects to DB using name `postgres`
7. DB stores data in volume

So you get:

- Communication (Network)
- Persistence (Volume)
- Automation (Compose)

---

# Important Questions

## Basic Docker

1. What is Docker?
2. What is the difference between Docker and Virtual Machine?
3. What is an image?
4. What is a container?
5. What is Dockerfile?
6. What is the difference between `RUN`, `CMD`, and `ENTRYPOINT`?
7. What is the use of `EXPOSE`?
8. What is the difference between `COPY` and `ADD`?
9. What is Docker Hub?
10. What is the Docker daemon?

---

## Docker Networking

1. What is Docker network?
2. Types of Docker networks?
3. What is bridge network?
4. How do containers communicate with each other?
5. Why can't we use localhost between containers?
6. What is port mapping?
7. What is DNS in Docker?
8. What is overlay network?
9. Difference between default bridge and user-defined bridge?
10. How to connect a container to multiple networks?

---

## Docker Volumes & Storage

1. What is Docker volume?
2. Difference between volume and bind mount?
3. Where does Docker store volumes?
4. Why do databases need volumes?
5. What is tmpfs mount?
6. How to backup a Docker volume?
7. What happens to volume when container is deleted?
8. How to share data between containers?
9. How to migrate volume data?
10. What is volume driver?

---

## Docker Compose

1. What is Docker Compose?
2. Difference between Dockerfile and Docker Compose?
3. What is a service in Compose?
4. What is `depends_on`?
5. What is `restart` policy?
6. What is `.env` file in Compose?
7. What is scaling in Compose?
8. What is healthcheck?
9. Difference between `docker-compose` and `docker compose`?
10. What is `profiles`?

---

## Advanced & DevOps Level

1. What is multi-stage build?
2. What is layer caching?
3. How does Docker reduce image size?
4. What is container lifecycle?
5. What is cgroups and namespaces?
6. What is OCI?
7. How does Docker provide isolation?
8. What is init process in container?
9. What is rootless Docker?
10. How is Docker used in CI/CD?

---

## Real Project Questions

1. How do you connect Spring Boot with PostgreSQL using Docker?
2. How do you secure secrets in Docker?
3. How do you handle environment configs?
4. How do you reduce image size?
5. How do you monitor containers?
6. How do you handle logs?
7. How do you deploy using Docker Compose?
8. How do you migrate to Kubernetes?
9. How do you handle zero-downtime deployment?
10. How do you debug a failing container?

---

## Scenario-Based Questions

1. DB container restarts and data lost. Why?
2. App cannot connect to DB in Docker. What could be the reason?
3. Port is already in use. How to fix?
4. Container starts and stops immediately. Why?
5. Image build is slow. How to optimize?
6. Container memory is high. How to limit it?
7. How to scale backend containers?
8. How to do rolling update?
9. How to share config files?
10. How to expose multiple services via Nginx?
