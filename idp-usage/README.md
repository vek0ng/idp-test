# IDP Stack - Application Usage

This directory contains everything developers need to build, test, and deploy applications using the IDP Stack platform.

## 🚀 Quick Start

### 1. Create a New Application
```bash
task scaffold:create-app APP_NAME=my-api TEMPLATE=go-api
```

### 2. Start Local Development
```bash
task local:dev APP_NAME=my-api
```

### 3. Deploy to Development
```bash
task quick-start APP_NAME=my-api
```

## 📁 Directory Structure

```
idp-usage/
├── apps/                           # Application source code
│   └── sample-go-app/             # Example Go application
│       ├── main.go                # Application entry point
│       ├── Dockerfile             # Container configuration
│       └── k8s/                   # Kubernetes manifests
├── taskfiles/                     # Task automation
│   ├── application/               # Application lifecycle
│   │   ├── build.yaml            # Build automation
│   │   ├── deploy.yaml           # Deployment automation
│   │   └── test.yaml             # Testing automation
│   └── developer/                 # Developer workflows
│       ├── scaffold.yaml         # Application scaffolding
│       └── local.yaml            # Local development
├── templates/                     # Application templates
│   ├── go-api/                   # Go API template
│   ├── node-api/                 # Node.js API template
│   └── python-fastapi/           # Python FastAPI template
└── docs/                         # Documentation
    └── developer-guide.md        # Developer guide
```

## 🏗️ Available Commands

### Application Lifecycle
- `task build:help` - Build system help and commands
- `task deploy:help` - Deployment help and commands  
- `task test:help` - Testing help and commands

### Developer Workflows
- `task scaffold:help` - Application scaffolding help
- `task local:help` - Local development help

### Quick Actions
- `task help` - Show all available commands
- `task list-apps` - List all applications
- `task doctor` - Check development environment health

## 🎯 Common Workflows

### Full Development Cycle
```bash
# Build, test, and deploy
task full-cycle APP_NAME=my-api ENVIRONMENT=dev
```

### Development Cycle
```bash
# Quick build and deploy to dev
task dev-cycle APP_NAME=my-api
```

### Production Deployment
```bash
# Deploy to production with confirmation
task prod-deploy APP_NAME=my-api IMAGE_TAG=v1.2.3
```

### Local Development with Hot Reload
```bash
# Start with automatic reload on changes
task local:dev APP_NAME=my-api
```

## 📊 Application Management

### List Applications
```bash
task list-apps
```

### Application Status
```bash
task status APP_NAME=my-api PROVIDER=kind
```

### View Logs
```bash
task logs APP_NAME=my-api ENVIRONMENT=dev
```

### Clean Workspace
```bash
task clean-workspace
```

## 🔧 Configuration

### Standardized Parameters
All tasks use these standardized parameters:

- `CLUSTER_NAME` - Target cluster name (default: "idp-cluster")
- `PROVIDER` - Cloud provider (kind/aws/gcp/azure, default: "kind")
- `ENVIRONMENT` - Target environment (dev/staging/prod, default: "dev")
- `REGION` - Cloud region (default: "us-west-2")
- `APP_NAME` - Application name (default: "sample-go-app")
- `IMAGE_TAG` - Container image tag (default: "latest")

### Example Usage
```bash
task deploy:prod \
  APP_NAME=my-api \
  PROVIDER=aws \
  ENVIRONMENT=prod \
  REGION=us-east-1 \
  IMAGE_TAG=v1.2.3
```

## 🔌 Integration with Platform

### Check Platform Status
```bash
task platform-status
```

### Connect to Platform Tools
```bash
# Connect to ArgoCD
task platform-connect TOOL=argocd

# Connect to Backstage
task platform-connect TOOL=backstage
```

## 🛠️ Development Environment

### Prerequisites Check
```bash
task doctor
```

### Required Tools
- Docker (for containerization)
- kubectl (for Kubernetes interaction)
- Go 1.19+ (for Go applications)
- Node.js 18+ (for Node.js applications)
- Python 3.9+ (for Python applications)

### Optional Tools for Enhanced Development
- `air` (Go hot reload): `go install github.com/cosmtrek/air@latest`
- `nodemon` (Node.js hot reload): `npm install -g nodemon`
- `dlv` (Go debugger): `go install github.com/go-delve/delve/cmd/dlv@latest`

## 📚 Examples

### Create Different Application Types

#### Go API
```bash
task scaffold:create-app APP_NAME=go-service TEMPLATE=go-api
```

