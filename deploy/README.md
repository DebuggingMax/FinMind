# FinMind Deployment Configurations

This folder contains deployment configurations for all supported platforms.

## Structure

```
deploy/
├── railway/           # Railway one-click deploy
│   ├── railway.json
│   └── README.md
├── render/            # Render Blueprint
│   ├── render.yaml
│   └── README.md
├── fly/               # Fly.io deployment
│   ├── fly.toml
│   └── README.md
├── heroku/            # Heroku Container deployment
│   ├── heroku.yml
│   ├── app.json
│   └── README.md
├── digitalocean/      # DigitalOcean App Platform
│   ├── .do/app.yaml
│   └── README.md
├── aws/               # AWS ECS/Fargate
│   ├── ecs-task-definition.json
│   └── README.md
├── gcp/               # Google Cloud Run
│   ├── cloudrun.yaml
│   └── README.md
├── azure/             # Azure Container Apps
│   ├── container-app.yaml
│   └── README.md
├── helm/              # Kubernetes Helm chart
│   ├── finmind/
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   └── README.md
├── k8s/               # Raw Kubernetes manifests
│   ├── namespace.yaml
│   ├── app-stack.yaml
│   ├── secrets.example.yaml
│   └── monitoring-stack.yaml
├── tilt/              # Tilt local K8s development
│   └── README.md
├── nginx/             # Production Nginx config
│   └── nginx.prod.conf
├── vercel/            # Vercel frontend config
│   └── vercel.json
├── netlify/           # Netlify frontend config
│   └── netlify.toml
└── frontend/          # Frontend deployment guide
    └── README.md
```

## Quick Reference

| Platform | One-Click | Free Tier | Backend | Frontend |
|----------|-----------|-----------|---------|----------|
| Railway | ✅ | ✅ $5/mo | ✅ | ❌ |
| Render | ✅ | ✅ | ✅ | ✅ |
| Fly.io | ❌ | ✅ | ✅ | ✅ |
| Heroku | ✅ | ❌ | ✅ | ❌ |
| DigitalOcean | ✅ | ❌ | ✅ | ✅ |
| AWS | ❌ | ❌ | ✅ | ✅ |
| GCP | ❌ | ✅ | ✅ | ✅ |
| Azure | ❌ | ✅ | ✅ | ✅ |
| Kubernetes | ❌ | N/A | ✅ | ✅ |
| Vercel | ✅ | ✅ | ❌ | ✅ |
| Netlify | ✅ | ✅ | ❌ | ✅ |

## Recommended Setups

### Free Tier (Development)
- **Backend**: Railway or Render (free PostgreSQL + Redis)
- **Frontend**: Vercel or Netlify

### Production (Small Scale)
- **Full Stack**: DigitalOcean App Platform or Render
- **Cost**: ~$20-40/month

### Production (Scale)
- **Backend**: Kubernetes (EKS/GKE/AKS) with Helm chart
- **Frontend**: Vercel/Cloudflare
- **Cost**: $100+/month

### Self-Hosted (VPS)
- Use `docker-compose.prod.yml`
- Any VPS with 2GB+ RAM
- **Cost**: $10-20/month
