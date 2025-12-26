# Supabase Self-Hosted - Alternativa de Backend

## 1. Descripción General

**Supabase** es una alternativa open source a Firebase construida sobre PostgreSQL. Ofrece una solución completa de backend-as-a-service (BaaS) que puede desplegarse de forma **self-hosted** en tu propio VPS.

### ¿Qué incluye Supabase?

| Componente | Función | Reemplaza en arquitectura actual |
|------------|---------|----------------------------------|
| **PostgreSQL 15+** | Base de datos relacional | ✅ PostgreSQL directo |
| **PostgREST** | API REST automática desde schema DB | ✅ Express + Prisma (parcialmente) |
| **GoTrue** | Autenticación y gestión de usuarios | ✅ JWT manual + middleware auth |
| **Realtime** | WebSockets para cambios en tiempo real | ➕ Funcionalidad adicional |
| **Storage** | Almacenamiento de archivos (S3-compatible) | ➕ Funcionalidad adicional |
| **Edge Functions** | Funciones serverless (Deno) | 🔄 Servicios custom (Excel, Chat IA) |
| **pgAdmin / Studio** | Interfaz web para gestión de BD | ✅ Gestión de base de datos |

## 2. Arquitectura Propuesta con Supabase Self-Hosted

```
┌─────────────────────────────────────────────────────────────┐
│                     VPS con Dokploy                          │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │             Supabase Self-Hosted                   │    │
│  │                                                    │    │
│  │  ┌──────────────┐  ┌──────────────┐             │    │
│  │  │ PostgreSQL   │  │   GoTrue     │             │    │
│  │  │   (BD)       │  │   (Auth)     │             │    │
│  │  └──────────────┘  └──────────────┘             │    │
│  │                                                    │    │
│  │  ┌──────────────┐  ┌──────────────┐             │    │
│  │  │  PostgREST   │  │  Realtime    │             │    │
│  │  │  (API auto)  │  │  (WebSocket) │             │    │
│  │  └──────────────┘  └──────────────┘             │    │
│  │                                                    │    │
│  │  ┌──────────────┐  ┌──────────────┐             │    │
│  │  │   Storage    │  │   Studio     │             │    │
│  │  │   (Archivos) │  │   (Admin UI) │             │    │
│  │  └──────────────┘  └──────────────┘             │    │
│  └────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │      Servicios Custom (Node.js/Deno)               │    │
│  │                                                    │    │
│  │  - ExcelExportService (exportar .xlsx)            │    │
│  │  - ChatAIService (OpenAI/Claude)                  │    │
│  │  - Lógica de negocio compleja                     │    │
│  └────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │             Frontend Next.js                       │    │
│  │                                                    │    │
│  │  - @supabase/supabase-js (cliente)                │    │
│  │  - Autenticación con GoTrue                       │    │
│  │  - Queries directas con PostgREST                 │    │
│  └────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │             Traefik (Reverse Proxy)                │    │
│  │                                                    │    │
│  │  - api.tudominio.com → Supabase                   │    │
│  │  - app.tudominio.com → Frontend                   │    │
│  │  - studio.tudominio.com → Supabase Studio         │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

## 3. Instalación de Supabase Self-Hosted con Dokploy

### 3.1 Requisitos del VPS

```
CPU: 2+ cores (4 cores recomendado)
RAM: 4GB mínimo (8GB recomendado para producción)
Almacenamiento: 20GB+ SSD
OS: Ubuntu 22.04 LTS
Docker: 24.0+
```

### 3.2 Despliegue con Docker Compose

Supabase proporciona un repositorio oficial para self-hosting:

```bash
# 1. Clonar repositorio oficial de Supabase
cd /opt
git clone --depth 1 https://github.com/supabase/supabase
cd supabase/docker

# 2. Copiar archivo de ejemplo de variables de entorno
cp .env.example .env

# 3. Generar secretos seguros
sed -i "s|POSTGRES_PASSWORD=your-super-secret-and-long-postgres-password|POSTGRES_PASSWORD=$(openssl rand -base64 32)|g" .env
sed -i "s|JWT_SECRET=your-super-secret-jwt-token-with-at-least-32-characters-long|JWT_SECRET=$(openssl rand -base64 32)|g" .env
sed -i "s|ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...|ANON_KEY=$(openssl rand -base64 32)|g" .env
sed -i "s|SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...|SERVICE_ROLE_KEY=$(openssl rand -base64 32)|g" .env

