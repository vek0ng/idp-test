# IDP Builder - Internal Developer Platform Setup and Management

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go)](https://golang.org/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.28+-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)

## Overview

**IDP Builder** is a comprehensive Internal Developer Platform (IDP) setup and management tool that automates the deployment and maintenance of enterprise-grade development infrastructure. It provides a complete solution for setting up GitOps, Infrastructure as Code, and Developer Portal components across multiple cloud providers.

## 🚀 Quick Start

### Prerequisites
- Docker and Kubernetes CLI tools
- Cloud provider CLI (AWS, GCP, or Azure)
- Go 1.21+ (for local development)

### Basic Installation
```bash
# Clone the repository
git clone https://github.com/vek0ng/idp-stack.git
cd idp-stack/idp-builder

# Check prerequisites
task prerequisites:check-all

# Quick setup for local development
task workflows:setup-development

# Or setup for specific cloud provider
task workflows:setup-production CLUSTER_NAME=my-idp PROVIDER=aws REGION=us-east-1
```

## 🏗️ Architecture Overview

IDP Builder manages three core platform layers:

### 1. Infrastructure Layer
- **Cluster Management**: Multi-provider Kubernetes cluster provisioning
- **Networking**: Ingress controllers, load balancers, DNS management
- **Storage**: Persistent volumes, storage classes, backup solutions

### 2. Platform Layer  
- **GitOps (ArgoCD)**: Continuous deployment and application lifecycle
- **Infrastructure as Code (Crossplane)**: Cloud resource provisioning
- **Developer Portal (Backstage)**: Service catalog and developer experience

### 3. Workflow Layer
- **Setup Workflows**: Complete platform initialization
- **Maintenance Workflows**: Ongoing operations and monitoring
- **Disaster Recovery**: Backup, restore, and business continuity

## 📋 Core Components

| Component | Purpose | Key Features |
|-----------|---------|--------------|
| **ArgoCD** | GitOps Platform | Continuous deployment, sync policies, multi-cluster |
| **Crossplane** | Infrastructure as Code | Cloud resources, compositions, providers |
| **Backstage** | Developer Portal | Service catalog, documentation, templates |

## 🛠️ Task Categories

### Core Infrastructure
```bash
task prerequisites:check-all              # Validate environment
task providers:configure-aws              # Setup cloud provider
task cluster:create-cluster               # Create Kubernetes cluster
task networking:setup-ingress             # Configure ingress
task storage:setup-storage                # Setup persistent storage
```

### Platform Management
```bash
task gitops:install-argocd               # Install GitOps platform
task crossplane:install-crossplane       # Install IaC platform  
task backstage:install-backstage         # Install developer portal
```

### Workflow Orchestration
```bash
task workflows:complete-setup            # End-to-end platform setup
task workflows:health-check              # Platform health monitoring
task workflows:backup-all                # Comprehensive backup
task workflows:disaster-recovery         # Business continuity
```

## 🌟 Key Features

### Multi-Cloud Support
- **AWS**: EKS clusters with VPC, IAM, and service integrations
- **Google Cloud**: GKE clusters with networking and identity management
- **Azure**: AKS clusters with virtual networks and resource groups
- **Local Development**: Kind clusters for testing and development

### Production-Ready Security
- **RBAC Integration**: Role-based access control with cloud identity
- **Network Policies**: Micro-segmentation and traffic control
- **Secret Management**: Encrypted storage and rotation
- **Security Scanning**: Container and infrastructure vulnerability scanning

### Enterprise Operations
- **High Availability**: Multi-zone deployments with failover
- **Monitoring**: Comprehensive metrics, logging, and alerting
- **Backup & Recovery**: Automated backup with point-in-time recovery
- **Updates**: Zero-downtime platform updates and maintenance

## 🎯 Use Cases

### Platform Engineering Teams
- **Standardized Infrastructure**: Consistent environments across dev/staging/production
- **GitOps Workflows**: Automated deployment pipelines with approval processes
- **Self-Service Resources**: Developer-accessible infrastructure provisioning

### DevOps Teams
- **Automated Operations**: Reduced manual overhead through workflow automation
- **Compliance**: Built-in security and compliance policies
- **Disaster Recovery**: Automated backup and recovery procedures

### Development Teams
- **Developer Experience**: Integrated tooling and service discovery
- **Service Templates**: Standardized application scaffolding
- **Environment Management**: Easy access to development and testing environments

## 📚 Documentation

- **[Installation Guide](installation.md)** - Detailed setup instructions
- **[Architecture Guide](architecture.md)** - Platform architecture and design
- **[API Reference](api-reference.md)** - Complete task reference
- **[Troubleshooting](troubleshooting.md)** - Common issues and solutions

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](../LICENSE) file for details.

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/vek0ng/idp-stack/issues)
- **Discussions**: [GitHub Discussions](https://github.com/vek0ng/idp-stack/discussions)
- **Documentation**: [Full Documentation](docs/)

---

**IDP Builder** - Simplifying Internal Developer Platform setup and management ⚡
