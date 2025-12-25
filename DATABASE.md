# Modelo de Datos y Base de Datos

## 1. Visión General

Diseño de base de datos relacional (PostgreSQL) optimizado para almacenar y consultar métricas de múltiples productos digitales, organizadas por flujos y categorizadas por tipo.

## 2. Diagrama Entidad-Relación

```
┌─────────────────────┐
│     productos       │
├─────────────────────┤
│ id (PK)             │
│ codigo              │◄─────┐
│ nombre              │      │
│ descripcion         │      │
│ metadata (JSON)     │      │
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
│ nombre              │
│ codigo              │
│ tipo_metrica        │◄─────┐
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
│ created_at          │
│ updated_at          │
│ created_by          │
└─────────────────────┘


┌─────────────────────┐
│  tipos_metrica      │
├─────────────────────┤
│ id (PK)             │
│ codigo              │
│ nombre              │
│ descripcion         │
│ color_hex           │
└─────────────────────┘
```

## 3. Esquema de Tablas

### 3.1 Tabla: productos

Almacena información de cada producto digital.

```sql
CREATE TABLE productos (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(50) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    descripcion TEXT,
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
  "responsable": "string",
  "departamento": "string",
  "url_produccion": "string",
  "tecnologias": ["string"],
  "tags": ["string"]
}
```

### 3.2 Tabla: flujos

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

### 3.3 Tabla: tipos_metrica

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

### 3.4 Tabla: metricas

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

### 3.5 Tabla: valores_mensuales

Almacena los valores históricos mensuales de cada métrica.

```sql
CREATE TABLE valores_mensuales (
    id SERIAL PRIMARY KEY,
    metrica_id INTEGER NOT NULL REFERENCES metricas(id) ON DELETE CASCADE,
    periodo DATE NOT NULL, -- Formato YYYY-MM-01
    valor DECIMAL(15, 4) NOT NULL,
    observaciones TEXT,
    fuente_datos VARCHAR(200),
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(100),

    CONSTRAINT valores_periodo_formato CHECK (EXTRACT(DAY FROM periodo) = 1),
    CONSTRAINT valores_unique_metrica_periodo UNIQUE (metrica_id, periodo)
);

-- Índices
CREATE INDEX idx_valores_metrica_id ON valores_mensuales(metrica_id);
CREATE INDEX idx_valores_periodo ON valores_mensuales(periodo DESC);
CREATE INDEX idx_valores_metrica_periodo ON valores_mensuales(metrica_id, periodo DESC);
CREATE INDEX idx_valores_created_at ON valores_mensuales(created_at);
CREATE INDEX idx_valores_metadata ON valores_mensuales USING GIN(metadata);

-- Trigger para updated_at
CREATE TRIGGER update_valores_mensuales_updated_at
    BEFORE UPDATE ON valores_mensuales
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

**Campos metadata** (JSONB):
```json
{
  "importado_desde": "string",
  "validado": boolean,
  "anomalia_detectada": boolean,
  "ajustado": boolean
}
```

### 3.6 Tabla: usuarios (opcional, para autenticación)

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nombre VARCHAR(200) NOT NULL,
    apellido VARCHAR(200),
    password_hash VARCHAR(255) NOT NULL,
    rol VARCHAR(50) DEFAULT 'viewer',
    activo BOOLEAN DEFAULT TRUE,
    ultimo_acceso TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT usuarios_email_lowercase CHECK (email = LOWER(email)),
    CONSTRAINT usuarios_rol_valid CHECK (rol IN ('admin', 'editor', 'viewer'))
);

-- Índices
CREATE INDEX idx_usuarios_email ON usuarios(email);
CREATE INDEX idx_usuarios_rol ON usuarios(rol);
CREATE INDEX idx_usuarios_activo ON usuarios(activo);
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

### 4.2 Vista: vista_metricas_completas

Vista desnormalizada para facilitar consultas.

```sql
CREATE OR REPLACE VIEW vista_metricas_completas AS
SELECT
    p.id AS producto_id,
    p.codigo AS producto_codigo,
    p.nombre AS producto_nombre,
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
INNER JOIN flujos f ON f.producto_id = p.id
INNER JOIN metricas m ON m.flujo_id = f.id
INNER JOIN tipos_metrica tm ON tm.id = m.tipo_metrica_id
WHERE p.activo = TRUE
ORDER BY p.nombre, f.orden, m.orden;
```

### 4.3 Vista: vista_valores_recientes

```sql
CREATE OR REPLACE VIEW vista_valores_recientes AS
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
    p.nombre AS producto_nombre,
    p.codigo AS producto_codigo,
    tm.nombre AS tipo_metrica_nombre,
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
WHERE p.activo = TRUE
ORDER BY vm.periodo DESC, p.nombre, f.orden, m.orden;
```

## 5. Queries Comunes

### 5.1 Obtener todas las métricas de un producto

```sql
SELECT *
FROM vista_metricas_completas
WHERE producto_codigo = 'mi-producto'
ORDER BY flujo_orden, metrica_orden;
```

### 5.2 Obtener valores de métricas de últimos 12 meses

```sql
SELECT
    p.nombre AS producto,
    f.nombre AS flujo,
    m.nombre AS metrica,
    vm.periodo,
    vm.valor,
    m.unidad_medida,
    m.target
