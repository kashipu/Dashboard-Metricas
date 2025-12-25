# Documentación de API REST

## 1. Información General

**Base URL**: `http://localhost:3000/api`
**Versión**: 1.0.0
**Autenticación**: JWT Bearer Token
**Formato de respuesta**: JSON

## 2. Autenticación

### 2.1 Login

```http
POST /api/auth/login
```

**Request Body**:
```json
{
  "email": "usuario@example.com",
  "password": "password123"
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Login exitoso",
  "data": {
    "user": {
      "id": 1,
      "email": "usuario@example.com",
      "nombre": "Juan Pérez",
      "rol": "admin"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

### 2.2 Registro

```http
POST /api/auth/register
```

**Request Body**:
```json
{
  "email": "usuario@example.com",
  "nombre": "Juan",
  "apellido": "Pérez",
  "password": "password123"
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Usuario registrado exitosamente",
  "data": {
    "user": {
      "id": 1,
      "email": "usuario@example.com",
      "nombre": "Juan Pérez"
    }
  }
}
```

### 2.3 Uso del Token

Incluir el token en el header de todas las peticiones protegidas:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## 3. Productos

### 3.1 Listar Productos

```http
GET /api/productos
```

**Query Parameters**:
- `activo` (boolean, opcional): Filtrar por estado activo
- `search` (string, opcional): Búsqueda por nombre o código

**Ejemplos**:
```http
GET /api/productos
GET /api/productos?activo=true
GET /api/productos?search=ecommerce
```

**Response** (200):
```json
{
  "success": true,
  "message": "Productos obtenidos exitosamente",
  "data": [
    {
      "id": 1,
      "codigo": "ecommerce-web",
      "nombre": "E-commerce Web",
      "descripcion": "Tienda en línea principal",
      "icono_url": "https://example.com/icon.png",
      "color_hex": "#3B82F6",
      "activo": true,
      "metadata": {
        "departamento": "Ventas",
        "responsable": "María González"
      },
      "created_at": "2025-01-01T00:00:00.000Z",
      "updated_at": "2025-01-15T10:30:00.000Z"
    }
  ]
}
```

### 3.2 Obtener Producto por ID

```http
GET /api/productos/:id
```

**Response** (200):
```json
{
  "success": true,
  "data": {
    "id": 1,
    "codigo": "ecommerce-web",
    "nombre": "E-commerce Web",
    "descripcion": "Tienda en línea principal",
    "icono_url": "https://example.com/icon.png",
    "color_hex": "#3B82F6",
    "activo": true,
    "metadata": {},
    "created_at": "2025-01-01T00:00:00.000Z",
    "updated_at": "2025-01-15T10:30:00.000Z"
  }
}
```

### 3.3 Obtener Producto Completo (con flujos y métricas)

```http
GET /api/productos/:id/complete
```

**Response** (200):
```json
{
  "success": true,
  "data": {
    "id": 1,
    "codigo": "ecommerce-web",
    "nombre": "E-commerce Web",
    "descripcion": "Tienda en línea principal",
    "flujos": [
      {
        "id": 1,
        "nombre": "Conversión",
        "descripcion": "Métricas de conversión de ventas",
        "orden": 1,
        "metricas": [
          {
            "id": 1,
            "codigo": "tasa_conversion",
            "nombre": "Tasa de Conversión",
            "tipo_metrica": {
              "id": 1,
              "codigo": "negocio",
              "nombre": "Negocio",
              "color_hex": "#10B981"
            },
            "unidad_medida": "porcentaje",
            "target": 3.5,
            "orden": 1,
            "valores_mensuales": [
              {
                "id": 1,
                "periodo": "2025-03-01T00:00:00.000Z",
                "valor": "3.8",
                "observaciones": "Incremento por campaña de marketing"
              },
              {
                "id": 2,
                "periodo": "2025-02-01T00:00:00.000Z",
                "valor": "3.5",
                "observaciones": null
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### 3.4 Crear Producto

```http
POST /api/productos
```

**Request Body**:
```json
{
  "codigo": "app-mobile",
  "nombre": "App Móvil",
  "descripcion": "Aplicación móvil iOS y Android",
  "icono_url": "https://example.com/mobile-icon.png",
  "color_hex": "#F59E0B",
  "metadata": {
    "departamento": "Producto",
    "tecnologias": ["React Native", "Firebase"]
  }
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Producto creado exitosamente",
  "data": {
    "id": 2,
    "codigo": "app-mobile",
    "nombre": "App Móvil",
    "descripcion": "Aplicación móvil iOS y Android",
    "icono_url": "https://example.com/mobile-icon.png",
    "color_hex": "#F59E0B",
    "activo": true,
    "metadata": {
      "departamento": "Producto",
      "tecnologias": ["React Native", "Firebase"]
    },
    "created_at": "2025-03-15T14:30:00.000Z",
    "updated_at": "2025-03-15T14:30:00.000Z"
  }
}
```

**Errores**:
- `400`: Validación fallida
- `409`: Ya existe un producto con ese código

### 3.5 Actualizar Producto

```http
PUT /api/productos/:id
```

**Request Body** (campos opcionales):
```json
{
  "nombre": "E-commerce Web 2.0",
  "descripcion": "Nueva versión de la tienda",
  "metadata": {
    "version": "2.0"
  }
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Producto actualizado exitosamente",
  "data": {
    "id": 1,
    "codigo": "ecommerce-web",
    "nombre": "E-commerce Web 2.0",
    "descripcion": "Nueva versión de la tienda",
    "updated_at": "2025-03-16T09:15:00.000Z"
  }
}
```

### 3.6 Eliminar Producto (Soft Delete)

```http
DELETE /api/productos/:id
```

**Response** (204):
```json
{
  "success": true,
  "message": "Producto eliminado exitosamente"
}
```

## 4. Flujos

### 4.1 Listar Flujos de un Producto

```http
GET /api/flujos?producto_id=:producto_id
```

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "producto_id": 1,
      "nombre": "Adquisición",
      "descripcion": "Métricas de adquisición de usuarios",
      "orden": 1,
      "activo": true
    },
    {
      "id": 2,
      "producto_id": 1,
      "nombre": "Conversión",
      "descripcion": "Métricas de conversión",
      "orden": 2,
      "activo": true
    }
  ]
}
```

### 4.2 Crear Flujo

```http
POST /api/flujos
```

**Request Body**:
```json
{
  "producto_id": 1,
  "nombre": "Retención",
  "descripcion": "Métricas de retención de usuarios",
  "orden": 3
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Flujo creado exitosamente",
  "data": {
    "id": 3,
    "producto_id": 1,
    "nombre": "Retención",
    "descripcion": "Métricas de retención de usuarios",
    "orden": 3,
    "activo": true
  }
}
```

### 4.3 Actualizar Flujo

```http
PUT /api/flujos/:id
```

**Request Body**:
```json
{
  "nombre": "Retención y Engagement",
  "orden": 4
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Flujo actualizado exitosamente",
  "data": {
    "id": 3,
    "nombre": "Retención y Engagement",
    "orden": 4
  }
}
```

### 4.4 Eliminar Flujo

```http
DELETE /api/flujos/:id
```

**Response** (204):
```json
{
  "success": true,
  "message": "Flujo eliminado exitosamente"
}
```

## 5. Métricas

### 5.1 Listar Métricas de un Flujo

```http
GET /api/metricas?flujo_id=:flujo_id
```

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "flujo_id": 1,
      "codigo": "tasa_conversion",
      "nombre": "Tasa de Conversión",
      "descripcion": "Porcentaje de usuarios que completan una compra",
      "tipo_metrica_id": 1,
      "tipo_metrica": {
        "id": 1,
        "codigo": "negocio",
        "nombre": "Negocio",
        "color_hex": "#10B981"
      },
      "unidad_medida": "porcentaje",
      "target": 3.5,
      "umbral_minimo": 2.0,
      "umbral_maximo": 5.0,
      "orden": 1,
      "activo": true
    }
  ]
}
```

### 5.2 Obtener Métrica por ID

```http
GET /api/metricas/:id
```

**Response** (200):
```json
{
  "success": true,
  "data": {
    "id": 1,
    "codigo": "tasa_conversion",
    "nombre": "Tasa de Conversión",
    "tipo_metrica": {
      "codigo": "negocio",
      "nombre": "Negocio"
    },
    "unidad_medida": "porcentaje",
    "target": 3.5
  }
}
```

### 5.3 Crear Métrica

```http
POST /api/metricas
```

**Request Body**:
```json
{
  "flujo_id": 1,
  "tipo_metrica_id": 1,
  "codigo": "ticket_promedio",
  "nombre": "Ticket Promedio",
  "descripcion": "Valor promedio de cada compra",
  "unidad_medida": "dinero",
  "target": 85.00,
  "umbral_minimo": 70.00,
  "umbral_maximo": 150.00,
  "orden": 2
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Métrica creada exitosamente",
  "data": {
    "id": 2,
    "flujo_id": 1,
    "codigo": "ticket_promedio",
    "nombre": "Ticket Promedio",
    "unidad_medida": "dinero",
    "target": 85.00
  }
}
```

### 5.4 Actualizar Métrica

```http
PUT /api/metricas/:id
```

**Request Body**:
```json
{
  "target": 90.00,
  "descripcion": "Valor promedio de compra actualizado"
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Métrica actualizada exitosamente",
  "data": {
    "id": 2,
    "target": 90.00,
    "descripcion": "Valor promedio de compra actualizado"
  }
}
```

### 5.5 Eliminar Métrica

```http
DELETE /api/metricas/:id
```

**Response** (204):
```json
{
  "success": true,
  "message": "Métrica eliminada exitosamente"
}
```

## 6. Valores Mensuales

### 6.1 Listar Valores de una Métrica

```http
GET /api/valores?metrica_id=:metrica_id
```

**Query Parameters**:
- `metrica_id` (number, requerido): ID de la métrica
- `start_date` (string, opcional): Fecha inicial (YYYY-MM-DD)
- `end_date` (string, opcional): Fecha final (YYYY-MM-DD)
- `limit` (number, opcional): Límite de resultados (default: 12)

**Ejemplos**:
```http
GET /api/valores?metrica_id=1
GET /api/valores?metrica_id=1&start_date=2025-01-01&end_date=2025-12-31
GET /api/valores?metrica_id=1&limit=6
```

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "metrica_id": 1,
      "periodo": "2025-03-01T00:00:00.000Z",
      "valor": "3.8",
      "observaciones": "Incremento por campaña de marketing",
      "fuente_datos": "Google Analytics",
      "created_at": "2025-03-05T10:00:00.000Z"
    },
    {
      "id": 2,
      "metrica_id": 1,
      "periodo": "2025-02-01T00:00:00.000Z",
      "valor": "3.5",
      "observaciones": null,
      "fuente_datos": "Google Analytics",
      "created_at": "2025-02-05T10:00:00.000Z"
    }
  ]
}
```

### 6.2 Crear Valor Mensual

```http
POST /api/valores
```

**Request Body**:
```json
{
  "metrica_id": 1,
  "periodo": "2025-04-01",
  "valor": 4.2,
  "observaciones": "Excelente mes, nueva campaña de influencers",
  "fuente_datos": "Google Analytics"
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Valor mensual creado exitosamente",
  "data": {
    "id": 3,
    "metrica_id": 1,
    "periodo": "2025-04-01T00:00:00.000Z",
    "valor": "4.2",
    "observaciones": "Excelente mes, nueva campaña de influencers",
    "fuente_datos": "Google Analytics"
  }
}
```

**Errores**:
- `400`: Validación fallida (formato de fecha, valor negativo, etc.)
- `409`: Ya existe un valor para ese periodo

### 6.3 Actualizar Valor Mensual

```http
PUT /api/valores/:id
```

**Request Body**:
```json
{
  "valor": 4.3,
  "observaciones": "Valor corregido después de validación"
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Valor mensual actualizado exitosamente",
  "data": {
    "id": 3,
    "valor": "4.3",
    "observaciones": "Valor corregido después de validación",
    "updated_at": "2025-04-06T15:30:00.000Z"
  }
}
```

### 6.4 Eliminar Valor Mensual

```http
DELETE /api/valores/:id
```

**Response** (204):
```json
{
  "success": true,
  "message": "Valor mensual eliminado exitosamente"
}
```

### 6.5 Importación Masiva de Valores

```http
POST /api/valores/bulk
```

**Request Body**:
```json
{
  "metrica_id": 1,
  "valores": [
    {
      "periodo": "2025-01-01",
      "valor": 3.2
    },
    {
      "periodo": "2025-02-01",
      "valor": 3.5
    },
    {
      "periodo": "2025-03-01",
      "valor": 3.8
    }
  ]
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "3 valores importados exitosamente",
  "data": {
    "imported": 3,
    "errors": 0
  }
}
```

## 7. Exportación para BI

### 7.1 Exportar Producto a JSON

```http
GET /api/export/:product_code/json
```

**Query Parameters**:
- `start_date` (string, opcional): Fecha inicial (YYYY-MM-DD)
- `end_date` (string, opcional): Fecha final (YYYY-MM-DD)

**Ejemplo**:
```http
GET /api/export/ecommerce-web/json?start_date=2025-01-01&end_date=2025-12-31
```

**Response** (200):
```json
{
  "producto": {
    "codigo": "ecommerce-web",
    "nombre": "E-commerce Web"
  },
  "flujos": [
    {
      "nombre": "Conversión",
      "metricas": [
        {
          "codigo": "tasa_conversion",
          "nombre": "Tasa de Conversión",
          "tipo": "Negocio",
          "unidad": "porcentaje",
          "valores": [
            {
              "periodo": "2025-03-01T00:00:00.000Z",
              "valor": 3.8,
              "observaciones": "Incremento por campaña"
            },
            {
              "periodo": "2025-02-01T00:00:00.000Z",
              "valor": 3.5,
              "observaciones": null
            }
          ]
        }
      ]
    }
  ],
  "metadata": {
    "exportado_en": "2025-04-01T10:30:00.000Z",
    "total_flujos": 1,
    "total_metricas": 1
  }
}
```

**Uso en Looker/Power BI**:
Este formato JSON puede ser consumido directamente por conectores REST de:
- **Looker**: REST API Data Source
- **Power BI**: Web connector
- **Tableau**: Web Data Connector
- **Google Data Studio**: Community Connector

### 7.2 Exportar Producto a CSV

```http
GET /api/export/:product_code/csv
```

**Response** (200):
```csv
producto_codigo,producto_nombre,flujo_nombre,metrica_codigo,metrica_nombre,tipo_metrica,unidad_medida,periodo,valor,observaciones
ecommerce-web,E-commerce Web,Conversión,tasa_conversion,Tasa de Conversión,Negocio,porcentaje,2025-03-01,3.8,Incremento por campaña
ecommerce-web,E-commerce Web,Conversión,tasa_conversion,Tasa de Conversión,Negocio,porcentaje,2025-02-01,3.5,
ecommerce-web,E-commerce Web,Conversión,ticket_promedio,Ticket Promedio,Negocio,dinero,2025-03-01,87.5,
```

**Headers**:
```http
Content-Type: text/csv
Content-Disposition: attachment; filename="ecommerce-web-metrics.csv"
```

### 7.3 Exportar Todos los Productos

```http
GET /api/export/all/json
```

**Query Parameters**:
- `start_date` (string, opcional): Fecha inicial
- `end_date` (string, opcional): Fecha final

**Response** (200):
```json
[
  {
    "producto": {
      "codigo": "ecommerce-web",
      "nombre": "E-commerce Web"
    },
    "flujos": [...],
    "metadata": {...}
  },
  {
    "producto": {
      "codigo": "app-mobile",
      "nombre": "App Móvil"
    },
    "flujos": [...],
    "metadata": {...}
  }
]
```

## 8. Tipos de Métrica

### 8.1 Listar Tipos de Métrica

```http
GET /api/tipos-metrica
```

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "codigo": "negocio",
      "nombre": "Negocio",
      "descripcion": "Métricas relacionadas con resultados de negocio",
      "color_hex": "#10B981",
      "icono": "currency-dollar"
    },
    {
      "id": 2,
      "codigo": "experiencia",
      "nombre": "Experiencia",
      "descripcion": "Métricas de experiencia de usuario",
      "color_hex": "#F59E0B",
      "icono": "user-group"
    },
    {
      "id": 3,
      "codigo": "producto",
      "nombre": "Producto",
      "descripcion": "Métricas de uso del producto",
      "color_hex": "#3B82F6",
      "icono": "chart-bar"
    }
  ]
}
```

## 9. Estadísticas y Agregaciones

### 9.1 Resumen de Dashboard

```http
GET /api/dashboard/summary
```

**Response** (200):
```json
{
  "success": true,
  "data": {
    "total_productos": 5,
    "total_metricas": 47,
    "metricas_cumpliendo_target": 32,
    "porcentaje_cumplimiento": 68.09,
    "ultimo_mes_actualizado": "2025-03-01"
  }
}
```

### 9.2 Comparación de Métricas

```http
GET /api/metricas/:id/comparison
```

**Query Parameters**:
- `periods` (number, opcional): Número de periodos a comparar (default: 12)

**Response** (200):
```json
{
  "success": true,
  "data": {
    "metrica": {
      "id": 1,
      "nombre": "Tasa de Conversión",
      "target": 3.5
    },
    "comparacion": {
      "valor_actual": 3.8,
      "valor_anterior": 3.5,
      "cambio_absoluto": 0.3,
      "cambio_porcentual": 8.57,
      "tendencia": "positiva",
      "promedio_12_meses": 3.45,
      "maximo_12_meses": 4.2,
      "minimo_12_meses": 2.9
    }
  }
}
```

### 9.3 Métricas por Tipo

```http
GET /api/dashboard/metrics-by-type
```

**Response** (200):
```json
{
  "success": true,
  "data": {
    "negocio": {
      "total": 15,
      "cumpliendo_target": 10,
      "porcentaje": 66.67
    },
    "experiencia": {
      "total": 18,
      "cumpliendo_target": 14,
      "porcentaje": 77.78
    },
    "producto": {
      "total": 14,
      "cumpliendo_target": 8,
      "porcentaje": 57.14
    }
  }
}
```

## 10. Health Check

### 10.1 Estado del Servicio

```http
GET /health
```

**Response** (200):
```json
{
  "status": "ok",
  "timestamp": "2025-04-01T10:30:00.000Z",
  "uptime": 3600,
  "database": "connected"
}
```

## 11. Códigos de Estado HTTP

| Código | Significado | Uso |
|--------|-------------|-----|
| 200 | OK | Petición exitosa (GET, PUT) |
| 201 | Created | Recurso creado exitosamente (POST) |
| 204 | No Content | Eliminación exitosa (DELETE) |
| 400 | Bad Request | Error de validación |
| 401 | Unauthorized | Token inválido o no proporcionado |
| 403 | Forbidden | Sin permisos |
| 404 | Not Found | Recurso no encontrado |
| 409 | Conflict | Conflicto (ej: código duplicado) |
| 422 | Unprocessable Entity | Error de validación semántica |
| 429 | Too Many Requests | Rate limit excedido |
| 500 | Internal Server Error | Error del servidor |

## 12. Estructura de Respuestas

### 12.1 Respuesta Exitosa

```json
{
  "success": true,
  "message": "Descripción de la operación",
  "data": {
    // Datos de respuesta
  }
}
```

### 12.2 Respuesta de Error

```json
{
  "success": false,
  "message": "Descripción del error",
  "errors": [
    {
      "field": "codigo",
      "message": "El código solo puede contener letras minúsculas"
    }
  ]
}
```

## 13. Rate Limiting

- **Endpoints generales**: 100 requests / 15 minutos
- **Endpoints de exportación**: 10 requests / hora
- **Autenticación**: 5 requests / 15 minutos

**Headers de respuesta**:
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1677649200
```

## 14. Paginación

Para endpoints que retornan listas largas:

```http
GET /api/productos?page=1&limit=20
```

**Response**:
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

## 15. Filtros y Ordenamiento

### Ordenamiento

```http
GET /api/metricas?sort_by=nombre&order=asc
```

**Opciones**:
- `sort_by`: nombre, created_at, updated_at, orden
- `order`: asc, desc

### Filtros Avanzados

```http
GET /api/valores?metrica_id=1&start_date=2025-01-01&end_date=2025-12-31&order=desc
```

## 16. Webhooks (Opcional - Futuro)

### Configurar Webhook

```http
POST /api/webhooks
```

**Request Body**:
```json
{
  "url": "https://your-app.com/webhook",
  "events": ["metric.created", "metric.updated", "value.created"],
  "secret": "your-webhook-secret"
}
```

**Eventos Disponibles**:
- `metric.created`
- `metric.updated`
- `metric.deleted`
- `value.created`
- `value.updated`
- `product.created`

## 17. Ejemplos de Uso con cURL

### Crear Producto

```bash
curl -X POST http://localhost:3000/api/productos \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "codigo": "app-mobile",
    "nombre": "App Móvil",
    "descripcion": "Aplicación móvil"
  }'
```

### Obtener Métricas

```bash
curl -X GET "http://localhost:3000/api/metricas?flujo_id=1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Exportar a JSON

```bash
curl -X GET "http://localhost:3000/api/export/ecommerce-web/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -o metrics-export.json
```

### Exportar a CSV

```bash
curl -X GET "http://localhost:3000/api/export/ecommerce-web/csv" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -o metrics-export.csv
```

## 18. SDKs y Clientes

### JavaScript/TypeScript

```typescript
import axios from 'axios';

const client = axios.create({
  baseURL: 'http://localhost:3000/api',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  }
});

// Obtener productos
const productos = await client.get('/productos');

// Crear métrica
const metrica = await client.post('/metricas', {
  flujo_id: 1,
  nombre: 'Nueva Métrica',
  // ...
});
```

### Python

```python
import requests

headers = {
    'Authorization': f'Bearer {token}',
    'Content-Type': 'application/json'
}

# Obtener productos
response = requests.get('http://localhost:3000/api/productos', headers=headers)
productos = response.json()

# Exportar a JSON
response = requests.get('http://localhost:3000/api/export/ecommerce-web/json', headers=headers)
metrics = response.json()
```

## 19. Postman Collection

Se puede generar una colección de Postman importando el esquema OpenAPI/Swagger:

```http
GET http://localhost:3000/api-docs/swagger.json
```

## 20. Mejores Prácticas

1. **Siempre usar HTTPS** en producción
2. **Validar tokens** en cada request
3. **Cachear respuestas** cuando sea posible
4. **Respetar rate limits**
5. **Manejar errores** apropiadamente
6. **Usar paginación** para listas largas
7. **Incluir versionado** en la URL si es necesario
8. **Documentar cambios** en la API
9. **Usar ETags** para cache
10. **Implementar retry logic** con exponential backoff
