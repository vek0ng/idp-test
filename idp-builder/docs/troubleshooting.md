# IDP Builder Troubleshooting Guide

This guide provides solutions to common issues encountered when using IDP Builder, along with debugging techniques and recovery procedures.

## Quick Diagnostics

### Platform Health Check
```bash
# Run comprehensive health check
task workflows:health-check

# Check individual components
task gitops:health-check
task crossplane:health-check
task backstage:health-check

# Show cluster status
task cluster:cluster-status
```

### System Information
```bash
# Show current configuration
task environment:show-config

# Display provider status
task providers:show-provider-status

# Check prerequisites
task prerequisites:check-all
```

## Common Issues

### 1. Prerequisites and Installation

#### Issue: `task` command not found
**Symptoms:**
```bash
$ task prerequisites:check-all
bash: task: command not found
```

**Solution:**
```bash
# Install Task (Go Task)
# On macOS
brew install go-task/tap/go-task

# On Linux
sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d

# On Windows (PowerShell)
winget install Task.Task
```

**Verification:**
```bash
task --version
```

---

#### Issue: Docker not running
**Symptoms:**
```bash
ERROR: Docker daemon not running
```

**Solutions:**

**Linux:**
```bash
# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

**macOS:**
```bash
# Start Docker Desktop
open /Applications/Docker.app
```

**Windows:**
```bash
# Start Docker Desktop from Start Menu
```

**Verification:**
```bash
docker info
```

---

#### Issue: kubectl not configured
**Symptoms:**
```bash
ERROR: kubectl not configured or cluster unreachable
```

**Solution:**
```bash
# For Kind clusters
kind get kubeconfig --name idp-cluster > ~/.kube/config

# For cloud providers
# AWS EKS
aws eks update-kubeconfig --region us-east-1 --name my-cluster

# Google GKE
gcloud container clusters get-credentials my-cluster --zone us-central1-a

# Azure AKS
az aks get-credentials --resource-group my-rg --name my-cluster
```

**Verification:**
```bash
kubectl cluster-info
kubectl get nodes
```

### 2. Cloud Provider Issues

#### Issue: AWS authentication failure
**Symptoms:**
```bash
ERROR: Unable to locate credentials
ERROR: The security token included in the request is invalid
```

**Solutions:**

**Configure AWS CLI:**
```bash
# Method 1: AWS CLI
aws configure
# Enter: Access Key ID, Secret Access Key, Region, Output format

# Method 2: Environment variables
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="us-east-1"

# Method 3: AWS profiles
aws configure --profile idp-platform
export AWS_PROFILE=idp-platform
```

**Check IAM permissions:**
```bash
# Verify identity
aws sts get-caller-identity

# Test EKS permissions
aws eks list-clusters
```

**Required AWS permissions:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "eks:*",
        "ec2:*",
        "iam:PassRole",
        "iam:CreateServiceLinkedRole"
      ],
      "Resource": "*"
    }
  ]
}
```

---

#### Issue: Google Cloud authentication failure
**Symptoms:**
```bash
ERROR: (gcloud.auth.activate-service-account) Could not find the file
ERROR: Default credentials not found
```

**Solutions:**

**Authenticate with gcloud:**
```bash
# Interactive login
gcloud auth login
gcloud auth application-default login

# Service account
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/service-account.json"
gcloud auth activate-service-account --key-file=/path/to/service-account.json

# Set project
gcloud config set project YOUR_PROJECT_ID
```

**Verify setup:**
```bash
gcloud auth list
gcloud config get-value project
gcloud container clusters list
```

---

#### Issue: Azure authentication failure
**Symptoms:**
```bash
ERROR: No subscriptions found for account
ERROR: The client does not have authorization
```

**Solutions:**

**Authenticate with Azure CLI:**
```bash
# Interactive login
az login

# Service principal
az login --service-principal \
  --username $AZURE_CLIENT_ID \
  --password $AZURE_CLIENT_SECRET \
  --tenant $AZURE_TENANT_ID

# Set subscription
az account set --subscription "your-subscription-id"
```

**Verify setup:**
```bash
az account show
az aks list
```

### 3. Cluster Issues

