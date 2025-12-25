# Arquitectura del Backend

## 1. Visión General

Backend API REST construido con Node.js, TypeScript y Express, siguiendo principios de Clean Architecture y SOLID. Provee endpoints para gestionar productos, flujos, métricas y sus valores mensuales, además de endpoints especializados para integración con herramientas de BI.

## 2. Stack Tecnológico

### 2.1 Core
- **Runtime**: Node.js 20+ LTS
- **Lenguaje**: TypeScript 5+
- **Framework**: Express.js 4.18+
- **ORM**: Prisma 5+
- **Validación**: Zod
- **Autenticación**: JWT (jsonwebtoken)

### 2.2 Utilidades
- **Logger**: Winston
- **Documentación**: Swagger/OpenAPI
- **Testing**: Jest + Supertest
- **Linter**: ESLint
- **Formatter**: Prettier
- **Process Manager**: PM2 (producción)

### 2.3 Base de Datos
- **PostgreSQL**: 15+
- **Migrations**: Prisma Migrate
- **Seeding**: Prisma Seed

## 3. Estructura del Proyecto

```
backend/
├── src/
│   ├── config/                 # Configuración
│   │   ├── database.ts
│   │   ├── environment.ts
│   │   └── swagger.ts
│   │
│   ├── modules/               # Módulos por dominio
│   │   ├── productos/
│   │   │   ├── controllers/
│   │   │   │   └── productos.controller.ts
│   │   │   ├── services/
│   │   │   │   └── productos.service.ts
│   │   │   ├── repositories/
│   │   │   │   └── productos.repository.ts
│   │   │   ├── dto/
│   │   │   │   ├── create-producto.dto.ts
│   │   │   │   └── update-producto.dto.ts
│   │   │   ├── models/
│   │   │   │   └── producto.model.ts
│   │   │   └── routes/
│   │   │       └── productos.routes.ts
│   │   │
│   │   ├── flujos/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── repositories/
│   │   │   ├── dto/
│   │   │   └── routes/
│   │   │
│   │   ├── metricas/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── repositories/
│   │   │   ├── dto/
│   │   │   └── routes/
│   │   │
│   │   ├── valores/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── repositories/
│   │   │   ├── dto/
│   │   │   └── routes/
│   │   │
│   │   ├── export/            # Exportación para BI
│   │   │   ├── controllers/
│   │   │   │   └── export.controller.ts
│   │   │   ├── services/
│   │   │   │   ├── json-export.service.ts
│   │   │   │   ├── csv-export.service.ts
│   │   │   │   └── excel-export.service.ts
│   │   │   └── routes/
│   │   │       └── export.routes.ts
│   │   │
│   │   └── auth/              # Autenticación
│   │       ├── controllers/
│   │       ├── services/
│   │       ├── middleware/
│   │       └── routes/
│   │
│   ├── shared/                # Código compartido
│   │   ├── middleware/
│   │   │   ├── error-handler.middleware.ts
│   │   │   ├── validate.middleware.ts
│   │   │   ├── auth.middleware.ts
│   │   │   ├── rate-limit.middleware.ts
│   │   │   └── logger.middleware.ts
│   │   │
│   │   ├── utils/
│   │   │   ├── logger.ts
│   │   │   ├── response.ts
│   │   │   └── date.ts
│   │   │
│   │   ├── types/
│   │   │   ├── express.d.ts
│   │   │   └── common.types.ts
│   │   │
│   │   └── constants/
│   │       ├── errors.ts
│   │       └── messages.ts
│   │
│   ├── prisma/                # Prisma schema y migrations
│   │   ├── schema.prisma
│   │   ├── migrations/
│   │   └── seed.ts
│   │
│   ├── app.ts                 # Configuración de Express
│   └── server.ts              # Punto de entrada
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── docs/
│   └── swagger/
│
├── .env.example
├── .eslintrc.js
├── .prettierrc
├── tsconfig.json
├── package.json
└── README.md
```

## 4. Arquitectura en Capas

