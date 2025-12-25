# API de Usuarios y Permisos

Complemento a la documentación principal de API (API.md) con endpoints específicos de gestión de usuarios, asignaciones y permisos.

**Base URL**: `http://localhost:3000/api`

## 1. Gestión de Usuarios

### 1.1 Listar Usuarios

```http
GET /api/usuarios
```

**Permisos Requeridos**: `admin`

**Query Parameters**:
- `rol` (string, opcional): Filtrar por rol (`admin`, `responsable`, `viewer`)
- `departamento` (string, opcional): Filtrar por departamento
- `activo` (boolean, opcional): Filtrar por estado activo
- `search` (string, opcional): Búsqueda por nombre o email

**Ejemplos**:
```http
GET /api/usuarios
GET /api/usuarios?rol=responsable
GET /api/usuarios?departamento=Producto&activo=true
GET /api/usuarios?search=maria
```

**Response** (200):
```json
{
  "success": true,
  "message": "Usuarios obtenidos exitosamente",
  "data": [
    {
      "id": 1,
      "email": "maria.gonzalez@empresa.com",
      "nombre": "María",
      "apellido": "González",
      "nombre_completo": "María González",
      "cargo": "Product Manager",
      "departamento": "Producto",
      "avatar_url": "https://example.com/avatar.jpg",
      "rol": "responsable",
      "activo": true,
      "email_verificado": true,
      "created_at": "2025-01-01T00:00:00.000Z",
      "ultimo_acceso": "2025-03-15T10:30:00.000Z",
      "productos_asignados": 3
    }
  ],
  "pagination": {
    "total": 25,
    "page": 1,
    "limit": 20
  }
}
```

### 1.2 Obtener Usuario por ID

```http
GET /api/usuarios/:id
```

**Permisos Requeridos**: `admin` o el mismo usuario

**Response** (200):
```json
{
  "success": true,
  "data": {
    "id": 1,
    "email": "maria.gonzalez@empresa.com",
    "nombre": "María",
    "apellido": "González",
    "cargo": "Product Manager",
    "departamento": "Producto",
    "telefono": "+52 55 1234 5678",
    "avatar_url": "https://example.com/avatar.jpg",
    "rol": "responsable",
    "metadata": {
      "linkedin": "https://linkedin.com/in/maria",
      "notificaciones_email": true,
      "timezone": "America/Mexico_City"
    },
    "activo": true,
    "email_verificado": true,
    "created_at": "2025-01-01T00:00:00.000Z",
    "updated_at": "2025-03-10T15:20:00.000Z",
    "ultimo_acceso": "2025-03-15T10:30:00.000Z"
  }
}
```

### 1.3 Obtener Perfil del Usuario Actual

```http
GET /api/usuarios/me
```

**Permisos Requeridos**: Usuario autenticado

**Response** (200):
```json
{
  "success": true,
  "data": {
    "id": 1,
    "email": "maria.gonzalez@empresa.com",
    "nombre": "María",
    "apellido": "González",
    "nombre_completo": "María González",
    "cargo": "Product Manager",
    "departamento": "Producto",
    "avatar_url": "https://example.com/avatar.jpg",
    "rol": "responsable",
    "productos_asignados": [
      {
        "id": 1,
        "codigo": "ecommerce-web",
        "nombre": "E-commerce Web"
      },
      {
        "id": 2,
        "codigo": "app-mobile",
        "nombre": "App Móvil"
      }
    ],
    "permisos": {
      "puede_crear_productos": false,
      "puede_gestionar_usuarios": false,
      "puede_ver_auditoria": false
    }
  }
}
```

### 1.4 Crear Usuario

```http
POST /api/usuarios
```

**Permisos Requeridos**: `admin`

**Request Body**:
```json
{
  "email": "carlos.ruiz@empresa.com",
  "nombre": "Carlos",
  "apellido": "Ruiz",
  "cargo": "Growth Manager",
  "departamento": "Marketing",
  "telefono": "+52 55 9876 5432",
  "rol": "responsable",
  "password": "SecurePassword123!",
  "metadata": {
    "notificaciones_email": true,
    "timezone": "America/Mexico_City"
  }
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Usuario creado exitosamente",
  "data": {
    "id": 5,
    "email": "carlos.ruiz@empresa.com",
    "nombre": "Carlos",
    "apellido": "Ruiz",
    "nombre_completo": "Carlos Ruiz",
    "cargo": "Growth Manager",
    "departamento": "Marketing",
    "rol": "responsable",
    "activo": true,
    "email_verificado": false,
    "created_at": "2025-03-16T14:30:00.000Z"
  }
}
```

