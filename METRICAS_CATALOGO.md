# Catálogo de Métricas y Validaciones

## 1. Visión General

Catálogo completo de métricas predefinidas con sus definiciones, validaciones, rangos de valores y ayuda contextual. Este catálogo sirve como referencia para asegurar que las métricas se registren correctamente y de forma consistente.

## 2. Tipos de Unidades de Medida

### 2.1 Porcentaje (`porcentaje`)

**Rango**: 0 - 100 (o puede ser mayor a 100 en algunos casos)

**Formato**: `XX.XX%`

**Validaciones**:
```json
{
  "tipo": "porcentaje",
  "valor_minimo": 0,
  "valor_maximo": 100,
  "decimales": 2
}
```

**Métricas comunes**:
- Tasa de conversión
- Tasa de rebote
- Retención
- Churn rate

### 2.2 Score (-100 a +100) (`score_nps`)

**Rango**: -100 a +100

**Formato**: Número entero

**Validaciones**:
```json
{
  "tipo": "score_nps",
  "valor_minimo": -100,
  "valor_maximo": 100,
  "decimales": 0,
  "descripcion": "Net Promoter Score"
}
```

**Métricas comunes**:
- NPS (Net Promoter Score)

### 2.3 Score (0 a 100) (`score_csat`)

**Rango**: 0 - 100

**Formato**: Número entero o con decimales

**Validaciones**:
```json
{
  "tipo": "score_csat",
  "valor_minimo": 0,
  "valor_maximo": 100,
  "decimales": 1
}
```

**Métricas comunes**:
- CSAT (Customer Satisfaction Score)
- CES (Customer Effort Score)
- Puntajes de calidad

### 2.4 Score (1 a 5) (`score_rating`)

**Rango**: 1 - 5

**Formato**: Número con decimales

**Validaciones**:
```json
{
  "tipo": "score_rating",
  "valor_minimo": 1,
  "valor_maximo": 5,
  "decimales": 2
}
```

**Métricas comunes**:
- Rating de apps
- Calificación de productos
- Satisfacción con estrellas

### 2.5 Dinero (`dinero`)

**Rango**: 0 - ∞ (o puede ser negativo para pérdidas)

**Formato**: `$XX,XXX.XX`

**Validaciones**:
```json
{
  "tipo": "dinero",
  "valor_minimo": 0,
  "valor_maximo": null,
  "decimales": 2,
  "moneda": "USD"
}
```

**Métricas comunes**:
- Ingresos
- CAC (Customer Acquisition Cost)
- LTV (Lifetime Value)
- MRR (Monthly Recurring Revenue)
- Ticket promedio

### 2.6 Tiempo en Segundos (`tiempo_segundos`)

**Rango**: 0 - ∞

**Formato**: `XX.XX s` o convertido a minutos/horas

**Validaciones**:
```json
{
  "tipo": "tiempo_segundos",
  "valor_minimo": 0,
  "valor_maximo": null,
  "decimales": 2
}
```

**Métricas comunes**:
- Time on Task
- Tiempo de carga
- Tiempo de respuesta
- Duración de sesión

### 2.7 Tiempo en Milisegundos (`tiempo_ms`)

**Rango**: 0 - ∞

**Formato**: `XXX ms`

**Validaciones**:
```json
{
  "tipo": "tiempo_ms",
  "valor_minimo": 0,
  "valor_maximo": 10000,
  "decimales": 0,
  "alerta_sobre": 3000
}
```

**Métricas comunes**:
- Tiempo de carga de página
- Latencia de API
- First Contentful Paint
- Time to Interactive

### 2.8 Número (`numero`)

**Rango**: 0 - ∞ (o negativo si aplica)

**Formato**: `X,XXX`

**Validaciones**:
```json
{
  "tipo": "numero",
  "valor_minimo": 0,
  "valor_maximo": null,
  "decimales": 0
}
```

**Métricas comunes**:
- Usuarios activos
- Número de transacciones
- Cantidad de productos vendidos
- Nuevos registros

