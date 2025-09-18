# IDP Stack Examples

This directory contains real-world examples showcasing the advanced capabilities of the IDP Stack. Each example includes working code, configurations, and step-by-step instructions.

## Example Categories

### Applications
Ready-to-deploy application examples with complete platform integration:

- **[Microservices](applications/microservices/)** - Go, Node.js, Python service examples with observability
- **[Frontend Apps](applications/frontend/)** - React, Vue, Angular applications with CI/CD  
- **[Data Pipelines](applications/data-pipelines/)** - Analytics and ML workloads on the platform
- **[Legacy Modernization](applications/legacy/)** - Migration patterns and modernization strategies

### Infrastructure  
Infrastructure-as-Code examples for different resource types:

- **[Databases](infrastructure/databases/)** - PostgreSQL, MySQL, Redis with backup strategies
- **[Messaging](infrastructure/messaging/)** - Kafka, RabbitMQ, NATS configurations  
- **[Storage](infrastructure/storage/)** - Object storage, persistent volumes, CDN integration
- **[Networking](infrastructure/networking/)** - Load balancers, ingress controllers, service mesh

### 🔒 Security
Security and compliance examples:

- **[Authentication](security/authentication/)** - OAuth, OIDC, RBAC implementation patterns
- **[Secrets Management](security/secrets/)** - Vault, sealed-secrets, external-secrets integration
- **[Network Policies](security/network-policies/)** - Zero-trust networking configurations
- **[Compliance](security/compliance/)** - SOC2, GDPR, HIPAA compliance automation

### Operations
Operational excellence examples:

- **[Monitoring](operations/monitoring/)** - Prometheus, Grafana, alerting configurations
- **[Logging](operations/logging/)** - ELK, Loki, structured logging patterns
- **[Backup & Restore](operations/backup-restore/)** - Disaster recovery procedures
- **[Performance](operations/performance/)** - Resource optimization and scaling patterns

## Advanced Deployment Patterns

### Production-Ready Backstage Examples
- **[Custom Plugin Development](advanced/backstage-plugins/)** - TeraSky plugin ecosystem
- **[Software Templates](advanced/software-templates/)** - Custom application scaffolding
- **[Service Catalog](advanced/service-catalog/)** - Advanced catalog management

### Multi-Cloud Infrastructure Examples  
- **[Hybrid Cloud Setup](advanced/hybrid-cloud/)** - Cross-provider infrastructure
- **[Multi-Region Deployment](advanced/multi-region/)** - Global application distribution
- **[Provider Parity](advanced/provider-parity/)** - Consistent APIs across clouds

### Advanced Application Lifecycles
- **[Canary Deployments](advanced/canary-deployments/)** - Progressive rollout strategies
- **[Blue-Green Deployments](advanced/blue-green/)** - Zero-downtime deployment patterns
- **[Multi-Environment Promotion](advanced/promotion-workflows/)** - Dev→Stage→Prod automation

## 📚 Usage Patterns

Each example includes:

- **`README.md`** - Overview, prerequisites, and instructions  
- **`manifests/`** - Kubernetes YAML configurations
- **`scripts/`** - Automation and helper scripts
- **`docs/`** - Detailed documentation and troubleshooting
- **`tests/`** - Validation and smoke tests

## Quick Start

1. **Browse Examples**: Find examples relevant to your use case
2. **Check Prerequisites**: Ensure your platform meets requirements
3. **Follow Instructions**: Each example has step-by-step guides  
4. **Customize**: Adapt examples to your specific needs
5. **Contribute**: Share improvements and new examples

## Learning Paths

### 🆕 New to Platform
Start with these foundational examples:
1. [Simple Microservice](applications/microservices/simple-go-service/)
2. [Basic Infrastructure](infrastructure/databases/postgresql-basic/)
3. [GitOps Workflow](operations/gitops/basic-workflow/)

### 👨‍💻 Experienced Developer
Focus on workflow and integration:
1. [Custom Backstage Templates](advanced/software-templates/microservice-template/)
2. [Multi-Environment Pipeline](advanced/promotion-workflows/dev-stage-prod/)
3. [Observability Integration](operations/monitoring/application-metrics/)

### DevOps Engineer  
Explore advanced deployment patterns:
1. [Canary Deployment Strategy](advanced/canary-deployments/traffic-splitting/)
2. [Multi-Cloud Infrastructure](advanced/hybrid-cloud/aws-gcp-setup/)
3. [Disaster Recovery](operations/backup-restore/cross-region-backup/)

### Platform Engineer
Deep dive into platform capabilities:
1. [Custom Crossplane Compositions](advanced/crossplane-compositions/enterprise-patterns/)
2. [Policy Framework](security/compliance/policy-as-code/)
3. [Tenant Management](advanced/multi-tenancy/enterprise-isolation/)

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](../CONTRIBUTING.md) for:

- Example submission process
- Quality standards and testing requirements  
- Documentation templates
- Review and approval workflow

## 📞 Support

- **Documentation**: [Platform Docs](../docs/)
- **Issues**: [GitHub Issues](https://github.com/your-org/idp-stack/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/idp-stack/discussions)
- **Slack**: [#idp-stack-support](https://your-org.slack.com/channels/idp-stack-support)
