# Visualizaciones del Dashboard

## 1. Visión General

Sistema de visualización con **dos niveles** de detalle que permite a diseñadores y administradores ver la salud de sus productos digitales de forma clara y accionable.

## 2. Niveles de Visualización

### Nivel 1: Vista de Producto Completo
Muestra el producto con TODOS sus flujos y sus métricas principales.

### Nivel 2: Vista de Flujo Detallado
Muestra un flujo específico con todas sus métricas en detalle.

## 3. Ejemplo: Crédito de Vehículo

### Estructura del Producto

```
Producto: Crédito de Vehículo
│
├── Flujo 1: Colocación
│   ├── Solicitudes Recibidas (número)
│   ├── Tasa de Aprobación (porcentaje)
│   ├── Ticket Promedio (dinero)
│   ├── Tiempo de Evaluación (tiempo_segundos)
│   └── CSAT Proceso (score_csat)
│
└── Flujo 2: Legalización
    ├── Expedientes Procesados (número)
    ├── Tiempo de Legalización (tiempo_segundos)
    ├── Tasa de Éxito (porcentaje)
    ├── Documentos Faltantes (número)
    └── CSAT Documentación (score_csat)
```

## 4. Vista de Producto Completo

### 4.1 Mockup de Dashboard Completo

```
╔════════════════════════════════════════════════════════════════════╗
║  🚗 Crédito de Vehículo                          [Ver Todo ▼]     ║
║  Diseñador: María González                       [Exportar]       ║
╠════════════════════════════════════════════════════════════════════╣
║                                                                    ║
║  📊 RESUMEN GENERAL - Marzo 2025                                  ║
║  ┌──────────────┬──────────────┬──────────────┬──────────────┐   ║
║  │ Total Flujos │ Total        │ Métricas     │ Última       │   ║
║  │              │ Métricas     │ Cumpliendo   │ Actualización│   ║
║  ├──────────────┼──────────────┼──────────────┼──────────────┤   ║
║  │      2       │     10       │   8 / 10     │  5 mar 2025  │   ║
║  │              │              │   80% ✅     │              │   ║
║  └──────────────┴──────────────┴──────────────┴──────────────┘   ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  🔵 FLUJO: COLOCACIÓN                          [Ver Detalle →]   ║
║                                                                    ║
║  ┌─────────────────────┬─────────────────────┬─────────────────┐ ║
║  │ Solicitudes         │ Tasa Aprobación     │ Ticket Promedio │ ║
║  │ Recibidas           │                     │                 │ ║
║  │ Negocio             │ Negocio             │ Negocio         │ ║
║  │                     │                     │                 │ ║
║  │ 1,250               │ 68.5%               │ $285,000        │ ║
║  │ ✅ +15% vs ant      │ ✅ +2.5% vs ant     │ ✅ +3.2% vs ant │ ║
║  │ Target: 1,000       │ Target: 65%         │ Target: $250K   │ ║
║  │                     │                     │                 │ ║
║  │ [Mini gráfico ▁▂▃▅▇]│ [Mini gráfico ▃▄▅▆▇]│ [Mini gráfico]  │ ║
║  └─────────────────────┴─────────────────────┴─────────────────┘ ║
║                                                                    ║
║  ┌─────────────────────┬─────────────────────┐                   ║
║  │ Tiempo Evaluación   │ CSAT Proceso        │                   ║
║  │ Experiencia         │ Experiencia         │                   ║
║  │                     │                     │                   ║
║  │ 45 min              │ 82.5                │                   ║
║  │ ✅ -10min vs ant    │ ⚠️  -2.5 vs ant     │                   ║
║  │ Target: < 60 min    │ Target: > 85        │                   ║
║  │                     │                     │                   ║
║  │ [Mini gráfico ▇▆▅▄▃]│ [Mini gráfico ▆▅▄▃▂]│                   ║
║  └─────────────────────┴─────────────────────┘                   ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  🟢 FLUJO: LEGALIZACIÓN                        [Ver Detalle →]   ║
║                                                                    ║
║  ┌─────────────────────┬─────────────────────┬─────────────────┐ ║
║  │ Expedientes         │ Tiempo              │ Tasa de Éxito   │ ║
║  │ Procesados          │ Legalización        │                 │ ║
║  │ Producto            │ Experiencia         │ Negocio         │ ║
║  │                     │                     │                 │ ║
║  │ 890                 │ 12 días             │ 92.5%           │ ║
║  │ ✅ +8% vs ant       │ ⚠️  +1 día vs ant   │ ✅ +1.5% vs ant │ ║
║  │ Target: 800         │ Target: < 10 días   │ Target: > 90%   │ ║
║  │                     │                     │                 │ ║
║  │ [Mini gráfico ▁▃▅▆▇]│ [Mini gráfico ▃▄▅▆▇]│ [Mini gráfico]  │ ║
║  └─────────────────────┴─────────────────────┴─────────────────┘ ║
║                                                                    ║
║  ┌─────────────────────┬─────────────────────┐                   ║
║  │ Docs Faltantes      │ CSAT Documentación  │                   ║
║  │ Experiencia         │ Experiencia         │                   ║
║  │                     │                     │                   ║
║  │ 125                 │ 88.0                │                   ║
║  │ ✅ -20 vs ant       │ ✅ +3.5 vs ant      │                   ║
║  │ Target: < 100       │ Target: > 85        │                   ║
║  │                     │                     │                   ║
║  │ [Mini gráfico ▇▆▅▃▂]│ [Mini gráfico ▂▃▅▆▇]│                   ║
║  └─────────────────────┴─────────────────────┘                   ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  📈 GRÁFICA COMPARATIVA DE FLUJOS                                 ║
║  (Cumplimiento de Targets)                                        ║
║                                                                    ║
║  100% ┤                           ●  ● Legalización (92%)         ║
║   90% ┤                  ●  ●                                      ║
║   80% ┤         ●  ●                ● Colocación (80%)            ║
║   70% ┤  ●  ●                                                      ║
║   60% ┤                                                            ║
║       └──┬────┬────┬────┬────┬────┬────                           ║
║         Ene  Feb  Mar  Abr  May  Jun                              ║
║                                                                    ║
╚════════════════════════════════════════════════════════════════════╝
```

