> **ARCHIVED**: Este repositorio contiene material academico completado de la Maestria en IA Aplicada (MIAA), Universidad Icesi, periodo 2026-1. No recibe actualizaciones activas. Sirve como referencia historica para la fundamentacion AI del proyecto Carrillo Abogados.

---

# MIAA AI Research — Carrillo Abogados

Repositorio academico de la **Maestria en IA Aplicada (MIAA)** de la Universidad Icesi, periodo 2026-1. Contiene el material completo de dos cursos que fundamentan el desarrollo del **Asistente Virtual Legal** para Carrillo ABGD SAS.

## Estructura

```
miaa-ai-research/
├── Análisis de Datos I/           # Curso: Fundamentos de Análisis de Datos I (EDA)
│   ├── Sesión 1/                  # CRISP-DM, Pregunta SMART, EDA vino
│   ├── Sesión 2/                  # Tipos de analítica, Taller 1 (Formulación)
│   ├── Sesión 3/                  # Ingeniería de datos, Taller 2 (Arquitectura)
│   ├── Sesión 4/                  # EDA, outliers, imputación, visualización
│   ├── Sesión 5/                  # Pipelines, feature engineering, Taller 3
│   ├── Apuntes del Curso/         # PDFs de notas por sesión
│   ├── eda_couse-main/            # Repo del profesor (notebooks + datasets)
│   └── Plantilla ExpoFinal...     # Template sustentación final
├── Aprendizaje Automatico I/      # Curso: Aprendizaje Automático I
│   ├── Unidad 1/                  # Regresión lineal (OLS, gradient descent)
│   ├── Unidad 2/                  # Regresión logística, regularización
│   ├── Unidad 3/                  # Multicolinealidad, sesgo-varianza
│   ├── Unidad 4/                  # Métricas (F1, ROC, AUC), cross-validation
│   ├── Unidad 5/                  # K-Nearest Neighbors (KNN)
│   ├── Taller Final/              # Predicción rendimiento caña de azúcar
│   ├── Diapositivas del Curso/    # Slides del profesor
│   ├── Libros del curso/          # ISLP, Andrew NG, etc.
│   └── PracFin_N_*.ipynb          # Prácticas finales resueltas
├── CLAUDE.md                      # Instrucciones para Claude Code
└── README.md                      # Este archivo
```

## Cursos (Ambos finalizados — Periodo 2026-1)

### Análisis de Datos I
- **Profesor**: Jose Armando Ordoñez Cordoba
- **Enfoque**: CRISP-DM, EDA, ingeniería de datos, pipelines, feature engineering
- **Proyecto final**: Asistente Virtual Legal (RAG) para Carrillo Abogados
- **Evaluación**: Talleres 40% + Proyecto final 60%
- **IA**: Nivel 4 — IAG Completa permitida
- **Estado**: FINALIZADO (sustentación 14 Mar 2026)

### Aprendizaje Automático I
- **Profesor**: Milton Sarria
- **Enfoque**: Regresión lineal/logística, regularización (L1/L2), métricas, KNN
- **Taller final**: Predicción rendimiento caña de azúcar (Ingenio Providencia)
- **Examen final**: Clasificación riesgo cardiovascular
- **Estado**: FINALIZADO (examen 13 Mar 2026)

## Proyecto Académico: Asistente Virtual Legal

**Pregunta SMART**: ¿Cómo puede un asistente virtual basado en IA generativa (RAG) integrado al CRM de Carrillo Abogados, reducir en un 75% el tiempo de primera respuesta a leads y automatizar el 70% de las consultas frecuentes, liberando 400 horas facturables anuales?

| Componente | Tipo Analytics | Estado |
|---|---|---|
| Lead Scoring | Predictivo (heurístico → ML) | Implementado en n8n Cloud |
| Intent Classification | NLP multiclase | Diseño |
| RAG Pipeline | IA Generativa (LLM + Vector DB) | Diseño |
| Data Pipeline | Arquitectura Medallion | Implementado en PostgreSQL |

**Metodología**: CRISP-DM | **Compliance**: Ley 1581/2012

## Relación con la plataforma

El código productivo vive en [`backend-services/`](https://github.com/Carrillo-Abogados/backend-services). Este repositorio es exclusivamente material académico y de investigación. La documentación oficial está en [`documentation/`](https://github.com/Carrillo-Abogados/documentation).

---

*Maestría en IA Aplicada — Universidad Icesi, Cali, Colombia — Periodo 2026-1*
