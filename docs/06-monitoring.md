# Fase 6: Monitoreo y Mantenimiento

> CRISP-ML(Q) — Monitoring and Maintenance

## 6.1 Estrategia de Monitoreo

El monitoreo de Moni cubre tres dimensiones: **infraestructura**, **aplicacion**, y **modelo**.

## 6.2 Monitoreo de Infraestructura

### Cloud Run Metrics (GCP Console)

| Metrica | Alerta | Accion |
|---------|--------|--------|
| Request latency P95 | > 5s | Investigar saturacion de recursos |
| Error rate (5xx) | > 5% | Revisar logs, verificar Neon connectivity |
| Instance count | > max (5) | Evaluar scaling o rate limit |
| Memory usage | > 80% (410Mi) | Optimizar o aumentar memoria |
| Cold start duration | > 10s | Verificar min_instances=1 |

### Neon PostgreSQL

| Metrica | Riesgo | Mitigacion |
|---------|--------|-----------|
| Scale-to-zero wakeup | Latencia en primer request | HikariCP init-fail-timeout: 60s |
| Connection pool exhaustion | Requests rechazados | asyncpg pool: min=2, max=10 |
| Storage growth | Costo incremental | Monitorear tamano de chat_sessions |

## 6.3 Monitoreo de Aplicacion

### Logging estructurado

Cada interaccion genera logs con:
- `session_id`: Trazabilidad de la conversacion
- `page_origin`: Pagina de origen del usuario
- `lead_captured`: Si se capturo un lead en la sesion
- `consent_given`: Estado del consentimiento
- `action_type`: schedule, capture_lead, o null

### Metricas de negocio

| Metrica | Fuente | Frecuencia |
|---------|--------|-----------|
| Sesiones de chat | assistant.chat_sessions COUNT | Diario |
| Leads capturados | lead_captured = TRUE COUNT | Diario |
| Tasa de conversion | leads / sesiones | Semanal |
| Citas agendadas | assistant.appointments COUNT | Semanal |
| Mensajes por sesion | AVG(jsonb_array_length(messages)) | Semanal |

## 6.4 Monitoreo del Modelo

### Model Staleness

| Riesgo | Indicador | Accion |
|--------|-----------|--------|
| Corpus desactualizado | Cambios en sitio web no reflejados | Re-ingest corpus |
| Modelo LLM deprecado | Google depreca version de Gemini | Migrar a nueva version |
| Embeddings drift | Nuevo modelo de embeddings disponible | Re-generar embeddings |
| Prompts inefectivos | Tasa de conversion baja | Refinar system prompt |

### Continued Model Evaluation

```
[Produccion]
    │
    ├── Moni responde a usuarios reales
    │
    ├── Logging de todas las interacciones
    │     ├── Queries del usuario
    │     ├── Documentos RAG recuperados (con similarity scores)
    │     ├── Respuestas generadas
    │     └── Acciones tomadas (lead, cita)
    │
    └── Revision periodica
          ├── Queries sin documentos relevantes (similarity < 0.3)
          │     → Agregar documentos al corpus
          ├── Respuestas incorrectas reportadas
          │     → Refinar system prompt
          └── Nuevos servicios de la firma
                → Actualizar corpus + re-ingest
```

## 6.5 Plan de Actualizacion

### Actualizacion del corpus

1. Identificar cambios en la informacion de la firma
2. Actualizar `corpus/servicios-carrillo.json`
3. `TRUNCATE assistant.knowledge_base`
4. Ejecutar `python scripts/ingest_corpus.py`
5. Verificar que las queries principales retornan documentos relevantes

### Actualizacion del modelo LLM

1. Evaluar nueva version del modelo (latencia, calidad, costo)
2. Actualizar referencia en codigo (`gemini-2.5-flash` → nueva version)
3. Ejecutar suite de tests
4. Deploy con verificacion post-deploy

### Actualizacion del system prompt

1. Identificar patron de respuesta suboptimo
2. Modificar `app/prompts/system_prompt.py`
3. Ejecutar tests adversariales
4. Deploy con monitoreo intensivo primeras 24h

## 6.6 Contingencia

### Fallback plan

| Escenario | Accion |
|-----------|--------|
| Gemini API down | Retornar mensaje "servicio temporalmente no disponible" + datos de contacto |
| Neon down | Health check falla, Cloud Run no enruta trafico |
| Rate limit excedido | 429 con retry-after header |
| Webhook n8n timeout | Moni responde sin confirmacion de cita, sugiere contacto directo |

### Rollback

Cloud Run mantiene revisiones anteriores. Rollback inmediato:

```bash
gcloud run services update-traffic ai-service --to-revisions=REVISION_ID=100
```