# 4. Configurar dominio personalizado
nano .env
```

### 3.3 Configuración del archivo `.env`

```bash
############
# Secrets
############
POSTGRES_PASSWORD=tu-password-super-seguro-generado
JWT_SECRET=tu-jwt-secret-generado
ANON_KEY=clave-anon-generada
SERVICE_ROLE_KEY=clave-service-role-generada

############
# Database
############
POSTGRES_HOST=db
POSTGRES_DB=postgres
POSTGRES_PORT=5432

############
# API Proxy
############
KONG_HTTP_PORT=8000
KONG_HTTPS_PORT=8443

############
# API
############
PGRST_DB_SCHEMAS=public,storage,graphql_public
PGRST_JWT_SECRET=${JWT_SECRET}

############
# Auth
############
SITE_URL=https://app.tudominio.com
ADDITIONAL_REDIRECT_URLS=
JWT_EXPIRY=3600
DISABLE_SIGNUP=false
API_EXTERNAL_URL=https://api.tudominio.com

############
# Email
############
SMTP_ADMIN_EMAIL=admin@tudominio.com
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=tu-email@gmail.com
SMTP_PASS=tu-password-smtp
SMTP_SENDER_NAME=Dashboard Métricas

############
# Studio
############
STUDIO_DEFAULT_ORGANIZATION=Dashboard Métricas
STUDIO_DEFAULT_PROJECT=Métricas Productos

############
# Logs
############
LOGFLARE_API_KEY=tu-api-key-logflare # Opcional
```

### 3.4 Iniciar Supabase

```bash
# Iniciar todos los servicios
docker compose up -d

# Verificar que todos los contenedores estén corriendo
docker compose ps

# Ver logs
docker compose logs -f
```

### 3.5 Acceder a Supabase Studio

Supabase Studio es la interfaz web para gestionar tu base de datos, autenticación, storage, etc.

```
URL: http://localhost:3000
Usuario: (el configurado en .env)
```

Para exposición pública, configurar Traefik en Dokploy:
```
https://studio.tudominio.com → localhost:3000
https://api.tudominio.com → localhost:8000
```

## 4. Configuración de la Base de Datos

### 4.1 Migrar Schema Existente

Puedes usar el mismo schema SQL documentado en `DATABASE.md`:

```sql
-- Conectarse a PostgreSQL de Supabase
psql -h localhost -U postgres -d postgres

-- Ejecutar migraciones
\i schema.sql
```

O usar Supabase Studio:
1. Ir a "SQL Editor"
2. Pegar el schema completo de `DATABASE.md`
3. Ejecutar

### 4.2 Habilitar Row Level Security (RLS)

Supabase recomienda usar RLS para seguridad a nivel de fila:

```sql
-- Habilitar RLS en todas las tablas
ALTER TABLE usuarios ENABLE ROW LEVEL SECURITY;
ALTER TABLE productos ENABLE ROW LEVEL SECURITY;
ALTER TABLE flujos ENABLE ROW LEVEL SECURITY;
ALTER TABLE metricas ENABLE ROW LEVEL SECURITY;
ALTER TABLE valores_mensuales ENABLE ROW LEVEL SECURITY;

-- Políticas de acceso para diseñadores
CREATE POLICY "Diseñadores ven sus productos"
ON productos
FOR SELECT
USING (auth.uid()::text = responsable_id::text OR auth.jwt()->>'role' = 'admin');

CREATE POLICY "Diseñadores editan sus productos"
ON productos
FOR UPDATE
USING (auth.uid()::text = responsable_id::text OR auth.jwt()->>'role' = 'admin');

-- Políticas para valores_mensuales
CREATE POLICY "Diseñadores leen valores de sus productos"
ON valores_mensuales
FOR SELECT
USING (
  EXISTS (
    SELECT 1 FROM metricas m
    JOIN flujos f ON m.flujo_id = f.id
    JOIN productos p ON f.producto_id = p.id
    WHERE m.id = valores_mensuales.metrica_id
    AND (p.responsable_id::text = auth.uid()::text OR auth.jwt()->>'role' = 'admin')
  )
);

CREATE POLICY "Diseñadores insertan valores en sus productos"
ON valores_mensuales
FOR INSERT
WITH CHECK (
  EXISTS (
    SELECT 1 FROM metricas m
    JOIN flujos f ON m.flujo_id = f.id
    JOIN productos p ON f.producto_id = p.id
    WHERE m.id = valores_mensuales.metrica_id
    AND (p.responsable_id::text = auth.uid()::text OR auth.jwt()->>'role' = 'admin')
  )
);
```

## 5. Frontend con Supabase Client

### 5.1 Instalación

```bash
npm install @supabase/supabase-js
```

### 5.2 Configuración del Cliente

```typescript
// src/lib/supabase.ts
import { createClient } from '@supabase/supabase-js';

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!;
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!;

