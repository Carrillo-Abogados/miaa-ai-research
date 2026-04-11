# Fase 2: Ingenieria de Datos

> CRISP-ML(Q) — Data Engineering (Data Preparation)

## 2.1 Arquitectura de Datos

Se implementa una arquitectura tipo **Medallion** adaptada al contexto del proyecto:

```
[Fuentes]          [Bronze]           [Silver]            [Gold]
  Sitio web   -->  JSON crudo    -->  JSON curado    -->  Embeddings 768-dim
  Blog posts  -->  HTML/MD       -->  Chunks JSON    -->  pgvector knowledge_base
  Datos firma -->  Documentos    -->  Campos struct  -->  Corpus listo para RAG
```

### Bronze (Raw)
- Informacion extraida directamente de las fuentes originales
- Sin transformaciones, formato original

### Silver (Curado)
- Texto limpio, sin HTML, ortografia verificada
- Segmentado en documentos con campos estructurados (title, content, category)
- Metadata enriquecida (blog_url, fecha)

### Gold (Produccion)
- Embeddings generados con `gemini-embedding-001` (768 dimensiones)
- Almacenados en PostgreSQL con extension pgvector
- Indexados con HNSW para busqueda eficiente

## 2.2 Pipeline de Ingestion

### Formato de entrada

El corpus se estructura como un JSON array en `corpus/servicios-carrillo.json`:

```json
[
  {
    "title": "Titulo del documento",
    "content": "Contenido completo del documento...",
    "category": "firma|servicios|legal|blog|contacto",
    "blog_url": "https://... (opcional, solo para articulos)"
  }
]
```

### Proceso de ingestion (`scripts/ingest_corpus.py`)

1. **Carga**: Lee el JSON del corpus
2. **Embedding**: Genera embeddings batch con `gemini-embedding-001`
   - Modelo: Matryoshka (truncado de 3072 a 768 dimensiones)
   - Task type: `RETRIEVAL_DOCUMENT`
3. **Almacenamiento**: INSERT en `assistant.knowledge_base` con ON CONFLICT DO NOTHING
4. **Validacion**: Verifica que todos los documentos se insertaron correctamente

### Proceso de re-ingestion

```sql
-- 1. Limpiar tabla (ON CONFLICT DO NOTHING no actualiza existentes)
TRUNCATE assistant.knowledge_base;

-- 2. Re-ejecutar ingestion
python scripts/ingest_corpus.py --file corpus/servicios-carrillo.json
```

## 2.3 Esquema de Base de Datos

Base de datos: PostgreSQL 16 con extension **pgvector** en Neon (serverless).

### Schema: `assistant`

```sql
-- Tabla 1: Base de conocimiento (RAG)
CREATE TABLE assistant.knowledge_base (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    category TEXT DEFAULT 'general',
    embedding vector(768),
    metadata JSONB DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Tabla 2: Sesiones de chat
CREATE TABLE assistant.chat_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id TEXT UNIQUE NOT NULL,
    messages JSONB DEFAULT '[]'::jsonb,
    page_origin TEXT DEFAULT '/',
    consent_given BOOLEAN DEFAULT FALSE,
    lead_captured BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Tabla 3: Citas (registro local)
CREATE TABLE assistant.appointments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id TEXT REFERENCES assistant.chat_sessions(session_id),
    client_name TEXT,
    client_phone TEXT,
    client_email TEXT,
    service_type TEXT,
    preferred_date TEXT,
    status TEXT DEFAULT 'pending',
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Indices

| Indice | Tabla | Tipo | Configuracion |
|--------|-------|------|---------------|
| HNSW vector | knowledge_base.embedding | `vector_cosine_ops` | m=16, ef_construction=64 |
| B-tree | chat_sessions.session_id | UNIQUE | Lookup por sesion |

## 2.4 Composicion del Corpus

### Distribucion por categoria (16 documentos)

| Categoria | Documentos | Contenido |
|-----------|-----------|-----------|
| firma | 2 | Vision general, diferenciadores, historia |
| servicios | 2 | Areas de practica, propiedad intelectual |
| contacto | 1 | Proceso de consulta inicial |
| legal | 1 | Marco juridico PI en Colombia (OMPI, CAN, Madrid) |
| blog | 10 | Articulos educativos segmentados en chunks |

### Temas del blog (chunks)
- Derechos de autor en Colombia (3 docs)
- Registro de marcas: costos y plazos SIC (3 docs)
- Tipos de sociedad en Colombia / SAS (3 docs)
- IA generativa y propiedad intelectual para startups (1 doc)

## 2.5 Calidad de Datos

### Verificaciones implementadas

| Verificacion | Metodo | Resultado |
|-------------|--------|-----------|
| Completitud de campos | Validacion JSON schema | 16/16 documentos validos |
| Ortografia | Revision manual (espanol) | Corregida en S42 |
| Datos de la firma | Cross-check con frontend produccion | Sincronizado |
| URLs de blog | Verificacion HTTP | Todos responden 200 |
| Longitud de contenido | Analisis estadistico | Rango: 200-2000 palabras |

### Riesgos de calidad mitigados

- **Datos desactualizados**: El frontend en produccion es la fuente de verdad. El corpus se sincroniza con esos datos.
- **Inconsistencia entre fuentes**: Se unificaron direccion, telefono, email y anos de experiencia en todos los documentos.
- **Sesgos**: El corpus refleja exclusivamente informacion oficial de la firma, no opiniones ni interpretaciones legales.
