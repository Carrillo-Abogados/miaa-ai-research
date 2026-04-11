# Sustentacion Proyecto Final - Analisis de Datos I (MIAA 2026-1)
# Asistente Virtual Legal con Arquitectura RAG — Carrillo Abogados

**Autor**: Alexis Jaramillo Martinez
**Curso**: Fundamentos de Analisis de Datos I (EDA) — Prof. Jose Armando Ordonez
**Universidad**: ICESI — Maestria en IA Aplicada
**Fecha**: 14 de Marzo 2026
**Duracion**: 10 minutos max | Sin codigo | Audiencia no-tecnica

---

## SLIDE 1: PORTADA
**Titulo**: Asistente Virtual Legal con IA Generativa (RAG)
**Subtitulo**: Transformacion Digital de Carrillo Abogados mediante Analitica de Datos e Inteligencia Artificial
**Autor**: Alexis Jaramillo Martinez
**Programa**: Maestria en IA Aplicada — Universidad ICESI
**Curso**: Fundamentos de Analisis de Datos I
**Periodo**: 2026-1

---

## SLIDE 2: DEFINICION DEL PROBLEMA (Rubrica: 20 pts)

### Contexto del Negocio
- **Carrillo ABGD SAS** — Bufete de abogados fundado en 2001 (25 anos)
- Ubicacion: Cali, Colombia (Torre de Cali, Piso 21)
- Equipo: 7 abogados especializados + 2 administrativos
- 5 areas de practica: Administrativo, Competencias, Corporativo, Telecomunicaciones, Marcas

### El Problema
- Gestion de clientes 100% manual (Excel, llamadas, WhatsApp)
- Tiempo de primera respuesta a leads: **4.8 horas** (laboral) / **>18 horas** (no habil)
- Tasa de abandono de leads: **35%**
- Solo **20 leads/mes**, conversion **~5%** = ~15 clientes nuevos/ano
- Costo de oportunidad: **$72M COP anuales** (~480 horas facturables perdidas)

### La Oportunidad
- El **62%** de consultas son repetitivas (FAQs) → automatizables con IA
- Ninguna solucion LegalTech en Colombia opera sobre corpus juridico colombiano nativo
- Estado del arte: Harvey AI (OpenAI) y CoCounsel (Thomson Reuters) — solo en ingles

### Pregunta SMART
> Como puede un asistente virtual basado en IA generativa (RAG) integrado al CRM de Carrillo Abogados, **reducir en un 75%** el tiempo de primera respuesta a leads y **automatizar el 70%** de las consultas frecuentes, liberando **400 horas facturables** anuales en un plazo de 6 meses?

---

## SLIDE 3: OBJETIVOS Y METRICAS DE EXITO

### Objetivo Principal
Desarrollar un sistema inteligente de gestion legal que automatice la captura, clasificacion y respuesta a clientes potenciales, reduciendo drasticamente los tiempos de atencion.

### KPIs del Proyecto

| Metrica | Actual (Baseline) | Meta (6 meses) | Mejora |
|---------|:-----------------:|:--------------:|:------:|
| Tiempo respuesta a leads | 4.8 hrs | < 1.2 hrs | **-75%** |
| Leads capturados/mes | 20 | 300+ | **15x** |
| Automatizacion FAQs | 0% | 70% | **+70pp** |
| Tasa conversion | 5% | 15% | **3x** |
| Horas facturables recuperadas | 0 | 400/ano | **+$72M** |

### Metricas del Modelo
- **Lead Scoring**: Precision > 80% clasificando HOT/WARM/COLD
- **Intent Classification**: Accuracy > 85% en 5 categorias juridicas
- **Response Generation**: Tiempo < 60 segundos por lead

---

## SLIDE 4: DATOS — FUENTES Y ARQUITECTURA (Rubrica: 20 pts)

### Ecosistema de Datos Hibrido

**Fuentes Internas Estructuradas** (Disponibilidad: Alta)
- PostgreSQL 16 con 8 schemas independientes
- Schema `clients`: leads, perfiles, scoring (30-100 pts)
- Schema `cases`: tipologia procesal, timeline, actividades
- Schema `notifications`: historial de interacciones

**Fuentes Internas No Estructuradas** (Disponibilidad: Media-Alta)
- Documentos legales en PDF (contratos, memoriales)
- Requieren pipeline de Ingenieria de Datos: OCR → chunking → vectorizacion

**Fuentes Externas Publicas** (Disponibilidad: Publica)
- Corpus juridico colombiano: Constitucion, Ley 1564/2012, Ley 599/2000
- Sentencias de la Relatoria de la Corte Constitucional y Consejo de Estado

### Arquitectura Medallion (Bronze → Silver → Gold)
- **Bronze**: Captura cruda desde formularios web, Google Workspace, n8n
- **Silver**: Limpieza, deduplicacion, normalizacion en PostgreSQL
- **Gold**: Leads clasificados (HOT/WARM/COLD), scores predictivos, insights

---

## SLIDE 5: DATOS — EDA Y HALLAZGOS CLAVE

