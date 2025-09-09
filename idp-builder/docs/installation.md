# IDP Builder Installation Guide

This guide provides detailed instructions for installing and configuring IDP Builder across different environments and cloud providers.

## Prerequisites

### System Requirements
- **Operating System**: Linux, macOS, or Windows with WSL2
- **Memory**: Minimum 8GB RAM (16GB recommended)
- **Storage**: At least 50GB free disk space
- **Network**: Internet connectivity for downloading dependencies

### Required Tools

#### Essential Tools (Required)
```bash
# Check if tools are installed
task prerequisites:check-minimal

# Install missing tools (basic)
task prerequisites:install-prerequisites
```

| Tool | Version | Purpose |
|------|---------|---------|
| **Docker** | 24.0+ | Container runtime and image building |
| **kubectl** | 1.28+ | Kubernetes cluster interaction |
| **kind** | 0.20+ | Local Kubernetes clusters |
| **helm** | 3.12+ | Kubernetes package management |
| **task** | 3.28+ | Task runner and automation |

#### Cloud Provider Tools (Choose based on your provider)

**AWS**
```bash
# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip && sudo ./aws/install

# Install eksctl
curl --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

# Verify installation
aws --version && eksctl version
```

**Google Cloud**
```bash
# Install gcloud CLI
curl https://sdk.cloud.google.com | bash
exec -l $SHELL

# Install gke-gcloud-auth-plugin
gcloud components install gke-gcloud-auth-plugin

# Verify installation
gcloud --version
```

**Azure**
```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Verify installation
az --version
```

### Cloud Provider Authentication

#### AWS Configuration
```bash
# Configure AWS credentials
aws configure
# Enter: Access Key ID, Secret Access Key, Region, Output format

# Or use environment variables
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="us-east-1"

# Verify authentication
task prerequisites:check-authentication PROVIDER=aws
```

#### Google Cloud Configuration
```bash
# Authenticate with Google Cloud
gcloud auth login
gcloud auth application-default login

# Set project
gcloud config set project YOUR_PROJECT_ID
export PROJECT_ID="your-project-id"

# Verify authentication
task prerequisites:check-authentication PROVIDER=gcp
```

#### Azure Configuration
```bash
# Authenticate with Azure
az login

# Set subscription
az account set --subscription "your-subscription-id"

# Verify authentication
task prerequisites:check-authentication PROVIDER=azure
```

## Installation Methods

### Method 1: Quick Setup (Recommended)

#### Local Development Setup
```bash
cd idp-builder

# Check prerequisites
task prerequisites:check-all

# Complete local setup with Kind
task workflows:setup-development
```

This creates:
- Local Kubernetes cluster (Kind)
- ArgoCD GitOps platform
- Crossplane infrastructure management
- Backstage developer portal
- Ingress and networking
- Basic monitoring

#### Cloud Provider Setup
```bash
# AWS Production Setup
task workflows:setup-production \
  CLUSTER_NAME=my-idp-prod \
  PROVIDER=aws \
  REGION=us-east-1 \
  ENVIRONMENT=production

# Google Cloud Setup  
task workflows:setup-production \
  CLUSTER_NAME=my-idp-prod \
  PROVIDER=gcp \
  REGION=us-central1 \
  ENVIRONMENT=production

# Azure Setup
task workflows:setup-production \
  CLUSTER_NAME=my-idp-prod \
  PROVIDER=azure \
  REGION=eastus \
  ENVIRONMENT=production
```

### Method 2: Step-by-Step Setup

#### Step 1: Prerequisites and Environment
```bash
# Check system prerequisites
task prerequisites:check-all

# Configure environment variables
task environment:set-variable KEY=CLUSTER_NAME VALUE=my-idp
task environment:set-variable KEY=PROVIDER VALUE=aws
task environment:set-variable KEY=REGION VALUE=us-east-1

# Validate environment
task environment:validate
```

#### Step 2: Cloud Provider Configuration
```bash
# Configure your chosen provider
task providers:configure-aws      # For AWS
task providers:configure-gcp      # For Google Cloud
task providers:configure-azure    # For Azure
task providers:configure-kind     # For local development

# Validate provider setup
task providers:validate-provider PROVIDER=aws
```

#### Step 3: Infrastructure Setup
```bash
# Create Kubernetes cluster
task cluster:create-cluster \
  CLUSTER_NAME=my-idp \
  PROVIDER=aws \
  REGION=us-east-1

# Setup networking
task networking:setup-ingress PROVIDER=aws

# Setup storage
task storage:setup-storage PROVIDER=aws
```

