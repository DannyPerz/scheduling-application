# Flime.ai - Tech Stack & Architecture Decisions

**Versión:** 1.0
**Fecha:** Diciembre 30, 2024
**Status:** Approved

---

## 🎯 Stack Selection Principles

### Decision Criteria
1. **Velocidad de desarrollo** - HMR instantáneo, DX óptima
2. **Type safety** - TypeScript end-to-end
3. **Costo bajo** - Free tier generoso para MVP
4. **Escalabilidad** - Preparado para crecer sin refactors mayores
5. **Experiencia del fundador** - Aprovechar conocimiento de React/Next.js
6. **Comunidad activa** - Soporte, librerías, troubleshooting

### Non-negotiables
- ✅ TypeScript estricto
- ✅ HMR < 1 segundo
- ✅ Deploy en < 5 minutos
- ✅ Free tier para MVP (< $50 USD/mes)
- ✅ Responsive by default

---

## 🏗️ Architecture Overview

### Pattern: JAMstack SaaS
- **Frontend:** Next.js (React) con SSR/SSG
- **Backend:** Next.js API Routes (serverless)
- **Database:** PostgreSQL (Supabase)
- **Auth:** Supabase Auth
- **Deploy:** Vercel (edge network)

### Why JAMstack?
- ✅ Performance extremo (CDN + Edge)
- ✅ Scaling automático
- ✅ Costo bajo (pay-as-you-go)
- ✅ DX increíble (preview deployments)

---

## 💻 Frontend Stack

### Next.js 15 (App Router)
**Versión:** 15.x (latest stable)

**Por qué Next.js:**
- ✅ React Server Components (menos JS en cliente)
- ✅ App Router (file-based routing)
- ✅ Turbopack (HMR ultra rápido)
- ✅ Image optimization automática
- ✅ SEO-friendly (SSR/SSG)
- ✅ API Routes built-in

**Configuración:**
```typescript
// next.config.ts
import type { NextConfig } from 'next'

const config: NextConfig = {
  typescript: {
    strict: true, // Estricto
  },
  experimental: {
    turbo: true, // Turbopack para dev
  },
  images: {
    domains: ['avatars.githubusercontent.com'], // Google OAuth avatars
  },
}

export default config
```

---

### TypeScript 5.x
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

### Tailwind CSS v4
**Versión:** 4.x (latest)

**Por qué Tailwind v4:**
- ✅ Velocidad extrema (nuevo engine en Rust)
- ✅ HMR instantáneo
- ✅ Utility-first (rápido de escribir)
- ✅ Bundle size pequeño (purge automático)
- ✅ Design system consistent

**Configuración:**
```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6', // Azul principal
          600: '#2563eb',
          700: '#1d4ed8',
        },
        success: '#10b981',
        warning: '#f59e0b',
        danger: '#ef4444',
      },
    },
  },
  plugins: [],
}

export default config
```

---

### shadcn/ui
**Por qué shadcn/ui:**
- ✅ Componentes hermosos pre-hechos
- ✅ Basado en Radix UI (accesibilidad)
- ✅ Customizable (no es librería, son archivos)
- ✅ TypeScript nativo
- ✅ Tailwind CSS compatible

**Componentes a usar:**
- Button
- Input, Textarea
- Select, Dropdown
- Dialog (Modal)
- Calendar, DatePicker
- Toast (notificaciones)
- Tabs
- Card
- Badge
- Avatar

**Instalación:**
```bash
npx shadcn-ui@latest init
npx shadcn-ui@latest add button input select dialog calendar toast
```

---

### React Hook Form + Zod
**Por qué:**
- ✅ Validación type-safe
- ✅ Performance (uncontrolled forms)
- ✅ UX excelente (validación en tiempo real)

**Ejemplo:**
```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const taskSchema = z.object({
  title: z.string().min(1, 'Título requerido').max(200),
  description: z.string().max(1000).optional(),
  dueDate: z.date().optional(),
  priority: z.enum(['high', 'medium', 'low']),
})

type TaskFormData = z.infer<typeof taskSchema>

const { register, handleSubmit } = useForm<TaskFormData>({
  resolver: zodResolver(taskSchema),
})
```

---

### TanStack Query (React Query)
**Por qué:**
- ✅ Cache inteligente
- ✅ Sincronización automática
- ✅ Optimistic updates
- ✅ Less boilerplate

**Ejemplo:**
```typescript
import { useQuery, useMutation } from '@tanstack/react-query'

// Fetch tasks
const { data: tasks } = useQuery({
  queryKey: ['tasks'],
  queryFn: fetchTasks,
})

// Create task
const createTask = useMutation({
  mutationFn: createTaskAPI,
  onSuccess: () => {
    queryClient.invalidateQueries(['tasks'])
  },
})
```