### 4.1 Capa de Rutas (Routes)

Define los endpoints y los conecta con los controladores.

```typescript
// src/modules/productos/routes/productos.routes.ts
import { Router } from 'express';
import { ProductosController } from '../controllers/productos.controller';
import { authenticate } from '@/shared/middleware/auth.middleware';
import { validate } from '@/shared/middleware/validate.middleware';
import { CreateProductoDto, UpdateProductoDto } from '../dto';

const router = Router();
const controller = new ProductosController();

router.get('/', authenticate, controller.getAll);
router.get('/:id', authenticate, controller.getById);
router.post('/', authenticate, validate(CreateProductoDto), controller.create);
router.put('/:id', authenticate, validate(UpdateProductoDto), controller.update);
router.delete('/:id', authenticate, controller.delete);

// Endpoint específico: obtener producto con todos sus flujos y métricas
router.get('/:id/complete', authenticate, controller.getCompleteProduct);

export default router;
```

### 4.2 Capa de Controladores (Controllers)

Maneja las peticiones HTTP y las respuestas.

```typescript
// src/modules/productos/controllers/productos.controller.ts
import { Request, Response, NextFunction } from 'express';
import { ProductosService } from '../services/productos.service';
import { successResponse, errorResponse } from '@/shared/utils/response';
import { StatusCodes } from 'http-status-codes';

export class ProductosController {
  private service: ProductosService;

  constructor() {
    this.service = new ProductosService();
  }

  getAll = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { activo, search } = req.query;
      const productos = await this.service.getAll({
        activo: activo === 'true',
        search: search as string,
      });

      return successResponse(res, productos, 'Productos obtenidos exitosamente');
    } catch (error) {
      next(error);
    }
  };

  getById = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params;
      const producto = await this.service.getById(Number(id));

      if (!producto) {
        return errorResponse(res, 'Producto no encontrado', StatusCodes.NOT_FOUND);
      }

      return successResponse(res, producto);
    } catch (error) {
      next(error);
    }
  };

  create = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const producto = await this.service.create(req.body);
      return successResponse(res, producto, 'Producto creado exitosamente', StatusCodes.CREATED);
    } catch (error) {
      next(error);
    }
  };

  update = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params;
      const producto = await this.service.update(Number(id), req.body);
      return successResponse(res, producto, 'Producto actualizado exitosamente');
    } catch (error) {
      next(error);
    }
  };

  delete = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params;
      await this.service.delete(Number(id));
      return successResponse(res, null, 'Producto eliminado exitosamente', StatusCodes.NO_CONTENT);
    } catch (error) {
      next(error);
    }
  };

  getCompleteProduct = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params;
      const producto = await this.service.getCompleteProduct(Number(id));

      if (!producto) {
        return errorResponse(res, 'Producto no encontrado', StatusCodes.NOT_FOUND);
      }

      return successResponse(res, producto);
    } catch (error) {
      next(error);
    }
  };
}
```

### 4.3 Capa de Servicios (Services)

Contiene la lógica de negocio.

```typescript
// src/modules/productos/services/productos.service.ts
import { ProductosRepository } from '../repositories/productos.repository';
import { CreateProductoDto, UpdateProductoDto } from '../dto';
import { AppError } from '@/shared/utils/errors';
import { StatusCodes } from 'http-status-codes';

export class ProductosService {
  private repository: ProductosRepository;

  constructor() {
    this.repository = new ProductosRepository();
  }

  async getAll(filters?: { activo?: boolean; search?: string }) {
    return await this.repository.findAll(filters);
  }

  async getById(id: number) {
    return await this.repository.findById(id);
  }

  async create(data: CreateProductoDto) {
    // Validar que no exista un producto con el mismo código
    const existing = await this.repository.findByCode(data.codigo);
    if (existing) {
      throw new AppError('Ya existe un producto con ese código', StatusCodes.CONFLICT);
    }

    return await this.repository.create(data);
  }

  async update(id: number, data: UpdateProductoDto) {
    // Verificar que el producto existe
    const producto = await this.repository.findById(id);
    if (!producto) {
      throw new AppError('Producto no encontrado', StatusCodes.NOT_FOUND);
    }

    // Si se cambia el código, validar que no exista
    if (data.codigo && data.codigo !== producto.codigo) {
      const existing = await this.repository.findByCode(data.codigo);
      if (existing) {
        throw new AppError('Ya existe un producto con ese código', StatusCodes.CONFLICT);
      }
    }

    return await this.repository.update(id, data);
  }

  async delete(id: number) {
    const producto = await this.repository.findById(id);
    if (!producto) {
      throw new AppError('Producto no encontrado', StatusCodes.NOT_FOUND);
    }

    // Soft delete
    return await this.repository.update(id, { activo: false });
  }

  async getCompleteProduct(id: number) {
    return await this.repository.findByIdWithRelations(id);
  }
}
```

