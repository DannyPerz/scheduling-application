# Sprint 1: Setup & Foundation

**Duración:** Diciembre 30, 2024 - Diciembre 31, 2024
**Objetivo:** Proyecto configurado profesionalmente y listo para desarrollo activo
**Status:** 🔵 Completado

---

## 🎯 Sprint Goal

Tener un proyecto Next.js 16 completamente configurado con Tailwind CSS v4, Supabase, Drizzle ORM, Sentry, y despliegue automático en Vercel con CI/CD. Al final del sprint, el proyecto debe estar listo para empezar a construir features.

---

## 📋 Sprint Backlog

### 1. Inicialización del Proyecto ✅

**Tasks:**
- [x] Crear proyecto Next.js 16 con TypeScript
- [x] Configurar `tsconfig.json` con strict mode
- [x] Configurar estructura de carpetas:
  ```
  src/
  ├── app/
  ├── components/
  │   └── ui/
  ├── lib/
  │   ├── providers/
  │   ├── supabase/
  │   ├── utils/
  │   └── validations/
  ├── db/
  │   └── schema/
  ├── types/
  └── emails/
  ```
- [x] Crear `.env.example` con variables necesarias

**Implementación Real:**
- Next.js 16.1.1 (no 15 como planeado)
- TypeScript 5.9.3
- pnpm 10.26.2 como package manager
- Proxy pattern (`proxy.ts`) en lugar de middleware (Next.js 16)

**Criterio de aceptación:**
- ✅ `pnpm dev` corre sin errores
- ✅ TypeScript strict mode habilitado
- ✅ Estructura de carpetas clara

---

### 2. Configuración Tailwind CSS v4 ✅

**Tasks:**
- [x] Instalar Tailwind CSS v4.1.18
- [x] Configurar `@tailwindcss/postcss`
- [x] Configurar sistema de diseño con CSS variables
- [x] Test: Componentes renderizando con Tailwind

**Implementación Real:**
- Tailwind CSS v4.1.18 (stable)
- @tailwindcss/postcss@4.1.18
- Configuración en `postcss.config.mjs`

**Criterio de aceptación:**
- ✅ Tailwind v4 funcionando
- ✅ HMR funcionando (cambios instantáneos)

---

### 3. Instalación shadcn/ui ✅

**Tasks:**
- [x] Instalar shadcn/ui con CLI
- [x] Agregar componentes iniciales:
  - Button, Input, Dialog, Select
  - Toast (Sonner), Card, Badge
  - Dropdown Menu, Tabs
- [x] Configurar theme provider

**Implementación Real:**
- shadcn CLI 3.6.2
- Componentes instalados: button, input, dialog, select, sonner, card, badge, dropdown-menu, tabs
- Configuración en `components.json`

**Criterio de aceptación:**
- ✅ Componentes shadcn/ui disponibles
- ✅ Estilo consistente con Tailwind
- ✅ Todos los componentes renderizando correctamente

---

### 4. Configuración Supabase ✅

**Tasks:**
- [x] Crear proyecto en Supabase (región São Paulo)
- [x] Copiar credenciales
- [x] Instalar `@supabase/ssr` y `@supabase/supabase-js`
- [x] Crear clientes Supabase:
  - `src/lib/supabase/client.ts` (browser)
  - `src/lib/supabase/server.ts` (server)
  - `src/lib/supabase/proxy.ts` (middleware replacement)
- [x] Configurar variables de entorno
- [x] Implementar proxy pattern para Next.js 16

**Implementación Real:**
- @supabase/ssr v0.8.0
- @supabase/supabase-js v2.89.0
- Proxy pattern en lugar de middleware (Next.js 16)
- Archivo `src/proxy.ts` para manejar sesiones

**Criterio de aceptación:**
- ✅ Conexión a Supabase funcionando
- ✅ Clientes configurados correctamente
- ✅ Variables de entorno seguras
- ✅ Proxy pattern implementado

---

### 5. Configuración Drizzle ORM ✅

