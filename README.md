# MIAA AI Research — Carrillo Abogados

Repositorio de investigación y desarrollo del **Asistente Virtual Legal** para Carrillo ABGD SAS, enmarcado en la **Maestría en IA Aplicada (MIAA)** de la Universidad Icesi.

## Estructura

```
miaa-ai-research/
├── analisis-datos/              # Curso: Análisis de Datos I
│   ├── talleres/                # Entregables académicos
│   ├── sesiones/                # Material por sesión (1-5)
│   ├── notebooks/               # Laboratorios y repo del curso
│   ├── apuntes/                 # Notas y diapositivas
│   └── datasets/                # Datos para análisis
├── aprendizaje-automatico/      # Curso: Aprendizaje Automático I
│   ├── talleres/                # Entregables (incluye taller final)
│   ├── unidades/                # Material por unidad (1-5)
│   ├── notebooks/               # Laboratorios
│   ├── apuntes/                 # Notas y diapositivas
│   ├── libros/                  # Libros de referencia
│   └── datasets/                # Datos para modelos
├── experiments/                 # Experimentos MLflow
├── prototypes/                  # Prototipos de componentes
└── docs/                        # Model cards, ADRs, documentación
```

## Proyecto: Asistente Virtual Legal

**Objetivo**: Reducir en 75% el tiempo de respuesta a consultas legales y automatizar el 70% de FAQs mediante un asistente basado en RAG (Retrieval-Augmented Generation).

**Componentes**:
- **RAG Pipeline**: LLM + Vector DB con jurisprudencia colombiana
- **Lead Scoring**: Clasificación predictiva de leads (score 0-100)
- **Intent Classification**: Ruteo automático por área legal

**Metodología**: CRISP-DM | **Compliance**: Ley 1581/2012

## Relación con la plataforma

El código productivo del `ai-service` vive en [`backend-services/`](https://github.com/Carrillo-Abogados/backend-services). Este repositorio es exclusivamente para investigación, experimentación académica y prototipos.

---

*Desarrollado con Claude Code (Opus 4.6) como parte del programa MIAA, Universidad Icesi.*
