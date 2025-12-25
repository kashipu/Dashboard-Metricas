# Arquitectura del Sistema de Consolidación de Métricas

## 1. Visión General

Sistema centralizado para consolidar y visualizar métricas de múltiples productos digitales. Permite el seguimiento mensual de métricas de negocio, experiencia y producto a través de diferentes flujos de trabajo.

## 2. Objetivos del Sistema

- **Consolidación Multi-producto**: Gestionar métricas de diferentes productos digitales en un solo lugar
- **Múltiples Flujos**: Cada producto puede tener diferentes flujos de trabajo con métricas específicas
- **Categorización de Métricas**: Organizar métricas por tipo (negocio, experiencia, producto)
- **Análisis Temporal**: Seguimiento mensual de todas las métricas
- **Visualización**: Dashboard individual por producto
- **Integración BI**: Endpoints para exportar datos a Looker, Power BI u otras herramientas

## 3. Arquitectura Propuesta

### 3.1 Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────┐
│                    CAPA DE PRESENTACIÓN                      │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────┐      ┌──────────────────┐            │
│  │  Dashboard Web   │      │  Integraciones   │            │
│  │  (React/Next.js) │      │  BI (API REST)   │            │
│  └──────────────────┘      └──────────────────┘            │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    CAPA DE APLICACIÓN                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              API REST (Node.js/Express)              │  │
│  ├──────────────────────────────────────────────────────┤  │
│  │  • Gestión de Productos                              │  │
│  │  • Gestión de Flujos                                 │  │
│  │  • Gestión de Métricas                               │  │
│  │  • Endpoints de Exportación                          │  │
│  │  • Servicios de Agregación                           │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    CAPA DE DATOS                             │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────┐      ┌──────────────────┐            │
│  │   PostgreSQL     │      │   Redis Cache    │            │
│  │  (Base de Datos) │      │   (Opcional)     │            │
│  └──────────────────┘      └──────────────────┘            │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Stack Tecnológico Recomendado

#### Frontend
- **Framework**: Next.js 14+ (React)
- **UI Components**: Shadcn/ui o Material-UI
- **Gráficos**: Recharts o Chart.js
- **Estado**: Zustand o React Context
- **Styling**: TailwindCSS
- **TypeScript**: Para type safety

#### Backend
- **Runtime**: Node.js 20+
- **Framework**: Express.js o Fastify
- **ORM**: Prisma o TypeORM
- **Validación**: Zod
- **TypeScript**: Para type safety
- **Documentación API**: Swagger/OpenAPI

#### Base de Datos
- **Principal**: PostgreSQL 15+
- **Cache (opcional)**: Redis
- **Migraciones**: Prisma Migrate o TypeORM migrations

#### DevOps y Deployment
- **VPS**: Servidor privado con Dokploy instalado
- **Gestión de Infraestructura**: Dokploy (gestiona Docker, bases de datos, deployments)
- **Containerización**: Docker (gestionado por Dokploy)
- **Base de datos**: PostgreSQL 15+ (provisto por Dokploy)
- **Cache**: Redis (opcional, provisto por Dokploy)
- **CI/CD**: GitHub Actions → Dokploy webhooks
- **Proxy reverso**: Traefik (incluido en Dokploy)
- **SSL**: Let's Encrypt automático (gestionado por Dokploy)
- **Monitoreo**: Dokploy dashboard + logs integrados

**Ventajas de Dokploy**:
- Auto-hosting en tu propio VPS (control total)
- Gestión visual de aplicaciones y bases de datos
- Deploy automático desde GitHub
- SSL automático
- Backups automáticos de base de datos
- Logs centralizados
- Zero-downtime deployments

## 4. Modelo de Dominio

### 4.1 Entidades Principales

```
Producto
├── id
├── nombre
├── descripción
├── código_único
├── fecha_creación
└── Flujos[]
    ├── id
    ├── nombre
    ├── descripción
    ├── orden
    └── Métricas[]
        ├── id
        ├── nombre
        ├── tipo (negocio | experiencia | producto)
        ├── unidad_medida
        ├── descripción
        └── Valores_Mensuales[]
            ├── id
            ├── periodo (año-mes)
            ├── valor
            └── observaciones
```

### 4.2 Tipos de Métricas

1. **Métricas de Negocio**
   - Ingresos
   - Conversión
   - ROI
   - CAC (Customer Acquisition Cost)
   - LTV (Lifetime Value)

2. **Métricas de Experiencia**
   - NPS (Net Promoter Score)
   - CSAT (Customer Satisfaction)
   - Tiempo de carga
   - Tasa de error
   - Bounce rate

3. **Métricas de Producto**
   - Usuarios activos
   - Engagement
   - Feature adoption
   - Retención
   - Churn rate

## 5. Patrones de Diseño

### 5.1 Backend
- **Repository Pattern**: Para abstracción de acceso a datos
- **Service Layer**: Lógica de negocio separada de controladores
- **DTO Pattern**: Para validación y transformación de datos
- **Middleware**: Para autenticación, validación, logging

### 5.2 Frontend
- **Component Composition**: Componentes reutilizables
- **Custom Hooks**: Lógica compartida
- **Container/Presentational**: Separación de lógica y presentación
- **Context API**: Para estado global

## 6. Seguridad

### 6.1 Autenticación y Autorización
- JWT para autenticación
- RBAC (Role-Based Access Control)
- Rate limiting en endpoints
- CORS configurado correctamente