export const supabase = createClient(supabaseUrl, supabaseAnonKey);
```

```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL=https://api.tudominio.com
NEXT_PUBLIC_SUPABASE_ANON_KEY=tu-anon-key-de-supabase
```

### 5.3 Autenticación

```typescript
// src/hooks/useAuth.ts
import { supabase } from '@/lib/supabase';

export function useAuth() {
  const signIn = async (email: string, password: string) => {
    const { data, error } = await supabase.auth.signInWithPassword({
      email,
      password,
    });

    if (error) throw error;
    return data;
  };

  const signOut = async () => {
    const { error } = await supabase.auth.signOut();
    if (error) throw error;
  };

  const getCurrentUser = async () => {
    const { data: { user } } = await supabase.auth.getUser();
    return user;
  };

  return { signIn, signOut, getCurrentUser };
}
```

### 5.4 Queries con PostgREST

```typescript
// src/services/productService.ts
import { supabase } from '@/lib/supabase';

export const productService = {
  // Obtener productos del diseñador autenticado
  async getMyProducts() {
    const { data: user } = await supabase.auth.getUser();

    const { data, error } = await supabase
      .from('productos')
      .select(`
        *,
        flujos (
          *,
          metricas (
            *,
            valores_mensuales (*)
          )
        )
      `)
      .eq('responsable_id', user.user?.id);

    if (error) throw error;
    return data;
  },

  // Insertar nuevo valor mensual
  async insertValorMensual(metricaId: number, valor: number, periodo: string) {
    const { data, error } = await supabase
      .from('valores_mensuales')
      .insert({
        metrica_id: metricaId,
        valor,
        periodo,
      })
      .select();

    if (error) throw error;
    return data[0];
  },

  // Actualizar valor mensual
  async updateValorMensual(id: number, valor: number) {
    const { data, error } = await supabase
      .from('valores_mensuales')
      .update({ valor })
      .eq('id', id)
      .select();

    if (error) throw error;
    return data[0];
  },
};
```

### 5.5 Realtime - Actualizaciones en Tiempo Real

```typescript
// src/hooks/useRealtimeMetrics.ts
import { useEffect, useState } from 'react';
import { supabase } from '@/lib/supabase';

export function useRealtimeMetrics(productoId: number) {
  const [metrics, setMetrics] = useState([]);

  useEffect(() => {
    // Suscribirse a cambios en valores_mensuales
    const channel = supabase
      .channel('valores_changes')
      .on(
        'postgres_changes',
        {
          event: '*', // INSERT, UPDATE, DELETE
          schema: 'public',
          table: 'valores_mensuales',
        },
        (payload) => {
          console.log('Cambio detectado:', payload);
          // Actualizar estado local
          fetchMetrics();
        }
      )
      .subscribe();

    return () => {
      supabase.removeChannel(channel);
    };
  }, [productoId]);

  return metrics;
}
```

## 6. Servicios Custom con Edge Functions (Deno)

Para funcionalidades custom como exportar a Excel o Chat con IA, puedes usar **Supabase Edge Functions** (runtime Deno):

### 6.1 Crear Edge Function para Excel Export

```bash
# Crear nueva función
supabase functions new export-excel
```

```typescript
// supabase/functions/export-excel/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts';
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2';
import * as XLSX from 'https://cdn.sheetjs.com/xlsx-0.19.3/package/xlsx.mjs';