### 4.2 Características de la Vista Completa

**Lo que muestra**:
- ✅ Resumen general del producto (total flujos, métricas, cumplimiento)
- ✅ Cada flujo con sus métricas principales (máximo 5 métricas por flujo)
- ✅ Mini gráficos de tendencia (últimos 6 meses)
- ✅ Indicadores visuales de cumplimiento (✅ cumple, ⚠️ alerta, ❌ no cumple)
- ✅ Comparación mes a mes
- ✅ Gráfica comparativa de flujos
- ✅ Botón "Ver Detalle" por cada flujo

**Propósito**:
- Vista panorámica rápida
- Identificar qué flujos necesitan atención
- Ver salud general del producto

## 5. Vista de Flujo Detallado

### 5.1 Mockup de Flujo "Colocación"

```
╔════════════════════════════════════════════════════════════════════╗
║  ← Volver a Crédito de Vehículo                                   ║
║                                                                    ║
║  🔵 FLUJO: COLOCACIÓN                                             ║
║  Producto: Crédito de Vehículo                                    ║
║  Diseñador: María González                       [Exportar CSV]   ║
╠════════════════════════════════════════════════════════════════════╣
║                                                                    ║
║  📊 RESUMEN DEL FLUJO - Marzo 2025                                ║
║  ┌──────────────┬──────────────┬──────────────┬──────────────┐   ║
║  │ Total        │ Cumpliendo   │ Por Debajo   │ Sin Target   │   ║
║  │ Métricas     │ Target       │ de Target    │              │   ║
║  ├──────────────┼──────────────┼──────────────┼──────────────┤   ║
║  │      5       │   4 (80%)    │   1 (20%)    │      0       │   ║
║  └──────────────┴──────────────┴──────────────┴──────────────┘   ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  💰 MÉTRICAS DE NEGOCIO                                           ║
║                                                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ Solicitudes Recibidas                    👤 María González │  ║
║  │ Número                                   📅 Últ: 5 mar 25  │  ║
║  ├────────────────────────────────────────────────────────────┤  ║
║  │                                                            │  ║
║  │  Valor Actual: 1,250                                       │  ║
║  │  ✅ +15% vs mes anterior (1,087)                           │  ║
║  │  ✅ Cumple target: 1,000                                   │  ║
║  │                                                            │  ║
║  │  📈 TENDENCIA ÚLTIMOS 12 MESES                             │  ║
║  │  1400┤                                              ●      │  ║
║  │  1200┤                                    ●    ●           │  ║
║  │  1000┤                          ●    ●                     │  ║
║  │   800┤                ●    ●                               │  ║
║  │   600┤      ●    ●                                         │  ║
║  │      └──┬────┬────┬────┬────┬────┬────┬────┬────┬────┬──  │  ║
║  │        Abr  May  Jun  Jul  Ago  Sep  Oct  Nov  Dic Ene Mar│  ║
║  │                                                            │  ║
║  │  📊 COMPARACIÓN MENSUAL                                    │  ║
║  │  ┌────────┬────────┬──────────┬──────────┐                │  ║
║  │  │ Mes    │ Valor  │ vs Ant.  │ vs Target│                │  ║
║  │  ├────────┼────────┼──────────┼──────────┤                │  ║
║  │  │ Mar 25 │ 1,250  │ +15.0%   │ +25.0%   │                │  ║
║  │  │ Feb 25 │ 1,087  │  +8.5%   │  +8.7%   │                │  ║
║  │  │ Ene 25 │ 1,002  │  -5.2%   │  +0.2%   │                │  ║
║  │  │ Dic 24 │ 1,057  │  +2.3%   │  +5.7%   │                │  ║
║  │  └────────┴────────┴──────────┴──────────┘                │  ║
║  │                                                            │  ║
║  │  💡 Insight: Tendencia positiva. Marzo superó el target   │  ║
║  │     en 25%. Continuar con estrategias actuales.           │  ║
║  │                                                            │  ║
║  │  [Registrar Valor Abril] [Editar Métrica] [Ver Histórico]│  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ Tasa de Aprobación                       👤 María González│  ║
║  │ Porcentaje                               📅 Últ: 5 mar 25 │  ║
║  ├────────────────────────────────────────────────────────────┤  ║
║  │                                                            │  ║
║  │  Valor Actual: 68.5%                                       │  ║
║  │  ✅ +2.5% vs mes anterior (66.8%)                          │  ║
║  │  ✅ Cumple target: 65%                                     │  ║
║  │                                                            │  ║
║  │  📈 TENDENCIA                                              │  ║
║  │   75%┤                                              ●      │  ║
║  │   70%┤                                    ●    ●           │  ║
║  │   65%┤ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  (Target)       │  ║
║  │   60%┤                ●    ●                               │  ║
║  │   55%┤      ●    ●                                         │  ║
║  │                                                            │  ║
║  │  💡 Benchmark: E-commerce 60-70% (estás en rango bueno)   │  ║
║  │                                                            │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ Ticket Promedio                          👤 María González│  ║
║  │ Dinero                                   📅 Últ: 5 mar 25 │  ║
║  ├────────────────────────────────────────────────────────────┤  ║
║  │                                                            │  ║
║  │  Valor Actual: $285,000                                    │  ║
║  │  ✅ +3.2% vs mes anterior ($276,150)                       │  ║
║  │  ✅ Cumple target: $250,000                                │  ║
║  │                                                            │  ║
║  │  📈 TENDENCIA                                              │  ║
║  │  300K┤                                              ●      │  ║
║  │  280K┤                                    ●    ●           │  ║
║  │  260K┤                          ●    ●                     │  ║
║  │  250K┤ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  (Target)       │  ║
║  │  240K┤                ●    ●                               │  ║
║  │                                                            │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  😊 MÉTRICAS DE EXPERIENCIA                                       ║
║                                                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ Tiempo de Evaluación                     👤 María González│  ║
║  │ Tiempo (minutos)                         📅 Últ: 5 mar 25 │  ║
║  ├────────────────────────────────────────────────────────────┤  ║
║  │                                                            │  ║
║  │  Valor Actual: 45 min                                      │  ║
║  │  ✅ -10 min vs mes anterior (55 min)                       │  ║
║  │  ✅ Cumple target: < 60 min                                │  ║
║  │                                                            │  ║
║  │  📈 TENDENCIA (menor es mejor)                             │  ║
║  │   70┤  ●                                                   │  ║
║  │   60┤ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  (Target)        │  ║
║  │   50┤       ●    ●    ●                                    │  ║
║  │   40┤                          ●    ●              ●       │  ║
║  │   30┤                                                      │  ║
║  │                                                            │  ║
║  │  💡 Excelente: Se redujo el tiempo en 18% este mes        │  ║
║  │                                                            │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ CSAT Proceso                             👤 María González│  ║
║  │ Score CSAT (0-100)                       📅 Últ: 5 mar 25 │  ║
║  ├────────────────────────────────────────────────────────────┤  ║
║  │                                                            │  ║
║  │  Valor Actual: 82.5                                        │  ║
║  │  ⚠️  -2.5 vs mes anterior (85.0)                           │  ║
║  │  ⚠️  Por debajo de target: 85                              │  ║
║  │                                                            │  ║
║  │  📈 TENDENCIA                                              │  ║
║  │   90┤       ●    ●    ●                                    │  ║
║  │   85┤ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  (Target)        │  ║
║  │   80┤                                              ●       │  ║
║  │   75┤                          ●                           │  ║
║  │   70┤                                                      │  ║
║  │                                                            │  ║
║  │  ⚠️  Alerta: El CSAT bajó por primera vez en 4 meses.     │  ║
║  │     Recomendación: Revisar feedback de clientes.          │  ║
║  │                                                            │  ║
║  │  Observaciones (Marzo):                                    │  ║
║  │  "Algunos clientes reportaron lentitud en el sistema de   │  ║
║  │   captura de documentos. Se está investigando."           │  ║
║  │                                                            │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   ║
║                                                                    ║
║  📊 ANÁLISIS CRUZADO                                              ║
║                                                                    ║
║  Correlaciones detectadas:                                        ║
║  • ✅ Mayor número de solicitudes correlaciona con menor          ║
║       tiempo de evaluación (proceso optimizado)                   ║
║  • ⚠️  CSAT bajó cuando aumentaron solicitudes (posible           ║
║       saturación del equipo)                                      ║
║                                                                    ║
║  Recomendaciones:                                                 ║
║  1. Investigar caída en CSAT - posible relación con volumen      ║
║  2. Considerar aumentar capacidad del equipo                      ║
║  3. Mantener estrategias de adquisición actuales                  ║
║                                                                    ║
╚════════════════════════════════════════════════════════════════════╝
```

