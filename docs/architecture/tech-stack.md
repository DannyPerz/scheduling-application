# Flime.ai - Tech Stack & Architecture Decisions

**Versión:** 2.0 (Actual Implementation)
**Fecha:** Diciembre 31, 2024
**Status:** ✅ Implementado en Sprint 1

---

## 🎯 Stack Selection Principles

### Decision Criteria
1. **Velocidad de desarrollo** - HMR instantáneo, DX óptima
2. **Type safety** - TypeScript end-to-end
3. **Costo bajo** - Free tier generoso para MVP
4. **Escalabilidad** - Preparado para crecer sin refactors mayores
5. **Experiencia del fundador** - Aprovechar conocimiento de React/Next.js
6. **Comunidad activa** - Soporte, librerías, troubleshooting
7. **ADHD-Friendly** - Stack que permita desarrollo rápido con buena DX

### Non-negotiables
- ✅ TypeScript estricto
- ✅ HMR < 1 segundo
- ✅ Deploy en < 5 minutos
- ✅ Free tier para MVP (< $50 USD/mes)
- ✅ Responsive by default
- ✅ CI/CD automatizado desde día 1

---

## 🏗️ Architecture Overview

### Pattern: JAMstack SaaS
- **Frontend:** Next.js 16 (React) con App Router
- **Backend:** Next.js API Routes (serverless)
- **Database:** PostgreSQL 15 (Supabase)
- **Auth:** Supabase Auth (Email + OAuth)
- **Deploy:** Vercel (Edge Network - São Paulo)
- **Monitoring:** Sentry (Error Tracking + Performance)

### Why JAMstack?
- ✅ Performance extremo (CDN + Edge)
- ✅ Scaling automático
- ✅ Costo bajo (pay-as-you-go)
- ✅ DX increíble (preview deployments)
- ✅ CI/CD desde día 1

---

## 💻 Frontend Stack

### Next.js 16.1.1 (App Router)
**Versión:** 16.1.1 (latest stable)

**Por qué Next.js:**
- ✅ React 19.2.3 (Server Components)
- ✅ App Router (file-based routing)
- ✅ Turbopack (HMR ultra rápido)
- ✅ Image optimization automática
- ✅ SEO-friendly (SSR/SSG)
- ✅ API Routes built-in

**Cambio importante:** Next.js 16 deprecó `middleware.ts`, ahora usamos **proxy.ts pattern**

**Configuración:**
```typescript
// next.config.ts
import { withSentryConfig } from '@sentry/nextjs'
import type { NextConfig } from 'next'

const config: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'avatars.githubusercontent.com',
      },
    ],
  },
}

export default withSentryConfig(config, {
  org: 'daniel-perez-org',
  project: 'javascript-nextjs',
  authToken: process.env.SENTRY_AUTH_TOKEN,
  silent: !process.env.CI,
  widenClientFileUpload: true,
  reactComponentAnnotation: {
    enabled: true,
  },
  tunnelRoute: '/monitoring',
  disableLogger: true,
  automaticVercelMonitors: true,
})
```

---

### TypeScript 5.9.3
**Por qué TypeScript:**
- ✅ Type safety end-to-end
- ✅ Autocomplete en VSCode
- ✅ Refactoring seguro
- ✅ Menos bugs en producción

**Configuración:**
```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "jsx": "preserve",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

### Tailwind CSS v4.1.18
**Versión:** 4.1.18 (stable)

**Por qué Tailwind v4:**
- ✅ Velocidad extrema (nuevo engine en Rust)
- ✅ HMR instantáneo
- ✅ Utility-first (rápido de escribir)
- ✅ Bundle size pequeño (purge automático)
- ✅ Design system consistent

**Configuración:**
```javascript
// postcss.config.mjs
export default {
  plugins: {
    '@tailwindcss/postcss': {},
  },
}
```

**CSS Variables en app/globals.css:**
```css
@import "tailwindcss";

