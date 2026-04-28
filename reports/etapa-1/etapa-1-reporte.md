# Moni: Asistente Virtual Legal con Generación Aumentada por Recuperación (RAG) para una Firma de Abogados Colombiana

**Reporte de la Primera Etapa — Proyecto I de Innovación Tecnológica en Inteligencia Artificial**

| Campo | Valor |
|-------|-------|
| Programa | Maestría en Inteligencia Artificial Aplicada (MIAA) |
| Universidad | Universidad Icesi, Cali, Colombia |
| Periodo | 2026-1 |
| Curso | Proyecto I de Innovación Tecnológica en IA |
| Docente | Milton Orlando Sarria Paja |
| Estudiante | Alexis Jaramillo Martínez |
| Fecha | 27 de abril de 2026 |
| Repositorio | `Carrillo-Abogados/miaa-ai-research` |

---

## Resumen

El presente trabajo documenta la primera etapa del desarrollo de **Moni**, un asistente virtual inteligente diseñado para la firma legal colombiana **Carrillo ABGD SAS** (Cali, fundada en 1996), cuyo propósito es automatizar la primera respuesta a leads, responder consultas legales frecuentes y agendar citas en el calendario del equipo profesional. El sistema implementa una arquitectura de **Generación Aumentada por Recuperación (RAG)** sobre **Gemini 2.5 Flash** como modelo de lenguaje y **pgvector** sobre PostgreSQL serverless (Neon) como almacén vectorial, con **embeddings de 768 dimensiones** (`gemini-embedding-001`, truncado Matryoshka) e índice **HNSW**. Se sigue la metodología **CRISP-ML(Q)** (extensión de CRISP-DM con aseguramiento de calidad), articulada en seis fases. En esta primera etapa se presenta: (i) el análisis del problema con identificación de causas y efectos mediante árbol de problemas, definición de pregunta SMART y objetivos derivados; (ii) el estado del arte sobre RAG, modelos de lenguaje grandes en español y soluciones existentes en legal-tech (DoNotPay, Harvey AI, Casetext CoCounsel); y (iii) la planeación del proyecto con tareas y cronograma del semestre 2026-1. El MVP de Moni se encuentra desplegado en producción en GCP Cloud Run desde el 10 de abril de 2026, con un corpus inicial de 19 documentos curados, 25 pruebas unitarias, y un conjunto de evaluación (*ground truth*) de 39 preguntas estructuradas en 10 categorías que registra una tasa de acierto del 100% y tasa de alucinación del 0% post-ajuste, validando la viabilidad técnica del enfoque.

**Índice de Términos** — Generación Aumentada por Recuperación (RAG), Asistente Virtual Legal, Modelos de Lenguaje Grandes (LLM), CRISP-ML(Q).

---

## I. Introducción

La industria de servicios legales en Colombia enfrenta una presión creciente por reducir tiempos de respuesta a clientes potenciales (leads) sin sacrificar la calidad ni el rigor profesional. Las firmas de tamaño medio, como Carrillo ABGD SAS — una firma con más de **30 años de trayectoria** y más de **800 marcas registradas ante la Superintendencia de Industria y Comercio (SIC)** — deben atender consultas iniciales por múltiples canales (teléfono, correo electrónico, formulario web) que dependen de la disponibilidad del equipo de abogados, generando latencias de horas a días, pérdida de leads, y consumo de tiempo facturable en consultas repetitivas.

La aparición reciente de **modelos de lenguaje grandes (LLM)** comerciales con capacidades multilingües sólidas, combinada con técnicas de **Generación Aumentada por Recuperación (RAG)** que permiten anclar las respuestas en conocimiento curado y verificable, abre una oportunidad concreta para automatizar la primera interacción con leads de forma escalable, controlada y económicamente viable.

