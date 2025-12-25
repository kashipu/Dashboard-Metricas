# Roles y Permisos del Sistema

## 1. Visión General

Sistema de roles y permisos basado en RBAC (Role-Based Access Control) que define qué puede hacer cada tipo de usuario en el sistema de métricas.

## 2. Roles del Sistema

### 2.1 Admin (Administrador)

**Descripción**: Administrador total del sistema con acceso completo.

**Permisos**:
- ✅ Ver todos los productos
- ✅ Crear/editar/eliminar cualquier producto
- ✅ Asignar responsables a productos
- ✅ Ver todas las métricas
- ✅ Crear/editar/eliminar métricas de cualquier producto
- ✅ Registrar/editar valores de cualquier métrica
- ✅ Gestionar usuarios (crear, editar, desactivar)
- ✅ Cambiar roles de usuarios
- ✅ Ver logs y auditoría
- ✅ Configurar tipos de métricas
- ✅ Exportar datos de cualquier producto
- ✅ Acceder a configuración global

**Casos de Uso**:
- Personal de TI
- Gerente de Producto (nivel ejecutivo)
- Data Engineering

### 2.2 Diseñador

**Descripción**: Usuario encargado de diseñar y dar seguimiento a uno o más productos. Puede gestionar completamente sus productos asignados, agregando métricas mes a mes.

**Permisos**:
- ✅ Ver **sus productos y flujos** asignados
- ✅ Editar información de **sus productos**
- ✅ Ver/crear/editar flujos de **sus productos**
- ✅ Ver/crear/editar métricas de **sus productos**
- ✅ Registrar valores mensuales de **sus productos**
- ✅ Editar valores que ellos mismos registraron
- ✅ Ver dashboard de **sus productos** (Vista Completa y Vista Detallada por Flujo)
- ✅ Exportar datos de **sus productos**
- ✅ Ver otros productos (solo lectura)
- ❌ No puede asignar diseñadores
- ❌ No puede gestionar usuarios
- ❌ No puede ver logs de auditoría completos

**Casos de Uso**:
- Diseñadores de Producto
- Product Designers
- UX Designers
- Service Designers
- Responsables de Producto

### 2.3 Viewer (Observador)

**Descripción**: Usuario con acceso de solo lectura. Puede ver dashboards y exportar reportes pero no modificar nada.

**Permisos**:
- ✅ Ver todos los productos
- ✅ Ver todos los dashboards
- ✅ Ver todas las métricas
- ✅ Ver valores históricos
- ✅ Exportar datos en formato lectura
- ❌ No puede crear/editar productos
- ❌ No puede registrar valores
- ❌ No puede modificar nada

**Casos de Uso**:
- Stakeholders
- Ejecutivos (CEO, CFO)
- Data Analysts
- Equipos de reporting

## 3. Matriz de Permisos

| Acción | Admin | Diseñador | Viewer |
|--------|-------|-----------|--------|
| **USUARIOS** |
| Ver usuarios | ✅ | ❌ | ❌ |
| Crear usuarios | ✅ | ❌ | ❌ |
| Editar usuarios | ✅ | ❌ (solo perfil propio) | ❌ (solo perfil propio) |
| Cambiar roles | ✅ | ❌ | ❌ |
| Desactivar usuarios | ✅ | ❌ | ❌ |
| **PRODUCTOS** |
| Ver todos los productos | ✅ | ✅ (lectura) | ✅ |
| Ver solo sus productos | ✅ | ✅ | N/A |
| Crear productos | ✅ | ❌ | ❌ |
| Editar cualquier producto | ✅ | ❌ | ❌ |
| Editar sus productos | ✅ | ✅ | ❌ |
| Eliminar productos | ✅ | ❌ | ❌ |
| Asignar responsables | ✅ | ❌ | ❌ |
| **FLUJOS** |
| Ver flujos | ✅ | ✅ | ✅ |
| Crear flujos (cualquier producto) | ✅ | ❌ | ❌ |
| Crear flujos (sus productos) | ✅ | ✅ | ❌ |
| Editar flujos (cualquier producto) | ✅ | ❌ | ❌ |
| Editar flujos (sus productos) | ✅ | ✅ | ❌ |
| Eliminar flujos | ✅ | ✅ (sus productos) | ❌ |
| **MÉTRICAS** |
| Ver métricas | ✅ | ✅ | ✅ |
| Crear métricas (cualquier producto) | ✅ | ❌ | ❌ |
| Crear métricas (sus productos) | ✅ | ✅ | ❌ |
| Editar métricas (cualquier producto) | ✅ | ❌ | ❌ |
| Editar métricas (sus productos) | ✅ | ✅ | ❌ |
| Eliminar métricas | ✅ | ✅ (sus productos) | ❌ |
| Configurar tipos de métricas | ✅ | ❌ | ❌ |
| **VALORES MENSUALES** |
| Ver valores | ✅ | ✅ | ✅ |
| Registrar valores (cualquier producto) | ✅ | ❌ | ❌ |
| Registrar valores (sus productos) | ✅ | ✅ | ❌ |
| Editar valores (cualquier producto) | ✅ | ❌ | ❌ |
| Editar valores (sus productos) | ✅ | ✅ | ❌ |
| Eliminar valores | ✅ | ✅ (sus registros) | ❌ |
| **EXPORTACIÓN** |
| Exportar datos (cualquier producto) | ✅ | ❌ | ✅ (lectura) |
| Exportar datos (sus productos) | ✅ | ✅ | ✅ |
| **SISTEMA** |
| Ver logs de auditoría | ✅ | ❌ | ❌ |
| Ver configuración global | ✅ | ❌ | ❌ |
| Modificar configuración | ✅ | ❌ | ❌ |

