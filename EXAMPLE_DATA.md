# Ejemplo de Datos - Dashboard de Métricas

Este documento muestra ejemplos concretos de cómo se estructurarían los datos en el sistema para diferentes tipos de productos digitales.

## Ejemplo 1: E-commerce Web

### Producto
```json
{
  "codigo": "ecommerce-web",
  "nombre": "E-commerce Web",
  "descripcion": "Tienda en línea principal de la compañía",
  "color_hex": "#10B981",
  "metadata": {
    "departamento": "Ventas",
    "responsable": "María González",
    "url_produccion": "https://tienda.example.com",
    "tecnologias": ["Next.js", "Shopify", "Stripe"]
  }
}
```

### Flujo 1: Adquisición

#### Métricas:

**1. Costo por Adquisición (CAC)**
- Tipo: Negocio
- Unidad: dinero
- Target: $25.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 23.50, "observaciones": "Campaña de Facebook efectiva" },
    { "periodo": "2025-02", "valor": 28.00, "observaciones": "Mes alto por San Valentín" },
    { "periodo": "2025-01", "valor": 26.00, "observaciones": null }
  ]
}
```

**2. Tasa de Rebote**
- Tipo: Experiencia
- Unidad: porcentaje
- Target: 40.00 (menor es mejor)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 38.5, "observaciones": "Mejora en velocidad de carga" },
    { "periodo": "2025-02", "valor": 42.0, "observaciones": null },
    { "periodo": "2025-01", "valor": 45.0, "observaciones": "Mes alto por tráfico de anuncios" }
  ]
}
```

**3. Nuevos Usuarios**
- Tipo: Producto
- Unidad: numero
- Target: 5000

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 5800, "observaciones": "Campaña viral en redes" },
    { "periodo": "2025-02", "valor": 4200, "observaciones": null },
    { "periodo": "2025-01", "valor": 3900, "observaciones": null }
  ]
}
```

### Flujo 2: Conversión

#### Métricas:

**1. Tasa de Conversión**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 3.50

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 3.8, "observaciones": "Optimización del checkout" },
    { "periodo": "2025-02", "valor": 3.5, "observaciones": "Cumplió target" },
    { "periodo": "2025-01", "valor": 3.2, "observaciones": "Mes post-navidad bajo" }
  ]
}
```

**2. Ticket Promedio**
- Tipo: Negocio
- Unidad: dinero
- Target: 85.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 92.50, "observaciones": "Promoción de productos premium" },
    { "periodo": "2025-02", "valor": 88.00, "observaciones": "San Valentín aumentó ticket" },
    { "periodo": "2025-01", "valor": 78.00, "observaciones": "Enero tradicionalmente bajo" }
  ]
}
```

**3. Ingresos Totales**
- Tipo: Negocio
- Unidad: dinero
- Target: 150000.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 168500.00, "observaciones": "Mejor mes del trimestre" },
    { "periodo": "2025-02", "valor": 145000.00, "observaciones": "Cercano a target" },
    { "periodo": "2025-01", "valor": 132000.00, "observaciones": "Mes de recuperación" }
  ]
}
```

### Flujo 3: Retención

#### Métricas:

**1. Tasa de Retención**
- Tipo: Producto
- Unidad: porcentaje
- Target: 65.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 68.5, "observaciones": "Programa de fidelidad funcionando" },
    { "periodo": "2025-02", "valor": 66.0, "observaciones": null },
    { "periodo": "2025-01", "valor": 63.0, "observaciones": "Por debajo de target" }
  ]
}
```

**2. NPS (Net Promoter Score)**
- Tipo: Experiencia
- Unidad: score
- Target: 50.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 55, "observaciones": "Encuesta a 500 clientes" },
    { "periodo": "2025-02", "valor": 48, "observaciones": "Problemas de envío afectaron" },
    { "periodo": "2025-01", "valor": 52, "observaciones": null }
  ]
}
```

---

## Ejemplo 2: Aplicación Móvil SaaS

### Producto
```json
{
  "codigo": "app-fitness",
  "nombre": "App Fitness Pro",
  "descripcion": "Aplicación móvil de entrenamiento personalizado",
  "color_hex": "#F59E0B",
  "metadata": {
    "departamento": "Producto",
    "responsable": "Carlos Ruiz",
    "plataformas": ["iOS", "Android"],
    "tecnologias": ["React Native", "Firebase", "Stripe"]
  }
}
```

### Flujo 1: Activación

#### Métricas:

**1. Tasa de Activación**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 40.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 42.5, "observaciones": "Nuevo onboarding mejoró activación" },
    { "periodo": "2025-02", "valor": 38.0, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 35.5, "observaciones": "Mes bajo" }
  ]
}
```

**2. Tiempo hasta Primera Rutina**
- Tipo: Experiencia
- Unidad: tiempo_seg
- Target: 180.00 (3 minutos)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 165, "observaciones": "Onboarding simplificado" },
    { "periodo": "2025-02", "valor": 210, "observaciones": "Onboarding complejo" },
    { "periodo": "2025-01", "valor": 225, "observaciones": null }
  ]
}
```

