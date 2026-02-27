# FinMind Deployment Guide

Universal one-click deployment support for FinMind across all major platforms.

## 🚀 Quick Deploy Buttons

### PaaS Platforms (One-Click)

| Platform | Deploy Button | Free Tier |
|----------|---------------|-----------|
| Railway | [![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/finmind) | ✅ $5/mo credit |
| Render | [![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/rohitdash08/FinMind) | ✅ Limited |
| Heroku | [![Deploy to Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/rohitdash08/FinMind) | ❌ |
| DigitalOcean | [![Deploy to DO](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/rohitdash08/FinMind/tree/main) | ❌ |

### Frontend Only

| Platform | Deploy Button |
|----------|---------------|
| Vercel | [![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/rohitdash08/FinMind&root-directory=app) |
| Netlify | [![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/rohitdash08/FinMind) |

## 📦 Deployment Options

### 1. Docker Compose (Local/VPS)

**Development:**
```bash
cp .env.example .env
# Edit .env with your secrets
docker compose up --build
```

**Production:**
```bash
cp .env.example .env.production
# Edit .env.production with production secrets
docker compose -f docker-compose.prod.yml up -d
```

### 2. Kubernetes (Helm)

```bash
cd deploy/helm/finmind
helm dependency update
helm install finmind . \
  --namespace finmind --create-namespace \
  --set secrets.jwtSecret=$(openssl rand -hex 32) \
  --set postgresql.auth.password=$(openssl rand -base64 16) \
  --set ingress.hosts[0].host=api.finmind.example.com
```

### 3. Local K8s Development (Tilt)

```bash
# Prerequisites: Docker Desktop/Rancher with K8s, Tilt
cp deploy/k8s/secrets.example.yaml deploy/k8s/secrets.yaml
# Edit secrets.yaml
tilt up
```

## 🌐 Platform-Specific Guides

| Platform | Guide | Config Files |
|----------|-------|--------------|
| Railway | [deploy/railway/README.md](deploy/railway/README.md) | `railway.json` |
| Render | [deploy/render/README.md](deploy/render/README.md) | `render.yaml` |
| Fly.io | [deploy/fly/README.md](deploy/fly/README.md) | `fly.toml` |
| Heroku | [deploy/heroku/README.md](deploy/heroku/README.md) | `heroku.yml`, `app.json` |
| DigitalOcean | [deploy/digitalocean/README.md](deploy/digitalocean/README.md) | `.do/app.yaml` |
| AWS ECS/Fargate | [deploy/aws/README.md](deploy/aws/README.md) | `ecs-task-definition.json` |
| GCP Cloud Run | [deploy/gcp/README.md](deploy/gcp/README.md) | `cloudrun.yaml` |
| Azure Container Apps | [deploy/azure/README.md](deploy/azure/README.md) | `container-app.yaml` |
| Kubernetes | [deploy/helm/README.md](deploy/helm/README.md) | Helm chart |
| Tilt (Local K8s) | [deploy/tilt/README.md](deploy/tilt/README.md) | `Tiltfile` |

## 🔧 Environment Variables

### Required

| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | PostgreSQL connection string |
| `REDIS_URL` | Redis connection string |
| `JWT_SECRET` | JWT signing secret (generate: `openssl rand -hex 32`) |

### Optional

| Variable | Description | Default |
|----------|-------------|---------|
| `GEMINI_API_KEY` | Google Gemini API key | - |
| `OPENAI_API_KEY` | OpenAI API key (alternative) | - |
| `LOG_LEVEL` | Logging level | `INFO` |
| `GEMINI_MODEL` | Gemini model name | `gemini-1.5-flash` |
| `TWILIO_ACCOUNT_SID` | Twilio account SID | - |
| `TWILIO_AUTH_TOKEN` | Twilio auth token | - |
| `TWILIO_WHATSAPP_FROM` | Twilio WhatsApp number | - |
| `EMAIL_FROM` | Email sender address | - |
| `SMTP_URL` | SMTP connection string | - |

### Frontend

| Variable | Description |
|----------|-------------|
| `VITE_API_URL` | Backend API URL |

## 🏗️ Architecture

```
┌─────────────────┐     ┌─────────────────┐
│   Frontend      │────▶│   CDN/Edge      │
│   (React/Vite)  │     │ (Vercel/Netlify)│
└─────────────────┘     └────────┬────────┘
                                 │
                                 ▼
┌─────────────────┐     ┌─────────────────┐
│   Load Balancer │────▶│   Backend API   │
│   (Nginx/ALB)   │     │   (Flask)       │
└─────────────────┘     └────────┬────────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
           ┌─────────────────┐       ┌─────────────────┐
           │   PostgreSQL    │       │     Redis       │
           │   (Database)    │       │    (Cache)      │
           └─────────────────┘       └─────────────────┘
```

## 🔒 Security Checklist

- [ ] Generate strong JWT secret: `openssl rand -hex 32`
- [ ] Use strong PostgreSQL password
- [ ] Enable HTTPS/TLS
- [ ] Set up rate limiting
- [ ] Configure CORS properly
- [ ] Enable security headers
- [ ] Set up WAF (production)
- [ ] Regular security updates

## 📊 Monitoring

All deployments include Prometheus metrics at `/metrics`:
- Request count by endpoint/status
- Request duration histograms
- Reminder event counters

For full observability stack (Grafana, Loki, Prometheus), use:
```bash
docker compose up  # Includes monitoring
```

## 🆘 Troubleshooting

### Database Connection Issues
```bash
# Check DATABASE_URL format
postgresql+psycopg2://USER:PASSWORD@HOST:5432/DATABASE

# Test connection
docker compose exec backend python -c "from app import db; print(db.engine.url)"
```

### Redis Connection Issues
```bash
# Check REDIS_URL format
redis://HOST:6379/0

# Test connection
docker compose exec backend python -c "import redis; r = redis.from_url('redis://redis:6379/0'); print(r.ping())"
```

### Health Check Failing
```bash
# Check backend logs
docker compose logs backend

# Test health endpoint
curl http://localhost:8000/health
```

## 📝 License

MIT License - See [LICENSE](LICENSE)