### 5.2 Características de la Vista Detallada

**Lo que muestra**:
- ✅ **Todas las métricas** del flujo (sin límite, todas visibles)
- ✅ **Gráfico grande** de tendencia de 12 meses por métrica
- ✅ **Tabla de comparación** mensual con todos los valores
- ✅ **Insights automáticos** generados por el sistema
- ✅ **Información del diseñador** que registró cada valor
- ✅ **Fecha de última actualización** por métrica
- ✅ **Observaciones** registradas por el diseñador
- ✅ **Análisis cruzado** entre métricas
- ✅ **Recomendaciones** basadas en los datos

**Propósito**:
- Análisis profundo de un flujo específico
- Ver evolución detallada de cada métrica
- Identificar correlaciones entre métricas
- Tomar decisiones informadas
- Registrar nuevos valores

## 6. Navegación entre Vistas

```
┌──────────────────────────────────────────────┐
│ Mis Productos                                │
│                                              │
│ • Crédito de Vehículo           [Ver →]     │
│ • Tarjeta de Crédito            [Ver →]     │
│ • Cuenta de Ahorro              [Ver →]     │
└──────────────────────────────────────────────┘
                   │
                   │ Click en "Crédito de Vehículo"
                   ▼
┌──────────────────────────────────────────────┐
│ Vista de Producto Completo                   │
│ Crédito de Vehículo                          │
│                                              │
│ • Flujo: Colocación    [Ver Detalle →]      │
│ • Flujo: Legalización  [Ver Detalle →]      │
└──────────────────────────────────────────────┘
                   │
                   │ Click en "Ver Detalle" de Colocación
                   ▼
┌──────────────────────────────────────────────┐
│ Vista de Flujo Detallado                     │
│ Colocación (del producto Crédito Vehículo)  │
│                                              │
│ [← Volver a Crédito de Vehículo]            │
│                                              │
│ Todas las métricas del flujo con gráficos   │
│ completos, tablas, análisis...               │
└──────────────────────────────────────────────┘
```

