All the documentation needed to run the app 

This is exactly the right thing to do.

Documenting:

* commands
* workflows
* debugging
* architecture

is how you actually retain DevOps/backend/platform knowledge.

Below is a practical “engineering cheat sheet” for your Task API stack.

---

# Task API — Complete Command Reference

---

# 1. GO APPLICATION COMMANDS

---

# Run App Locally

```bash id="m2h0cn"
go run cmd/server/main.go
```

---

# Build Binary

```bash id="xxjlwm"
go build -o task-api ./cmd/server
```

---

# Run Binary

```bash id="x9jlwm"
./task-api
```

---

# Install Dependencies

```bash id="jlwm3m"
go get github.com/lib/pq
```

---

# Cleanup Dependencies

```bash id="r4jlwm"
go mod tidy
```

---

# Verify Module

```bash id="k8jlwm"
cat go.mod
```

---

# Build Entire Project

```bash id="z7jlwm"
go build ./...
```

---

# 2. API TESTING COMMANDS (curl)

---

# Health Check

```bash id="5xjlwm"
curl localhost:8080/health
```

Expected:

```text id="1pjlwm"
OK
```

---

# Get All Tasks

```bash id="n4jlwm"
curl localhost:8080/tasks
```

---

# Create Task

```bash id="g6jlwm"
curl -X POST localhost:8080/tasks \
-H "Content-Type: application/json" \
-d '{
  "title":"Learn Kubernetes",
  "description":"Understand pods and services"
}'
```

---

# Delete Task

```bash id="m1jlwm"
curl -X DELETE localhost:8080/tasks/1
```

---

# Example Future PUT Request

```bash id="7tjlwm"
curl -X PUT localhost:8080/tasks/1 \
-H "Content-Type: application/json" \
-d '{
  "title":"Updated Task",
  "description":"Updated description",
  "status":"completed"
}'
```

---

# Verbose API Request

Useful for debugging.

```bash id="c8jlwm"
curl -v localhost:8080/tasks
```

---

# 3. DOCKER COMMANDS

---

# Build Docker Image

```bash id="8vjlwm"
docker build -t task-api:v1 .
```

---

# List Images

```bash id="4wjlwm"
docker images
```

---

# Run Container

```bash id="h5jlwm"
docker run -p 8080:8080 task-api:v1
```

---

# Run Using Host Network

```bash id="3mjlwm"
docker run --network host task-api:v1
```

---

# List Running Containers

```bash id="u7jlwm"
docker ps
```

---

# List All Containers

```bash id="9zjlwm"
docker ps -a
```

---

# View Container Logs

```bash id="l2jlwm"
docker logs <container-id>
```

---

# Stop Container

```bash id="d1jlwm"
docker stop <container-id>
```

---

# Remove Container

```bash id="r8jlwm"
docker rm <container-id>
```

---

# Remove Image

```bash id="f0jlwm"
docker rmi task-api:v1
```

---

# Remove Unused Images

```bash id="0v’wini"
docker image prune
```

---

# Remove Unused Networks

```bash id="2rjlwm"
docker network prune
```

---

# Remove Everything Unused

```bash id="4njlwm"
docker system prune -a
```

Use carefully.

---

# 4. DOCKER COMPOSE COMMANDS

---

# Start Compose Stack

```bash id="m5jlwm"
docker compose up
```

---

# Build + Start

```bash id="g2jlwm"
docker compose up --build
```

---

# Run in Background

```bash id="q1jlwm"
docker compose up -d
```

---

# Stop Compose Stack

```bash id="5q’wini"
docker compose down
```

---

# Stop + Remove Volumes

WARNING:
Deletes PostgreSQL data.

```bash id="8sjlwm"
docker compose down -v
```

---

# Restart Specific Service

```bash id="0w’wini"
docker compose restart task-api
```

---

# View Logs

```bash id="9n4jlwm"
docker compose logs
```

---

# View Specific Service Logs

```bash id="7v5jlwm"
docker compose logs task-api
```

---

# Follow Live Logs

```bash id="4x8jlwm"
docker compose logs -f
```

---

# List Compose Containers

```bash id="3u4jlwm"
docker compose ps
```

---

# Rebuild Specific Service

```bash id="9q1jlwm"
docker compose build task-api
```

---

# 5. POSTGRESQL COMMANDS

---

# Connect to PostgreSQL

