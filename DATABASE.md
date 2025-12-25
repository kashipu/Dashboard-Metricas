# Modelo de Datos y Base de Datos

## 1. Visión General

Diseño de base de datos relacional (PostgreSQL) optimizado para almacenar y consultar métricas de múltiples productos digitales, organizadas por flujos y categorizadas por tipo. Incluye sistema de usuarios con asignación de productos a responsables.

## 2. Diagrama Entidad-Relación

```
┌─────────────────────┐
│      usuarios       │
├─────────────────────┤
│ id (PK)             │◄─────────────┐
│ email               │              │
│ nombre              │              │
│ apellido            │              │
│ password_hash       │              │
│ cargo               │              │
│ departamento        │              │
│ avatar_url          │              │
│ rol                 │              │
│ activo              │              │
│ ultimo_acceso       │              │
│ created_at          │              │
│ updated_at          │              │
└─────────────────────┘              │
                                     │
                                     │ 1:N (responsable)
                                     │
┌─────────────────────┐              │
│     productos       │              │
├─────────────────────┤              │
│ id (PK)             │              │
│ codigo              │◄─────┐       │
│ nombre              │      │       │
│ descripcion         │      │       │
│ responsable_id (FK) │──────┼───────┘
│ metadata (JSON)     │      │
│ icono_url           │      │
│ color_hex           │      │
│ activo              │      │
│ created_at          │      │
│ updated_at          │      │
└─────────────────────┘      │
                              │
                              │ 1:N
                              │
┌─────────────────────┐      │
│       flujos        │      │
├─────────────────────┤      │
│ id (PK)             │      │
│ producto_id (FK)    │──────┘
│ nombre              │
│ descripcion         │
│ orden               │◄─────┐
│ activo              │      │
│ created_at          │      │
│ updated_at          │      │
└─────────────────────┘      │
                              │
                              │ 1:N
                              │
┌─────────────────────┐      │
│      metricas       │      │
├─────────────────────┤      │
│ id (PK)             │      │
│ flujo_id (FK)       │──────┘
│ tipo_metrica_id(FK) │◄─────┐
│ nombre              │      │
│ codigo              │      │
│ unidad_medida       │      │
│ descripcion         │      │
│ formula (JSON)      │      │
│ target              │      │
│ orden               │      │
│ activo              │      │
│ created_at          │      │
│ updated_at          │      │
└─────────────────────┘      │
                              │
                              │ 1:N
                              │
┌─────────────────────┐      │
│  valores_mensuales  │      │
├─────────────────────┤      │
│ id (PK)             │      │
│ metrica_id (FK)     │──────┘
│ periodo (YYYY-MM)   │
│ valor               │
│ observaciones       │
│ fuente_datos        │
│ created_by (FK)     │───┐
│ updated_by (FK)     │───┼──► usuarios
│ created_at          │   │
│ updated_at          │   │
└─────────────────────┘   │
                          │
┌─────────────────────┐   │
│  tipos_metrica      │   │
├─────────────────────┤   │
│ id (PK)             │   │
│ codigo              │   │
│ nombre              │   │
│ descripcion         │   │
│ color_hex           │   │
│ icono               │   │
└─────────────────────┘   │
                          │
┌─────────────────────────┤
│ producto_usuarios       │
│ (tabla de asignación)   │
├─────────────────────────┤
│ id (PK)                 │
│ producto_id (FK)        │──► productos
│ usuario_id (FK)         │──┘
│ puede_editar (BOOL)     │
│ puede_ver (BOOL)        │
│ asignado_en             │
│ asignado_por (FK)       │──► usuarios
└─────────────────────────┘
```

## 3. Esquema de Tablas

### 3.1 Tabla: usuarios

Almacena información de usuarios del sistema con diferentes roles.

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    apellido VARCHAR(200),
    password_hash VARCHAR(255) NOT NULL,
    cargo VARCHAR(100),
    departamento VARCHAR(100),
    telefono VARCHAR(50),
    avatar_url VARCHAR(500),
    rol VARCHAR(50) DEFAULT 'responsable',

    -- Estructura organizacional del Centro de Diseño
    direccion VARCHAR(100),           -- Dirección a la que pertenece
    lider_senior VARCHAR(200),        -- Líder Senior
    lider_junior VARCHAR(200),        -- Líder Junior
    tribu VARCHAR(100),               -- Tribu a la que pertenece

    metadata JSONB DEFAULT '{}',
    activo BOOLEAN DEFAULT TRUE,
    ultimo_acceso TIMESTAMP WITH TIME ZONE,
    email_verificado BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT usuarios_email_lowercase CHECK (email = LOWER(email)),
    CONSTRAINT usuarios_rol_valid CHECK (rol IN ('admin', 'responsable', 'viewer'))
);

-- Índices
CREATE INDEX idx_usuarios_email ON usuarios(email);
CREATE INDEX idx_usuarios_rol ON usuarios(rol);
CREATE INDEX idx_usuarios_activo ON usuarios(activo);
CREATE INDEX idx_usuarios_departamento ON usuarios(departamento);
CREATE INDEX idx_usuarios_direccion ON usuarios(direccion);
CREATE INDEX idx_usuarios_tribu ON usuarios(tribu);
CREATE INDEX idx_usuarios_metadata ON usuarios USING GIN(metadata);

-- Trigger para updated_at
CREATE TRIGGER update_usuarios_updated_at
    BEFORE UPDATE ON usuarios
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

**Roles del Sistema**:
- `admin`: Administrador total del sistema
- `responsable`: Usuario responsable de productos (puede editar sus productos)
- `viewer`: Solo lectura (reportes, dashboards)

**Estructura Organizacional del Centro de Diseño**:
La jerarquía organizacional se captura mediante campos simples para permitir futuras visualizaciones agregadas:
```
Centro de Diseño
└── Dirección (direccion)
    └── Líder Senior (lider_senior)
        └── Líder Junior (lider_junior)
            └── Tribu (tribu)
                └── Diseñador (nombre + apellido)
                    └── Productos
                        └── Flujos
                            └── Métricas
```

Estos campos permiten filtrar y generar visualizaciones por:
- Dirección completa
- Tribu específica
- Todo el Centro de Diseño

**Campos metadata** (JSONB) - extensible:
```json
{
  "linkedin": "string",
  "slack_id": "string",
  "notificaciones_email": boolean,
  "timezone": "string"
}
```

### 3.2 Tabla: productos

Almacena información de cada producto digital.

