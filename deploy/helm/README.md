# FinMind Helm Chart

Production-ready Kubernetes deployment with Helm.

## Prerequisites

- Kubernetes 1.25+
- Helm 3.10+
- Ingress controller (nginx-ingress recommended)
- cert-manager (optional, for TLS)

## Quick Start

```bash
# Add Bitnami repo for dependencies
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Install dependencies
cd deploy/helm/finmind
helm dependency update

# Install FinMind
helm install finmind . \
  --namespace finmind \
  --create-namespace \
  --set secrets.jwtSecret=$(openssl rand -hex 32) \
  --set postgresql.auth.password=$(openssl rand -base64 16) \
  --set ingress.hosts[0].host=api.finmind.example.com
```

## Configuration

### Required Values

| Parameter | Description | Example |
|-----------|-------------|---------|
| `secrets.jwtSecret` | JWT signing secret | `openssl rand -hex 32` |
| `postgresql.auth.password` | PostgreSQL password | `openssl rand -base64 16` |
| `ingress.hosts[0].host` | API hostname | `api.finmind.example.com` |

### Optional Values

| Parameter | Description | Default |
|-----------|-------------|---------|
| `secrets.geminiApiKey` | Gemini API key | `""` |
| `backend.replicaCount` | Backend replicas | `2` |
| `backend.autoscaling.enabled` | Enable HPA | `true` |
| `backend.autoscaling.maxReplicas` | Max replicas | `10` |
| `ingress.enabled` | Enable ingress | `true` |
| `ingress.className` | Ingress class | `nginx` |
| `postgresql.enabled` | Deploy PostgreSQL | `true` |
| `redis.enabled` | Deploy Redis | `true` |

## Advanced Configuration

### External Database

```bash
helm install finmind . \
  --set postgresql.enabled=false \
  --set externalDatabase.url="postgresql://user:pass@host:5432/db"
```

### External Redis

```bash
helm install finmind . \
  --set redis.enabled=false \
  --set externalRedis.url="redis://host:6379/0"
```

### TLS with cert-manager

```bash
helm install finmind . \
  --set ingress.annotations."cert-manager\.io/cluster-issuer"=letsencrypt-prod \
  --set ingress.tls[0].secretName=finmind-tls \
  --set ingress.tls[0].hosts[0]=api.finmind.example.com
```

### Custom Values File

```bash
helm install finmind . -f my-values.yaml
```

Example `my-values.yaml`:
```yaml
backend:
  replicaCount: 3
  resources:
    limits:
      cpu: 1
      memory: 1Gi

ingress:
  hosts:
    - host: api.finmind.mycompany.com
      paths:
        - path: /
          pathType: Prefix
          service: backend

secrets:
  jwtSecret: "your-jwt-secret"
  geminiApiKey: "your-gemini-key"

postgresql:
  auth:
    password: "your-db-password"
```

## Upgrade

```bash
helm upgrade finmind . --namespace finmind -f my-values.yaml
```

## Uninstall

```bash
helm uninstall finmind --namespace finmind
kubectl delete namespace finmind
```

## Monitoring

If Prometheus Operator is installed:

```bash
helm install finmind . \
  --set metrics.serviceMonitor.enabled=true
```

## Troubleshooting

```bash
# Check pod status
kubectl get pods -n finmind

# View logs
kubectl logs -n finmind -l app.kubernetes.io/component=backend

# Describe deployment
kubectl describe deployment -n finmind finmind-backend

# Check ingress
kubectl get ingress -n finmind
```