---

### date-fns
**Por qué:**
- ✅ Tree-shakeable (solo importas lo que usas)
- ✅ Funcional, inmutable
- ✅ TypeScript nativo
- ✅ i18n incluido

**Ejemplo:**
```typescript
import { format, addDays, isBefore } from 'date-fns'
import { es } from 'date-fns/locale'

const formatted = format(new Date(), 'PPP', { locale: es })
// "30 de diciembre de 2024"
```

---

## 🗄️ Backend Stack

### Supabase (BaaS)
**Por qué Supabase:**
- ✅ PostgreSQL (database robusto)
- ✅ Auth built-in (email, OAuth)
- ✅ Realtime subscriptions (WebSockets)
- ✅ Row Level Security (RLS)
- ✅ Storage (S3-like)
- ✅ Free tier generoso

**Servicios usados:**
1. **Database** - PostgreSQL 15
2. **Auth** - Email/password + Google OAuth
3. **Realtime** - Tareas en tiempo real (Fase 2)
4. **Storage** - Avatars (Fase 2)

**Configuración:**
```typescript
// src/lib/supabase.ts
import { createClient } from '@supabase/supabase-js'

export const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)
```

---

### Drizzle ORM
**Por qué Drizzle > Prisma:**
- ✅ Más ligero y rápido
- ✅ SQL-like (más control)
- ✅ Type-safe queries
- ✅ Mejor integración con Supabase
- ✅ Migrations simples

**Esquema ejemplo:**
```typescript
// src/db/schema.ts
import { pgTable, uuid, text, timestamp, boolean } from 'drizzle-orm/pg-core'

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').unique().notNull(),
  fullName: text('full_name').notNull(),
  plan: text('plan').default('free'), // 'free' | 'premium' | 'team'
  createdAt: timestamp('created_at').defaultNow(),
})

export const boards = pgTable('boards', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id),
  name: text('name').notNull(),
  color: text('color').notNull(),
  icon: text('icon').notNull(),
  createdAt: timestamp('created_at').defaultNow(),
})

export const tasks = pgTable('tasks', {
  id: uuid('id').primaryKey().defaultRandom(),
  boardId: uuid('board_id').references(() => boards.id),
  title: text('title').notNull(),
  description: text('description'),
  dueDate: timestamp('due_date'),
  priority: text('priority').default('medium'),
  status: text('status').default('todo'), // 'todo' | 'done'
  completedAt: timestamp('completed_at'),
  createdAt: timestamp('created_at').defaultNow(),
})
```

**Migrations:**
```bash
npx drizzle-kit generate:pg
npx drizzle-kit push:pg
```

---

### Next.js API Routes
**Por qué:**
- ✅ Serverless (scaling automático)
- ✅ Co-located con frontend
- ✅ TypeScript end-to-end
- ✅ Edge Runtime opcional

**Estructura:**
```
src/app/api/
├── auth/
│   ├── signup/route.ts
│   ├── login/route.ts
│   └── logout/route.ts
├── boards/
│   ├── route.ts (GET, POST)
│   └── [id]/route.ts (GET, PUT, DELETE)
├── tasks/
│   ├── route.ts
│   └── [id]/route.ts
└── webhooks/
    └── mercadopago/route.ts
```

**Ejemplo:**
```typescript
// src/app/api/tasks/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/db'
import { tasks } from '@/db/schema'

export async function GET(req: NextRequest) {
  const userId = req.headers.get('x-user-id') // Auth middleware

  const userTasks = await db
    .select()
    .from(tasks)
    .where(eq(tasks.userId, userId))

  return NextResponse.json(userTasks)
}
```

---

## 🔐 Authentication

### Supabase Auth
**Métodos:**
1. **Email + Password**
   - Magic link (código 6 dígitos)
   - Password hasheado (bcrypt)

2. **Google OAuth**
   - Supabase maneja el flujo completo
   - Callback automático

**Flujo:**
```typescript
// Signup con email
const { data, error } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'secure-password',
})

// Login con Google
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: `${window.location.origin}/auth/callback`,
  },
})
```

**Session Management:**
- JWT tokens (HttpOnly cookies)
- Refresh token rotation automático
- Server-side validation

---

## 💳 Payments

### Mercado Pago
**Por qué Mercado Pago:**
- ✅ Acepta persona natural en Colombia
- ✅ Fácil integración
- ✅ Comisiones razonables (~3.5%)
- ✅ Webhooks confiables

