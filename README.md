# Moni — Asistente Virtual Legal con IA

**Proyecto de Innovacion Tecnologica en Inteligencia Artificial**
Maestria en IA Aplicada (MIAA) — Universidad Icesi, Cali, Colombia

---

## Descripcion

Moni es un asistente virtual inteligente desarrollado para **Carrillo ABGD SAS**, una firma de abogados con mas de 30 anos de experiencia en Cali, Colombia. El sistema utiliza **Retrieval-Augmented Generation (RAG)** para responder consultas legales frecuentes, capturar leads de forma conversacional, y agendar citas directamente en el calendario del equipo.

Este repositorio documenta el desarrollo de Moni siguiendo la metodologia **CRISP-ML(Q)** (Cross-Industry Standard Process for Machine Learning with Quality Assurance), y sirve como entregable academico del curso *Proyecto I de Innovacion Tecnologica en IA*.

## Stack Tecnico

| Componente | Tecnologia |
|-----------|-----------|
| **LLM** | Google Gemini 2.5 Flash |
| **Embeddings** | gemini-embedding-001 (768 dims, Matryoshka) |
| **Vector DB** | PostgreSQL 16 + pgvector (Neon serverless) |
| **Backend** | Python 3.12 + FastAPI 0.115 |
| **Frontend** | Next.js 16 + React 18 (TypeScript) |
| **Infraestructura** | GCP Cloud Run + Vercel |
| **Automatizacion** | n8n Cloud (lead scoring, agendamiento) |
| **SDK** | google-genai (API directa, sin LangChain) |

## Arquitectura

```
[Usuario] → [Frontend Next.js] → [API Gateway] → [ai-service FastAPI]
                                                         │
                                    ┌────────────────────┤
                                    │                    │
                              [pgvector RAG]      [Gemini 2.5 Flash]
                              Corpus 16 docs      System Prompt 98 lineas
                                    │                    │
                                    └────────┬───────────┘
                                             │
                                      [Respuesta JSON]
                                             │
                                    ┌────────┼────────────┐
                                    │        │            │
                              [Respuesta] [Lead]    [Agendamiento]
                               al usuario  Capture   via n8n + Calendar
```

## Documentacion CRISP-ML(Q)

| Fase | Documento | Descripcion |
|------|----------|-------------|
| 1 | [Business Understanding](docs/01-business-understanding.md) | Problema, pregunta SMART, KPIs, viabilidad |
| 2 | [Data Engineering](docs/02-data-engineering.md) | Arquitectura Medallion, pipeline embeddings, schema |
| 3 | [Model Engineering](docs/03-model-engineering.md) | RAG, seleccion de modelo, system prompt, lead detection |
| 4 | [Evaluation](docs/04-evaluation.md) | Metricas, tests, QA, decision de despliegue |
| 5 | [Deployment](docs/05-deployment.md) | Cloud Run, Docker, CI/CD, networking |
| 6 | [Monitoring](docs/06-monitoring.md) | Observabilidad, model staleness, actualizacion |
| — | [Ethics & Compliance](docs/07-ethics-compliance.md) | Ley 1581/2012, sesgos, impacto social |

## Estructura del Repositorio

```
miaa-ai-research/
├── docs/                          # Documentacion CRISP-ML(Q) del proyecto Moni
│   ├── 01-business-understanding.md
│   ├── 02-data-engineering.md
│   ├── 03-model-engineering.md
│   ├── 04-evaluation.md
│   ├── 05-deployment.md
│   ├── 06-monitoring.md
│   └── 07-ethics-compliance.md
├── notebooks/                     # Analisis y experimentacion reproducible
├── reports/                       # Entregas formales del curso
│   ├── etapa-1/                   # Reporte primera etapa (20%)
│   ├── etapa-2/                   # Reporte segunda etapa (10%)
│   └── reporte-final/             # Reporte final (70%)
├── references/                    # Papers y material de referencia
├── courses/                       # Material academico de la maestria
│   ├── analisis-datos-1/          # Analisis de Datos I (completado 2025-2)
│   ├── aprendizaje-automatico-1/  # Aprendizaje Automatico I (completado 2025-2)
│   ├── aprendizaje-automatico-2/  # Aprendizaje Automatico II (2026-1)
│   ├── extraccion-almacenamiento/ # Extraccion y Almacenamiento de Datos (2026-1)
│   └── proyecto-1/                # Proyecto I — este proyecto (2026-1)
├── CLAUDE.md                      # Instrucciones para Claude Code
└── README.md                      # Este archivo
```

## Codigo Productivo

El codigo fuente de Moni vive en el repositorio [`backend-services/ai-service`](https://github.com/Carrillo-Abogados/backend-services). Este repositorio contiene exclusivamente documentacion, analisis y experimentacion. No se duplica codigo productivo.

| Repositorio | Contenido |
|------------|-----------|
| [`backend-services`](https://github.com/Carrillo-Abogados/backend-services) | ai-service (FastAPI), 6 microservicios Java |
| [`frontend`](https://github.com/Carrillo-Abogados/frontend) | Next.js 16, widget de chat Moni |
| [`documentation`](https://github.com/Carrillo-Abogados/documentation) | Documentacion oficial del proyecto |
| **Este repositorio** | Documentacion CRISP-ML(Q), material academico, entregas del curso |

## Informacion Academica

| Campo | Valor |
|-------|-------|
| **Programa** | Maestria en Inteligencia Artificial Aplicada (MIAA) |
| **Universidad** | Universidad Icesi, Cali, Colombia |
| **Periodo** | 2026-1 |
| **Curso** | Proyecto I de Innovacion Tecnologica en IA |
| **Docente** | Milton Orlando Sarria Paja |
| **Estudiante** | Alexis Jaramillo Martinez |
| **Metodologia** | CRISP-ML(Q) |
| **Nivel IAG** | 5 — Exploracion con IAG |

## Estado

| Componente | Estado |
|-----------|--------|
| ai-service (Moni) | En produccion (Cloud Run) |
| RAG Pipeline | 16 documentos, pgvector HNSW |
| Lead Capture | Funcionando (regex + Gemini + n8n) |
| Agendamiento | Funcionando (n8n + Google Calendar + Meet) |
| Tests | 20 tests unitarios, 100% pass |
| Documentacion CRISP-ML(Q) | Completa (7 documentos) |
| Entregas del curso | En progreso |

---

*Maestria en IA Aplicada — Universidad Icesi, Cali, Colombia — Periodo 2026-1*
