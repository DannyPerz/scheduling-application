# Sprint 1: Setup & Foundation

**Duración:** Semana 1 (Enero 2-8, 2026)
**Objetivo:** Proyecto configurado profesionalmente y listo para desarrollo activo
**Horas disponibles:** 14 horas
**Status:** 🟡 Pending

---

## 🎯 Sprint Goal

Tener un proyecto Next.js 15 completamente configurado con Tailwind CSS v4, Supabase, Drizzle ORM, y despliegue automático en Vercel. Al final del sprint, el proyecto debe estar listo para empezar a construir features.

---

## 📋 Sprint Backlog

### 1. Inicialización del Proyecto (2h)

**Tasks:**
- [ ] Crear proyecto Next.js 15 con TypeScript
  ```bash
  npx create-next-app@latest flime --typescript --tailwind --app --src-dir
  cd flime
  ```
- [ ] Configurar `tsconfig.json` con strict mode
- [ ] Configurar estructura de carpetas:
  ```
  src/
  ├── app/
  ├── components/
  ├── lib/
  ├── db/
  ├── types/
  └── emails/
  ```
- [ ] Actualizar README.md con instrucciones de setup
- [ ] Crear `.env.local.example` con variables necesarias

**Criterio de aceptación:**
- ✅ `npm run dev` corre sin errores
- ✅ TypeScript strict mode habilitado
- ✅ Estructura de carpetas clara

---

### 2. Configuración Tailwind CSS v4 (1h)

**Tasks:**
- [ ] Actualizar a Tailwind CSS v4 (si ya salió estable)
- [ ] Configurar `tailwind.config.ts`:
  - Colores personalizados (primary, success, warning, danger)
  - Espaciado base 8px
  - Border radius personalizado
- [ ] Configurar sistema de design tokens
- [ ] Crear archivo `src/styles/globals.css` con CSS base
- [ ] Test: Crear componente simple con Tailwind

**Criterio de aceptación:**
- ✅ Tailwind v4 funcionando
- ✅ Colores personalizados aplicables
- ✅ HMR funcionando (cambios instantáneos)

---

### 3. Instalación shadcn/ui (1h)

**Tasks:**
- [ ] Instalar shadcn/ui:
  ```bash
  npx shadcn-ui@latest init
  ```
- [ ] Agregar componentes iniciales:
  ```bash
  npx shadcn-ui@latest add button
  npx shadcn-ui@latest add input
  npx shadcn-ui@latest add dialog
  npx shadcn-ui@latest add select
  npx shadcn-ui@latest add toast
  npx shadcn-ui@latest add card
  ```
- [ ] Test: Crear página demo con todos los componentes
- [ ] Configurar Toast provider en layout

**Criterio de aceptación:**
- ✅ Componentes shadcn/ui disponibles
- ✅ Estilo consistente con Tailwind
- ✅ Todos los componentes renderizando correctamente

---

### 4. Configuración Supabase (2h)

**Tasks:**
- [ ] Crear proyecto en Supabase dashboard
- [ ] Copiar credenciales (URL, anon key, service key)
- [ ] Instalar dependencias:
  ```bash
  npm install @supabase/supabase-js @supabase/auth-helpers-nextjs
  ```
- [ ] Crear `src/lib/supabase.ts`:
  - Cliente para browser
  - Cliente para server
- [ ] Configurar variables de entorno:
  ```
  NEXT_PUBLIC_SUPABASE_URL=
  NEXT_PUBLIC_SUPABASE_ANON_KEY=
  SUPABASE_SERVICE_ROLE_KEY=
  ```
- [ ] Test: Query simple a Supabase (verificar conexión)

**Criterio de aceptación:**
- ✅ Conexión a Supabase funcionando
- ✅ Clientes configurados correctamente
- ✅ Variables de entorno seguras

---

### 5. Configuración Drizzle ORM (2h)

**Tasks:**
- [ ] Instalar dependencias:
  ```bash
  npm install drizzle-orm postgres
  npm install -D drizzle-kit
  ```
- [ ] Crear `drizzle.config.ts`
- [ ] Crear `src/db/schema.ts` con schema inicial:
  - users table
  - boards table
  - tasks table
  - subscriptions table
  - notifications table
