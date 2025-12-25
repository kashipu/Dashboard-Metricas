# Dashboard de Métricas - Sistema de Consolidación Multi-Producto

Sistema centralizado para consolidar, visualizar y exportar métricas de múltiples productos digitales, organizadas por flujos y categorizadas por tipo (negocio, experiencia, producto).

## Descripción del Proyecto

Este proyecto permite a las organizaciones:

- **Centralizar métricas** de diferentes productos digitales en un solo lugar
- **Visualizar dashboards** individuales por producto con gráficos de tendencias
- **Seguir métricas** mensuales organizadas por flujos de trabajo
- **Exportar datos** a herramientas de BI como Looker, Power BI, Tableau
- **Categorizar métricas** por tipo: negocio, experiencia y producto

## Características Principales

- Dashboard interactivo por producto
- Gráficos de tendencias mensuales
- Seguimiento de cumplimiento de targets
- Comparación mes a mes
- Exportación JSON y CSV
- API REST completa
- Interfaz responsive (mobile, tablet, desktop)
- Autenticación segura (JWT)

## Documentación Completa

Este repositorio contiene documentación exhaustiva de arquitectura y plan de implementación:

### 📋 Índice de Documentos

1. **[ARCHITECTURE.md](./ARCHITECTURE.md)**
   - Visión general del sistema
   - Arquitectura de alto nivel
   - Stack tecnológico completo
   - Modelo de dominio
   - Patrones de diseño
   - Estrategias de escalabilidad
   - Seguridad e integraciones

2. **[DATABASE.md](./DATABASE.md)**
   - Modelo de datos completo
   - Diagrama Entidad-Relación
   - Esquema de tablas SQL
   - Índices y optimizaciones
   - Queries comunes
   - Vistas y funciones
   - Estrategias de backup

3. **[BACKEND.md](./BACKEND.md)**
   - Arquitectura del backend
   - Estructura del proyecto
   - Arquitectura en capas
   - Middleware y servicios
   - Servicios de exportación BI
   - Testing y deployment
   - Mejores prácticas

4. **[FRONTEND.md](./FRONTEND.md)**
   - Arquitectura del frontend
   - Estructura del proyecto
   - Componentes principales
   - Custom hooks
   - Estado global (Zustand)
   - API client
   - Responsive design
   - Optimizaciones

5. **[API.md](./API.md)**
   - Documentación completa de API REST
   - Todos los endpoints documentados
   - Ejemplos de request/response
   - Autenticación y seguridad
   - Rate limiting
   - Endpoints de exportación BI
   - Ejemplos con cURL

6. **[IMPLEMENTATION_PLAN.md](./IMPLEMENTATION_PLAN.md)**
   - Plan de implementación detallado
   - 10 fases con tareas específicas
   - Cronograma estimado (45 días)
   - Recursos necesarios
   - Riesgos y mitigaciones
   - Criterios de aceptación
   - Checklist de inicio

## Stack Tecnológico

### Backend
- **Node.js** 20+ con TypeScript
- **Express.js** - Framework web
- **Prisma** - ORM
- **PostgreSQL** 15+ - Base de datos
- **Zod** - Validación
- **JWT** - Autenticación

### Frontend
- **Next.js** 14+ (App Router)
- **React** 18+
- **TypeScript**
- **TailwindCSS** - Estilos
- **Shadcn/ui** - Componentes
- **Recharts** - Gráficos
- **TanStack Query** - Data fetching
- **Zustand** - Estado global

### DevOps
- **Docker** - Containerización
- **Vercel** - Deploy frontend
- **Railway/Render** - Deploy backend
- **GitHub Actions** - CI/CD

## Estructura de Métricas

```
Producto (ej: E-commerce Web)
├── Flujo 1: Adquisición
│   ├── Métrica Negocio: Costo por Adquisición
│   ├── Métrica Experiencia: Tasa de rebote
│   └── Métrica Producto: Nuevos usuarios
│
├── Flujo 2: Activación
│   ├── Métrica Negocio: Tasa de activación
│   └── Métrica Experiencia: Tiempo de primer valor
│
├── Flujo 3: Conversión
│   ├── Métrica Negocio: Tasa de conversión
│   ├── Métrica Negocio: Ticket promedio
│   └── Métrica Producto: Conversión por canal
│
└── Flujo 4: Retención
    ├── Métrica Negocio: Churn rate
    ├── Métrica Experiencia: NPS
    └── Métrica Producto: Usuarios activos mensuales
```

## Tipos de Métricas

### Métricas de Negocio
Relacionadas con resultados financieros y de negocio:
- Ingresos
- Conversión
- ROI
- CAC (Customer Acquisition Cost)
- LTV (Lifetime Value)