@theme {
  --color-primary: #3b82f6;
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-danger: #ef4444;
}
```

---

### shadcn/ui v3.6.2
**Por qué shadcn/ui:**
- ✅ Componentes hermosos pre-hechos
- ✅ Basado en Radix UI (accesibilidad)
- ✅ Customizable (no es librería, son archivos)
- ✅ TypeScript nativo
- ✅ Tailwind CSS v4 compatible

**Componentes instalados (Sprint 1):**
- Button
- Input
- Dialog
- Select
- Sonner (Toast)
- Card
- Badge
- Dropdown Menu
- Tabs

**Instalación:**
```bash
pnpm dlx shadcn@latest init
pnpm dlx shadcn@latest add button input dialog select sonner card badge dropdown-menu tabs
```

---

### React Hook Form v7.69.0 + Zod v4.2.1
**Por qué:**
- ✅ Validación type-safe
- ✅ Performance (uncontrolled forms)
- ✅ UX excelente (validación en tiempo real)

**Implementación:**
```typescript
// src/lib/validations/task.ts
import { z } from 'zod'

export const createTaskSchema = z.object({
  title: z.string().min(1, 'El título es requerido').max(200),
  description: z.string().max(1000).optional(),
  dueDate: z.date().optional(),
  priority: z.enum(['low', 'medium', 'high', 'urgent']),
  status: z.enum(['pending', 'in_progress', 'completed', 'archived']),
  estimatedDuration: z.number().int().positive().optional(),
})

export type CreateTaskInput = z.infer<typeof createTaskSchema>
```

---

### TanStack Query v5.90.16 (React Query)
**Por qué:**
- ✅ Cache inteligente
- ✅ Sincronización automática
- ✅ Optimistic updates
- ✅ Less boilerplate

**Configuración:**
```typescript
// src/lib/providers/query-provider.tsx
'use client'

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function QueryProvider({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            staleTime: 60 * 1000, // 1 minuto
            refetchOnWindowFocus: false,
          },
        },
      })
  )

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}
```

---

### date-fns v4.1.0
**Por qué:**
- ✅ Tree-shakeable (solo importas lo que usas)
- ✅ Funcional, inmutable
- ✅ TypeScript nativo
- ✅ i18n incluido (español configurado)

**Implementación:**
```typescript
// src/lib/utils/date.ts
import { format, formatDistanceToNow, addDays, isBefore } from 'date-fns'
import { es } from 'date-fns/locale'

export const formatDate = (date: Date, pattern: string = 'PPP') => {
  return format(date, pattern, { locale: es })
}

export const formatRelative = (date: Date) => {
  return formatDistanceToNow(date, { locale: es, addSuffix: true })
}
```

---

## 🗄️ Backend Stack

### Supabase (BaaS)
**Región:** São Paulo (aws-1-sa-east-1)
**PostgreSQL:** 15

**Por qué Supabase:**
- ✅ PostgreSQL (database robusto)
- ✅ Auth built-in (email, OAuth)
- ✅ Row Level Security (RLS)
- ✅ Realtime subscriptions (WebSockets) - Fase 2
- ✅ Storage (S3-like) - Fase 2
- ✅ Free tier generoso

**Servicios usados (Sprint 1):**
1. **Database** - PostgreSQL 15 (7 tablas)
2. **Auth** - Email/password + OAuth (Fase 2)
3. **RLS** - Row Level Security habilitado

**Configuración:**
```typescript
// src/lib/supabase/client.ts (Browser)
import { createBrowserClient } from '@supabase/ssr'

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

```typescript
// src/lib/supabase/server.ts (Server Components)
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createClient() {
  const cookieStore = await cookies()

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll()
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value, options }) =>
            cookieStore.set(name, value, options)
          )
        },
      },
    }
  )
}
```