```sql
CREATE TABLE productos (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(50) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
    responsable_id INTEGER REFERENCES usuarios(id) ON DELETE SET NULL,
    metadata JSONB DEFAULT '{}',
    icono_url VARCHAR(500),
    color_hex VARCHAR(7) DEFAULT '#3B82F6',
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT productos_codigo_lowercase CHECK (codigo = LOWER(codigo)),
    CONSTRAINT productos_codigo_alphanumeric CHECK (codigo ~ '^[a-z0-9_-]+$')
);

-- Índices
CREATE INDEX idx_productos_codigo ON productos(codigo);
CREATE INDEX idx_productos_activo ON productos(activo);
CREATE INDEX idx_productos_responsable_id ON productos(responsable_id);
CREATE INDEX idx_productos_metadata ON productos USING GIN(metadata);

-- Trigger para updated_at
CREATE TRIGGER update_productos_updated_at
    BEFORE UPDATE ON productos
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

**Campos metadata** (JSONB):
```json
{
  "url_produccion": "string",
  "url_staging": "string",
  "tecnologias": ["string"],
  "tags": ["string"],
  "repositorio": "string"
}
```

### 3.3 Tabla: producto_usuarios (Asignaciones)

Tabla de relación muchos-a-muchos para asignar productos a usuarios.
Permite que un producto tenga múltiples responsables y un usuario gestione múltiples productos.

```sql
CREATE TABLE producto_usuarios (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER NOT NULL REFERENCES productos(id) ON DELETE CASCADE,
    usuario_id INTEGER NOT NULL REFERENCES usuarios(id) ON DELETE CASCADE,
    puede_editar BOOLEAN DEFAULT TRUE,
    puede_ver BOOLEAN DEFAULT TRUE,
    asignado_en TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    asignado_por INTEGER REFERENCES usuarios(id),

    CONSTRAINT producto_usuarios_unique UNIQUE (producto_id, usuario_id)
);

-- Índices
CREATE INDEX idx_producto_usuarios_producto ON producto_usuarios(producto_id);
CREATE INDEX idx_producto_usuarios_usuario ON producto_usuarios(usuario_id);
CREATE INDEX idx_producto_usuarios_puede_editar ON producto_usuarios(puede_editar);
```

**Nota**: Esta tabla permite flexibilidad para el futuro. Actualmente usaremos principalmente el campo `responsable_id` en la tabla productos, pero esta tabla permite asignar colaboradores adicionales.

### 3.4 Tabla: flujos

Representa los diferentes flujos de trabajo dentro de un producto.

```sql
CREATE TABLE flujos (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER NOT NULL REFERENCES productos(id) ON DELETE CASCADE,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
    orden INTEGER NOT NULL DEFAULT 0,
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT flujos_orden_positive CHECK (orden >= 0),
    CONSTRAINT flujos_unique_orden_por_producto UNIQUE (producto_id, orden)
);

-- Índices
CREATE INDEX idx_flujos_producto_id ON flujos(producto_id);
CREATE INDEX idx_flujos_activo ON flujos(activo);
CREATE INDEX idx_flujos_orden ON flujos(producto_id, orden);

-- Trigger para updated_at
CREATE TRIGGER update_flujos_updated_at
    BEFORE UPDATE ON flujos
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

### 3.5 Tabla: tipos_metrica

Catálogo de tipos de métricas (negocio, experiencia, producto).

```sql
CREATE TABLE tipos_metrica (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(50) UNIQUE NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    color_hex VARCHAR(7) DEFAULT '#6B7280',
    icono VARCHAR(50),
    orden INTEGER DEFAULT 0,

    CONSTRAINT tipos_metrica_codigo_lowercase CHECK (codigo = LOWER(codigo))
);

-- Datos iniciales
INSERT INTO tipos_metrica (codigo, nombre, descripcion, color_hex, icono, orden) VALUES
('negocio', 'Negocio', 'Métricas relacionadas con resultados de negocio e ingresos', '#10B981', 'currency-dollar', 1),
('experiencia', 'Experiencia', 'Métricas de experiencia de usuario y satisfacción', '#F59E0B', 'user-group', 2),
('producto', 'Producto', 'Métricas de uso y adopción del producto', '#3B82F6', 'chart-bar', 3);
```

### 3.6 Tabla: catalogo_metricas (Opcional - Catálogo de Métricas Predefinidas)

Catálogo de métricas predefinidas con validaciones, ayuda contextual y benchmarks.
Esta tabla es opcional pero altamente recomendada para mantener consistencia y proveer ayuda a los usuarios.

