# Arquitectura del Frontend

## 1. Visión General

Aplicación web moderna construida con Next.js 14+, React y TypeScript. Proporciona dashboards interactivos para visualizar métricas de productos digitales, con capacidades de filtrado, comparación temporal y navegación intuitiva.

## 2. Stack Tecnológico

### 2.1 Core
- **Framework**: Next.js 14+ (App Router)
- **React**: 18+
- **TypeScript**: 5+
- **Lenguaje de estilos**: TailwindCSS 4 (nueva versión con mejor rendimiento)

### 2.2 UI y Componentes
- **Component Library**: Shadcn/ui
- **Icons**: Lucide React
- **Gráficos**: Recharts
- **Tablas**: TanStack Table (React Table v8)
- **Forms**: React Hook Form + Zod

### 2.3 Estado y Data Fetching
- **Estado Global**: Zustand
- **Data Fetching**: TanStack Query (React Query)
- **HTTP Client**: Axios

### 2.4 Utilidades
- **Formato de fechas**: date-fns
- **Formato de números**: numeral
- **Notificaciones**: Sonner (toast)
- **Validación**: Zod

## 3. Estructura del Proyecto

```
frontend/
├── src/
│   ├── app/                          # Next.js App Router
│   │   ├── (auth)/                   # Rutas de autenticación
│   │   │   ├── login/
│   │   │   └── layout.tsx
│   │   │
│   │   ├── (dashboard)/              # Rutas del dashboard
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx              # Home - Lista de productos
│   │   │   ├── productos/
│   │   │   │   ├── page.tsx          # Lista de productos
│   │   │   │   ├── [id]/
│   │   │   │   │   └── page.tsx      # Dashboard del producto
│   │   │   │   └── nuevo/
│   │   │   │       └── page.tsx      # Crear producto
│   │   │   │
│   │   │   ├── metricas/
│   │   │   │   └── page.tsx          # Gestión de métricas
│   │   │   │
│   │   │   └── reportes/
│   │   │       └── page.tsx          # Reportes y exportación
│   │   │
│   │   ├── api/                      # API Routes (opcional)
│   │   ├── layout.tsx                # Root layout
│   │   └── globals.css               # Estilos globales
│   │
│   ├── components/                   # Componentes reutilizables
│   │   ├── ui/                       # Componentes base (Shadcn)
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── dropdown-menu.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   ├── table.tsx
│   │   │   └── ...
│   │   │
│   │   ├── layout/                   # Componentes de layout
│   │   │   ├── Navbar.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── DashboardLayout.tsx
│   │   │
│   │   ├── productos/                # Componentes de productos
│   │   │   ├── ProductCard.tsx
│   │   │   ├── ProductList.tsx
│   │   │   ├── ProductForm.tsx
│   │   │   └── ProductHeader.tsx
│   │   │
│   │   ├── metricas/                 # Componentes de métricas
│   │   │   ├── MetricCard.tsx
│   │   │   ├── MetricChart.tsx
│   │   │   ├── MetricTable.tsx
│   │   │   ├── MetricForm.tsx
│   │   │   ├── MetricTrend.tsx
│   │   │   └── MetricComparison.tsx
│   │   │
│   │   ├── flujos/                   # Componentes de flujos
│   │   │   ├── FlowSection.tsx
│   │   │   ├── FlowCard.tsx
│   │   │   └── FlowForm.tsx
│   │   │
│   │   ├── charts/                   # Gráficos reutilizables
│   │   │   ├── LineChart.tsx
│   │   │   ├── BarChart.tsx
│   │   │   ├── AreaChart.tsx
│   │   │   └── PieChart.tsx
│   │   │
│   │   └── common/                   # Componentes comunes
│   │       ├── Loading.tsx
│   │       ├── ErrorBoundary.tsx
│   │       ├── EmptyState.tsx
│   │       ├── PageHeader.tsx
│   │       ├── SearchInput.tsx
│   │       ├── DateRangePicker.tsx
│   │       └── ExportButton.tsx
│   │
│   ├── lib/                          # Utilidades y configuración
│   │   ├── api/                      # Cliente API
│   │   │   ├── client.ts
│   │   │   ├── endpoints.ts
│   │   │   └── types.ts
│   │   │
│   │   ├── hooks/                    # Custom hooks
│   │   │   ├── useProductos.ts
│   │   │   ├── useMetricas.ts
│   │   │   ├── useValores.ts
│   │   │   ├── useAuth.ts
│   │   │   └── useExport.ts
│   │   │
│   │   ├── store/                    # Zustand stores
│   │   │   ├── authStore.ts
│   │   │   ├── productStore.ts
│   │   │   └── uiStore.ts
│   │   │
│   │   ├── utils/                    # Funciones utilitarias
│   │   │   ├── format.ts
│   │   │   ├── validation.ts
│   │   │   ├── date.ts
│   │   │   └── export.ts
│   │   │
│   │   └── constants/                # Constantes
│   │       ├── routes.ts
│   │       ├── metrics.ts
│   │       └── colors.ts
│   │
│   ├── types/                        # TypeScript types
│   │   ├── producto.ts
│   │   ├── flujo.ts
│   │   ├── metrica.ts
│   │   ├── valor.ts
│   │   └── api.ts
│   │
│   └── styles/                       # Estilos adicionales
│       └── charts.css
│
├── public/
│   ├── icons/
│   └── images/
│
├── .env.local
├── .eslintrc.json
├── next.config.js
├── tailwind.config.ts
├── tsconfig.json
└── package.json
```