```typescript
// src/proxy.ts (Next.js 16 pattern)
import { type NextRequest } from 'next/server'
import { updateSession } from '@/lib/supabase/proxy'

export async function proxy(request: NextRequest) {
  return await updateSession(request)
}

export const config = {
  matcher: [
    '/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg|jpeg|gif|webp)$).*)',
  ],
}
```

---

### Drizzle ORM v0.45.1
**Por qué Drizzle > Prisma:**
- ✅ Más ligero y rápido
- ✅ SQL-like (más control)
- ✅ Type-safe queries
- ✅ Mejor integración con Supabase
- ✅ Migrations simples

**Esquema implementado (Sprint 1):**
```typescript
// src/db/schema/users.ts
export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').unique().notNull(),
  fullName: text('full_name').notNull(),
  avatarUrl: text('avatar_url'),
  plan: text('plan', { enum: ['free', 'premium', 'team'] })
    .default('free')
    .notNull(),
  timezone: text('timezone').default('America/Bogota').notNull(),
  language: text('language', { enum: ['es', 'en'] }).default('es').notNull(),
  // ... preferences fields
  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**7 tablas implementadas:**
1. `users` - Usuarios con preferencias
2. `tasks` - Tareas con campos ADHD-friendly
3. `reminders` - Recordatorios para tareas
4. `tags` - Etiquetas para organizar
5. `task_tags` - Relación many-to-many
6. `subscriptions` - Suscripciones Premium
7. `payments` - Historial de pagos

**Migrations:**
```bash
pnpm db:push  # Push schema directly to Supabase
pnpm db:generate  # Generate migration files
pnpm db:migrate  # Run migrations
pnpm db:studio  # Open Drizzle Studio
```

**Manual SQL (ejecutado en Supabase SQL Editor):**
- `src/db/migrations/0001_rls_policies.sql` - Row Level Security
- `src/db/migrations/0002_triggers.sql` - Auto update timestamps + auth sync

---

### Next.js API Routes (Serverless)
**Por qué:**
- ✅ Serverless (scaling automático)
- ✅ Co-located con frontend
- ✅ TypeScript end-to-end
- ✅ Edge Runtime opcional

**Estructura planificada:**
```
src/app/api/
├── auth/
│   ├── signup/route.ts
│   ├── login/route.ts
│   └── callback/route.ts
├── tasks/
│   ├── route.ts (GET, POST)
│   └── [id]/route.ts (GET, PUT, DELETE)
├── reminders/
│   └── route.ts
└── webhooks/
    └── mercadopago/route.ts
```

---

## 🔐 Authentication (Fase 2 - Sprint 2)

### Supabase Auth
**Métodos planificados:**
1. **Email + Password**
   - Password hasheado (bcrypt)
   - Email verification

2. **Google OAuth**
   - Supabase maneja el flujo completo
   - Callback automático

**Session Management:**
- JWT tokens (HttpOnly cookies)
- Refresh token rotation automático
- Server-side validation con proxy.ts

---

## 💳 Payments (Fase 3)

### Mercado Pago
**Por qué Mercado Pago:**
- ✅ Acepta persona natural en Colombia
- ✅ Fácil integración
- ✅ Comisiones razonables (~3.5%)
- ✅ Webhooks confiables

**Flujo planificado:**
1. Usuario click "Upgrade to Premium"
2. Backend crea preferencia de pago
3. Redirect a checkout de Mercado Pago
4. Usuario paga
5. Webhook confirma pago → tabla `payments`
6. Backend actualiza `users.plan` y crea registro en `subscriptions`

---

## 📧 Email (Fase 2)

### Resend
**Por qué Resend:**
- ✅ Developer-friendly (API simple)
- ✅ Templates con React
- ✅ Free tier: 3,000 emails/mes
- ✅ Deliverability excelente

**Tipos de emails planificados:**
1. Verificación de cuenta
2. Reset password
3. Recordatorios de tareas
4. Resumen diario
5. Recibos de pago

---

## 🚀 Deployment & Infrastructure

### Vercel
**Región:** São Paulo (gru1)
**Plan:** Hobby (Free)

**Por qué Vercel:**
- ✅ Creadores de Next.js (integración perfecta)
- ✅ Deploy en segundos
- ✅ Preview deployments por PR
- ✅ Edge Network global
- ✅ Free tier generoso

**Features implementadas (Sprint 1):**
- ✅ Automatic deploys (push to main)
- ✅ Preview URLs (cada PR)
- ✅ Environment variables configuradas
- ✅ Analytics
- ✅ Sentry integration

**URL:** https://flime-ai.vercel.app

---

### Supabase Cloud
**Región:** São Paulo (aws-1-sa-east-1)
**Plan:** Free tier

**Features usadas:**
- ✅ Managed PostgreSQL 15
- ✅ Database con 7 tablas
- ✅ Row Level Security habilitado
- ✅ Triggers y functions
- ✅ Backups automáticos

---

### GitHub Actions (CI/CD)
**Workflows implementados (Sprint 1):**

1. **ci.yml** - Lint, Type Check, Format Check, Build
2. **deploy.yml** - Production deployment
3. **preview.yml** - PR preview deployments

**Secrets configurados:**
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `DATABASE_URL`
- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`
- `SENTRY_AUTH_TOKEN`
- `NEXT_PUBLIC_SENTRY_DSN`