### Dataset Analizado
- **500 interacciones historicas** de leads (anonimizadas)
- 5 variables principales analizadas

### Hallazgos del Analisis Univariado

**1. Tiempo de Respuesta (response_time_minutes)**
- Media: 256 min (~4.3 hrs) | Mediana: 188 min | Max: 1,205 min (20 hrs)
- Distribucion: Asimetria positiva pronunciada (right-skewed)
- "Cola larga" de outliers = consultas nocturnas/fin de semana
- **Insight**: Valida la necesidad de atencion IA 24/7

**2. Lead Score (lead_score)**
- Rango: 30-100 | Mediana: ~55 pts | Distribucion normal truncada
- Grueso de leads en zona "tibia" (WARM: 40-69)
- **Insight**: Oportunidad para nurturing automatico con IA

**3. Categoria Juridica (consultation_category)**
- Derecho Civil: 40% del volumen (predominante)
- Laboral: 20% | Familia: 15% | Penal: 15% | Comercial: 10%
- **Insight**: MVP debe priorizar indexacion semantica en jurisdiccion Civil y Laboral

**4. Longitud del Mensaje (message_length)**
- Mayoria < 100 caracteres (mensajes cortos)
- **Insight**: La IA necesitara prompting iterativo para hacer preguntas aclaratorias

**5. Conversion (conversion_flag)**
- Tasa actual: 23.8% (119/500)
- **Insight**: Variable objetivo para futuros modelos predictivos

---

## SLIDE 6: DECISIONES SOBRE LOS DATOS

### Retos Encontrados
1. **Desbalance de clases**: Civil acapara 40% — riesgo de sesgo en clasificacion
2. **Outliers en response_time**: Cola larga > 10 hrs — representan caso real, no error
3. **Mensajes cortos**: Insuficientes para clasificacion directa → requiere interaccion iterativa
4. **Datos no estructurados**: PDFs legales sin OCR previo → pipeline especifico necesario

### Decisiones Tomadas
- **Mantener outliers**: Representan el caso de uso critico (atencion fuera de horario)
- **Priorizar Civil + Laboral**: 60% del volumen → maximo impacto con minimo esfuerzo
- **Anonimizacion PII**: Pipeline de entidades nombradas antes de embedding (Ley 1581/2012)
- **Arquitectura Medallion**: Separar calidad de datos por capas para trazabilidad

### Compliance y Etica
- **Ley 1581/2012 (Habeas Data)**: Consentimiento explicito en formularios
- **Secreto profesional**: Datos de casos solo accesibles a abogado + cliente
- **Inferencia local**: Datos sensibles NO expuestos a APIs de terceros

---

## SLIDE 7: ENFOQUE ANALITICO / MODELO (Rubrica: 10 pts)

### Tres Tipos de Analitica Integrados

**1. Analitica Descriptiva** — "Que paso?"
- Monitoreo historico del CRM (schema clients)
- Perfilado de patrones de consulta y tiempos de espera
- Dashboard de KPIs operativos

**2. Analitica Predictiva** — "Que pasara?"
- **Lead Scoring**: Modelo heuristico (MVP) → ML supervisado (futuro)
  - Score 0-100 basado en 7 caracteristicas (servicio, empresa, cargo, etc.)
  - Clasificacion: HOT (70-100) / WARM (40-69) / COLD (0-39)
- **Intent Classification**: NLP multiclase → 5 areas juridicas
  - Analisis semantico del mensaje del lead + servicio seleccionado

**3. IA Generativa / Prescriptiva** — "Como lo resolvemos?"
- **RAG Pipeline**: LLM (Claude API) + Vector DB con jurisprudencia colombiana
- Genera respuestas personalizadas en < 60 segundos
- Sugiere curso de accion legal fundamentado en hechos reales

### Justificacion
- Dominio legal exige comprension semantica profunda
- RAG mitiga alucinaciones fundamentando respuestas en documentos reales
- Supera chatbots basados en reglas que no pueden manejar ambiguedades juridicas

### Supuestos y Limitaciones
- Datos sinteticos para baseline (500 leads) — modelo mejorara con datos reales
- Scoring heuristico inicial → sera reemplazado por ML supervisado con historico real
- RAG limitado a corpus colombiano — no cubre jurisprudencia internacional

---

## SLIDE 8: RESULTADOS E INSIGHTS

### Hallazgos Principales del Proyecto

**1. El "dolor" esta cuantificado**
- Dispersion en response_time confirma cuellos de botella severos fuera del horario laboral
- Un LLM "aplanaria" la distribucion hacia la izquierda (respuestas casi inmediatas)

**2. Priorizacion basada en datos**
- 60% del volumen es Civil + Laboral → primer entrenamiento RAG enfocado ahi
- Mensajes cortos (<100 chars) → IA necesita capacidad de dialogo iterativo

**3. Oportunidad en la zona WARM**
- Grueso de leads con score 40-69 → nurturing automatico puede elevar conversion
- De 23.8% conversion actual a meta de 35%+ con seguimiento automatizado

