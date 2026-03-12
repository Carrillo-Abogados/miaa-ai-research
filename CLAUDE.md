# MIAA AI Research — Instrucciones de Proyecto

## Identidad
- **Repositorio**: Investigación y desarrollo del Asistente Virtual Legal
- **Empresa**: Carrillo ABGD SAS (Cali, Colombia)
- **Programa Académico**: Maestría en IA Aplicada (MIAA), Universidad Icesi, 2026-1
- **Desarrollador Principal**: Claude Code (Opus 4.6)

## Propósito

Este repositorio contiene:
1. **Material académico** de dos cursos MIAA que fundamentan el desarrollo del Asistente Virtual Legal
2. **Experimentos y prototipos** de modelos de ML/NLP para el asistente
3. **Documentación de investigación** que respalda decisiones técnicas

**El código productivo del ai-service vive en `backend-services/`** — este repo es para investigación, experimentación y desarrollo académico.

## Cursos

### Análisis de Datos I
- **Enfoque**: CRISP-DM, EDA, ingeniería de datos, pipelines, feature engineering
- **Proyecto**: Asistente Virtual Legal (RAG) — formulación, arquitectura de datos, preparación
- **Entregables**: Taller 1 (formulación), Taller 2 (arquitectura datos), Taller 3 (preparación datos)
- **Ubicación**: `analisis-datos/`

### Aprendizaje Automático I
- **Enfoque**: Regresión lineal/logística, regularización, métricas, KNN
- **Proyecto**: Taller final (predicción rendimiento caña de azúcar)
- **Libro guía**: ISLP (Introduction to Statistical Learning with Python)
- **Ubicación**: `aprendizaje-automatico/`

## Componentes del Asistente Virtual Legal

| Componente | Tipo Analytics | Técnica | Estado |
|------------|---------------|---------|--------|
| Chatbot Legal | Generativo | RAG (LLM + Vector DB) | Diseño |
| Lead Scoring | Predictivo | LogReg / RF / XGBoost | Prototipo |
| Intent Classification | Descriptivo | TF-IDF + clasificador | Diseño |
| Data Pipeline | Infraestructura | Medallion (Bronze/Silver/Gold) | Diseño |

## Stack Técnico

- **Lenguaje**: Python 3.12
- **ML/Data**: scikit-learn, pandas, numpy, matplotlib, seaborn
- **NLP/RAG**: LangChain, sentence-transformers, ChromaDB
- **LLM**: Claude API (Anthropic)
- **API**: FastAPI (prototipo) → integración en backend-services (producción)
- **Cloud**: GCP Vertex AI (training), Cloud Run (serving)
- **Experiment Tracking**: MLflow

## Metodología

- **CRISP-DM**: 6 fases (Business Understanding → Deployment)
- **Reproducibilidad**: Seeds fijos, datasets versionados, notebooks exportados
- **Compliance**: Ley 1581/2012 (Protección de Datos Personales, Colombia)
- **Ética**: Auditoría de bias, métricas de fairness, explicabilidad (SHAP/LIME)

## Relación con otros repos

| Repo | Relación |
|------|----------|
| `backend-services/` | ai-service productivo (Java wrapper o FastAPI en Cloud Run) |
| `frontend/` | UI del chatbot y dashboard de analytics |
| `documentation/` | Documentación oficial del ai-service |
| `platform/` | Infraestructura (Terraform, Helm) para ai-service |

## Comunicación
- **Idioma**: Español para toda comunicación y documentación
- **Tono**: Técnico-académico, riguroso, con referencias estadísticas
