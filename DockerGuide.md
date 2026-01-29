# Complete Docker Guide: From Basics to Production

## 📚 Table of Contents
1. [Docker Fundamentals](#docker-fundamentals)
2. [Installation](#installation)
3. [Core Concepts](#core-concepts)
4. [Essential Docker Commands](#essential-docker-commands)
5. [Project Setup](#project-setup)
6. [Debugging Configuration](#debugging-configuration)
7. [Best Practices](#best-practices)
8. [Troubleshooting](#troubleshooting)

---

## 🐳 Docker Fundamentals

### What is Docker?
Docker is a platform that packages applications and their dependencies into **containers** - lightweight, standalone, executable packages that include everything needed to run the software.

### Why Docker?
- **Consistency**: "Works on my machine" → "Works everywhere"
- **Isolation**: Each container runs independently
- **Portability**: Run anywhere (local, cloud, production)
- **Efficiency**: Lightweight compared to virtual machines
- **Scalability**: Easy to scale up/down

### Docker vs Virtual Machines

| Feature | Docker Container | Virtual Machine |
|---------|-----------------|-----------------|
| Size | MBs | GBs |
| Startup | Seconds | Minutes |
| Performance | Near-native | Slower |
| Isolation | Process-level | Complete OS |

---

## 📥 Installation

### Windows
1. Download [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
2. Install and restart
3. Enable WSL 2 backend (recommended)
4. Verify: `docker --version`

### Mac
1. Download [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop)
2. Install and start Docker Desktop
3. Verify: `docker --version`

### Linux (Ubuntu/Debian)
```bash
# Update package index
sudo apt-get update

# Install prerequisites
sudo apt-get install ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verify installation
docker --version
docker compose version
```

### Post-Installation (Linux)
```bash
# Add user to docker group (avoid using sudo)
sudo usermod -aG docker $USER
newgrp docker

# Test Docker
docker run hello-world
```

---

## 🧩 Core Concepts

### 1. **Image**
- Blueprint/template for containers
- Read-only
- Contains OS, application code, dependencies
- Built from a `Dockerfile`

### 2. **Container**
- Running instance of an image
- Isolated process
- Writable layer on top of image
- Can be started, stopped, deleted

### 3. **Dockerfile**
- Text file with instructions to build an image
- Each instruction creates a layer

### 4. **Docker Compose**
- Tool for defining multi-container applications
- Uses `docker-compose.yml` file
- Manages multiple containers as a single service

### 5. **Volume**
- Persistent data storage
- Data survives container deletion
- Shared between containers and host

### 6. **Network**
- Allows containers to communicate
- Isolated network environment

### 7. **Registry**
- Storage for Docker images
- Docker Hub is the default public registry

---

## 🛠 Essential Docker Commands

### Image Commands
```bash
# List images
docker images
docker image ls

# Pull image from registry
docker pull nginx:latest

# Build image from Dockerfile
docker build -t myapp:1.0 .

# Remove image
docker rmi image_name
docker image rm image_name

# Remove unused images
docker image prune

# Tag image
docker tag myapp:1.0 myapp:latest

# Push to registry
docker push username/myapp:1.0
```

### Container Commands
```bash
# Run container
docker run nginx
docker run -d nginx                    # detached mode
docker run -d -p 8080:80 nginx        # port mapping
docker run -d --name mynginx nginx    # custom name
docker run -it ubuntu bash            # interactive terminal

# List containers
docker ps           # running containers
docker ps -a        # all containers

# Stop container
docker stop container_id
docker stop container_name

# Start stopped container
docker start container_id

# Restart container
docker restart container_id

# Remove container
docker rm container_id
docker rm -f container_id  # force remove running container

# View logs
docker logs container_id
docker logs -f container_id  # follow/stream logs

# Execute command in running container
docker exec -it container_id bash
docker exec container_id ls /app

# View container details
docker inspect container_id

# View resource usage
docker stats
docker stats container_id

# Copy files to/from container
docker cp file.txt container_id:/path/
docker cp container_id:/path/file.txt .
```

### Docker Compose Commands
```bash
# Start services
docker compose up
docker compose up -d          # detached mode
docker compose up --build     # rebuild images

# Stop services
docker compose down
docker compose down -v        # also remove volumes

# View logs
docker compose logs
docker compose logs -f service_name

# List services
docker compose ps

# Restart services
docker compose restart

# Execute command
docker compose exec service_name bash

# Build/rebuild services
docker compose build
```

### System Commands
```bash
# View Docker info
docker info

# View disk usage
docker system df

# Clean up everything
docker system prune
docker system prune -a        # remove all unused images
docker system prune -a --volumes  # remove everything

# Remove all stopped containers
docker container prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune
```

### Network Commands
```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Connect container to network
docker network connect mynetwork container_id

# Inspect network
docker network inspect mynetwork

# Remove network
docker network rm mynetwork
```

### Volume Commands
```bash
# List volumes
docker volume ls

# Create volume
docker volume create myvolume

# Inspect volume
docker volume inspect myvolume

# Remove volume
docker volume rm myvolume

# Remove unused volumes
docker volume prune
```

---

## 🚀 Project Setup

### Project Structure
```
project-root/
├── backend/                 # Spring Boot
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
├── frontend/                # React TypeScript
│   ├── src/
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml
├── docker-compose.debug.yml
└── README.md
```

### 1. Spring Boot Dockerfile

**backend/Dockerfile**
```dockerfile
# Multi-stage build for smaller image

# Stage 1: Build
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app

# Copy pom.xml and download dependencies (cached layer)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy source code and build
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Create non-root user for security
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copy jar from build stage
COPY --from=build /app/target/*.jar app.jar

# Expose application port
EXPOSE 8080

# Expose debug port
EXPOSE 5005

# Run with debug enabled
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

**backend/Dockerfile.debug** (for debugging)
```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app

# Install Maven
RUN apk add --no-cache maven

# Copy pom.xml and download dependencies
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy source code
COPY src ./src

# Expose ports
EXPOSE 8080
EXPOSE 5005

# Run with Maven Spring Boot plugin (hot reload)
CMD ["mvn", "spring-boot:run", "-Dspring-boot.run.jvmArguments=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005"]
```

### 2. React TypeScript Dockerfile

**frontend/Dockerfile**
```dockerfile
# Multi-stage build

# Stage 1: Build
FROM node:18-alpine AS build
WORKDIR /app

# Copy package files
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Stage 2: Production with Nginx
FROM nginx:alpine
WORKDIR /usr/share/nginx/html

# Remove default nginx files
RUN rm -rf ./*

# Copy built files from build stage
COPY --from=build /app/build .

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**frontend/Dockerfile.dev** (for development)
```dockerfile
FROM node:18-alpine
WORKDIR /app

# Copy package files
COPY package.json package-lock.json ./

# Install dependencies
RUN npm install

# Copy source code
COPY . .

# Expose port
EXPOSE 3000

# Start development server
CMD ["npm", "start"]
```

**frontend/nginx.conf**
```nginx
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    # Enable gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Handle React Router
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # API proxy (if backend is separate)
    location /api {
        proxy_pass http://backend:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 3. Docker Compose - Production Mode

**docker-compose.yml**
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    container_name: project-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: myappdb
      POSTGRES_USER: myappuser
      POSTGRES_PASSWORD: myapppassword
      PGDATA: /var/lib/postgresql/data/pgdata
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql  # Optional: initialization script
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myappuser -d myappdb"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Spring Boot Backend
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: project-backend
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      # Spring profiles
      SPRING_PROFILES_ACTIVE: prod
      
      # Database configuration
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/myappdb
      SPRING_DATASOURCE_USERNAME: myappuser
      SPRING_DATASOURCE_PASSWORD: myapppassword
      
      # JPA/Hibernate
      SPRING_JPA_HIBERNATE_DDL_AUTO: update
      SPRING_JPA_SHOW_SQL: "false"
      SPRING_JPA_PROPERTIES_HIBERNATE_DIALECT: org.hibernate.dialect.PostgreSQLDialect
      
      # Server configuration
      SERVER_PORT: 8080
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/actuator/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  # React Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: project-frontend
    restart: unless-stopped
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  postgres_data:
    driver: local
```

### 4. Docker Compose - Debug Mode

**docker-compose.debug.yml**
```yaml
version: '3.8'

services:
  # PostgreSQL Database (same as production)
  postgres:
    image: postgres:15-alpine
    container_name: project-postgres-debug
    restart: unless-stopped
    environment:
      POSTGRES_DB: myappdb
      POSTGRES_USER: myappuser
      POSTGRES_PASSWORD: myapppassword
      PGDATA: /var/lib/postgresql/data/pgdata
    ports:
      - "5432:5432"
    volumes:
      - postgres_data_debug:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myappuser -d myappdb"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Spring Boot Backend - Debug Mode
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.debug
    container_name: project-backend-debug
    restart: unless-stopped
    ports:
      - "8080:8080"      # Application port
      - "5005:5005"      # Debug port (JPDA)
    environment:
      # Spring profiles
      SPRING_PROFILES_ACTIVE: dev
      
      # Database configuration
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/myappdb
      SPRING_DATASOURCE_USERNAME: myappuser
      SPRING_DATASOURCE_PASSWORD: myapppassword
      
      # JPA/Hibernate - More verbose in debug
      SPRING_JPA_HIBERNATE_DDL_AUTO: update
      SPRING_JPA_SHOW_SQL: "true"
      SPRING_JPA_PROPERTIES_HIBERNATE_FORMAT_SQL: "true"
      SPRING_JPA_PROPERTIES_HIBERNATE_DIALECT: org.hibernate.dialect.PostgreSQLDialect
      
      # Logging
      LOGGING_LEVEL_ROOT: INFO
      LOGGING_LEVEL_COM_YOURPACKAGE: DEBUG
      
      # DevTools (if using Spring Boot DevTools)
      SPRING_DEVTOOLS_RESTART_ENABLED: "true"
      SPRING_DEVTOOLS_LIVERELOAD_ENABLED: "true"
    volumes:
      # Mount source code for hot reload
      - ./backend/src:/app/src
      - backend_m2:/root/.m2  # Cache Maven dependencies
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - app-network

  # React Frontend - Development Mode
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    container_name: project-frontend-debug
    restart: unless-stopped
    ports:
      - "3000:3000"      # Development server port
    environment:
      - REACT_APP_API_URL=http://localhost:8080
      - CHOKIDAR_USEPOLLING=true  # Enable hot reload in Docker
    volumes:
      # Mount source code for hot reload
      - ./frontend/src:/app/src
      - ./frontend/public:/app/public
      - frontend_node_modules:/app/node_modules  # Prevent overwriting node_modules
    depends_on:
      - backend
    networks:
      - app-network
    stdin_open: true  # Keep stdin open
    tty: true         # Allocate pseudo-TTY

networks:
  app-network:
    driver: bridge

volumes:
  postgres_data_debug:
    driver: local
  backend_m2:
    driver: local
  frontend_node_modules:
    driver: local
```

---

## 🐛 Debugging Configuration

### IntelliJ IDEA / VS Code Spring Boot Debug Setup

#### IntelliJ IDEA
1. Run docker compose in debug mode:
   ```bash
   docker compose -f docker-compose.debug.yml up
   ```

2. Create Remote Debug Configuration:
   - Run → Edit Configurations → Add New → Remote JVM Debug
   - Name: Docker Debug
   - Host: localhost
   - Port: 5005
   - Use module classpath: [your-module]

3. Set breakpoints and click Debug

#### VS Code
Create `.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "java",
      "name": "Docker Remote Debug",
      "request": "attach",
      "hostName": "localhost",
      "port": 5005,
      "projectName": "your-project-name"
    }
  ]
}
```

### React TypeScript Debug Setup

#### Chrome DevTools
1. Run frontend in debug mode
2. Open http://localhost:3000
3. Press F12 → Sources tab
4. Source maps are enabled automatically
5. Set breakpoints in original TypeScript files

#### VS Code
Create `.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Debug React in Chrome",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/frontend/src",
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```

### Database Debugging

#### Connect to PostgreSQL in Docker
```bash
# Using psql in container
docker compose exec postgres psql -U myappuser -d myappdb

# Using external client
Host: localhost
Port: 5432
Database: myappdb
Username: myappuser
Password: myapppassword
```

#### Common PostgreSQL Commands
```sql
-- List databases
\l

-- Connect to database
\c myappdb

-- List tables
\dt

-- Describe table
\d table_name

-- List all schemas
\dn

-- Show running queries
SELECT * FROM pg_stat_activity;

-- Exit
\q
```

---

## 🎯 Running the Project

### Production Mode
```bash
# Build and start all services
docker compose up --build

# Run in background
docker compose up -d

# View logs
docker compose logs -f

# Stop all services
docker compose down

# Stop and remove volumes (CAUTION: deletes data)
docker compose down -v
```

### Debug Mode
```bash
# Build and start in debug mode
docker compose -f docker-compose.debug.yml up --build

# Run in background
docker compose -f docker-compose.debug.yml up -d

# View logs
docker compose -f docker-compose.debug.yml logs -f backend

# Restart specific service
docker compose -f docker-compose.debug.yml restart backend

# Stop
docker compose -f docker-compose.debug.yml down
```

### Access Points
- **Frontend**: http://localhost (production) or http://localhost:3000 (debug)
- **Backend API**: http://localhost:8080
- **Backend Debug Port**: localhost:5005
- **PostgreSQL**: localhost:5432

---

## ✅ Best Practices

### 1. Dockerfile Best Practices

```dockerfile
# ✅ DO: Use specific versions
FROM node:18-alpine

# ❌ DON'T: Use latest tag
FROM node:latest

# ✅ DO: Multi-stage builds for smaller images
FROM maven:3.9 AS build
# ... build steps
FROM eclipse-temurin:17-jre-alpine
COPY --from=build /app/target/*.jar app.jar

# ✅ DO: Order instructions from least to most frequently changing
COPY package.json package-lock.json ./
RUN npm ci
COPY . .

# ✅ DO: Use .dockerignore
# Create .dockerignore file with:
node_modules
.git
.env
*.log

# ✅ DO: Run as non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# ✅ DO: Use COPY instead of ADD (unless extracting tar)
COPY app.jar /app/

# ✅ DO: Combine RUN commands to reduce layers
RUN apt-get update && \
    apt-get install -y package1 package2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 2. Docker Compose Best Practices

```yaml
# ✅ DO: Use version control
version: '3.8'

# ✅ DO: Use health checks
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
  interval: 30s
  timeout: 10s
  retries: 3

# ✅ DO: Use depends_on with conditions
depends_on:
  postgres:
    condition: service_healthy

# ✅ DO: Use named volumes for persistence
volumes:
  postgres_data:
    driver: local

# ✅ DO: Use environment files for sensitive data
env_file:
  - .env

# ✅ DO: Set restart policies
restart: unless-stopped

# ✅ DO: Limit resources (production)
deploy:
  resources:
    limits:
      cpus: '0.5'
      memory: 512M
```

### 3. Security Best Practices

```dockerfile
# 1. Use minimal base images
FROM alpine:3.18

# 2. Don't run as root
USER nonroot

# 3. Scan images for vulnerabilities
# docker scan myimage:tag

# 4. Use secrets for sensitive data
# Don't hardcode passwords in docker-compose.yml

# 5. Keep images updated
# Regularly rebuild with latest base images

# 6. Limit container capabilities
# security_opt:
#   - no-new-privileges:true

# 7. Use read-only file systems where possible
# read_only: true
```

### 4. Performance Best Practices

```dockerfile
# 1. Leverage build cache
# Copy dependency files first, then source code

# 2. Minimize layers
# Combine RUN commands

# 3. Use .dockerignore
# Exclude unnecessary files

# 4. Use multi-stage builds
# Separate build and runtime stages

# 5. Clean up in same layer
RUN apt-get update && \
    apt-get install -y package && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

---

## 🔧 Troubleshooting

### Common Issues and Solutions

#### 1. Port Already in Use
```bash
# Error: "port is already allocated"

# Find process using port
# Linux/Mac:
sudo lsof -i :8080
# Windows:
netstat -ano | findstr :8080

# Kill process or change port in docker-compose.yml
ports:
  - "8081:8080"  # Host:Container
```

#### 2. Container Keeps Restarting
```bash
# Check logs
docker compose logs backend

# Common causes:
# - Application crash
# - Wrong environment variables
# - Database not ready (use depends_on with health check)
# - Port conflicts
```

#### 3. Cannot Connect to Database
```yaml
# ✅ Solution: Ensure database is in same network
# Use service name (postgres) not localhost
SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/myappdb

# ✅ Solution: Wait for database to be ready
depends_on:
  postgres:
    condition: service_healthy
```

#### 4. Changes Not Reflecting
```bash
# Rebuild images
docker compose up --build

# For debug mode with volumes, restart container
docker compose -f docker-compose.debug.yml restart backend

# Clear everything and rebuild
docker compose down -v
docker compose up --build
```

#### 5. Out of Disk Space
```bash
# Check disk usage
docker system df

# Clean up
docker system prune -a --volumes

# Remove specific items
docker image prune
docker container prune
docker volume prune
```

#### 6. Permission Denied Errors (Linux)
```bash
# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker

# For volume permission issues
# Use appropriate user in Dockerfile or chmod volumes
```

#### 7. Network Issues
```bash
# Inspect network
docker network inspect project_app-network

# Recreate network
docker compose down
docker compose up
```

#### 8. Debug Port Not Accessible
```bash
# Ensure debug port is exposed in docker-compose.yml
ports:
  - "5005:5005"

# Check if port is listening
netstat -an | grep 5005

# Check firewall settings
```

---

## 📝 Useful Commands Cheat Sheet

```bash
# Build and run
docker compose up --build -d

# View logs
docker compose logs -f [service_name]

# Execute command in container
docker compose exec backend bash

# Stop all
docker compose down

# Restart service
docker compose restart backend

# Rebuild specific service
docker compose build backend

# Scale service
docker compose up -d --scale backend=3

# View running containers
docker compose ps

# Check resource usage
docker stats

# Clean everything
docker system prune -a --volumes

# Export/Import images
docker save -o myimage.tar myimage:tag
docker load -i myimage.tar

# Copy files
docker cp container_id:/path/to/file ./local/path
docker cp ./local/file container_id:/path/to/
```

---

## 🎓 Learning Resources

### Official Documentation
- [Docker Docs](https://docs.docker.com/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

### Best Practices
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### Interactive Learning
- [Play with Docker](https://labs.play-with-docker.com/)
- [Docker Tutorials](https://docker-curriculum.com/)

---

## 📋 Quick Start Checklist

- [ ] Install Docker and Docker Compose
- [ ] Verify installation: `docker --version`
- [ ] Create project structure
- [ ] Create Dockerfiles for backend and frontend
- [ ] Create docker-compose.yml
- [ ] Create docker-compose.debug.yml
- [ ] Create .dockerignore files
- [ ] Test production mode: `docker compose up`
- [ ] Test debug mode: `docker compose -f docker-compose.debug.yml up`
- [ ] Configure IDE for remote debugging
- [ ] Test database connection
- [ ] Test frontend-backend communication
- [ ] Set up hot reload for development
- [ ] Document environment variables
- [ ] Add health checks
- [ ] Test cleanup: `docker compose down -v`

---

## 🚀 Next Steps

1. **Learn Kubernetes** - Container orchestration at scale
2. **CI/CD Integration** - Automate building and deployment
3. **Docker Security** - Vulnerability scanning, secrets management
4. **Monitoring** - Prometheus, Grafana for container monitoring
5. **Cloud Deployment** - AWS ECS, Azure Container Instances, Google Cloud Run

---

**Happy Dockerizing! 🐳**