```bash id="5d3jlwm"
psql -U taskuser -d taskdb -h localhost
```

---

# Switch to postgres User

```bash id="k7xjlwm"
sudo -i -u postgres
```

---

# Open PostgreSQL Shell

```bash id="6m4jlwm"
psql
```

---

# List Databases

```sql id="r2xjlwm"
\l
```

---

# Connect to Database

```sql id="0t3jlwm"
\c taskdb
```

---

# List Tables

```sql id="z6njlwm"
\dt
```

---

# Describe Table

```sql id="2w1jlwm"
\d tasks
```

---

# View Data

```sql id="9m2jlwm"
SELECT * FROM tasks;
```

---

# Insert Row

```sql id="8h4jlwm"
INSERT INTO tasks (title, description)
VALUES ('Learn Docker', 'Containers');
```

---

# Update Row

```sql id="5j6’wini"
UPDATE tasks
SET status = 'completed'
WHERE id = 1;
```

---

# Delete Row

```sql id="u2rjlwm"
DELETE FROM tasks
WHERE id = 1;
```

---

# Exit psql

```sql id="1n8’wini"
\q
```

---

# 6. LINUX COMMANDS

---

# Current Directory

```bash id="6f9’wini"
pwd
```

---

# List Files

```bash id="m0x’wini"
ls
```

---

# Detailed List

```bash id="0z2’wini"
ls -la
```

---

# Change Directory

```bash id="5r1’wini"
cd
```

---

# Create Folder Structure

```bash id="7b4’wini"
mkdir -p cmd/server
```

---

# Remove File

```bash id="9c7’wini"
rm file.txt
```

---

# View File

```bash id="2g8’wini"
cat file.txt
```

---

# Edit File

```bash id="1t5’wini"
nano filename
```

---

# Folder Tree

```bash id="6p2’wini"
tree
```

Install if missing:

```bash id="4v7’wini"
sudo apt install tree
```

---

# Search Running Processes

```bash id="3n1’wini"
ps aux
```

---

# Check Open Ports

```bash id="8y4’wini"
ss -tulnp
```

---

# 7. DEBUGGING COMMANDS

---

# Verify App Port

```bash id="7u9’wini"
curl localhost:8080/health
```

---

# Verify PostgreSQL Port

```bash id="2e6’wini"
ss -tulnp | grep 5432
```

---

# Verify API Port

```bash id="9w3’wini"
ss -tulnp | grep 8080
```

---

# Check Container Logs

```bash id="1o7’wini"
docker compose logs task-api
```

---

# Check PostgreSQL Logs

```bash id="5i8’wini"
docker compose logs postgres
```

---

# Enter Running Container

```bash id="4k5’wini"
docker exec -it task-api sh
```

---

# Enter PostgreSQL Container

```bash id="6q2’wini"
docker exec -it postgres-db bash
```

---

# 8. IMPORTANT FILES IN YOUR PROJECT

---

# Go Entry Point

```text id="8j1’wini"
cmd/server/main.go
```

---

# Database Connection Logic

```text id="2h9’wini"
internal/database/postgres.go
```

---

# Docker Build Instructions

```text id="0f6’wini"
Dockerfile
```

---

# Multi-Container Orchestration

```text id="7x3’wini"
docker-compose.yml
```

---

# Environment Variables

```text id="3m8’wini"
.env
```

---

# Database Initialization

```text id="9r5’wini"
init.sql
```

---

# Go Module Metadata

```text id="1c4’wini"
go.mod
```

---

# 9. MOST IMPORTANT CONCEPTS YOU’VE LEARNED

| Concept               | Why It Matters          |
| --------------------- | ----------------------- |
| REST APIs             | Backend foundation      |
| PostgreSQL            | Persistent storage      |
| Docker                | Containerization        |
| Docker Compose        | Multi-container systems |
| Volumes               | Persistent data         |
| Service discovery     | Container networking    |
| Environment variables | Config management       |
| Health checks         | Reliability             |
| Logs                  | Debugging               |
| Connection pooling    | Scalability             |

---

# 10. YOUR CURRENT ARCHITECTURE

```text id="5t2’wini"
Client (curl/Postman)
        ↓
Go Task API Container
        ↓
PostgreSQL Container
        ↓
Docker Network + Volume
```

===== 16 may progress above ============

K8s troubleshooting commands

1. kubectl get pods
2. kubectl logs
3. kubectl describe pod
4. kubectl exec