Este proyecto, denominado **Moni**, busca construir un asistente virtual inteligente especializado en el dominio legal de Carrillo Abogados, que (i) responda consultas frecuentes con información oficial verificada, (ii) capture datos de contacto de manera natural y estructurada, y (iii) agende citas directamente en el calendario del equipo. El proyecto se desarrolla en el marco del curso *Proyecto I de Innovación Tecnológica en Inteligencia Artificial* de la Maestría en Inteligencia Artificial Aplicada (MIAA) de la Universidad Icesi, aplicando la metodología CRISP-ML(Q) como guía estructurada del ciclo de vida del proyecto.

El presente documento corresponde a la **primera entrega** del curso (20% de la nota final) y cubre el análisis del problema, el estado del arte y la planeación del proyecto.

---

## II. Análisis del Problema

### II.A Descripción del Problema

Carrillo ABGD SAS recibe consultas iniciales de potenciales clientes a través de múltiples canales digitales y telefónicos. El proceso de primera respuesta es **manual, dependiente de la disponibilidad del equipo legal**, lento (de horas a días), e inconsistente entre canales. Como consecuencia, una proporción significativa de leads se pierde por demoras de respuesta, el equipo legal invierte tiempo facturable en consultas repetitivas de bajo valor, y la firma carece de capacidad de atención fuera del horario laboral, perdiendo oportunidades de negocio en momentos de alta intención del cliente.

### II.B Árbol de Problemas

El siguiente árbol consolida la cadena causal: causas raíz (parte inferior), problema central (parte media) y efectos (parte superior).

```
                        EFECTOS
   ┌──────────────────────┬──────────────────────┬──────────────────────┐
   │  Pérdida de leads    │  Saturación equipo   │  Pérdida de ingresos │
   │  por demora de       │  legal con consultas │  por leads no        │
   │  primera respuesta   │  repetitivas         │  convertidos         │
   └──────────────────────┴──────────────────────┴──────────────────────┘
              │                     │                     │
              └─────────────────────┴─────────────────────┘
                                    │
                                    ▼
   ┌────────────────────────────────────────────────────────────────────┐
   │  PROBLEMA CENTRAL                                                   │
   │  La firma Carrillo ABGD SAS no logra ofrecer una primera respuesta │
   │  oportuna, consistente y disponible 24/7 a leads que llegan por    │
   │  canales digitales, lo que reduce la conversión y consume tiempo   │
   │  facturable del equipo legal en consultas repetitivas.             │
   └────────────────────────────────────────────────────────────────────┘
              ▲                     ▲                     ▲
              │                     │                     │
   ┌──────────┴──────────┬──────────┴──────────┬─────────┴────────────┐
   │  Proceso manual     │  Sin atención fuera │  Información         │
   │  dependiente del    │  de horario laboral │  inconsistente entre │
   │  abogado disponible │  (8AM-6PM L-V)      │  canales y abogados  │
   └─────────────────────┴─────────────────────┴──────────────────────┘
                                    │
                        CAUSAS RAÍZ │
   ┌────────────────────────────────┴───────────────────────────────────┐
   │  Ausencia de un sistema automatizado que combine conocimiento      │
   │  curado de la firma con generación de lenguaje natural en español  │
   │  para responder consultas legales frecuentes y capturar leads.     │
   └────────────────────────────────────────────────────────────────────┘
```

### II.C Pregunta de Investigación (SMART)

> ¿Cómo puede un asistente virtual basado en IA generativa con arquitectura RAG, integrado a la plataforma web de Carrillo Abogados, **reducir en un 75% el tiempo promedio de primera respuesta a leads** y **automatizar el 70% de las consultas frecuentes** durante el primer semestre de 2026, manteniendo una tasa de alucinación inferior al 5% sobre datos canónicos de la firma?