**Tasks:**
- [x] Instalar drizzle-orm, postgres, drizzle-kit
- [x] Crear `drizzle.config.ts`
- [x] Crear schemas en `src/db/schema/`:
  - users (con preferencias)
  - tasks (con campos ADHD-friendly)
  - reminders
  - tags y task_tags (relación many-to-many)
  - subscriptions
  - payments
- [x] Crear `src/db/index.ts` (Drizzle client)
- [x] Configurar scripts en `package.json`
- [x] Aplicar schema con `pnpm db:push`

**Implementación Real:**
- drizzle-orm v0.45.1
- drizzle-kit v0.31.8
- postgres driver v3.4.7
- 7 tablas creadas (NO boards ni invoices ni notifications)
- Tasks con campos: status, priority, dueDate, estimatedDuration, actualDuration, isRecurring, recurrencePattern, parentTaskId

**Criterio de aceptación:**
- ✅ Schema definido en TypeScript
- ✅ Tablas creadas en Supabase
- ✅ Database URL con quotes para funcionar correctamente

---

### 6. Configuración de Database Triggers & RLS ✅

**Tasks:**
- [x] Crear SQL script con triggers `update_updated_at`
- [x] Crear trigger para sync auth.users con public.users
- [x] Aplicar Row Level Security (RLS) a todas las tablas
- [x] Ejecutar scripts en Supabase SQL Editor

**Implementación Real:**
- Archivo: `src/db/migrations/0001_rls_policies.sql`
- Archivo: `src/db/migrations/0002_triggers.sql`
- RLS habilitado en: users, tasks, reminders, tags, task_tags, subscriptions, payments
- Políticas: usuarios solo ven sus propios datos
- Trigger de updated_at para todas las tablas
- Trigger para sync automático con auth.users

**Criterio de aceptación:**
- ✅ Triggers funcionando
- ✅ RLS aplicado a todas las tablas
- ✅ Seguridad validada

---

### 7. Configuración ESLint & Prettier ✅

**Tasks:**
- [x] Configurar ESLint con Next.js
- [x] Instalar Prettier con plugins
- [x] Crear `.prettierrc` y `.prettierignore`
- [x] Agregar scripts de lint y format
- [x] Resolver conflictos ESLint/Prettier

**Implementación Real:**
- ESLint flat config (Next.js 16)
- Prettier 3.7.4
- eslint-config-prettier y eslint-plugin-prettier
- Scripts: `lint`, `lint:fix`, `format`, `format:check`, `type-check`
- Configuración simplificada en `eslint.config.mjs`

**Criterio de aceptación:**
- ✅ Linter sin errores
- ✅ Auto-format funcionando
- ✅ Type-check pasando

---

### 8. Setup Vercel Deploy ✅

**Tasks:**
- [x] Crear repositorio en GitHub
- [x] Push código inicial
- [x] Conectar repo a Vercel
- [x] Configurar environment variables en Vercel
- [x] Configurar auto-deploy
- [x] Resolver problemas de deployment

**Implementación Real:**
- Proyecto: flime-ai.vercel.app
- Región: São Paulo (gru1)
- Variables configuradas: NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY, NEXT_PUBLIC_SENTRY_DSN, SENTRY_AUTH_TOKEN
- Auto-deploy configurado en main branch

**Criterio de aceptación:**
- ✅ Deploy exitoso en Vercel
- ✅ URL accesible y funcionando
- ✅ Auto-deploy configurado

---

### 9. GitHub CI/CD ✅

**Tasks:**
- [x] Crear `.github/workflows/ci.yml` (lint, type-check, format-check, build)
- [x] Crear `.github/workflows/deploy.yml` (production deployment)
- [x] Crear `.github/workflows/preview.yml` (PR previews)
- [x] Configurar GitHub Secrets
- [x] Configurar GitHub Environments (SUPABASE, VERCEL)
- [x] Test workflows