**Errores**:
- `400`: Validación fallida (email inválido, password débil, etc.)
- `409`: Email ya existe

### 1.5 Actualizar Usuario

```http
PUT /api/usuarios/:id
```

**Permisos Requeridos**: `admin` o el mismo usuario (solo puede editar su perfil, no rol)

**Request Body** (todos los campos opcionales):
```json
{
  "nombre": "María Elena",
  "cargo": "Senior Product Manager",
  "telefono": "+52 55 1234 5678",
  "avatar_url": "https://example.com/new-avatar.jpg",
  "metadata": {
    "linkedin": "https://linkedin.com/in/maria-gonzalez",
    "notificaciones_email": false
  }
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Usuario actualizado exitosamente",
  "data": {
    "id": 1,
    "email": "maria.gonzalez@empresa.com",
    "nombre": "María Elena",
    "cargo": "Senior Product Manager",
    "updated_at": "2025-03-16T15:00:00.000Z"
  }
}
```

### 1.6 Cambiar Rol de Usuario

```http
PUT /api/usuarios/:id/rol
```

**Permisos Requeridos**: `admin`

**Request Body**:
```json
{
  "rol": "admin"
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Rol actualizado exitosamente",
  "data": {
    "id": 1,
    "email": "maria.gonzalez@empresa.com",
    "rol": "admin",
    "updated_at": "2025-03-16T15:10:00.000Z"
  }
}
```

**Errores**:
- `400`: Rol inválido
- `403`: Sin permisos para cambiar roles

### 1.7 Desactivar Usuario

```http
DELETE /api/usuarios/:id
```

**Permisos Requeridos**: `admin`

**Response** (200):
```json
{
  "success": true,
  "message": "Usuario desactivado exitosamente",
  "data": {
    "id": 1,
    "activo": false
  }
}
```

**Nota**: Es un soft delete. El usuario queda desactivado pero no se elimina de la base de datos.

## 2. Asignación de Productos a Usuarios

### 2.1 Asignar Responsable a Producto

```http
PUT /api/productos/:id/responsable
```

**Permisos Requeridos**: `admin`

**Request Body**:
```json
{
  "responsable_id": 5
}
```

**Response** (200):
```json
{
  "success": true,
  "message": "Responsable asignado exitosamente",
  "data": {
    "id": 1,
    "codigo": "ecommerce-web",
    "nombre": "E-commerce Web",
    "responsable_id": 5,
    "responsable": {
      "id": 5,
      "nombre": "Carlos",
      "apellido": "Ruiz",
      "email": "carlos.ruiz@empresa.com"
    },
    "updated_at": "2025-03-16T16:00:00.000Z"
  }
}
```

**Notificación Automática**:
- Se envía email al nuevo responsable
- Se notifica al anterior responsable (si había)
- Se registra en auditoría

### 2.2 Obtener Productos de un Usuario

```http
GET /api/usuarios/:id/productos
```