### 4.4 Capa de Repositorios (Repositories)

Abstrae el acceso a la base de datos.

```typescript
// src/modules/productos/repositories/productos.repository.ts
import { PrismaClient } from '@prisma/client';
import { CreateProductoDto, UpdateProductoDto } from '../dto';

const prisma = new PrismaClient();

export class ProductosRepository {
  async findAll(filters?: { activo?: boolean; search?: string }) {
    return await prisma.producto.findMany({
      where: {
        ...(filters?.activo !== undefined && { activo: filters.activo }),
        ...(filters?.search && {
          OR: [
            { nombre: { contains: filters.search, mode: 'insensitive' } },
            { codigo: { contains: filters.search, mode: 'insensitive' } },
          ],
        }),
      },
      orderBy: { nombre: 'asc' },
    });
  }

  async findById(id: number) {
    return await prisma.producto.findUnique({
      where: { id },
    });
  }

  async findByCode(codigo: string) {
    return await prisma.producto.findUnique({
      where: { codigo },
    });
  }

  async create(data: CreateProductoDto) {
    return await prisma.producto.create({
      data,
    });
  }

  async update(id: number, data: UpdateProductoDto) {
    return await prisma.producto.update({
      where: { id },
      data,
    });
  }

  async findByIdWithRelations(id: number) {
    return await prisma.producto.findUnique({
      where: { id },
      include: {
        flujos: {
          where: { activo: true },
          orderBy: { orden: 'asc' },
          include: {
            metricas: {
              where: { activo: true },
              orderBy: { orden: 'asc' },
              include: {
                tipo_metrica: true,
                valores_mensuales: {
                  orderBy: { periodo: 'desc' },
                  take: 12, // Últimos 12 meses
                },
              },
            },
          },
        },
      },
    });
  }
}
```

### 4.5 DTOs (Data Transfer Objects)

Definen y validan la estructura de datos de entrada.

```typescript
// src/modules/productos/dto/create-producto.dto.ts
import { z } from 'zod';

export const CreateProductoDto = z.object({
  codigo: z.string()
    .min(2, 'El código debe tener al menos 2 caracteres')
    .max(50, 'El código no puede superar los 50 caracteres')
    .regex(/^[a-z0-9_-]+$/, 'El código solo puede contener letras minúsculas, números, guiones y guiones bajos')
    .transform(val => val.toLowerCase()),

  nombre: z.string()
    .min(3, 'El nombre debe tener al menos 3 caracteres')
    .max(200, 'El nombre no puede superar los 200 caracteres'),

  descripcion: z.string().optional(),

  metadata: z.record(z.any()).optional(),

  icono_url: z.string().url('La URL del ícono no es válida').optional(),

  color_hex: z.string()
    .regex(/^#[0-9A-F]{6}$/i, 'El color debe estar en formato hexadecimal (#RRGGBB)')
    .default('#3B82F6'),

  activo: z.boolean().default(true),
});

export type CreateProductoDto = z.infer<typeof CreateProductoDto>;
```

