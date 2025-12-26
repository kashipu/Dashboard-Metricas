# Configuración del Entorno de Desarrollo

Guía paso a paso para configurar tu entorno de desarrollo local para el Dashboard de Métricas.

## 📋 Tabla de Contenidos

1. [Requisitos Previos](#1-requisitos-previos)
2. [Instalación de Herramientas](#2-instalación-de-herramientas)
3. [Clonar el Repositorio](#3-clonar-el-repositorio)
4. [Configurar la Base de Datos](#4-configurar-la-base-de-datos)
5. [Configurar el Backend](#5-configurar-el-backend)
6. [Configurar el Frontend](#6-configurar-el-frontend)
7. [Variables de Entorno](#7-variables-de-entorno)
8. [Ejecutar el Proyecto](#8-ejecutar-el-proyecto)
9. [Verificar la Instalación](#9-verificar-la-instalación)
10. [Herramientas de Desarrollo](#10-herramientas-de-desarrollo)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. Requisitos Previos

Antes de comenzar, asegúrate de tener instalado lo siguiente en tu sistema:

### Sistema Operativo
- ✅ **Linux** (Ubuntu 22.04+, Debian, Arch)
- ✅ **macOS** (11.0+)
- ✅ **Windows** (10/11 con WSL2 recomendado)

### Software Requerido

| Software | Versión Mínima | Versión Recomendada | Verificar Instalación |
|----------|----------------|---------------------|----------------------|
| **Node.js** | 18.x | 20.x LTS | `node --version` |
| **npm** | 9.x | 10.x | `npm --version` |
| **PostgreSQL** | 14.x | 15.x | `psql --version` |
| **Git** | 2.30+ | Última | `git --version` |

### Opcional pero Recomendado

| Software | Propósito |
|----------|-----------|
| **Docker** + **Docker Compose** | Para ejecutar PostgreSQL en contenedor |
| **pnpm** o **yarn** | Alternativas más rápidas a npm |
| **VSCode** | Editor de código recomendado |

---

## 2. Instalación de Herramientas

### 2.1 Instalar Node.js (usando nvm)

**Linux / macOS / WSL2**:
```bash
# Instalar nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash

# Reiniciar terminal o ejecutar
source ~/.bashrc  # o ~/.zshrc en macOS

# Instalar Node.js 20 LTS
nvm install 20
nvm use 20
nvm alias default 20

# Verificar instalación
node --version  # Debe mostrar v20.x.x
npm --version   # Debe mostrar 10.x.x
```

**Windows**:
- Descargar instalador desde [nodejs.org](https://nodejs.org)
- Ejecutar instalador y seguir wizard
- O usar **nvm-windows**: [github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows)

### 2.2 Instalar PostgreSQL

#### Opción 1: Instalación Nativa

**Ubuntu / Debian**:
```bash
# Actualizar repositorios
sudo apt update

# Instalar PostgreSQL 15
sudo apt install postgresql-15 postgresql-contrib-15

# Iniciar servicio
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Verificar instalación
psql --version
```

**macOS** (con Homebrew):
```bash
# Instalar PostgreSQL
brew install postgresql@15

# Iniciar servicio
brew services start postgresql@15

# Verificar instalación
psql --version
```

**Windows**:
- Descargar instalador desde [postgresql.org/download/windows](https://www.postgresql.org/download/windows/)
- Ejecutar instalador
- Anotar la contraseña del usuario `postgres`

#### Opción 2: PostgreSQL con Docker (Recomendado)

```bash
# Crear archivo docker-compose.yml
cat > docker-compose.dev.yml <<EOF
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: metrics-db-dev
    restart: unless-stopped
    environment:
      POSTGRES_USER: metrics_dev
      POSTGRES_PASSWORD: dev_password_2024
      POSTGRES_DB: metrics_development
    ports:
      - "5432:5432"
    volumes:
      - postgres_dev_data:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: pgadmin-dev
    restart: unless-stopped
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@metrics.local
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"
    depends_on:
      - postgres

volumes:
  postgres_dev_data:
EOF

# Levantar PostgreSQL
docker compose -f docker-compose.dev.yml up -d

# Verificar que esté corriendo
docker ps
```

### 2.3 Instalar Git

**Linux**:
```bash
sudo apt install git  # Ubuntu/Debian
sudo yum install git  # CentOS/RHEL
```

**macOS**:
```bash
brew install git
```

**Windows**:
- Descargar desde [git-scm.com](https://git-scm.com/)

### 2.4 Instalar VSCode (Opcional)

```bash
# Ubuntu/Debian
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code

# macOS
brew install --cask visual-studio-code

# Windows: Descargar desde code.visualstudio.com
```

---

## 3. Clonar el Repositorio

```bash
# Navegar a tu directorio de proyectos
cd ~/proyectos  # o donde prefieras

# Clonar el repositorio
git clone https://github.com/tu-organizacion/dashboard-metricas.git

# Entrar al directorio
cd dashboard-metricas

# Verificar estructura
ls -la
```

**Estructura esperada**:
```
dashboard-metricas/
├── backend/           # API Node.js + Express
├── frontend/          # Aplicación Next.js
├── docs/              # Documentación (archivos .md)
├── docker-compose.yml
├── README.md
└── .gitignore
```

---

## 4. Configurar la Base de Datos

### 4.1 Acceder a PostgreSQL

**Si usas instalación nativa**:
```bash
# Cambiar a usuario postgres
sudo -u postgres psql

# O si configuraste usuario sin sudo
psql -U postgres
```

**Si usas Docker**:
```bash
# Conectarse al contenedor
docker exec -it metrics-db-dev psql -U metrics_dev -d metrics_development
```

### 4.2 Crear Base de Datos y Usuario

```sql
-- Crear usuario (solo si usas instalación nativa)
CREATE USER metrics_dev WITH PASSWORD 'dev_password_2024';

-- Crear base de datos
CREATE DATABASE metrics_development;

-- Otorgar permisos
GRANT ALL PRIVILEGES ON DATABASE metrics_development TO metrics_dev;

-- Salir
\q
```

### 4.3 Ejecutar Migraciones del Schema

**Opción 1: Con archivo SQL directo**:
```bash
# Navegar al directorio backend
cd backend

# Ejecutar schema.sql
psql -h localhost -U metrics_dev -d metrics_development -f prisma/schema.sql

# Ingresar password cuando se solicite: dev_password_2024
```

**Opción 2: Con Prisma Migrate**:
```bash
# Dentro del directorio backend
cd backend

# Ejecutar migraciones
npx prisma migrate dev --name init

# Generar cliente Prisma
npx prisma generate
```

### 4.4 Insertar Datos de Ejemplo (Opcional)

```bash
# Ejecutar seed script
cd backend
npm run prisma:seed

# O manualmente
psql -h localhost -U metrics_dev -d metrics_development -f prisma/seed.sql
```

---

## 5. Configurar el Backend

### 5.1 Instalar Dependencias

```bash
# Navegar al directorio backend
cd backend

# Instalar dependencias con npm
npm install

# O con pnpm (más rápido)
pnpm install

# O con yarn
yarn install
```

**Dependencias principales que se instalarán**:
- `express` - Framework web
- `@prisma/client` - ORM
- `typescript` - TypeScript
- `zod` - Validación
- `jsonwebtoken` - JWT para autenticación
- `bcrypt` - Hash de contraseñas
- `cors` - CORS middleware
- `dotenv` - Variables de entorno
- `exceljs` - Exportación a Excel
- `openai` / `@anthropic-ai/sdk` - Chat con IA

### 5.2 Configurar Variables de Entorno

```bash
# Crear archivo .env desde ejemplo
cp .env.example .env

# Editar archivo .env
nano .env  # o code .env si usas VSCode
```

**Contenido de `backend/.env`**:
```bash
# Base de datos
DATABASE_URL="postgresql://metrics_dev:dev_password_2024@localhost:5432/metrics_development"

# Puerto del servidor
PORT=3000

# JWT
JWT_SECRET="tu-secreto-jwt-super-seguro-cambiar-en-produccion"
JWT_EXPIRES_IN="24h"
JWT_REFRESH_SECRET="tu-secreto-refresh-super-seguro"
JWT_REFRESH_EXPIRES_IN="7d"

# Entorno
NODE_ENV="development"

# CORS (permitir frontend local)
CORS_ORIGIN="http://localhost:3001"

# Chat con IA (opcional)
AI_PROVIDER="openai"  # o "anthropic"
OPENAI_API_KEY="sk-..."  # Tu API key de OpenAI
ANTHROPIC_API_KEY="sk-ant-..."  # Tu API key de Claude
AI_MODEL="gpt-4-turbo"  # o "claude-3-5-sonnet-20241022"
AI_MAX_TOKENS=1000
AI_TEMPERATURE=0.7

# Email (opcional, para notificaciones)
SMTP_HOST="smtp.gmail.com"
SMTP_PORT=587
SMTP_USER="tu-email@gmail.com"
SMTP_PASS="tu-password-smtp"
SMTP_FROM="Dashboard Métricas <noreply@tudominio.com>"

# Rate Limiting (opcional)
REDIS_URL="redis://localhost:6379"
```

### 5.3 Generar Cliente Prisma

```bash
# Generar cliente de base de datos
npx prisma generate

# Ver schema en navegador (opcional)
npx prisma studio
# Abre en http://localhost:5555
```

### 5.4 Compilar TypeScript

```bash
# Compilar una vez
npm run build

# O en modo watch (recompila automáticamente)
npm run build -- --watch
```

---

## 6. Configurar el Frontend

### 6.1 Instalar Dependencias

```bash
# Navegar al directorio frontend
cd ../frontend  # o cd frontend desde la raíz

# Instalar dependencias
npm install
```

**Dependencias principales que se instalarán**:
- `next` - Framework React
- `react` - Librería UI
- `typescript` - TypeScript
- `tailwindcss` - Estilos
- `@tanstack/react-query` - Data fetching
- `zustand` - Estado global
- `axios` - HTTP client
- `recharts` - Gráficos
- `shadcn/ui` - Componentes UI
- `lucide-react` - Iconos
- `date-fns` - Manejo de fechas
- `react-hook-form` - Formularios
- `zod` - Validación

### 6.2 Configurar Variables de Entorno

```bash
# Crear archivo .env.local
cp .env.example .env.local

# Editar archivo
nano .env.local
```

**Contenido de `frontend/.env.local`**:
```bash
# URL del backend
NEXT_PUBLIC_API_URL="http://localhost:3000/api"

# Configuración de la app
NEXT_PUBLIC_APP_NAME="Dashboard de Métricas"
NEXT_PUBLIC_APP_URL="http://localhost:3001"

# Supabase (si usas Supabase, sino comentar)
# NEXT_PUBLIC_SUPABASE_URL="https://api.tudominio.com"
# NEXT_PUBLIC_SUPABASE_ANON_KEY="tu-anon-key"

# Analytics (opcional)
# NEXT_PUBLIC_GA_ID="G-XXXXXXXXXX"
```

### 6.3 Configurar TailwindCSS 4

Si usas TailwindCSS 4, ya debe estar configurado en el proyecto. Verifica:

```bash
# Verificar instalación
cat package.json | grep tailwindcss

# Debe mostrar: "tailwindcss": "^4.0.0-alpha.X"
```

---

## 7. Variables de Entorno

### 7.1 Resumen de Variables Críticas

**Backend** (`backend/.env`):
```bash
DATABASE_URL="postgresql://user:pass@localhost:5432/db"  # ⚠️ CRÍTICO
JWT_SECRET="cambiar-en-produccion"                        # ⚠️ CRÍTICO
PORT=3000
NODE_ENV="development"
```

**Frontend** (`frontend/.env.local`):
```bash
NEXT_PUBLIC_API_URL="http://localhost:3000/api"  # ⚠️ CRÍTICO
```

### 7.2 Generar Secretos Seguros

```bash
# Generar JWT_SECRET aleatorio
openssl rand -base64 32

# Generar JWT_REFRESH_SECRET aleatorio
openssl rand -base64 32

# Usar estos valores en tu .env
```

---

## 8. Ejecutar el Proyecto

### 8.1 Opción 1: Ejecutar Backend y Frontend por Separado

**Terminal 1 - Backend**:
```bash
# Navegar al backend
cd backend

# Modo desarrollo (con hot-reload)
npm run dev

# Deberías ver:
# [INFO] Server running on http://localhost:3000
# [INFO] Database connected successfully
```

**Terminal 2 - Frontend**:
```bash
# Navegar al frontend
cd frontend

# Modo desarrollo
npm run dev

# Deberías ver:
# ▲ Next.js 14.x.x
# - Local:   http://localhost:3001
# - Ready in 2.5s
```

### 8.2 Opción 2: Script Combinado (Recomendado)

Crear script en la raíz del proyecto:

```bash
# Crear script dev.sh en la raíz
cat > dev.sh <<'EOF'
#!/bin/bash

echo "🚀 Iniciando Dashboard de Métricas en modo desarrollo..."

# Verificar si postgres está corriendo
if ! pg_isready -h localhost -p 5432; then
  echo "⚠️  PostgreSQL no está corriendo. Iniciando con Docker..."
  docker compose -f docker-compose.dev.yml up -d postgres
  sleep 3
fi

# Función para manejar Ctrl+C
cleanup() {
  echo ""
  echo "🛑 Deteniendo servidores..."
  kill $BACKEND_PID $FRONTEND_PID 2>/dev/null
  exit 0
}

trap cleanup SIGINT SIGTERM

# Iniciar backend
echo "📦 Iniciando backend..."
cd backend
npm run dev &
BACKEND_PID=$!
cd ..

# Esperar 3 segundos para que el backend inicie
sleep 3

# Iniciar frontend
echo "🎨 Iniciando frontend..."
cd frontend
npm run dev &
FRONTEND_PID=$!
cd ..

echo ""
echo "✅ Servidores iniciados:"
echo "   Backend:  http://localhost:3000"
echo "   Frontend: http://localhost:3001"
echo ""
echo "Presiona Ctrl+C para detener"

# Esperar a que los procesos terminen
wait
EOF

# Hacer ejecutable
chmod +x dev.sh

# Ejecutar
./dev.sh
```

### 8.3 Opción 3: Con Docker Compose (Proyecto Completo)

```bash
# Crear docker-compose completo
cat > docker-compose.full.yml <<'EOF'
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: metrics-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: metrics_dev
      POSTGRES_PASSWORD: dev_password_2024
      POSTGRES_DB: metrics_development
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    container_name: metrics-backend
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://metrics_dev:dev_password_2024@postgres:5432/metrics_development
      NODE_ENV: development
    volumes:
      - ./backend:/app
      - /app/node_modules
    depends_on:
      - postgres

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    container_name: metrics-frontend
    restart: unless-stopped
    ports:
      - "3001:3001"
    environment:
      NEXT_PUBLIC_API_URL: http://localhost:3000/api
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next
    depends_on:
      - backend

volumes:
  postgres_data:
EOF

# Levantar todo
docker compose -f docker-compose.full.yml up -d

# Ver logs
docker compose -f docker-compose.full.yml logs -f
```

---

## 9. Verificar la Instalación

### 9.1 Verificar Backend

```bash
# Test de health check
curl http://localhost:3000/api/health

# Respuesta esperada:
# {"status":"ok","timestamp":"2025-12-26T...","database":"connected"}

# Test de autenticación (login)
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@metrics.com","password":"admin123"}'

# Respuesta esperada:
# {"success":true,"token":"eyJhbG...","user":{...}}
```

### 9.2 Verificar Frontend

Abrir navegador en `http://localhost:3001`

**Deberías ver**:
- ✅ Página de login
- ✅ Sin errores en la consola del navegador (F12)
- ✅ Estilos cargados correctamente (TailwindCSS)

**Login de prueba** (si ejecutaste seed):
- Email: `admin@metrics.com`
- Password: `admin123`

### 9.3 Verificar Base de Datos

```bash
# Conectarse a PostgreSQL
psql -h localhost -U metrics_dev -d metrics_development

# Verificar tablas
\dt

# Deberías ver:
# usuarios, productos, flujos, metricas, valores_mensuales, catalogo_metricas

# Verificar datos de ejemplo
SELECT * FROM usuarios;

# Salir
\q
```

### 9.4 Checklist de Verificación

- [ ] PostgreSQL corriendo en puerto 5432
- [ ] Backend corriendo en puerto 3000
- [ ] Frontend corriendo en puerto 3001
- [ ] `http://localhost:3000/api/health` responde OK
- [ ] `http://localhost:3001` carga página de login
- [ ] Sin errores en consola del navegador
- [ ] Login funciona con usuario de prueba
- [ ] Base de datos tiene tablas creadas

---

## 10. Herramientas de Desarrollo

### 10.1 VSCode - Extensiones Recomendadas

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "prisma.prisma",
    "bradlc.vscode-tailwindcss",
    "streetsidesoftware.code-spell-checker",
    "christian-kohler.path-intellisense",
    "ms-vscode.vscode-typescript-next",
    "firsttris.vscode-jest-runner",
    "rangav.vscode-thunder-client",
    "cweijan.vscode-postgresql-client2"
  ]
}
```

**Instalar todas a la vez**:
```bash
# Crear archivo .vscode/extensions.json en la raíz
mkdir -p .vscode
cat > .vscode/extensions.json <<'EOF'
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "prisma.prisma",
    "bradlc.vscode-tailwindcss",
    "streetsidesoftware.code-spell-checker"
  ]
}
EOF
```

### 10.2 VSCode - Configuración Recomendada

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"]
  ],
  "[prisma]": {
    "editor.defaultFormatter": "Prisma.prisma"
  }
}
```

### 10.3 Herramientas CLI Útiles

```bash
# Prisma Studio - UI para ver base de datos
npx prisma studio

# Thunder Client - Cliente REST en VSCode
# (alternativa a Postman, como extensión)

# pgAdmin - UI para PostgreSQL
# Si usas Docker, ya está en http://localhost:5050
# Email: admin@metrics.local
# Password: admin
```

### 10.4 Scripts NPM Útiles

**Backend** (`backend/package.json`):
```json
{
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:seed": "tsx prisma/seed.ts",
    "prisma:studio": "prisma studio",
    "test": "jest",
    "test:watch": "jest --watch",
    "lint": "eslint src --ext .ts",
    "format": "prettier --write \"src/**/*.ts\""
  }
}
```

**Frontend** (`frontend/package.json`):
```json
{
  "scripts": {
    "dev": "next dev -p 3001",
    "build": "next build",
    "start": "next start -p 3001",
    "lint": "next lint",
    "format": "prettier --write \"src/**/*.{ts,tsx}\""
  }
}
```

---

## 11. Troubleshooting

### 11.1 Error: "EADDRINUSE: address already in use :::3000"

**Problema**: El puerto 3000 ya está en uso.

**Solución**:
```bash
# Ver qué proceso está usando el puerto
lsof -i :3000  # Linux/macOS
netstat -ano | findstr :3000  # Windows

# Matar el proceso
kill -9 <PID>  # Linux/macOS
taskkill /PID <PID> /F  # Windows

# O cambiar el puerto en backend/.env
PORT=3001
```

### 11.2 Error: "relation 'usuarios' does not exist"

**Problema**: Las tablas de la base de datos no existen.

**Solución**:
```bash
cd backend

# Ejecutar migraciones
npx prisma migrate dev --name init

# O ejecutar schema SQL manualmente
psql -h localhost -U metrics_dev -d metrics_development -f prisma/schema.sql
```

### 11.3 Error: "password authentication failed for user"

**Problema**: Credenciales incorrectas en DATABASE_URL.

**Solución**:
```bash
# Verificar credenciales en backend/.env
DATABASE_URL="postgresql://USER:PASSWORD@localhost:5432/DB"

# Resetear password de PostgreSQL
sudo -u postgres psql
ALTER USER metrics_dev WITH PASSWORD 'nueva_password';
\q

# Actualizar .env con nueva password
```

### 11.4 Error: "Module not found" o dependencias faltantes

**Problema**: Dependencias no instaladas.

**Solución**:
```bash
# Backend
cd backend
rm -rf node_modules package-lock.json
npm install

# Frontend
cd frontend
rm -rf node_modules package-lock.json
npm install
```

### 11.5 Error: "Cannot find module '@prisma/client'"

**Problema**: Cliente Prisma no generado.

**Solución**:
```bash
cd backend
npx prisma generate
```

### 11.6 Frontend no carga estilos (TailwindCSS)

**Problema**: TailwindCSS no compilado.

**Solución**:
```bash
cd frontend

# Verificar que tailwind.config.ts existe
ls tailwind.config.ts

# Verificar que globals.css tiene @import 'tailwindcss'
cat src/app/globals.css

# Limpiar caché de Next.js
rm -rf .next
npm run dev
```

### 11.7 Error: "connect ECONNREFUSED 127.0.0.1:5432"

**Problema**: PostgreSQL no está corriendo.

**Solución**:
```bash
# Si usas instalación nativa
sudo systemctl start postgresql  # Linux
brew services start postgresql@15  # macOS

# Si usas Docker
docker compose -f docker-compose.dev.yml up -d postgres

# Verificar que esté corriendo
pg_isready -h localhost -p 5432
```

### 11.8 CORS Error en el navegador

**Problema**: Backend no permite requests desde frontend.

**Solución**:
```bash
# Verificar CORS_ORIGIN en backend/.env
CORS_ORIGIN="http://localhost:3001"

# Reiniciar backend
```

### 11.9 Chat con IA no funciona

**Problema**: API keys no configuradas.

**Solución**:
```bash
# Verificar backend/.env
AI_PROVIDER="openai"
OPENAI_API_KEY="sk-..."  # Debe empezar con sk-

# Verificar que la key sea válida
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

### 11.10 Hot reload no funciona

**Problema**: Cambios en código no se reflejan.

**Solución**:
```bash
# Backend - Verificar que tsx está en modo watch
npm run dev  # Debe usar "tsx watch src/server.ts"

# Frontend - Limpiar caché
rm -rf .next
npm run dev

# Si usas WSL2 en Windows, puede ser problema de filesystem
# Usar CHOKIDAR_USEPOLLING=true npm run dev
```

---

## 12. Próximos Pasos

Una vez que tengas el entorno funcionando:

1. **Explorar el código**:
   - Lee `backend/src/server.ts` para entender las rutas
   - Lee `frontend/src/app/page.tsx` para ver la UI

2. **Crear tu primer endpoint**:
   - Agrega un endpoint en `backend/src/routes/`
   - Conéctalo desde el frontend

3. **Modificar la UI**:
   - Edita componentes en `frontend/src/components/`
   - Verás cambios en tiempo real

4. **Leer documentación**:
   - `ARCHITECTURE.md` - Arquitectura del sistema
   - `DATABASE.md` - Esquema de base de datos
   - `BACKEND.md` - Detalles del backend
   - `FRONTEND.md` - Detalles del frontend

---

## 13. Recursos Adicionales

### Documentación Oficial
- [Node.js Docs](https://nodejs.org/docs/)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [Prisma Docs](https://www.prisma.io/docs/)
- [Next.js Docs](https://nextjs.org/docs)
- [TailwindCSS Docs](https://tailwindcss.com/docs)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)

### Tutoriales Útiles
- [Prisma Getting Started](https://www.prisma.io/docs/getting-started)
- [Next.js App Router](https://nextjs.org/docs/app)
- [TailwindCSS Setup](https://tailwindcss.com/docs/installation)

### Comunidad
- [Stack Overflow](https://stackoverflow.com/)
- [Discord de Next.js](https://discord.gg/nextjs)
- [Prisma Community](https://www.prisma.io/community)

---

## ✅ Checklist Final

- [ ] Node.js 20+ instalado
- [ ] PostgreSQL 15+ corriendo
- [ ] Repositorio clonado
- [ ] Base de datos creada y migrada
- [ ] Backend: dependencias instaladas
- [ ] Backend: `.env` configurado
- [ ] Backend: `npm run dev` funciona
- [ ] Frontend: dependencias instaladas
- [ ] Frontend: `.env.local` configurado
- [ ] Frontend: `npm run dev` funciona
- [ ] Login funciona con usuario de prueba
- [ ] VSCode configurado con extensiones

**¡Felicidades! Tu entorno de desarrollo está listo.** 🎉

Si encuentras algún problema no listado aquí, consulta la documentación específica del módulo o abre un issue en el repositorio.