### 2.9 Ratio (`ratio`)

**Rango**: 0 - ∞

**Formato**: `X.XX:1` o `X.XX`

**Validaciones**:
```json
{
  "tipo": "ratio",
  "valor_minimo": 0,
  "valor_maximo": null,
  "decimales": 2
}
```

**Métricas comunes**:
- LTV/CAC
- Ratio de conversión por canal
- Relación costo/beneficio

## 3. Catálogo de Métricas Predefinidas

### 3.1 Métricas de Negocio

#### Tasa de Conversión

```json
{
  "codigo": "tasa_conversion",
  "nombre": "Tasa de Conversión",
  "tipo_metrica": "negocio",
  "unidad_medida": "porcentaje",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 2
  },
  "descripcion": "Porcentaje de usuarios que completan una acción deseada (compra, registro, etc.)",
  "formula": "(Conversiones / Visitantes) × 100",
  "ejemplo_valor": 3.5,
  "ejemplo_formato": "3.50%",
  "mejores_practicas": [
    "Medir en el mismo periodo de tiempo",
    "Considerar conversiones únicas",
    "Segmentar por canal de adquisición"
  ],
  "benchmarks": {
    "ecommerce": "2-3%",
    "saas": "3-5%",
    "b2b": "2-3%"
  },
  "ayuda_contextual": "La tasa de conversión mide qué tan efectivo es tu embudo. Un valor de 3.5% significa que de cada 100 visitantes, 3.5 completan la acción deseada."
}
```

#### CAC (Customer Acquisition Cost)

```json
{
  "codigo": "cac",
  "nombre": "CAC (Costo de Adquisición de Cliente)",
  "tipo_metrica": "negocio",
  "unidad_medida": "dinero",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": null,
    "decimales": 2
  },
  "descripcion": "Costo promedio para adquirir un nuevo cliente",
  "formula": "Gastos de Marketing y Ventas / Número de Nuevos Clientes",
  "ejemplo_valor": 45.50,
  "ejemplo_formato": "$45.50",
  "mejores_practicas": [
    "Incluir todos los gastos de marketing y ventas",
    "Calcular mensualmente para ver tendencias",
    "Comparar con LTV (debe ser CAC < LTV/3)"
  ],
  "benchmarks": {
    "ecommerce": "$10-$50",
    "saas": "$100-$400",
    "b2b": "$200-$500"
  },
  "ayuda_contextual": "El CAC es crítico para determinar la rentabilidad. Idealmente, el LTV (valor de vida del cliente) debe ser al menos 3 veces el CAC."
}
```

#### MRR (Monthly Recurring Revenue)

```json
{
  "codigo": "mrr",
  "nombre": "MRR (Ingresos Recurrentes Mensuales)",
  "tipo_metrica": "negocio",
  "unidad_medida": "dinero",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": null,
    "decimales": 2
  },
  "descripcion": "Ingresos predecibles que recibes mensualmente de suscripciones activas",
  "formula": "Suma de todas las suscripciones mensuales activas",
  "ejemplo_valor": 45000,
  "ejemplo_formato": "$45,000.00",
  "mejores_practicas": [
    "No incluir ingresos one-time",
    "Normalizar suscripciones anuales a mensuales",
    "Excluir trial periods"
  ],
  "ayuda_contextual": "El MRR es la métrica más importante para negocios SaaS. Mide la salud financiera predecible de tu negocio."
}
```

#### Churn Rate

```json
{
  "codigo": "churn_rate",
  "nombre": "Churn Rate (Tasa de Cancelación)",
  "tipo_metrica": "negocio",
  "unidad_medida": "porcentaje",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 2,
    "alerta_sobre": 5
  },
  "descripcion": "Porcentaje de clientes que cancelan su suscripción en un periodo",
  "formula": "(Clientes que Cancelaron / Total Clientes al Inicio) × 100",
  "ejemplo_valor": 3.2,
  "ejemplo_formato": "3.20%",
  "mejores_practicas": [
    "Menor es mejor",
    "Medir mensualmente",
    "Separar churn voluntario de involuntario"
  ],
  "benchmarks": {
    "saas_b2c": "5-7%",
    "saas_b2b": "3-5%"
  },
  "ayuda_contextual": "El churn es una de las métricas más críticas. Un churn del 5% mensual significa que pierdes el 60% de tus clientes al año. Meta ideal: < 3%."
}
```