```typescript
// src/modules/productos/dto/update-producto.dto.ts
import { z } from 'zod';
import { CreateProductoDto } from './create-producto.dto';

export const UpdateProductoDto = CreateProductoDto.partial();

export type UpdateProductoDto = z.infer<typeof UpdateProductoDto>;
```

## 5. Middleware

### 5.1 Autenticación

```typescript
// src/shared/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';
import { AppError } from '@/shared/utils/errors';
import { StatusCodes } from 'http-status-codes';

export const authenticate = (req: Request, res: Response, next: NextFunction) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');

    if (!token) {
      throw new AppError('Token no proporcionado', StatusCodes.UNAUTHORIZED);
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET!);
    req.user = decoded;

    next();
  } catch (error) {
    next(new AppError('Token inválido o expirado', StatusCodes.UNAUTHORIZED));
  }
};
```

### 5.2 Validación

```typescript
// src/shared/middleware/validate.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { ZodSchema } from 'zod';
import { errorResponse } from '@/shared/utils/response';
import { StatusCodes } from 'http-status-codes';

export const validate = (schema: ZodSchema) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      req.body = await schema.parseAsync(req.body);
      next();
    } catch (error: any) {
      const errors = error.errors?.map((err: any) => ({
        field: err.path.join('.'),
        message: err.message,
      }));

      return errorResponse(
        res,
        'Error de validación',
        StatusCodes.BAD_REQUEST,
        errors
      );
    }
  };
};
```

### 5.3 Manejo de Errores

```typescript
// src/shared/middleware/error-handler.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { StatusCodes } from 'http-status-codes';
import { logger } from '@/shared/utils/logger';

export class AppError extends Error {
  statusCode: number;
  isOperational: boolean;

  constructor(message: string, statusCode: number = StatusCodes.INTERNAL_SERVER_ERROR) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

export const errorHandler = (
  err: Error | AppError,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  let statusCode = StatusCodes.INTERNAL_SERVER_ERROR;
  let message = 'Error interno del servidor';

  if (err instanceof AppError) {
    statusCode = err.statusCode;
    message = err.message;
  }

  logger.error('Error:', {
    message: err.message,
    stack: err.stack,
    statusCode,
    path: req.path,
    method: req.method,
  });

  res.status(statusCode).json({
    success: false,
    message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
};
```

### 5.4 Rate Limiting

```typescript
// src/shared/middleware/rate-limit.middleware.ts
import rateLimit from 'express-rate-limit';

export const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // 100 requests por ventana
  message: 'Demasiadas solicitudes desde esta IP, intente más tarde',
});

export const exportLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hora
  max: 10, // 10 exportaciones por hora
  message: 'Límite de exportaciones alcanzado, intente más tarde',
});
```

## 6. Servicios de Exportación para BI

### 6.1 Servicio de Exportación JSON

```typescript
// src/modules/export/services/json-export.service.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export class JsonExportService {
  async exportProductMetrics(productCode: string, startDate?: Date, endDate?: Date) {
    const producto = await prisma.producto.findUnique({
      where: { codigo: productCode },
      include: {
        flujos: {
          where: { activo: true },
          include: {
            metricas: {
              where: { activo: true },
              include: {
                tipo_metrica: true,
                valores_mensuales: {
                  where: {
                    ...(startDate && { periodo: { gte: startDate } }),
                    ...(endDate && { periodo: { lte: endDate } }),
                  },
                  orderBy: { periodo: 'asc' },
                },
              },
            },
          },
        },
      },
    });

    if (!producto) {
      throw new Error('Producto no encontrado');
    }

    // Transformar a formato óptimo para BI
    return {
      producto: {
        codigo: producto.codigo,
        nombre: producto.nombre,
      },
      flujos: producto.flujos.map(flujo => ({
        nombre: flujo.nombre,
        metricas: flujo.metricas.map(metrica => ({
          codigo: metrica.codigo,
          nombre: metrica.nombre,
          tipo: metrica.tipo_metrica.nombre,
          unidad: metrica.unidad_medida,
          valores: metrica.valores_mensuales.map(valor => ({
            periodo: valor.periodo,
            valor: Number(valor.valor),
            observaciones: valor.observaciones,
          })),
        })),
      })),
      metadata: {
        exportado_en: new Date().toISOString(),
        total_flujos: producto.flujos.length,
        total_metricas: producto.flujos.reduce((acc, f) => acc + f.metricas.length, 0),
      },
    };
  }

  async exportAllProducts(startDate?: Date, endDate?: Date) {
    const productos = await prisma.producto.findMany({
      where: { activo: true },
    });

    const exports = await Promise.all(
      productos.map(p => this.exportProductMetrics(p.codigo, startDate, endDate))
    );

    return exports;
  }
}
```

