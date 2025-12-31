# Flime.ai - Database Schema Design

**Versión:** 2.0 (Actual Implementation)
**Fecha:** Diciembre 31, 2024
**Database:** PostgreSQL 15 (Supabase)
**ORM:** Drizzle v0.45.1
**Status:** ✅ Implementado en Sprint 1

---

## 🎯 Schema Principles

### Design Goals
1. **Simplicidad** - Solo tablas necesarias para MVP
2. **ADHD-Friendly** - Campos específicos para usuarios con ADHD/ADD
3. **Escalabilidad** - Preparado para features futuras
4. **Performance** - Índices estratégicos
5. **Seguridad** - Row Level Security (RLS)
6. **Integridad** - Foreign keys, constraints

### Naming Conventions
- Tablas: `snake_case` plural (ej: `users`, `tasks`)
- Columnas: `snake_case` (ej: `created_at`, `full_name`)
- IDs: UUID v4 (no auto-increment)
- Timestamps: `timestamptz` (timezone aware)

---

## 📊 Entity Relationship Diagram (ERD)

```
┌─────────────────┐
│     users       │
│─────────────────│
│ id (PK)         │
│ email           │
│ full_name       │
│ avatar_url      │
│ plan            │
│ timezone        │
│ language        │
│ preferences...  │
│ created_at      │
│ updated_at      │
└────────┬────────┘
         │
         │ 1:N
         │
    ┌────┴────────────────────────────┐
    │                                 │
┌───▼───────────┐          ┌──────────▼──────┐
│     tasks     │          │  subscriptions  │
│───────────────│          │─────────────────│
│ id (PK)       │          │ id (PK)         │
│ user_id (FK)  │◄─────┐   │ user_id (FK)    │
│ title         │      │   │ status          │
│ description   │      │   │ plan_type       │
│ status        │      │   │ amount          │
│ priority      │      │   │ currency        │
│ due_date      │      │   │ interval        │
│ completed_at  │      │   │ current_period  │
│ estimated_dur │      │   │ cancel_at       │
│ actual_dur    │      │   │ mp_subscription │
│ is_recurring  │      │   │ created_at      │
│ recurrence_...│      │   │ updated_at      │
│ parent_task_id│      │   └─────────────────┘
│ order         │      │
│ created_at    │      │   ┌─────────────────┐
│ updated_at    │      │   │    payments     │
└───────┬───────┘      │   │─────────────────│
        │              │   │ id (PK)         │
        │ 1:N          │   │ user_id (FK)    │
        │              │   │ subscription_id │
    ┌───▼───────┐      │   │ amount          │
    │ reminders │      │   │ currency        │
    │───────────│      │   │ status          │
    │ id (PK)   │      │   │ mp_payment_id   │
    │ task_id   │◄─────┘   │ mp_preference_id│
    │ user_id   │          │ payment_method  │
    │ send_at   │          │ paid_at         │
    │ sent      │          │ created_at      │
    │ channel   │          └─────────────────┘
    │ created_at│
    └───────────┘          ┌─────────────────┐
                           │      tags       │
        ┌──────────────────┤─────────────────│
        │                  │ id (PK)         │
        │                  │ user_id (FK)    │
        │                  │ name            │
        │                  │ color           │
        │                  │ created_at      │
        │                  │ updated_at      │
        │                  └────────┬────────┘
        │                           │
        │                           │ N:M
        │                           │
        │                  ┌────────▼────────┐
        │                  │   task_tags     │
        └──────────────────┤─────────────────│
                           │ task_id (FK)    │
                           │ tag_id (FK)     │
                           │ created_at      │
                           └─────────────────┘
```

---

## 📋 Table Definitions

### 1. users

Tabla principal de usuarios (synced con Supabase Auth).