## 4. Asignación de Productos y Flujos

### 4.1 Diseñador Principal

Cada producto (y sus flujos) tiene **un diseñador principal** asignado:

```sql
-- Campo en tabla productos
responsable_id INTEGER REFERENCES usuarios(id) -- FK al diseñador asignado
```

**Beneficios**:
- Claridad de ownership
- Punto de contacto único para el producto
- Responsabilidad clara del diseño y seguimiento de métricas

### 4.2 Colaboradores Adicionales (Futuro)

Para equipos grandes, se puede usar la tabla `producto_usuarios`:

```sql
-- Asignar colaborador adicional
INSERT INTO producto_usuarios (producto_id, usuario_id, puede_editar, puede_ver)
VALUES (1, 5, TRUE, TRUE);
```

**Escenarios**:
- Producto con múltiples PMs
- PM Junior apoyando a PM Senior
- Analista que ayuda con métricas

## 5. Flujo de Asignación

### 5.1 Admin Asigna Diseñador a Producto y Flujos

```
┌──────────┐
│  Admin   │
└────┬─────┘
     │
     │ 1. Accede a "Gestión de Productos"
     ▼
┌─────────────────────────────────┐
│  Lista de Productos             │
│  [Crédito Vehículo] [Editar]    │
└────┬────────────────────────────┘
     │ 2. Click en "Editar"
     ▼
┌─────────────────────────────────┐
│  Editar Producto: Crédito Veh.  │
│                                 │
│  Diseñador: [María G. ▼]       │
│  (dropdown con usuarios diseñadores)
│                                 │
│  [Guardar]                      │
└────┬────────────────────────────┘
     │ 3. Guardar
     ▼
┌─────────────────────────────────┐
│  Producto y flujos asignados    │
│  Email enviado a María          │
└─────────────────────────────────┘
```

### 5.2 Diseñador Gestiona Sus Productos

```
┌──────────┐
│  María   │
│(Diseñador)│
└────┬─────┘
     │
     │ 1. Login al sistema
     ▼
┌─────────────────────────────────┐
│  Mis Productos                  │
│  ┌───────────────────┐          │
│  │ Crédito Vehículo  │          │
│  │ • Colocación      │          │
│  │ • Legalización    │          │
│  │ [Ver Dashboard]   │          │
│  │ [Registrar Valor] │          │
│  └───────────────────┘          │
└────┬────────────────────────────┘
     │ 2. Click "Registrar Valor"
     ▼
┌─────────────────────────────────┐
│  Registrar Valores - Marzo 2025 │
│                                 │
│  Producto: Crédito Vehículo (fijo)
│  Flujo: [Colocación ▼]         │
│  Métrica: [Tasa Aprobación ▼]  │
│  Valor: [78.5]                  │
│                                 │
│  [Guardar]                      │
└─────────────────────────────────┘
```

## 6. Validaciones de Seguridad

### 6.1 Backend (Middleware)

```typescript
// Middleware de autorización
export const authorizeProductAccess = (action: 'read' | 'write') => {
  return async (req: Request, res: Response, next: NextFunction) => {
    const user = req.user; // Del JWT
    const productId = req.params.id;

    // Admin puede todo
    if (user.rol === 'admin') {
      return next();
    }

    // Viewer solo lectura
    if (user.rol === 'viewer') {
      if (action === 'write') {
        return res.status(403).json({
          success: false,
          message: 'No tienes permisos para modificar'
        });
      }
      return next();
    }

    // Diseñador: verificar ownership
    if (user.rol === 'disenador') {
      const producto = await prisma.producto.findUnique({
        where: { id: Number(productId) }
      });

      if (!producto) {
        return res.status(404).json({
          success: false,
          message: 'Producto no encontrado'
        });
      }

      // Verificar si es el diseñador asignado
      if (producto.responsable_id !== user.id) {
        if (action === 'write') {
          return res.status(403).json({
            success: false,
            message: 'No eres el diseñador de este producto'
          });
        }
        // Puede ver (lectura)
        return next();
      }

      return next();
    }

    return res.status(403).json({
      success: false,
      message: 'No tienes permisos'
    });
  };
};
```