FROM valores_mensuales vm
INNER JOIN metricas m ON m.id = vm.metrica_id
INNER JOIN flujos f ON f.id = m.flujo_id
INNER JOIN productos p ON p.id = f.producto_id
WHERE p.codigo = 'mi-producto'
    AND vm.periodo >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '12 months')
ORDER BY vm.periodo DESC, f.orden, m.orden;
```

### 5.3 Comparación mes actual vs mes anterior

```sql
SELECT
    m.nombre AS metrica,
    current_month.valor AS valor_actual,
    previous_month.valor AS valor_anterior,
    ROUND(
        ((current_month.valor - previous_month.valor) / previous_month.valor * 100)::numeric,
        2
    ) AS porcentaje_cambio
FROM metricas m
LEFT JOIN valores_mensuales current_month
    ON current_month.metrica_id = m.id
    AND current_month.periodo = DATE_TRUNC('month', CURRENT_DATE)
LEFT JOIN valores_mensuales previous_month
    ON previous_month.metrica_id = m.id
    AND previous_month.periodo = DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month')
WHERE m.flujo_id IN (SELECT id FROM flujos WHERE producto_id = 1);
```

### 5.4 Ranking de productos por performance

```sql
SELECT
    p.nombre AS producto,
    COUNT(DISTINCT m.id) AS total_metricas,
    COUNT(DISTINCT CASE
        WHEN vm.valor >= m.target THEN m.id
    END) AS metricas_cumplidas,
    ROUND(
        COUNT(DISTINCT CASE WHEN vm.valor >= m.target THEN m.id END)::numeric /
        COUNT(DISTINCT m.id) * 100,
        2
    ) AS porcentaje_cumplimiento
FROM productos p
INNER JOIN flujos f ON f.producto_id = p.id
INNER JOIN metricas m ON m.flujo_id = f.id
LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id
    AND vm.periodo = DATE_TRUNC('month', CURRENT_DATE)
WHERE p.activo = TRUE
    AND m.target IS NOT NULL
GROUP BY p.id, p.nombre
ORDER BY porcentaje_cumplimiento DESC;
```

## 6. Optimización

### 6.1 Índices Compuestos

```sql
-- Para consultas frecuentes por producto y periodo
CREATE INDEX idx_valores_producto_periodo ON valores_mensuales(metrica_id, periodo DESC)
    WHERE periodo >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '24 months');