## 4. Componentes Principales

### 4.1 Dashboard de Producto

```typescript
// src/app/(dashboard)/productos/[id]/page.tsx
'use client';

import { useParams } from 'next/navigation';
import { useProducto } from '@/lib/hooks/useProductos';
import { ProductHeader } from '@/components/productos/ProductHeader';
import { FlowSection } from '@/components/flujos/FlowSection';
import { Loading } from '@/components/common/Loading';
import { ErrorBoundary } from '@/components/common/ErrorBoundary';

export default function ProductoDashboard() {
  const { id } = useParams();
  const { data: producto, isLoading, error } = useProducto(Number(id));

  if (isLoading) return <Loading />;
  if (error) return <ErrorBoundary error={error} />;
  if (!producto) return <EmptyState message="Producto no encontrado" />;

  return (
    <div className="space-y-6">
      <ProductHeader producto={producto} />

      <div className="space-y-8">
        {producto.flujos.map((flujo) => (
          <FlowSection key={flujo.id} flujo={flujo} />
        ))}
      </div>
    </div>
  );
}
```

### 4.2 Componente de Métrica con Gráfico

```typescript
// src/components/metricas/MetricCard.tsx
'use client';

import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { MetricChart } from './MetricChart';
import { MetricTrend } from './MetricTrend';
import { formatNumber, formatUnit } from '@/lib/utils/format';
import { TrendingUp, TrendingDown } from 'lucide-react';
import type { Metrica } from '@/types/metrica';

interface MetricCardProps {
  metrica: Metrica;
}

export function MetricCard({ metrica }: MetricCardProps) {
  const ultimoValor = metrica.valores_mensuales[0];
  const valorAnterior = metrica.valores_mensuales[1];

  const cambio = valorAnterior
    ? ((Number(ultimoValor.valor) - Number(valorAnterior.valor)) / Number(valorAnterior.valor)) * 100
    : 0;

  const cumpleTarget = metrica.target
    ? Number(ultimoValor.valor) >= metrica.target
    : null;

  return (
    <Card>
      <CardHeader>
        <CardTitle className="flex items-center justify-between">
          <div>
            <span className="text-sm font-medium text-muted-foreground">
              {metrica.tipo_metrica.nombre}
            </span>
            <h3 className="text-lg font-semibold">{metrica.nombre}</h3>
          </div>

          {cumpleTarget !== null && (
            <div
              className={`px-2 py-1 rounded text-xs font-medium ${
                cumpleTarget
                  ? 'bg-green-100 text-green-800'
                  : 'bg-yellow-100 text-yellow-800'
              }`}
            >
              {cumpleTarget ? 'Cumplido' : 'Pendiente'}
            </div>
          )}
        </CardTitle>
      </CardHeader>

      <CardContent className="space-y-4">
        {/* Valor actual */}
        <div>
          <div className="flex items-baseline gap-2">
            <span className="text-3xl font-bold">
              {formatNumber(Number(ultimoValor.valor), metrica.unidad_medida)}
            </span>
            <span className="text-sm text-muted-foreground">
              {formatUnit(metrica.unidad_medida)}
            </span>
          </div>

          {/* Cambio porcentual */}
          {valorAnterior && (
            <div className="flex items-center gap-1 mt-1">
              {cambio > 0 ? (
                <TrendingUp className="w-4 h-4 text-green-600" />
              ) : (
                <TrendingDown className="w-4 h-4 text-red-600" />
              )}
              <span
                className={`text-sm font-medium ${
                  cambio > 0 ? 'text-green-600' : 'text-red-600'
                }`}
              >
                {cambio > 0 ? '+' : ''}
                {cambio.toFixed(1)}% vs mes anterior
              </span>
            </div>
          )}

          {/* Target */}
          {metrica.target && (
            <div className="text-sm text-muted-foreground mt-1">
              Target: {formatNumber(metrica.target, metrica.unidad_medida)}{' '}
              {formatUnit(metrica.unidad_medida)}
            </div>
          )}
        </div>

        {/* Gráfico */}
        <MetricChart data={metrica.valores_mensuales} unidad={metrica.unidad_medida} />

        {/* Observaciones */}
        {ultimoValor.observaciones && (
          <div className="text-xs text-muted-foreground border-l-2 border-blue-500 pl-3">
            {ultimoValor.observaciones}
          </div>
        )}
      </CardContent>
    </Card>
  );
}
```