**Environments:**
- `SUPABASE` - Para CI build job
- `VERCEL` - Para deployment workflows

---

## 📊 Monitoring & Analytics

### Error Tracking: Sentry v10.32.1
**Status:** ✅ Implementado en Sprint 1

**Features configuradas:**
- ✅ Error tracking (client + server + edge)
- ✅ Session Replay (masked PII)
- ✅ Performance monitoring
- ✅ Source maps upload automático
- ✅ Vercel Cron Monitors

**Configuración:**
```typescript
// instrumentation.ts
import * as Sentry from '@sentry/nextjs'

export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    await import('./sentry.server.config')
  }
  if (process.env.NEXT_RUNTIME === 'edge') {
    await import('./sentry.edge.config')
  }
}

export const onRequestError = Sentry.captureRequestError
```

**Páginas de prueba:**
- `/sentry-example-page` - Test de errores
- `/api/sentry-example-api` - Test API route

---

### Product Analytics: PostHog
**Status:** ⏸️ Pospuesto para después de Sprint 1

**Eventos planificados:**
- User signup
- Task created/completed
- Reminder sent
- Upgrade to Premium
- Churn events

---

## 🧪 Testing Stack (Fase 2)

### Unit & Integration: Vitest
**Status:** Planificado para Fase 2

**Por qué Vitest > Jest:**
- ✅ Más rápido
- ✅ ESM nativo
- ✅ Vite powered
- ✅ Compatible con Jest API

---

### E2E: Playwright
**Status:** Planificado para Fase 3

---

## 🗂️ Project Structure (Implementado)

```
flime/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── layout.tsx         # Root layout con providers
│   │   ├── page.tsx           # Landing page
│   │   ├── global-error.tsx   # Sentry error boundary
│   │   ├── sentry-example-page/ # Test Sentry
│   │   └── api/               # API routes (Fase 2)
│   ├── components/            # React components
│   │   └── ui/               # shadcn/ui components
│   ├── lib/                   # Utilities
│   │   ├── providers/        # React Query, Theme
│   │   ├── supabase/         # Client, Server, Proxy
│   │   ├── utils/            # Helpers, date utils
│   │   └── validations/      # Zod schemas
│   ├── db/                    # Drizzle ORM
│   │   ├── schema/           # Database schemas
│   │   ├── migrations/       # SQL migrations
│   │   └── index.ts          # DB client
│   ├── types/                 # TypeScript types
│   └── emails/                # Email templates (Fase 2)
├── public/                    # Static assets
├── .github/
│   └── workflows/            # CI/CD workflows
├── docs/                      # Documentation
├── planning/                  # Sprints, roadmap
└── design/                    # Wireframes (Fase 2)
```

