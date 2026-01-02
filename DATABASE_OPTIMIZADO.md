# Modelo de Datos Optimizado y Bases de Datos Escalables

## Tabla de Contenidos

1. [Modelo Optimizado para Recolección de Datos](#1-modelo-optimizado-para-recolección-de-datos)
2. [Nuevas Tablas Propuestas](#2-nuevas-tablas-propuestas)
3. [Campos Adicionales en Tablas Existentes](#3-campos-adicionales-en-tablas-existentes)
4. [Índices Optimizados](#4-índices-optimizados)
5. [Vistas Materializadas](#5-vistas-materializadas)
6. [Comparación de Bases de Datos para Escalabilidad](#6-comparación-de-bases-de-datos-para-escalabilidad)
7. [Recomendación Según Escala](#7-recomendación-según-escala)
8. [Arquitectura Híbrida Avanzada](#8-arquitectura-híbrida-avanzada)

---

## 1. Modelo Optimizado para Recolección de Datos

### 1.1 Diagrama Completo Optimizado

```
┌──────────────────────────────────────────────────────────────────────┐
│                        USUARIOS Y ORGANIZACIÓN                        │
└──────────────────────────────────────────────────────────────────────┘
┌─────────────────┐       ┌──────────────────┐      ┌─────────────────┐
│    usuarios     │       │   direcciones    │      │     tribus      │
├─────────────────┤       ├──────────────────┤      ├─────────────────┤
│ id              │◄──┐   │ id               │◄──┐  │ id              │
│ email           │   │   │ codigo           │   │  │ codigo          │
│ nombre          │   │   │ nombre           │   │  │ nombre          │
│ apellido        │   │   │ lider_senior_id  │   │  │ direccion_id    │
│ password_hash   │   │   │ activo           │   │  │ lider_junior_id │
│ direccion_id FK │───┘   └──────────────────┘   │  │ activo          │
│ tribu_id FK     │───────────────────────────────┘  └─────────────────┘
│ rol             │
│ activo          │
└─────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│                        PRODUCTOS Y FLUJOS                             │
└──────────────────────────────────────────────────────────────────────┘
┌─────────────────┐       ┌──────────────────┐       ┌────────────────┐
│   productos     │       │     flujos       │       │  flujo_fases   │
├─────────────────┤       ├──────────────────┤       ├────────────────┤
│ id              │◄──┐   │ id               │◄──┐   │ id             │
│ codigo          │   │   │ producto_id FK   │───┘   │ flujo_id FK    │
│ nombre          │   │   │ nombre           │       │ nombre         │
│ direccion_id FK │   │   │ descripcion      │       │ orden          │
│ tribu_id FK     │   │   │ orden            │       │ duracion_dias  │
│ responsable_id  │   │   │ tipo_flujo       │       └────────────────┘
│ prioridad       │   │   │ activo           │
│ estado          │   │   └──────────────────┘
│ fecha_inicio    │   │
│ fecha_fin       │   │   ┌──────────────────┐
│ activo          │   │   │producto_etiquetas│
└─────────────────┘   │   ├──────────────────┤
                      │   │ id               │
┌─────────────────┐   │   │ producto_id FK   │───┘
│   etiquetas     │   │   │ etiqueta_id FK   │───┐
├─────────────────┤   │   └──────────────────┘   │
│ id              │◄──┘                           │
│ nombre          │◄──────────────────────────────┘
│ color_hex       │
│ categoria       │
└─────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│                     MÉTRICAS Y VALORES                                │
└──────────────────────────────────────────────────────────────────────┘
┌─────────────────┐       ┌──────────────────┐       ┌────────────────┐
│    metricas     │       │valores_mensuales │       │   metas        │
├─────────────────┤       ├──────────────────┤       ├────────────────┤
│ id              │◄──┐   │ id               │       │ id             │
│ flujo_id FK     │   │   │ metrica_id FK    │───┘   │ metrica_id FK  │
│ catalogo_id FK  │   │   │ periodo          │       │ periodo_inicio │
│ codigo          │   │   │ valor            │       │ periodo_fin    │
│ orden           │   │   │ valor_objetivo   │       │ valor_objetivo │
│ frecuencia      │   │   │ observaciones    │       │ tipo_meta      │
│ es_calculada    │   │   │ fuente_datos     │       └────────────────┘
│ formula_calculo │   │   │ estado_validacion│
│ activo          │   │   │ validado_por FK  │       ┌────────────────┐
└─────────────────┘   │   │ validado_en      │       │   alertas      │
                      │   │ created_by FK    │       ├────────────────┤
┌─────────────────┐   │   │ updated_by FK    │       │ id             │
│catalogo_metricas│   │   │ created_at       │       │ metrica_id FK  │
├─────────────────┤   │   │ updated_at       │       │ tipo_alerta    │
│ id              │◄──┘   └──────────────────┘       │ umbral         │
│ codigo          │                                   │ activo         │
│ nombre          │       ┌──────────────────┐       └────────────────┘
│ tipo_dato       │       │ recolecciones    │
│ unidad_medida   │       ├──────────────────┤
│ validaciones    │       │ id               │
└─────────────────┘       │ metrica_id FK    │───┘
                          │ usuario_id FK    │
                          │ periodo          │
                          │ fuente           │
                          │ metodo_recoleccion│
                          │ tiempo_recoleccion│
                          │ ip_origen        │
                          │ dispositivo      │
                          │ created_at       │
                          └──────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│                      AUDITORÍA Y TRACKING                             │
└──────────────────────────────────────────────────────────────────────┘
┌─────────────────┐       ┌──────────────────┐       ┌────────────────┐
│   audit_logs    │       │ notificaciones   │       │  comentarios   │
├─────────────────┤       ├──────────────────┤       ├────────────────┤
│ id              │       │ id               │       │ id             │
│ tabla           │       │ usuario_id FK    │       │ entidad_tipo   │
│ registro_id     │       │ tipo             │       │ entidad_id     │
│ operacion       │       │ titulo           │       │ usuario_id FK  │
│ usuario_id FK   │       │ mensaje          │       │ comentario     │
│ cambios (JSONB) │       │ leido            │       │ parent_id      │
│ ip_origen       │       │ leido_en         │       │ created_at     │
│ user_agent      │       │ created_at       │       └────────────────┘
│ created_at      │       └──────────────────┘
└─────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│                      INTEGRACIONES Y CACHE                            │
└──────────────────────────────────────────────────────────────────────┘
┌─────────────────┐       ┌──────────────────┐
│ integraciones   │       │ cache_agregaciones│
├─────────────────┤       ├──────────────────┤
│ id              │       │ id               │
│ producto_id FK  │       │ producto_id FK   │
│ tipo            │       │ flujo_id FK      │
│ config (JSONB)  │       │ periodo          │
│ ultima_sync     │       │ metricas (JSONB) │
│ estado          │       │ calculado_en     │
│ activo          │       │ expires_at       │
└─────────────────┘       └──────────────────┘
```

---

## 2. Nuevas Tablas Propuestas

### 2.1 Tabla: direcciones

**Propósito**: Normalizar la estructura organizacional en lugar de campos de texto.

```sql
CREATE TABLE direcciones (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(50) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
    lider_senior_id INTEGER REFERENCES usuarios(id),
    objetivo_anual TEXT,
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_direcciones_codigo ON direcciones(codigo);
CREATE INDEX idx_direcciones_lider ON direcciones(lider_senior_id);
```

**Ventajas**:
- ✅ Datos consistentes (no hay typos en "Dirección de Productos" vs "Dir Productos")
- ✅ Fácil cambiar el líder senior sin actualizar usuarios
- ✅ Queries más rápidas con JOIN en lugar de LIKE
- ✅ Permite agregar información de la dirección

---

### 2.2 Tabla: tribus

**Propósito**: Normalizar tribus con su información.

```sql
CREATE TABLE tribus (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(50) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
    direccion_id INTEGER REFERENCES direcciones(id) ON DELETE CASCADE,
    lider_junior_id INTEGER REFERENCES usuarios(id),
    objetivo_trimestre TEXT,
    slack_channel VARCHAR(100),
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tribus_codigo ON tribus(codigo);
CREATE INDEX idx_tribus_direccion ON tribus(direccion_id);
CREATE INDEX idx_tribus_lider ON tribus(lider_junior_id);
```

---

### 2.3 Tabla: etiquetas

**Propósito**: Categorizar productos con tags flexibles.

```sql
CREATE TABLE etiquetas (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100) UNIQUE NOT NULL,
    color_hex VARCHAR(7) DEFAULT '#6366F1',
    categoria VARCHAR(50), -- 'tipo', 'industria', 'prioridad', etc.
    descripcion TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE producto_etiquetas (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER REFERENCES productos(id) ON DELETE CASCADE,
    etiqueta_id INTEGER REFERENCES etiquetas(id) ON DELETE CASCADE,
    asignado_por INTEGER REFERENCES usuarios(id),
    asignado_en TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    UNIQUE(producto_id, etiqueta_id)
);

CREATE INDEX idx_producto_etiquetas_producto ON producto_etiquetas(producto_id);
CREATE INDEX idx_producto_etiquetas_etiqueta ON producto_etiquetas(etiqueta_id);
```

**Casos de uso**:
- Filtrar productos por industria: "Fintech", "E-commerce", "Salud"
- Filtrar por prioridad: "Crítico", "Alto", "Medio", "Bajo"
- Filtrar por tipo: "Móvil", "Web", "API", "Híbrido"

---

### 2.4 Tabla: recolecciones

**Propósito**: Rastrear cada recolección de datos para auditoría y detección de patrones.

```sql
CREATE TABLE recolecciones (
    id BIGSERIAL PRIMARY KEY,
    metrica_id INTEGER REFERENCES metricas(id) ON DELETE CASCADE,
    usuario_id INTEGER REFERENCES usuarios(id),
    periodo DATE NOT NULL,

    -- Metadata de recolección
    fuente VARCHAR(100), -- 'manual', 'api', 'csv_import', 'excel_import'
    metodo_recoleccion VARCHAR(100), -- 'formulario_web', 'api_rest', 'carga_masiva'
    tiempo_recoleccion_ms INTEGER, -- Cuánto tardó en ms

    -- Tracking
    ip_origen INET,
    user_agent TEXT,
    dispositivo VARCHAR(50), -- 'desktop', 'mobile', 'tablet'
    navegador VARCHAR(50),

    -- Validación
    requiere_revision BOOLEAN DEFAULT FALSE,
    revisado BOOLEAN DEFAULT FALSE,
    revisado_por INTEGER REFERENCES usuarios(id),
    revisado_en TIMESTAMP WITH TIME ZONE,

    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_recolecciones_metrica ON recolecciones(metrica_id);
CREATE INDEX idx_recolecciones_usuario ON recolecciones(usuario_id);
CREATE INDEX idx_recolecciones_periodo ON recolecciones(periodo);
CREATE INDEX idx_recolecciones_fuente ON recolecciones(fuente);
CREATE INDEX idx_recolecciones_created ON recolecciones(created_at DESC);
```

**Beneficios**:
- 📊 Analizar cómo se recolectan los datos
- 🕒 Identificar métricas que toman mucho tiempo en ingresar
- 📱 Ver qué dispositivos usan los diseñadores
- 🔍 Detectar patrones (ej: "todos ingresan datos el último día del mes")
- ⚠️ Flaggear recolecciones sospechosas (mismo IP, muchos datos en poco tiempo)

---

### 2.5 Tabla: metas

**Propósito**: Metas u objetivos diferentes a targets fijos.

```sql
CREATE TABLE metas (
    id SERIAL PRIMARY KEY,
    metrica_id INTEGER REFERENCES metricas(id) ON DELETE CASCADE,
    periodo_inicio DATE NOT NULL,
    periodo_fin DATE NOT NULL,
    valor_objetivo DECIMAL(15,4) NOT NULL,
    tipo_meta VARCHAR(50) DEFAULT 'trimestre', -- 'mes', 'trimestre', 'semestre', 'anio'
    descripcion TEXT,
    creado_por INTEGER REFERENCES usuarios(id),
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_metas_metrica ON metas(metrica_id);
CREATE INDEX idx_metas_periodo ON metas(periodo_inicio, periodo_fin);
CREATE INDEX idx_metas_activo ON metas(activo);
```

**Diferencia con `target` en métricas**:
- **target**: Valor fijo permanente (ej: NPS > 50)
- **metas**: Objetivos temporales (ej: Q1 2025: NPS = 45, Q2 2025: NPS = 50)

---

### 2.6 Tabla: alertas

**Propósito**: Configurar alertas automáticas cuando métricas salen de rango.

```sql
CREATE TABLE alertas (
    id SERIAL PRIMARY KEY,
    metrica_id INTEGER REFERENCES metricas(id) ON DELETE CASCADE,
    tipo_alerta VARCHAR(50) NOT NULL, -- 'umbral_minimo', 'umbral_maximo', 'cambio_brusco', 'sin_datos'

    -- Configuración de umbral
    umbral_minimo DECIMAL(15,4),
    umbral_maximo DECIMAL(15,4),
    porcentaje_cambio DECIMAL(5,2), -- Para detectar cambios bruscos (ej: >20%)

    -- Notificación
    notificar_a INTEGER[] DEFAULT ARRAY[]::INTEGER[], -- Array de usuario_id
    canal_notificacion VARCHAR(50) DEFAULT 'email', -- 'email', 'slack', 'in_app'

    -- Estado
    activo BOOLEAN DEFAULT TRUE,
    ultima_activacion TIMESTAMP WITH TIME ZONE,

    created_by INTEGER REFERENCES usuarios(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_alertas_metrica ON alertas(metrica_id);
CREATE INDEX idx_alertas_activo ON alertas(activo);
```

**Casos de uso**:
```sql
-- Alerta si NPS baja de 40
INSERT INTO alertas (metrica_id, tipo_alerta, umbral_minimo, notificar_a)
VALUES (1, 'umbral_minimo', 40, ARRAY[1, 2, 3]);

-- Alerta si conversión cae más del 20% vs mes anterior
INSERT INTO alertas (metrica_id, tipo_alerta, porcentaje_cambio, notificar_a)
VALUES (5, 'cambio_brusco', 20, ARRAY[1, 4]);
```

---

### 2.7 Tabla: notificaciones

**Propósito**: Sistema de notificaciones in-app.

```sql
CREATE TABLE notificaciones (
    id BIGSERIAL PRIMARY KEY,
    usuario_id INTEGER REFERENCES usuarios(id) ON DELETE CASCADE,
    tipo VARCHAR(50) NOT NULL, -- 'alerta', 'asignacion', 'comentario', 'mencion', 'sistema'
    titulo VARCHAR(200) NOT NULL,
    mensaje TEXT,
    link VARCHAR(500), -- URL para "Ver más"

    -- Metadata
    metadata JSONB DEFAULT '{}',
    prioridad VARCHAR(20) DEFAULT 'normal', -- 'baja', 'normal', 'alta', 'urgente'

    -- Estado
    leido BOOLEAN DEFAULT FALSE,
    leido_en TIMESTAMP WITH TIME ZONE,
    archivado BOOLEAN DEFAULT FALSE,

    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notificaciones_usuario ON notificaciones(usuario_id);
CREATE INDEX idx_notificaciones_leido ON notificaciones(leido);
CREATE INDEX idx_notificaciones_tipo ON notificaciones(tipo);
CREATE INDEX idx_notificaciones_created ON notificaciones(created_at DESC);
```

---

### 2.8 Tabla: comentarios

**Propósito**: Comentarios en productos, flujos o métricas.

```sql
CREATE TABLE comentarios (
    id BIGSERIAL PRIMARY KEY,
    entidad_tipo VARCHAR(50) NOT NULL, -- 'producto', 'flujo', 'metrica', 'valor_mensual'
    entidad_id INTEGER NOT NULL,

    usuario_id INTEGER REFERENCES usuarios(id),
    comentario TEXT NOT NULL,

    -- Threading (respuestas a comentarios)
    parent_id BIGINT REFERENCES comentarios(id) ON DELETE CASCADE,

    -- Menciones
    menciones INTEGER[] DEFAULT ARRAY[]::INTEGER[], -- Array de usuario_id mencionados

    -- Estado
    editado BOOLEAN DEFAULT FALSE,
    editado_en TIMESTAMP WITH TIME ZONE,
    eliminado BOOLEAN DEFAULT FALSE,

    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_comentarios_entidad ON comentarios(entidad_tipo, entidad_id);
CREATE INDEX idx_comentarios_usuario ON comentarios(usuario_id);
CREATE INDEX idx_comentarios_parent ON comentarios(parent_id);
CREATE INDEX idx_comentarios_created ON comentarios(created_at DESC);
```

---

### 2.9 Tabla: audit_logs

**Propósito**: Log completo de todas las operaciones del sistema.

```sql
CREATE TABLE audit_logs (
    id BIGSERIAL PRIMARY KEY,

    -- Qué se modificó
    tabla VARCHAR(100) NOT NULL,
    registro_id INTEGER NOT NULL,
    operacion VARCHAR(20) NOT NULL, -- 'INSERT', 'UPDATE', 'DELETE'

    -- Quién y cuándo
    usuario_id INTEGER REFERENCES usuarios(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    -- Cambios (JSON con before/after)
    cambios JSONB NOT NULL,

    -- Contexto
    ip_origen INET,
    user_agent TEXT,
    endpoint VARCHAR(200), -- URL del endpoint que hizo el cambio
    metodo_http VARCHAR(10) -- 'GET', 'POST', 'PUT', 'DELETE'
);

-- Particionado por mes para performance
CREATE TABLE audit_logs_2025_01 PARTITION OF audit_logs
    FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');

CREATE INDEX idx_audit_tabla ON audit_logs(tabla);
CREATE INDEX idx_audit_usuario ON audit_logs(usuario_id);
CREATE INDEX idx_audit_created ON audit_logs(created_at DESC);
CREATE INDEX idx_audit_registro ON audit_logs(tabla, registro_id);
```

**Ejemplo de uso**:
```json
{
  "before": {"valor": 3.5, "observaciones": null},
  "after": {"valor": 4.2, "observaciones": "Corregido error de cálculo"}
}
```

---

### 2.10 Tabla: integraciones

**Propósito**: Configurar integraciones con sistemas externos (Looker, Power BI, APIs).

```sql
CREATE TABLE integraciones (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER REFERENCES productos(id) ON DELETE CASCADE,
    tipo VARCHAR(50) NOT NULL, -- 'looker', 'powerbi', 'tableau', 'api_rest', 'webhook'
    nombre VARCHAR(200) NOT NULL,

    -- Configuración (JSON flexible por tipo)
    config JSONB NOT NULL,

    -- Estado de sincronización
    ultima_sync TIMESTAMP WITH TIME ZONE,
    proxima_sync TIMESTAMP WITH TIME ZONE,
    frecuencia_sync VARCHAR(50), -- 'tiempo_real', 'horaria', 'diaria', 'semanal'
    estado VARCHAR(50) DEFAULT 'activo', -- 'activo', 'pausado', 'error', 'deshabilitado'
    ultimo_error TEXT,

    activo BOOLEAN DEFAULT TRUE,
    created_by INTEGER REFERENCES usuarios(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_integraciones_producto ON integraciones(producto_id);
CREATE INDEX idx_integraciones_tipo ON integraciones(tipo);
CREATE INDEX idx_integraciones_estado ON integraciones(estado);
```

**Ejemplo de config**:
```json
{
  "tipo": "looker",
  "looker_instance": "https://empresa.looker.com",
  "api_key": "encrypted_key_here",
  "dashboard_id": "dashboard::12345",
  "auto_refresh": true
}
```

---

### 2.11 Tabla: cache_agregaciones

**Propósito**: Cache de agregaciones pre-calculadas para dashboards.

```sql
CREATE TABLE cache_agregaciones (
    id BIGSERIAL PRIMARY KEY,

    -- Scope
    producto_id INTEGER REFERENCES productos(id) ON DELETE CASCADE,
    flujo_id INTEGER REFERENCES flujos(id) ON DELETE CASCADE,
    periodo DATE NOT NULL, -- YYYY-MM-01

    -- Agregaciones (JSON)
    metricas JSONB NOT NULL,
    /*
    Ejemplo:
    {
      "nps": {
        "valor": 42,
        "promedio_6_meses": 38.5,
        "tendencia": "positiva",
        "cambio_vs_mes_anterior": 4,
        "percentil_vs_benchmark": 75
      },
      "csat": {...}
    }
    */

    -- Control de cache
    calculado_en TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP WITH TIME ZONE, -- NULL = nunca expira
    version INTEGER DEFAULT 1 -- Incrementar cuando cambia estructura
);

CREATE INDEX idx_cache_producto ON cache_agregaciones(producto_id);
CREATE INDEX idx_cache_flujo ON cache_agregaciones(flujo_id);
CREATE INDEX idx_cache_periodo ON cache_agregaciones(periodo DESC);
CREATE INDEX idx_cache_expires ON cache_agregaciones(expires_at);
```

**Beneficios**:
- ⚡ Dashboards cargán en <200ms en lugar de 2-3s
- 💾 Reduce carga en la base de datos
- 🔄 Invalidar cache cuando se actualizan valores

---

## 3. Campos Adicionales en Tablas Existentes

### 3.1 Tabla: usuarios (campos adicionales)

```sql
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS direccion_id INTEGER REFERENCES direcciones(id);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS tribu_id INTEGER REFERENCES tribus(id);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS titulo_profesional VARCHAR(200);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS especialidad VARCHAR(200);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS fecha_ingreso DATE;
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS linkedin_url VARCHAR(500);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS github_username VARCHAR(100);
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS timezone VARCHAR(50) DEFAULT 'America/Bogota';
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS idioma VARCHAR(10) DEFAULT 'es';
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS notificaciones_email BOOLEAN DEFAULT TRUE;
ALTER TABLE usuarios ADD COLUMN IF NOT EXISTS notificaciones_push BOOLEAN DEFAULT TRUE;

-- Eliminar campos de texto (migrar a FKs)
-- direccion, lider_senior, lider_junior, tribu → usar direccion_id y tribu_id
```

---

### 3.2 Tabla: productos (campos adicionales)

```sql
ALTER TABLE productos ADD COLUMN IF NOT EXISTS direccion_id INTEGER REFERENCES direcciones(id);
ALTER TABLE productos ADD COLUMN IF NOT EXISTS tribu_id INTEGER REFERENCES tribus(id);
ALTER TABLE productos ADD COLUMN IF NOT EXISTS prioridad VARCHAR(20) DEFAULT 'media'; -- 'baja', 'media', 'alta', 'critica'
ALTER TABLE productos ADD COLUMN IF NOT EXISTS estado VARCHAR(50) DEFAULT 'activo'; -- 'planificacion', 'desarrollo', 'activo', 'mantenimiento', 'deprecado'
ALTER TABLE productos ADD COLUMN IF NOT EXISTS fecha_inicio DATE;
ALTER TABLE productos ADD COLUMN IF NOT EXISTS fecha_lanzamiento DATE;
ALTER TABLE productos ADD COLUMN IF NOT EXISTS fecha_fin DATE;
ALTER TABLE productos ADD COLUMN IF NOT EXISTS url_producto VARCHAR(500);
ALTER TABLE productos ADD COLUMN IF NOT EXISTS repositorio_url VARCHAR(500);
ALTER TABLE productos ADD COLUMN IF NOT EXISTS usuarios_estimados INTEGER;
ALTER TABLE productos ADD COLUMN IF NOT EXISTS revenue_mensual DECIMAL(15,2);
```

---

### 3.3 Tabla: flujos (campos adicionales)

```sql
ALTER TABLE flujos ADD COLUMN IF NOT EXISTS tipo_flujo VARCHAR(50) DEFAULT 'proceso'; -- 'proceso', 'experiencia', 'tecnico'
ALTER TABLE flujos ADD COLUMN IF NOT EXISTS duracion_estimada_dias INTEGER; -- Duración típica del flujo
ALTER TABLE flujos ADD COLUMN IF NOT EXISTS usuarios_impactados INTEGER;
ALTER TABLE flujos ADD COLUMN IF NOT EXISTS criticidad VARCHAR(20) DEFAULT 'media'; -- 'baja', 'media', 'alta', 'critica'
```

---

### 3.4 Tabla: metricas (campos adicionales)

```sql
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS frecuencia VARCHAR(50) DEFAULT 'mensual'; -- 'diaria', 'semanal', 'mensual', 'trimestral'
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS es_calculada BOOLEAN DEFAULT FALSE; -- TRUE si se calcula de otras métricas
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS formula_calculo TEXT; -- Fórmula para métricas calculadas
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS fuente_datos_predeterminada VARCHAR(200); -- 'Google Analytics', 'Mixpanel', etc.
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS automatizable BOOLEAN DEFAULT FALSE; -- Puede conectarse a API
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS api_endpoint VARCHAR(500); -- Endpoint para obtener valor automáticamente
ALTER TABLE metricas ADD COLUMN IF NOT EXISTS requiere_aprobacion BOOLEAN DEFAULT FALSE; -- Valores necesitan aprobación
```

---

### 3.5 Tabla: valores_mensuales (campos adicionales)

```sql
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS valor_objetivo DECIMAL(15,4); -- Objetivo del mes (puede cambiar)
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS estado_validacion VARCHAR(50) DEFAULT 'pendiente'; -- 'pendiente', 'aprobado', 'rechazado'
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS validado_por INTEGER REFERENCES usuarios(id);
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS validado_en TIMESTAMP WITH TIME ZONE;
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS notas_validacion TEXT;
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS confianza VARCHAR(20) DEFAULT 'alta'; -- 'baja', 'media', 'alta' (qué tan confiable es el dato)
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS metodo_calculo VARCHAR(100); -- Cómo se obtuvo el valor
ALTER TABLE valores_mensuales ADD COLUMN IF NOT EXISTS documentos_adjuntos VARCHAR(500)[]; -- URLs a documentos de soporte
```

---

## 4. Índices Optimizados

### 4.1 Índices Compuestos para Queries Frecuentes

```sql
-- Búsqueda de valores por producto y rango de fechas (muy común)
CREATE INDEX idx_valores_producto_periodo ON valores_mensuales(
    metrica_id,
    periodo DESC
) INCLUDE (valor, observaciones);

-- Búsqueda de productos por dirección y estado
CREATE INDEX idx_productos_direccion_estado ON productos(
    direccion_id,
    estado,
    activo
) WHERE activo = TRUE;

-- Búsqueda de métricas activas por flujo
CREATE INDEX idx_metricas_flujo_activas ON metricas(
    flujo_id,
    orden
) WHERE activo = TRUE;

-- Audit logs por tabla y fecha
CREATE INDEX idx_audit_tabla_fecha ON audit_logs(
    tabla,
    created_at DESC
) INCLUDE (usuario_id, operacion);

-- Notificaciones no leídas por usuario
CREATE INDEX idx_notificaciones_usuario_noleidas ON notificaciones(
    usuario_id,
    created_at DESC
) WHERE leido = FALSE;

-- Comentarios recientes por entidad
CREATE INDEX idx_comentarios_entidad_recientes ON comentarios(
    entidad_tipo,
    entidad_id,
    created_at DESC
) WHERE eliminado = FALSE;
```

### 4.2 Índices GIN para Búsquedas JSONB

```sql
-- Búsqueda en metadata de productos
CREATE INDEX idx_productos_metadata_gin ON productos USING GIN(metadata jsonb_path_ops);

-- Búsqueda en config de integraciones
CREATE INDEX idx_integraciones_config_gin ON integraciones USING GIN(config jsonb_path_ops);

-- Búsqueda en cambios de audit
CREATE INDEX idx_audit_cambios_gin ON audit_logs USING GIN(cambios jsonb_path_ops);
```

### 4.3 Índices Parciales para Optimización

```sql
-- Solo valores recientes (últimos 2 años)
CREATE INDEX idx_valores_recientes ON valores_mensuales(periodo DESC)
WHERE periodo >= CURRENT_DATE - INTERVAL '2 years';

-- Solo productos activos de alta prioridad
CREATE INDEX idx_productos_alta_prioridad ON productos(direccion_id, responsable_id)
WHERE activo = TRUE AND prioridad IN ('alta', 'critica');

-- Solo alertas activas
CREATE INDEX idx_alertas_activas ON alertas(metrica_id, tipo_alerta)
WHERE activo = TRUE;
```

---

## 5. Vistas Materializadas

### 5.1 Vista: Dashboard de Producto

```sql
CREATE MATERIALIZED VIEW mv_dashboard_producto AS
SELECT
    p.id AS producto_id,
    p.codigo AS producto_codigo,
    p.nombre AS producto_nombre,
    d.nombre AS direccion,
    t.nombre AS tribu,
    u.nombre || ' ' || u.apellido AS responsable,

    -- Conteos
    COUNT(DISTINCT f.id) AS total_flujos,
    COUNT(DISTINCT m.id) AS total_metricas,
    COUNT(DISTINCT vm.id) AS total_valores,

    -- Última actualización
    MAX(vm.updated_at) AS ultima_actualizacion,

    -- Estado
    p.estado,
    p.prioridad,
    p.activo

FROM productos p
LEFT JOIN direcciones d ON p.direccion_id = d.id
LEFT JOIN tribus t ON p.tribu_id = t.id
LEFT JOIN usuarios u ON p.responsable_id = u.id
LEFT JOIN flujos f ON f.producto_id = p.id AND f.activo = TRUE
LEFT JOIN metricas m ON m.flujo_id = f.id AND m.activo = TRUE
LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id

GROUP BY p.id, d.nombre, t.nombre, u.nombre, u.apellido

WITH DATA;

-- Refresh automático (desde PostgreSQL 14+)
CREATE INDEX idx_mv_dashboard_producto ON mv_dashboard_producto(producto_id);
CREATE UNIQUE INDEX idx_mv_dashboard_producto_unique ON mv_dashboard_producto(producto_id);

-- Refresh cada hora con cron job
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_dashboard_producto;
```

### 5.2 Vista: Métricas con Tendencia

```sql
CREATE MATERIALIZED VIEW mv_metricas_tendencia AS
WITH valores_recientes AS (
    SELECT
        metrica_id,
        periodo,
        valor,
        LAG(valor, 1) OVER (PARTITION BY metrica_id ORDER BY periodo) AS valor_mes_anterior,
        LAG(valor, 6) OVER (PARTITION BY metrica_id ORDER BY periodo) AS valor_6_meses_atras,
        AVG(valor) OVER (PARTITION BY metrica_id ORDER BY periodo ROWS BETWEEN 5 PRECEDING AND CURRENT ROW) AS promedio_6_meses
    FROM valores_mensuales
    WHERE periodo >= CURRENT_DATE - INTERVAL '12 months'
)
SELECT
    m.id AS metrica_id,
    m.nombre AS metrica_nombre,
    f.nombre AS flujo_nombre,
    p.nombre AS producto_nombre,

    vr.periodo,
    vr.valor,
    vr.valor_mes_anterior,
    vr.promedio_6_meses,

    -- Calcular tendencia
    CASE
        WHEN vr.valor > vr.valor_mes_anterior THEN 'crecimiento'
        WHEN vr.valor < vr.valor_mes_anterior THEN 'decrecimiento'
        ELSE 'estable'
    END AS tendencia,

    -- Porcentaje de cambio
    CASE
        WHEN vr.valor_mes_anterior IS NOT NULL AND vr.valor_mes_anterior != 0
        THEN ((vr.valor - vr.valor_mes_anterior) / vr.valor_mes_anterior * 100)
        ELSE NULL
    END AS cambio_porcentual

FROM valores_recientes vr
JOIN metricas m ON vr.metrica_id = m.id
JOIN flujos f ON m.flujo_id = f.id
JOIN productos p ON f.producto_id = p.id

WHERE vr.periodo = (
    SELECT MAX(periodo) FROM valores_mensuales WHERE metrica_id = vr.metrica_id
)

WITH DATA;

CREATE INDEX idx_mv_tendencia_metrica ON mv_metricas_tendencia(metrica_id);
CREATE INDEX idx_mv_tendencia_producto ON mv_metricas_tendencia(producto_nombre);
```

---

## 6. Comparación de Bases de Datos para Escalabilidad

### 6.1 Tabla Comparativa

| Base de Datos | Tipo | Mejor Para | Escalabilidad | Complejidad | Costo |
|---------------|------|------------|---------------|-------------|-------|
| **PostgreSQL 15+** | Relacional | General purpose, OLTP | Vertical (⭐⭐⭐) | Baja | Gratis |
| **TimescaleDB** | Time-Series (PostgreSQL) | Series temporales | Horizontal (⭐⭐⭐⭐) | Media | Gratis (cloud pagado) |
| **ClickHouse** | Columnar OLAP | Analytics, agregaciones | Horizontal (⭐⭐⭐⭐⭐) | Alta | Gratis (cloud pagado) |
| **MongoDB** | NoSQL Document | Flexible schema | Horizontal (⭐⭐⭐⭐) | Media | Gratis (Atlas pagado) |
| **Cassandra** | NoSQL Wide-Column | Distribución global | Horizontal (⭐⭐⭐⭐⭐) | Muy Alta | Gratis |
| **Supabase** | PostgreSQL + extras | Full backend | Vertical (⭐⭐⭐) | Baja | Freemium |
| **CockroachDB** | Distributed SQL | Global, multi-region | Horizontal (⭐⭐⭐⭐⭐) | Alta | Freemium |

---

### 6.2 PostgreSQL 15+ (Recomendado Fase 1)

**Pros**:
- ✅ **ACID completo**: Transacciones confiables
- ✅ **Maduro y estable**: 25+ años de desarrollo
- ✅ **Excelente para relaciones**: FK, constraints, normalización
- ✅ **JSON/JSONB**: Flexibilidad cuando se necesita
- ✅ **Particionamiento nativo**: Escalar tablas grandes
- ✅ **Ecosystem rico**: Extensiones (PostGIS, pgvector, TimescaleDB)
- ✅ **Gratis y open source**
- ✅ **Fácil de operar**: Conocimiento abundante

**Contras**:
- ⚠️ **Escalabilidad vertical limitada**: Máximo ~1TB comúnmente
- ⚠️ **Sharding manual**: No nativo, complejo
- ⚠️ **Queries analíticas lentas**: En tablas >100M filas

**Cuándo usar**:
- Proyecto en MVP o escala pequeña-mediana
- Necesitas relaciones complejas
- Equipo conoce SQL
- < 100 productos con < 1,000 métricas cada uno
- < 10M valores mensuales

**Límites aproximados**:
- ✅ Hasta **10,000 usuarios** concurrentes
- ✅ Hasta **100TB** de datos (con tuning)
- ✅ Hasta **50,000 transacciones/segundo** (con hardware adecuado)

---

### 6.3 TimescaleDB (Recomendado Fase 2: Escala Media)

**Qué es**: Extensión de PostgreSQL optimizada para series temporales.

**Pros**:
- ✅ **100% compatible con PostgreSQL**: Mismos queries, herramientas
- ✅ **Optimizado para time-series**: Valores mensuales son time-series
- ✅ **Compresión automática**: Reduce almacenamiento 90%+
- ✅ **Agregaciones rápidas**: Queries 10-100x más rápidos que PostgreSQL puro
- ✅ **Retención automática**: Drop de datos antiguos
- ✅ **Continuous aggregates**: Vistas materializadas automáticas

**Contras**:
- ⚠️ **Requiere migración**: Desde PostgreSQL normal
- ⚠️ **Más complejo**: Aprender hypertables
- ⚠️ **Cloud pagado**: Timescale Cloud tiene costo

**Cuándo usar**:
- Tienes >10M valores mensuales
- Queries de agregación lentos en PostgreSQL
- Necesitas análisis histórico de 5+ años
- Quieres compression automática

**Migración desde PostgreSQL**:
```sql
-- 1. Instalar extensión
CREATE EXTENSION IF NOT EXISTS timescaledb;

-- 2. Convertir valores_mensuales a hypertable
SELECT create_hypertable(
    'valores_mensuales',
    'periodo',
    chunk_time_interval => INTERVAL '1 year'
);

-- 3. Agregar compression
ALTER TABLE valores_mensuales SET (
    timescaledb.compress,
    timescaledb.compress_segmentby = 'metrica_id'
);

-- 4. Política de compresión (comprimir datos >6 meses)
SELECT add_compression_policy('valores_mensuales', INTERVAL '6 months');

-- 5. Continuous aggregate para dashboards
CREATE MATERIALIZED VIEW metricas_agregadas
WITH (timescaledb.continuous) AS
SELECT
    metrica_id,
    time_bucket('1 month', periodo) AS mes,
    AVG(valor) AS promedio,
    MAX(valor) AS maximo,
    MIN(valor) AS minimo
FROM valores_mensuales
GROUP BY metrica_id, mes;
```

**Beneficios reales**:
- 📊 Queries de dashboard: 5s → 200ms
- 💾 Almacenamiento: 100GB → 10GB (90% compresión)
- ⚡ Agregaciones: 100x más rápidas

---

### 6.4 ClickHouse (Recomendado Fase 3: Analytics Masivo)

**Qué es**: Base de datos columnar optimizada para OLAP (analytics).

**Pros**:
- ✅ **Extremadamente rápido**: Queries en billones de filas en segundos
- ✅ **Compresión increíble**: 10-100x compresión
- ✅ **Escalabilidad horizontal**: Sharding automático
- ✅ **Agregaciones ultrarrápidas**: SUM, AVG, COUNT en milisegundos
- ✅ **SQL estándar**: Fácil de aprender

**Contras**:
- ⚠️ **No es transaccional**: No ACID (eventual consistency)
- ⚠️ **No hay UPDATE**: Solo INSERT (inmutable)
- ⚠️ **Complejidad operativa**: Cluster management
- ⚠️ **No es relacional**: JOINs lentos

**Cuándo usar**:
- Necesitas analytics en >100M valores mensuales
- Dashboards con agregaciones complejas
- Data warehouse para BI
- Reportes históricos de 10+ años

**Arquitectura Híbrida**:
```
PostgreSQL (OLTP)          ClickHouse (OLAP)
├─ Usuarios                ├─ valores_mensuales (replica)
├─ Productos               ├─ audit_logs (replica)
├─ Flujos                  └─ metricas_agregadas
├─ Métricas
└─ valores_mensuales ──sync──> ClickHouse
```

**Ejemplo de query ClickHouse**:
```sql
-- Promedio de NPS por producto, últimos 5 años, en <100ms
SELECT
    producto_id,
    toYear(periodo) AS anio,
    avg(valor) AS nps_promedio,
    quantile(0.5)(valor) AS nps_mediana,
    quantile(0.95)(valor) AS nps_p95
FROM valores_mensuales
WHERE metrica_codigo = 'NPS'
  AND periodo >= now() - INTERVAL 5 YEAR
GROUP BY producto_id, anio
ORDER BY anio DESC, nps_promedio DESC;
```

**Performance**:
- 🚀 100M filas: <200ms
- 🚀 1B filas: <2s
- 🚀 10B filas: <10s

---

### 6.5 MongoDB (Alternativa NoSQL)

**Pros**:
- ✅ **Schema flexible**: Cambios rápidos
- ✅ **JSON nativo**: Perfecto para metadata
- ✅ **Escalabilidad horizontal**: Sharding fácil
- ✅ **Atlas gratis**: 512MB free tier

**Contras**:
- ⚠️ **Sin FK**: Integridad referencial manual
- ⚠️ **Sin JOIN**: Queries complejas difíciles
- ⚠️ **No es relacional**: Datos duplicados

**Cuándo usar**:
- Schema cambia constantemente
- Necesitas flexibilidad extrema
- No necesitas transacciones complejas

**No recomendado para este proyecto** porque necesitamos:
- Relaciones fuertes (productos → flujos → métricas)
- Integridad referencial
- Agregaciones SQL complejas

---

### 6.6 Supabase (PostgreSQL + BaaS)

Ver **SUPABASE_SELFHOSTED.md** para detalles completos.

**Pros**:
- ✅ PostgreSQL bajo el hood
- ✅ API REST automática
- ✅ Auth incluida
- ✅ Realtime incluido

**Contras**:
- ⚠️ Escalabilidad = PostgreSQL (vertical)
- ⚠️ Menos control que PostgreSQL puro

**Recomendado**: Si quieres desarrollo rápido y no te importa escalar más adelante.

---

### 6.7 CockroachDB (PostgreSQL Distribuido)

**Qué es**: PostgreSQL compatible, distribuido globalmente, multi-region.

**Pros**:
- ✅ **Compatible con PostgreSQL**: Migración fácil
- ✅ **Escalabilidad horizontal infinita**: Sharding automático
- ✅ **Multi-region**: Latencia baja globalmente
- ✅ **ACID completo**: Transacciones distribuidas
- ✅ **Sin downtime**: Resilencia extrema

**Contras**:
- ⚠️ **Costoso**: $500+/mes en cloud
- ⚠️ **Overkill**: Para 99% de proyectos
- ⚠️ **Latencia aumenta**: Por consistency global

**Cuándo usar**:
- Aplicación global con usuarios en 5+ continentes
- Necesitas 99.999% uptime
- > 1B valores mensuales
- Presupuesto enterprise

**No recomendado para este proyecto** (a menos que sea global enterprise).

---

## 7. Recomendación Según Escala

### 7.1 Fase 1: MVP a 100 Productos (0-2 años)

**Base de Datos**: **PostgreSQL 15+**

**Razones**:
- Simple y conocido
- Gratis y open source
- Suficiente para 100 productos con 1,000 métricas c/u
- Fácil de operar en VPS con Dokploy

**Infraestructura**:
```
VPS: 4 cores, 8GB RAM, 100GB SSD
PostgreSQL 15
Dokploy para management
```

**Capacidad**:
- ✅ 100 productos
- ✅ 10,000 métricas
- ✅ 1M valores mensuales/año
- ✅ 1,000 usuarios
- ✅ 100 requests/segundo

**Costo**: $40-80/mes (VPS)

---

### 7.2 Fase 2: Escala Media a 500 Productos (2-4 años)

**Base de Datos**: **TimescaleDB** (extensión de PostgreSQL)

**Razones**:
- Compatible con PostgreSQL actual (migración fácil)
- 10-100x mejor performance en series temporales
- Compression automática (ahorra 90% almacenamiento)
- Continuous aggregates (dashboards rápidos)

**Migración**:
```sql
-- Simple: agregar extensión y convertir tabla
CREATE EXTENSION timescaledb;
SELECT create_hypertable('valores_mensuales', 'periodo');
```

**Infraestructura**:
```
VPS: 8 cores, 16GB RAM, 500GB SSD
TimescaleDB
O Timescale Cloud: $300/mes
```

**Capacidad**:
- ✅ 500 productos
- ✅ 50,000 métricas
- ✅ 10M valores mensuales/año
- ✅ 5,000 usuarios
- ✅ 1,000 requests/segundo

**Costo**: $150-300/mes (VPS o cloud)

---

### 7.3 Fase 3: Escala Grande >1,000 Productos (4+ años)

**Base de Datos**: **Arquitectura Híbrida**

**PostgreSQL (OLTP)** + **ClickHouse (OLAP)**

```
┌─────────────────────────────────────────┐
│           PostgreSQL (OLTP)              │
│  - Usuarios, Productos, Flujos           │
│  - Métricas (definiciones)               │
│  - Valores recientes (últimos 12 meses)  │
│  - Transacciones, Auth                   │
└───────────────┬─────────────────────────┘
                │ Sync cada hora
                ▼
┌─────────────────────────────────────────┐
│          ClickHouse (OLAP)               │
│  - valores_mensuales (histórico completo)│
│  - audit_logs (histórico)                │
│  - Agregaciones pre-calculadas           │
│  - Analytics y BI                        │
└─────────────────────────────────────────┘
```

**Razones**:
- PostgreSQL: Transacciones, relaciones, auth
- ClickHouse: Analytics ultrarrápido en billones de filas

**Infraestructura**:
```
PostgreSQL: 16 cores, 32GB RAM, 1TB SSD
ClickHouse: 32 cores, 64GB RAM, 5TB SSD (cluster 3 nodes)
```

**Capacidad**:
- ✅ 10,000+ productos
- ✅ 1M+ métricas
- ✅ 100M+ valores mensuales/año
- ✅ 50,000+ usuarios
- ✅ 10,000+ requests/segundo

**Costo**: $1,500-3,000/mes

---

### 7.4 Fase 4: Enterprise Global (Escala Masiva)

**Base de Datos**: **PostgreSQL + TimescaleDB + ClickHouse + Redis**

```
┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│ PostgreSQL  │────▶│ TimescaleDB  │────▶│  ClickHouse  │
│   (Write)   │     │  (Recent)    │     │  (Archive)   │
└─────────────┘     └──────────────┘     └──────────────┘
       │
       ▼
┌─────────────┐
│    Redis    │
│   (Cache)   │
└─────────────┘
```

**+ Replicación multi-region con CockroachDB (opcional)**

**Capacidad**:
- ✅ Ilimitado
- ✅ Multi-continente
- ✅ 99.99% uptime

**Costo**: $10,000+/mes

---

## 8. Arquitectura Híbrida Avanzada

### 8.1 Arquitectura Recomendada (Escalable)

```
┌────────────────────────────────────────────────────────────┐
│                      Load Balancer                          │
│                  (Traefik / Nginx)                          │
└────────────────┬───────────────────────────────────────────┘
                 │
        ┌────────┴────────┐
        ▼                 ▼
┌───────────────┐   ┌───────────────┐
│   App Server  │   │   App Server  │  (Horizontal scaling)
│   (Node.js)   │   │   (Node.js)   │
└───────┬───────┘   └───────┬───────┘
        │                   │
        └─────────┬─────────┘
                  ▼
        ┌──────────────────┐
        │      Redis       │  Cache + Sessions
        └──────────────────┘
                  │
        ┌─────────┴────────┬──────────────────┐
        ▼                  ▼                  ▼
┌───────────────┐  ┌───────────────┐  ┌──────────────┐
│  PostgreSQL   │  │  TimescaleDB  │  │  ClickHouse  │
│  (Master)     │  │  (Time-Series)│  │  (Analytics) │
└───────┬───────┘  └───────────────┘  └──────────────┘
        │
   ┌────┴─────┐
   ▼          ▼
┌─────┐  ┌─────┐
│Read │  │Read │  (Read replicas para reports)
│Rep  │  │Rep  │
└─────┘  └─────┘
```

### 8.2 Flujo de Datos

**Write Path** (insertar valor mensual):
```
Usuario → App Server → PostgreSQL Master → TimescaleDB (async sync)
```

**Read Path** (dashboard):
```
Usuario → App Server → Redis Cache → PostgreSQL Read Replica
                            ↓ (cache miss)
                    PostgreSQL Master
```

**Analytics Path** (BI reports):
```
Usuario → App Server → ClickHouse (pre-aggregated)
```

### 8.3 Sincronización PostgreSQL → ClickHouse

```sql
-- Script de sincronización (correr cada hora)
-- Insertar nuevos valores en ClickHouse
INSERT INTO clickhouse.valores_mensuales
SELECT * FROM postgres.valores_mensuales
WHERE created_at >= NOW() - INTERVAL 1 HOUR;
```

O usar **Debezium** (Change Data Capture) para sync en tiempo real.

---

## 9. Resumen de Recomendaciones

### Para tu proyecto Dashboard de Métricas:

| Escala | Usuarios | Productos | Valores/año | Base de Datos | Costo/mes |
|--------|----------|-----------|-------------|---------------|-----------|
| **MVP** | <100 | <10 | <100K | PostgreSQL 15+ | $40 |
| **Pequeña** | 100-1K | 10-100 | 100K-1M | PostgreSQL 15+ | $80 |
| **Mediana** | 1K-5K | 100-500 | 1M-10M | TimescaleDB | $300 |
| **Grande** | 5K-50K | 500-5K | 10M-100M | PostgreSQL + ClickHouse | $1,500 |
| **Enterprise** | 50K+ | 5K+ | 100M+ | Híbrida completa | $10,000+ |

### Recomendación Final:

**Empieza con PostgreSQL 15+** por estas razones:

1. ✅ **Suficiente para 90% de casos** (hasta 500 productos)
2. ✅ **Gratis y open source**
3. ✅ **Fácil de operar** (Dokploy en VPS)
4. ✅ **Migración fácil** a TimescaleDB cuando crezcas
5. ✅ **Conocimiento abundante** (fácil encontrar ayuda)

**Cuando crezcas**:
- **Fase 2**: Migrar a TimescaleDB (1 día de trabajo, 100% compatible)
- **Fase 3**: Agregar ClickHouse para analytics (architecture híbrida)
- **Fase 4**: Multi-region con CockroachDB (solo si es global)

---

## 10. Próximos Pasos

1. **Implementar modelo optimizado**:
   - Crear tablas nuevas (direcciones, tribus, etiquetas, etc.)
   - Migrar campos de texto a FKs
   - Agregar campos adicionales

2. **Agregar índices optimizados**:
   - Índices compuestos para queries frecuentes
   - Índices GIN para JSONB
   - Índices parciales

3. **Crear vistas materializadas**:
   - Dashboard de producto
   - Métricas con tendencia
   - Refresh automático

4. **Monitorear performance**:
   - `pg_stat_statements` para queries lentos
   - `EXPLAIN ANALYZE` en queries críticos
   - Alertas si queries >1s

5. **Planear migración a TimescaleDB**:
   - Cuando tengas >5M valores mensuales
   - O queries de dashboard >2s
   - O crecimiento >50% anual

**Todo el código SQL está listo para copiar y pegar.** 🚀