### 4.3 Gráfico de Línea para Métricas

```typescript
// src/components/metricas/MetricChart.tsx
'use client';

import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from 'recharts';
import { format } from 'date-fns';
import { es } from 'date-fns/locale';
import type { ValorMensual } from '@/types/valor';

interface MetricChartProps {
  data: ValorMensual[];
  unidad: string;
}

export function MetricChart({ data, unidad }: MetricChartProps) {
  const chartData = data
    .slice()
    .reverse()
    .map((valor) => ({
      periodo: format(new Date(valor.periodo), 'MMM yyyy', { locale: es }),
      valor: Number(valor.valor),
    }));

  return (
    <ResponsiveContainer width="100%" height={200}>
      <LineChart data={chartData}>
        <CartesianGrid strokeDasharray="3 3" className="stroke-muted" />
        <XAxis
          dataKey="periodo"
          className="text-xs"
          tick={{ fontSize: 12 }}
        />
        <YAxis className="text-xs" tick={{ fontSize: 12 }} />
        <Tooltip
          contentStyle={{
            backgroundColor: 'hsl(var(--background))',
            border: '1px solid hsl(var(--border))',
            borderRadius: '6px',
          }}
          formatter={(value: number) => [
            unidad === 'porcentaje' ? `${value.toFixed(2)}%` : value.toFixed(2),
            'Valor',
          ]}
        />
        <Line
          type="monotone"
          dataKey="valor"
          stroke="hsl(var(--primary))"
          strokeWidth={2}
          dot={{ fill: 'hsl(var(--primary))', r: 4 }}
          activeDot={{ r: 6 }}
        />
      </LineChart>
    </ResponsiveContainer>
  );
}
```

### 4.4 Sección de Flujo

```typescript
// src/components/flujos/FlowSection.tsx
'use client';

import { Card } from '@/components/ui/card';
import { MetricCard } from '@/components/metricas/MetricCard';
import type { Flujo } from '@/types/flujo';

interface FlowSectionProps {
  flujo: Flujo;
}

export function FlowSection({ flujo }: FlowSectionProps) {
  return (
    <div className="space-y-4">
      <div>
        <h2 className="text-2xl font-bold">{flujo.nombre}</h2>
        {flujo.descripcion && (
          <p className="text-muted-foreground mt-1">{flujo.descripcion}</p>
        )}
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        {flujo.metricas.map((metrica) => (
          <MetricCard key={metrica.id} metrica={metrica} />
        ))}
      </div>
    </div>
  );
}
```

### 4.5 Header del Producto

