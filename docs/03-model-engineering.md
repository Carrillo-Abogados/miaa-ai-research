# Fase 3: Ingenieria de Modelos

> CRISP-ML(Q) — Machine Learning Model Engineering

## 3.1 Arquitectura del Sistema

Moni es un sistema multi-componente que combina varias tecnicas de IA:

```
                    ┌──────────────────────────────────┐
                    │         Frontend (Next.js)        │
                    │         app.carrilloabgd.com      │
                    └───────────────┬───────────────────┘
                                    │ POST /api/chat/message
                    ┌───────────────▼───────────────────┐
                    │         ai-service (FastAPI)       │
                    │         Cloud Run (8901)           │
                    ├───────────────────────────────────┤
                    │  1. Session Management             │
                    │  2. RAG Pipeline                   │
                    │  3. LLM Generation (Gemini)        │
                    │  4. Lead Capture                   │
                    │  5. Appointment Scheduling          │
                    └──┬──────────┬───────────┬─────────┘
                       │          │           │
              ┌────────▼──┐ ┌────▼─────┐ ┌───▼──────────┐
              │  pgvector  │ │  Gemini  │ │  n8n Cloud   │
              │  (Neon)    │ │  2.5     │ │  (webhooks)  │
              │  RAG Store │ │  Flash   │ │  Calendar +  │
              │            │ │          │ │  Lead Score  │
              └────────────┘ └──────────┘ └──────────────┘
```

## 3.2 Componente 1: RAG (Retrieval-Augmented Generation)

### Seleccion del enfoque

| Enfoque evaluado | Ventajas | Desventajas | Decision |
|-----------------|----------|-------------|----------|
| Fine-tuning LLM | Respuestas mas naturales | Costo alto, datos insuficientes, no actualizable | Descartado |
| RAG con pgvector | Actualizable, grounded, bajo costo | Requiere corpus curado | **Seleccionado** |
| Prompt injection | Simple | No escala, limite de contexto | Descartado |
| LangChain | Framework completo | Overhead, abstraccion innecesaria | Descartado |

### Modelo de embeddings

- **Modelo**: `gemini-embedding-001` (Google)
- **Dimensionalidad**: 768 (truncado Matryoshka desde 3072)
- **Justificacion**: Balance entre calidad de representacion y eficiencia de almacenamiento/busqueda. 768 dims permite usar HNSW index de pgvector (limite: 2000 dims).

### Busqueda por similaridad

```python
# Cosine similarity con pgvector
SELECT title, content, category, metadata,
       1 - (embedding <=> query_vector) AS similarity
FROM assistant.knowledge_base
WHERE 1 - (embedding <=> query_vector) > 0.3  -- threshold
ORDER BY embedding <=> query_vector
LIMIT 3  -- top_k
```

| Parametro | Valor | Justificacion |
|-----------|-------|---------------|
| Similaridad | Coseno | Estandar para embeddings de texto, invariante a magnitud |
| Threshold | 0.3 | Empirico: filtra documentos irrelevantes sin perder recall |
| Top-K | 3 | Suficiente contexto sin exceder ventana del LLM |
| Indice | HNSW (m=16, ef_construction=64) | Balance recall/velocidad para corpus pequeno |

### Formato de contexto inyectado

```
[categoria] Titulo del documento
Contenido relevante del documento...
Articulo completo: https://url-del-blog (si existe)
---
[categoria] Siguiente documento...
```

## 3.3 Componente 2: Generacion con LLM

### Seleccion del modelo

| Modelo evaluado | Latencia | Costo | Calidad espanol | Decision |
|----------------|----------|-------|-----------------|----------|
| GPT-4o | ~3s | Alto | Buena | Descartado (costo) |
| Claude 3.5 Sonnet | ~2s | Medio | Excelente | Descartado (complejidad API) |
| Gemini 2.5 Flash | ~1.5s | Bajo | Buena | **Seleccionado** |
| Gemini 1.5 Flash | ~1s | Muy bajo | Aceptable | Descartado (calidad) |

**Justificacion**: Gemini 2.5 Flash ofrece el mejor balance costo/calidad/latencia para un chatbot conversacional en espanol. La integracion nativa con `gemini-embedding-001` simplifica el stack.

### System prompt

El system prompt (98 lineas) define:

1. **Personalidad**: Profesional pero cercana, tuteo colombiano, sin jerga legal
2. **Autoridad de contenido**: Datos verificados de la firma (direccion, equipo, fundacion)
3. **Patron de respuesta obligatorio**: Dato concreto -> Matiz -> Riesgo/Oportunidad -> Siguiente paso
4. **Reglas de seguridad**: No asesoria legal, no interpretacion de contratos, no articulos exactos
5. **Formato de salida**: JSON estructurado con message, suggested_actions, action

### Configuracion del modelo

| Parametro | Valor | Justificacion |
|-----------|-------|---------------|
| max_output_tokens | 2048 | 512 truncaba JSON con tildes en espanol |
| temperature | default | Balance entre coherencia y naturalidad |
| response_format | JSON | Parseo estructurado para acciones |

## 3.4 Componente 3: Deteccion de Datos de Contacto

### Enfoque: Regex + LLM hibrido

```
Mensaje del usuario
       │
       ├── Regex: detectar email + telefono colombiano
       │     EMAIL: r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
       │     PHONE: r'(?:\+57\s?)?(?:3\d{2}[\s.-]?\d{3}[\s.-]?\d{4}|\d{7,10})'
       │
       └── Gemini: extraer nombre + servicio_interes (en action.data)
              │
              ▼
       Merge de ambas fuentes
              │
              ▼
       Validacion: email_valid AND nombre_non_empty
              │
              ├── True: enviar lead webhook (BackgroundTasks)
              └── False: esperar mas datos en siguiente mensaje
```

### Decision: Por que no ML para lead scoring?

| Factor | Analisis |
|--------|----------|
| Volumen de datos | Insuficiente para entrenar modelo supervisado (< 100 leads) |
| Complejidad del problema | Binario (tiene datos o no), no requiere scoring graduado |
| Escalabilidad | El scoring complejo se delega a n8n Cloud (Gemini scoring) |
| Mantenimiento | Reglas heuristicas son transparentes y faciles de auditar |

El scoring ML se realiza downstream en el pipeline de n8n, donde Gemini evalua la calidad del lead con criterios de negocio mas complejos.

## 3.5 Componente 4: Agendamiento de Citas

Integracion sincrona con n8n Cloud para agendar citas via Google Calendar + Meet.

```
Moni detecta intencion "schedule"
       │
       ▼
POST webhook n8n (30s timeout)
       │
       ▼
n8n Spoke: Schedule Appointment v2
  ├── Crear evento Google Calendar
  ├── Agregar Google Meet link
  ├── Enviar email al cliente (confirmacion)
  └── Enviar email al abogado (notificacion)
       │
       ▼
Respuesta a Moni: { confirmed, date, meet_link, event_id }
       │
       ▼
Moni muestra confirmacion al usuario
```

## 3.6 Reproducibilidad

| Aspecto | Implementacion |
|---------|---------------|
| **Modelo LLM** | `gemini-2.5-flash` (versionado por Google) |
| **Embeddings** | `gemini-embedding-001` (768 dims, deterministico) |
| **Corpus** | JSON versionado en git (`corpus/servicios-carrillo.json`) |
| **System prompt** | Python module versionado en git |
| **Dependencias** | `requirements.txt` con versiones pinned |
| **Infraestructura** | Dockerfile multi-stage, reproducible |
| **Schema DB** | Migraciones SQL versionadas (`migrations/001_create_schema.sql`) |