| Criterio | Definición |
|----------|------------|
| **Specific** | Asistente virtual con RAG sobre corpus curado de la firma para primera respuesta a leads y captura de datos de contacto. |
| **Measurable** | Reducción ≥ 75% en tiempo de primera respuesta; ≥ 70% de consultas frecuentes resueltas sin escalación humana; alucinación < 5%. |
| **Achievable** | Combinación de LLM comercial (Gemini 2.5 Flash) + RAG sobre corpus verificado + reglas estrictas en *system prompt*; viable con stack de infraestructura existente. |
| **Relevant** | Impacta directamente conversión de leads, rentabilidad y experiencia de cliente; alineado con el plan de digitalización de la firma. |
| **Time-bound** | MVP desplegado en producción para abril de 2026; evaluación formal y entrega final del curso en mayo de 2026. |

### II.D Objetivo General

Diseñar, desarrollar, desplegar y evaluar un asistente virtual inteligente basado en una arquitectura de Generación Aumentada por Recuperación (RAG), capaz de automatizar la primera respuesta a consultas legales frecuentes y la captura de leads en la firma Carrillo ABGD SAS, garantizando trazabilidad, calidad de respuesta y cumplimiento normativo bajo la Ley 1581 de 2012 de protección de datos personales en Colombia.

### II.E Objetivos Específicos

1. **Construir un corpus curado** de conocimiento de la firma (información institucional, servicios, equipo, áreas de práctica y contenido educativo del blog), estructurado en formato JSON y vectorizado mediante embeddings semánticos.
2. **Implementar la canalización RAG** integrando un almacén vectorial PostgreSQL/pgvector con índice HNSW y un modelo de lenguaje grande (Gemini 2.5 Flash) accedido vía API directa, con un *system prompt* que defina personalidad, datos canónicos no negociables y reglas de seguridad anti-alucinación.
3. **Implementar la captura de leads** mediante un enfoque híbrido (regex + LLM) que detecte datos de contacto (correo electrónico, teléfono colombiano) y los publique a un pipeline de automatización (n8n) para enriquecimiento y *scoring*.
4. **Implementar el agendamiento de citas** integrando webhooks asincrónicos hacia n8n para crear eventos en Google Calendar con enlaces de Google Meet y notificación a las partes.
5. **Construir un conjunto de evaluación (*ground truth*)** de al menos 35 preguntas representativas en categorías clave (datos de la firma, equipo, áreas de práctica, propiedad intelectual, derecho corporativo, *compliance* y casos adversariales) y un *script* de evaluación reproducible que mida tasa de acierto factual, tasa de alucinación, tasa de rechazo correcto en casos fuera de alcance, y latencia.
6. **Desplegar el sistema en producción** sobre GCP Cloud Run, con almacenamiento en Neon serverless PostgreSQL, escalado automático, monitoreo de salud y publicación detrás de un *gateway* API público.
7. **Documentar el desarrollo bajo CRISP-ML(Q)** en seis fases (Comprensión del Negocio y los Datos, Ingeniería de Datos, Ingeniería de Modelos, Evaluación, Despliegue, Monitoreo) con énfasis en aseguramiento de calidad, reproducibilidad y consideraciones éticas conforme a la Ley 1581/2012.

### II.F Indicadores Clave de Desempeño (KPI)

| Categoría | KPI | Meta |
|-----------|-----|------|
| Negocio | Tiempo promedio de primera respuesta | < 5 segundos (vs. horas o días) |
| Negocio | Cobertura de consultas frecuentes automatizadas | ≥ 70% |
| Negocio | Captura automatizada de leads (precisión) | ≥ 95% |
| Calidad ML | Tasa de acierto factual sobre *ground truth* | ≥ 85% |
| Calidad ML | Tasa de alucinación sobre datos canónicos | < 5% |
| Calidad ML | Tasa de rechazo correcto en casos fuera de alcance | ≥ 90% |
| Calidad ML | Latencia *end-to-end* p95 | < 8 segundos |
| Operacional | Costo operativo mensual (LLM + infraestructura) | < USD 50 |
| Operacional | Disponibilidad del servicio | ≥ 99% |

