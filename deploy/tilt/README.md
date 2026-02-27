# Tilt Local Development

[Tilt](https://tilt.dev/) provides fast, iterative local Kubernetes development with live updates.

## Prerequisites

1. **Kubernetes cluster** (any of):
   - Docker Desktop with Kubernetes enabled
   - Rancher Desktop
   - minikube
   - kind

2. **Tilt CLI**:
   ```bash
   # macOS
   brew install tilt
   
   # Linux
   curl -fsSL https://raw.githubusercontent.com/tilt-dev/tilt/master/scripts/install.sh | bash
   
   # Windows
   scoop install tilt
   ```

## Quick Start

```bash
# 1. Copy secrets
cp deploy/k8s/secrets.example.yaml deploy/k8s/secrets.yaml
# Edit deploy/k8s/secrets.yaml with real values

# 2. Start Tilt
tilt up

# 3. Open Tilt UI
# Tilt automatically opens http://localhost:10350
```

## Features

### Live Updates
- Backend code changes sync instantly without rebuild
- Frontend hot-reload works automatically
- Dependency changes trigger rebuild

### Port Forwards
| Service | Local Port |
|---------|------------|
| Backend | 8000 |
| Nginx | 8080 |
| PostgreSQL | 5432 |
| Redis | 6379 |
| Grafana | 3000 |
| Prometheus | 9090 |

### Manual Triggers
From Tilt UI (http://localhost:10350):
- `backend-tests` - Run pytest
- `frontend-tests` - Run vitest

## Configuration

### Production Mode
```bash
tilt up -- --production
```
This enables the full monitoring stack.

### Custom Cluster
```bash
# minikube
minikube start
tilt up

# kind
kind create cluster --name finmind
tilt up
```

## Workflow

1. Make code changes
2. Tilt detects changes and syncs/rebuilds
3. Pod restarts with new code
4. Test at localhost:8000

## Troubleshooting

```bash
# View logs
tilt logs backend

# Restart resource
tilt trigger backend

# Clean up
tilt down
kubectl delete namespace finmind
```

## Commands

```bash
tilt up           # Start development
tilt down         # Stop and clean up
tilt logs <name>  # View resource logs
tilt trigger <n>  # Force rebuild
tilt args -- --production  # Set flags
```