#### Issue: Kind cluster creation fails
**Symptoms:**
```bash
ERROR: failed to create cluster: failed to create nodes
ERROR: failed to find an available port for the control plane
```

**Solutions:**

**Check Docker resources:**
```bash
# Verify Docker has sufficient resources
docker system df
docker system prune

# Check running containers
docker ps
```

**Port conflicts:**
```bash
# Find processes using common ports
sudo netstat -tulpn | grep :80
sudo netstat -tulpn | grep :443
sudo netstat -tulpn | grep :6443

# Stop conflicting services or change Kind config
```

**Clean up previous clusters:**
```bash
# List Kind clusters
kind get clusters

# Delete old clusters
kind delete cluster --name old-cluster-name

# Create fresh cluster
task cluster:create-cluster CLUSTER_NAME=fresh-cluster PROVIDER=kind
```

---

#### Issue: EKS cluster creation timeout
**Symptoms:**
```bash
ERROR: Cluster creation timeout after 30 minutes
ERROR: Resource creation cancelled
```

**Solutions:**

**Check AWS service limits:**
```bash
# Check VPC limits
aws ec2 describe-account-attributes --attribute-names supported-platforms

# Check EIP limits
aws ec2 describe-account-attributes --attribute-names max-elastic-ips
```

**Verify subnet configuration:**
```bash
# Check available subnets
aws ec2 describe-subnets --filters "Name=state,Values=available"

# Check NAT gateway requirements
aws ec2 describe-nat-gateways
```

**Manual cleanup if stuck:**
```bash
# Delete CloudFormation stacks
aws cloudformation delete-stack --stack-name eksctl-cluster-name

# Clean up orphaned resources
eksctl delete cluster --name cluster-name --region us-east-1 --wait
```

### 4. Platform Component Issues

#### Issue: ArgoCD installation fails
**Symptoms:**
```bash
ERROR: Failed to install ArgoCD: timeout waiting for condition
ERROR: Unable to connect to ArgoCD server
```

**Solutions:**

**Check cluster resources:**
```bash
# Check node resources
kubectl top nodes
kubectl describe nodes

# Check namespace events
kubectl get events -n argocd --sort-by='.lastTimestamp'
```

**Manual ArgoCD troubleshooting:**
```bash
# Check ArgoCD pods
kubectl get pods -n argocd

# Check pod logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-server

# Restart ArgoCD components
kubectl rollout restart deployment -n argocd argocd-server
```

**Resource requirements:**
```yaml
# Minimum cluster requirements for ArgoCD
resources:
  requests:
    memory: "2Gi"
    cpu: "1000m"
  limits:
    memory: "4Gi" 
    cpu: "2000m"
```

---

#### Issue: Crossplane provider installation fails
**Symptoms:**
```bash
ERROR: Provider package installation failed
ERROR: Provider not healthy after installation
```

**Solutions:**

**Check provider status:**
```bash
# List installed providers
kubectl get providers

# Check provider details
kubectl describe provider provider-aws

# Check provider pods
kubectl get pods -n crossplane-system
```

**Manual provider troubleshooting:**
```bash
# Check provider logs
kubectl logs -n crossplane-system -l pkg.crossplane.io/provider=aws

# Reinstall provider
kubectl delete provider provider-aws
task crossplane:install-providers PROVIDER=aws
```

**Common provider issues:**
```bash
# AWS provider - check IAM permissions
kubectl logs -n crossplane-system -l pkg.crossplane.io/provider=aws | grep -i error

# GCP provider - check service account
kubectl get secret -n crossplane-system gcp-secret -o yaml
```

---

#### Issue: Backstage startup errors
**Symptoms:**
```bash
ERROR: Backstage failed to start: Configuration error
ERROR: Database connection failed
```

**Solutions:**

**Check configuration:**
```bash
# Check Backstage configuration
kubectl get configmap -n backstage backstage-config -o yaml

# Check database connectivity
kubectl exec -n backstage deployment/backstage -- pg_isready -h postgres
```

**Database issues:**
```bash
# Check PostgreSQL status
kubectl get pods -n backstage -l app=postgresql

# Reset database if needed
kubectl delete pvc -n backstage data-postgresql-0
kubectl delete pod -n backstage -l app=postgresql
```