- [ ] Crear `src/db/index.ts` (Drizzle client)
- [ ] Configurar script en `package.json`:
  ```json
  "db:generate": "drizzle-kit generate:pg",
  "db:push": "drizzle-kit push:pg"
  ```
- [ ] Generar y aplicar primera migration

**Criterio de aceptación:**
- ✅ Schema definido en TypeScript
- ✅ Migrations aplicadas en Supabase
- ✅ Tablas creadas correctamente

---

### 6. Configuración de Database Triggers & RLS (1.5h)

**Tasks:**
- [ ] Crear SQL script con:
  - Trigger `update_updated_at` para todas las tablas
  - Function `check_board_limit` (límite FREE)
  - Function `check_task_limit` (límite FREE)
  - Function `set_completed_at` (auto-set al completar tarea)
- [ ] Aplicar Row Level Security (RLS):
  - Políticas para users
  - Políticas para boards
  - Políticas para tasks
  - Políticas para subscriptions
  - Políticas para notifications
- [ ] Ejecutar script en Supabase SQL Editor
- [ ] Test: Verificar que RLS funciona correctamente

**Criterio de aceptación:**
- ✅ Triggers funcionando
- ✅ RLS aplicado a todas las tablas
- ✅ Seguridad validada

---

### 7. Configuración ESLint & Prettier (0.5h)

**Tasks:**
- [ ] Configurar ESLint:
  ```bash
  npm install -D eslint-config-next
  ```
- [ ] Configurar Prettier:
  ```bash
  npm install -D prettier prettier-plugin-tailwindcss
  ```
- [ ] Crear `.prettierrc`:
  ```json
  {
    "semi": false,
    "singleQuote": true,
    "tabWidth": 2,
    "plugins": ["prettier-plugin-tailwindcss"]
  }
  ```
- [ ] Agregar scripts en `package.json`:
  ```json
  "lint": "next lint",
  "format": "prettier --write ."
  ```

**Criterio de aceptación:**
- ✅ Linter sin errores
- ✅ Auto-format funcionando

---

### 8. Setup Vercel Deploy (1h)

**Tasks:**
- [ ] Crear repositorio en GitHub
- [ ] Push código inicial
- [ ] Conectar repo a Vercel
- [ ] Configurar environment variables en Vercel:
  - NEXT_PUBLIC_SUPABASE_URL
  - NEXT_PUBLIC_SUPABASE_ANON_KEY
  - SUPABASE_SERVICE_ROLE_KEY
  - DATABASE_URL
- [ ] Configurar auto-deploy:
  - main branch → producción
  - PRs → preview deployments
- [ ] Test: Deploy y verificar que funciona

**Criterio de aceptación:**
- ✅ Deploy exitoso en Vercel
- ✅ URL accesible y funcionando
- ✅ Auto-deploy configurado

---

### 9. GitHub CI/CD (1h)

**Tasks:**
- [ ] Crear `.github/workflows/ci.yml`:
  ```yaml
  name: CI
  on: [push, pull_request]
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - uses: actions/setup-node@v3
          with:
            node-version: '20'
        - run: npm ci
        - run: npm run lint
        - run: npm run type-check
        - run: npm run build
  ```
- [ ] Agregar script `type-check` en `package.json`:
  ```json
  "type-check": "tsc --noEmit"
  ```
- [ ] Test: Push y verificar que CI pasa

**Criterio de aceptación:**
- ✅ CI corre en cada push
- ✅ Lint + Type check + Build pasan
- ✅ GitHub Actions badge verde

---

### 10. Configuración de Dependencias Adicionales (1h)

**Tasks:**
- [ ] Instalar TanStack Query:
  ```bash
  npm install @tanstack/react-query
  ```
- [ ] Instalar React Hook Form + Zod:
  ```bash
  npm install react-hook-form @hookform/resolvers zod
  ```
- [ ] Instalar date-fns:
  ```bash
  npm install date-fns
  ```
- [ ] Crear provider para TanStack Query en layout
- [ ] Test: Crear ejemplo simple con cada librería

**Criterio de aceptación:**
- ✅ Todas las dependencias instaladas
- ✅ Providers configurados
- ✅ Type-safety funcionando

---

### 11. Setup Monitoring (1h)