```sql
CREATE TABLE catalogo_metricas (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(100) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    tipo_metrica_id INTEGER REFERENCES tipos_metrica(id),
    unidad_medida VARCHAR(50) NOT NULL,

    -- Validaciones y reglas
    validacion JSONB DEFAULT '{}',

    -- Información de ayuda
    descripcion TEXT,
    formula TEXT,
    ejemplo_valor DECIMAL(15, 4),
    ejemplo_formato VARCHAR(100),
    ayuda_contextual TEXT,

    -- Benchmarks e interpretación
    benchmarks JSONB DEFAULT '{}',
    interpretacion JSONB DEFAULT '{}',

    -- Mejores prácticas
    mejores_practicas TEXT[],

    -- Metadata
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT catalogo_metricas_codigo_lowercase CHECK (codigo = LOWER(codigo))
);

-- Índices
CREATE INDEX idx_catalogo_metricas_tipo ON catalogo_metricas(tipo_metrica_id);
CREATE INDEX idx_catalogo_metricas_codigo ON catalogo_metricas(codigo);
CREATE INDEX idx_catalogo_metricas_activo ON catalogo_metricas(activo);

-- Trigger para updated_at
CREATE TRIGGER update_catalogo_metricas_updated_at
    BEFORE UPDATE ON catalogo_metricas
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();

-- Datos iniciales de ejemplo
INSERT INTO catalogo_metricas (
    codigo, nombre, tipo_metrica_id, unidad_medida,
    validacion, descripcion, formula, ejemplo_valor, ejemplo_formato,
    ayuda_contextual, benchmarks, interpretacion
) VALUES
-- NPS
(
    'nps',
    'NPS (Net Promoter Score)',
    (SELECT id FROM tipos_metrica WHERE codigo = 'experiencia'),
    'score_nps',
    '{"valor_minimo": -100, "valor_maximo": 100, "decimales": 0}'::jsonb,
    'Mide la lealtad del cliente y probabilidad de recomendación',
    '% Promotores (9-10) - % Detractores (0-6)',
    42,
    '42',
    'NPS va de -100 a +100. Scores sobre 50 son excelentes. Se calcula restando el % de detractores del % de promotores.',
    '{"tecnologia": "40-60", "retail": "30-50", "banca": "20-40"}'::jsonb,
    '{"excelente": "> 70", "bueno": "50-70", "aceptable": "30-50", "mejorar": "0-30", "critico": "< 0"}'::jsonb
),
-- CSAT
(
    'csat',
    'CSAT (Customer Satisfaction Score)',
    (SELECT id FROM tipos_metrica WHERE codigo = 'experiencia'),
    'score_csat',
    '{"valor_minimo": 0, "valor_maximo": 100, "decimales": 1}'::jsonb,
    'Mide la satisfacción del cliente con un producto, servicio o interacción específica',
    '(Respuestas Satisfechas / Total Respuestas) × 100',
    85.5,
    '85.5',
    'CSAT mide satisfacción inmediata. Un score de 85% significa que 85 de cada 100 clientes están satisfechos.',
    '{"promedio": "75-85"}'::jsonb,
    '{"excelente": "> 85", "bueno": "75-85", "aceptable": "65-75", "mejorar": "< 65"}'::jsonb
),
-- Tasa de Conversión
(
    'tasa_conversion',
    'Tasa de Conversión',
    (SELECT id FROM tipos_metrica WHERE codigo = 'negocio'),
    'porcentaje',
    '{"valor_minimo": 0, "valor_maximo": 100, "decimales": 2}'::jsonb,
    'Porcentaje de usuarios que completan una acción deseada (compra, registro, etc.)',
    '(Conversiones / Visitantes) × 100',
    3.5,
    '3.50%',
    'La tasa de conversión mide qué tan efectivo es tu embudo. Un valor de 3.5% significa que de cada 100 visitantes, 3.5 completan la acción deseada.',
    '{"ecommerce": "2-3%", "saas": "3-5%", "b2b": "2-3%"}'::jsonb,
    '{"excelente": "> 5%", "bueno": "3-5%", "aceptable": "2-3%", "mejorar": "< 2%"}'::jsonb
),
-- CAC
(
    'cac',
    'CAC (Customer Acquisition Cost)',
    (SELECT id FROM tipos_metrica WHERE codigo = 'negocio'),
    'dinero',
    '{"valor_minimo": 0, "valor_maximo": null, "decimales": 2}'::jsonb,
    'Costo promedio para adquirir un nuevo cliente',
    'Gastos de Marketing y Ventas / Número de Nuevos Clientes',
    45.50,
    '$45.50',
    'El CAC es crítico para determinar la rentabilidad. Idealmente, el LTV (valor de vida del cliente) debe ser al menos 3 veces el CAC.',
    '{"ecommerce": "$10-$50", "saas": "$100-$400", "b2b": "$200-$500"}'::jsonb,
    '{}'::jsonb
),
-- Churn Rate
(
    'churn_rate',
    'Churn Rate (Tasa de Cancelación)',
    (SELECT id FROM tipos_metrica WHERE codigo = 'negocio'),
    'porcentaje',
    '{"valor_minimo": 0, "valor_maximo": 100, "decimales": 2, "alerta_sobre": 5}'::jsonb,
    'Porcentaje de clientes que cancelan su suscripción en un periodo',
    '(Clientes que Cancelaron / Total Clientes al Inicio) × 100',
    3.2,
    '3.20%',
    'El churn es una de las métricas más críticas. Un churn del 5% mensual significa que pierdes el 60% de tus clientes al año. Meta ideal: < 3%.',
    '{"saas_b2c": "5-7%", "saas_b2b": "3-5%"}'::jsonb,
    '{"excelente": "< 3%", "bueno": "3-5%", "aceptable": "5-7%", "critico": "> 7%"}'::jsonb
);
```

**Campos validacion** (JSONB):
```json
{
  "valor_minimo": number | null,
  "valor_maximo": number | null,
  "decimales": number,
  "alerta_sobre": number,
  "alerta_bajo": number
}
```

**Campos benchmarks** (JSONB):
```json
{
  "industria1": "rango",
  "industria2": "rango"
}
```

**Campos interpretacion** (JSONB):
```json
{
  "excelente": "rango",
  "bueno": "rango",
  "aceptable": "rango",
  "mejorar": "rango"
}
```

### 3.7 Tabla: metricas

Define las métricas específicas de cada flujo.