### 6.2 Servicio de Exportación CSV

```typescript
// src/modules/export/services/csv-export.service.ts
import { Parser } from 'json2csv';
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export class CsvExportService {
  async exportProductMetrics(productCode: string) {
    // Consulta desnormalizada para CSV
    const data = await prisma.$queryRaw`
      SELECT
        p.codigo AS producto_codigo,
        p.nombre AS producto_nombre,
        f.nombre AS flujo_nombre,
        m.codigo AS metrica_codigo,
        m.nombre AS metrica_nombre,
        tm.nombre AS tipo_metrica,
        m.unidad_medida,
        vm.periodo,
        vm.valor,
        vm.observaciones
      FROM productos p
      INNER JOIN flujos f ON f.producto_id = p.id
      INNER JOIN metricas m ON m.flujo_id = f.id
      INNER JOIN tipos_metrica tm ON tm.id = m.tipo_metrica_id
      LEFT JOIN valores_mensuales vm ON vm.metrica_id = m.id
      WHERE p.codigo = ${productCode}
        AND p.activo = true
        AND f.activo = true
        AND m.activo = true
      ORDER BY f.orden, m.orden, vm.periodo DESC
    `;

    const fields = [
      'producto_codigo',
      'producto_nombre',
      'flujo_nombre',
      'metrica_codigo',
      'metrica_nombre',
      'tipo_metrica',
      'unidad_medida',
      'periodo',
      'valor',
      'observaciones',
    ];

    const parser = new Parser({ fields });
    return parser.parse(data);
  }
}
```

### 6.3 Servicio de Exportación a Excel

