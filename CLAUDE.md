# CLAUDE.md — Moni AI Research

## Identidad

- **Repositorio**: Documentacion CRISP-ML(Q) del proyecto Moni + material academico MIAA
- **Programa**: Maestria en IA Aplicada — Universidad Icesi
- **Curso activo**: Proyecto I de Innovacion Tecnologica en IA (Milton Sarria)
- **Proyecto**: Moni — asistente virtual legal para Carrillo ABGD SAS
- **Estado**: MVP en produccion, documentacion CRISP-ML(Q) completa

## Proposito

Este repositorio tiene dos funciones:

1. **Documentacion del proyecto Moni** siguiendo CRISP-ML(Q) — 7 documentos en `docs/`
2. **Material academico** de la MIAA — 5 cursos en `courses/`

El codigo productivo de Moni vive en `backend-services/ai-service`. Aqui NO se duplica codigo — solo documentacion, analisis y experimentacion.

## Estructura

```
docs/                          # CRISP-ML(Q) — documentacion principal
├── 01-business-understanding.md
├── 02-data-engineering.md
├── 03-model-engineering.md
├── 04-evaluation.md
├── 05-deployment.md
├── 06-monitoring.md
└── 07-ethics-compliance.md
notebooks/                     # Jupyter notebooks de analisis
reports/                       # Entregas del curso (etapa-1, etapa-2, reporte-final)
references/                    # Papers y articulos de referencia
courses/                       # Material academico MIAA (5 cursos)
```

## Cursos

| Curso | Periodo | Estado |
|-------|---------|--------|
| Analisis de Datos I | 2025-2 | Completado |
| Aprendizaje Automatico I | 2025-2 | Completado |
| Aprendizaje Automatico II | 2026-1 | En curso |
| Extraccion y Almacenamiento de Datos | 2026-1 | En curso |
| Proyecto I de Innovacion Tecnologica en IA | 2026-1 | En curso |

## Stack del proyecto Moni

- Python 3.12 + FastAPI + google-genai + pgvector + httpx
- LLM: Gemini 2.5 Flash | Embeddings: gemini-embedding-001 (768 dims)
- DB: PostgreSQL 16 + pgvector en Neon | Infra: GCP Cloud Run
- Metodologia: CRISP-ML(Q) | Compliance: Ley 1581/2012

## Reglas

- Espanol para documentacion, ingles para codigo
- Tono academico-profesional en docs/ y reports/
- No duplicar codigo productivo de backend-services
- Notebooks deben ser reproducibles (datos incluidos o URL documentada)
- Entregas del curso van en reports/ con el formato de la universidad