```typescript
// src/components/productos/ProductHeader.tsx
'use client';

import { Button } from '@/components/ui/button';
import { ExportButton } from '@/components/common/ExportButton';
import { Edit, Settings } from 'lucide-react';
import type { Producto } from '@/types/producto';

interface ProductHeaderProps {
  producto: Producto;
}

export function ProductHeader({ producto }: ProductHeaderProps) {
  return (
    <div className="flex items-start justify-between">
      <div className="flex items-center gap-4">
        {producto.icono_url && (
          <div
            className="w-16 h-16 rounded-lg flex items-center justify-center"
            style={{ backgroundColor: producto.color_hex }}
          >
            <img src={producto.icono_url} alt={producto.nombre} className="w-10 h-10" />
          </div>
        )}

        <div>
          <h1 className="text-3xl font-bold">{producto.nombre}</h1>
          <p className="text-muted-foreground">{producto.descripcion}</p>
          <div className="flex items-center gap-2 mt-2">
            <span className="text-xs bg-muted px-2 py-1 rounded">
              {producto.codigo}
            </span>
            {producto.metadata?.departamento && (
              <span className="text-xs bg-primary/10 text-primary px-2 py-1 rounded">
                {producto.metadata.departamento}
              </span>
            )}
          </div>
        </div>
      </div>

      <div className="flex gap-2">
        <ExportButton productCode={producto.codigo} />
        <Button variant="outline" size="sm">
          <Edit className="w-4 h-4 mr-2" />
          Editar
        </Button>
        <Button variant="outline" size="sm">
          <Settings className="w-4 h-4 mr-2" />
          Configurar
        </Button>
      </div>
    </div>
  );
}
```

## 5. Custom Hooks

### 5.1 useProductos

```typescript
// src/lib/hooks/useProductos.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { productosApi } from '@/lib/api/endpoints';
import type { Producto, CreateProductoDto } from '@/types/producto';

export function useProductos(filters?: { activo?: boolean; search?: string }) {
  return useQuery({
    queryKey: ['productos', filters],
    queryFn: () => productosApi.getAll(filters),
  });
}

export function useProducto(id: number) {
  return useQuery({
    queryKey: ['producto', id],
    queryFn: () => productosApi.getById(id),
    enabled: !!id,
  });
}

export function useCreateProducto() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateProductoDto) => productosApi.create(data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['productos'] });
    },
  });
}

export function useUpdateProducto() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, data }: { id: number; data: Partial<Producto> }) =>
      productosApi.update(id, data),
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({ queryKey: ['productos'] });
      queryClient.invalidateQueries({ queryKey: ['producto', variables.id] });
    },
  });
}
```

### 5.2 useMetricas

```typescript
// src/lib/hooks/useMetricas.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { metricasApi } from '@/lib/api/endpoints';
import type { Metrica, CreateMetricaDto } from '@/types/metrica';

export function useMetricasByFlujo(flujoId: number) {
  return useQuery({
    queryKey: ['metricas', 'flujo', flujoId],
    queryFn: () => metricasApi.getByFlujo(flujoId),
    enabled: !!flujoId,
  });
}

export function useCreateMetrica() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateMetricaDto) => metricasApi.create(data),
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({
        queryKey: ['metricas', 'flujo', variables.flujo_id],
      });
    },
  });
}
```

## 6. API Client

### 6.1 Cliente Base

```typescript
// src/lib/api/client.ts
import axios from 'axios';
import { useAuthStore } from '@/lib/store/authStore';

export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000/api',
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor para agregar token
apiClient.interceptors.request.use(
  (config) => {
    const token = useAuthStore.getState().token;
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor para manejar errores
apiClient.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout();
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);
```

### 6.2 Endpoints