## 7. Permisos de Visualización

### Admin
```
✅ Puede ver TODOS los productos (lista completa)
✅ Puede ver cualquier producto completo
✅ Puede ver cualquier flujo detallado
✅ Puede cambiar entre productos libremente
```

### Diseñador
```
✅ Puede ver SOLO sus productos asignados
✅ Puede ver sus productos completos
✅ Puede ver los flujos detallados de sus productos
❌ NO puede ver productos de otros diseñadores
```

**Ejemplo**:
- María (Diseñadora) tiene asignado: "Crédito de Vehículo"
  - ✅ Ve: Crédito de Vehículo completo
  - ✅ Ve: Flujo Colocación detallado
  - ✅ Ve: Flujo Legalización detallado
  - ❌ NO ve: "Tarjeta de Crédito" (asignado a Carlos)

## 8. Formulario de Registro de Valores

### 8.1 Formulario Individual por Métrica

```
┌─────────────────────────────────────────────────┐
│ Registrar Valor Mensual                         │
├─────────────────────────────────────────────────┤
│                                                 │
│ Producto: Crédito de Vehículo (fijo)            │
│ Flujo: Colocación (fijo)                        │
│                                                 │
│ Métrica: [Solicitudes Recibidas ▼]       ℹ️    │
│                                                 │
│ 💡 Ayuda: Solicitudes Recibidas                │
│ Tipo: Número                                    │
│ Descripción: Total de solicitudes de crédito   │
│ recibidas en el mes                             │
│                                                 │
│ Mes: [Abril 2025 📅]                            │
│                                                 │
│ Valor: [_______]                                │
│                                                 │
│ Observaciones (opcional):                       │
│ [_________________________________________]     │
│ [_________________________________________]     │
│                                                 │
│ Fuente de datos (opcional):                     │
│ [Sistema CRM ▼]                                 │
│                                                 │
│ [Cancelar] [Guardar]                            │
└─────────────────────────────────────────────────┘
```