### 3.2 Métricas de Experiencia

#### NPS (Net Promoter Score)

```json
{
  "codigo": "nps",
  "nombre": "NPS (Net Promoter Score)",
  "tipo_metrica": "experiencia",
  "unidad_medida": "score_nps",
  "validacion": {
    "valor_minimo": -100,
    "valor_maximo": 100,
    "decimales": 0
  },
  "descripcion": "Mide la lealtad del cliente y probabilidad de recomendación",
  "formula": "% Promotores (9-10) - % Detractores (0-6)",
  "ejemplo_valor": 42,
  "ejemplo_formato": "42",
  "interpretacion": {
    "excelente": "> 70",
    "bueno": "50-70",
    "aceptable": "30-50",
    "mejorar": "0-30",
    "critico": "< 0"
  },
  "mejores_practicas": [
    "Encuestar después de interacciones clave",
    "Mínimo 100 respuestas para ser significativo",
    "Hacer seguimiento con detractores"
  ],
  "benchmarks": {
    "tecnologia": "40-60",
    "retail": "30-50",
    "banca": "20-40"
  },
  "ayuda_contextual": "NPS va de -100 a +100. Scores sobre 50 son excelentes. Se calcula restando el % de detractores del % de promotores."
}
```

#### CSAT (Customer Satisfaction Score)

```json
{
  "codigo": "csat",
  "nombre": "CSAT (Puntuación de Satisfacción del Cliente)",
  "tipo_metrica": "experiencia",
  "unidad_medida": "score_csat",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 1
  },
  "descripcion": "Mide la satisfacción del cliente con un producto, servicio o interacción específica",
  "formula": "(Respuestas Satisfechas / Total Respuestas) × 100",
  "ejemplo_valor": 85.5,
  "ejemplo_formato": "85.5",
  "interpretacion": {
    "excelente": "> 85",
    "bueno": "75-85",
    "aceptable": "65-75",
    "mejorar": "< 65"
  },
  "mejores_practicas": [
    "Encuestar inmediatamente después de la interacción",
    "Usar escala de 1-5 o 1-10",
    "Hacer pregunta específica sobre la experiencia"
  ],
  "ayuda_contextual": "CSAT mide satisfacción inmediata. Un score de 85% significa que 85 de cada 100 clientes están satisfechos."
}
```

#### Tiempo de Carga

```json
{
  "codigo": "tiempo_carga",
  "nombre": "Tiempo de Carga de Página",
  "tipo_metrica": "experiencia",
  "unidad_medida": "tiempo_segundos",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 60,
    "decimales": 2,
    "alerta_sobre": 3
  },
  "descripcion": "Tiempo que tarda en cargar completamente una página web",
  "ejemplo_valor": 2.5,
  "ejemplo_formato": "2.50s",
  "interpretacion": {
    "excelente": "< 1s",
    "bueno": "1-2s",
    "aceptable": "2-3s",
    "mejorar": "> 3s"
  },
  "mejores_practicas": [
    "Medir en condiciones reales (3G, 4G, WiFi)",
    "Usar herramientas como Lighthouse",
    "Considerar First Contentful Paint"
  ],
  "benchmarks": {
    "mobile": "< 3s",
    "desktop": "< 2s"
  },
  "ayuda_contextual": "Cada segundo de retraso puede reducir conversiones en 7%. Apunta a menos de 3 segundos en mobile."
}
```

#### Tasa de Error