```typescript
// src/modules/export/services/excel-export.service.ts
import ExcelJS from 'exceljs';
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export class ExcelExportService {
  /**
   * Exporta producto completo con todos sus flujos a Excel
   */
  async exportProductToExcel(
    productCode: string,
    startDate?: Date,
    endDate?: Date
  ): Promise<ExcelJS.Buffer> {
    const producto = await prisma.producto.findUnique({
      where: { codigo: productCode },
      include: {
        flujos: {
          where: { activo: true },
          include: {
            metricas: {
              where: { activo: true },
              include: {
                valores_mensuales: {
                  where: {
                    ...(startDate && { periodo: { gte: startDate } }),
                    ...(endDate && { periodo: { lte: endDate } }),
                  },
                  orderBy: { periodo: 'asc' },
                },
                tipo_metrica: true,
              },
            },
          },
          orderBy: { orden: 'asc' },
        },
      },
    });

    if (!producto) {
      throw new Error('Producto no encontrado');
    }

    const workbook = new ExcelJS.Workbook();
    workbook.creator = 'Dashboard Métricas';
    workbook.created = new Date();

    // Hoja 1: Resumen
    const resumenSheet = workbook.addWorksheet('Resumen');
    resumenSheet.columns = [
      { header: 'Campo', key: 'campo', width: 25 },
      { header: 'Valor', key: 'valor', width: 50 },
    ];

    resumenSheet.addRows([
      { campo: 'Producto', valor: producto.nombre },
      { campo: 'Código', valor: producto.codigo },
      { campo: 'Total de Flujos', valor: producto.flujos.length },
      {
        campo: 'Total de Métricas',
        valor: producto.flujos.reduce((sum, f) => sum + f.metricas.length, 0)
      },
      { campo: 'Exportado en', valor: new Date().toLocaleString('es-ES') },
    ]);

    // Estilo header
    resumenSheet.getRow(1).font = { bold: true, color: { argb: 'FFFFFFFF' } };
    resumenSheet.getRow(1).fill = {
      type: 'pattern',
      pattern: 'solid',
      fgColor: { argb: 'FF4472C4' },
    };

    // Hoja por cada flujo
    producto.flujos.forEach((flujo) => {
      const sheet = workbook.addWorksheet(flujo.nombre);

      // Obtener todos los períodos únicos
      const periodos = new Set<string>();
      flujo.metricas.forEach((metrica) => {
        metrica.valores_mensuales.forEach((valor) => {
          periodos.add(valor.periodo.toISOString().substring(0, 7)); // YYYY-MM
        });
      });

      const periodosOrdenados = Array.from(periodos).sort();

      // Crear columnas dinámicamente
      const columns = [
        { header: 'Métrica', key: 'metrica', width: 30 },
        { header: 'Tipo', key: 'tipo', width: 15 },
        { header: 'Unidad', key: 'unidad', width: 15 },
        ...periodosOrdenados.map((periodo) => ({
          header: periodo,
          key: periodo,
          width: 12,
        })),
      ];

      sheet.columns = columns;

      // Agregar datos de métricas
      flujo.metricas.forEach((metrica) => {
        const row: any = {
          metrica: metrica.nombre,
          tipo: metrica.tipo_metrica.nombre,
          unidad: metrica.unidad_medida,
        };

        // Agregar valores por período
        metrica.valores_mensuales.forEach((valor) => {
          const periodoKey = valor.periodo.toISOString().substring(0, 7);
          row[periodoKey] = valor.valor;
        });

        sheet.addRow(row);
      });

      // Estilo del header
      sheet.getRow(1).font = { bold: true, color: { argb: 'FFFFFFFF' } };
      sheet.getRow(1).fill = {
        type: 'pattern',
        pattern: 'solid',
        fgColor: { argb: 'FF4472C4' },
      };

      // Formato de números
      sheet.eachRow((row, rowNumber) => {
        if (rowNumber > 1) {
          row.eachCell((cell, colNumber) => {
            if (colNumber > 3 && typeof cell.value === 'number') {
              cell.numFmt = '#,##0.00';
            }
          });
        }
      });
    });

    return await workbook.xlsx.writeBuffer();
  }

  /**
   * Exporta un flujo específico a Excel
   */
  async exportFlujoToExcel(
    productCode: string,
    flujoId: number,
    startDate?: Date,
    endDate?: Date
  ): Promise<ExcelJS.Buffer> {
    const flujo = await prisma.flujo.findFirst({
      where: {
        id: flujoId,
        producto: { codigo: productCode },
        activo: true,
      },
      include: {
        producto: true,
        metricas: {
          where: { activo: true },
          include: {
            valores_mensuales: {
              where: {
                ...(startDate && { periodo: { gte: startDate } }),
                ...(endDate && { periodo: { lte: endDate } }),
              },
              orderBy: { periodo: 'asc' },
            },
            tipo_metrica: true,
          },
          orderBy: { orden: 'asc' },
        },
      },
    });

    if (!flujo) {
      throw new Error('Flujo no encontrado');
    }

    const workbook = new ExcelJS.Workbook();
    workbook.creator = 'Dashboard Métricas';
    workbook.created = new Date();

    // Hoja 1: Métricas con valores mensuales
    const metricasSheet = workbook.addWorksheet('Métricas');

    const periodos = new Set<string>();
    flujo.metricas.forEach((metrica) => {
      metrica.valores_mensuales.forEach((valor) => {
        periodos.add(valor.periodo.toISOString().substring(0, 7));
      });
    });

    const periodosOrdenados = Array.from(periodos).sort();

    metricasSheet.columns = [
      { header: 'Métrica', key: 'metrica', width: 30 },
      { header: 'Tipo', key: 'tipo', width: 15 },
      { header: 'Unidad', key: 'unidad', width: 15 },
      ...periodosOrdenados.map((periodo) => ({
        header: periodo,
        key: periodo,
        width: 12,
      })),
    ];

    flujo.metricas.forEach((metrica) => {
      const row: any = {
        metrica: metrica.nombre,
        tipo: metrica.tipo_metrica.nombre,
        unidad: metrica.unidad_medida,
      };

      metrica.valores_mensuales.forEach((valor) => {
        const periodoKey = valor.periodo.toISOString().substring(0, 7);
        row[periodoKey] = valor.valor;
      });

      metricasSheet.addRow(row);
    });

    // Estilo del header
    metricasSheet.getRow(1).font = { bold: true, color: { argb: 'FFFFFFFF' } };
    metricasSheet.getRow(1).fill = {
      type: 'pattern',
      pattern: 'solid',
      fgColor: { argb: 'FF4472C4' },
    };

    return await workbook.xlsx.writeBuffer();
  }
}
```