### 8.2 Formulario Masivo por Flujo (Opción Alternativa)

```
┌─────────────────────────────────────────────────┐
│ Registrar Valores del Mes - Abril 2025          │
├─────────────────────────────────────────────────┤
│                                                 │
│ Producto: Crédito de Vehículo                   │
│ Flujo: Colocación                               │
│ Diseñador: María González                       │
│                                                 │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   │
│                                                 │
│ 💰 MÉTRICAS DE NEGOCIO                          │
│                                                 │
│ Solicitudes Recibidas:                          │
│ [_______] (número)                         ℹ️   │
│                                                 │
│ Tasa de Aprobación:                             │
│ [_______] %                                ℹ️   │
│                                                 │
│ Ticket Promedio:                                │
│ $[_______]                                 ℹ️   │
│                                                 │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   │
│                                                 │
│ 😊 MÉTRICAS DE EXPERIENCIA                      │
│                                                 │
│ Tiempo de Evaluación:                           │
│ [_______] minutos                          ℹ️   │
│                                                 │
│ CSAT Proceso:                                   │
│ [_______] (0-100)                          ℹ️   │
│                                                 │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━   │
│                                                 │
│ Observaciones generales del mes:                │
│ [_________________________________________]     │
│ [_________________________________________]     │
│                                                 │
│ [Cancelar] [Guardar Todo]                       │
└─────────────────────────────────────────────────┘
```