#### Node.js API
```bash
task scaffold:create-app APP_NAME=node-service TEMPLATE=node-api
```

#### Python FastAPI
```bash
task scaffold:create-app APP_NAME=python-service TEMPLATE=python-fastapi
```

### Testing Workflows

#### Unit Tests
```bash
task test:unit APP_NAME=my-api
```

#### Integration Tests
```bash
task test:integration APP_NAME=my-api
```

#### Load Tests
```bash
task test:load APP_NAME=my-api CONCURRENT_USERS=50
```

#### Security Tests
```bash
task test:security APP_NAME=my-api
```

### Build Workflows

#### Development Build
```bash
task build:dev APP_NAME=my-api
```

#### Production Build
```bash
task build:release APP_NAME=my-api IMAGE_TAG=v1.2.3
```

#### Multi-Architecture Build
```bash
task build:multi-arch APP_NAME=my-api PLATFORMS=linux/amd64,linux/arm64
```

### Deployment Workflows

#### Deploy to Development
```bash
task deploy:dev APP_NAME=my-api PROVIDER=kind
```

#### Deploy to Staging
```bash
task deploy:staging APP_NAME=my-api PROVIDER=aws
```

#### Deploy to Production
```bash
task deploy:prod APP_NAME=my-api PROVIDER=aws IMAGE_TAG=v1.2.3
```

#### Blue-Green Deployment
```bash
task deploy:blue-green APP_NAME=my-api ENVIRONMENT=prod
```

## 🔍 Troubleshooting

### Common Issues

1. **Application not found**
   ```bash
   task list-apps  # Check available applications
   ```

2. **Build failures**
   ```bash
   task build:validate APP_NAME=my-api  # Validate configuration
   task doctor  # Check environment
   ```

3. **Deployment failures**
   ```bash
   task deploy:validate APP_NAME=my-api  # Validate deployment config
   task platform-status  # Check platform status
   ```

4. **Local development issues**
   ```bash
   task local:setup  # Setup local environment
   task local:status-services  # Check local services
   ```

### Debug Mode
Enable debug output by setting:
```bash
export DEBUG=true
task <command>
```

## 📖 Documentation

### Detailed Guides
- [Developer Guide](docs/developer-guide.md) - Comprehensive development guide
- [Platform Integration](../idp-builder/README.md) - Platform setup and management

### Help System
```bash
task help                    # Main help
task build:help             # Build system help
task deploy:help            # Deployment help
task test:help              # Testing help
task scaffold:help          # Scaffolding help
task local:help             # Local development help
```

### Examples and Tutorials
```bash
task examples               # Show common usage patterns
task docs                   # Show documentation overview
```

## 🤝 Contributing

When adding new applications or modifying workflows:

1. Follow the established directory structure
2. Use standardized parameters consistently  
3. Include comprehensive help text in tasks
4. Add validation for required parameters
5. Update documentation and examples

## 🔗 Related Resources

- [IDP Builder](../idp-builder/) - Platform setup and management
- [Task Documentation](https://taskfile.dev/) - Task runner documentation
- [Kubernetes Documentation](https://kubernetes.io/docs/) - Kubernetes resources
- [Docker Documentation](https://docs.docker.com/) - Container documentation

The foundation and directory structure have been established with:
- ✅ Complete directory structure  
- ✅ Functional Taskfile.yaml with help system
- ✅ Directory structure validation
- ✅ Application and template listing capabilities
- ✅ Developer-focused parameter framework

## Next Phases

**Phase 4** will add:
- Sample applications (Go, Node.js, Python)
- Build and deployment workflows
- CI/CD pipeline templates
- Developer scaffolding tools

**Phase 5** will add:
- Backstage templates for project creation
- Developer onboarding workflows
- Integration with IDP Builder platform

## Configuration

Default configuration values:
- **Application**: `sample-app`
- **Environment**: `dev`
- **Registry**: `ghcr.io/vek0ng`
- **Namespace**: `default`
- **Domain**: `local.dev`

Override with task parameters:
```bash
task version APP_NAME=my-service ENVIRONMENT=prod REGISTRY=my-registry.com
```

## Developer Workflow

1. **Discover**: `task list-templates` to see available project templates
2. **Create**: Use templates to scaffold new applications (Phase 4)
3. **Build**: `task build` to build container images (Phase 4)
4. **Deploy**: `task deploy` to deploy applications (Phase 4)

## Requirements

- Task runner installed
- Bash shell
- Directory structure in place
- Access to container registry (for Phase 4)

This is the developer-facing layer of the IDP Stack, designed for application developers and development teams.