**Implementación Real:**
- 3 workflows funcionando
- Secrets configurados: NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY, DATABASE_URL, VERCEL_TOKEN, VERCEL_ORG_ID, VERCEL_PROJECT_ID, SENTRY_AUTH_TOKEN, NEXT_PUBLIC_SENTRY_DSN
- Environments: SUPABASE (para CI build), VERCEL (para deploys)
- pnpm usado en todos los workflows

**Criterio de aceptación:**
- ✅ CI corre en cada push
- ✅ Lint + Type check + Format check + Build pasan
- ✅ Deployment automático funcionando
- ✅ Preview deployments en PRs

---

### 10. Configuración de Dependencias Adicionales ✅

**Tasks:**
- [x] Instalar TanStack Query
- [x] Instalar React Hook Form + Zod
- [x] Instalar date-fns
- [x] Crear providers y validaciones
- [x] Crear utilidades de fecha con locale español

**Implementación Real:**
- @tanstack/react-query v5.90.16
- react-hook-form v7.69.0
- @hookform/resolvers v5.2.2
- zod v4.2.1
- date-fns v4.1.0
- QueryProvider creado en `src/lib/providers/query-provider.tsx`
- Validaciones en `src/lib/validations/` (auth.ts, task.ts)
- Utilidades de fecha en `src/lib/utils/date.ts` con locale español

**Criterio de aceptación:**
- ✅ Todas las dependencias instaladas
- ✅ Providers configurados
- ✅ Type-safety funcionando

---

### 11. Setup Monitoring con Sentry ✅

**Tasks:**
- [x] Crear cuenta en Sentry
- [x] Instalar @sentry/nextjs
- [x] Crear archivos de configuración:
  - `instrumentation.ts` (registro)
  - `instrumentation-client.ts` (cliente)
  - `sentry.server.config.ts` (servidor)
  - `sentry.edge.config.ts` (edge)
- [x] Configurar Next.js con `withSentryConfig`
- [x] Crear página de ejemplo y API de test
- [x] Crear global-error.tsx
- [x] Configurar source maps
- [x] Configurar Sentry en CI/CD
- [x] Test de captura de errores

**Implementación Real:**
- @sentry/nextjs v10.32.1
- Organización: daniel-perez-org
- Proyecto: javascript-nextjs
- Session Replay habilitado
- Performance monitoring (tracing)
- Source maps subiendo automáticamente en builds
- Página de prueba: /sentry-example-page
- Archivo `.env.sentry-build-plugin` para auth token
- Variables en Vercel configuradas

**Criterio de aceptación:**
- ✅ Sentry capturando errores
- ✅ Source maps funcionando
- ✅ Dashboards configurados
- ✅ CI/CD integrando Sentry

**Nota:** PostHog pospuesto para fase posterior

---

## 🧪 Testing & Validation

### Definition of Done (Sprint 1)
- [x] `pnpm dev` corre sin errores
- [x] `pnpm build` completa exitosamente
- [x] `pnpm lint` pasa sin warnings
- [x] Deploy en Vercel accesible
- [x] Database schema aplicado en Supabase
- [x] RLS verificado (usuarios no pueden ver datos de otros)
- [x] CI/CD pasando en GitHub Actions
- [x] Sentry capturando errores

### Manual Testing Checklist
- [x] Abrir proyecto en localhost:3000
- [x] Verificar Tailwind CSS funcionando
- [x] Verificar componentes shadcn/ui renderizan
- [x] Verificar que Drizzle puede conectarse a DB
- [x] Forzar un error y verificar Sentry
- [x] Deploy a Vercel y verificar producción

---

## 📊 Sprint Metrics

**Horas estimadas:** 14 horas
**Horas reales:** ~16 horas (incluyendo troubleshooting)

**Tasks completados:** 11/11 (100%)

**Cambios vs Plan Original:**
- ✅ Next.js 16 en lugar de 15 (upgrade de versión)
- ✅ Proxy pattern en lugar de middleware (cambio de Next.js 16)
- ✅ 7 tablas en lugar de 6 (sin boards, notifications, invoices - con tags, task_tags, payments)
- ✅ PostHog pospuesto
- ✅ pnpm en lugar de npm