```sql
CREATE TABLE metricas (
    id SERIAL PRIMARY KEY,
    flujo_id INTEGER NOT NULL REFERENCES flujos(id) ON DELETE CASCADE,
    tipo_metrica_id INTEGER NOT NULL REFERENCES tipos_metrica(id),
    codigo VARCHAR(100) NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
    unidad_medida VARCHAR(50) NOT NULL,
    formula JSONB DEFAULT '{}',
    target DECIMAL(15, 2),
    umbral_minimo DECIMAL(15, 2),
    umbral_maximo DECIMAL(15, 2),
    orden INTEGER NOT NULL DEFAULT 0,
    activo BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT metricas_codigo_unique_por_flujo UNIQUE (flujo_id, codigo),
    CONSTRAINT metricas_orden_positive CHECK (orden >= 0)
);

-- Índices
CREATE INDEX idx_metricas_flujo_id ON metricas(flujo_id);
CREATE INDEX idx_metricas_tipo ON metricas(tipo_metrica_id);
CREATE INDEX idx_metricas_activo ON metricas(activo);
CREATE INDEX idx_metricas_codigo ON metricas(codigo);
CREATE INDEX idx_metricas_formula ON metricas USING GIN(formula);

-- Trigger para updated_at
CREATE TRIGGER update_metricas_updated_at
    BEFORE UPDATE ON metricas
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

**Unidades de medida comunes**:
- `porcentaje`, `numero`, `dinero`, `tiempo_ms`, `tiempo_seg`, `ratio`, `score`, `usuarios`

**Campos formula** (JSONB) - para métricas calculadas:
```json
{
  "tipo": "calculada",
  "metricas_base": [123, 456],
  "operacion": "division",
  "formato": "porcentaje"
}
```

### 3.7 Tabla: valores_mensuales

Almacena los valores históricos mensuales de cada métrica con auditoría de quién los creó/modificó.

```sql
CREATE TABLE valores_mensuales (
    id SERIAL PRIMARY KEY,
    metrica_id INTEGER NOT NULL REFERENCES metricas(id) ON DELETE CASCADE,
    periodo DATE NOT NULL, -- Formato YYYY-MM-01
    valor DECIMAL(15, 4) NOT NULL,
    observaciones TEXT,
    fuente_datos VARCHAR(200),
    metadata JSONB DEFAULT '{}',
    created_by INTEGER REFERENCES usuarios(id),
    updated_by INTEGER REFERENCES usuarios(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT valores_periodo_formato CHECK (EXTRACT(DAY FROM periodo) = 1),
    CONSTRAINT valores_unique_metrica_periodo UNIQUE (metrica_id, periodo)
);

-- Índices
CREATE INDEX idx_valores_metrica_id ON valores_mensuales(metrica_id);
CREATE INDEX idx_valores_periodo ON valores_mensuales(periodo DESC);
CREATE INDEX idx_valores_metrica_periodo ON valores_mensuales(metrica_id, periodo DESC);
CREATE INDEX idx_valores_created_by ON valores_mensuales(created_by);
CREATE INDEX idx_valores_updated_by ON valores_mensuales(updated_by);
CREATE INDEX idx_valores_created_at ON valores_mensuales(created_at);
CREATE INDEX idx_valores_metadata ON valores_mensuales USING GIN(metadata);

-- Trigger para updated_at y updated_by
CREATE OR REPLACE FUNCTION update_valores_mensuales_trigger()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    -- updated_by debe ser seteado por la aplicación
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_valores_mensuales_updated_at
    BEFORE UPDATE ON valores_mensuales
    FOR EACH ROW
    EXECUTE FUNCTION update_valores_mensuales_trigger();
```

**Campos metadata** (JSONB):
```json
{
  "importado_desde": "string",
  "validado": boolean,
  "anomalia_detectada": boolean,
  "ajustado": boolean,
  "ip_origen": "string"
}
```

## 4. Funciones y Triggers

### 4.1 Función: update_updated_at_column

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### 4.2 Vista: vista_productos_con_responsable

Vista que une productos con información de su responsable.

```sql
CREATE OR REPLACE VIEW vista_productos_con_responsable AS
SELECT
    p.id AS producto_id,
    p.codigo AS producto_codigo,
    p.nombre AS producto_nombre,
    p.descripcion AS producto_descripcion,
    p.color_hex,
    p.icono_url,
    p.activo AS producto_activo,
    u.id AS responsable_id,
    u.nombre AS responsable_nombre,
    u.apellido AS responsable_apellido,
    u.email AS responsable_email,
    u.cargo AS responsable_cargo,
    u.departamento AS responsable_departamento,
    u.avatar_url AS responsable_avatar,
    CONCAT(u.nombre, ' ', u.apellido) AS responsable_nombre_completo,
    p.created_at,
    p.updated_at
FROM productos p
LEFT JOIN usuarios u ON u.id = p.responsable_id
WHERE p.activo = TRUE
ORDER BY p.nombre;
```

### 4.3 Vista: vista_metricas_completas

Vista desnormalizada para facilitar consultas con toda la información.

```sql
CREATE OR REPLACE VIEW vista_metricas_completas AS
SELECT
    p.id AS producto_id,
    p.codigo AS producto_codigo,
    p.nombre AS producto_nombre,
    p.responsable_id,
    CONCAT(u.nombre, ' ', u.apellido) AS responsable_nombre,
    u.email AS responsable_email,
    f.id AS flujo_id,
    f.nombre AS flujo_nombre,
    f.orden AS flujo_orden,
    m.id AS metrica_id,
    m.codigo AS metrica_codigo,
    m.nombre AS metrica_nombre,
    m.unidad_medida,
    m.target,
    tm.codigo AS tipo_metrica_codigo,
    tm.nombre AS tipo_metrica_nombre,
    tm.color_hex AS tipo_metrica_color,
    m.orden AS metrica_orden,
    m.activo AS metrica_activa
FROM productos p
LEFT JOIN usuarios u ON u.id = p.responsable_id
INNER JOIN flujos f ON f.producto_id = p.id
INNER JOIN metricas m ON m.flujo_id = f.id
INNER JOIN tipos_metrica tm ON tm.id = m.tipo_metrica_id
WHERE p.activo = TRUE
ORDER BY p.nombre, f.orden, m.orden;
```

### 4.4 Vista: vista_valores_con_auditoria

Vista que incluye información de auditoría (quién creó/modificó).

```sql
CREATE OR REPLACE VIEW vista_valores_con_auditoria AS
SELECT
    vm.id,
    vm.metrica_id,
    vm.periodo,
    vm.valor,
    vm.observaciones,
    m.nombre AS metrica_nombre,
    m.unidad_medida,
    m.target,
    f.nombre AS flujo_nombre,
    p.id AS producto_id,
    p.nombre AS producto_nombre,
    p.codigo AS producto_codigo,
    tm.nombre AS tipo_metrica_nombre,
    -- Usuario que creó
    uc.id AS creado_por_id,
    CONCAT(uc.nombre, ' ', uc.apellido) AS creado_por_nombre,
    uc.email AS creado_por_email,
    vm.created_at,
    -- Usuario que modificó
    uu.id AS actualizado_por_id,
    CONCAT(uu.nombre, ' ', uu.apellido) AS actualizado_por_nombre,
    uu.email AS actualizado_por_email,
    vm.updated_at,
    -- Responsable del producto
    ur.id AS responsable_producto_id,
    CONCAT(ur.nombre, ' ', ur.apellido) AS responsable_producto_nombre,
    -- Estado del target
    CASE
        WHEN m.target IS NOT NULL AND vm.valor >= m.target THEN 'cumplido'
        WHEN m.target IS NOT NULL AND vm.valor < m.target THEN 'pendiente'
        ELSE 'sin_target'
    END AS estado_target
FROM valores_mensuales vm
INNER JOIN metricas m ON m.id = vm.metrica_id
INNER JOIN flujos f ON f.id = m.flujo_id
INNER JOIN productos p ON p.id = f.producto_id
INNER JOIN tipos_metrica tm ON tm.id = m.tipo_metrica_id
LEFT JOIN usuarios uc ON uc.id = vm.created_by
LEFT JOIN usuarios uu ON uu.id = vm.updated_by
LEFT JOIN usuarios ur ON ur.id = p.responsable_id
WHERE p.activo = TRUE
ORDER BY vm.periodo DESC, p.nombre, f.orden, m.orden;
```

## 5. Queries Comunes

### 5.1 Obtener productos asignados a un usuario

```sql
SELECT
    p.*,
    CONCAT(u.nombre, ' ', u.apellido) AS responsable_nombre
FROM productos p
INNER JOIN usuarios u ON u.id = p.responsable_id
WHERE p.responsable_id = $1
    AND p.activo = TRUE
ORDER BY p.nombre;
```

### 5.2 Obtener todos los productos que un usuario puede gestionar

Incluye productos donde es responsable principal O tiene asignación explícita.

```sql
SELECT DISTINCT
    p.*,
    ur.nombre AS responsable_nombre,
    CASE
        WHEN p.responsable_id = $1 THEN TRUE
        ELSE pu.puede_editar
    END AS puede_editar
FROM productos p
LEFT JOIN usuarios ur ON ur.id = p.responsable_id
LEFT JOIN producto_usuarios pu ON pu.producto_id = p.id
WHERE (p.responsable_id = $1 OR pu.usuario_id = $1)
    AND p.activo = TRUE
ORDER BY p.nombre;
```

### 5.3 Obtener métricas de productos de un responsable

```sql
SELECT *
FROM vista_metricas_completas
WHERE responsable_id = $1
ORDER BY producto_nombre, flujo_orden, metrica_orden;
```

### 5.4 Registrar valor mensual con auditoría

```sql
INSERT INTO valores_mensuales (
    metrica_id,
    periodo,
    valor,
    observaciones,
    fuente_datos,
    created_by
) VALUES (
    $1,  -- metrica_id
    $2,  -- periodo (DATE '2025-03-01')
    $3,  -- valor
    $4,  -- observaciones
    $5,  -- fuente_datos
    $6   -- usuario_id que lo registra
)
ON CONFLICT (metrica_id, periodo)
DO UPDATE SET
    valor = EXCLUDED.valor,
    observaciones = EXCLUDED.observaciones,
    fuente_datos = EXCLUDED.fuente_datos,
    updated_by = EXCLUDED.created_by,
    updated_at = CURRENT_TIMESTAMP
RETURNING *;
```

### 5.5 Obtener valores mensuales con filtros

```sql
-- Filtrar por producto y flujo
SELECT
    v.*,
    m.nombre AS metrica_nombre,
    m.unidad_medida,
    tm.nombre AS tipo_metrica
FROM valores_mensuales v
INNER JOIN metricas m ON m.id = v.metrica_id
INNER JOIN tipos_metrica tm ON tm.id = m.tipo_metrica_id
INNER JOIN flujos f ON f.id = m.flujo_id
INNER JOIN productos p ON p.id = f.producto_id
WHERE p.id = $1  -- producto_id
    AND ($2::INTEGER IS NULL OR f.id = $2)  -- flujo_id (opcional)
    AND v.periodo >= $3  -- fecha_inicio
    AND v.periodo <= $4  -- fecha_fin
ORDER BY v.periodo DESC, f.orden, m.orden;
```

### 5.6 Historial de cambios de un valor

```sql
-- Para implementar auditoría completa, se puede crear una tabla de historial
CREATE TABLE valores_mensuales_historial (
    id SERIAL PRIMARY KEY,
    valor_mensual_id INTEGER REFERENCES valores_mensuales(id),
    metrica_id INTEGER,
    periodo DATE,
    valor_anterior DECIMAL(15, 4),
    valor_nuevo DECIMAL(15, 4),
    modificado_por INTEGER REFERENCES usuarios(id),
    modificado_en TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    motivo TEXT
);

-- Trigger para registrar cambios
CREATE OR REPLACE FUNCTION registrar_cambio_valor()
RETURNS TRIGGER AS $$
BEGIN
    IF OLD.valor != NEW.valor THEN
        INSERT INTO valores_mensuales_historial (
            valor_mensual_id,
            metrica_id,
            periodo,
            valor_anterior,
            valor_nuevo,
            modificado_por
        ) VALUES (
            OLD.id,
            OLD.metrica_id,
            OLD.periodo,
            OLD.valor,
            NEW.valor,
            NEW.updated_by
        );
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_historial_valores
    AFTER UPDATE ON valores_mensuales
    FOR EACH ROW
    EXECUTE FUNCTION registrar_cambio_valor();
```

### 5.7 Dashboard del responsable

```sql
-- Resumen de productos del responsable
SELECT
    COUNT(DISTINCT p.id) AS total_productos,
    COUNT(DISTINCT f.id) AS total_flujos,
    COUNT(DISTINCT m.id) AS total_metricas,
    COUNT(DISTINCT vm.id) AS total_valores_registrados,
    COUNT(DISTINCT CASE
        WHEN vm.valor >= m.target THEN m.id
    END) AS metricas_cumpliendo_target
FROM productos p
LEFT JOIN flujos f ON f.producto_id = p.id AND f.activo = TRUE
LEFT JOIN metricas m ON m.flujo_id = f.id AND m.activo = TRUE
LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id
    AND vm.periodo = DATE_TRUNC('month', CURRENT_DATE)
WHERE p.responsable_id = $1
    AND p.activo = TRUE;
```

### 5.8 Consultas por estructura organizacional

```sql
-- Obtener todos los diseñadores de una tribu
SELECT
    id,
    nombre,
    apellido,
    email,
    cargo,
    rol,
    tribu,
    lider_junior,
    lider_senior,
    direccion
FROM usuarios
WHERE tribu = $1
    AND activo = TRUE
ORDER BY nombre;

-- Obtener métricas agregadas por dirección
SELECT
    u.direccion,
    COUNT(DISTINCT u.id) AS total_disenadores,
    COUNT(DISTINCT p.id) AS total_productos,
    COUNT(DISTINCT f.id) AS total_flujos,
    COUNT(DISTINCT m.id) AS total_metricas
FROM usuarios u
LEFT JOIN productos p ON p.responsable_id = u.id
LEFT JOIN flujos f ON f.producto_id = p.id
LEFT JOIN metricas m ON m.flujo_id = f.id
WHERE u.direccion = $1
    AND u.activo = TRUE
GROUP BY u.direccion;

-- Obtener métricas agregadas por tribu
SELECT
    u.tribu,
    u.lider_junior,
    COUNT(DISTINCT u.id) AS total_disenadores,
    COUNT(DISTINCT p.id) AS total_productos,
    COUNT(DISTINCT vm.id) FILTER (WHERE vm.periodo >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '6 months')) AS valores_ultimos_6_meses