**Tasks:**
- [ ] Crear cuenta en Sentry (free tier)
- [ ] Instalar Sentry:
  ```bash
  npx @sentry/wizard@latest -i nextjs
  ```
- [ ] Configurar environment en Sentry (development/production)
- [ ] Test: Forzar error y verificar que llega a Sentry
- [ ] Crear cuenta en PostHog (analytics)
- [ ] Configurar PostHog en layout
- [ ] Test: Verificar que eventos se capturan

**Criterio de aceptación:**
- ✅ Sentry capturando errores
- ✅ PostHog capturando eventos
- ✅ Dashboards configurados

---

## 🧪 Testing & Validation

### Definition of Done (Sprint 1)
- [ ] `npm run dev` corre sin errores
- [ ] `npm run build` completa exitosamente
- [ ] `npm run lint` pasa sin warnings
- [ ] Deploy en Vercel accesible
- [ ] Database schema aplicado en Supabase
- [ ] RLS verificado (usuarios no pueden ver datos de otros)
- [ ] CI/CD pasando en GitHub Actions
- [ ] Sentry y PostHog capturando data

### Manual Testing Checklist
- [ ] Abrir proyecto en localhost:3000
- [ ] Verificar Tailwind CSS funcionando (crear un botón de prueba)
- [ ] Verificar componentes shadcn/ui renderizan
- [ ] Hacer query a Supabase desde API route
- [ ] Verificar que Drizzle puede hacer CRUD a DB
- [ ] Forzar un error y verificar Sentry
- [ ] Deploy a Vercel y verificar producción

---

## 📊 Sprint Metrics

**Velocity estimada:** 14 horas / 11 tasks = ~1.27h por task

**Burndown:**
| Día | Horas restantes |
|-----|-----------------|
| Lun | 12h |
| Mar | 10h |
| Mié | 8h |
| Jue | 6h |
| Vie | 4h |
| Sáb | 2h |
| Dom | 0h (Done) |

---

## 🚧 Blockers & Dependencies

**Posibles blockers:**
- Supabase downtime (mitigación: usar DB local si es necesario)
- Tailwind v4 no estable aún (mitigación: usar v3 temporalmente)
- Issues con Vercel deploy (mitigación: documentación oficial)

**Dependencias externas:**
- Cuenta Supabase activa
- Cuenta Vercel activa
- Cuenta GitHub activa

---

## 🎓 Learning Outcomes

**Al final de Sprint 1 deberías saber:**
- ✅ Cómo configurar Next.js 15 App Router
- ✅ Cómo integrar Supabase con Next.js
- ✅ Cómo usar Drizzle ORM para schema y migrations
- ✅ Cómo aplicar Row Level Security en Supabase
- ✅ Cómo configurar CI/CD con GitHub Actions
- ✅ Cómo deployar en Vercel

---

## 📝 Notes & Observations

**Tips:**
- Usa `npx` para comandos one-off (shadcn, create-next-app)
- Commitea frecuentemente (al menos 1 commit por task)
- Si te trabas > 30 min, busca ayuda (docs, ChatGPT, comunidad)
- Documenta decisiones importantes en este archivo

**Comandos útiles:**
```bash
# Dev
npm run dev

# Build
npm run build

# Lint
npm run lint

# Type check
npm run type-check

# Format
npm run format

# Database
npm run db:generate
npm run db:push
```

---

## 🏁 Sprint Review

**Preguntas para retrospectiva:**
1. ¿Logramos el sprint goal?
2. ¿Qué salió bien?
3. ¿Qué salió mal?
4. ¿Qué deberíamos hacer diferente en Sprint 2?
5. ¿Hay tech debt que abordar?

**A completar al final del sprint:**
- Horas reales trabajadas: ___
- Tasks completados: ___ / 11
- Blockers encontrados: ___
- Aprendizajes clave: ___

---

## ➡️ Next Sprint

**Sprint 2 (Sem 2) - Authentication:**
- Supabase Auth integration
- Signup/Login/Logout pages
- Google OAuth
- Onboarding wizard

---

**Sprint Owner:** Fundador Flime
**Start Date:** Enero 2, 2026
**End Date:** Enero 8, 2026
**Status:** 🟡 Pending → 🟢 In Progress → 🔵 Done