```json
{
  "codigo": "tasa_error",
  "nombre": "Tasa de Error",
  "tipo_metrica": "experiencia",
  "unidad_medida": "porcentaje",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 3,
    "alerta_sobre": 1
  },
  "descripcion": "Porcentaje de solicitudes que resultan en error",
  "formula": "(Requests con Error / Total Requests) × 100",
  "ejemplo_valor": 0.5,
  "ejemplo_formato": "0.500%",
  "interpretacion": {
    "excelente": "< 0.1%",
    "bueno": "0.1-0.5%",
    "aceptable": "0.5-1%",
    "critico": "> 1%"
  },
  "ayuda_contextual": "La tasa de error debe ser lo más baja posible. Un 1% significa que 1 de cada 100 usuarios experimenta un error."
}
```

### 3.3 Métricas de Producto

#### DAU (Daily Active Users)

```json
{
  "codigo": "dau",
  "nombre": "DAU (Usuarios Activos Diarios)",
  "tipo_metrica": "producto",
  "unidad_medida": "numero",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": null,
    "decimales": 0
  },
  "descripcion": "Número de usuarios únicos que usan el producto en un día",
  "ejemplo_valor": 15000,
  "ejemplo_formato": "15,000",
  "mejores_practicas": [
    "Definir claramente qué constituye 'activo'",
    "Contar usuarios únicos (no sesiones)",
    "Medir consistentemente"
  ],
  "ayuda_contextual": "DAU mide el engagement diario. Úsalo junto con MAU para calcular la 'stickiness' (DAU/MAU)."
}
```

#### Tasa de Retención

```json
{
  "codigo": "retencion",
  "nombre": "Tasa de Retención",
  "tipo_metrica": "producto",
  "unidad_medida": "porcentaje",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 2
  },
  "descripcion": "Porcentaje de usuarios que regresan después de cierto periodo",
  "formula": "(Usuarios que Regresan / Usuarios Iniciales) × 100",
  "ejemplo_valor": 65.5,
  "ejemplo_formato": "65.50%",
  "interpretacion": {
    "excelente": "> 70%",
    "bueno": "60-70%",
    "aceptable": "50-60%",
    "mejorar": "< 50%"
  },
  "mejores_practicas": [
    "Medir retención de cohortes",
    "Definir periodo (día 1, día 7, día 30)",
    "Segmentar por fuente de adquisición"
  ],
  "ayuda_contextual": "La retención es más importante que la adquisición. Un 65% de retención significa que 65 de cada 100 usuarios regresan."
}
```

#### Feature Adoption Rate

```json
{
  "codigo": "feature_adoption",
  "nombre": "Tasa de Adopción de Funcionalidad",
  "tipo_metrica": "producto",
  "unidad_medida": "porcentaje",
  "validacion": {
    "valor_minimo": 0,
    "valor_maximo": 100,
    "decimales": 2
  },
  "descripcion": "Porcentaje de usuarios que usan una nueva funcionalidad",
  "formula": "(Usuarios que Usaron Feature / Total Usuarios Activos) × 100",
  "ejemplo_valor": 35.0,
  "ejemplo_formato": "35.00%",
  "interpretacion": {
    "excelente": "> 50%",
    "bueno": "30-50%",
    "aceptable": "15-30%",
    "mejorar": "< 15%"
  },
  "ayuda_contextual": "Mide qué tan bien adoptan los usuarios las nuevas funcionalidades. Importante para validar el roadmap de producto."
}
```

## 4. Sistema de Validación

### 4.1 Validación en Tiempo Real

Cuando un usuario ingresa un valor, el sistema debe:

1. **Verificar rango**: El valor está dentro del rango permitido
2. **Verificar formato**: El valor tiene los decimales correctos
3. **Mostrar ayuda**: Tooltip con información contextual
4. **Alertar**: Si el valor está fuera de benchmarks

### 4.2 Ejemplo de Validación