serve(async (req) => {
  try {
    // Autenticación
    const authHeader = req.headers.get('Authorization')!;
    const supabase = createClient(
      Deno.env.get('SUPABASE_URL') ?? '',
      Deno.env.get('SUPABASE_ANON_KEY') ?? '',
      { global: { headers: { Authorization: authHeader } } }
    );

    // Verificar usuario
    const { data: { user } } = await supabase.auth.getUser();
    if (!user) {
      return new Response('No autorizado', { status: 401 });
    }

    // Obtener parámetros
    const { productCode } = await req.json();

    // Consultar datos del producto
    const { data: producto } = await supabase
      .from('productos')
      .select(`
        *,
        flujos (
          *,
          metricas (
            *,
            valores_mensuales (*)
          )
        )
      `)
      .eq('codigo', productCode)
      .single();

    // Generar Excel con SheetJS
    const workbook = XLSX.utils.book_new();

    // Hoja resumen
    const resumenData = [
      ['Producto', producto.nombre],
      ['Código', producto.codigo],
      ['Total Flujos', producto.flujos.length],
    ];
    const resumenSheet = XLSX.utils.aoa_to_sheet(resumenData);
    XLSX.utils.book_append_sheet(workbook, resumenSheet, 'Resumen');

    // Hoja por cada flujo
    producto.flujos.forEach((flujo) => {
      const flujoData = flujo.metricas.map((metrica) => ({
        Métrica: metrica.nombre,
        Tipo: metrica.tipo_metrica,
        ...Object.fromEntries(
          metrica.valores_mensuales.map((v) => [
            new Date(v.periodo).toLocaleDateString('es-ES', { month: 'short', year: 'numeric' }),
            v.valor,
          ])
        ),
      }));

      const flujoSheet = XLSX.utils.json_to_sheet(flujoData);
      XLSX.utils.book_append_sheet(workbook, flujoSheet, flujo.nombre);
    });

    // Convertir a buffer
    const excelBuffer = XLSX.write(workbook, { type: 'buffer', bookType: 'xlsx' });

    // Retornar archivo
    return new Response(excelBuffer, {
      headers: {
        'Content-Type': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
        'Content-Disposition': `attachment; filename="${productCode}.xlsx"`,
      },
    });
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' },
    });
  }
});
```

### 6.2 Crear Edge Function para Chat con IA

```typescript
// supabase/functions/chat-ai/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts';
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2';
import OpenAI from 'https://esm.sh/openai@4.20.1';

serve(async (req) => {
  try {
    // Autenticación
    const authHeader = req.headers.get('Authorization')!;
    const supabase = createClient(
      Deno.env.get('SUPABASE_URL') ?? '',
      Deno.env.get('SUPABASE_ANON_KEY') ?? '',
      { global: { headers: { Authorization: authHeader } } }
    );

    const { data: { user } } = await supabase.auth.getUser();
    if (!user) {
      return new Response('No autorizado', { status: 401 });
    }

    // Obtener pregunta
    const { question, conversationHistory } = await req.json();

    // Obtener datos de métricas del usuario
    const { data: productos } = await supabase
      .from('productos')
      .select(`
        codigo,
        nombre,
        flujos (
          nombre,
          metricas (
            nombre,
            tipo_metrica,
            unidad_medida,
            valores_mensuales (
              periodo,
              valor
            )
          )
        )
      `)
      .eq('responsable_id', user.id);

    // Construir contexto
    const context = JSON.stringify(productos, null, 2);

    // Llamar a OpenAI
    const openai = new OpenAI({
      apiKey: Deno.env.get('OPENAI_API_KEY'),
    });

    const completion = await openai.chat.completions.create({
      model: 'gpt-4-turbo',
      messages: [
        {
          role: 'system',
          content: `Eres un asistente experto en análisis de métricas. Datos del usuario:\n${context}`,
        },
        ...conversationHistory,
        { role: 'user', content: question },
      ],
      temperature: 0.7,
      max_tokens: 1000,
    });

    const answer = completion.choices[0].message.content;

    return new Response(JSON.stringify({ answer }), {
      headers: { 'Content-Type': 'application/json' },
    });
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' },
    });
  }
});
```

### 6.3 Desplegar Edge Functions

```bash
# Desplegar función
supabase functions deploy export-excel

# Configurar secretos
supabase secrets set OPENAI_API_KEY=sk-...

