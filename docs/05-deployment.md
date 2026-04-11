# Fase 5: Despliegue

> CRISP-ML(Q) — Deployment

## 5.1 Estrategia de Despliegue

El ai-service se despliega como un **microservicio independiente** dentro de la arquitectura de Carrillo Abogados, expuesto a traves de un API Gateway.

### Arquitectura de produccion

```
[Browser]
    │
    ▼
[Vercel - Next.js Frontend]          [n8n Cloud]
  app.carrilloabgd.com                 ├── Lead Scoring (Gemini)
    │                                  ├── Schedule Appointment
    ▼                                  └── Email Notifications
[Cloud Run - API Gateway :8080]
  api.carrilloabgd.com
    │
    ├── /blog-service/*     → blog-service :8900
    ├── /client-service/*   → client-service :8200
    ├── /case-service/*     → case-service :8300
    └── /ai-service/*       → ai-service :8901  ←── Moni
                                  │
                                  ▼
                            [Neon PostgreSQL]
                              pgvector + schema assistant
```

## 5.2 Infraestructura

### Cloud Run (GCP)

| Parametro | Valor | Justificacion |
|-----------|-------|---------------|
| **Region** | us-central1 | Baja latencia + tier gratuito |
| **CPU** | 1 vCPU | Suficiente para FastAPI + uvicorn |
| **Memoria** | 512 Mi | Footprint ligero (Python slim) |
| **min_instances** | 1 | Evitar cold starts |
| **max_instances** | 5 | Limite de costo |
| **Ingress** | ALL | MVP — pendiente VPC egress post-MVP |
| **Concurrency** | 80 | Default Cloud Run |

### Container

```dockerfile
FROM python:3.12-slim AS builder
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.12-slim AS runner
RUN adduser --disabled-password --uid 1001 appuser
COPY --from=builder /usr/local/lib/python3.12/site-packages /usr/local/lib/python3.12/site-packages
COPY app/ app/
USER appuser
EXPOSE 8901
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8901/health')"
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8901", "--workers", "2"]
```

**Caracteristicas**:
- Multi-stage build (minimiza imagen final)
- Usuario no-root (seguridad)
- Health check nativo en Docker
- 2 workers uvicorn

### Base de datos (Neon)

| Parametro | Valor |
|-----------|-------|
| **Provider** | Neon (PostgreSQL serverless) |
| **Version** | PostgreSQL 16 |
| **Extension** | pgvector |
| **Region** | us-east-1 |
| **Scale-to-zero** | Habilitado (ahorro de costo) |
| **Connection pooling** | PgBouncer integrado |

## 5.3 Secrets Management

Todos los secrets se gestionan via **Google Secret Manager**:

| Secret | Servicio |
|--------|----------|
| gemini-api-key | ai-service |
| ai-database-url | ai-service |

Los secrets se montan como variables de entorno en Cloud Run, nunca en codigo ni archivos de configuracion.

## 5.4 CI/CD Pipeline

```
[Developer]
    │ git push
    ▼
[GitHub Actions]
    ├── Build: mvn package jib:buildTar (Java) / docker build (Python)
    ├── Push: Google Artifact Registry (carrillo-admin-ops)
    └── Deploy: gcloud run services update --image IMAGE --update-env-vars
                                           --update-secrets --min-instances=1
```

**Regla critica**: NUNCA usar `--set-env-vars` (reemplaza todo). SIEMPRE `--update-env-vars`.

## 5.5 Networking

| Componente | Dominio | DNS |
|-----------|---------|-----|
| Frontend | app.carrilloabgd.com | Vercel CNAME |
| API Gateway | api.carrilloabgd.com | Cloud Run domain mapping |
| ai-service | ai.api.carrilloabgd.com | Cloud Run domain mapping |

DNS gestionado en Cloudflare (DNS Only, sin proxy). SSL terminado en Cloud Run.

## 5.6 Rate Limiting

| Parametro | Valor | Scope |
|-----------|-------|-------|
| Limite | 20 requests/minuto | Por IP |
| Respuesta | 429 Too Many Requests | JSON error |
| Implementacion | slowapi (based on limits) | Middleware FastAPI |

## 5.7 CORS

Origenes permitidos en produccion:
- `https://app.carrilloabgd.com`
- `https://api.carrilloabgd.com`
- `http://localhost:3000` (desarrollo)
- `http://localhost:8080` (desarrollo)

## 5.8 Monitoreo Post-Deploy

### Health check

```bash
# Verificacion basica
curl -s https://api.carrilloabgd.com/ai-service/health | python3 -m json.tool
# Esperado: {"status": "healthy", "service": "ai-service", "version": "0.1.0"}
```

### Verificacion de deployment

1. Health check retorna 200
2. CORS headers presentes en respuesta
3. Rate limit activo (X-RateLimit headers)
4. Chat endpoint responde con JSON valido
5. RAG retorna documentos relevantes