```typescript
interface MetricValidation {
  tipo_unidad: string;
  valor_minimo: number | null;
  valor_maximo: number | null;
  decimales: number;
  alerta_sobre?: number;
  alerta_bajo?: number;
}

function validarValorMetrica(
  valor: number,
  validacion: MetricValidation
): ValidationResult {
  const errors: string[] = [];
  const warnings: string[] = [];

  // Validar rango
  if (validacion.valor_minimo !== null && valor < validacion.valor_minimo) {
    errors.push(`El valor debe ser mayor o igual a ${validacion.valor_minimo}`);
  }

  if (validacion.valor_maximo !== null && valor > validacion.valor_maximo) {
    errors.push(`El valor debe ser menor o igual a ${validacion.valor_maximo}`);
  }

  // Validar decimales
  const decimalesActuales = (valor.toString().split('.')[1] || '').length;
  if (decimalesActuales > validacion.decimales) {
    errors.push(`Máximo ${validacion.decimales} decimales permitidos`);
  }

  // Alertas
  if (validacion.alerta_sobre && valor > validacion.alerta_sobre) {
    warnings.push(`Valor alto: considera revisar. Benchmark: < ${validacion.alerta_sobre}`);
  }

  if (validacion.alerta_bajo && valor < validacion.alerta_bajo) {
    warnings.push(`Valor bajo: considera revisar. Benchmark: > ${validacion.alerta_bajo}`);
  }

  return {
    valido: errors.length === 0,
    errores: errors,
    advertencias: warnings
  };
}
```

## 5. UI/UX de Ayuda Contextual

### 5.1 Tooltip al Seleccionar Métrica

```
┌─────────────────────────────────────────────────┐
│ Métrica: [NPS (Net Promoter Score) ▼]    ℹ️    │
├─────────────────────────────────────────────────┤
│                                                 │
│ 💡 Ayuda: NPS (Net Promoter Score)             │
│                                                 │
│ Rango: -100 a +100                              │
│ Tipo: Score (número entero)                    │
│                                                 │
│ Descripción:                                    │
│ Mide la lealtad del cliente y probabilidad     │
│ de recomendación.                               │
│                                                 │
│ Interpretación:                                 │
│ • Excelente: > 70                               │
│ • Bueno: 50-70                                  │
│ • Aceptable: 30-50                              │
│ • Mejorar: < 30                                 │
│                                                 │
│ Ejemplo: 42 (significa 42% más promotores       │
│ que detractores)                                │
│                                                 │
│ [Ver más información]                           │
└─────────────────────────────────────────────────┘
```

### 5.2 Validación en Input

```
┌─────────────────────────────────────────────────┐
│ NPS (Net Promoter Score)                        │
├─────────────────────────────────────────────────┤
│                                                 │
│ Valor: [  42  ]  ✅                             │
│        ├────────┬────────┤                      │
│       -100      0       100                     │
│                                                 │
│ ✅ Valor válido                                 │
│ 💡 Bueno: Tu NPS está en rango aceptable        │
│                                                 │
│ Benchmark tecnología: 40-60                     │
└─────────────────────────────────────────────────┘
```

### 5.3 Error de Validación

```
┌─────────────────────────────────────────────────┐
│ Tasa de Conversión                              │
├─────────────────────────────────────────────────┤
│                                                 │
│ Valor: [ 150 ]  ❌                              │
│                                                 │
│ ❌ Error: El valor debe estar entre 0 y 100    │
│                                                 │
│ 💡 Consejo: La tasa de conversión es un        │
│ porcentaje. Ejemplo: 3.5 significa 3.5%        │
└─────────────────────────────────────────────────┘
```

## 6. Cómo Agregar Nuevas Métricas

### 6.1 En la Base de Datos

```sql
-- Insertar nueva métrica al catálogo
INSERT INTO catalogo_metricas (
    codigo,
    nombre,
    tipo_metrica_id,
    unidad_medida,
    validacion,
    descripcion,
    formula,
    ejemplo_valor,
    ayuda_contextual,
    benchmarks
) VALUES (
    'time_on_task',
    'Time on Task (Tiempo en Tarea)',
    (SELECT id FROM tipos_metrica WHERE codigo = 'experiencia'),
    'tiempo_segundos',
    '{
        "valor_minimo": 0,
        "valor_maximo": 600,
        "decimales": 2,
        "alerta_sobre": 300
    }'::jsonb,
    'Tiempo promedio que tarda un usuario en completar una tarea específica',
    'Suma de tiempos / Número de tareas completadas',
    120.5,
    'El Time on Task mide la eficiencia. Menor es mejor. Un valor de 120s significa que en promedio los usuarios tardan 2 minutos en completar la tarea.',
    '{
        "excelente": "< 60s",
        "bueno": "60-180s",
        "mejorar": "> 180s"
    }'::jsonb
);
```