**Drizzle Schema:**
```typescript
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

  // Notification Preferences
  emailNotifications: boolean('email_notifications').default(true).notNull(),
  webPushNotifications: boolean('web_push_notifications')
    .default(true)
    .notNull(),

  // Daily Summary Preferences
  dailySummary: boolean('daily_summary').default(false).notNull(),
  dailySummaryTime: time('daily_summary_time').default('08:00').notNull(),

  // Calendar Preferences
  weekStartsOn: integer('week_starts_on').default(1).notNull(), // 0=Sunday, 1=Monday

  // Metadata
  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**RLS Policies:**
```sql
-- Users can view own data
CREATE POLICY "Users can view own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- Users can update own data
CREATE POLICY "Users can update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

**Indexes:**
- `idx_users_email` on `email`
- `idx_users_plan` on `plan`

---

### 2. tasks

Tareas del usuario con campos ADHD-friendly.

**Drizzle Schema:**
```typescript
export const tasks = pgTable('tasks', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),

  title: text('title').notNull(),
  description: text('description'),

  // Status & Priority
  status: text('status', {
    enum: ['pending', 'in_progress', 'completed', 'archived'],
  })
    .default('pending')
    .notNull(),
  priority: text('priority', { enum: ['low', 'medium', 'high', 'urgent'] })
    .default('medium')
    .notNull(),

  // Dates
  dueDate: timestamp('due_date', { withTimezone: true }),
  completedAt: timestamp('completed_at', { withTimezone: true }),

  // ADHD-Friendly Fields
  estimatedDuration: integer('estimated_duration'), // minutos
  actualDuration: integer('actual_duration'), // minutos

  // Recurrence (Premium)
  isRecurring: boolean('is_recurring').default(false).notNull(),
  recurrencePattern: text('recurrence_pattern'), // RRULE format

  // Subtasks Support
  parentTaskId: uuid('parent_task_id'), // Self-reference for subtasks
  order: integer('order').default(0).notNull(),

  // Metadata
  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**Key Features:**
- **estimatedDuration / actualDuration**: Para usuarios con ADHD que luchan con time blindness
- **parentTaskId**: Permite crear subtareas (self-reference)
- **isRecurring / recurrencePattern**: Soporte para tareas recurrentes (Premium)
- **status**: 4 estados (pending, in_progress, completed, archived)
- **priority**: 4 niveles (low, medium, high, urgent)

**RLS Policies:**
```sql
-- Users can only see their own tasks
CREATE POLICY "Users can view own tasks"
  ON tasks FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create own tasks"
  ON tasks FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own tasks"
  ON tasks FOR UPDATE
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own tasks"
  ON tasks FOR DELETE
  USING (auth.uid() = user_id);