### Métricas de Experiencia
Relacionadas con la experiencia del usuario:
- NPS (Net Promoter Score)
- CSAT (Customer Satisfaction)
- Tiempo de carga
- Tasa de error
- Bounce rate

### Métricas de Producto
Relacionadas con el uso y adopción del producto:
- Usuarios activos
- Engagement
- Feature adoption
- Retención
- Churn rate

## Casos de Uso

### 1. Producto E-commerce
Seguimiento de métricas de:
- Adquisición de usuarios
- Conversión de ventas
- Retención de clientes
- Valor del ticket promedio

### 2. Aplicación SaaS
Monitoreo de:
- Activación de usuarios
- Engagement con features
- Churn y retención
- Ingresos recurrentes

### 3. Aplicación Móvil
Análisis de:
- Instalaciones y activaciones
- Engagement diario/mensual
- Retención por cohortes
- Performance técnico

## Exportación a Herramientas BI

El sistema provee endpoints optimizados para integración con:

### Looker
```javascript
// Configurar REST API Data Source
url: "https://api.metrics.com/export/ecommerce-web/json"
auth: "Bearer token"
```

### Power BI
```javascript
// Web connector
Origen de datos > Web > Avanzado
URL: https://api.metrics.com/export/ecommerce-web/json
Autenticación: Clave de API
```

### Tableau
```javascript
// Web Data Connector
URL: https://api.metrics.com/export/ecommerce-web/json
Headers: Authorization: Bearer token
```

## Quick Start

### Prerrequisitos
- Node.js 20+
- PostgreSQL 15+
- npm o yarn

### Instalación Backend

```bash
# Clonar repositorio
git clone https://github.com/tu-org/dashboard-metricas.git
cd dashboard-metricas/backend

# Instalar dependencias
npm install

# Configurar .env
cp .env.example .env
# Editar .env con tus credenciales

# Ejecutar migraciones
npx prisma migrate dev

# Iniciar servidor
npm run dev
```

### Instalación Frontend

```bash
cd frontend

# Instalar dependencias
npm install

# Configurar .env.local
cp .env.example .env.local
# Editar .env.local

# Iniciar aplicación
npm run dev
```

### Acceder a la Aplicación

- Frontend: http://localhost:3001
- Backend API: http://localhost:3000/api
- Swagger Docs: http://localhost:3000/api-docs

## Roadmap

### Fase 1: MVP (Actual)
- ✅ CRUD de productos, flujos y métricas
- ✅ Dashboard básico
- ✅ Exportación JSON/CSV
- ✅ API REST completa

### Fase 2: Mejoras (Futuro)
- Alertas automáticas
- Predicciones con ML
- Integración directa con fuentes de datos
- Roles y permisos granulares
- Webhooks
- Auditoría completa

### Fase 3: Avanzado (Futuro)
- Analytics avanzado
- Comparación entre productos
- Benchmarks de industria
- Reportes automáticos
- Mobile app

## Arquitectura Visual

```
┌─────────────────────────────────────────────┐
│         FRONTEND (Next.js + React)          │
│  ┌──────────────┐      ┌────────────────┐  │
│  │  Dashboards  │      │  Exportación   │  │
│  │   por        │      │  CSV/JSON      │  │
│  │  Producto    │      │                │  │
│  └──────────────┘      └────────────────┘  │
└─────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────┐
│         BACKEND (Node.js + Express)         │
│  ┌──────────────────────────────────────┐  │
│  │         API REST                     │  │
│  │  • Productos  • Flujos  • Métricas  │  │
│  │  • Valores    • Exportación         │  │
│  └──────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────┐
│         BASE DE DATOS (PostgreSQL)          │
│  productos → flujos → metricas → valores    │
└─────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────┐
│      HERRAMIENTAS BI (Looker, Power BI)     │
└─────────────────────────────────────────────┘
```

## Contribuir

Este es un proyecto de documentación de arquitectura. Para contribuir:

1. Fork el repositorio
2. Crea una rama para tu feature (`git checkout -b feature/mejora`)
3. Commit tus cambios (`git commit -m 'Agrega mejora X'`)
4. Push a la rama (`git push origin feature/mejora`)
5. Abre un Pull Request

## Licencia

MIT

## Contacto

Para preguntas o soporte:
- Email: soporte@metrics.com
- Slack: #dashboard-metricas
- Documentación: https://docs.metrics.com

## Agradecimientos

Proyecto desarrollado siguiendo las mejores prácticas de:
- Clean Architecture
- SOLID Principles
- RESTful API Design
- Material Design Guidelines

---

**Documentación creada**: Diciembre 2025

**Última actualización**: Diciembre 2025

**Versión**: 1.0.0

**Estado**: Documentación Completa ✅