FROM usuarios u
LEFT JOIN productos p ON p.responsable_id = u.id
LEFT JOIN flujos f ON f.producto_id = p.id
LEFT JOIN metricas m ON m.flujo_id = f.id
LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id
WHERE u.tribu = $1
    AND u.activo = TRUE
GROUP BY u.tribu, u.lider_junior;

-- Dashboard completo del Centro de Diseño
SELECT
    COUNT(DISTINCT u.direccion) AS total_direcciones,
    COUNT(DISTINCT u.tribu) AS total_tribus,
    COUNT(DISTINCT u.id) FILTER (WHERE u.rol = 'responsable') AS total_disenadores,
    COUNT(DISTINCT p.id) AS total_productos,
    COUNT(DISTINCT f.id) AS total_flujos,
    COUNT(DISTINCT m.id) AS total_metricas,
    COUNT(DISTINCT vm.id) FILTER (WHERE vm.periodo >= DATE_TRUNC('month', CURRENT_DATE)) AS valores_mes_actual
FROM usuarios u
LEFT JOIN productos p ON p.responsable_id = u.id AND p.activo = TRUE
LEFT JOIN flujos f ON f.producto_id = p.id AND f.activo = TRUE
LEFT JOIN metricas m ON m.flujo_id = f.id AND m.activo = TRUE
LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id
WHERE u.activo = TRUE;
```

## 6. Optimización

### 6.1 Índices Compuestos

```sql
-- Para consultas frecuentes por responsable y periodo
CREATE INDEX idx_valores_responsable_periodo ON valores_mensuales(created_by, periodo DESC);

-- Para filtrar productos por responsable y código
CREATE INDEX idx_productos_responsable_codigo ON productos(responsable_id, codigo);

-- Para búsquedas de valores por usuario que los creó
CREATE INDEX idx_valores_created_by_periodo ON valores_mensuales(created_by, periodo DESC);
```

### 6.2 Particionamiento (para grandes volúmenes)

```sql
-- Particionar valores_mensuales por año si hay muchos datos
CREATE TABLE valores_mensuales_2024 PARTITION OF valores_mensuales
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE valores_mensuales_2025 PARTITION OF valores_mensuales
    FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');