---

## III. Estado del Arte

### III.A Generación Aumentada por Recuperación (RAG)

La arquitectura RAG fue formalizada por Lewis et al. en 2020 [1] como mecanismo para combinar la capacidad generativa de los modelos de lenguaje pre-entrenados con la precisión y actualizabilidad de bases de conocimiento externas. La idea central consiste en, para cada consulta del usuario, recuperar fragmentos relevantes de un corpus indexado mediante embeddings semánticos, e inyectarlos como contexto al modelo de lenguaje generativo, reduciendo de manera sustancial las alucinaciones y permitiendo respuestas ancladas en información verificable. Karpukhin et al. [2] demostraron que la **recuperación densa** mediante embeddings de paso superaba a métodos clásicos basados en BM25 en tareas de respuesta a preguntas de dominio abierto, motivando la adopción generalizada de almacenes vectoriales.

En la práctica industrial, RAG se ha consolidado como el patrón dominante para construir asistentes especializados en dominios cerrados (legal, médico, financiero) por tres razones: (i) evita el costo y la complejidad del *fine-tuning* sobre datos sensibles, (ii) permite actualizar el conocimiento simplemente re-indexando el corpus, y (iii) ofrece trazabilidad de las fuentes que sustentan cada respuesta. Anthropic [7] ha documentado patrones de diseño efectivos para sistemas agénticos que combinan RAG con orquestación de herramientas, mientras que Google publica guías oficiales de implementación de RAG sobre la API Gemini [8].

### III.B Modelos de Lenguaje Grandes (LLM) en Español

Los modelos de lenguaje grandes han evolucionado rápidamente desde GPT-3 [3], que demostró capacidades emergentes de aprendizaje en contexto sin necesidad de *fine-tuning*. La familia Llama de Meta [4] aceleró la adopción de modelos abiertos competitivos. Para aplicaciones en español, los modelos comerciales más recientes (GPT-4o de OpenAI, Claude 3.5 Sonnet de Anthropic, Gemini 2.5 Flash de Google) ofrecen calidad de generación nativa en español sin necesidad de modelos específicamente pre-entrenados en este idioma. La selección entre estos depende de criterios de costo, latencia, calidad de instrucciones y disponibilidad regional.

### III.C Almacenes Vectoriales y pgvector

Los almacenes vectoriales son componentes críticos de cualquier sistema RAG. Las opciones disponibles incluyen soluciones especializadas (Pinecone [9], Weaviate, Qdrant, Milvus) y extensiones sobre bases de datos relacionales. Para proyectos donde ya existe un *stack* PostgreSQL, la extensión **pgvector** ofrece un balance favorable: soporta índices HNSW e IVFFlat para búsqueda aproximada de vecinos más cercanos, se integra de forma transparente con el resto del modelo de datos relacional, y elimina la necesidad de operar un servicio adicional, simplificando el costo operativo y la línea base de seguridad.

### III.D Soluciones Existentes en Legal-Tech con IA

| Solución | Origen | Enfoque | Modelo | Audiencia | Limitación para el caso colombiano |
|----------|--------|---------|--------|-----------|-------------------------------------|
| **DoNotPay** | EE.UU., 2015 | Asistente legal de consumo (multas, suscripciones, devoluciones) | Modelos propietarios | Consumidor final | Sin adaptación al marco normativo colombiano; en inglés. |
| **Harvey AI** | EE.UU./Reino Unido, 2022 | Asistente especializado para grandes firmas (alianza con Allen & Overy, PwC) | GPT-4 + *fine-tuning* legal | Abogados de grandes firmas | Costo prohibitivo para firma media colombiana; sin corpus colombiano. |
| **Casetext CoCounsel** | EE.UU., adquirido por Thomson Reuters 2023 | Búsqueda de jurisprudencia y redacción de memoriales | GPT-4 + base jurisprudencial EE.UU. | Investigación jurídica EE.UU. | Cobertura solo derecho anglosajón. |
| **Lexis+ AI** | EE.UU./Internacional, 2023 | Búsqueda inteligente sobre LexisNexis | GPT-4 + base LexisNexis | Investigación jurídica | Sin cobertura específica Colombia; modelo de licencia corporativo. |

