# Production-Ready Backstage Custom App Creation

This example demonstrates the complete Production-Ready Backstage workflow with TeraSky plugin ecosystem, showcasing custom app creation, Docker image building, and Helm chart packaging.

## What This Example Shows

- Custom Backstage application creation using TeraSky plugins
- Production-ready build pipeline with Docker and Helm
- GitHub Container Registry integration
- Multi-environment deployment automation
- Service catalog integration with advanced metadata

## Prerequisites

- IDP Stack with Backstage installed
- Docker runtime with registry access
- GitHub Container Registry credentials
- Kind or cloud Kubernetes cluster

## Quick Start

```bash
# 1. Navigate to this example
cd examples/advanced/production-backstage

# 2. Set up environment variables
cp .env.example .env
# Edit .env with your configuration

# 3. Run the complete example
./run-example.sh

# 4. Access results
open http://localhost:3000  # Backstage UI
```

## Example Structure

```
production-backstage/
├── README.md                    # This file
├── .env.example                 # Environment template
├── run-example.sh              # Automated example runner
├── templates/                   # Backstage software templates
│   ├── microservice-template/
│   ├── frontend-template/
│   └── data-pipeline-template/
├── plugins/                     # Custom plugin configurations
│   ├── crossplane-config.yaml
│   ├── kubernetes-ingestor.yaml
│   └── scaffolder-actions.yaml
├── manifests/                   # Kubernetes configurations
│   ├── backstage-production.yaml
│   ├── plugin-configs.yaml
│   └── rbac-policies.yaml
├── scripts/                     # Helper scripts
│   ├── build-custom-image.sh
│   ├── package-helm-chart.sh
│   └── deploy-production.sh
└── docs/                       # Detailed documentation
    ├── plugin-development.md
    ├── template-customization.md
    └── troubleshooting.md
```

## Step-by-Step Walkthrough

### Step 1: Configure Production-Ready Backstage

```bash
# Configure Backstage with TeraSky plugins
task backstage:configure \
  ENABLE_CROSSPLANE=true \
  ENABLE_KUBERNETES_INGESTOR=true

# Install production plugins
task backstage:install-backend-packages
task backstage:install-frontend-packages
```

### Step 2: Create Custom Application Template

The example includes a sophisticated microservice template:

```yaml
# templates/microservice-template/template.yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: production-microservice
  title: Production-Ready Microservice
  description: Create a production-ready microservice with observability, security, and deployment automation
spec:
  owner: platform-team
  type: service
  parameters:
    - title: Service Information
      required: [name, description, owner]
      properties:
        name:
          title: Service Name
          type: string
          pattern: '^[a-z][a-z0-9-]*[a-z0-9]$'
        description:
          title: Description
          type: string
        owner:
          title: Owner
          type: string
          ui:field: OwnerPicker
        system:
          title: System
          type: string
          ui:field: EntityPicker
          ui:options:
            catalogFilter:
              kind: [System]
    
    - title: Technology Stack
      properties:
        language:
          title: Programming Language
          type: string
          enum: [go, nodejs, python, java]
        database:
          title: Database Type
          type: string
          enum: [postgresql, mysql, mongodb, none]
        messaging:
          title: Message Queue
          type: string  
          enum: [kafka, rabbitmq, nats, none]
    
    - title: Infrastructure Requirements
      properties:
        hyperscaler:
          title: Target Cloud Provider
          type: string
          enum: [kind, aws, gcp, azure]
        environment:
          title: Initial Environment
          type: string
          enum: [dev, staging, prod]
        resources:
          title: Resource Requirements
          type: object
          properties:
            cpu:
              title: CPU Request
              type: string
              default: "100m"
            memory:
              title: Memory Request  
              type: string
              default: "128Mi"
            replicas:
              title: Initial Replicas
              type: integer
              default: 2
```

### Step 3: Build Production-Ready Application

The template generates a complete application with:

```bash
# Generated application structure
${{values.name}}/
├── src/                        # Application source code
├── Dockerfile                  # Multi-stage production build
├── helm/                       # Helm chart for deployment
├── .github/workflows/          # CI/CD pipelines
├── k8s/                        # Kubernetes manifests
├── docs/                       # API documentation
├── tests/                      # Unit and integration tests
└── catalog-info.yaml          # Backstage service metadata
```

### Step 4: Execute Production Build Pipeline

```bash
# Build custom Docker image
task backstage:build-backstage-custom \
  REGISTRY_URL=ghcr.io \
  REGISTRY_NAMESPACE=your-org \
  IMAGE_NAME=microservice-app \
  IMAGE_TAG=v1.0.0

# Package Helm chart  
task backstage:package-helm-chart-custom \
  CHART_NAME=production-microservice \
  CHART_VERSION=1.0.0

# Deploy to production
task backstage:deploy-custom-production \
  NAMESPACE=production \
  ENABLE_CROSSPLANE=true \
  ENABLE_KUBERNETES_INGESTOR=true
```