---

## 🔧 Development Tools

### Package Manager: pnpm v10.26.2
**Por qué pnpm:**
- ✅ Más rápido que npm/yarn
- ✅ Disk space efficient (symlinks)
- ✅ Strict node_modules structure
- ✅ Monorepo support

**Configuración:**
```json
// package.json
{
  "packageManager": "pnpm@10.26.2"
}
```

### Linting & Formatting

**ESLint v9.39.2** (Flat Config)
```javascript
// eslint.config.mjs
import nextPlugin from 'eslint-config-next'

const eslintConfig = [
  {
    ignores: ['.next/**', 'node_modules/**', 'out/**', 'dist/**'],
  },
  ...nextPlugin,
  {
    rules: {
      '@typescript-eslint/no-unused-vars': ['warn', { argsIgnorePattern: '^_', varsIgnorePattern: '^_' }],
      '@typescript-eslint/no-explicit-any': 'warn',
      'no-console': ['warn', { allow: ['warn', 'error'] }],
    },
  },
]

export default eslintConfig
```

**Prettier v3.7.4**
```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

### Scripts npm
```json
{
  "scripts": {
    "dev": "next dev --turbo",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "type-check": "tsc --noEmit",
    "db:push": "drizzle-kit push",
    "db:generate": "drizzle-kit generate",
    "db:studio": "drizzle-kit studio"
  }
}
```

---

## 💰 Cost Breakdown (MVP)

| Servicio | Tier | Costo/mes |
|----------|------|-----------|
| Vercel | Hobby | $0 |
| Supabase | Free | $0 |
| Resend | Free (3k emails) | $0 |
| Mercado Pago | Comisión | 3.5% por transacción |
| Dominio | .ai | $2/mes (~$25/año) |
| Sentry | Free (5k errors) | $0 |
| PostHog | Pospuesto | $0 |
| **TOTAL MVP** | | **~$2 USD/mes** |

**Cuando escale:**
- Vercel Pro: $20/mes
- Supabase Pro: $25/mes
- Resend Pro: $20/mes
- **Total escalado:** ~$65-80 USD/mes

---

## 🚦 Performance Targets

### Core Web Vitals (Objetivos)
- **LCP (Largest Contentful Paint):** < 2.5s
- **FID (First Input Delay):** < 100ms
- **CLS (Cumulative Layout Shift):** < 0.1

### Lighthouse Scores (Objetivos)
- **Performance:** > 90
- **Accessibility:** > 95
- **Best Practices:** > 95
- **SEO:** > 95

### API Response Times (Objetivos)
- GET requests: < 200ms (p95)
- POST requests: < 500ms (p95)
- Database queries: < 100ms (p95)

---

## 🔐 Security Checklist

- [x] HTTPS only (enforced by Vercel)
- [x] CORS configurado correctamente
- [ ] Rate limiting en API routes (Fase 2)
- [x] SQL injection protection (Drizzle ORM)
- [x] XSS protection (React auto-escaping)
- [x] CSRF tokens (Next.js built-in)
- [x] Secure headers (next.config.ts)
- [x] Environment variables seguras (Vercel + GitHub)
- [x] Row Level Security (Supabase RLS)
- [ ] Password hashing (Supabase Auth - Fase 2)
- [x] Input validation (Zod schemas)

---

## 📝 Environment Variables

```bash
# .env.local (development)
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=xxx

DATABASE_URL="postgresql://postgres.xxx:xxx@aws-1-sa-east-1.pooler.supabase.com:6543/postgres"

NEXT_PUBLIC_SENTRY_DSN=https://xxx@xxx.ingest.sentry.io/xxx
SENTRY_AUTH_TOKEN=xxx