---

## 🚧 Blockers & Resolución

**Blocker 1:** Middleware deprecado en Next.js 16
- **Solución:** Migrar a proxy.ts pattern

**Blocker 2:** Drizzle no leía .env.local
- **Solución:** Crear archivo .env adicional

**Blocker 3:** Error circular en tasks schema
- **Solución:** Remover .references() de parentTaskId

**Blocker 4:** ESLint circular structure con FlatCompat
- **Solución:** Simplificar config directamente importando eslint-config-next

**Blocker 5:** pnpm version conflict en GitHub Actions
- **Solución:** Remover version specification, usar packageManager de package.json

**Blocker 6:** Deploy workflow sin acceso a secrets
- **Solución:** Agregar environment: VERCEL al workflow

**Blocker 7:** Vercel sin variables de Supabase
- **Solución:** Agregar variables de entorno en Vercel dashboard

---

## 🎓 Learning Outcomes

**Al final de Sprint 1 aprendimos:**
- ✅ Cómo configurar Next.js 16 App Router con proxy pattern
- ✅ Cómo integrar Supabase con Next.js usando @supabase/ssr
- ✅ Cómo usar Drizzle ORM para schema y migrations
- ✅ Cómo aplicar Row Level Security en Supabase
- ✅ Cómo configurar CI/CD con GitHub Actions y Environments
- ✅ Cómo deployar en Vercel con variables de entorno
- ✅ Cómo integrar Sentry para error monitoring
- ✅ Cómo usar pnpm como package manager
- ✅ Cómo configurar Tailwind CSS v4 con PostCSS

---

## 📝 Stack Tecnológico Final

**Frontend:**
- Next.js 16.1.1 (App Router)
- React 19.2.3
- TypeScript 5.9.3
- Tailwind CSS v4.1.18

**Backend & Database:**
- Supabase (PostgreSQL 15)
- Drizzle ORM v0.45.1
- Row Level Security
- Database Triggers

**Development Tools:**
- pnpm 10.26.2
- ESLint 9.39.2
- Prettier 3.7.4
- TanStack Query v5.90.16
- React Hook Form v7.69.0
- Zod v4.2.1
- date-fns v4.1.0

**UI Components:**
- shadcn/ui v3.6.2
- Radix UI primitives
- Lucide React icons
- Sonner (toasts)

**Monitoring & Observability:**
- Sentry v10.32.1 (Error tracking, Session Replay, Performance)

**CI/CD:**
- GitHub Actions (3 workflows)
- Vercel (Deployments)

---

## 🏁 Sprint Review

**¿Logramos el sprint goal?**
✅ Sí, completamente. El proyecto está configurado profesionalmente y listo para desarrollo de features.

**¿Qué salió bien?**
- Configuración completa sin tech debt significativo
- CI/CD funcionando desde día 1
- Sentry capturando errores correctamente
- Esquema de base de datos bien diseñado con RLS

**¿Qué salió mal?**
- Varios blockers por cambios en Next.js 16 (middleware → proxy)
- Tiempo extra en troubleshooting de CI/CD con environments
- Problemas con variables de entorno en Vercel

**¿Qué hacer diferente en Sprint 2?**
- Leer release notes de versiones nuevas antes de usar
- Configurar variables de entorno desde el inicio
- Testear CI/CD más temprano en el sprint

**Tech debt:**
- ❌ No hay tech debt significativo
- ✅ Código limpio y bien organizado
- ✅ Todos los workflows pasando

---

## ➡️ Next Sprint

**Sprint 2 - Authentication & User Management:**
- Implementar sistema de autenticación con Supabase Auth
- Páginas de Signup/Login/Logout
- Protected routes
- Onboarding wizard
- User profile settings

---

**Sprint Owner:** Daniel Pérez
**Start Date:** Diciembre 30, 2024
**End Date:** Diciembre 31, 2024
**Status:** 🔵 Completado
**Deploy:** https://flime-ai.vercel.app
