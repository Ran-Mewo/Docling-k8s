# Doclings Kubernetes Deployment

Kubernetes deployment for Doclings document processing service.

## 🚀 Quick Start

### Option 1: Single YAML File (Simplest)

1. **Configure Domain**: Edit `deploy.yaml` and replace `doclings.yourdomain.com` with your domain
2. **Deploy**: `kubectl apply -f deploy.yaml`
3. **Access**: Visit `https://<your domain>` (or use port-forward for testing)

### Option 2: Helm Chart (Enterprise)

1. **Configure Domain**: Edit `helm/doclings/values.yaml` and set your domain in `ingress.hosts[0].host`
2. **Deploy**: `helm install doclings helm/doclings`
3. **Access**: Visit your configured domain

**Quick Helm install with custom domain:**
```bash
helm install doclings helm/doclings --set ingress.hosts[0].host=doclings.mydomain.com
```

## 📋 Requirements

- Kubernetes cluster (any version)
- kubectl configured
- Ingress controller (nginx, traefik, etc.) for external access
- 1GB+ RAM available in cluster

## 🌐 External Access Options

### Option 1: Ingress (Recommended)
**Single YAML**: Edit the domain in `deploy.yaml`:
```yaml
rules:
- host: doclings.yourdomain.com  # CHANGE THIS
```

**Helm**: Set domain in values or command line:
```bash
helm install doclings helm/doclings --set ingress.hosts[0].host=doclings.mydomain.com
```

### Option 2: LoadBalancer (Cloud Provider)
**Single YAML**: Uncomment the LoadBalancer service in `deploy.yaml`

**Helm**: Change service type:
```bash
helm install doclings helm/doclings \
  --set service.type=LoadBalancer \
  --set ingress.enabled=false
```

### Option 3: NodePort (Development)
**Helm**: Use NodePort:
```bash
helm install doclings helm/doclings \
  --set service.type=NodePort \
  --set service.nodePort=30501 \
  --set ingress.enabled=false
```

### Option 4: Port-Forward (Local Testing)
```bash
kubectl port-forward svc/doclings 5001:5001
# Access at: http://localhost:5001
```

## 🔧 Configuration

### Basic Configuration

**Single YAML**: Edit the ConfigMap in `deploy.yaml`:
```yaml
data:
  DOCLING_SERVE_ENABLE_UI: "true"
  # Add additional environment variables here
  # DOCLING_SERVE_HOST: "0.0.0.0"
  # DOCLING_SERVE_PORT: "5001"
```

**Helm**: Edit `helm/doclings/values.yaml`:
```yaml
config:
  enableUI: true
  extraEnv:
    DOCLING_SERVE_HOST: "0.0.0.0"
    DOCLING_SERVE_PORT: "5001"
```

### HTTPS/TLS Configuration

**Single YAML**: Uncomment TLS section in `deploy.yaml`:
```yaml
spec:
  tls:
  - hosts:
    - doclings.yourdomain.com
    secretName: doclings-tls
```

**Helm**: Enable TLS in `helm/doclings/values.yaml`:
```yaml
ingress:
  enabled: true
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
  tls:
  - secretName: doclings-tls
    hosts:
    - doclings.yourdomain.com
```

### Storage Configuration (Optional)

If you need persistent storage for document processing cache:

**Helm**: Enable storage in `helm/doclings/values.yaml`:
```yaml
storage:
  enabled: true
  size: 5Gi
  storageClass: "fast-ssd"  # Your storage class
  mountPath: /app/cache
```

### Resource Configuration

**Single YAML**: Edit resources in `deploy.yaml`:
```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "2Gi"
    cpu: "1000m"
```

**Helm**: Edit `helm/doclings/values.yaml`:
```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "2Gi"
    cpu: "1000m"
```

### Scaling Configuration

**Helm**: Set replica count:
```bash
helm install doclings helm/doclings --set replicaCount=3
```

## 🗑️ Cleanup

**Single YAML**: `kubectl delete -f deploy.yaml`
**Helm**: `helm uninstall doclings`

## 🔍 Troubleshooting

```bash
# Check status
kubectl get pods -n doclings

# View logs
kubectl logs -n doclings -l app=doclings

# Describe pod for issues
kubectl describe pod -n doclings -l app=doclings

# Check service
kubectl get svc -n doclings

# Check ingress
kubectl get ingress -n doclings
```