## 9. Widgets Informativos

### 9.1 Widget de Estado del Producto

```
┌──────────────────────────────────────────┐
│ Estado: Crédito de Vehículo              │
├──────────────────────────────────────────┤
│                                          │
│ 📊 Completitud Abril 2025:               │
│                                          │
│ ████████████████░░░░ 80%                 │
│                                          │
│ Métricas registradas: 8 / 10             │
│                                          │
│ Pendientes:                              │
│ • Flujo Legalización: 2 métricas         │
│                                          │
│ [Registrar Pendientes]                   │
└──────────────────────────────────────────┘
```

### 9.2 Widget de Cumplimiento

```
┌──────────────────────────────────────────┐
│ Cumplimiento de Targets                  │
├──────────────────────────────────────────┤
│                                          │
│ ✅ Cumpliendo: 8 (80%)                   │
│ ⚠️  Alerta: 1 (10%)                      │
│ ❌ Crítico: 1 (10%)                      │
│                                          │
│ Desglose por flujo:                      │
│                                          │
│ Colocación:     80% ████████░░           │
│ Legalización:   100% ██████████          │
│                                          │
└──────────────────────────────────────────┘
```

## 10. Comparación de Flujos (Vista Producto)

```
╔════════════════════════════════════════════════════╗
║  Comparación de Flujos - Crédito de Vehículo      ║
╠════════════════════════════════════════════════════╣
║                                                    ║
║  Cumplimiento de Targets (Últimos 6 meses)        ║
║                                                    ║
║  100%┤                              ● ● ● Legal.  ║
║   90%┤                        ● ●                  ║
║   80%┤           ● ● ● ●                  Coloc.  ║
║   70%┤     ● ●                                     ║
║   60%┤                                             ║
║      └──┬────┬────┬────┬────┬────┬────             ║
║        Nov  Dic  Ene  Feb  Mar  Abr               ║
║                                                    ║
║  Insight: Legalización tiene mejor desempeño      ║
║  consistente. Colocación mejoró últimos 3 meses.  ║
║                                                    ║
╚════════════════════════════════════════════════════╝
```

## 11. Exportación

### 11.1 Exportar Producto Completo

```json
{
  "producto": {
    "codigo": "credito-vehiculo",
    "nombre": "Crédito de Vehículo",
    "diseñador": "María González"
  },
  "periodo": "2025-04",
  "flujos": [
    {
      "nombre": "Colocación",
      "metricas": [
        {
          "nombre": "Solicitudes Recibidas",
          "tipo": "Negocio",
          "valor": 1250,
          "target": 1000,
          "cumple_target": true,
          "cambio_vs_anterior": "+15%"
        }
        // ... más métricas
      ]
    },
    {
      "nombre": "Legalización",
      "metricas": [...]
    }
  ],
  "resumen": {
    "total_flujos": 2,
    "total_metricas": 10,
    "metricas_cumpliendo": 8,
    "porcentaje_cumplimiento": 80
  }
}
```

### 11.2 Exportar Flujo Específico