Como se observa, las soluciones existentes están orientadas a (i) consumidores en mercados anglosajones, o (ii) firmas de gran tamaño con presupuestos significativos para investigación jurídica en derecho anglosajón. **Ninguna ofrece una respuesta adaptada a una firma legal colombiana de tamaño medio que requiera respuestas en español, ancladas a información oficial verificada de la firma, con cumplimiento normativo bajo la Ley 1581 de 2012 [10] y costo operativo viable**. Esto justifica el desarrollo de Moni como solución específica.

### III.E Metodologías para Proyectos de Aprendizaje Automático

CRISP-DM [6] (Cross-Industry Standard Process for Data Mining) ha sido durante dos décadas el modelo de referencia para proyectos de minería de datos. Studer et al. [5] propusieron en 2020 una extensión denominada **CRISP-ML(Q)** que adapta CRISP-DM a las particularidades del aprendizaje automático moderno, incorporando explícitamente el aseguramiento de calidad (Quality Assurance) en cada fase y reconociendo el carácter iterativo y exploratorio del desarrollo de modelos. CRISP-ML(Q) define seis fases: (1) Comprensión del Negocio y los Datos, (2) Ingeniería de Datos, (3) Ingeniería de Modelos de ML, (4) Evaluación, (5) Despliegue, y (6) Monitoreo y Mantenimiento. Susskind [11] argumenta que la transformación digital del sector legal es ineludible y exige enfoques estructurados que combinen rigor metodológico con sensibilidad ética y normativa.

---

## IV. Planeación del Proyecto

### IV.A Metodología

El proyecto adopta **CRISP-ML(Q)** [5] como marco metodológico principal, en tanto extiende **CRISP-DM** [6] (mencionado en la Unidad 1 del curso) con prácticas explícitas de aseguramiento de calidad y reconoce la naturaleza iterativa del aprendizaje automático. La primera entrega corresponde principalmente a la **Fase 1 — Comprensión del Negocio y los Datos**, cuyas tareas son:

1. Definir objetivos de negocio y traducirlos a objetivos de Machine Learning.
2. Recolectar y verificar datos disponibles.
3. Evaluar la viabilidad técnica, económica y normativa del proyecto.
4. Construir una prueba de concepto (POC) que valide los supuestos críticos.

Las cinco fases restantes se desarrollan en las entregas dos y tres del curso, y están documentadas en el repositorio bajo `docs/02-data-engineering.md`, `docs/03-model-engineering.md`, `docs/04-evaluation.md`, `docs/05-deployment.md`, `docs/06-monitoring.md` y `docs/07-ethics-compliance.md`.

### IV.B Estructura de Tareas

| ID | Tarea | Fase CRISP-ML(Q) | Estado |
|----|-------|-------------------|--------|
| T1 | Comprensión del problema y definición de pregunta SMART | 1 | Completada |
| T2 | Identificación y verificación del corpus de conocimiento de la firma | 1 | Completada |
| T3 | Diseño de arquitectura RAG y selección de stack tecnológico | 1, 3 | Completada |
| T4 | Definición de criterios de éxito y KPIs | 1 | Completada |
| T5 | Estado del arte y revisión bibliográfica | 1 | Completada |
| T6 | Estructuración del corpus en JSON y generación de embeddings | 2 | Completada |
| T7 | Implementación del *system prompt* y reglas anti-alucinación | 3 | Completada |
| T8 | Implementación de captura de leads (regex + LLM) | 3 | Completada |
| T9 | Implementación de agendamiento de citas vía n8n + Google Calendar | 3 | Completada |
| T10 | Despliegue en GCP Cloud Run con CI/CD | 5 | Completada |
| T11 | Construcción del *ground truth set* (39 preguntas, 10 categorías) | 4 | Completada |
| T12 | Ejecución de baseline y reporte de métricas | 4 | Completada |
| T13 | Ajuste del *system prompt* y enriquecimiento del corpus | 3 | Completada |
| T14 | Re-evaluación post-ajuste y documentación de hallazgos | 4 | Completada |
| T15 | Análisis exploratorio formal del corpus y registro de chats | 2 | En progreso (Etapa 2) |
| T16 | Comparación de modelos alternativos (GPT-4o, Claude, Gemini) | 3 | En progreso (Etapa 2) |
| T17 | Reporte final consolidado y video de presentación | — | Programada (Etapa 3) |