#### Step 4: Platform Components
```bash
# Install GitOps platform
task gitops:install-argocd CLUSTER_NAME=my-idp

# Install Infrastructure as Code
task crossplane:install-crossplane CLUSTER_NAME=my-idp

# Install Developer Portal
task backstage:install-backstage CLUSTER_NAME=my-idp
```

#### Step 5: Validation
```bash
# Validate complete setup
task workflows:validate-complete-setup CLUSTER_NAME=my-idp

# Check platform health
task workflows:health-check CLUSTER_NAME=my-idp
```

## Configuration Options

### Environment Variables

Create a `.env` file in the `idp-builder` directory:

```bash
# Required Configuration
CLUSTER_NAME=my-idp-cluster
PROVIDER=aws
ENVIRONMENT=production
REGION=us-east-1

# GitHub Integration (Optional)
GITHUB_USER=your-github-username
GITHUB_TOKEN=ghp_your_personal_access_token

# Container Registry (Optional)
REGISTRY=ghcr.io
REGISTRY_USERNAME=your-registry-username

# Monitoring (Optional)
ENABLE_MONITORING=true
MONITORING_NAMESPACE=monitoring

# Debug (Optional)
DEBUG=false
```

### Provider-Specific Configuration

#### AWS Configuration
```yaml
# Save as configs/aws-config.yaml
provider: aws
cluster:
  nodeGroups:
    - name: workers
      instanceType: t3.medium
      desiredCapacity: 3
      minSize: 1
      maxSize: 5
  version: "1.28"
networking:
  vpcCIDR: "10.0.0.0/16"
  enableDNS: true
storage:
  storageClass: gp3
  encrypted: true
```

#### GCP Configuration  
```yaml
# Save as configs/gcp-config.yaml
provider: gcp
cluster:
  machineType: e2-standard-4
  numNodes: 3
  version: "1.28"
networking:
  subnetwork: default
  enableIPAlias: true
storage:
  storageClass: ssd-retain
  zones: ["us-central1-a", "us-central1-b"]
```

#### Azure Configuration
```yaml
# Save as configs/azure-config.yaml
provider: azure
cluster:
  vmSize: Standard_D2s_v3
  nodeCount: 3
  version: "1.28"
networking:
  networkPlugin: azure
  serviceCidr: "10.0.0.0/16"
storage:
  storageClass: managed-premium
  replication: LRS
```

## Verification and Testing

### Platform Health Check
```bash
# Comprehensive health check
task workflows:health-check CLUSTER_NAME=my-idp

# Individual component checks
task gitops:health-check
task crossplane:health-check  
task backstage:health-check
```

### Access Platform Components

#### ArgoCD UI
```bash
# Get ArgoCD admin password
task gitops:get-admin-password

# Access UI (port-forward)
task gitops:access-ui
# Open: http://localhost:8080
```

#### Backstage Developer Portal
```bash
# Access Backstage UI
task backstage:access-ui  
# Open: http://localhost:3000
```

#### Kubernetes Dashboard (if installed)
```bash
kubectl proxy
# Open: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Troubleshooting

### Common Issues

#### Prerequisites Missing
```bash
# Install missing tools
task prerequisites:install-prerequisites

# Check again
task prerequisites:check-all
```

#### Authentication Failures
```bash
# Validate cloud provider authentication
task prerequisites:check-authentication PROVIDER=aws

# Re-configure if needed
task providers:configure-aws
```

#### Cluster Creation Issues
```bash
# Check provider configuration
task providers:show-provider-status

# Try creating cluster with debug
DEBUG=true task cluster:create-cluster CLUSTER_NAME=debug-cluster
```

#### Platform Component Failures
```bash
# Check cluster status
task cluster:cluster-status

# Check individual components
task gitops:status
task crossplane:status
task backstage:status

# View logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-server
```

### Getting Help

1. **Check the troubleshooting guide**: [troubleshooting.md](troubleshooting.md)
2. **Review logs**: `kubectl logs -n <namespace> <pod-name>`
3. **Validate configuration**: `task environment:show-config`
4. **Open an issue**: [GitHub Issues](https://github.com/vek0ng/idp-stack/issues)

## Next Steps

After successful installation:

1. **Configure your applications**: Move to `idp-usage` for application development
2. **Setup monitoring**: Configure observability and alerting
3. **Create backups**: Setup automated backup procedures
4. **Team onboarding**: Onboard development teams to the platform

See the [Architecture Guide](architecture.md) for understanding the platform design and [API Reference](api-reference.md) for detailed task documentation.