**Permisos Requeridos**: `admin` o el mismo usuario

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "codigo": "ecommerce-web",
      "nombre": "E-commerce Web",
      "descripcion": "Tienda en línea principal",
      "color_hex": "#10B981",
      "icono_url": "https://example.com/ecommerce-icon.png",
      "total_flujos": 4,
      "total_metricas": 12,
      "ultima_actualizacion": "2025-03-15T10:00:00.000Z"
    },
    {
      "id": 2,
      "codigo": "app-mobile",
      "nombre": "App Móvil",
      "descripcion": "Aplicación móvil iOS y Android",
      "color_hex": "#F59E0B",
      "total_flujos": 3,
      "total_metricas": 8,
      "ultima_actualizacion": "2025-03-14T15:30:00.000Z"
    }
  ]
}
```

### 2.3 Agregar Colaborador a Producto

```http
POST /api/productos/:id/colaboradores
```

**Permisos Requeridos**: `admin`

**Request Body**:
```json
{
  "usuario_id": 7,
  "puede_editar": true,
  "puede_ver": true
}
```

**Response** (201):
```json
{
  "success": true,
  "message": "Colaborador agregado exitosamente",
  "data": {
    "id": 10,
    "producto_id": 1,
    "usuario_id": 7,
    "usuario": {
      "nombre": "Ana",
      "apellido": "López",
      "email": "ana.lopez@empresa.com"
    },
    "puede_editar": true,
    "puede_ver": true,
    "asignado_en": "2025-03-16T16:30:00.000Z"
  }
}
```

## 3. "Mis Productos" (Responsable)

### 3.1 Obtener Mis Productos

```http
GET /api/mis-productos
```

**Permisos Requeridos**: Usuario autenticado (rol `responsable`)

**Query Parameters**:
- `incluir_metricas` (boolean, opcional): Incluir métricas en respuesta
- `periodo` (string, opcional): Periodo para últimos valores (formato YYYY-MM)

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "codigo": "ecommerce-web",
      "nombre": "E-commerce Web",
      "descripcion": "Tienda en línea principal",
      "color_hex": "#10B981",
      "icono_url": "https://example.com/icon.png",
      "flujos": [
        {
          "id": 1,
          "nombre": "Conversión",
          "orden": 1,
          "total_metricas": 3,
          "metricas_con_datos_mes_actual": 2,
          "porcentaje_completitud": 66.67
        },
        {
          "id": 2,
          "nombre": "Retención",
          "orden": 2,
          "total_metricas": 2,
          "metricas_con_datos_mes_actual": 2,
          "porcentaje_completitud": 100.0
        }
      ],
      "resumen": {
        "total_metricas": 5,
        "metricas_con_datos": 4,
        "metricas_cumpliendo_target": 3,
        "porcentaje_cumplimiento": 75.0,
        "ultimo_registro": "2025-03-15T10:00:00.000Z"
      }
    }
  ]
}
```

### 3.2 Dashboard de "Mis Productos"

```http
GET /api/mis-productos/dashboard
```

**Permisos Requeridos**: `responsable`

**Response** (200):
```json
{
  "success": true,
  "data": {
    "total_productos": 3,
    "total_metricas": 15,
    "valores_registrados_mes_actual": 12,
    "porcentaje_completitud": 80.0,
    "metricas_cumpliendo_target": 10,
    "porcentaje_cumplimiento": 66.67,
    "productos": [
      {
        "id": 1,
        "nombre": "E-commerce Web",
        "estado": "completo",
        "porcentaje_completitud": 100.0,
        "ultima_actualizacion": "2025-03-15T10:00:00.000Z"
      },
      {
        "id": 2,
        "nombre": "App Móvil",
        "estado": "parcial",
        "porcentaje_completitud": 60.0,
        "ultima_actualizacion": "2025-03-10T14:00:00.000Z"
      }
    ],
    "alertas": [
      {
        "tipo": "valores_pendientes",
        "mensaje": "3 métricas sin valores del mes actual",
        "producto_id": 2
      }
    ]
  }
}
```

## 4. Filtros Mejorados

### 4.1 Listar Productos con Filtros

```http
GET /api/productos
```

**Query Parameters Extendidos**:
- `responsable_id` (number, opcional): Filtrar por responsable
- `departamento` (string, opcional): Filtrar por departamento del responsable
- `activo` (boolean, opcional): Filtrar por estado
- `search` (string, opcional): Búsqueda por nombre o código
- `tiene_metricas` (boolean, opcional): Solo productos con métricas configuradas
- `tiene_valores` (boolean, opcional): Solo productos con valores registrados