### IV.C Cronograma del Semestre 2026-1

| Mes | Actividad principal | Fase CRISP-ML(Q) | Entregable |
|-----|---------------------|-------------------|------------|
| Enero 2026 | Conformación del proyecto, levantamiento de contexto del cliente | 1 | Documento de contexto |
| Febrero 2026 | Definición del problema, pregunta SMART, KPIs, viabilidad | 1 | `docs/01-business-understanding.md` |
| Marzo 2026 | Diseño de arquitectura, construcción del corpus, generación de embeddings | 2, 3 | `docs/02-data-engineering.md`, `docs/03-model-engineering.md` |
| Abril 2026 (1ª quincena) | Despliegue MVP en GCP Cloud Run, integración n8n | 5 | MVP en producción (10-Abr) |
| Abril 2026 (2ª quincena) | Construcción de *ground truth*, evaluación baseline, ajustes | 4 | `docs/04-evaluation.md`, baselines |
| **27 Abril 2026** | **Primera entrega del curso (20%) — Reporte primera etapa** | — | **Este documento** |
| Mayo 2026 (1ª quincena) | Análisis exploratorio formal, pruebas iniciales con modelos alternativos | 2, 3 | Reporte segunda etapa |
| Mayo 2026 (2ª quincena) | Segunda entrega (10%), documentación monitoreo y ética | 4, 6 | `docs/06-monitoring.md`, `docs/07-ethics-compliance.md` |
| Mayo–Junio 2026 | Reporte final, presentación en video, sustentación | — | Tercera entrega (70%) |

### IV.D Recursos

| Recurso | Tipo | Justificación |
|---------|------|---------------|
| Google Gemini 2.5 Flash (API) | LLM | Calidad en español, latencia y costo balanceados. |
| `gemini-embedding-001` (768 dims) | Embeddings | Compatibilidad con índice HNSW de pgvector (límite 2000 dims). |
| PostgreSQL 16 + pgvector (Neon serverless) | Almacén vectorial | Integración con stack relacional existente; tier gratuito viable. |
| GCP Cloud Run | Cómputo | Escalado automático, modelo serverless, costo por uso. |
| FastAPI 0.115 + Python 3.12 | Framework | Asincronía nativa para latencia óptima. |
| n8n Cloud | Automatización | Integración con Google Calendar, *lead scoring* downstream. |
| GitHub + GitHub Actions | CI/CD | Versionado y despliegue automatizado. |

### IV.E Estado Actual del Proyecto

A la fecha de esta primera entrega, el sistema Moni se encuentra **desplegado en producción** sobre GCP Cloud Run (revisión `ai-service-00018-ddv`), accesible a través del *gateway* público de la plataforma de Carrillo Abogados. El estado de los componentes principales es:

| Componente | Estado |
|-----------|--------|
| Servicio `ai-service` (FastAPI) | En producción, 25 pruebas unitarias, 100% éxito |
| Corpus en `assistant.knowledge_base` | 19 documentos, 5 categorías, embeddings vigentes |
| *Ground truth* | 39 preguntas en 10 categorías (`tests/ground_truth_set.json`) |
| Captura de leads | Funcional (regex + Gemini + webhook n8n) |
| Agendamiento | Funcional (n8n + Google Calendar + Meet) |
| Documentación CRISP-ML(Q) | 7 documentos completados (`docs/01` a `docs/07`) |