**Dependencias necesarias**:
```bash
npm install exceljs
npm install --save-dev @types/exceljs
```

**Ejemplo de controlador**:
```typescript
// src/modules/export/controllers/export.controller.ts
export class ExportController {
  private excelService = new ExcelExportService();

  exportProductToExcel = async (req: Request, res: Response) => {
    const { product_code } = req.params;
    const { start_date, end_date } = req.query;

    const buffer = await this.excelService.exportProductToExcel(
      product_code,
      start_date ? new Date(start_date as string) : undefined,
      end_date ? new Date(end_date as string) : undefined
    );

    res.setHeader(
      'Content-Type',
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    );
    res.setHeader(
      'Content-Disposition',
      `attachment; filename="${product_code}-metrics.xlsx"`
    );

    res.send(buffer);
  };

  exportFlujoToExcel = async (req: Request, res: Response) => {
    const { product_code, flujo_id } = req.params;
    const { start_date, end_date } = req.query;

    const buffer = await this.excelService.exportFlujoToExcel(
      product_code,
      Number(flujo_id),
      start_date ? new Date(start_date as string) : undefined,
      end_date ? new Date(end_date as string) : undefined
    );

    res.setHeader(
      'Content-Type',
      'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'
    );
    res.setHeader(
      'Content-Disposition',
      `attachment; filename="${product_code}-flujo-${flujo_id}.xlsx"`
    );

    res.send(buffer);
  };
}
```

## 7. Configuración de la Aplicación

### 7.1 App.ts

```typescript
// src/app.ts
import express, { Application } from 'express';
import cors from 'cors';
import helmet from 'helmet';
import compression from 'compression';
import { errorHandler } from '@/shared/middleware/error-handler.middleware';
import { loggerMiddleware } from '@/shared/middleware/logger.middleware';
import { apiLimiter } from '@/shared/middleware/rate-limit.middleware';

// Routes
import productosRoutes from '@/modules/productos/routes/productos.routes';
import flujosRoutes from '@/modules/flujos/routes/flujos.routes';
import metricasRoutes from '@/modules/metricas/routes/metricas.routes';
import valoresRoutes from '@/modules/valores/routes/valores.routes';
import exportRoutes from '@/modules/export/routes/export.routes';
import authRoutes from '@/modules/auth/routes/auth.routes';

const app: Application = express();

// Security middleware
app.use(helmet());
app.use(cors());
app.use(compression());

// Body parsing
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Logging
app.use(loggerMiddleware);

// Rate limiting
app.use('/api/', apiLimiter);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/productos', productosRoutes);
app.use('/api/flujos', flujosRoutes);
app.use('/api/metricas', metricasRoutes);
app.use('/api/valores', valoresRoutes);
app.use('/api/export', exportRoutes);

// Error handling
app.use(errorHandler);

export default app;
```

### 7.2 Server.ts