```

## 7. Seguridad y Permisos

### 7.1 Row Level Security (RLS)

```sql
-- Habilitar RLS en productos
ALTER TABLE productos ENABLE ROW LEVEL SECURITY;

-- Política: Responsables solo ven sus productos
CREATE POLICY productos_responsable_policy ON productos
    FOR SELECT
    USING (
        responsable_id = current_setting('app.user_id')::INTEGER
        OR current_setting('app.user_rol') = 'admin'
    );

-- Política: Responsables solo editan sus productos
CREATE POLICY productos_responsable_edit_policy ON productos
    FOR UPDATE
    USING (
        responsable_id = current_setting('app.user_id')::INTEGER
        OR current_setting('app.user_rol') = 'admin'
    );

-- Habilitar RLS en valores_mensuales
ALTER TABLE valores_mensuales ENABLE ROW LEVEL SECURITY;

-- Política: Solo puedes registrar valores de tus productos
CREATE POLICY valores_mensuales_responsable_policy ON valores_mensuales
    FOR INSERT
    WITH CHECK (
        EXISTS (
            SELECT 1 FROM metricas m
            INNER JOIN flujos f ON f.id = m.flujo_id
            INNER JOIN productos p ON p.id = f.producto_id
            WHERE m.id = metrica_id
                AND (
                    p.responsable_id = current_setting('app.user_id')::INTEGER
                    OR current_setting('app.user_rol') = 'admin'
                )
        )
    );
```

### 7.2 Roles de Base de Datos

```sql
-- Rol de solo lectura para herramientas BI
CREATE ROLE metrics_readonly;
GRANT CONNECT ON DATABASE metrics_db TO metrics_readonly;
GRANT USAGE ON SCHEMA public TO metrics_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO metrics_readonly;

-- Rol de aplicación (con permisos limitados)
CREATE ROLE metrics_app;
GRANT CONNECT ON DATABASE metrics_db TO metrics_app;
GRANT USAGE ON SCHEMA public TO metrics_app;
GRANT SELECT, INSERT, UPDATE ON productos, flujos, metricas, valores_mensuales TO metrics_app;
GRANT SELECT ON usuarios, tipos_metrica TO metrics_app;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO metrics_app;

-- Rol de admin (todos los permisos)
CREATE ROLE metrics_admin;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO metrics_admin;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO metrics_admin;
```

## 8. Datos de Ejemplo

### 8.1 Seed de Usuarios

```sql
-- Insertar usuarios de ejemplo (passwords deben hashearse en la aplicación)
INSERT INTO usuarios (email, nombre, apellido, cargo, departamento, rol, password_hash) VALUES
('admin@empresa.com', 'Admin', 'Sistema', 'Administrador', 'TI', 'admin', '$2b$10$...'),
('maria.gonzalez@empresa.com', 'María', 'González', 'Product Manager', 'Producto', 'responsable', '$2b$10$...'),
('carlos.ruiz@empresa.com', 'Carlos', 'Ruiz', 'Growth Lead', 'Marketing', 'responsable', '$2b$10$...'),
('ana.lopez@empresa.com', 'Ana', 'López', 'Data Analyst', 'Analytics', 'viewer', '$2b$10$...');
```

### 8.2 Seed de Producto con Responsable

```sql
-- Insertar producto con responsable
INSERT INTO productos (codigo, nombre, descripcion, responsable_id, color_hex)
VALUES (
    'ecommerce-web',
    'E-commerce Web',
    'Tienda en línea principal',
    (SELECT id FROM usuarios WHERE email = 'maria.gonzalez@empresa.com'),
    '#10B981'
);
```

## 9. Backup, Integridad y Recuperación de Datos

### 9.1 Backups Automáticos con Dokploy

**Configuración en Dokploy Dashboard**:

Dokploy gestiona backups automáticos de PostgreSQL:

```yaml
# Configuración en Dokploy
Database: metrics-db
Backup Schedule: Daily at 2:00 AM
Retention: 7 days (configurable hasta 30 días)
Storage: Local en VPS + opcional S3/Backblaze
Compression: Enabled
```

**Características**:
- ✅ Backups diarios automáticos
- ✅ Compresión automática
- ✅ Restauración con un click desde dashboard
- ✅ Notificaciones por email si falla
- ✅ Verificación de integridad automática

**Restaurar desde Dokploy**:
1. Ir a Database → metrics-db → Backups
2. Seleccionar backup de la fecha deseada
3. Click "Restore"
4. Confirmar operación

### 9.2 Backups Manuales Completos

**Script de backup completo**:

```bash
#!/bin/bash
# backup-complete.sh - Backup completo de la base de datos

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/metricas"
DB_NAME="metrics_production"
DB_USER="metrics_user"
DB_HOST="localhost"

# Crear directorio si no existe
mkdir -p $BACKUP_DIR

# Backup en formato custom (comprimido)
pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME \
    -F c \
    -b \
    -v \
    --file="$BACKUP_DIR/full_backup_$DATE.backup"

# Backup en formato SQL (legible)
pg_dump -h $DB_HOST -U $DB_USER -d $DB_NAME \
    -F p \
    -b \
    -v \
    --file="$BACKUP_DIR/full_backup_$DATE.sql"

# Comprimir SQL
gzip "$BACKUP_DIR/full_backup_$DATE.sql"

# Verificar integridad
pg_restore --list "$BACKUP_DIR/full_backup_$DATE.backup" > /dev/null

if [ $? -eq 0 ]; then
    echo "✅ Backup exitoso: $DATE"
    echo "📁 Archivos:"
    echo "   - full_backup_$DATE.backup (para pg_restore)"
    echo "   - full_backup_$DATE.sql.gz (SQL legible)"
else
    echo "❌ Error en backup: $DATE"
    exit 1
fi

# Limpiar backups antiguos (mantener últimos 30 días)
find $BACKUP_DIR -name "*.backup" -mtime +30 -delete
find $BACKUP_DIR -name "*.sql.gz" -mtime +30 -delete

echo "🧹 Backups antiguos limpiados"
```

**Ejecutar manualmente**:
```bash
chmod +x backup-complete.sh
./backup-complete.sh
```

**Automatizar con cron**:
```bash
# Editar crontab
crontab -e

# Agregar línea (backup diario a las 3 AM)
0 3 * * * /path/to/backup-complete.sh >> /var/log/metricas-backup.log 2>&1
```

### 9.3 Exportación Completa de Datos (Portable)

**Exportar TODOS los datos en formato portable**:

```bash
#!/bin/bash
# export-all-data.sh - Exportación completa en múltiples formatos

DATE=$(date +%Y%m%d)
EXPORT_DIR="/exports/metricas_$DATE"

mkdir -p $EXPORT_DIR

# 1. Backup PostgreSQL completo
pg_dump -h localhost -U metrics_user -d metrics_production \
    -F c -f "$EXPORT_DIR/database_full.backup"