# Invocar desde frontend
const { data, error } = await supabase.functions.invoke('export-excel', {
  body: { productCode: 'CRED-VEH' },
});
```

## 7. Comparación: Supabase vs Express + Prisma

| Aspecto | Express + Prisma | Supabase Self-Hosted |
|---------|------------------|----------------------|
| **API REST** | Manual (escribir endpoints) | ✅ Automática con PostgREST |
| **Autenticación** | Manual (JWT, bcrypt) | ✅ Incluida (GoTrue) |
| **Base de datos** | PostgreSQL + Prisma ORM | PostgreSQL + SQL directo |
| **Realtime** | ❌ Requiere WebSocket manual | ✅ Incluido |
| **Storage** | ❌ Usar S3/local | ✅ Incluido (S3-compatible) |
| **Admin UI** | ❌ Crear custom | ✅ Supabase Studio incluido |
| **Typescript** | ✅ TypeScript nativo | ✅ Tipos auto-generados |
| **Lógica custom** | ✅ Fácil (servicios Node.js) | 🔄 Edge Functions (Deno) |
| **Curva aprendizaje** | Media (Express conocido) | Media-Alta (aprender Supabase) |
| **Control total** | ✅ Control completo | 🔄 Limitado por Supabase |
| **Deployment** | Manual con Docker | Docker Compose oficial |
| **Escalabilidad** | Requiere configuración | ✅ Pre-configurada |
| **Costos** | Solo VPS | Solo VPS (igual) |

## 8. Ventajas de Supabase Self-Hosted

✅ **API automática**: PostgREST genera API REST desde el schema SQL
✅ **Autenticación incluida**: No necesitas implementar JWT manualmente
✅ **Realtime gratis**: WebSockets para actualizaciones en tiempo real
✅ **Supabase Studio**: Interfaz visual para gestionar BD, auth, storage
✅ **Row Level Security**: Seguridad a nivel de base de datos
✅ **Tipos TypeScript auto-generados**: Desde el schema SQL
✅ **Storage incluido**: Para avatares, archivos, etc.
✅ **Menos código backend**: Mucha funcionalidad ya resuelta
✅ **Ecosystem**: Librerías oficiales para JS, React, Next.js, Flutter, etc.
✅ **Self-hosted**: Control total, sin vendor lock-in

## 9. Desventajas de Supabase Self-Hosted

⚠️ **Curva de aprendizaje**: Necesitas aprender Supabase, PostgREST, RLS
⚠️ **Lógica compleja**: Edge Functions (Deno) en lugar de Node.js familiar
⚠️ **Menos control**: Algunos aspectos están pre-configurados
⚠️ **Debugging**: Más complejo que Express tradicional
⚠️ **Dependencias**: Múltiples servicios Docker (Kong, GoTrue, PostgREST, etc.)
⚠️ **Recursos VPS**: Requiere más RAM (4GB+ vs 2GB con Express)

## 10. Recomendación

### Usar Supabase si:
- ✅ Quieres desarrollo rápido con menos código backend
- ✅ Necesitas Realtime (actualización automática de dashboards)
- ✅ Quieres autenticación robusta sin implementarla
- ✅ Te gusta trabajar con PostgREST y RLS
- ✅ Necesitas Storage para archivos
- ✅ Quieres Supabase Studio para gestión visual

### Usar Express + Prisma si:
- ✅ Prefieres control total sobre el backend
- ✅ Tu equipo conoce bien Node.js/Express
- ✅ Necesitas lógica de negocio muy custom
- ✅ Quieres menor consumo de recursos (VPS pequeño)
- ✅ Prefieres escribir API endpoints explícitos
- ✅ No necesitas Realtime urgentemente

## 11. Arquitectura Híbrida (Recomendada)

**Mejor de ambos mundos**: Usar Supabase para funcionalidades estándar + servicios Node.js para lógica compleja.

```
Frontend (Next.js)
    ↓
    ├─→ Supabase (PostgREST)     → CRUD de productos, flujos, métricas
    ├─→ Supabase (GoTrue)        → Autenticación
    ├─→ Supabase (Realtime)      → Actualizaciones en vivo
    └─→ API Node.js Custom       → Excel Export, Chat IA, lógica compleja
```

**Configuración**:
```typescript
// Frontend usa ambos
import { supabase } from '@/lib/supabase';
import axios from 'axios';

// CRUD básico → Supabase
const productos = await supabase.from('productos').select('*');

// Funcionalidades custom → API Node.js
const excel = await axios.get('/api/export/CRED-VEH/excel');
const chatResponse = await axios.post('/api/chat/ask', { question: '...' });
```

## 12. Migración Gradual

Si decides usar Supabase, puedes migrar gradualmente:

**Fase 1**: Desplegar Supabase, migrar schema SQL
**Fase 2**: Migrar autenticación a GoTrue
**Fase 3**: Reemplazar queries Prisma por Supabase client
**Fase 4**: Mantener servicios custom (Excel, Chat) en Node.js o migrar a Edge Functions
**Fase 5**: Habilitar Realtime para dashboards

## 13. Siguiente Paso

¿Quieres que documente la implementación completa con Supabase self-hosted, o prefieres mantener la arquitectura actual con Express + Prisma?

Ambas opciones son excelentes y viables para tu proyecto.