### 6.2 Frontend (React)

```typescript
// Hook de permisos
export function usePermissions() {
  const { user } = useAuthStore();

  const can = (action: string, resource: any) => {
    // Admin puede todo
    if (user?.rol === 'admin') {
      return true;
    }

    // Viewer solo lectura
    if (user?.rol === 'viewer') {
      return action === 'read';
    }

    // Diseñador
    if (user?.rol === 'disenador') {
      // Puede leer cualquier cosa
      if (action === 'read') {
        return true;
      }

      // Solo puede escribir en sus productos
      if (action === 'write' || action === 'update') {
        return resource?.responsable_id === user.id;
      }
    }

    return false;
  };

  return { can, isAdmin: user?.rol === 'admin' };
}

// Uso en componente
function ProductCard({ producto }) {
  const { can } = usePermissions();

  return (
    <div>
      <h3>{producto.nombre}</h3>

      {can('write', producto) && (
        <button>Editar</button>
      )}

      {can('read', producto) && (
        <button>Ver Dashboard</button>
      )}
    </div>
  );
}
```

## 7. Rutas Protegidas

### 7.1 Backend

```typescript
// Rutas de productos
router.get('/productos', authenticate, productosController.getAll);
router.get('/productos/:id', authenticate, productosController.getById);
router.post('/productos', authenticate, requireRole('admin'), productosController.create);
router.put('/productos/:id', authenticate, authorizeProductAccess('write'), productosController.update);
router.delete('/productos/:id', authenticate, requireRole('admin'), productosController.delete);

// Rutas de valores mensuales
router.post('/valores', authenticate, authorizeProductAccess('write'), valoresController.create);
router.put('/valores/:id', authenticate, authorizeValueEdit, valoresController.update);

// Rutas de admin
router.get('/usuarios', authenticate, requireRole('admin'), usuariosController.getAll);
router.post('/usuarios', authenticate, requireRole('admin'), usuariosController.create);
router.put('/productos/:id/disenador', authenticate, requireRole('admin'), productosController.assignDisenador);
```

### 7.2 Frontend

```typescript
// app/(dashboard)/layout.tsx
export default function DashboardLayout({ children }) {
  const { user } = useAuthStore();

  if (!user) {
    redirect('/login');
  }

  return (
    <div>
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}

// app/(dashboard)/admin/page.tsx
export default function AdminPage() {
  const { user } = useAuthStore();

  if (user?.rol !== 'admin') {
    redirect('/dashboard');
  }

  return <AdminPanel />;
}

// app/(dashboard)/mis-productos/page.tsx
export default function MisProductosPage() {
  const { user } = useAuthStore();

  if (user?.rol === 'viewer') {
    redirect('/dashboard'); // Viewers no tienen "sus productos"
  }

  // Solo para diseñadores
  return <MisProductosList />;
}
```

## 8. Notificaciones de Asignación

Cuando se asigna un diseñador a un producto:

```typescript
// Servicio de notificaciones
async function notifyProductAssignment(producto: Producto, disenador: Usuario, asignadoPor: Usuario) {
  // Email
  await sendEmail({
    to: disenador.email,
    subject: `Te asignaron el producto: ${producto.nombre}`,
    html: `
      <h2>Nuevo Producto Asignado</h2>
      <p>Hola ${disenador.nombre},</p>
      <p>${asignadoPor.nombre} te ha asignado como diseñador del producto <strong>${producto.nombre}</strong>.</p>
      <p>Ahora puedes:</p>
      <ul>
        <li>Gestionar flujos y métricas del producto</li>
        <li>Registrar valores mensuales</li>
        <li>Ver dashboard de producto completo y detalle de flujos</li>
      </ul>
      <a href="${process.env.APP_URL}/productos/${producto.id}">Ver Producto</a>
    `
  });

  // Notificación in-app (futuro)
  await prisma.notificacion.create({
    data: {
      usuario_id: disenador.id,
      tipo: 'producto_asignado',
      titulo: `Producto asignado: ${producto.nombre}`,
      mensaje: `${asignadoPor.nombre} te asignó como diseñador`,
      link: `/productos/${producto.id}`,
      leido: false
    }
  });
}
```