**Flujo:**
1. Usuario click "Upgrade to Premium"
2. Backend crea preferencia de pago (Mercado Pago API)
3. Redirect a checkout de Mercado Pago
4. Usuario paga
5. Webhook confirma pago
6. Backend actualiza plan del usuario

**Integración:**
```typescript
// src/lib/mercadopago.ts
import { MercadoPagoConfig, Preference } from 'mercadopago'

const client = new MercadoPagoConfig({
  accessToken: process.env.MERCADOPAGO_ACCESS_TOKEN!,
})

const preference = new Preference(client)

// Crear preferencia
const response = await preference.create({
  items: [
    {
      title: 'Flime Premium - Mensual',
      unit_price: 5,
      quantity: 1,
    },
  ],
  back_urls: {
    success: 'https://flime.ai/payment/success',
    failure: 'https://flime.ai/payment/failure',
  },
  notification_url: 'https://flime.ai/api/webhooks/mercadopago',
})
```

---

## 📧 Email

### Resend
**Por qué Resend:**
- ✅ Developer-friendly (API simple)
- ✅ Templates con React
- ✅ Free tier: 3,000 emails/mes
- ✅ Deliverability excelente

**Tipos de emails:**
1. Verificación de cuenta
2. Reset password
3. Recordatorios de tareas
4. Resumen diario
5. Recibos de pago

**Template ejemplo:**
```typescript
// src/emails/task-reminder.tsx
import { Html, Button, Text } from '@react-email/components'

export default function TaskReminder({ task }) {
  return (
    <Html>
      <Text>Recordatorio: {task.title}</Text>
      <Text>Vence en 1 hora</Text>
      <Button href={`https://flime.ai/tasks/${task.id}`}>
        Ver tarea
      </Button>
    </Html>
  )
}
```

```typescript
// Enviar email
import { Resend } from 'resend'
import TaskReminder from '@/emails/task-reminder'

const resend = new Resend(process.env.RESEND_API_KEY)

await resend.emails.send({
  from: 'Flime <noreply@flime.ai>',
  to: user.email,
  subject: 'Recordatorio: Tu tarea vence pronto',
  react: TaskReminder({ task }),
})
```

---

## 🚀 Deployment & Infrastructure

### Vercel
**Por qué Vercel:**
- ✅ Creadores de Next.js (integración perfecta)
- ✅ Deploy en segundos
- ✅ Preview deployments por PR
- ✅ Edge Network global
- ✅ Free tier generoso

**Features usadas:**
- Automatic deploys (push to main)
- Preview URLs (cada PR)
- Environment variables
- Analytics
- Web Vitals monitoring

**Plan:** Hobby (Free) → Pro ($20/mes cuando escale)

---

### Supabase Cloud
**Por qué:**
- ✅ Managed PostgreSQL
- ✅ Backups automáticos
- ✅ 500MB storage free

**Plan:** Free tier → Pro ($25/mes cuando exceda límites)

---

### Domain & DNS
**Registrar:** Namecheap o Cloudflare
**DNS:** Cloudflare (free, rápido, proxy)

**Setup:**
```
flime.ai → Vercel (A record)
www.flime.ai → flime.ai (CNAME)
```

---

## 📊 Monitoring & Analytics

### Error Tracking: Sentry
**Free tier:** 5,000 errors/mes

```typescript
// sentry.client.config.ts
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 0.1,
  environment: process.env.NODE_ENV,
})
```

---

### Product Analytics: PostHog
**Por qué PostHog:**
- ✅ Open source
- ✅ Free tier: 1M events/mes
- ✅ Privacy-friendly (GDPR)
- ✅ Funnels, cohorts, feature flags

**Eventos a trackear:**
- User signup
- Task created/completed
- Upgrade to Premium
- Churn events

```typescript
import posthog from 'posthog-js'

posthog.init(process.env.NEXT_PUBLIC_POSTHOG_KEY!, {
  api_host: 'https://app.posthog.com',
})