### Flujo 2: Engagement

#### Métricas:

**1. Usuarios Activos Diarios (DAU)**
- Tipo: Producto
- Unidad: numero
- Target: 15000

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 16800, "observaciones": "Crecimiento sostenido" },
    { "periodo": "2025-02", "valor": 15200, "observaciones": "Cumplió target" },
    { "periodo": "2025-01", "valor": 14500, "observaciones": "Propósitos de año nuevo" }
  ]
}
```

**2. Sesiones por Usuario**
- Tipo: Producto
- Unidad: numero
- Target: 4.50

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 4.8, "observaciones": "Notificaciones push mejoraron" },
    { "periodo": "2025-02", "valor": 4.2, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 5.2, "observaciones": "Enero alto por propósitos" }
  ]
}
```

**3. Tiempo Promedio en App**
- Tipo: Experiencia
- Unidad: tiempo_seg
- Target: 1200.00 (20 minutos)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 1380, "observaciones": "23 minutos promedio" },
    { "periodo": "2025-02", "valor": 1140, "observaciones": "19 minutos" },
    { "periodo": "2025-01", "valor": 1500, "observaciones": "25 minutos - enero alto" }
  ]
}
```

### Flujo 3: Monetización

#### Métricas:

**1. Tasa de Conversión a Premium**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 8.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 9.2, "observaciones": "Promoción 30% OFF efectiva" },
    { "periodo": "2025-02", "valor": 7.5, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 11.5, "observaciones": "Enero alto por propósitos" }
  ]
}
```

**2. MRR (Monthly Recurring Revenue)**
- Tipo: Negocio
- Unidad: dinero
- Target: 45000.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 48200, "observaciones": "Crecimiento del 7% vs mes anterior" },
    { "periodo": "2025-02", "valor": 45000, "observaciones": "Cumplió target exacto" },
    { "periodo": "2025-01", "valor": 52000, "observaciones": "Enero excepcionalmente alto" }
  ]
}
```

**3. Churn Rate**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 5.00 (menor es mejor)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 4.2, "observaciones": "Mejoras en producto redujeron churn" },
    { "periodo": "2025-02", "valor": 5.8, "observaciones": "Cancelaciones post-enero" },
    { "periodo": "2025-01", "valor": 3.5, "observaciones": "Enero siempre bajo en churn" }
  ]
}
```

---

## Ejemplo 3: Plataforma B2B SaaS

### Producto
```json
{
  "codigo": "crm-platform",
  "nombre": "CRM Platform Enterprise",
  "descripcion": "Plataforma CRM para empresas medianas y grandes",
  "color_hex": "#3B82F6",
  "metadata": {
    "departamento": "Enterprise",
    "responsable": "Ana Martínez",
    "modelo_negocio": "B2B SaaS",
    "tecnologias": ["React", "Node.js", "MongoDB", "AWS"]
  }
}
```

### Flujo 1: Adquisición Enterprise

#### Métricas:

**1. Leads Calificados (SQL)**
- Tipo: Negocio
- Unidad: numero
- Target: 50

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 58, "observaciones": "Evento de industria generó leads" },
    { "periodo": "2025-02", "valor": 45, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 42, "observaciones": "Enero tradicionalmente bajo" }
  ]
}
```

**2. Tasa de Conversión Lead-to-Customer**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 15.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 17.5, "observaciones": "Equipo de ventas excelente" },
    { "periodo": "2025-02", "valor": 14.0, "observaciones": "Ligeramente por debajo" },
    { "periodo": "2025-01", "valor": 13.5, "observaciones": null }
  ]
}
```

**3. Ciclo de Venta Promedio**
- Tipo: Experiencia
- Unidad: numero
- Target: 45.00 (días)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 42, "observaciones": "Proceso optimizado funcionando" },
    { "periodo": "2025-02", "valor": 48, "observaciones": "Ligeramente alto" },
    { "periodo": "2025-01", "valor": 50, "observaciones": "Navidad retrasó decisiones" }
  ]
}
```

### Flujo 2: Onboarding

#### Métricas:

**1. Tiempo hasta Go-Live**
- Tipo: Experiencia
- Unidad: numero
- Target: 30.00 (días)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 28, "observaciones": "Nuevo equipo de onboarding" },
    { "periodo": "2025-02", "valor": 35, "observaciones": "Por encima de target" },
    { "periodo": "2025-01", "valor": 32, "observaciones": null }
  ]
}
```

**2. CSAT Onboarding**
- Tipo: Experiencia
- Unidad: score
- Target: 85.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 88, "observaciones": "Feedback excelente" },
    { "periodo": "2025-02", "valor": 82, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 86, "observaciones": null }
  ]
}
```

### Flujo 3: Retención y Expansión

#### Métricas:

**1. Net Retention Rate**
- Tipo: Negocio
- Unidad: porcentaje
- Target: 110.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 115, "observaciones": "Upsells superaron churn" },
    { "periodo": "2025-02", "valor": 108, "observaciones": "Ligeramente bajo" },
    { "periodo": "2025-01", "valor": 112, "observaciones": "Buen mes" }
  ]
}
```