```csv
Producto,Flujo,Métrica,Tipo,Periodo,Valor,Target,Cumple,Cambio_vs_Ant,Diseñador
Crédito de Vehículo,Colocación,Solicitudes Recibidas,Negocio,2025-04,1250,1000,Sí,+15%,María González
Crédito de Vehículo,Colocación,Tasa de Aprobación,Negocio,2025-04,68.5,65,Sí,+2.5%,María González
```

## 12. Mejores Prácticas de Visualización

### Principios de Diseño

1. **Jerarquía Visual Clara**
   - Vista Producto: panorámica rápida
   - Vista Flujo: análisis profundo

2. **Información Progresiva**
   - No abrumar con todos los datos a la vez
   - Permitir drill-down (ir más profundo)

3. **Código de Colores Consistente**
   - 🟢 Verde: Cumpliendo target
   - 🟡 Amarillo: Alerta (cerca del límite)
   - 🔴 Rojo: Crítico (no cumple)
   - 🔵 Azul: Informativo

4. **Feedback Inmediato**
   - Mostrar si cumple target visualmente
   - Comparación vs mes anterior
   - Tendencia (↗️ subiendo, ↘️ bajando, ➡️ estable)

5. **Contexto Siempre Visible**
   - Nombre del producto
   - Nombre del diseñador
   - Periodo actual
   - Breadcrumbs de navegación

## 13. Responsive Design

### Desktop (> 1024px)
- Vista completa con todos los flujos lado a lado
- Gráficos grandes
- Tablas expandidas

### Tablet (768px - 1024px)
- Flujos apilados verticalmente
- Gráficos medianos
- Tablas con scroll horizontal

### Mobile (< 768px)
- Un flujo a la vez
- Gráficos pequeños
- Tablas colapsables
- Navegación con tabs

## 14. Alertas y Notificaciones

### En Dashboard

```
┌──────────────────────────────────────────┐
│ ⚠️  Alertas (3)                          │
├──────────────────────────────────────────┤
│                                          │
│ • CSAT Proceso (Colocación) bajó a 82.5 │
│   Por debajo de target (85)              │
│   [Ver →]                                │
│                                          │
│ • Tiempo Legalización subió a 12 días   │
│   Por encima de target (10 días)         │
│   [Ver →]                                │
│                                          │
│ • Faltan registrar 2 métricas de Abril  │
│   [Registrar →]                          │
│                                          │
└──────────────────────────────────────────┘
```

## 15. Casos de Uso Completos

### Caso 1: Diseñador Revisa su Producto

```
1. María hace login
2. Ve "Mis Productos" (solo Crédito de Vehículo)
3. Click en "Crédito de Vehículo"
4. Ve vista completa con ambos flujos
5. Identifica que CSAT Colocación bajó (⚠️)
6. Click en "Ver Detalle" del flujo Colocación
7. Ve gráfico detallado y análisis
8. Lee recomendación del sistema
9. Agrega observación para seguimiento
```

### Caso 2: Diseñador Registra Valores Mensuales

```
1. María entra a Crédito de Vehículo
2. Sistema muestra: "Faltan 5 métricas de Abril"
3. Click en "Registrar Valores"
4. Selecciona Flujo: Colocación
5. Llena formulario con valores de Abril
6. Sistema valida rangos en tiempo real
7. Guarda valores
8. Dashboard se actualiza automáticamente
9. Ve nuevos gráficos con datos de Abril
```

### Caso 3: Admin Supervisa Todos los Productos

```
1. Admin hace login
2. Ve lista de TODOS los productos
3. Puede filtrar por diseñador
4. Ve productos con alertas primero
5. Entra a cualquier producto
6. Revisa flujos que necesitan atención
7. Puede reasignar productos si necesario
```

---

**Notas importantes**:

- Todo es **mensual** (no diario)
- Dos niveles: **Producto Completo** y **Flujo Detallado**
- **Admin** ve todo, **Diseñador** ve solo lo suyo
- Visualización diseñada para **toma de decisiones**
- **Insights automáticos** ayudan a interpretar datos