```typescript
// src/lib/api/endpoints.ts
import { apiClient } from './client';
import type { Producto, CreateProductoDto } from '@/types/producto';
import type { ApiResponse } from '@/types/api';

export const productosApi = {
  getAll: async (filters?: { activo?: boolean; search?: string }) => {
    return apiClient.get<ApiResponse<Producto[]>>('/productos', { params: filters });
  },

  getById: async (id: number) => {
    return apiClient.get<ApiResponse<Producto>>(`/productos/${id}`);
  },

  getComplete: async (id: number) => {
    return apiClient.get<ApiResponse<Producto>>(`/productos/${id}/complete`);
  },

  create: async (data: CreateProductoDto) => {
    return apiClient.post<ApiResponse<Producto>>('/productos', data);
  },

  update: async (id: number, data: Partial<Producto>) => {
    return apiClient.put<ApiResponse<Producto>>(`/productos/${id}`, data);
  },

  delete: async (id: number) => {
    return apiClient.delete<ApiResponse<void>>(`/productos/${id}`);
  },
};

export const exportApi = {
  exportJSON: async (productCode: string, startDate?: string, endDate?: string) => {
    return apiClient.get(`/export/${productCode}/json`, {
      params: { startDate, endDate },
    });
  },

  exportCSV: async (productCode: string) => {
    return apiClient.get(`/export/${productCode}/csv`, {
      responseType: 'blob',
    });
  },
};
```

## 7. Zustand Store

```typescript
// src/lib/store/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: number;
  email: string;
  nombre: string;
  rol: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  login: (user: User, token: string) => void;
  logout: () => void;
  isAuthenticated: () => boolean;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set, get) => ({
      user: null,
      token: null,

      login: (user, token) => set({ user, token }),

      logout: () => set({ user: null, token: null }),

      isAuthenticated: () => !!get().token,
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

## 8. Utilidades

### 8.1 Formateo de Números

```typescript
// src/lib/utils/format.ts
import numeral from 'numeral';

export function formatNumber(value: number, unidad: string): string {
  switch (unidad) {
    case 'porcentaje':
      return `${value.toFixed(2)}%`;
    case 'dinero':
      return numeral(value).format('$0,0.00');
    case 'numero':
      return numeral(value).format('0,0');
    case 'tiempo_ms':
      return `${value.toFixed(0)}ms`;
    case 'tiempo_seg':
      return `${value.toFixed(2)}s`;
    default:
      return value.toFixed(2);
  }
}

export function formatUnit(unidad: string): string {
  const units: Record<string, string> = {
    porcentaje: '%',
    dinero: 'USD',
    numero: '',
    tiempo_ms: 'ms',
    tiempo_seg: 's',
    usuarios: 'usuarios',
    ratio: '',
    score: 'pts',
  };

  return units[unidad] || '';
}
```

## 9. Configuración

### 9.1 TailwindCSS

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss';

const config: Config = {
  darkMode: ['class'],
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        // ... más colores
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
};

export default config;
```

### 9.2 Next.js Config

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    domains: ['localhost'],
  },
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  },
};

module.exports = nextConfig;
```

## 10. Variables de Entorno

```bash
# .env.local
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_APP_NAME=Dashboard de Métricas
```

## 11. Responsive Design

### Mobile First
- Grid responsivo: 1 col (mobile) → 2 cols (tablet) → 3 cols (desktop)
- Sidebar colapsable en móvil
- Gráficos adaptables
- Tablas con scroll horizontal en móvil

## 12. Optimizaciones de Performance

1. **Code Splitting**: Next.js automático
2. **Image Optimization**: next/image
3. **Lazy Loading**: React.lazy para componentes pesados
4. **Memoization**: React.memo para componentes que no cambian frecuentemente
5. **Virtual Scrolling**: Para listas largas
6. **Debouncing**: En búsquedas y filtros
7. **Prefetching**: TanStack Query automático

## 13. Accesibilidad

- Navegación por teclado
- ARIA labels
- Contraste de colores WCAG AA
- Focus visible
- Textos alternativos en imágenes

## 14. Testing

```typescript
// src/components/metricas/__tests__/MetricCard.test.tsx
import { render, screen } from '@testing-library/react';
import { MetricCard } from '../MetricCard';

describe('MetricCard', () => {
  it('debe renderizar correctamente', () => {
    const mockMetrica = {
      id: 1,
      nombre: 'Tasa de Conversión',
      unidad_medida: 'porcentaje',
      valores_mensuales: [
        { valor: 3.5, periodo: new Date('2025-01-01') },
      ],
    };

    render(<MetricCard metrica={mockMetrica} />);

    expect(screen.getByText('Tasa de Conversión')).toBeInTheDocument();
    expect(screen.getByText('3.50%')).toBeInTheDocument();
  });
});
```

## 15. Deployment

### Vercel (Recomendado)

```bash
# Desplegar automáticamente desde Git
vercel --prod