## 9. Auditoría de Acciones

Todas las acciones importantes quedan registradas:

```typescript
// Tabla de auditoría (opcional pero recomendado)
CREATE TABLE auditoria (
    id SERIAL PRIMARY KEY,
    usuario_id INTEGER REFERENCES usuarios(id),
    accion VARCHAR(100) NOT NULL, -- 'crear', 'editar', 'eliminar', 'asignar'
    entidad VARCHAR(50) NOT NULL, -- 'producto', 'metrica', 'valor'
    entidad_id INTEGER,
    datos_anteriores JSONB,
    datos_nuevos JSONB,
    ip_origen VARCHAR(50),
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

// Registrar en auditoría
async function logAudit(data: AuditLog) {
  await prisma.auditoria.create({
    data: {
      usuario_id: data.usuarioId,
      accion: data.accion,
      entidad: data.entidad,
      entidad_id: data.entidadId,
      datos_anteriores: data.datosAnteriores,
      datos_nuevos: data.datosNuevos,
      ip_origen: data.ip,
      user_agent: data.userAgent
    }
  });
}

// Ejemplo de uso
await logAudit({
  usuarioId: adminUser.id,
  accion: 'asignar_responsable',
  entidad: 'producto',
  entidadId: producto.id,
  datosAnteriores: { responsable_id: null },
  datosNuevos: { responsable_id: maria.id },
  ip: req.ip,
  userAgent: req.headers['user-agent']
});
```

## 10. Casos de Uso Comunes

### 10.1 Nuevo Diseñador se Une al Equipo

```
1. Admin crea usuario con rol 'disenador'
2. Admin asigna productos y flujos al nuevo diseñador
3. Sistema envía email de bienvenida con credenciales
4. Diseñador hace login
5. Ve sus productos y flujos asignados
6. Puede comenzar a registrar métricas mes a mes
```

### 10.2 Cambio de Diseñador

```
1. Admin edita producto
2. Cambia diseñador de María a Carlos
3. Sistema:
   - Envía email a Carlos (nuevo diseñador)
   - Envía email a María (notificación de cambio)
   - Registra en auditoría
4. Carlos ahora puede editar, María pasa a modo lectura
```

### 10.3 Diseñador Registra Métricas Mensuales

```
1. Diseñador login
2. Va a "Mis Productos"
3. Selecciona producto (ej: Crédito Vehículo)
4. Ve vista completa del producto con sus 2 flujos
5. Click "Registrar Valores" en flujo Colocación
6. Completa formulario con valores del mes
7. Sistema valida permisos y rangos
8. Guarda con created_by = disenador_id
9. Actualiza dashboard automáticamente (vista completa + vista detallada)
```

## 11. Mejores Prácticas

### 11.1 Seguridad

- ✅ Siempre validar permisos en backend (no confiar solo en frontend)
- ✅ Usar JWT con expiración corta (7 días máximo)
- ✅ Implementar refresh tokens
- ✅ Hash de passwords con bcrypt (salt rounds >= 10)
- ✅ Rate limiting en endpoints de autenticación
- ✅ Logs de auditoría para acciones críticas

### 11.2 UX

- ✅ Ocultar opciones que el usuario no puede usar
- ✅ Mostrar mensajes claros cuando no hay permisos
- ✅ Diferenciar visualmente el rol (badge en header)
- ✅ Vista "Mis Productos" para diseñadores (con navegación a vista completa y detallada)
- ✅ Vista "Todos los Productos" para admins y viewers

### 11.3 Escalabilidad

- ✅ Cachear permisos del usuario en sesión
- ✅ Índices en campos de permisos (responsable_id que apunta al diseñador, rol)
- ✅ Queries optimizadas con filtros de permisos
- ✅ Considerar Row Level Security (RLS) en PostgreSQL

## 12. Roadmap de Permisos

### Fase 1 (MVP - Actual)
- ✅ 3 roles básicos: admin, disenador, viewer
- ✅ Diseñador por producto (con acceso a todos sus flujos)
- ✅ Validación en backend y frontend
- ✅ Auditoría básica
- ✅ Visualización en dos niveles (producto completo + flujo detallado)

### Fase 2 (Futuro)
- Equipos de múltiples diseñadores
- Permisos granulares por flujo específico
- Permisos granulares por métrica
- Aprobación de valores (workflow)
- Delegación temporal de permisos

### Fase 3 (Avanzado)
- Permisos personalizados (custom roles)
- Grupos de usuarios
- Permisos basados en departamento
- Integración con SSO/SAML
- OAuth 2.0