**Ejemplos**:
```http
GET /api/productos?responsable_id=5
GET /api/productos?departamento=Marketing
GET /api/productos?search=ecommerce&activo=true
GET /api/productos?tiene_valores=true
```

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "codigo": "ecommerce-web",
      "nombre": "E-commerce Web",
      "descripcion": "Tienda en línea principal",
      "responsable": {
        "id": 5,
        "nombre": "María",
        "apellido": "González",
        "email": "maria.gonzalez@empresa.com",
        "cargo": "Product Manager",
        "departamento": "Producto",
        "avatar_url": "https://example.com/avatar.jpg"
      },
      "color_hex": "#10B981",
      "activo": true,
      "total_flujos": 4,
      "total_metricas": 12,
      "tiene_valores_mes_actual": true
    }
  ]
}
```

### 4.2 Listar Valores con Filtros Mejorados

```http
GET /api/valores
```

**Query Parameters Extendidos**:
- `producto_id` (number, opcional): Filtrar por producto
- `flujo_id` (number, opcional): Filtrar por flujo
- `metrica_id` (number, opcional): Filtrar por métrica específica
- `tipo_metrica` (string, opcional): Filtrar por tipo (negocio/experiencia/producto)
- `periodo_inicio` (string, opcional): Fecha inicial (YYYY-MM-DD)
- `periodo_fin` (string, opcional): Fecha final (YYYY-MM-DD)
- `responsable_id` (number, opcional): Filtrar por responsable del producto
- `created_by` (number, opcional): Filtrar por quién registró el valor

**Ejemplos**:
```http
GET /api/valores?producto_id=1&flujo_id=2
GET /api/valores?tipo_metrica=negocio&periodo_inicio=2025-01-01
GET /api/valores?responsable_id=5&periodo_inicio=2025-03-01
GET /api/valores?created_by=5
```

## 5. Auditoría

### 5.1 Obtener Log de Auditoría

```http
GET /api/auditoria
```

**Permisos Requeridos**: `admin`

**Query Parameters**:
- `usuario_id` (number, opcional): Filtrar por usuario
- `accion` (string, opcional): Tipo de acción (crear, editar, eliminar, asignar)
- `entidad` (string, opcional): Tipo de entidad (producto, metrica, valor)
- `fecha_inicio` (string, opcional): Fecha inicial
- `fecha_fin` (string, opcional): Fecha final

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 150,
      "usuario": {
        "id": 1,
        "nombre": "Admin Sistema",
        "email": "admin@empresa.com"
      },
      "accion": "asignar_responsable",
      "entidad": "producto",
      "entidad_id": 1,
      "datos_anteriores": {
        "responsable_id": 3
      },
      "datos_nuevos": {
        "responsable_id": 5
      },
      "ip_origen": "192.168.1.100",
      "created_at": "2025-03-16T16:00:00.000Z"
    },
    {
      "id": 149,
      "usuario": {
        "id": 5,
        "nombre": "María González",
        "email": "maria.gonzalez@empresa.com"
      },
      "accion": "crear",
      "entidad": "valor",
      "entidad_id": 450,
      "datos_nuevos": {
        "metrica_id": 12,
        "periodo": "2025-03-01",
        "valor": 3.8
      },
      "ip_origen": "192.168.1.105",
      "created_at": "2025-03-15T10:00:00.000Z"
    }
  ],
  "pagination": {
    "total": 500,
    "page": 1,
    "limit": 20
  }
}
```

## 6. Estadísticas por Usuario

### 6.1 Estadísticas del Responsable

```http
GET /api/usuarios/:id/estadisticas
```

**Permisos Requeridos**: `admin` o el mismo usuario

**Response** (200):
```json
{
  "success": true,
  "data": {
    "usuario": {
      "id": 5,
      "nombre": "María González",
      "rol": "responsable"
    },
    "productos_asignados": 3,
    "total_flujos": 10,
    "total_metricas": 35,
    "valores_registrados": {
      "mes_actual": 28,
      "mes_anterior": 32,
      "total": 420
    },
    "cumplimiento_targets": {
      "cumpliendo": 25,
      "total": 35,
      "porcentaje": 71.43
    },
    "actividad_reciente": {
      "ultimos_7_dias": {
        "valores_registrados": 12,
        "metricas_editadas": 3,
        "productos_actualizados": 2
      },
      "ultimos_30_dias": {
        "valores_registrados": 45,
        "metricas_editadas": 8,
        "productos_actualizados": 3
      }
    },
    "productos": [
      {
        "id": 1,
        "nombre": "E-commerce Web",
        "completitud": 100.0,
        "cumplimiento_target": 80.0
      },
      {
        "id": 2,
        "nombre": "App Móvil",
        "completitud": 75.0,
        "cumplimiento_target": 60.0
      }
    ]
  }
}
```

## 7. Notificaciones

### 7.1 Obtener Notificaciones

```http
GET /api/notificaciones
```

**Permisos Requeridos**: Usuario autenticado

**Query Parameters**:
- `leido` (boolean, opcional): Filtrar por estado de lectura
- `tipo` (string, opcional): Tipo de notificación

**Response** (200):
```json
{
  "success": true,
  "data": [
    {
      "id": 25,
      "tipo": "producto_asignado",
      "titulo": "Nuevo producto asignado",
      "mensaje": "Te asignaron como responsable de E-commerce Web",
      "link": "/productos/1",
      "leido": false,
      "created_at": "2025-03-16T16:00:00.000Z"
    },
    {
      "id": 24,
      "tipo": "metrica_sin_cumplir",
      "titulo": "Métrica por debajo de target",
      "mensaje": "La métrica 'Tasa de conversión' está por debajo del target",
      "link": "/productos/1",
      "leido": false,
      "created_at": "2025-03-15T09:00:00.000Z"
    }
  ],
  "no_leidas": 2
}
```