# O configurar en vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "env": {
    "NEXT_PUBLIC_API_URL": "@api-url"
  }
}
```

## 16. Mejores Prácticas

1. **TypeScript estricto**: No usar `any`
2. **Components pequeños**: Máximo 200 líneas
3. **Custom hooks**: Extraer lógica reutilizable
4. **Error boundaries**: Para manejar errores de componentes
5. **Loading states**: Feedback visual en operaciones async
6. **Optimistic updates**: Para mejor UX
7. **Accessibility first**: Cumplir WCAG
8. **Performance monitoring**: Core Web Vitals
9. **SEO**: Metadata en cada página
10. **Code splitting**: Componentes lazy cuando sea apropiado

## 17. Chat con IA - Asistente de Métricas

### 17.1 Descripción

Botón flotante con chat inteligente que permite a los diseñadores hacer preguntas en lenguaje natural sobre las métricas de sus productos.

**Características**:
- 💬 Chat contextual con IA (OpenAI, Anthropic Claude, etc.)
- 📊 Responde preguntas sobre tendencias, comparaciones, insights
- 🎯 Solo tiene acceso a datos del producto del usuario
- 🔒 Respeta permisos (diseñador solo ve sus productos)
- 📱 Botón flotante responsive
- 💾 Historial de conversación

### 17.2 Casos de Uso

**Preguntas que puede responder**:
```
"¿Cómo está el NPS de mi producto este mes?"
"Compara la tasa de conversión de los últimos 3 meses"
"¿Qué métricas bajaron en el último mes?"
"Dame un resumen del flujo de Colocación"
"¿Cuál es el promedio del CSAT en el último trimestre?"
"¿Qué flujo tiene mejor rendimiento?"
"Necesito un insight sobre la tendencia de aprobaciones"
```

### 17.3 Arquitectura

```
┌─────────────────────────────────────┐
│  Frontend (Next.js + TailwindCSS 4) │
│                                     │
│  ┌───────────────────────────────┐ │
│  │  FloatingChatButton.tsx       │ │
│  │  (botón flotante)             │ │
│  └─────────────┬─────────────────┘ │
│                │                   │
│  ┌─────────────▼─────────────────┐ │
│  │  ChatPanel.tsx                │ │
│  │  (panel de chat deslizable)   │ │
│  └─────────────┬─────────────────┘ │
│                │                   │
│  ┌─────────────▼─────────────────┐ │
│  │  useChatAI() hook             │ │
│  │  (lógica de estado)           │ │
│  └─────────────┬─────────────────┘ │
└────────────────┼───────────────────┘
                 │ API Request
                 ▼
┌─────────────────────────────────────┐
│  Backend API                        │
│                                     │
│  POST /api/chat/ask                 │
│  - Autenticación JWT               │
│  - Obtiene productos del usuario   │
│  - Construye contexto con métricas │
│  - Llama a LLM (OpenAI/Claude)     │
│  - Retorna respuesta               │
└─────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────┐
│  LLM API (OpenAI, Claude, etc.)     │
│  - Procesa pregunta + contexto      │
│  - Genera respuesta natural         │
└─────────────────────────────────────┘
```

### 17.4 Implementación Frontend

**Instalación de dependencias**:
```bash
npm install ai @ai-sdk/openai
# o
npm install @anthropic-ai/sdk
```

**Componente del botón flotante**:

```tsx
// src/components/chat/FloatingChatButton.tsx
'use client';

import { MessageCircle, X } from 'lucide-react';
import { useState } from 'react';
import { ChatPanel } from './ChatPanel';

export function FloatingChatButton() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      {/* Botón flotante */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="fixed bottom-6 right-6 z-50 flex h-14 w-14 items-center justify-center rounded-full bg-blue-600 text-white shadow-lg transition-transform hover:scale-110 hover:bg-blue-700 focus:outline-none focus:ring-4 focus:ring-blue-300"
        aria-label={isOpen ? 'Cerrar chat' : 'Abrir chat con IA'}
      >
        {isOpen ? (
          <X className="h-6 w-6" />
        ) : (
          <MessageCircle className="h-6 w-6" />
        )}
      </button>

      {/* Panel de chat deslizable */}
      {isOpen && <ChatPanel onClose={() => setIsOpen(false)} />}
    </>
  );
}
```

**Panel de chat**:

```tsx
// src/components/chat/ChatPanel.tsx
'use client';