// Track event
posthog.capture('task_created', {
  board_id: boardId,
  priority: priority,
})
```

---

### Uptime Monitoring: UptimeRobot
**Free tier:** 50 monitors

Monitor:
- https://flime.ai (HTTP)
- https://api.flime.ai/health (API health)

Alertas via email si downtime.

---

## 🧪 Testing Stack

### Unit & Integration: Vitest
**Por qué Vitest > Jest:**
- ✅ Más rápido
- ✅ ESM nativo
- ✅ Vite powered
- ✅ Compatible con Jest API

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

---

### E2E: Playwright (Fase 2)
**Por qué Playwright:**
- ✅ Multi-browser
- ✅ Auto-wait
- ✅ Screenshots/videos

```typescript
test('user can create task', async ({ page }) => {
  await page.goto('https://flime.ai')
  await page.click('text=Login')
  await page.fill('[name=email]', 'test@example.com')
  await page.fill('[name=password]', 'password')
  await page.click('button[type=submit]')

  await page.click('text=New Task')
  await page.fill('[name=title]', 'My first task')
  await page.click('text=Save')

  await expect(page.locator('text=My first task')).toBeVisible()
})
```

---

## 🗂️ Project Structure

```
flime/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (auth)/            # Auth routes (signup, login)
│   │   ├── (dashboard)/       # Dashboard routes
│   │   ├── api/               # API routes
│   │   ├── layout.tsx         # Root layout
│   │   └── page.tsx           # Landing page
│   ├── components/            # React components
│   │   ├── ui/               # shadcn/ui components
│   │   ├── boards/           # Board components
│   │   ├── tasks/            # Task components
│   │   └── layout/           # Layout components
│   ├── lib/                   # Utilities
│   │   ├── supabase.ts       # Supabase client
│   │   ├── mercadopago.ts    # Mercado Pago client
│   │   └── utils.ts          # Helper functions
│   ├── db/                    # Drizzle ORM
│   │   ├── schema.ts         # Database schema
│   │   └── index.ts          # DB client
│   ├── emails/                # Email templates (React)
│   └── types/                 # TypeScript types
├── public/                    # Static assets
├── docs/                      # Documentation
├── planning/                  # Sprints, roadmap
├── design/                    # Wireframes, mockups
└── tests/                     # Tests
```

---

## 🔧 Development Tools

### VSCode Extensions
- ESLint
- Prettier
- Tailwind CSS IntelliSense
- PostCSS Language Support
- Prisma (para syntax highlighting de Drizzle)

### Scripts npm
```json
{
  "scripts": {
    "dev": "next dev --turbo",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit",
    "db:generate": "drizzle-kit generate:pg",
    "db:push": "drizzle-kit push:pg",
    "test": "vitest",
    "test:ui": "vitest --ui"
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
| Sentry | Free | $0 |
| PostHog | Free | $0 |
| UptimeRobot | Free | $0 |
| **TOTAL MVP** | | **~$2 USD/mes** |

**Cuando escale:**
- Vercel Pro: $20/mes
- Supabase Pro: $25/mes
- Resend Pro: $20/mes
- **Total escalado:** ~$65-80 USD/mes

---

## 🚦 Performance Targets

### Core Web Vitals
- **LCP (Largest Contentful Paint):** < 2.5s
- **FID (First Input Delay):** < 100ms
- **CLS (Cumulative Layout Shift):** < 0.1

### Lighthouse Scores
- **Performance:** > 90
- **Accessibility:** > 95
- **Best Practices:** > 95
- **SEO:** > 95

### API Response Times
- GET requests: < 200ms (p95)
- POST requests: < 500ms (p95)
- Database queries: < 100ms (p95)

---

## 🔐 Security Checklist

- [x] HTTPS only (enforced)
- [x] CORS configurado correctamente
- [x] Rate limiting en API routes
- [x] SQL injection protection (Drizzle ORM)
- [x] XSS protection (React auto-escaping)
- [x] CSRF tokens (Next.js built-in)
- [x] Secure headers (next.config.ts)
- [x] Environment variables seguras (Vercel)
- [x] Row Level Security (Supabase RLS)
- [x] Password hashing (Supabase Auth)
- [x] Input validation (Zod schemas)

---

## 📝 Environment Variables

```bash
# .env.local (development)
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=xxx
SUPABASE_SERVICE_ROLE_KEY=xxx (server-only)

MERCADOPAGO_ACCESS_TOKEN=xxx
MERCADOPAGO_PUBLIC_KEY=xxx

RESEND_API_KEY=xxx

NEXT_PUBLIC_POSTHOG_KEY=xxx
NEXT_PUBLIC_SENTRY_DSN=xxx

DATABASE_URL=postgresql://xxx (Drizzle)
```

---

## 🎓 Learning Resources

**Next.js:**
- Official Docs: https://nextjs.org/docs
- Next.js 15 Guide: https://nextjs.org/blog/next-15

**Supabase:**
- Docs: https://supabase.com/docs
- Auth Guide: https://supabase.com/docs/guides/auth

**Drizzle ORM:**
- Docs: https://orm.drizzle.team/docs/overview
- Supabase Guide: https://orm.drizzle.team/docs/quick-start/supabase

**Tailwind CSS v4:**
- Docs: https://tailwindcss.com/docs
- v4 Changelog: https://tailwindcss.com/blog/tailwindcss-v4

---

**Última actualización:** Diciembre 30, 2024
**Próxima revisión:** Post Sprint 2 (ajustar si necesario)