**2. Tasa de Adopción de Features**
- Tipo: Producto
- Unidad: porcentaje
- Target: 70.00

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 75, "observaciones": "Webinars aumentaron adopción" },
    { "periodo": "2025-02", "valor": 68, "observaciones": "Por debajo de target" },
    { "periodo": "2025-01", "valor": 72, "observaciones": null }
  ]
}
```

**3. Support Tickets por Cliente**
- Tipo: Experiencia
- Unidad: numero
- Target: 2.00 (menor es mejor)

```json
{
  "valores_mensuales": [
    { "periodo": "2025-03", "valor": 1.8, "observaciones": "Documentación mejorada" },
    { "periodo": "2025-02", "valor": 2.5, "observaciones": "Bug importante generó tickets" },
    { "periodo": "2025-01", "valor": 2.1, "observaciones": null }
  ]
}
```

---

## Visualización en Dashboard

Así se vería el dashboard para "E-commerce Web":

```
╔════════════════════════════════════════════════════════════╗
║  E-commerce Web                                     [Export]║
║  Tienda en línea principal                                 ║
╠════════════════════════════════════════════════════════════╣
║                                                             ║
║  📈 FLUJO: ADQUISICIÓN                                     ║
║  ┌──────────────────┐ ┌──────────────────┐ ┌─────────────┐║
║  │ CAC              │ │ Tasa de Rebote   │ │ Nuevos      │║
║  │ Negocio          │ │ Experiencia      │ │ Usuarios    │║
║  │                  │ │                  │ │ Producto    │║
║  │ $23.50           │ │ 38.5%            │ │ 5,800       │║
║  │ ✅ -12.5% vs ant │ │ ✅ -8.3% vs ant  │ │ ✅ +38% ant │║
║  │ Target: $25      │ │ Target: <40%     │ │ Target: 5K  │║
║  │ [Gráfico línea]  │ │ [Gráfico línea]  │ │ [Gráfico]   │║
║  └──────────────────┘ └──────────────────┘ └─────────────┘║
║                                                             ║
║  💰 FLUJO: CONVERSIÓN                                      ║
║  ┌──────────────────┐ ┌──────────────────┐ ┌─────────────┐║
║  │ Tasa Conversión  │ │ Ticket Promedio  │ │ Ingresos    │║
║  │ Negocio          │ │ Negocio          │ │ Totales     │║
║  │                  │ │                  │ │ Negocio     │║
║  │ 3.8%             │ │ $92.50           │ │ $168,500    │║
║  │ ✅ +8.6% vs ant  │ │ ✅ +5.1% vs ant  │ │ ✅ +16.2%   │║
║  │ Target: 3.5%     │ │ Target: $85      │ │ Target: 150K│║
║  │ [Gráfico línea]  │ │ [Gráfico línea]  │ │ [Gráfico]   │║
║  └──────────────────┘ └──────────────────┘ └─────────────┘║
║                                                             ║
║  🔄 FLUJO: RETENCIÓN                                       ║
║  ┌──────────────────┐ ┌──────────────────┐                ║
║  │ Retención        │ │ NPS              │                ║
║  │ Producto         │ │ Experiencia      │                ║
║  │                  │ │                  │                ║
║  │ 68.5%            │ │ 55               │                ║
║  │ ✅ +3.8% vs ant  │ │ ✅ +14.6% vs ant │                ║
║  │ Target: 65%      │ │ Target: 50       │                ║
║  │ [Gráfico línea]  │ │ [Gráfico línea]  │                ║
║  └──────────────────┘ └──────────────────┘                ║
╚════════════════════════════════════════════════════════════╝
```

## Exportación para Looker

Ejemplo de JSON exportado para integración con Looker:

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
            { "periodo": "2025-03-01", "valor": 3.8 },
            { "periodo": "2025-02-01", "valor": 3.5 },
            { "periodo": "2025-01-01", "valor": 3.2 }
          ]
        }
      ]
    }
  ],
  "metadata": {
    "exportado_en": "2025-03-16T10:30:00Z",
    "total_flujos": 3,
    "total_metricas": 8
  }
}
```

## Resumen de Métricas por Tipo

### Por Producto "E-commerce Web":

| Tipo | Total Métricas | Cumpliendo Target | % Cumplimiento |
|------|----------------|-------------------|----------------|
| Negocio | 5 | 4 | 80% |
| Experiencia | 2 | 2 | 100% |
| Producto | 2 | 2 | 100% |
| **TOTAL** | **9** | **8** | **89%** |

Este es un ejemplo completo de cómo se estructurarían y visualizarían los datos en el sistema.