```typescript
// src/server.ts
import app from './app';
import { logger } from '@/shared/utils/logger';

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  logger.info(`🚀 Server running on port ${PORT}`);
  logger.info(`📝 Environment: ${process.env.NODE_ENV}`);
  logger.info(`🔗 API: http://localhost:${PORT}/api`);
});
```

## 8. Variables de Entorno

```bash
# .env.example
NODE_ENV=development
PORT=3000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/metrics_db

# JWT
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRES_IN=7d

# CORS
CORS_ORIGIN=http://localhost:5173

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100

# Logging
LOG_LEVEL=info
```

## 9. Testing

### 9.1 Test Unitario

```typescript
// tests/unit/productos.service.test.ts
import { ProductosService } from '@/modules/productos/services/productos.service';
import { ProductosRepository } from '@/modules/productos/repositories/productos.repository';

jest.mock('@/modules/productos/repositories/productos.repository');

describe('ProductosService', () => {
  let service: ProductosService;
  let repository: jest.Mocked<ProductosRepository>;

  beforeEach(() => {
    service = new ProductosService();
    repository = new ProductosRepository() as jest.Mocked<ProductosRepository>;
  });

  describe('create', () => {
    it('debe crear un producto correctamente', async () => {
      const mockProducto = {
        codigo: 'test-product',
        nombre: 'Test Product',
      };

      repository.findByCode.mockResolvedValue(null);
      repository.create.mockResolvedValue({ id: 1, ...mockProducto } as any);

      const result = await service.create(mockProducto);

      expect(result).toHaveProperty('id');
      expect(result.codigo).toBe(mockProducto.codigo);
    });

    it('debe lanzar error si el código ya existe', async () => {
      const mockProducto = {
        codigo: 'existing-product',
        nombre: 'Existing Product',
      };

      repository.findByCode.mockResolvedValue({ id: 1 } as any);

      await expect(service.create(mockProducto)).rejects.toThrow('Ya existe un producto con ese código');
    });
  });
});
```

## 10. Documentación con Swagger

```typescript
// src/config/swagger.ts
import swaggerJsdoc from 'swagger-jsdoc';

const options: swaggerJsdoc.Options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'Metrics Dashboard API',
      version: '1.0.0',
      description: 'API para gestión de métricas de productos digitales',
    },
    servers: [
      {
        url: 'http://localhost:3000/api',
        description: 'Development server',
      },
    ],
    components: {
      securitySchemes: {
        bearerAuth: {
          type: 'http',
          scheme: 'bearer',
          bearerFormat: 'JWT',
        },
      },
    },
  },
  apis: ['./src/modules/**/routes/*.ts'],
};

export const swaggerSpec = swaggerJsdoc(options);
```

## 11. Deployment

### 11.1 Dockerfile

```dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/

RUN npm ci

COPY . .

RUN npm run build
RUN npx prisma generate

FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/package*.json ./

EXPOSE 3000

CMD ["npm", "run", "start:prod"]
```

### 11.2 Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: metrics_user
      POSTGRES_PASSWORD: metrics_password
      POSTGRES_DB: metrics_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://metrics_user:metrics_password@postgres:5432/metrics_db
      JWT_SECRET: your-secret-key
      NODE_ENV: production
    depends_on:
      - postgres

volumes:
  postgres_data:
```

## 12. Scripts de Package.json

```json
{
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "start:prod": "NODE_ENV=production node dist/server.js",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:seed": "tsx prisma/seed.ts",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "lint": "eslint src --ext .ts",
    "format": "prettier --write \"src/**/*.ts\""
  }
}
```

## 13. Mejores Prácticas

1. **Separation of Concerns**: Cada capa tiene una responsabilidad específica
2. **Dependency Injection**: Facilita testing y mantenimiento
3. **Error Handling**: Manejo centralizado de errores
4. **Validation**: Validación robusta con Zod
5. **Type Safety**: TypeScript en todo el código
6. **Logging**: Logs estructurados para debugging
7. **Security**: JWT, rate limiting, helmet
8. **Testing**: Cobertura de tests unitarios e integración
9. **Documentation**: Swagger para documentación de API
10. **Performance**: Queries optimizadas, caching cuando sea necesario