import { useState } from 'react';
import { Send, Loader2 } from 'lucide-react';
import { useChatAI } from '@/hooks/useChatAI';

interface ChatPanelProps {
  onClose: () => void;
}

export function ChatPanel({ onClose }: ChatPanelProps) {
  const [input, setInput] = useState('');
  const { messages, sendMessage, isLoading } = useChatAI();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    if (!input.trim() || isLoading) return;

    await sendMessage(input);
    setInput('');
  };

  return (
    <div className="fixed bottom-24 right-6 z-40 flex h-[600px] w-[400px] flex-col rounded-lg bg-white shadow-2xl">
      {/* Header */}
      <div className="flex items-center justify-between border-b bg-gradient-to-r from-blue-600 to-blue-700 px-4 py-3 text-white">
        <div className="flex items-center gap-2">
          <div className="h-2 w-2 rounded-full bg-green-400"></div>
          <h3 className="font-semibold">Asistente de Métricas</h3>
        </div>
        <button
          onClick={onClose}
          className="rounded-full p-1 hover:bg-white/20"
        >
          <X className="h-5 w-5" />
        </button>
      </div>

      {/* Messages */}
      <div className="flex-1 overflow-y-auto p-4 space-y-4">
        {messages.length === 0 && (
          <div className="text-center text-gray-500 mt-8">
            <MessageCircle className="h-12 w-12 mx-auto mb-2 text-gray-300" />
            <p className="text-sm">Pregúntame sobre tus métricas</p>
            <div className="mt-4 space-y-2 text-xs text-left">
              <p className="bg-gray-50 p-2 rounded">
                💡 "¿Cómo está el NPS este mes?"
              </p>
              <p className="bg-gray-50 p-2 rounded">
                💡 "Compara conversión últimos 3 meses"
              </p>
            </div>
          </div>
        )}

        {messages.map((message) => (
          <div
            key={message.id}
            className={`flex ${
              message.role === 'user' ? 'justify-end' : 'justify-start'
            }`}
          >
            <div
              className={`max-w-[80%] rounded-lg px-4 py-2 ${
                message.role === 'user'
                  ? 'bg-blue-600 text-white'
                  : 'bg-gray-100 text-gray-900'
              }`}
            >
              <p className="text-sm whitespace-pre-wrap">{message.content}</p>
              <span className="text-xs opacity-70">
                {message.timestamp.toLocaleTimeString('es-ES', {
                  hour: '2-digit',
                  minute: '2-digit',
                })}
              </span>
            </div>
          </div>
        ))}

        {isLoading && (
          <div className="flex justify-start">
            <div className="bg-gray-100 rounded-lg px-4 py-2">
              <Loader2 className="h-5 w-5 animate-spin text-gray-500" />
            </div>
          </div>
        )}
      </div>

      {/* Input */}
      <form onSubmit={handleSubmit} className="border-t p-4">
        <div className="flex gap-2">
          <input
            type="text"
            value={input}
            onChange={(e) => setInput(e.target.value)}
            placeholder="Escribe tu pregunta..."
            className="flex-1 rounded-lg border border-gray-300 px-4 py-2 text-sm focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-200"
            disabled={isLoading}
          />
          <button
            type="submit"
            disabled={isLoading || !input.trim()}
            className="rounded-lg bg-blue-600 px-4 py-2 text-white transition-colors hover:bg-blue-700 disabled:bg-gray-300"
          >
            <Send className="h-5 w-5" />
          </button>
        </div>
      </form>
    </div>
  );
}
```

**Custom Hook para el chat**:

```tsx
// src/hooks/useChatAI.ts
import { useState } from 'react';
import { useQuery } from '@tanstack/react-query';
import axios from 'axios';

interface Message {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  timestamp: Date;
}