### 7.2 Marcar Notificación como Leída

```http
PUT /api/notificaciones/:id/leer
```

**Response** (200):
```json
{
  "success": true,
  "message": "Notificación marcada como leída"
}
```

## 8. Ejemplos de cURL

### Crear Usuario (Admin)

```bash
curl -X POST http://localhost:3000/api/usuarios \
  -H "Authorization: Bearer YOUR_ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "carlos.ruiz@empresa.com",
    "nombre": "Carlos",
    "apellido": "Ruiz",
    "cargo": "Growth Manager",
    "departamento": "Marketing",
    "rol": "responsable",
    "password": "SecurePass123!"
  }'
```

### Asignar Responsable a Producto

```bash
curl -X PUT http://localhost:3000/api/productos/1/responsable \
  -H "Authorization: Bearer YOUR_ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "responsable_id": 5
  }'
```

### Obtener Mis Productos

```bash
curl -X GET http://localhost:3000/api/mis-productos \
  -H "Authorization: Bearer YOUR_RESPONSABLE_TOKEN"
```

### Registrar Valor Mensual (Responsable)

```bash
curl -X POST http://localhost:3000/api/valores \
  -H "Authorization: Bearer YOUR_RESPONSABLE_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "metrica_id": 12,
    "periodo": "2025-03-01",
    "valor": 3.8,
    "observaciones": "Mes con campaña especial"
  }'
```

### Obtener Productos de un Responsable

```bash
curl -X GET "http://localhost:3000/api/productos?responsable_id=5" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Obtener Estadísticas de Mis Productos

```bash
curl -X GET http://localhost:3000/api/usuarios/me \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 9. Resumen de Roles y Endpoints

| Endpoint | Admin | Responsable | Viewer |
|----------|-------|-------------|--------|
| `GET /usuarios` | ✅ | ❌ | ❌ |
| `POST /usuarios` | ✅ | ❌ | ❌ |
| `PUT /usuarios/:id` | ✅ | ✅ (solo sí mismo) | ✅ (solo sí mismo) |
| `PUT /usuarios/:id/rol` | ✅ | ❌ | ❌ |
| `GET /usuarios/me` | ✅ | ✅ | ✅ |
| `PUT /productos/:id/responsable` | ✅ | ❌ | ❌ |
| `GET /mis-productos` | ❌ | ✅ | ❌ |
| `GET /mis-productos/dashboard` | ❌ | ✅ | ❌ |
| `GET /productos?responsable_id=X` | ✅ | ✅ | ✅ |
| `POST /valores` | ✅ | ✅ (sus productos) | ❌ |
| `GET /auditoria` | ✅ | ❌ | ❌ |

## 10. Validaciones y Restricciones

### Validaciones de Seguridad

1. **Responsable solo puede editar sus productos**:
   - Validación en backend antes de cualquier modificación
   - Query verifica `responsable_id = usuario_actual_id`

2. **Viewer no puede modificar nada**:
   - Todos los endpoints de escritura retornan `403`
   - Solo acceso GET permitido

3. **Admin puede todo**:
   - Bypass de validaciones de ownership
   - Acceso completo a auditoría

### Rate Limiting por Rol

```javascript
// Configuración de rate limiting
{
  "admin": "300 requests / 15 min",
  "responsable": "150 requests / 15 min",
  "viewer": "100 requests / 15 min"
}
```

### Respuestas de Error Comunes

**403 - Sin Permisos**:
```json
{
  "success": false,
  "message": "No tienes permisos para realizar esta acción",
  "codigo": "FORBIDDEN"
}
```

**403 - No Es Tu Producto**:
```json
{
  "success": false,
  "message": "No eres responsable de este producto",
  "codigo": "NOT_YOUR_PRODUCT"
}
```

**401 - No Autenticado**:
```json
{
  "success": false,
  "message": "Token no válido o expirado",
  "codigo": "UNAUTHORIZED"
}
```

---

**Ver también**:
- [API.md](./API.md) - Documentación principal de API
- [ROLES_Y_PERMISOS.md](./ROLES_Y_PERMISOS.md) - Detalles de roles y permisos
- [DATABASE.md](./DATABASE.md) - Modelo de datos