**Check integrations:**
```bash
# Verify GitHub token
curl -H "Authorization: token $GITHUB_TOKEN" https://api.github.com/user

# Check ArgoCD connectivity
kubectl exec -n backstage deployment/backstage -- curl -k https://argocd-server.argocd.svc.cluster.local
```

### 5. Network and Connectivity Issues

#### Issue: Ingress not working
**Symptoms:**
```bash
ERROR: Service temporarily unavailable
ERROR: Connection timed out
```

**Solutions:**

**Check ingress controller:**
```bash
# Check ingress controller pods
kubectl get pods -n ingress-nginx

# Check ingress controller logs
kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx

# Check ingress resources
kubectl get ingress --all-namespaces
```

**Verify DNS resolution:**
```bash
# Check DNS configuration
nslookup backstage.platform.local

# Check /etc/hosts for local development
cat /etc/hosts | grep platform.local
```

**LoadBalancer issues (cloud providers):**
```bash
# AWS - check ELB status
aws elbv2 describe-load-balancers

# GCP - check external IP
kubectl get services -n ingress-nginx

# Azure - check public IP
az network public-ip list --resource-group MC_*
```

---

#### Issue: TLS certificate problems
**Symptoms:**
```bash
ERROR: x509: certificate signed by unknown authority
ERROR: TLS handshake timeout
```

**Solutions:**

**Check cert-manager:**
```bash
# Check cert-manager pods
kubectl get pods -n cert-manager

# Check certificates
kubectl get certificates --all-namespaces

# Check certificate details
kubectl describe certificate -n backstage backstage-tls
```

**Manual certificate troubleshooting:**
```bash
# Check certificate logs
kubectl logs -n cert-manager -l app=cert-manager

# Force certificate renewal
kubectl delete secret -n backstage backstage-tls
kubectl delete certificate -n backstage backstage-tls
```

### 6. Performance Issues

#### Issue: Slow cluster performance
**Symptoms:**
- High pod startup times
- API server timeouts
- Resource scheduling delays

**Solutions:**

**Check resource utilization:**
```bash
# Node resources
kubectl top nodes

# Pod resources
kubectl top pods --all-namespaces

# Check resource quotas
kubectl get resourcequota --all-namespaces
```

**Optimize cluster settings:**
```bash
# Increase node resources
task cluster:scale-nodes CLUSTER_NAME=my-cluster NODE_COUNT=5

# Check for resource constraints
kubectl describe nodes | grep -A 5 "Allocated resources"
```

**Database performance (PostgreSQL):**
```bash
# Check database metrics
kubectl exec -n backstage deployment/postgresql -- psql -U postgres -c "SELECT * FROM pg_stat_database;"

# Optimize database settings
kubectl patch configmap postgresql-config -n backstage --patch '{"data":{"postgresql.conf":"shared_preload_libraries = '\''pg_stat_statements'\''\nmax_connections = 200"}}'
```

### 7. Data and State Issues

#### Issue: Lost cluster state
**Symptoms:**
- Applications showing as "Unknown"
- Missing ArgoCD applications
- Configuration drift

**Solutions:**

**Restore from backup:**
```bash
# List available backups
task workflows:list-backups CLUSTER_NAME=my-cluster

# Restore from backup
task workflows:disaster-recovery \
  CLUSTER_NAME=my-cluster \
  BACKUP_DATE=2024-01-15 \
  RESTORE_TYPE=config-only
```

**Manual state recovery:**
```bash
# Recreate ArgoCD applications
kubectl apply -f argocd/

# Sync all applications
argocd app sync --all

# Force refresh
argocd app get backstage --refresh
```

---

#### Issue: Persistent volume issues
**Symptoms:**
```bash
ERROR: Pod stuck in Pending state
ERROR: PersistentVolumeClaim is Pending
```

**Solutions:**

**Check storage classes:**
```bash
# List storage classes
kubectl get storageclass

# Check PVC status
kubectl get pvc --all-namespaces

# Check PV status  
kubectl get pv
```

**Fix storage issues:**
```bash
# For Kind clusters - ensure local-path provisioner is running
kubectl get pods -n local-path-storage

# For cloud providers - check CSI drivers
kubectl get pods -n kube-system | grep csi
```

## Advanced Debugging