# Fase 2:
# MERCADOPAGO_ACCESS_TOKEN=xxx
# MERCADOPAGO_PUBLIC_KEY=xxx
# RESEND_API_KEY=xxx
# NEXT_PUBLIC_POSTHOG_KEY=xxx
```

---

## 🎓 Learning Resources

**Next.js:**
- Official Docs: https://nextjs.org/docs
- Next.js 16 Guide: https://nextjs.org/blog/next-16

**Supabase:**
- Docs: https://supabase.com/docs
- Auth Guide: https://supabase.com/docs/guides/auth
- RLS Guide: https://supabase.com/docs/guides/auth/row-level-security

**Drizzle ORM:**
- Docs: https://orm.drizzle.team/docs/overview
- Supabase Guide: https://orm.drizzle.team/docs/guides/supabase

**Tailwind CSS v4:**
- Docs: https://tailwindcss.com/docs
- v4 Changelog: https://tailwindcss.com/blog/tailwindcss-v4

**Sentry:**
- Next.js Docs: https://docs.sentry.io/platforms/javascript/guides/nextjs/

---

## 📋 Tech Stack Summary (Sprint 1)

**Frontend:**
- Next.js 16.1.1 (App Router)
- React 19.2.3
- TypeScript 5.9.3
- Tailwind CSS v4.1.18
- shadcn/ui v3.6.2

**Backend & Database:**
- Supabase (PostgreSQL 15, São Paulo)
- Drizzle ORM v0.45.1
- postgres driver v3.4.7
- 7 tablas implementadas

**Development Tools:**
- pnpm 10.26.2
- ESLint 9.39.2 (Flat Config)
- Prettier 3.7.4
- TanStack Query v5.90.16
- React Hook Form v7.69.0
- Zod v4.2.1
- date-fns v4.1.0

**UI Components:**
- shadcn/ui (Radix UI primitives)
- Lucide React icons
- Sonner (toasts)

**Monitoring:**
- Sentry v10.32.1 (Error tracking, Session Replay, Performance)

**CI/CD:**
- GitHub Actions (3 workflows)
- Vercel Deployments (São Paulo)

---

## 📝 Design Decisions

### Why Next.js 16 over Next.js 15?
**Decision:** Upgraded to Next.js 16.1.1 durante Sprint 1

**Rationale:**
- ✅ Latest stable release
- ✅ React 19 support
- ⚠️ Breaking change: middleware.ts → proxy.ts

### Why proxy.ts pattern?
**Decision:** Usar proxy.ts en lugar de middleware.ts

**Rationale:**
- ✅ Next.js 16 deprecó middleware.ts
- ✅ proxy.ts es el nuevo patrón recomendado
- ✅ Mejor performance en Edge Runtime

### Why pnpm over npm/yarn?
**Decision:** pnpm como package manager

**Rationale:**
- ✅ Más rápido que npm
- ✅ Disk space efficient
- ✅ GitHub Actions support nativo

### Why 7 tables instead of 6?
**Decision:** Implementar 7 tablas (users, tasks, reminders, tags, task_tags, subscriptions, payments)

**Rationale:**
- ✅ NO boards (tags son suficientes para MVP)
- ✅ NO notifications_log (reminders.sent es suficiente)
- ✅ NO invoices (payments cubre MVP, facturación formal en Fase 2)
- ✅ SÍ reminders (core feature para ADHD)
- ✅ SÍ tags + task_tags (organización flexible)

### Why postpone PostHog?
**Decision:** Posponer PostHog para después de Sprint 1

**Rationale:**
- ✅ Sentry cubre error tracking
- ✅ Analytics no es crítico para MVP
- ✅ Enfoque en features core primero

---

**Última actualización:** Diciembre 31, 2024 (v2.0 - Sprint 1 Implementation)
**Próxima revisión:** Post Sprint 2 (Authentication & User Management)
**Status:** ✅ Sprint 1 Completado
**Deploy:** https://flime-ai.vercel.app
