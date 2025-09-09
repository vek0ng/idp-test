# Sample Go Application

This is a sample Go web application that demonstrates basic HTTP server functionality with the Gin framework. It's part of the IDP Stack usage examples.

## Features

- Simple HTTP server using Gin framework
- CORS support for cross-origin requests
- Environment variable configuration
- Graceful shutdown support
- Health check endpoints
- Docker containerization
- Kubernetes deployment manifests

## Environment Variables

- `PORT`: Server port (default: 8080)
- `VERSION`: Application version (displayed in response)
- `MESSAGE`: Custom message (default: "This is a silly demo")
- `DEBUG`: Enable debug logging (set to "true")
- `FAIL`: Force application to return error responses
- `NO_SIGNALS`: Disable graceful shutdown handling

## Development

### Local Development

```bash
# Run locally
go run main.go root.go

# Test the application
curl http://localhost:8080
curl http://localhost:8080?html=1
curl http://localhost:8080?fail=1
```

### Using IDP Usage Tasks

```bash
# Build the application
task application:build APP_NAME=sample-go-app

# Run tests
task application:test APP_NAME=sample-go-app

# Deploy to Kubernetes
task application:deploy APP_NAME=sample-go-app ENVIRONMENT=dev
```

## Docker

```bash
# Build Docker image
docker build -t sample-go-app:latest .

# Run container
docker run -p 8080:8080 -e VERSION=1.0.0 sample-go-app:latest
```

## Kubernetes

The `k8s/` directory contains Kubernetes manifests for:
- Deployment with 2 replicas
- Service (ClusterIP)
- Ingress with nginx

```bash
# Deploy to Kubernetes
kubectl apply -f k8s/deployment.yaml
```

## API Endpoints

- `GET /` - Returns a simple message
  - Query parameters:
    - `html=1` - Return HTML formatted response
    - `fail=1` - Return error response (for testing)

## Architecture

This is a minimal microservice that demonstrates:
- Cloud-native application patterns
- Container-first development
- Kubernetes-ready deployment
- Environment-based configuration
- Health monitoring capabilities

Perfect for demonstrating IDP workflows, GitOps deployment, and developer onboarding processes.