export function useChatAI() {
  const [messages, setMessages] = useState<Message[]>([]);
  const [isLoading, setIsLoading] = useState(false);

  const sendMessage = async (content: string) => {
    // Agregar mensaje del usuario
    const userMessage: Message = {
      id: crypto.randomUUID(),
      role: 'user',
      content,
      timestamp: new Date(),
    };

    setMessages((prev) => [...prev, userMessage]);
    setIsLoading(true);

    try {
      // Llamar al backend
      const response = await axios.post('/api/chat/ask', {
        question: content,
        conversationHistory: messages.map((m) => ({
          role: m.role,
          content: m.content,
        })),
      });

      // Agregar respuesta de la IA
      const aiMessage: Message = {
        id: crypto.randomUUID(),
        role: 'assistant',
        content: response.data.answer,
        timestamp: new Date(),
      };

      setMessages((prev) => [...prev, aiMessage]);
    } catch (error) {
      console.error('Error al enviar mensaje:', error);

      // Mensaje de error
      const errorMessage: Message = {
        id: crypto.randomUUID(),
        role: 'assistant',
        content: 'Lo siento, ocurrió un error. Por favor intenta de nuevo.',
        timestamp: new Date(),
      };

      setMessages((prev) => [...prev, errorMessage]);
    } finally {
      setIsLoading(false);
    }
  };

  const clearHistory = () => {
    setMessages([]);
  };

  return {
    messages,
    sendMessage,
    isLoading,
    clearHistory,
  };
}
```

### 17.5 Configuración de TailwindCSS 4

**Instalar TailwindCSS 4**:

```bash
npm install tailwindcss@next @tailwindcss/postcss@next
```

**Configuración `tailwind.config.ts`**:

```ts
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        // ... más colores de shadcn
      },
      animation: {
        'slide-in-right': 'slide-in-right 0.3s ease-out',
        'fade-in': 'fade-in 0.2s ease-in',
      },
      keyframes: {
        'slide-in-right': {
          '0%': { transform: 'translateX(100%)' },
          '100%': { transform: 'translateX(0)' },
        },
        'fade-in': {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
};

export default config;
```

**`postcss.config.js`**:

```js
module.exports = {
  plugins: {
    '@tailwindcss/postcss': {},
  },
};
```

**`app/globals.css`** (con Tailwind 4):

```css
@import 'tailwindcss';

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 221.2 83.2% 53.3%;
    --primary-foreground: 210 40% 98%;
    /* ... más variables */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    /* ... más variables dark mode */
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### 17.6 Agregar al Layout Principal

```tsx
// src/app/(dashboard)/layout.tsx
import { FloatingChatButton } from '@/components/chat/FloatingChatButton';

export default function DashboardLayout({ children }) {
  return (
    <div className="min-h-screen bg-gray-50">
      <Navbar />
      <div className="flex">
        <Sidebar />
        <main className="flex-1 p-6">{children}</main>
      </div>

      {/* Chat flotante disponible en todo el dashboard */}
      <FloatingChatButton />
    </div>
  );
}
```

### 17.7 Ventajas del Chat con IA

✅ **Acceso rápido a insights**: Sin navegar por múltiples dashboards
✅ **Lenguaje natural**: No necesita saber SQL o filtros complejos
✅ **Contextual**: Solo ve datos de sus productos asignados
✅ **Ahorro de tiempo**: Respuestas inmediatas a preguntas comunes
✅ **Detección de tendencias**: La IA puede identificar patrones
✅ **Disponible 24/7**: Siempre disponible para consultas

### 17.8 Limitaciones y Consideraciones

⚠️ **Costos de API**: Cada pregunta consume tokens del LLM
⚠️ **Rate limiting**: Implementar límites por usuario
⚠️ **Privacidad**: Solo enviar datos del usuario autenticado
⚠️ **Precisión**: La IA puede malinterpretar, siempre mostrar fuentes
⚠️ **Latencia**: Respuestas pueden tomar 2-5 segundos

### 17.9 Optimizaciones

```typescript
// Caché de respuestas comunes
const cacheKey = `chat:${userId}:${questionHash}`;
const cachedResponse = await redis.get(cacheKey);

if (cachedResponse) {
  return cachedResponse;
}

// Streaming de respuestas (para respuestas largas)
const stream = await openai.chat.completions.create({
  model: 'gpt-4-turbo',
  messages: [...],
  stream: true,
});

for await (const chunk of stream) {
  // Enviar chunks al frontend con SSE
}
```