### IV.F Métricas Empíricas Iniciales (Validación de Viabilidad)

La ejecución del *script* de evaluación `scripts/evaluate_rag.py` sobre el *ground truth* arrojó los siguientes resultados, que confirman la viabilidad del enfoque y serán profundizados en las etapas dos y tres:

| Métrica | Baseline pre-ajuste | Post-ajuste (objetivo handoff) |
|---------|----------------------|-------------------------------|
| Tasa de acierto factual | 87.2% (34/39) | **100.0% (39/39)** |
| Tasa de alucinación | 2.6% | **0.0%** |
| Tasa de rechazo correcto (casos fuera de alcance) | 100% | 100% |
| Latencia p50 | 1.52 s | 4.43 s |
| Latencia p95 | 2.03 s | 8.03 s |

El incremento de latencia post-ajuste se debe al crecimiento del *system prompt* tras incorporar un bloque de "Datos Canónicos No Negociables" que garantiza precisión sobre información institucional verificable (año de fundación, dirección, equipo, datos de contacto). El compromiso latencia/precisión se considera aceptable para una interacción conversacional asincrónica.

### IV.G Riesgos Identificados y Mitigación

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| Alucinación sobre datos institucionales sensibles | Media | Alto | Bloque de Datos Canónicos en *system prompt*; reglas anti-alucinación; *ground truth* como contrato. |
| Ofrecer asesoría legal específica (riesgo profesional) | Media | Alto | Reglas explícitas en *system prompt* que prohíben asesoría sobre casos individuales; categoría `limites_legal` en *ground truth*. |
| Incumplimiento Ley 1581/2012 (datos personales) | Baja | Alto | Disclaimer obligatorio antes de capturar datos; consentimiento explícito; documento `docs/07-ethics-compliance.md`. |
| Costo de API LLM superior al presupuesto | Baja | Medio | Selección de Gemini 2.5 Flash (modelo de bajo costo); rate limit `20/minute` por IP. |
| Indisponibilidad del LLM (Gemini API) | Baja | Medio | Mensaje de fallback al usuario; monitoreo de salud; plan de migración a Claude Haiku como contingencia. |

---

## V. Conclusiones Preliminares

La primera etapa del proyecto Moni concluye con los siguientes aportes:

1. **Identificación rigurosa del problema**: el árbol de problemas evidencia que la latencia y la inconsistencia en la primera respuesta a leads es un problema sistémico de la firma cuyas causas raíz son la dependencia de personal y la ausencia de un sistema automatizado, no la falta de talento del equipo legal.
2. **Pregunta SMART y objetivos derivados**: la pregunta de investigación cumple los cinco criterios SMART y se descompone en siete objetivos específicos accionables que guían la ejecución del proyecto.
3. **Estado del arte sólido**: la revisión confirma que ninguna solución existente cubre el nicho de "firma legal colombiana de tamaño medio con corpus propio y respuestas en español" — lo que valida la pertinencia del proyecto.
4. **Planeación realista**: el cronograma del semestre 2026-1 está alineado con el ciclo de entregas del curso y con la disponibilidad de la firma; las fases de CRISP-ML(Q) están repartidas de manera coherente entre las tres entregas.
5. **Validación temprana de viabilidad**: el MVP ya en producción y los resultados empíricos iniciales (100% tasa de acierto factual, 0% alucinación post-ajuste sobre 39 preguntas) confirman que el enfoque RAG con Gemini 2.5 Flash y pgvector es técnica y económicamente viable para el caso de uso planteado.