-- Para búsquedas full-text en nombres
CREATE INDEX idx_productos_nombre_trgm ON productos USING gin(nombre gin_trgm_ops);
CREATE INDEX idx_metricas_nombre_trgm ON metricas USING gin(nombre gin_trgm_ops);
```

### 6.2 Particionamiento (para grandes volúmenes)

```sql
-- Particionar valores_mensuales por año
CREATE TABLE valores_mensuales_2024 PARTITION OF valores_mensuales
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE valores_mensuales_2025 PARTITION OF valores_mensuales
    FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');
```

## 7. Seguridad

### 7.1 Row Level Security (RLS)

```sql
-- Habilitar RLS
ALTER TABLE productos ENABLE ROW LEVEL SECURITY;
ALTER TABLE valores_mensuales ENABLE ROW LEVEL SECURITY;

-- Política de ejemplo: usuarios solo ven productos de su departamento
CREATE POLICY productos_departamento_policy ON productos
    FOR SELECT
    USING (metadata->>'departamento' = current_setting('app.user_departamento'));
```

### 7.2 Roles de Base de Datos

```sql
-- Rol de solo lectura para herramientas BI
CREATE ROLE metrics_readonly;
GRANT CONNECT ON DATABASE metrics_db TO metrics_readonly;
GRANT USAGE ON SCHEMA public TO metrics_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO metrics_readonly;

-- Rol de aplicación
CREATE ROLE metrics_app;
GRANT CONNECT ON DATABASE metrics_db TO metrics_app;
GRANT USAGE ON SCHEMA public TO metrics_app;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO metrics_app;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO metrics_app;
```

## 8. Backup y Mantenimiento

### 8.1 Estrategia de Backup

```bash
# Backup diario automatizado
pg_dump -h localhost -U postgres -d metrics_db -F c -b -v -f backup_$(date +%Y%m%d).backup

# Backup de solo datos
pg_dump -h localhost -U postgres -d metrics_db -a -t valores_mensuales -f valores_backup.sql
```

### 8.2 Mantenimiento

```sql
-- Vacuum regular
VACUUM ANALYZE valores_mensuales;

-- Reindexar tablas grandes
REINDEX TABLE valores_mensuales;

-- Limpiar datos antiguos (si aplica)
DELETE FROM valores_mensuales
WHERE periodo < DATE_TRUNC('month', CURRENT_DATE - INTERVAL '5 years');
```

## 9. Modelo de Datos Ejemplo

### Ejemplo: Producto E-commerce

```sql
-- Producto
INSERT INTO productos (codigo, nombre, descripcion)
VALUES ('ecommerce-web', 'E-commerce Web', 'Tienda en línea principal');

-- Flujos
INSERT INTO flujos (producto_id, nombre, orden) VALUES
(1, 'Adquisición', 1),
(1, 'Activación', 2),
(1, 'Conversión', 3),
(1, 'Retención', 4);

-- Métricas para flujo "Conversión"
INSERT INTO metricas (flujo_id, tipo_metrica_id, codigo, nombre, unidad_medida, target, orden) VALUES
(3, 1, 'tasa_conversion', 'Tasa de Conversión', 'porcentaje', 3.5, 1),
(3, 1, 'ticket_promedio', 'Ticket Promedio', 'dinero', 85.00, 2),
(3, 1, 'ingresos_totales', 'Ingresos Totales', 'dinero', 150000.00, 3);

-- Valores mensuales
INSERT INTO valores_mensuales (metrica_id, periodo, valor, fuente_datos) VALUES
(1, '2025-01-01', 3.2, 'Google Analytics'),
(1, '2025-02-01', 3.5, 'Google Analytics'),
(1, '2025-03-01', 3.8, 'Google Analytics');
```

## 10. Consideraciones Futuras

### 10.1 Escalabilidad
- Considerar TimescaleDB para series temporales
- Implementar particionamiento por fecha
- Caché de agregaciones frecuentes

### 10.2 Funcionalidades Adicionales
- Alertas automáticas cuando métricas no cumplen targets
- Auditoría de cambios en valores
- Versionado de métricas
- Métricas calculadas en tiempo real
- Comparaciones con periodos anteriores