# 2. Exportar cada tabla a CSV (importable en Excel/otros DBs)
psql -h localhost -U metrics_user -d metrics_production <<EOF
\copy usuarios TO '$EXPORT_DIR/usuarios.csv' CSV HEADER;
\copy productos TO '$EXPORT_DIR/productos.csv' CSV HEADER;
\copy flujos TO '$EXPORT_DIR/flujos.csv' CSV HEADER;
\copy metricas TO '$EXPORT_DIR/metricas.csv' CSV HEADER;
\copy valores_mensuales TO '$EXPORT_DIR/valores_mensuales.csv' CSV HEADER;
\copy tipos_metrica TO '$EXPORT_DIR/tipos_metrica.csv' CSV HEADER;
\copy catalogo_metricas TO '$EXPORT_DIR/catalogo_metricas.csv' CSV HEADER;
EOF

# 3. Exportar schema completo (DDL)
pg_dump -h localhost -U metrics_user -d metrics_production \
    --schema-only \
    -f "$EXPORT_DIR/schema_complete.sql"

# 4. Crear JSON con toda la data
psql -h localhost -U metrics_user -d metrics_production -t -c "
SELECT json_build_object(
    'export_date', NOW(),
    'database', 'metrics_production',
    'usuarios', (SELECT json_agg(u) FROM usuarios u),
    'productos', (SELECT json_agg(p) FROM productos p),
    'flujos', (SELECT json_agg(f) FROM flujos f),
    'metricas', (SELECT json_agg(m) FROM metricas m),
    'valores_mensuales', (SELECT json_agg(v) FROM valores_mensuales v)
)
" > "$EXPORT_DIR/complete_data.json"

# 5. Crear README con metadata
cat > "$EXPORT_DIR/README.txt" <<ENDREADME
===========================================
EXPORTACIÓN COMPLETA - Dashboard Métricas
===========================================

Fecha de exportación: $DATE
Base de datos: metrics_production

ARCHIVOS INCLUIDOS:
-------------------
1. database_full.backup   - Backup PostgreSQL (usar pg_restore)
2. schema_complete.sql    - Schema completo (DDL)
3. complete_data.json     - Todos los datos en JSON
4. *.csv                  - Cada tabla en CSV (importable en Excel)

RESTAURACIÓN:
------------
# Opción 1: Restaurar backup completo
pg_restore -h localhost -U metrics_user -d metrics_production database_full.backup

# Opción 2: Recrear desde schema + CSVs
psql -h localhost -U metrics_user -d metrics_production -f schema_complete.sql
# Luego importar CSVs con \copy

INTEGRIDAD:
----------
Total de registros por tabla:
ENDREADME

# Agregar conteos de registros
psql -h localhost -U metrics_user -d metrics_production -t -c "
SELECT
    'usuarios: ' || COUNT(*) FROM usuarios
UNION ALL
SELECT 'productos: ' || COUNT(*) FROM productos
UNION ALL
SELECT 'flujos: ' || COUNT(*) FROM flujos
UNION ALL
SELECT 'metricas: ' || COUNT(*) FROM metricas
UNION ALL
SELECT 'valores_mensuales: ' || COUNT(*) FROM valores_mensuales;
" >> "$EXPORT_DIR/README.txt"

# 6. Comprimir todo
tar -czf "metricas_export_$DATE.tar.gz" -C /exports "metricas_$DATE"

echo "✅ Exportación completa finalizada"
echo "📦 Archivo: metricas_export_$DATE.tar.gz"
echo "📁 Tamaño: $(du -h metricas_export_$DATE.tar.gz | cut -f1)"
```

### 9.4 Restauración de Backups

**Restaurar backup completo**:

```bash
#!/bin/bash
# restore-backup.sh - Restaurar desde backup

BACKUP_FILE=$1

if [ -z "$BACKUP_FILE" ]; then
    echo "Uso: ./restore-backup.sh <archivo_backup>"
    exit 1
fi

echo "⚠️  ADVERTENCIA: Esto sobrescribirá la base de datos actual"
read -p "¿Continuar? (yes/no): " CONFIRM

if [ "$CONFIRM" != "yes" ]; then
    echo "Operación cancelada"
    exit 0
fi

# Crear backup de seguridad antes de restaurar
echo "📦 Creando backup de seguridad..."
pg_dump -h localhost -U metrics_user -d metrics_production \
    -F c -f "safety_backup_$(date +%Y%m%d_%H%M%S).backup"

# Terminar conexiones activas
echo "🔌 Cerrando conexiones activas..."
psql -h localhost -U postgres -c "
SELECT pg_terminate_backend(pg_stat_activity.pid)
FROM pg_stat_activity
WHERE pg_stat_activity.datname = 'metrics_production'
  AND pid <> pg_backend_pid();
"

# Restaurar
echo "♻️  Restaurando desde: $BACKUP_FILE"
pg_restore -h localhost -U metrics_user \
    -d metrics_production \
    --clean \
    --if-exists \
    --verbose \
    "$BACKUP_FILE"

if [ $? -eq 0 ]; then
    echo "✅ Restauración exitosa"

    # Verificar integridad
    echo "🔍 Verificando integridad..."
    psql -h localhost -U metrics_user -d metrics_production -c "
    SELECT
        'usuarios: ' || COUNT(*) FROM usuarios
    UNION ALL
    SELECT 'productos: ' || COUNT(*) FROM productos
    UNION ALL
    SELECT 'valores_mensuales: ' || COUNT(*) FROM valores_mensuales;
    "
else
    echo "❌ Error en restauración"
    exit 1
fi
```

### 9.5 Verificación de Integridad de Datos

**Script de verificación periódica**:

```sql
-- verify-integrity.sql
-- Ejecutar semanalmente para verificar integridad

-- 1. Verificar referencias huérfanas
SELECT 'VERIFICACIÓN DE INTEGRIDAD' AS status, NOW() AS timestamp;

-- Productos sin responsable válido
SELECT
    'Productos sin responsable válido' AS issue,
    COUNT(*) AS count
FROM productos p
LEFT JOIN usuarios u ON u.id = p.responsable_id
WHERE p.responsable_id IS NOT NULL AND u.id IS NULL;

-- Métricas huérfanas (sin flujo)
SELECT
    'Métricas sin flujo válido' AS issue,
    COUNT(*) AS count
FROM metricas m
LEFT JOIN flujos f ON f.id = m.flujo_id
WHERE f.id IS NULL;

-- Valores mensuales huérfanos
SELECT
    'Valores sin métrica válida' AS issue,
    COUNT(*) AS count
FROM valores_mensuales v
LEFT JOIN metricas m ON m.id = v.metrica_id
WHERE m.id IS NULL;

-- 2. Verificar duplicados
SELECT
    'Valores duplicados (métrica + período)' AS issue,
    COUNT(*) AS count
FROM (
    SELECT metrica_id, periodo, COUNT(*)
    FROM valores_mensuales
    GROUP BY metrica_id, periodo
    HAVING COUNT(*) > 1
) duplicados;