Las etapas siguientes profundizarán en (i) el análisis exploratorio formal del corpus y de los registros de conversaciones, (ii) la comparación cuantitativa entre modelos alternativos (GPT-4o, Claude 3.5 Sonnet, Gemini 2.5 Flash), y (iii) la documentación final de impacto, ética y monitoreo continuo.

---

## VI. Uso de Inteligencia Artificial Generativa (IAG)

Conforme al acuerdo institucional sobre el uso de IAG en el curso, el desarrollo de este proyecto se enmarca en el **Nivel 3 — Colaboración con IAG**. Se ha utilizado un asistente generativo (Claude Code, Anthropic) como apoyo para la redacción, estructuración y verificación de coherencia documental, así como para tareas de revisión cruzada de información entre los repositorios del proyecto. Los contenidos sustantivos —análisis del problema, definición de objetivos, métricas, decisiones de arquitectura, ejecución de evaluaciones empíricas y resultados reportados— son de autoría propia y han sido verificados contra el código fuente y la documentación oficial del proyecto.

---

## Reconocimiento

El autor agradece a la firma **Carrillo ABGD SAS** por la apertura para desarrollar este proyecto sobre un caso real, y al docente **Milton Orlando Sarria Paja** por la orientación metodológica del curso *Proyecto I de Innovación Tecnológica en Inteligencia Artificial*.

---

## REFERENCIAS

[1] P. Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," in *Advances in Neural Information Processing Systems (NeurIPS)*, vol. 33, 2020, pp. 9459–9474.

[2] V. Karpukhin et al., "Dense Passage Retrieval for Open-Domain Question Answering," in *Proc. 2020 Conf. on Empirical Methods in Natural Language Processing (EMNLP)*, 2020, pp. 6769–6781.

[3] T. B. Brown et al., "Language Models are Few-Shot Learners," in *Advances in Neural Information Processing Systems (NeurIPS)*, vol. 33, 2020, pp. 1877–1901.

[4] H. Touvron et al., "Llama 2: Open Foundation and Fine-Tuned Chat Models," *arXiv preprint arXiv:2307.09288*, 2023.

[5] S. Studer, T. B. Bui, C. Drescher, A. Hanuschkin, L. Winkler, S. Peters, and K.-R. Müller, "Towards CRISP-ML(Q): A Machine Learning Process Model with Quality Assurance Methodology," *arXiv preprint arXiv:2003.05155*, 2020.

[6] P. Chapman et al., *CRISP-DM 1.0: Step-by-Step Data Mining Guide*. SPSS Inc., 2000.

[7] Anthropic, "Building Effective Agents," *Anthropic Engineering Blog*, Dec. 2024. [En línea]. Disponible: https://www.anthropic.com/engineering/building-effective-agents

[8] Google, "Gemini API Documentation — Embeddings and Retrieval-Augmented Generation," *Google AI for Developers*, 2026. [En línea]. Disponible: https://ai.google.dev/gemini-api/docs

[9] Pinecone, "What is a Vector Database?," *Pinecone Learn*, 2024. [En línea]. Disponible: https://www.pinecone.io/learn/vector-database/

[10] Congreso de la República de Colombia, "Ley Estatutaria 1581 de 2012 — Por la cual se dictan disposiciones generales para la protección de datos personales," *Diario Oficial No. 48587*, oct. 2012.

[11] R. Susskind, *Tomorrow's Lawyers: An Introduction to Your Future*, 2nd ed. Oxford, U.K.: Oxford University Press, 2017.

[12] D. Cielen, A. Meysman, and M. Ali, *Introducing Data Science: Big Data, Machine Learning and More, Using Python Tools*. Shelter Island, NY, USA: Manning Publications, 2016.

[13] G. Harrison, *Next Generation Databases: NoSQL and Big Data*. Berkeley, CA, USA: Apress, 2015.

[14] B. D. Lund, "The Art of (Data) Storytelling," *International Journal of Information, Diversity, & Inclusion*, vol. 6, no. 1/2, pp. 31–41, 2022.