### Enable Debug Mode
```bash
# Enable verbose logging for all tasks
export DEBUG=true
export VERBOSE=true

# Run tasks with debug output
task workflows:health-check
```

### Collect Debug Information
```bash
#!/bin/bash
# debug-collection.sh

echo "=== IDP Platform Debug Information ==="
echo "Date: $(date)"
echo

echo "=== Environment ==="
task environment:show-config
echo

echo "=== Cluster Info ==="
kubectl cluster-info
kubectl get nodes -o wide
echo

echo "=== Platform Components ==="
kubectl get pods --all-namespaces
echo

echo "=== Recent Events ==="
kubectl get events --all-namespaces --sort-by='.lastTimestamp' | tail -20
echo

echo "=== Storage ==="
kubectl get pv,pvc --all-namespaces
echo

echo "=== Network ==="
kubectl get services --all-namespaces
kubectl get ingress --all-namespaces
```

### Log Collection
```bash
# Collect logs for troubleshooting
mkdir -p debug-logs/$(date +%Y%m%d)

# ArgoCD logs
kubectl logs -n argocd --selector app.kubernetes.io/name=argocd-server \
  > debug-logs/$(date +%Y%m%d)/argocd-server.log

# Crossplane logs
kubectl logs -n crossplane-system --selector app=crossplane \
  > debug-logs/$(date +%Y%m%d)/crossplane.log

# Backstage logs
kubectl logs -n backstage --selector app.kubernetes.io/name=backstage \
  > debug-logs/$(date +%Y%m%d)/backstage.log
```

## Recovery Procedures

### Complete Platform Recovery
```bash
#!/bin/bash
# complete-recovery.sh

set -e

CLUSTER_NAME=${1:-"recovery-cluster"}
PROVIDER=${2:-"kind"}

echo "Starting complete platform recovery..."

# 1. Verify prerequisites
task prerequisites:check-all

# 2. Create fresh cluster if needed
if ! kubectl cluster-info &>/dev/null; then
  echo "Creating new cluster..."
  task cluster:create-cluster CLUSTER_NAME=$CLUSTER_NAME PROVIDER=$PROVIDER
fi

# 3. Install platform components
echo "Installing platform components..."
task gitops:install-argocd
task crossplane:install-crossplane
task backstage:install-backstage

# 4. Restore from backup
echo "Restoring configuration..."
task workflows:disaster-recovery \
  CLUSTER_NAME=$CLUSTER_NAME \
  BACKUP_DATE=$(date -d "yesterday" +%Y-%m-%d) \
  RESTORE_TYPE=config-only

# 5. Validate setup
echo "Validating setup..."
task workflows:validate-complete-setup CLUSTER_NAME=$CLUSTER_NAME

echo "Recovery completed successfully!"
```

### Component-Specific Recovery

#### ArgoCD Recovery
```bash
# Remove corrupted ArgoCD installation
kubectl delete namespace argocd --force --grace-period=0

# Reinstall ArgoCD
task gitops:install-argocd

# Restore applications
kubectl apply -f argocd/app.yaml
```

#### Backstage Recovery
```bash
# Scale down Backstage
kubectl scale deployment backstage -n backstage --replicas=0

# Reset database
kubectl delete pvc data-postgresql-0 -n backstage

# Scale up Backstage
kubectl scale deployment backstage -n backstage --replicas=1
```

## Getting Help

### Community Support
- **GitHub Issues**: [IDP Stack Issues](https://github.com/vek0ng/idp-stack/issues)
- **Discussions**: [IDP Stack Discussions](https://github.com/vek0ng/idp-stack/discussions)

### Professional Support
- **Enterprise Support**: Contact for enterprise-grade support options
- **Consulting Services**: Professional services for implementation and customization

### Useful Resources
- **Kubernetes Troubleshooting**: [Official Guide](https://kubernetes.io/docs/tasks/debug-application-cluster/)
- **ArgoCD Troubleshooting**: [ArgoCD Docs](https://argo-cd.readthedocs.io/en/stable/operator-manual/troubleshooting/)
- **Crossplane Troubleshooting**: [Crossplane Docs](https://crossplane.io/docs/troubleshoot/)

Remember to always check the [API Reference](api-reference.md) for the most up-to-date task parameters and options.