-- 3. Verificar consistencia de datos
SELECT
    'Valores fuera de rango válido' AS issue,
    COUNT(*) AS count
FROM valores_mensuales v
INNER JOIN metricas m ON m.id = v.metrica_id
WHERE
    (m.unidad_medida = 'porcentaje' AND (v.valor < 0 OR v.valor > 100))
    OR (m.codigo LIKE '%nps%' AND (v.valor < -100 OR v.valor > 100));

-- 4. Resumen de registros
SELECT
    'Total de registros' AS summary,
    json_build_object(
        'usuarios', (SELECT COUNT(*) FROM usuarios WHERE activo = true),
        'productos', (SELECT COUNT(*) FROM productos WHERE activo = true),
        'flujos', (SELECT COUNT(*) FROM flujos WHERE activo = true),
        'metricas', (SELECT COUNT(*) FROM metricas WHERE activo = true),
        'valores_mensuales', (SELECT COUNT(*) FROM valores_mensuales)
    ) AS counts;
```

**Ejecutar verificación**:
```bash
psql -h localhost -U metrics_user -d metrics_production \
    -f verify-integrity.sql \
    > integrity_check_$(date +%Y%m%d).txt
```

### 9.6 Backup Remoto (Off-site)

**Sincronizar backups a almacenamiento externo**:

```bash
#!/bin/bash
# sync-to-remote.sh - Sincronizar backups a S3/Backblaze

BACKUP_DIR="/backups/metricas"
REMOTE_BUCKET="s3://empresa-metricas-backups"

# Instalar AWS CLI o rclone
# apt-get install awscli
# o
# apt-get install rclone

# Opción 1: Sync a AWS S3
aws s3 sync $BACKUP_DIR $REMOTE_BUCKET \
    --storage-class STANDARD_IA \
    --exclude "*" \
    --include "*.backup" \
    --include "*.sql.gz"

# Opción 2: Sync a Backblaze B2 (más económico)
rclone sync $BACKUP_DIR remote:metricas-backups \
    --include "*.backup" \
    --include "*.sql.gz" \
    --progress

echo "✅ Backups sincronizados a almacenamiento remoto"
```

**Configurar como tarea automática**:
```bash
# crontab -e
0 4 * * * /path/to/sync-to-remote.sh >> /var/log/remote-sync.log 2>&1
```

### 9.7 Política de Retención Recomendada

```yaml
Backups Diarios (Dokploy):
  - Retención: 7 días
  - Frecuencia: Cada día a las 2 AM
  - Almacenamiento: Local en VPS

Backups Semanales:
  - Retención: 4 semanas
  - Frecuencia: Domingos a las 3 AM
  - Almacenamiento: Local + S3/Backblaze

Backups Mensuales:
  - Retención: 12 meses
  - Frecuencia: Primer día del mes
  - Almacenamiento: S3/Backblaze (archive)

Exportaciones Completas:
  - Retención: Indefinida
  - Frecuencia: Trimestral
  - Almacenamiento: S3 Glacier / Backblaze Archive
  - Formato: .tar.gz con CSV + JSON + SQL
```

### 9.8 Plan de Recuperación ante Desastres

**Escenarios y procedimientos**:

**Escenario 1: Pérdida de datos recientes (< 24 horas)**
```bash
# Restaurar desde último backup de Dokploy
1. Acceder a Dokploy Dashboard
2. Database → metrics-db → Backups
3. Restaurar último backup (automático de 2 AM)
4. Verificar datos
5. Tiempo estimado de recuperación: 5-10 minutos
```

**Escenario 2: Corrupción de base de datos**
```bash
# Restaurar desde backup manual verificado
1. Detener aplicación
2. Ejecutar restore-backup.sh con último backup válido
3. Verificar integridad con verify-integrity.sql
4. Reiniciar aplicación
5. Tiempo estimado: 15-30 minutos
```

**Escenario 3: Pérdida total del VPS**
```bash
# Recuperación desde backups remotos
1. Provisionar nuevo VPS
2. Instalar Dokploy
3. Crear nueva base de datos PostgreSQL
4. Descargar backup más reciente desde S3/Backblaze
5. Restaurar con pg_restore
6. Configurar aplicaciones backend y frontend
7. Tiempo estimado: 2-4 horas
```

**Contactos de emergencia**:
```
# Documentar en lugar seguro
- Admin Sistema: [email/teléfono]
- Proveedor VPS: [soporte]
- Credenciales S3: [ubicación segura]
- Procedimientos: [wiki/documentación]
```

### 9.9 Mantenimiento Regular

```sql
-- Ejecutar semanalmente
VACUUM ANALYZE valores_mensuales;
VACUUM ANALYZE productos;
VACUUM ANALYZE metricas;

-- Reindexar tablas grandes (mensualmente)
REINDEX TABLE valores_mensuales;
REINDEX INDEX idx_valores_metrica_periodo;

-- Limpiar datos de prueba/temporales (si aplica)
DELETE FROM valores_mensuales
WHERE observaciones LIKE '%TEST%' OR observaciones LIKE '%PRUEBA%';

-- Actualizar estadísticas
ANALYZE;
```

### 9.10 Checklist de Seguridad de Datos

```markdown
✅ Backups Configurados
  - [ ] Dokploy backup automático habilitado
  - [ ] Script de backup manual probado
  - [ ] Backup remoto configurado (S3/Backblaze)
  - [ ] Cron jobs funcionando

✅ Integridad
  - [ ] Verificación de integridad semanal
  - [ ] Constraints de FK activos
  - [ ] Validaciones en aplicación

✅ Recuperación
  - [ ] Proceso de restauración documentado
  - [ ] Restauración probada al menos una vez
  - [ ] Tiempos de recuperación conocidos

✅ Monitoreo
  - [ ] Alertas de fallos de backup
  - [ ] Monitoreo de espacio en disco
  - [ ] Logs de backup revisados regularmente

✅ Acceso
  - [ ] Credenciales de backup en lugar seguro
  - [ ] Solo personal autorizado tiene acceso
  - [ ] Backups encriptados (si contienen datos sensibles)
```

## 10. Consideraciones Futuras

### 10.1 Escalabilidad
- Considerar TimescaleDB para series temporales
- Implementar particionamiento por fecha en valores_mensuales
- Cache de agregaciones frecuentes con Redis
- Réplicas de lectura para reportes

### 10.2 Funcionalidades Adicionales
- Tabla de notificaciones para alertas
- Tabla de comentarios en métricas
- Tabla de metas/objetivos (diferentes de targets)
- Tabla de equipos (múltiples responsables organizados)
- Tabla de etiquetas/tags para productos
- Sistema de aprobaciones para valores (workflow)

### 10.3 Auditoría Completa
- Tabla de logs de todas las operaciones
- Registro de accesos (login/logout)
- Historial de cambios en todas las tablas
- Retención de datos eliminados (soft delete)