### Step 5: Verify Service Catalog Integration

The application automatically registers in Backstage with rich metadata:

```yaml
# Generated catalog-info.yaml
apiVersion: backstage.io/v1alpha1  
kind: Component
metadata:
  name: ${{values.name}}
  description: ${{values.description}}
  annotations:
    backstage.io/kubernetes-id: ${{values.name}}
    backstage.io/kubernetes-namespace: ${{values.environment}}
    argocd/app-name: ${{values.name}}
    crossplane.io/claim-name: ${{values.name}}-infrastructure
    prometheus.io/rule: ${{values.name}}-alerts
    grafana/dashboard-selector: service=${{values.name}}
spec:
  type: service
  lifecycle: production
  owner: ${{values.owner}}
  system: ${{values.system}}
  dependsOn:
    - resource:default/${{values.hyperscaler}}-cluster
    - resource:default/argocd-server
    - resource:default/crossplane
  providesApis:
    - ${{values.name}}-api
```

## 🎨 Advanced Features Demonstrated

### TeraSky Plugin Integration

```yaml
# Enhanced app-config.yaml showing plugin configuration
plugins:
  crossplane:
    enabled: true
    permissions: true
    resourceTypes: [databases, queues, storage]
  
  kubernetesIngestor:
    enabled: true
    components:
      excludedNamespaces: [kube-system, kube-public]
      customWorkloadTypes:
        - group: core.oam.dev
          apiVersion: v1beta1  
          plural: applications
    
  scaffolder:
    actions:
      - terasky:create-infrastructure
      - terasky:setup-monitoring
      - terasky:configure-rbac
```

### Custom Plugin Actions

The example includes custom scaffolder actions:

```typescript
// Custom action: terasky:create-infrastructure
export const createInfrastructureAction = () => {
  return createTemplateAction<{
    resourceType: string;
    hyperscaler: string;
    environment: string;
  }>({
    id: 'terasky:create-infrastructure',
    description: 'Creates infrastructure resources via Crossplane',
    schema: {
      input: {
        type: 'object',
        required: ['resourceType', 'hyperscaler'],
        properties: {
          resourceType: {
            title: 'Resource Type',
            type: 'string',
            enum: ['database', 'queue', 'storage'],
          },
          hyperscaler: {
            title: 'Hyperscaler', 
            type: 'string',
            enum: ['kind', 'aws', 'gcp', 'azure'],
          },
        },
      },
    },
    async handler(ctx) {
      // Create Crossplane claim
      const claim = generateCrossplaneClaim(ctx.input);
      await applyKubernetesResource(claim);
      
      ctx.logger.info(`Infrastructure created: ${ctx.input.resourceType}`);
    },
  });
};
```

## Success Metrics

After running this example, you should see:

- Custom Backstage app with TeraSky plugins installed
- Production-ready microservice template available
- Docker image built and pushed to registry
- Helm chart packaged and deployable
- Service registered in Backstage catalog
- Infrastructure automatically provisioned
- CI/CD pipeline configured and functional

## Real-World Usage

This example demonstrates patterns used by:

- **Enterprise Platform Teams** - Custom developer experiences
- **DevOps Engineers** - Production deployment automation  
- **Developers** - Self-service application creation
- **SRE Teams** - Standardized observability and operations

## Troubleshooting

### Common Issues

**Plugin Installation Fails**
```bash
# Check plugin compatibility
task backstage:list-plugins

# Reinstall with specific versions
task backstage:install-backend-packages ENABLE_CROSSPLANE=false
```

**Docker Build Errors**
```bash  
# Check registry credentials
docker login ghcr.io

# Use local registry for testing
task backstage:build-backstage-custom REGISTRY_URL=localhost:5000
```

**Template Generation Fails**
```bash
# Validate template syntax
task backstage:validate-template TEMPLATE_PATH=templates/microservice-template

# Debug scaffolder actions
kubectl logs -n backstage -l app=backstage-backend
```

## Next Steps

1. **Customize Templates** - Modify templates for your organization's standards
2. **Add Custom Plugins** - Develop organization-specific plugins  
3. **Integrate CI/CD** - Connect with your existing pipeline tools
4. **Scale Deployment** - Deploy across multiple clusters and environments
5. **Monitor Usage** - Set up analytics and user feedback collection

## 📚 Related Examples

- [Multi-Environment Promotion](../promotion-workflows/) - Complete deployment pipeline
- [Custom Crossplane Compositions](../crossplane-compositions/) - Infrastructure patterns
- [Advanced Monitoring](../../operations/monitoring/) - Observability integration

## 🤝 Contributing

Found improvements or issues? Please:

1. Test your changes thoroughly
2. Update documentation
3. Submit a pull request with clear description
4. Include screenshots or demo recordings