```

**Indexes:**
- `idx_tasks_user_id` on `user_id`
- `idx_tasks_status` on `status`
- `idx_tasks_due_date` on `due_date` (WHERE status != 'completed')
- `idx_tasks_priority` on `priority`

---

### 3. reminders

Recordatorios para tareas.

**Drizzle Schema:**
```typescript
export const reminders = pgTable('reminders', {
  id: uuid('id').primaryKey().defaultRandom(),
  taskId: uuid('task_id')
    .notNull()
    .references(() => tasks.id, { onDelete: 'cascade' }),
  userId: uuid('user_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),

  sendAt: timestamp('send_at', { withTimezone: true }).notNull(),
  sent: boolean('sent').default(false).notNull(),
  channel: text('channel', { enum: ['email', 'web_push', 'whatsapp', 'sms'] })
    .default('email')
    .notNull(),

  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**RLS Policies:**
```sql
CREATE POLICY "Users can view own reminders"
  ON reminders FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create own reminders"
  ON reminders FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own reminders"
  ON reminders FOR UPDATE
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own reminders"
  ON reminders FOR DELETE
  USING (auth.uid() = user_id);
```

**Indexes:**
- `idx_reminders_task_id` on `task_id`
- `idx_reminders_user_id` on `user_id`
- `idx_reminders_send_at` on `send_at` (WHERE sent = false)

---

### 4. tags

Etiquetas para organizar tareas.

**Drizzle Schema:**
```typescript
export const tags = pgTable('tags', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),

  name: text('name').notNull(),
  color: text('color').default('#3b82f6').notNull(),

  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**RLS Policies:**
```sql
CREATE POLICY "Users can view own tags"
  ON tags FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create own tags"
  ON tags FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own tags"
  ON tags FOR UPDATE
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own tags"
  ON tags FOR DELETE
  USING (auth.uid() = user_id);
```

**Indexes:**
- `idx_tags_user_id` on `user_id`
- `idx_tags_name` on `name`

---

### 5. task_tags

Relación many-to-many entre tasks y tags.

**Drizzle Schema:**
```typescript
export const taskTags = pgTable(
  'task_tags',
  {
    taskId: uuid('task_id')
      .notNull()
      .references(() => tasks.id, { onDelete: 'cascade' }),
    tagId: uuid('tag_id')
      .notNull()
      .references(() => tags.id, { onDelete: 'cascade' }),

    createdAt: timestamp('created_at', { withTimezone: true })
      .defaultNow()
      .notNull(),
  },
  (table) => ({
    pk: primaryKey({ columns: [table.taskId, table.tagId] }),
  })
)
```

**RLS Policies:**
```sql
CREATE POLICY "Users can view own task_tags"
  ON task_tags FOR SELECT
  USING (EXISTS (
    SELECT 1 FROM tasks WHERE tasks.id = task_tags.task_id AND tasks.user_id = auth.uid()
  ));

CREATE POLICY "Users can create own task_tags"
  ON task_tags FOR INSERT
  WITH CHECK (EXISTS (
    SELECT 1 FROM tasks WHERE tasks.id = task_tags.task_id AND tasks.user_id = auth.uid()
  ));

CREATE POLICY "Users can delete own task_tags"
  ON task_tags FOR DELETE
  USING (EXISTS (
    SELECT 1 FROM tasks WHERE tasks.id = task_tags.task_id AND tasks.user_id = auth.uid()
  ));
```

**Indexes:**
- `idx_task_tags_task_id` on `task_id`
- `idx_task_tags_tag_id` on `tag_id`

---

### 6. subscriptions

Gestión de suscripciones Premium (Mercado Pago).

**Drizzle Schema:**
```typescript
export const subscriptions = pgTable('subscriptions', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id')
    .unique()
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),

  status: text('status', {
    enum: ['active', 'canceled', 'past_due', 'trial'],
  }).notNull(),
  planType: text('plan_type', {
    enum: ['premium_monthly', 'premium_yearly', 'team_monthly'],
  }).notNull(),
  amount: decimal('amount', { precision: 10, scale: 2 }).notNull(),
  currency: text('currency').default('USD').notNull(),
  interval: text('interval', { enum: ['month', 'year'] }).notNull(),

  currentPeriodStart: timestamp('current_period_start', {
    withTimezone: true,
  }).notNull(),
  currentPeriodEnd: timestamp('current_period_end', {
    withTimezone: true,
  }).notNull(),
  cancelAtPeriodEnd: boolean('cancel_at_period_end').default(false).notNull(),
  canceledAt: timestamp('canceled_at', { withTimezone: true }),

  // Mercado Pago Integration
  mpSubscriptionId: text('mp_subscription_id'),
  mpPayerId: text('mp_payer_id'),

  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**RLS Policies:**
```sql
CREATE POLICY "Users can view own subscription"
  ON subscriptions FOR SELECT
  USING (auth.uid() = user_id);
```

**Indexes:**
- `idx_subscriptions_user_id` on `user_id`
- `idx_subscriptions_status` on `status`
- `idx_subscriptions_mp_id` on `mp_subscription_id`

---

### 7. payments

Registro de pagos (compliance y contabilidad).

**Drizzle Schema:**
```typescript
export const payments = pgTable('payments', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id')
    .notNull()
    .references(() => users.id, { onDelete: 'cascade' }),
  subscriptionId: uuid('subscription_id').references(() => subscriptions.id, {
    onDelete: 'set null',
  }),

  amount: decimal('amount', { precision: 10, scale: 2 }).notNull(),
  currency: text('currency').default('USD').notNull(),
  status: text('status', { enum: ['pending', 'approved', 'failed', 'refunded'] })
    .notNull(),

  // Mercado Pago
  mpPaymentId: text('mp_payment_id'),
  mpPreferenceId: text('mp_preference_id'),
  paymentMethod: text('payment_method'), // 'credit_card', 'debit_card', 'pse'

  paidAt: timestamp('paid_at', { withTimezone: true }),
  createdAt: timestamp('created_at', { withTimezone: true })
    .defaultNow()
    .notNull(),
})
```

**RLS Policies:**
```sql
CREATE POLICY "Users can view own payments"
  ON payments FOR SELECT
  USING (auth.uid() = user_id);
```

**Indexes:**
- `idx_payments_user_id` on `user_id`
- `idx_payments_subscription_id` on `subscription_id`
- `idx_payments_status` on `status`
- `idx_payments_mp_payment_id` on `mp_payment_id`
- `idx_payments_created_at` on `created_at DESC`

---

## 🔧 Database Functions & Triggers

### 1. Auto-update `updated_at`

Trigger para actualizar automáticamente `updated_at` en cada UPDATE.

**Implementado en:** `src/db/migrations/0002_triggers.sql`

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Aplicado a:
CREATE TRIGGER update_users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_tasks_updated_at
  BEFORE UPDATE ON tasks
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_tags_updated_at
  BEFORE UPDATE ON tags
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_subscriptions_updated_at
  BEFORE UPDATE ON subscriptions
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

---

### 2. Sync Supabase Auth with public.users

Trigger para crear user en public.users cuando se registra en auth.users.

**Implementado en:** `src/db/migrations/0002_triggers.sql`

```sql
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO public.users (id, email, full_name, avatar_url)
  VALUES (
    NEW.id,
    NEW.email,
    COALESCE(NEW.raw_user_meta_data->>'full_name', 'Usuario'),
    NEW.raw_user_meta_data->>'avatar_url'
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE FUNCTION handle_new_user();
```

---

## 🔒 Row Level Security (RLS) Summary

**Política general:**
- Usuarios solo pueden ver/editar/eliminar **sus propios datos**
- Validación a través de `auth.uid() = user_id`

**Implementado en:**
- ✅ users
- ✅ tasks
- ✅ reminders
- ✅ tags
- ✅ task_tags (valida a través de tasks.user_id)
- ✅ subscriptions
- ✅ payments

**Beneficio:**
Seguridad a nivel de base de datos, incluso si hay bug en aplicación.

**Implementado en:** `src/db/migrations/0001_rls_policies.sql`

---

## 📊 Indexes Summary

### Critical Indexes (Performance)

| Tabla | Index | Propósito |
|-------|-------|-----------|
| users | email | Login rápido |
| tasks | user_id, status, due_date | Dashboard queries |
| tasks | priority | Filtrar por prioridad |
| reminders | send_at, sent | Cron job (enviar pendientes) |
| tags | user_id | Listar tags de usuario |
| task_tags | task_id, tag_id | Relación many-to-many |
| subscriptions | user_id | Buscar suscripción activa |
| payments | user_id, created_at DESC | Historial de pagos ordenado |
| payments | mp_payment_id | Webhook lookup (validar pago) |

---

## 🚀 Migrations Strategy

### Drizzle Kit Workflow

**Script usado en Sprint 1:**
```bash
pnpm db:push  # Push schema directly to Supabase (no migration files)
```

**Configuración:** `drizzle.config.ts`
```typescript
import { defineConfig } from 'drizzle-kit'

export default defineConfig({
  schema: './src/db/schema/index.ts',
  out: './src/db/migrations',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
})
```

**Scripts en package.json:**
```json
{
  "scripts": {
    "db:push": "drizzle-kit push",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:studio": "drizzle-kit studio"
  }
}
```

### Manual SQL Migrations

Para triggers y RLS policies, ejecutar manualmente en Supabase SQL Editor:
1. `src/db/migrations/0001_rls_policies.sql`
2. `src/db/migrations/0002_triggers.sql`

---

## 📝 Design Decisions

### Why NO boards table?

**Decision:** Tasks NO están agrupadas en "boards" en MVP

**Rationale:**
- Simplicidad: Usuarios con ADHD se benefician de menos niveles de organización
- Tags son suficientes para categorizar (ej: #trabajo, #personal)
- Boards pueden agregarse en Fase 2 si usuarios los piden
- Evita over-engineering en MVP

### Why NO invoices table?

**Decision:** Tabla `payments` almacena pagos, pero NO generamos facturas automáticas en MVP

**Rationale:**
- Compliance: Facturas formales se generarán post-MVP cuando tengamos facturación electrónica configurada
- Payments table es suficiente para historial de pagos del usuario
- Mercado Pago provee recibos a los usuarios

**Futuro (Fase 2):**
Cuando configuremos facturación electrónica (DIAN Colombia), crear tabla `invoices` con invoice_number, PDF URL, etc.

### Why NO notifications table?

**Decision:** Tabla `reminders` almacena recordatorios, pero NO hay tabla de "notifications log"

**Rationale:**
- MVP: Solo necesitamos saber qué recordatorios enviar
- Log de notificaciones (sent/failed) puede agregarse cuando tengamos analytics
- Reminders.sent = true es suficiente para tracking básico

**Futuro (Fase 2):**
Crear tabla `notifications_log` para analytics, debugging, y tracking de delivery.

### Tables Count Summary

**MVP Sprint 1 (7 tablas implementadas):**
1. users
2. tasks (con campos ADHD-friendly)
3. reminders
4. tags
5. task_tags (relación many-to-many)
6. subscriptions
7. payments

**Post-MVP (agregar cuando necesario):**
- boards (si usuarios lo piden)
- invoices (facturación electrónica DIAN)
- notifications_log (analytics y debugging)
- workspaces (Team plan)
- workspace_roles (Team permissions)
- integrations (Google/MS Calendar)

---

## 🧪 Database Setup (Sprint 1)

### Comandos ejecutados:

```bash
# 1. Push schema to Supabase
pnpm db:push

# 2. Apply RLS policies (manual en Supabase SQL Editor)
# Ejecutar: src/db/migrations/0001_rls_policies.sql

# 3. Apply triggers (manual en Supabase SQL Editor)
# Ejecutar: src/db/migrations/0002_triggers.sql
```

### Validación:

- ✅ 7 tablas creadas en Supabase
- ✅ RLS habilitado en todas las tablas
- ✅ Triggers funcionando (updated_at, auth sync)
- ✅ Foreign keys configurados
- ✅ Indexes creados

---

## 🎓 Database Best Practices

1. **Siempre usar transactions** para operaciones multi-tabla
2. **Index estratégico** - Solo queries frecuentes
3. **Evitar N+1 queries** - Usar JOINs o batch queries
4. **Validar en DB** - Constraints + triggers (no solo en app)
5. **RLS siempre activo** - Seguridad en capas
6. **Monitor slow queries** - Supabase dashboard
7. **Migrations versionadas** - Nunca editar migrations aplicadas

---

## 📝 Monitoring & Observability (Postponed)

**PostHog:** Pospuesto para después de Sprint 1
- Event tracking
- User analytics
- Feature flags

**Current Setup (Sprint 1):**
- ✅ Sentry (error tracking)
- ❌ PostHog (analytics) - postponed

---

**Última actualización:** Diciembre 31, 2024 (v2.0 - Actual Implementation Sprint 1)
**Próxima revisión:** Post Sprint 2 (Authentication & User Management)
**Deploy:** https://flime-ai.vercel.app
