# Backstage Production Upgrade Guide

## Overview

The `task install-backstage` command installs a **placeholder nginx service** with a branded landing page for initial setup and validation. This guide explains how to upgrade to a production-ready Backstage Docker image.

## ⚠️ Important Notice

**The initial Backstage installation is NOT production-ready!** It uses:
- `nginx:alpine` image with a static HTML placeholder
- Port 80 instead of Backstage's default port 7007
- No Backstage application functionality

## Quick Upgrade Methods

### Method 1: Automated Upgrade Task (Recommended)

Use the built-in upgrade task to transition from placeholder to production:

```bash
# 1. Build or obtain your Backstage image
docker build -t my-company/backstage:v1.0.0 ./my-backstage-app

# 2. Upgrade the deployment
task upgrade-backstage-production BACKSTAGE_IMAGE=my-company/backstage:v1.0.0

# 3. Verify the upgrade
kubectl get pods -n backstage
kubectl port-forward svc/backstage 3000:80 -n backstage
```

### Method 2: Manual kubectl Commands

```bash
# Update the image
kubectl set image deployment/backstage backstage=my-backstage:v1.0.0 -n backstage

# Update container port (Backstage uses 7007)
kubectl patch deployment backstage -n backstage --type='merge' \
  -p='{"spec":{"template":{"spec":{"containers":[{"name":"backstage","ports":[{"containerPort":7007}]}]}}}}'

# Update service to route to Backstage port
kubectl patch service backstage -n backstage --type='merge' \
  -p='{"spec":{"ports":[{"port":80,"targetPort":7007}]}}'

# Wait for rollout
kubectl rollout status deployment/backstage -n backstage
```

### Method 3: Update Taskfile Permanently

Edit `taskfiles/platform/backstage.yaml` in the `_install-backstage-core` task:

```yaml
# Find this section and update:
containers:
- name: backstage
  image: my-company/backstage:v1.0.0  # <-- Replace nginx:alpine
  ports:
  - containerPort: 7007               # <-- Change from 80 to 7007
```

## Creating a Custom Backstage Image

### Quick Start with Official Template

```bash
# 1. Create new Backstage app
npx @backstage/create-app@latest my-backstage-app

# 2. Navigate to app directory
cd my-backstage-app

# 3. Configure app-config.yaml for your environment
vim app-config.yaml

# 4. Build Docker image
docker build . -t my-backstage:latest

# 5. Push to registry (if using remote cluster)
docker tag my-backstage:latest my-registry/backstage:v1.0.0
docker push my-registry/backstage:v1.0.0
```

### Production Configuration

Ensure your `app-config.yaml` includes:

```yaml
app:
  title: My Company Portal
  baseUrl: http://backstage.local  # Match your ingress

backend:
  baseUrl: http://backstage.local
  listen:
    port: 7007
    host: 0.0.0.0
  database:
    client: pg
    connection:
      host: postgres
      port: 5432
      user: postgres
      password: backstage123
      database: backstage

integrations:
  github:
    - host: github.com
      token: ${GITHUB_TOKEN}
```

## Verification Steps

After upgrading, verify your Backstage deployment:

```bash
# 1. Check pods are running
kubectl get pods -n backstage

# 2. Check logs for any errors
kubectl logs -f deployment/backstage -n backstage

# 3. Test health endpoint
kubectl port-forward svc/backstage 3000:80 -n backstage &
curl http://localhost:3000/api/app/health

# 4. Access UI
open http://localhost:3000
```

## Rollback if Needed

If the upgrade fails, rollback to the placeholder:

```bash
# Rollback to nginx placeholder
kubectl rollout undo deployment/backstage -n backstage

# Or reinstall completely
task backstage:uninstall-backstage
task install-backstage
```

## Production Checklist

Before production deployment, ensure:

- [ ] Custom Backstage image with required plugins
- [ ] Proper authentication configured (GitHub, OIDC, etc.)
- [ ] Database persistence (replace emptyDir with PVC)
- [ ] External database for production (managed PostgreSQL)
- [ ] SSL/TLS certificates for ingress
- [ ] Monitoring and logging integration
- [ ] Resource limits and requests configured
- [ ] Multi-replica setup for high availability
- [ ] Backup procedures implemented

## Advanced Examples

See the production examples for more sophisticated setups:

- **Basic Setup**: `examples/advanced/production-backstage/`
- **Custom Plugins**: `examples/advanced/production-backstage/plugins/`
- **Multi-Environment**: `examples/advanced/production-backstage/environments/`

## Getting Help

- **Documentation**: [docs/installation.md#production-deployment](installation.md#production-deployment)
- **Troubleshooting**: [docs/troubleshooting.md](troubleshooting.md)
- **Official Backstage Docs**: https://backstage.io/docs/
- **GitHub Issues**: https://github.com/vek0ng/idp-stack/issues
