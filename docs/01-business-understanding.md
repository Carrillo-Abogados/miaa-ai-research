# Fase 1: Comprension del Negocio y los Datos

> CRISP-ML(Q) — Business and Data Understanding

## 1.1 Contexto del Negocio

**Organizacion**: Carrillo ABGD SAS — firma de abogados fundada en 1996 en Cali, Colombia, especializada en propiedad intelectual, derecho administrativo, constitucional, corporativo y contratacion estatal. Mas de 30 anos de trayectoria, 800+ marcas registradas ante la SIC, 5 abogados especializados.

**Problema identificado**: La firma recibe consultas iniciales de potenciales clientes (leads) por multiples canales (telefono, email, formulario web). El proceso de primera respuesta es manual, lento (horas a dias) y depende de disponibilidad del equipo legal. Esto genera:

- Perdida de leads por tiempo de respuesta excesivo
- Tiempo facturable del equipo legal consumido en consultas repetitivas
- Informacion inconsistente en respuestas iniciales
- Sin capacidad de atencion fuera de horario laboral

**Oportunidad**: Implementar un asistente virtual inteligente (Moni) que automatice la primera interaccion con leads, responda preguntas frecuentes con informacion precisa de la firma, capture datos de contacto de forma natural, y agende citas directamente en el calendario del equipo.

## 1.2 Pregunta SMART

> Como puede un asistente virtual basado en IA generativa (RAG) integrado a la plataforma web de Carrillo Abogados, reducir en un 75% el tiempo de primera respuesta a leads y automatizar el 70% de las consultas frecuentes, liberando 400 horas facturables anuales?

| Criterio | Definicion |
|----------|-----------|
| **Specific** | Asistente virtual con RAG para primera respuesta a leads |
| **Measurable** | 75% reduccion en tiempo de respuesta, 70% automatizacion de FAQs |
| **Achievable** | RAG + LLM + base de conocimiento curada de la firma |
| **Relevant** | Impacta directamente conversion de leads y rentabilidad |
| **Time-bound** | MVP en produccion para Abril 2026 |

## 1.3 Criterios de Exito

### Criterios de Negocio
- Reduccion del tiempo promedio de primera respuesta de horas a segundos
- Capacidad de atencion 24/7 sin personal adicional
- Captura automatizada de datos de contacto de leads
- Agendamiento directo de citas con abogados

### Criterios de Machine Learning
- Respuestas del RAG con similaridad coseno > 0.3 (threshold de relevancia)
- Deteccion de datos de contacto (email, telefono) con precision > 95%
- Generacion de respuestas en espanol sin hallucinations sobre la firma
- Tiempo de respuesta del modelo < 5 segundos

### Criterios Economicos
- Costo operativo mensual < USD 50 (API Gemini + infraestructura)
- ROI positivo en primer trimestre (horas facturables liberadas vs. costo)

## 1.4 Fuentes de Datos

| Fuente | Tipo | Descripcion | Volumen |
|--------|------|-------------|---------|
| Corpus de la firma | Texto estructurado (JSON) | Informacion oficial: servicios, equipo, historia, contacto | 6 documentos |
| Articulos del blog | Texto largo (JSON chunks) | Contenido educativo sobre PI, marcas, SAS, IA | 10 documentos |
| Conversaciones de chat | JSONB (PostgreSQL) | Sesiones de chat usuario-Moni | Creciente |
| Datos de leads | Estructurado (PostgreSQL) | Nombre, email, telefono, servicio de interes | Creciente |
| Citas agendadas | Estructurado (PostgreSQL) | Solicitudes de cita con estado | Creciente |

### Calidad de los datos del corpus
- **Completitud**: Toda la informacion oficial de la firma esta cubierta
- **Exactitud**: Verificada contra el sitio web en produccion (fuente de verdad)
- **Actualidad**: Sincronizada en Abril 2026 con datos vigentes
- **Formato**: JSON estructurado con campos title, content, category, metadata

## 1.5 Restricciones

| Restriccion | Tipo | Impacto |
|-------------|------|---------|
| Ley 1581/2012 | Legal | Proteccion de datos personales — consentimiento obligatorio |
| Idioma | Funcional | Solo espanol (mercado colombiano) |
| Sin asesoria legal | Etico | Moni NO puede dar asesoria legal sobre casos especificos |
| Presupuesto | Economico | Infraestructura en tier gratuito/minimo de GCP |
| Latencia | Tecnico | Respuesta < 5 segundos para UX aceptable |

## 1.6 Evaluacion de Viabilidad

| Factor | Evaluacion | Justificacion |
|--------|-----------|---------------|
| Datos disponibles | Alta | Corpus curado con informacion oficial verificada |
| Complejidad tecnica | Media | RAG con LLM comercial (Gemini), no requiere entrenamiento custom |
| Infraestructura | Alta | GCP Cloud Run con escalamiento automatico |
| Dominio del problema | Alta | FAQ y captura de leads son problemas bien definidos |
| Riesgo de hallucination | Medio | Mitigado con RAG + reglas estrictas en system prompt |

**Conclusion**: Proyecto viable con riesgo controlado. La combinacion de RAG (grounding en datos verificados) + LLM comercial (Gemini 2.5 Flash) + reglas estrictas en el system prompt mitiga los principales riesgos.
