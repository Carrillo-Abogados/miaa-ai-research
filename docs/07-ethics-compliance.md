# Consideraciones Eticas y Compliance

> CRISP-ML(Q) — Responsabilidad Etica y Social (Competencia C.2)

## 7.1 Marco Regulatorio

### Ley 1581 de 2012 — Proteccion de Datos Personales (Colombia)

Moni recopila datos personales (nombre, email, telefono) durante la interaccion conversacional. La Ley 1581/2012 establece obligaciones para el tratamiento de estos datos.

| Principio | Implementacion en Moni |
|-----------|----------------------|
| **Finalidad** | Datos usados exclusivamente para contacto legal y agendamiento |
| **Libertad** | Consentimiento solicitado antes de recopilar datos |
| **Veracidad** | No se alteran los datos proporcionados por el usuario |
| **Transparencia** | Disclaimer explicito en primera interaccion |
| **Seguridad** | Datos almacenados en PostgreSQL con acceso restringido |
| **Confidencialidad** | Datos no compartidos con terceros |

### Implementacion tecnica del consentimiento

```python
# En cada sesion de chat:
consent_given: bool = False  # Se activa cuando el usuario acepta

# Disclaimer automatico en primera respuesta:
"Esta informacion es orientativa y no constituye asesoria legal."
"Tu informacion sera tratada conforme a nuestra politica de proteccion
 de datos personales."
```

El flag `consent_given` se persiste en la tabla `assistant.chat_sessions` y se verifica antes de procesar datos personales.

## 7.2 Limitaciones Eticas del Sistema

### Lo que Moni NO hace

| Restriccion | Justificacion etica |
|------------|-------------------|
| No da asesoria legal sobre casos | Riesgo de dano al usuario por consejo no profesional |
| No interpreta contratos | Requiere analisis contextual que un LLM no puede garantizar |
| No cita articulos exactos | Riesgo de hallucination con consecuencias legales |
| No promete resultados | Cada caso legal tiene particularidades unicas |
| No inventa datos de la firma | Integridad de la informacion comercial |
| No responde temas no legales | Mantiene el scope profesional |

### Identificacion como IA

Moni se identifica explicitamente como **asistente virtual**. El system prompt incluye:

> "Te identificas como asistente virtual. NUNCA simules ser abogada humana."

Esto cumple con principios de transparencia en IA y evita que el usuario asuma que interactua con un profesional del derecho.

## 7.3 Sesgos y Mitigacion

### Sesgos identificados

| Sesgo | Fuente | Mitigacion |
|-------|--------|-----------|
| **Sesgo de contenido** | Corpus limitado a servicios de la firma | Documentacion explicita de lo que Moni SI y NO cubre |
| **Sesgo linguistico** | Solo espanol colombiano | Declarado como restriccion, no como limitacion oculta |
| **Sesgo de conversion** | System prompt orientado a capturar leads | Balance entre informar y convertir — el usuario siempre puede declinar |
| **Sesgo del LLM** | Sesgos inherentes de Gemini | RAG como grounding reduce hallucinations |

### Mitigacion de hallucinations

1. **RAG como grounding**: Las respuestas se basan en documentos verificados
2. **Reglas estrictas**: El system prompt prohibe inventar informacion
3. **Sin citas legales**: Evita la generacion de articulos de ley inexactos
4. **Referencia a profesional**: Siempre sugiere consulta con abogado para casos especificos

## 7.4 Impacto Social

### Impacto positivo

- **Acceso a informacion legal**: Usuarios pueden obtener orientacion basica 24/7
- **Democratizacion**: Personas sin acceso facil a un abogado pueden informarse
- **Eficiencia**: Libera tiempo de abogados para casos complejos que requieren atencion humana
- **Transparencia de costos**: Informacion publica sobre rangos de precios de servicios

### Riesgos potenciales

| Riesgo | Probabilidad | Impacto | Mitigacion |
|--------|-------------|---------|-----------|
| Usuario toma decision legal basado solo en Moni | Media | Alto | Disclaimer obligatorio, referencia a abogado |
| Datos personales expuestos | Baja | Alto | Encryption in transit (TLS), acceso restringido a DB |
| Dependencia excesiva de IA | Baja | Medio | Moni complementa, no reemplaza, al equipo legal |

## 7.5 Uso de IA Generativa en este Proyecto

Este proyecto utiliza IA Generativa de acuerdo con el **Nivel 5: Exploracion con IAG** establecido por la Universidad Icesi para el curso Proyecto I.

| Herramienta | Uso | Registro |
|-------------|-----|----------|
| Claude Code (Anthropic) | Desarrollo de codigo, documentacion, debugging | Commits en GitHub con co-author |
| Gemini 2.5 Flash (Google) | Motor de Moni en produccion | Codigo fuente documentado |
| Claude API (Anthropic) | Generacion offline de contenido del corpus | Scripts documentados |

Conforme al Manifiesto sobre IA y Educacion de la Universidad Icesi, se promueve un uso etico, critico y transparente de las tecnologias IAG.