### 6.2 Validación de Datos
- Validación en frontend y backend
- Sanitización de inputs
- Prepared statements (protección SQL injection)

## 7. Escalabilidad

### 7.1 Estrategias
- **Horizontal scaling**: Backend stateless
- **Caching**: Redis para datos frecuentemente accedidos
- **Indexación**: Índices en PostgreSQL para queries frecuentes
- **Paginación**: Para listados grandes
- **Lazy loading**: En frontend para mejor performance

### 7.2 Performance
- Query optimization
- Database connection pooling
- CDN para assets estáticos
- Server-side rendering para SEO

## 8. Integraciones BI

### 8.1 Endpoints de Exportación
- **Formato JSON**: Para APIs modernas
- **Formato CSV**: Para Excel y herramientas legacy
- **Formato Parquet** (opcional): Para grandes volúmenes

### 8.2 Conectores
- **Looker**: REST API con autenticación
- **Power BI**: Web API connector
- **Tableau**: REST API connector
- **Google Data Studio**: Connector via API

## 9. Monitoreo y Logs

### 9.1 Logging
- Winston o Pino para logs estructurados
- Diferentes niveles: error, warn, info, debug
- Logs centralizados

### 9.2 Métricas del Sistema
- Healthcheck endpoints
- Monitoring de performance
- Error tracking (Sentry)

## 10. Flujo de Datos

```
Usuario → Dashboard → API → Service Layer → Repository → Database
                                    ↓
                                  Cache
                                    ↓
                            Herramienta BI
```

## 11. Ventajas de esta Arquitectura

1. **Modular**: Fácil de mantener y escalar
2. **Type-safe**: TypeScript en todo el stack
3. **Moderna**: Tecnologías actuales y en demanda
4. **Flexible**: Fácil agregar nuevos productos y métricas
5. **Integrable**: API REST estándar para cualquier herramienta
6. **Performante**: Optimizada para consultas y agregaciones
7. **Mantenible**: Código organizado y documentado
8. **Auto-hosted**: Control total con Dokploy en VPS privado

## 12. Deployment con Dokploy en VPS

### 12.1 Configuración Inicial del VPS

**Requisitos del VPS**:
- **RAM**: Mínimo 2GB (recomendado 4GB)
- **CPU**: 2 cores mínimo
- **Almacenamiento**: 20GB mínimo (SSD recomendado)
- **OS**: Ubuntu 22.04 LTS o Debian 12
- **Dokploy**: Instalado y configurado

### 12.2 Estructura de Aplicaciones en Dokploy

**Crear 3 servicios en Dokploy**:

1. **PostgreSQL Database**
   - Tipo: Database → PostgreSQL 15
   - Nombre: `metrics-db`
   - Usuario: `metrics_user`
   - Password: (generado automáticamente por Dokploy)
   - Base de datos: `metrics_production`
   - Backups automáticos: Habilitado (diario)

2. **Backend API**
   - Tipo: Application → Node.js
   - Nombre: `metrics-backend`
   - Repositorio: `https://github.com/tu-org/dashboard-metricas`
   - Branch: `main`
   - Build Command: `cd backend && npm install && npx prisma migrate deploy && npm run build`
   - Start Command: `cd backend && npm run start`
   - Puerto: 3000
   - Variables de entorno:
     ```
     DATABASE_URL=postgresql://user:pass@metrics-db:5432/metrics_production
     JWT_SECRET=(generado)
     NODE_ENV=production
     PORT=3000
     ```
   - Dominio: `api.dashboard-metricas.com`

3. **Frontend Web**
   - Tipo: Application → Next.js
   - Nombre: `metrics-frontend`
   - Repositorio: `https://github.com/tu-org/dashboard-metricas`
   - Branch: `main`
   - Build Command: `cd frontend && npm install && npm run build`
   - Start Command: `cd frontend && npm run start`
   - Puerto: 3001
   - Variables de entorno:
     ```
     NEXT_PUBLIC_API_URL=https://api.dashboard-metricas.com
     NODE_ENV=production
     ```
   - Dominio: `dashboard-metricas.com`

### 12.3 Configuración de Dominios

En Dokploy, configurar:
- `dashboard-metricas.com` → Frontend (puerto 3001)
- `api.dashboard-metricas.com` → Backend (puerto 3000)

SSL automático con Let's Encrypt (gestionado por Dokploy).

### 12.4 CI/CD con GitHub Actions

Crear archivo `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Dokploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger Dokploy Deploy
        run: |
          curl -X POST ${{ secrets.DOKPLOY_WEBHOOK_URL }}
```

Dokploy genera un webhook URL que se agrega a GitHub Secrets.

### 12.5 Backups

Dokploy gestiona backups automáticos:
- **Base de datos**: Backup diario a las 2 AM
- **Retención**: 7 días (configurable)
- **Restauración**: Un click desde Dokploy dashboard

### 12.6 Monitoreo

Desde Dokploy dashboard:
- **Logs en tiempo real** de backend y frontend
- **Métricas de CPU y RAM**
- **Uptime monitoring**
- **Alertas** por email (opcional)

### 12.7 Escalamiento

Para escalar:
1. **Vertical**: Aumentar recursos del VPS
2. **Horizontal**: Dokploy soporta múltiples réplicas (configurar en dashboard)

## 13. Próximos Pasos

1. Revisar y aprobar arquitectura
2. Definir modelo de datos detallado
3. Diseñar wireframes de dashboards
4. Configurar proyecto base
5. Implementar MVP con un producto de ejemplo
6. Iterar y expandir funcionalidades