### 6.2 En el Frontend

```typescript
// Agregar al catálogo de métricas
const METRICAS_CATALOGO = [
  {
    codigo: 'time_on_task',
    nombre: 'Time on Task',
    tipo: 'experiencia',
    unidad: 'tiempo_segundos',
    validacion: {
      min: 0,
      max: 600,
      decimales: 2,
      alertaSobre: 300
    },
    descripcion: 'Tiempo promedio que tarda un usuario en completar una tarea',
    ayuda: 'Menor es mejor. Valores sobre 300s (5 min) pueden indicar problemas de UX.',
    ejemplo: '120.5s significa 2 minutos promedio'
  }
  // ... más métricas
];
```

## 7. API de Catálogo de Métricas

### 7.1 Obtener Catálogo Completo

```http
GET /api/catalogo-metricas
```

**Response**:
```json
{
  "success": true,
  "data": [
    {
      "codigo": "nps",
      "nombre": "NPS (Net Promoter Score)",
      "tipo_metrica": "experiencia",
      "unidad_medida": "score_nps",
      "validacion": {
        "valor_minimo": -100,
        "valor_maximo": 100,
        "decimales": 0
      },
      "descripcion": "...",
      "ayuda_contextual": "...",
      "benchmarks": {...}
    }
  ]
}
```

### 7.2 Obtener Información de una Métrica

```http
GET /api/catalogo-metricas/:codigo
```

**Response**: Información completa de la métrica con ejemplos y validaciones.

### 7.3 Validar Valor

```http
POST /api/catalogo-metricas/:codigo/validar
```

**Request**:
```json
{
  "valor": 42
}
```

**Response**:
```json
{
  "success": true,
  "valido": true,
  "valor_formateado": "42",
  "interpretacion": "Bueno",
  "advertencias": [],
  "en_rango_benchmark": true
}
```

## 8. Tabla de Referencia Rápida

| Métrica | Unidad | Rango | Ejemplo | Interpretación |
|---------|--------|-------|---------|----------------|
| NPS | score_nps | -100 a 100 | 42 | > 50 es excelente |
| CSAT | score_csat | 0 a 100 | 85.5 | > 85 es excelente |
| Tasa Conversión | porcentaje | 0 a 100 | 3.5% | 3-5% es bueno |
| CAC | dinero | 0 a ∞ | $45.50 | Depende del LTV |
| Churn Rate | porcentaje | 0 a 100 | 3.2% | < 5% es aceptable |
| Tiempo Carga | tiempo_segundos | 0 a 60 | 2.5s | < 3s es bueno |
| DAU | numero | 0 a ∞ | 15,000 | Depende del producto |
| Retención | porcentaje | 0 a 100 | 65% | > 60% es bueno |

## 9. Mejores Prácticas

1. **Siempre incluir contexto**: No solo el número, sino qué significa
2. **Definir periodo de medición**: Diario, semanal, mensual
3. **Segmentar cuando sea relevante**: Por canal, por producto, por región
4. **Documentar cambios**: Si cambia la metodología de cálculo
5. **Comparar con benchmarks**: Para dar contexto al valor
6. **Validar antes de guardar**: Evitar datos incorrectos
7. **Mostrar tendencia**: No solo el valor actual sino el cambio
8. **Explicar anomalías**: Si hay un valor muy fuera de rango

## 10. Recursos Adicionales

- **Templates de métricas**: Plantillas para definir nuevas métricas
- **Calculadoras**: Herramientas para calcular métricas complejas
- **Benchmarks por industria**: Referencias de la industria
- **Glosario**: Definiciones de términos comunes
