# IDP Builder API Reference

This reference provides comprehensive documentation for all IDP Builder tasks, commands, and configuration options.

## Task Categories

- [Prerequisites](#prerequisites-tasks)
- [Environment Management](#environment-management-tasks)
- [Cloud Providers](#cloud-provider-tasks)
- [Infrastructure](#infrastructure-tasks)
- [Platform Components](#platform-component-tasks)
- [Workflows](#workflow-tasks)

## Prerequisites Tasks

### `prerequisites:check-all`
Performs a comprehensive check of all required tools and dependencies.

**Usage:**
```bash
task prerequisites:check-all
```

**Dependencies:** None

**Output:** Status report of all required tools

---

### `prerequisites:check-minimal`
Checks only essential tools required for basic functionality.

**Usage:**
```bash
task prerequisites:check-minimal
```

**Required Tools:**
- Docker
- kubectl
- kind
- helm
- task

---

### `prerequisites:install-prerequisites`
Automatically installs missing prerequisite tools.

**Usage:**
```bash
task prerequisites:install-prerequisites
```

**Parameters:**
- `FORCE` (optional): Force reinstallation of existing tools

**Example:**
```bash
task prerequisites:install-prerequisites FORCE=true
```

---

### `prerequisites:check-authentication`
Validates cloud provider authentication and permissions.

**Usage:**
```bash
task prerequisites:check-authentication PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Cloud provider (`aws`, `gcp`, `azure`, `kind`)

**Examples:**
```bash
task prerequisites:check-authentication PROVIDER=aws
task prerequisites:check-authentication PROVIDER=gcp
```

## Environment Management Tasks

### `environment:show-config`
Displays current environment configuration and variables.

**Usage:**
```bash
task environment:show-config
```

**Output:** Current environment variables and configuration

---

### `environment:set-variable`
Sets an environment variable for the platform.

**Usage:**
```bash
task environment:set-variable KEY=<key> VALUE=<value>
```

**Required Parameters:**
- `KEY`: Variable name
- `VALUE`: Variable value

**Example:**
```bash
task environment:set-variable KEY=CLUSTER_NAME VALUE=my-idp
```

---

### `environment:validate`
Validates the current environment configuration.

**Usage:**
```bash
task environment:validate
```

**Validates:**
- Required environment variables
- Cloud provider configuration
- Tool versions
- Network connectivity

## Cloud Provider Tasks

### `providers:configure-aws`
Configures AWS provider settings and authentication.

**Usage:**
```bash
task providers:configure-aws
```

**Environment Variables:**
- `AWS_REGION` (required): AWS region
- `AWS_ACCESS_KEY_ID` (optional): AWS access key
- `AWS_SECRET_ACCESS_KEY` (optional): AWS secret key

**Example:**
```bash
export AWS_REGION=us-east-1
task providers:configure-aws
```

---

### `providers:configure-gcp`
Configures Google Cloud provider settings.

**Usage:**
```bash
task providers:configure-gcp
```

**Environment Variables:**
- `PROJECT_ID` (required): Google Cloud project ID
- `REGION` (optional): Default region

**Example:**
```bash
export PROJECT_ID=my-gcp-project
task providers:configure-gcp
```

---

### `providers:configure-azure`
Configures Azure provider settings.

**Usage:**
```bash
task providers:configure-azure
```

**Environment Variables:**
- `AZURE_SUBSCRIPTION_ID` (required): Azure subscription ID
- `AZURE_LOCATION` (optional): Default location

---

### `providers:configure-kind`
Configures local Kind provider for development.

**Usage:**
```bash
task providers:configure-kind
```

**Options:**
- Creates local cluster configuration
- Sets up port mappings
- Configures ingress

---

### `providers:validate-provider`
Validates provider configuration and connectivity.

**Usage:**
```bash
task providers:validate-provider PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Provider to validate (`aws`, `gcp`, `azure`, `kind`)

**Example:**
```bash
task providers:validate-provider PROVIDER=aws
```

---

### `providers:show-provider-status`
Shows the current status of all configured providers.

**Usage:**
```bash
task providers:show-provider-status
```

## Infrastructure Tasks

### `cluster:create-cluster`
Creates a new Kubernetes cluster.

**Usage:**
```bash
task cluster:create-cluster CLUSTER_NAME=<name> PROVIDER=<provider>
```

**Required Parameters:**
- `CLUSTER_NAME`: Name of the cluster
- `PROVIDER`: Cloud provider (`aws`, `gcp`, `azure`, `kind`)

**Optional Parameters:**
- `REGION`: Cloud region (provider-specific default)
- `NODE_COUNT`: Number of worker nodes (default: 3)
- `INSTANCE_TYPE`: Node instance type (provider-specific)

**Examples:**
```bash
# AWS EKS cluster
task cluster:create-cluster \
  CLUSTER_NAME=my-idp-prod \
  PROVIDER=aws \
  REGION=us-east-1 \
  NODE_COUNT=5

# Local Kind cluster
task cluster:create-cluster \
  CLUSTER_NAME=my-idp-dev \
  PROVIDER=kind
```

---

### `cluster:delete-cluster`
Deletes an existing Kubernetes cluster.

**Usage:**
```bash
task cluster:delete-cluster CLUSTER_NAME=<name> PROVIDER=<provider>
```

**Required Parameters:**
- `CLUSTER_NAME`: Name of the cluster
- `PROVIDER`: Cloud provider

**Example:**
```bash
task cluster:delete-cluster CLUSTER_NAME=my-idp-dev PROVIDER=kind
```

---

### `cluster:cluster-status`
Shows the status of a Kubernetes cluster.

**Usage:**
```bash
task cluster:cluster-status
```

**Optional Parameters:**
- `CLUSTER_NAME`: Specific cluster (default: current context)

---

### `networking:setup-ingress`
Sets up ingress controller and networking components.

**Usage:**
```bash
task networking:setup-ingress PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Cloud provider

**Components Installed:**
- NGINX Ingress Controller
- cert-manager (for TLS)
- External DNS (cloud providers)

**Example:**
```bash
task networking:setup-ingress PROVIDER=aws
```

---

### `networking:configure-dns`
Configures DNS settings for the platform.

**Usage:**
```bash
task networking:configure-dns DOMAIN=<domain>
```

**Required Parameters:**
- `DOMAIN`: Base domain for the platform

**Example:**
```bash
task networking:configure-dns DOMAIN=platform.company.com
```

---

### `storage:setup-storage`
Configures storage classes and persistent volume management.

**Usage:**
```bash
task storage:setup-storage PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Cloud provider

**Storage Classes Created:**
- `fast-ssd`: High-performance SSD storage
- `standard`: Standard persistent disk
- `backup`: Backup-optimized storage

## Platform Component Tasks

### ArgoCD Tasks

#### `gitops:install-argocd`
Installs ArgoCD GitOps platform.

**Usage:**
```bash
task gitops:install-argocd
```

**Optional Parameters:**
- `CLUSTER_NAME`: Target cluster
- `NAMESPACE`: ArgoCD namespace (default: `argocd`)
- `VERSION`: ArgoCD version (default: latest stable)

**Example:**
```bash
task gitops:install-argocd NAMESPACE=argocd CLUSTER_NAME=production
```

---

#### `gitops:configure-github`
Configures GitHub integration for ArgoCD.

**Usage:**
```bash
task gitops:configure-github
```

**Required Environment Variables:**
- `GITHUB_USER`: GitHub username
- `GITHUB_TOKEN`: GitHub personal access token

---

#### `gitops:get-admin-password`
Retrieves ArgoCD admin password.

**Usage:**
```bash
task gitops:get-admin-password
```

---

#### `gitops:access-ui`
Creates port-forward to access ArgoCD UI.

**Usage:**
```bash
task gitops:access-ui
```

**Access:** http://localhost:8080

---

#### `gitops:health-check`
Performs health check on ArgoCD components.

**Usage:**
```bash
task gitops:health-check
```

---

#### `gitops:status`
Shows ArgoCD application status.

**Usage:**
```bash
task gitops:status
```

### Crossplane Tasks

#### `crossplane:install-crossplane`
Installs Crossplane infrastructure management platform.

**Usage:**
```bash
task crossplane:install-crossplane
```

**Optional Parameters:**
- `CLUSTER_NAME`: Target cluster
- `VERSION`: Crossplane version

---

#### `crossplane:install-providers`
Installs cloud provider packages for Crossplane.

**Usage:**
```bash
task crossplane:install-providers PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Cloud provider (`aws`, `gcp`, `azure`)

**Example:**
```bash
task crossplane:install-providers PROVIDER=aws
```

---

#### `crossplane:configure-credentials`
Configures cloud provider credentials for Crossplane.

**Usage:**
```bash
task crossplane:configure-credentials PROVIDER=<provider>
```

**Required Parameters:**
- `PROVIDER`: Cloud provider

**Prerequisites:**
- Valid cloud provider authentication
- Appropriate IAM/RBAC permissions

---

#### `crossplane:health-check`
Performs health check on Crossplane components.

**Usage:**
```bash
task crossplane:health-check
```

---

#### `crossplane:status`
Shows Crossplane provider and resource status.

**Usage:**
```bash
task crossplane:status
```

### Backstage Tasks

#### `backstage:install-backstage`
Installs Backstage developer portal.

**Usage:**
```bash
task backstage:install-backstage
```

**Optional Parameters:**
- `CLUSTER_NAME`: Target cluster
- `NAMESPACE`: Backstage namespace (default: `backstage`)

> **⚠️ Production Warning**: This task installs a placeholder nginx service for initial setup. Replace with a custom Backstage Docker image before production use. See [Production Deployment](installation.md#production-deployment) guide.

---

#### `backstage:configure-integrations`
Configures third-party integrations for Backstage.

**Usage:**
```bash
task backstage:configure-integrations
```

**Supported Integrations:**
- GitHub
- GitLab
- Bitbucket
- ArgoCD
- Prometheus
- Grafana

**Required Environment Variables:**
- Integration-specific tokens and URLs

---

#### `backstage:access-ui`
Creates port-forward to access Backstage UI.

**Usage:**
```bash
task backstage:access-ui
```

**Access:** http://localhost:3000

---

#### `backstage:health-check`
Performs health check on Backstage components.

**Usage:**
```bash
task backstage:health-check
```

---

#### `backstage:status`
Shows Backstage application status and metrics.

**Usage:**
```bash
task backstage:status
```

## Workflow Tasks

### `workflows:setup-development`
Complete development environment setup.

**Usage:**
```bash
task workflows:setup-development
```

**Actions:**
1. Validates prerequisites
2. Creates Kind cluster
3. Installs all platform components
4. Configures development settings

**Environment:** Local development with Kind

---

### `workflows:setup-production`
Complete production environment setup.

**Usage:**
```bash
task workflows:setup-production CLUSTER_NAME=<name> PROVIDER=<provider>
```

**Required Parameters:**
- `CLUSTER_NAME`: Production cluster name
- `PROVIDER`: Cloud provider

**Optional Parameters:**
- `ENVIRONMENT`: Environment type (default: `production`)
- `REGION`: Cloud region

**Actions:**
1. Validates cloud provider setup
2. Creates production cluster
3. Installs platform components
4. Configures production settings
5. Sets up monitoring and logging

**Example:**
```bash
task workflows:setup-production \
  CLUSTER_NAME=idp-prod-us \
  PROVIDER=aws \
  REGION=us-east-1 \
  ENVIRONMENT=production
```

---

### `workflows:health-check`
Comprehensive platform health check.

**Usage:**
```bash
task workflows:health-check
```

**Optional Parameters:**
- `CLUSTER_NAME`: Specific cluster to check

**Checks:**
- Cluster connectivity
- Core component health
- Platform service status
- Integration connectivity

---

### `workflows:validate-complete-setup`
Validates complete platform setup.

**Usage:**
```bash
task workflows:validate-complete-setup CLUSTER_NAME=<name>
```

**Required Parameters:**
- `CLUSTER_NAME`: Cluster to validate

**Validation Areas:**
- Infrastructure components
- Platform services
- Network connectivity
- Security configurations
- Integration health

---

### `workflows:disaster-recovery`
Performs disaster recovery procedures.

**Usage:**
```bash
task workflows:disaster-recovery CLUSTER_NAME=<name> BACKUP_DATE=<date>
```

**Required Parameters:**
- `CLUSTER_NAME`: Target cluster
- `BACKUP_DATE`: Backup date (YYYY-MM-DD format)

**Optional Parameters:**
- `RESTORE_TYPE`: Recovery type (`full`, `partial`, `config-only`)

**Example:**
```bash
task workflows:disaster-recovery \
  CLUSTER_NAME=production \
  BACKUP_DATE=2024-01-15 \
  RESTORE_TYPE=full
```

---

### `workflows:upgrade-platform`
Upgrades platform components to newer versions.

**Usage:**
```bash
task workflows:upgrade-platform
```

**Optional Parameters:**
- `COMPONENT`: Specific component to upgrade (`argocd`, `crossplane`, `backstage`)
- `VERSION`: Target version (default: latest stable)
- `DRY_RUN`: Preview changes without applying (default: false)

**Example:**
```bash
task workflows:upgrade-platform COMPONENT=argocd VERSION=v2.8.0 DRY_RUN=true
```

---

### `workflows:backup-platform`
Creates comprehensive platform backup.

**Usage:**
```bash
task workflows:backup-platform CLUSTER_NAME=<name>
```

**Required Parameters:**
- `CLUSTER_NAME`: Cluster to backup

**Optional Parameters:**
- `BACKUP_TYPE`: Backup scope (`full`, `config`, `data`)
- `STORAGE_LOCATION`: Backup storage location

**Backup Components:**
- Kubernetes resources
- Persistent volume data
- Configuration files
- Secrets (encrypted)

## Configuration Reference

### Environment Variables

#### Global Configuration
```bash
# Core platform settings
CLUSTER_NAME="my-idp"                    # Cluster identifier
PROVIDER="aws"                           # Cloud provider
ENVIRONMENT="production"                 # Environment type
REGION="us-east-1"                      # Cloud region

# Networking
DOMAIN="platform.company.com"           # Platform domain
ENABLE_TLS="true"                       # Enable TLS/SSL
CERT_MANAGER_EMAIL="admin@company.com"   # Let's Encrypt email

# Integration
GITHUB_USER="platform-admin"            # GitHub username
GITHUB_TOKEN="ghp_xxxx"                 # GitHub PAT
REGISTRY_URL="ghcr.io"                  # Container registry

# Monitoring
ENABLE_MONITORING="true"                # Enable monitoring stack
RETENTION_DAYS="30"                     # Metrics retention

# Debug
DEBUG="false"                           # Enable debug mode
VERBOSE="false"                         # Verbose logging
```

#### Provider-Specific Variables

**AWS**
```bash
AWS_REGION="us-east-1"
AWS_ACCESS_KEY_ID="AKIAXXXXX"
AWS_SECRET_ACCESS_KEY="xxxxx"
AWS_ACCOUNT_ID="123456789012"
```

**Google Cloud**
```bash
PROJECT_ID="my-gcp-project"
GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
COMPUTE_ZONE="us-central1-a"
```

**Azure**
```bash
AZURE_SUBSCRIPTION_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
AZURE_CLIENT_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
AZURE_CLIENT_SECRET="xxxxx"
AZURE_TENANT_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

### Task File Structure

```yaml
# Standard task structure
version: '3'

vars:
  CLUSTER_NAME: '{{.CLUSTER_NAME | default "idp-cluster"}}'
  PROVIDER: '{{.PROVIDER | default "kind"}}'

tasks:
  task-name:
    desc: Task description
    vars:
      LOCAL_VAR: value
    deps:
      - dependency-task
    preconditions:
      - sh: command-check
        msg: Error message
    cmds:
      - echo "Executing task"
    silent: false
    ignore_error: false
```

## Error Codes

### Exit Codes
- `0`: Success
- `1`: General error
- `2`: Prerequisites not met
- `3`: Authentication failure
- `4`: Network connectivity issue
- `5`: Resource not found
- `6`: Configuration error
- `7`: Provider-specific error

### Common Error Messages

#### Prerequisites Errors
- `PREREQ_001`: Docker not installed or not running
- `PREREQ_002`: kubectl not found in PATH
- `PREREQ_003`: Invalid tool version
- `PREREQ_004`: Insufficient system resources

#### Authentication Errors
- `AUTH_001`: Cloud provider credentials not configured
- `AUTH_002`: Invalid or expired credentials
- `AUTH_003`: Insufficient permissions
- `AUTH_004`: Multi-factor authentication required

#### Cluster Errors
- `CLUSTER_001`: Cluster creation failed
- `CLUSTER_002`: Cluster not found
- `CLUSTER_003`: Cluster unreachable
- `CLUSTER_004`: Insufficient cluster resources

#### Component Errors
- `COMP_001`: Component installation failed
- `COMP_002`: Component health check failed
- `COMP_003`: Component configuration error
- `COMP_004`: Component version incompatibility

## API Examples

### Basic Platform Setup
```bash
#!/bin/bash
set -e

# Prerequisites
task prerequisites:check-all

# Environment
task environment:set-variable KEY=CLUSTER_NAME VALUE=demo-idp
task environment:set-variable KEY=PROVIDER VALUE=kind

# Cluster setup
task cluster:create-cluster CLUSTER_NAME=demo-idp PROVIDER=kind

# Platform components
task gitops:install-argocd
task crossplane:install-crossplane
task backstage:install-backstage

# Validation
task workflows:health-check CLUSTER_NAME=demo-idp
```

### Production Deployment
```bash
#!/bin/bash
set -e

# Production setup
export CLUSTER_NAME=idp-production
export PROVIDER=aws
export REGION=us-east-1
export ENVIRONMENT=production

# Complete production setup
task workflows:setup-production \
  CLUSTER_NAME=$CLUSTER_NAME \
  PROVIDER=$PROVIDER \
  REGION=$REGION \
  ENVIRONMENT=$ENVIRONMENT

# Configure domain and TLS
task networking:configure-dns DOMAIN=idp.company.com

# Validate deployment
task workflows:validate-complete-setup CLUSTER_NAME=$CLUSTER_NAME
```

### Platform Monitoring
```bash
#!/bin/bash

# Health check script
while true; do
  echo "$(date): Running platform health check..."
  
  if task workflows:health-check --silent; then
    echo "✅ Platform healthy"
  else
    echo "❌ Platform issues detected"
    # Send alert notification
    task workflows:send-alert TYPE=health-check-failed
  fi
  
  sleep 300  # Check every 5 minutes
done
```

This API reference provides comprehensive documentation for all IDP Builder functionality. For additional examples and use cases, see the [Examples](../examples/README.md) directory.