**4. Valor economico validado**
- 480 horas facturables recuperables/ano = $72M COP
- ROI del sistema: ~6 meses (costo plataforma ~$50M vs recuperacion $72M/ano)

### Que Aprendio la Organizacion
- Los datos confirman que el problema NO es falta de demanda, sino capacidad de respuesta
- La automatizacion del primer contacto es el punto de mayor apalancamiento
- Civil y Laboral representan el 60% pero solo 2 de las 5 areas → enfocar recursos

---

## SLIDE 9: DESPLIEGUE Y USO (Rubrica: 10 pts)

### Integracion en el Proceso de Negocio

**Flujo automatizado (< 60 segundos)**:
1. Visitante llena formulario en landing page (carrilloabgd.com — YA en produccion)
2. Lead capturado en CRM (PostgreSQL, 8 microservicios Java/Spring Boot)
3. n8n Cloud ejecuta scoring y clasificacion con IA (Google Gemini)
4. Email personalizado generado y enviado automaticamente
5. Si HOT: notificacion inmediata al abogado asignado

### Usuarios Finales
| Rol | Forma de Consumo |
|-----|-----------------|
| **Abogados** (7) | Dashboard web con leads clasificados y priorizados |
| **Administrador** | Panel de KPIs, metricas de conversion, reportes |
| **Clientes** (~100+) | Portal privado con seguimiento de su caso |
| **Visitantes** | Landing page publica con formulario inteligente |

### Estado Actual del Despliegue
- Landing page en produccion (Vercel) con formulario funcional
- MW#1 Lead Lifecycle activo en n8n Cloud (scoring automatico)
- Backend: 3 microservicios funcionales (gateway, clients, cases)
- Infraestructura: Docker Compose local + GCP Cloud Run (planificado)
- MVP completo: Abril 2026

### Escalabilidad y Mantenimiento
- **Microservicios independientes**: cada componente escala por separado
- **Scale-to-zero en GCP**: costo $0 cuando no hay trafico
- **Observabilidad**: Stack Grafana LGTM (logs, metricas, trazas)
- **CI/CD**: GitHub Actions para deploy automatico

---

## SLIDE 10: CONCLUSION

### Valor Generado por el Proyecto

1. **Transformacion digital tangible**: De gestion 100% manual a plataforma inteligente con IA
2. **Reduccion de tiempos**: De 4.8 horas a < 1 minuto en respuesta a leads
3. **Escalabilidad**: De 20 a 300+ leads/mes sin incrementar equipo humano
4. **Innovacion sectorial**: Primera solucion LegalTech en Colombia sobre corpus juridico nativo
5. **Impacto financiero**: Recuperacion de $72M COP/ano en horas facturables

### Metodologia CRISP-DM — Recorrido Completo
- Business Understanding: Problema cuantificado con metricas reales
- Data Understanding: EDA sobre 500 interacciones con 4 visualizaciones
- Data Preparation: Arquitectura Medallion (Bronze/Silver/Gold)
- Modeling: Lead Scoring + Intent Classification + RAG
- Evaluation: KPIs definidos (conversion, tiempo, automatizacion)
- Deployment: Infraestructura en produccion parcial (landing + MW#1)

### Siguientes Pasos
1. Completar microservicios faltantes (payments, documents, calendar)
2. Migrar scoring heuristico a ML supervisado (Random Forest / XGBoost)
3. Implementar RAG completo con Vector DB (ChromaDB → pgvector)
4. Desplegar en GCP Cloud Run para produccion
5. Recolectar datos reales para re-entrenar modelos (Q2-Q3 2026)

### Reflexion Final
> Este proyecto demuestra que un bufete tradicional de 25 anos puede adoptar IA de forma pragmatica, usando datos reales y metodologia CRISP-DM, para resolver problemas concretos de negocio. La clave no es la tecnologia mas sofisticada, sino la correcta comprension del problema y los datos.

---

## NOTAS PARA EL PRESENTADOR

### Tiempo sugerido por slide:
| Slide | Seccion | Tiempo |
|-------|---------|--------|
| 1 | Portada | 15 seg |
| 2 | Definicion del problema | 2 min |
| 3 | Objetivos y metricas | 1 min |
| 4 | Datos - Fuentes y arquitectura | 1.5 min |
| 5 | Datos - EDA y hallazgos | 2 min |
| 6 | Decisiones sobre datos | 1 min |
| 7 | Enfoque analitico / modelo | 1.5 min |
| 8 | Resultados e insights | 1.5 min |
| 9 | Despliegue y uso | 1 min |
| 10 | Conclusion | 1 min |
| **TOTAL** | | **~10 min** |

### Tips para la sustentacion:
- **NO mostrar codigo** — solo diagramas, graficas, tablas
- Incluir las 4 graficas del EDA univariado (del Taller 1) como imagenes
- Usar la pregunta SMART como hilo conductor
- Enfatizar el IMPACTO DE NEGOCIO, no la tecnicidad
- Mencionar compliance Ley 1581/2012 — muestra madurez profesional
- Cerrar con la reflexion sobre innovacion en el sector legal colombiano
