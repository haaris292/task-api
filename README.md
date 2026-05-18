# Task API

A RESTful API built with Go for managing tasks. This application demonstrates a simple task management system with a PostgreSQL database backend, containerized with Docker, and deployable on Kubernetes.

## Features

- **Task Management**: Create, retrieve, and delete tasks
- **PostgreSQL Database**: Persistent data storage with SQL schema
- **RESTful API**: Clean HTTP endpoints for task operations
- **Docker Support**: Multi-stage Docker build for optimized container images
- **Docker Compose**: Easy local development setup with database
- **Kubernetes Ready**: Complete K8s manifests for production deployment
- **Health Checks**: Built-in health endpoint for monitoring

## Table of Contents

- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup & Installation](#setup--installation)
- [Running the Application](#running-the-application)
- [API Endpoints](#api-endpoints)
- [Docker](#docker)
- [Kubernetes Deployment](#kubernetes-deployment)
- [Troubleshooting](#troubleshooting)
- [Development](#development)
- [Contributing](#contributing)

---

## Quick Start

### Clone the Repository

```bash
git clone https://github.com/haaris292/task-api.git
cd task-api
```

### Run with Docker Compose

```bash
docker-compose up -d
```

The API will be available at `http://localhost:8080`

---

## Project Structure

```
task-api/
├── cmd/
│   └── server/
│       └── main.go              # Application entry point
├── internal/
│   └── database/
│       └── postgres.go          # PostgreSQL connection logic
├── k8s/                         # Kubernetes manifests
│   ├── task-api-deployment.yaml
│   ├── task-api-service.yaml
│   ├── postgres-deployment.yaml
│   ├── postgres-service.yaml
│   └── postgres-pvc.yaml
├── Dockerfile                   # Multi-stage Docker build
├── docker-compose.yml           # Local development setup
├── init.sql                     # Database schema initialization
├── go.mod                       # Go module definition
├── go.sum                       # Go module checksums
├── .env.example                 # Environment variables template
└── README.md                    # This file
```

---

## Prerequisites

### For Local Development

- **Go 1.22+** - [Download](https://golang.org/dl/)
- **PostgreSQL 17** - [Download](https://www.postgresql.org/download/) (or use Docker)

### For Docker

- **Docker 20.10+** - [Download](https://docs.docker.com/get-docker/)
- **Docker Compose 2.0+** - [Download](https://docs.docker.com/compose/install/)

### For Kubernetes

- **kubectl** - [Download](https://kubernetes.io/docs/tasks/tools/)
- **Kubernetes cluster** (1.20+) - Minikube, EKS, GKE, AKS, or any other K8s distribution

---

## Setup & Installation

### Local Setup for Development

#### 1. Clone the Repository

```bash
git clone https://github.com/haaris292/task-api.git
cd task-api
```

#### 2. Install Dependencies

```bash
go mod download
```

#### 3. Setup Environment Variables

Copy the example environment file:

```bash
cp .env.example .env
```

Edit `.env` with your PostgreSQL credentials:

```env
DB_HOST=localhost
DB_PORT=5432
DB_USER=taskuser
DB_PASSWORD=yourpassword
DB_NAME=taskdb
APP_PORT=8080
```

#### 4. Setup PostgreSQL Database

**Option A: Using Docker (Recommended)**

```bash
docker run --name postgres-db \
  -e POSTGRES_USER=taskuser \
  -e POSTGRES_PASSWORD=yourpassword \
  -e POSTGRES_DB=taskdb \
  -p 5432:5432 \
  -d \
  postgres:17
```

**Option B: Using Local PostgreSQL**

Connect to your PostgreSQL instance and run:

```sql
CREATE TABLE IF NOT EXISTS tasks (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    description TEXT,
    status TEXT DEFAULT 'pending'
);
```

#### 5. Install Go Dependencies

```bash
go mod tidy
```

---

## Running the Application

### Local Development (No Docker)

#### 1. Start PostgreSQL (if not already running)

```bash
# Using Docker
docker start postgres-db
```

#### 2. Run the Application

```bash
go run ./cmd/server/main.go
```

Expected output:

```
Successfully connected to PostgreSQL
Connected to PostgreSQL
Task API running on port 8080
```

#### 3. Test the API

```bash
# Health check
curl http://localhost:8080/health

# Get all tasks
curl http://localhost:8080/tasks

# Create a task
curl -X POST http://localhost:8080/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Buy groceries","description":"Milk, eggs, bread","status":"pending"}'

# Delete a task
curl -X DELETE http://localhost:8080/tasks/1
```

### Docker Compose (Recommended for Local Development)

#### Start the Application

```bash
docker-compose up -d
```

This command:

- Builds the Docker image from Dockerfile
- Starts PostgreSQL container
- Starts the Task API container
- Automatically initializes the database schema
- Exposes API on port 8080

#### View Logs

```bash
# View logs for all services
docker-compose logs -f

# View logs for specific service
docker-compose logs -f task-api
docker-compose logs -f postgres
```

#### Test the Application

```bash
# Health check
curl http://localhost:8080/health

# Get all tasks
curl http://localhost:8080/tasks

# Create a task
curl -X POST http://localhost:8080/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Learn Kubernetes","description":"Complete K8s course"}'

# Delete a task
curl -X DELETE http://localhost:8080/tasks/1
```

#### Stop the Application

```bash
# Stop and keep containers
docker-compose stop

# Stop and remove containers, volumes, networks
docker-compose down

# Stop and remove everything including volumes
docker-compose down -v
```

---

## API Endpoints

### Health Check

Returns the health status of the API.

```http
GET /health
```

**Response:**

```
200 OK
OK
```

### Get All Tasks

Retrieve all tasks from the database.

```http
GET /tasks
```

**Response:**

```json
[
  {
    "id": 1,
    "title": "Buy groceries",
    "description": "Milk, eggs, bread",
    "status": "pending"
  },
  {
    "id": 2,
    "title": "Complete project",
    "description": "Finish the API",
    "status": "in-progress"
  }
]
```

### Create a Task

Create a new task.

```http
POST /tasks
Content-Type: application/json

{
  "title": "Task title (required)",
  "description": "Task description (optional)",
  "status": "pending (optional, defaults to 'pending')"
}
```

**Response:**

```json
{
  "id": 3,
  "title": "New task",
  "description": "Task details",
  "status": "pending"
}
```

**Status Codes:**

- `201 Created` - Task successfully created
- `400 Bad Request` - Missing title or invalid JSON
- `500 Internal Server Error` - Database error

### Delete a Task

Delete a task by ID.

```http
DELETE /tasks/{id}
```

**Response:**

```
200 OK
Task deleted
```

**Status Codes:**

- `200 OK` - Task successfully deleted
- `400 Bad Request` - Invalid task ID
- `404 Not Found` - Task not found
- `500 Internal Server Error` - Database error

---

## Docker

### Building the Docker Image

The project uses a multi-stage Dockerfile for optimization:

```bash
# Build the image
docker build -t haaris292/task-api:v1 .

# Build with custom tag
docker build -t my-task-api:latest .
```

### Running with Docker

```bash
# Run with environment variables
docker run -d \
  --name task-api \
  -p 8080:8080 \
  -e DB_HOST=postgres \
  -e DB_PORT=5432 \
  -e DB_USER=taskuser \
  -e DB_PASSWORD=taskpassword \
  -e DB_NAME=taskdb \
  --link postgres-db:postgres \
  haaris292/task-api:v1
```

### Docker Compose Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose stop

# Stop and remove services
docker-compose down

# Remove volumes
docker-compose down -v

# Rebuild images
docker-compose build

# Rebuild and start
docker-compose up -d --build

# View service status
docker-compose ps

# Execute command in container
docker-compose exec task-api /bin/sh
docker-compose exec postgres psql -U taskuser -d taskdb
```

### Docker Troubleshooting

#### Container won't start

```bash
# Check logs
docker-compose logs task-api

# Inspect container
docker inspect <container_id>

# Check resource usage
docker stats
```

#### Database connection issues

```bash
# Check if postgres container is running
docker-compose ps

# Restart postgres service
docker-compose restart postgres

# Check postgres logs
docker-compose logs postgres

# Test connection from app container
docker-compose exec task-api sh -c 'nc -zv postgres 5432'
```

#### Port conflicts

```bash
# Check which process is using port 8080
lsof -i :8080

# Change port in docker-compose.yml
# Change "8080:8080" to "9090:8080"

# Or kill the conflicting process
kill -9 <PID>
```

#### Clear Docker cache and rebuild

```bash
# Remove dangling images
docker image prune -a

# Remove all stopped containers
docker container prune

# Rebuild without cache
docker-compose build --no-cache

# Full rebuild and restart
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d
```

---

## Kubernetes Deployment

### Prerequisites

- Kubernetes cluster running
- `kubectl` configured to access your cluster
- Docker image pushed to a registry (e.g., Docker Hub)

### Kubernetes Manifests

The `k8s/` directory contains:

- `postgres-pvc.yaml` - PersistentVolumeClaim for database storage
- `postgres-deployment.yaml` - PostgreSQL deployment
- `postgres-service.yaml` - PostgreSQL service
- `task-api-deployment.yaml` - Task API deployment
- `task-api-service.yaml` - Task API service

### Deploy to Kubernetes

#### 1. Create Namespace (Optional)

```bash
kubectl create namespace task-api
```

#### 2. Deploy PostgreSQL

```bash
# Apply PostgreSQL manifests
kubectl apply -f k8s/postgres-pvc.yaml
kubectl apply -f k8s/postgres-deployment.yaml
kubectl apply -f k8s/postgres-service.yaml

# Or apply all at once
kubectl apply -f k8s/ -l app=postgres
```

#### 3. Wait for PostgreSQL to be Ready

```bash
kubectl wait --for=condition=ready pod -l app=postgres --timeout=300s
```

#### 4. Deploy Task API

```bash
# Apply Task API manifests
kubectl apply -f k8s/task-api-deployment.yaml
kubectl apply -f k8s/task-api-service.yaml

# Or apply all at once
kubectl apply -f k8s/
```

#### 5. Verify Deployment

```bash
# Check pod status
kubectl get pods -l app=task-api
kubectl get pods -l app=postgres

# Get detailed pod information
kubectl describe pod <pod-name>

# View deployment status
kubectl get deployments
```

### Accessing the Application

#### Port Forward (for testing)

```bash
# Forward port 8080 to localhost
kubectl port-forward svc/task-api 8080:8080

# Access the API
curl http://localhost:8080/health
curl http://localhost:8080/tasks
```

#### Using Service (LoadBalancer or NodePort)

```bash
# Get service details
kubectl get svc task-api

# For LoadBalancer service, get external IP
kubectl get svc task-api -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

### Kubernetes Troubleshooting

#### Check Pod Status

```bash
# View pod logs
kubectl logs <pod-name>
kubectl logs <pod-name> -f  # Follow logs

# Check pod events
kubectl describe pod <pod-name>

# Get pod details
kubectl get pods -o wide
```

#### Database Connection Issues

```bash
# Check if postgres pod is running
kubectl get pods -l app=postgres

# Check postgres logs
kubectl logs -l app=postgres

# Test connection from app pod
kubectl exec -it <task-api-pod> -- sh
# Inside container:
nc -zv postgres 5432

# Check service DNS resolution
kubectl exec -it <task-api-pod> -- nslookup postgres
```

#### Restart Deployments

```bash
# Restart all pods in a deployment
kubectl rollout restart deployment/task-api
kubectl rollout restart deployment/postgres

# Check rollout status
kubectl rollout status deployment/task-api
```

#### Delete and Redeploy

```bash
# Delete all resources
kubectl delete -f k8s/

# Verify deletion
kubectl get all

# Redeploy
kubectl apply -f k8s/
```

#### Check Resource Usage

```bash
# View resource requests/limits
kubectl describe node

# Check pod resource usage
kubectl top pods
kubectl top nodes
```

#### View all resources

```bash
# List all resources
kubectl get all

# List specific resource types
kubectl get deployments
kubectl get services
kubectl get pods
kubectl get pvc
```

---

## Troubleshooting

### General Issues

#### Application fails to start

```bash
# Check if all dependencies are installed
go mod tidy
go mod verify

# Rebuild
go build -o server ./cmd/server

# Check for port conflicts
lsof -i :8080
```

#### Database connection fails

```bash
# Test PostgreSQL connection
psql -h localhost -U taskuser -d taskdb

# Check environment variables
echo $DB_HOST
echo $DB_PORT
echo $DB_USER
echo $DB_PASSWORD
echo $DB_NAME

# Verify database exists
psql -h localhost -U taskuser -l
```

#### Port already in use

```bash
# Find process using port 8080
lsof -i :8080

# Kill the process
kill -9 <PID>

# Or use a different port
APP_PORT=9090 go run ./cmd/server/main.go
```

### Docker Issues

```bash
# View Docker logs
docker logs <container_id>

# Inspect container
docker inspect <container_id>

# Execute command in running container
docker exec -it <container_id> /bin/sh

# Check container network
docker network ls
docker network inspect <network_name>
```

### Kubernetes Issues

```bash
# Check cluster health
kubectl cluster-info
kubectl get nodes

# View all events
kubectl get events

# Check resource status
kubectl describe deployment task-api
kubectl describe deployment postgres

# Test API from within cluster
kubectl run -it --rm debug --image=busybox --restart=Never -- /bin/sh
# Inside: wget -qO- http://task-api:8080/health
```

---

## Development

### Project Structure

- **cmd/server/main.go** - Application entry point with HTTP handlers
- **internal/database/postgres.go** - Database connection and configuration
- **init.sql** - Database schema definition
- **Dockerfile** - Multi-stage build for production container

### Building from Source

```bash
# Download dependencies
go mod download

# Build binary
go build -o server ./cmd/server

# Run binary
./server

# Clean build artifacts
go clean
```

### Code Organization

- **Task Struct** - Defines task model with JSON serialization
- **Handlers**:
  - `healthHandler()` - Health check endpoint
  - `getTasksHandler()` - Fetch all tasks
  - `createTaskHandler()` - Create new task
  - `deleteTaskHandler()` - Delete task by ID
  - `tasksHandler()` - Router for GET/POST methods

### Adding New Features

1. Define new HTTP handler function
2. Register route in `main()`
3. Test the endpoint
4. Update this README with new endpoints

Example:

```go
func updateTaskHandler(w http.ResponseWriter, r *http.Request) {
    // Implementation here
}

// In main():
http.HandleFunc("/tasks/update", updateTaskHandler)
```

---

## Contributing

We welcome contributions! Please follow these steps:

1. **Fork the repository**

   ```bash
   # Visit https://github.com/haaris292/task-api and click "Fork"
   ```

2. **Clone your fork**

   ```bash
   git clone https://github.com/<your-username>/task-api.git
   cd task-api
   ```

3. **Create a feature branch**

   ```bash
   git checkout -b feature/your-feature-name
   ```

4. **Make your changes**
   - Follow the existing code style
   - Update tests if applicable
   - Update README if adding new features

5. **Commit your changes**

   ```bash
   git add .
   git commit -m "feat: Add your feature description"
   ```

6. **Push to your fork**

   ```bash
   git push origin feature/your-feature-name
   ```

7. **Create a Pull Request**
   - Visit your fork on GitHub
   - Click "Create Pull Request"
   - Provide a clear description of your changes
   - Link any related issues

### Development Tips

- Use `docker-compose up -d` for quick local testing
- Check logs with `docker-compose logs -f`
- Use `curl` or Postman to test endpoints
- Run database queries directly: `docker-compose exec postgres psql -U taskuser -d taskdb`

---

## License

This project is open source and available under the MIT License.

---

## Support

For issues, questions, or suggestions:

- Open an [Issue](https://github.com/haaris292/task-api/issues)
- Create a [Discussion](https://github.com/haaris292/task-api/discussions)
- Submit a [Pull Request](https://github.com/haaris292/task-api/pulls)

---

## Quick Reference Commands

### Local Development

```bash
docker-compose up -d                    # Start all services
docker-compose logs -f                  # View logs
docker-compose down                     # Stop services
go run ./cmd/server/main.go             # Run without Docker
```

### Docker

```bash
docker build -t haaris292/task-api:v1 .
docker run -d --name task-api -p 8080:8080 haaris292/task-api:v1
docker logs -f task-api
docker stop task-api && docker rm task-api
```

### Kubernetes

```bash
kubectl apply -f k8s/                   # Deploy all
kubectl get all                         # View resources
kubectl logs -l app=task-api            # View logs
kubectl port-forward svc/task-api 8080:8080  # Access API
kubectl delete -f k8s/                  # Remove all
```

### API Testing

```bash
curl http://localhost:8080/health                          # Health check
curl http://localhost:8080/tasks                           # Get tasks
curl -X POST http://localhost:8080/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Test"}' \

curl -X DELETE http://localhost:8080/tasks/1               # Delete task
```

Run grafana on local port 3000
kubectl port-forward svc/monitoring-grafana 3000:80

Run prometheus on local port 9090
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090

If any changes made to help yaml files then run below to upgrade existing cluster
helm upgrade task-api ./helm/task-api
