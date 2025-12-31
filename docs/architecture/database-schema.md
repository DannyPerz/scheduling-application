# Flime.ai - Database Schema Design

**Versión:** 1.1
**Fecha:** Diciembre 30, 2024 (Actualizado con tabla invoices)
**Database:** PostgreSQL 15 (Supabase)
**ORM:** Drizzle

---

## 🎯 Schema Principles

### Design Goals
1. **Simplicidad** - Solo tablas necesarias para MVP
2. **Escalabilidad** - Preparado para features futuras
3. **Performance** - Índices estratégicos
4. **Seguridad** - Row Level Security (RLS)
5. **Integridad** - Foreign keys, constraints

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
│ created_at      │
│ updated_at      │
└────────┬────────┘
         │
         │ 1:N
         │
┌────────▼────────┐          ┌─────────────────┐
│     boards      │          │  subscriptions  │
│─────────────────│          │─────────────────│
│ id (PK)         │          │ id (PK)         │
│ user_id (FK)    │◄─────────│ user_id (FK)    │
│ name            │          │ status          │
│ color           │          │ plan_type       │
│ icon            │          │ amount          │
│ description     │          │ currency        │
│ order_index     │          │ interval        │
│ created_at      │          │ current_period  │
│ updated_at      │          │ cancel_at       │
└────────┬────────┘          │ mp_subscription │
         │                   │ created_at      │
         │ 1:N               │ updated_at      │
         │                   └────────┬────────┘
┌────────▼────────┐                   │ 1:N
│     tasks       │                   │
│─────────────────│          ┌────────▼────────┐
│ id (PK)         │          │    invoices     │
│ board_id (FK)   │          │─────────────────│
│ user_id (FK)    │◄─────────│ id (PK)         │
│ title           │          │ user_id (FK)    │
│ description     │          │ subscription_id │
│ due_date        │          │ invoice_number  │
│ due_time        │          │ status          │
│ priority        │          │ amount          │
│ status          │          │ currency        │
│ recurrence      │          │ mp_payment_id   │
│ recurrence_rule │          │ paid_at         │
│ reminder_offset │          │ created_at      │
│ completed_at    │          └─────────────────┘
│ created_at      │
│ updated_at      │          ┌─────────────────┐
└─────────────────┘          │  notifications  │
                             │─────────────────│
                             │ id (PK)         │
                             │ user_id (FK)    │
                             │ task_id (FK)    │
                             │ type            │
                             │ channel         │
                             │ sent_at         │
                             │ status          │
                             │ scheduled_for   │
                             │ created_at      │
                             └─────────────────┘
```

---

## 📋 Table Definitions

### 1. users

Tabla principal de usuarios (manejada parcialmente por Supabase Auth).

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE NOT NULL,
  full_name TEXT NOT NULL,
  avatar_url TEXT,
  plan TEXT NOT NULL DEFAULT 'free' CHECK (plan IN ('free', 'premium', 'team')),
  timezone TEXT NOT NULL DEFAULT 'America/Bogota',
  language TEXT NOT NULL DEFAULT 'es' CHECK (language IN ('es', 'en')),

  -- Preferences
  email_notifications BOOLEAN NOT NULL DEFAULT true,
  web_push_notifications BOOLEAN NOT NULL DEFAULT true,
  daily_summary BOOLEAN NOT NULL DEFAULT false,
  daily_summary_time TIME NOT NULL DEFAULT '08:00',
  week_starts_on INTEGER NOT NULL DEFAULT 1 CHECK (week_starts_on IN (0, 1)), -- 0=Sunday, 1=Monday

  -- Metadata
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_plan ON users(plan);

-- RLS (Row Level Security)
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

CREATE POLICY "Users can update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

**Drizzle Schema:**
```typescript
export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').unique().notNull(),
  fullName: text('full_name').notNull(),
  avatarUrl: text('avatar_url'),
  plan: text('plan', { enum: ['free', 'premium', 'team'] }).default('free').notNull(),
  timezone: text('timezone').default('America/Bogota').notNull(),
  language: text('language', { enum: ['es', 'en'] }).default('es').notNull(),

  emailNotifications: boolean('email_notifications').default(true).notNull(),
  webPushNotifications: boolean('web_push_notifications').default(true).notNull(),
  dailySummary: boolean('daily_summary').default(false).notNull(),
  dailySummaryTime: time('daily_summary_time').default('08:00').notNull(),
  weekStartsOn: integer('week_starts_on').default(1).notNull(),

  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).defaultNow().notNull(),
})
```

---

### 2. boards

Tableros de organización (Trabajo, Personal, etc.).

```sql
CREATE TABLE boards (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  name TEXT NOT NULL CHECK (LENGTH(name) <= 50),
  color TEXT NOT NULL DEFAULT '#3b82f6',
  icon TEXT NOT NULL DEFAULT 'folder',
  description TEXT CHECK (LENGTH(description) <= 200),
  order_index INTEGER NOT NULL DEFAULT 0,

  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_boards_user_id ON boards(user_id);
CREATE INDEX idx_boards_order ON boards(user_id, order_index);

-- RLS
ALTER TABLE boards ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own boards"
  ON boards FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create own boards"
  ON boards FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own boards"
  ON boards FOR UPDATE
  USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own boards"
  ON boards FOR DELETE
  USING (auth.uid() = user_id);
```

**Drizzle Schema:**
```typescript
export const boards = pgTable('boards', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id, { onDelete: 'cascade' }).notNull(),
  name: text('name').notNull(),
  color: text('color').default('#3b82f6').notNull(),
  icon: text('icon').default('folder').notNull(),
  description: text('description'),
  orderIndex: integer('order_index').default(0).notNull(),

  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).defaultNow().notNull(),
})
```

---

### 3. tasks

Tareas del usuario.

```sql
CREATE TABLE tasks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  board_id UUID NOT NULL REFERENCES boards(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  title TEXT NOT NULL CHECK (LENGTH(title) <= 200),
  description TEXT CHECK (LENGTH(description) <= 1000),

  due_date DATE,
  due_time TIME,
  priority TEXT NOT NULL DEFAULT 'medium' CHECK (priority IN ('high', 'medium', 'low')),
  status TEXT NOT NULL DEFAULT 'todo' CHECK (status IN ('todo', 'done')),

  -- Recurrence (Premium only)
  recurrence TEXT CHECK (recurrence IN ('daily', 'weekly', 'monthly')),
  recurrence_rule JSONB, -- Para reglas complejas (ej: cada lunes y miércoles)

  -- Reminder
  reminder_offset INTEGER, -- Minutos antes (ej: 15, 30, 60, 1440 para 1 día)

  completed_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_tasks_user_id ON tasks(user_id);
CREATE INDEX idx_tasks_board_id ON tasks(board_id);
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_due_date ON tasks(due_date) WHERE status = 'todo';
CREATE INDEX idx_tasks_priority ON tasks(priority);

-- Composite index para queries comunes
CREATE INDEX idx_tasks_user_status_due ON tasks(user_id, status, due_date);

-- RLS
ALTER TABLE tasks ENABLE ROW LEVEL SECURITY;

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

**Drizzle Schema:**
```typescript
export const tasks = pgTable('tasks', {
  id: uuid('id').primaryKey().defaultRandom(),
  boardId: uuid('board_id').references(() => boards.id, { onDelete: 'cascade' }).notNull(),
  userId: uuid('user_id').references(() => users.id, { onDelete: 'cascade' }).notNull(),

  title: text('title').notNull(),
  description: text('description'),

  dueDate: date('due_date'),
  dueTime: time('due_time'),
  priority: text('priority', { enum: ['high', 'medium', 'low'] }).default('medium').notNull(),
  status: text('status', { enum: ['todo', 'done'] }).default('todo').notNull(),

  recurrence: text('recurrence', { enum: ['daily', 'weekly', 'monthly'] }),
  recurrenceRule: jsonb('recurrence_rule'),

  reminderOffset: integer('reminder_offset'),

  completedAt: timestamp('completed_at', { withTimezone: true }),
  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).defaultNow().notNull(),
})
```

---

### 4. subscriptions

Gestión de suscripciones Premium.

```sql
CREATE TABLE subscriptions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  status TEXT NOT NULL CHECK (status IN ('active', 'canceled', 'past_due', 'trial')),
  plan_type TEXT NOT NULL CHECK (plan_type IN ('premium_monthly', 'premium_yearly', 'team_monthly')),
  amount DECIMAL(10, 2) NOT NULL,
  currency TEXT NOT NULL DEFAULT 'USD',
  interval TEXT NOT NULL CHECK (interval IN ('month', 'year')),

  current_period_start TIMESTAMPTZ NOT NULL,
  current_period_end TIMESTAMPTZ NOT NULL,
  cancel_at_period_end BOOLEAN NOT NULL DEFAULT false,
  canceled_at TIMESTAMPTZ,

  -- Mercado Pago
  mp_subscription_id TEXT,
  mp_payer_id TEXT,

  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_subscriptions_user_id ON subscriptions(user_id);
CREATE INDEX idx_subscriptions_status ON subscriptions(status);
CREATE INDEX idx_subscriptions_mp_id ON subscriptions(mp_subscription_id);

-- RLS
ALTER TABLE subscriptions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own subscription"
  ON subscriptions FOR SELECT
  USING (auth.uid() = user_id);
```

**Drizzle Schema:**
```typescript
export const subscriptions = pgTable('subscriptions', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').unique().references(() => users.id, { onDelete: 'cascade' }).notNull(),

  status: text('status', { enum: ['active', 'canceled', 'past_due', 'trial'] }).notNull(),
  planType: text('plan_type', { enum: ['premium_monthly', 'premium_yearly', 'team_monthly'] }).notNull(),
  amount: decimal('amount', { precision: 10, scale: 2 }).notNull(),
  currency: text('currency').default('USD').notNull(),
  interval: text('interval', { enum: ['month', 'year'] }).notNull(),

  currentPeriodStart: timestamp('current_period_start', { withTimezone: true }).notNull(),
  currentPeriodEnd: timestamp('current_period_end', { withTimezone: true }).notNull(),
  cancelAtPeriodEnd: boolean('cancel_at_period_end').default(false).notNull(),
  canceledAt: timestamp('canceled_at', { withTimezone: true }),

  mpSubscriptionId: text('mp_subscription_id'),
  mpPayerId: text('mp_payer_id'),

  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
  updatedAt: timestamp('updated_at', { withTimezone: true }).defaultNow().notNull(),
})
```

---

### 5. notifications

Log de notificaciones enviadas (para analytics y debugging).

```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  task_id UUID REFERENCES tasks(id) ON DELETE SET NULL,

  type TEXT NOT NULL CHECK (type IN ('task_reminder', 'daily_summary')),
  channel TEXT NOT NULL CHECK (channel IN ('email', 'web_push', 'whatsapp', 'sms')),
  status TEXT NOT NULL CHECK (status IN ('pending', 'sent', 'failed')),

  scheduled_for TIMESTAMPTZ NOT NULL,
  sent_at TIMESTAMPTZ,
  error_message TEXT,

  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_task_id ON notifications(task_id);
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_scheduled ON notifications(scheduled_for) WHERE status = 'pending';

-- RLS
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own notifications"
  ON notifications FOR SELECT
  USING (auth.uid() = user_id);
```

**Drizzle Schema:**
```typescript
export const notifications = pgTable('notifications', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id, { onDelete: 'cascade' }).notNull(),
  taskId: uuid('task_id').references(() => tasks.id, { onDelete: 'set null' }),

  type: text('type', { enum: ['task_reminder', 'daily_summary'] }).notNull(),
  channel: text('channel', { enum: ['email', 'web_push', 'whatsapp', 'sms'] }).notNull(),
  status: text('status', { enum: ['pending', 'sent', 'failed'] }).notNull(),

  scheduledFor: timestamp('scheduled_for', { withTimezone: true }).notNull(),
  sentAt: timestamp('sent_at', { withTimezone: true }),
  errorMessage: text('error_message'),

  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
})
```

---

### 6. invoices

Registro de facturas/recibos de pago (compliance y contabilidad).

```sql
CREATE TABLE invoices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  subscription_id UUID REFERENCES subscriptions(id) ON DELETE SET NULL,

  -- Invoice identification
  invoice_number TEXT UNIQUE NOT NULL, -- ej: "INV-2026-00001"
  status TEXT NOT NULL CHECK (status IN ('pending', 'paid', 'failed', 'refunded')),

  -- Amount
  amount DECIMAL(10, 2) NOT NULL,
  currency TEXT NOT NULL DEFAULT 'USD',

  -- Payment details
  payment_method TEXT, -- 'credit_card', 'debit_card', 'pse', 'cash'
  payment_provider TEXT DEFAULT 'mercadopago',
  mp_payment_id TEXT, -- Mercado Pago payment ID
  mp_preference_id TEXT, -- Mercado Pago preference ID

  -- Billing info (snapshot at time of purchase)
  billing_email TEXT,
  billing_name TEXT,

  -- Dates
  paid_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_invoices_user_id ON invoices(user_id);
CREATE INDEX idx_invoices_subscription_id ON invoices(subscription_id);
CREATE INDEX idx_invoices_status ON invoices(status);
CREATE INDEX idx_invoices_mp_payment_id ON invoices(mp_payment_id);
CREATE INDEX idx_invoices_created_at ON invoices(created_at DESC);

-- RLS
ALTER TABLE invoices ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own invoices"
  ON invoices FOR SELECT
  USING (auth.uid() = user_id);

-- Function to generate invoice number
CREATE OR REPLACE FUNCTION generate_invoice_number()
RETURNS TEXT AS $$
DECLARE
  year TEXT;
  sequence_num INTEGER;
  invoice_num TEXT;
BEGIN
  year := TO_CHAR(NOW(), 'YYYY');

  -- Get next sequence number for current year
  SELECT COALESCE(MAX(
    CAST(SUBSTRING(invoice_number FROM 'INV-' || year || '-(\d+)') AS INTEGER)
  ), 0) + 1
  INTO sequence_num
  FROM invoices
  WHERE invoice_number LIKE 'INV-' || year || '-%';

  -- Format: INV-2026-00001
  invoice_num := 'INV-' || year || '-' || LPAD(sequence_num::TEXT, 5, '0');

  RETURN invoice_num;
END;
$$ LANGUAGE plpgsql;

-- Trigger to auto-generate invoice number if not provided
CREATE OR REPLACE FUNCTION set_invoice_number()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.invoice_number IS NULL OR NEW.invoice_number = '' THEN
    NEW.invoice_number := generate_invoice_number();
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER auto_set_invoice_number
  BEFORE INSERT ON invoices
  FOR EACH ROW
  EXECUTE FUNCTION set_invoice_number();
```

**Drizzle Schema:**
```typescript
export const invoices = pgTable('invoices', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').references(() => users.id, { onDelete: 'cascade' }).notNull(),
  subscriptionId: uuid('subscription_id').references(() => subscriptions.id, { onDelete: 'set null' }),

  invoiceNumber: text('invoice_number').unique().notNull(),
  status: text('status', { enum: ['pending', 'paid', 'failed', 'refunded'] }).notNull(),

  amount: decimal('amount', { precision: 10, scale: 2 }).notNull(),
  currency: text('currency').default('USD').notNull(),

  paymentMethod: text('payment_method'),
  paymentProvider: text('payment_provider').default('mercadopago'),
  mpPaymentId: text('mp_payment_id'),
  mpPreferenceId: text('mp_preference_id'),

  billingEmail: text('billing_email'),
  billingName: text('billing_name'),

  paidAt: timestamp('paid_at', { withTimezone: true }),
  createdAt: timestamp('created_at', { withTimezone: true }).defaultNow().notNull(),
})
```

**Uso:**
```typescript
// Crear invoice cuando se confirma pago
const invoice = await db.insert(invoices).values({
  userId: user.id,
  subscriptionId: subscription.id,
  // invoice_number se genera automáticamente
  status: 'paid',
  amount: '5.00',
  currency: 'USD',
  paymentMethod: 'credit_card',
  mpPaymentId: paymentData.id,
  billingEmail: user.email,
  billingName: user.fullName,
  paidAt: new Date(),
})
```

---

## 🔧 Database Functions & Triggers

### 1. Auto-update `updated_at`

Trigger para actualizar automáticamente `updated_at` en cada UPDATE.

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Aplicar a todas las tablas relevantes
CREATE TRIGGER update_users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_boards_updated_at
  BEFORE UPDATE ON boards
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_tasks_updated_at
  BEFORE UPDATE ON tasks
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_subscriptions_updated_at
  BEFORE UPDATE ON subscriptions
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

---

### 2. Enforce FREE Plan Limits

Function para validar límites del plan FREE.

```sql
CREATE OR REPLACE FUNCTION check_board_limit()
RETURNS TRIGGER AS $$
DECLARE
  user_plan TEXT;
  board_count INTEGER;
BEGIN
  SELECT plan INTO user_plan FROM users WHERE id = NEW.user_id;

  IF user_plan = 'free' THEN
    SELECT COUNT(*) INTO board_count FROM boards WHERE user_id = NEW.user_id;

    IF board_count >= 2 THEN
      RAISE EXCEPTION 'Free plan limited to 2 boards. Upgrade to Premium.';
    END IF;
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER enforce_board_limit
  BEFORE INSERT ON boards
  FOR EACH ROW
  EXECUTE FUNCTION check_board_limit();
```

```sql
CREATE OR REPLACE FUNCTION check_task_limit()
RETURNS TRIGGER AS $$
DECLARE
  user_plan TEXT;
  active_task_count INTEGER;
BEGIN
  SELECT plan INTO user_plan FROM users WHERE id = NEW.user_id;

  IF user_plan = 'free' THEN
    SELECT COUNT(*) INTO active_task_count
    FROM tasks
    WHERE user_id = NEW.user_id AND status = 'todo';

    IF active_task_count >= 15 THEN
      RAISE EXCEPTION 'Free plan limited to 15 active tasks. Upgrade to Premium.';
    END IF;
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER enforce_task_limit
  BEFORE INSERT ON tasks
  FOR EACH ROW
  EXECUTE FUNCTION check_task_limit();
```

---

### 3. Auto-set `completed_at`

Cuando una tarea cambia a "done", actualizar `completed_at`.

```sql
CREATE OR REPLACE FUNCTION set_completed_at()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.status = 'done' AND OLD.status != 'done' THEN
    NEW.completed_at = NOW();
  ELSIF NEW.status = 'todo' AND OLD.status = 'done' THEN
    NEW.completed_at = NULL;
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER auto_set_completed_at
  BEFORE UPDATE ON tasks
  FOR EACH ROW
  EXECUTE FUNCTION set_completed_at();
```

---

## 📈 Database Views (Optional)

Views útiles para queries comunes.

### 1. user_stats

Vista con estadísticas del usuario.

```sql
CREATE VIEW user_stats AS
SELECT
  u.id AS user_id,
  u.full_name,
  u.plan,
  COUNT(DISTINCT b.id) AS total_boards,
  COUNT(t.id) FILTER (WHERE t.status = 'todo') AS active_tasks,
  COUNT(t.id) FILTER (WHERE t.status = 'done') AS completed_tasks,
  COUNT(t.id) FILTER (WHERE t.due_date = CURRENT_DATE AND t.status = 'todo') AS tasks_due_today
FROM users u
LEFT JOIN boards b ON b.user_id = u.id
LEFT JOIN tasks t ON t.user_id = u.id
GROUP BY u.id;
```

**Query:**
```sql
SELECT * FROM user_stats WHERE user_id = 'xxx';
```

---

## 🚀 Migrations Strategy

### Drizzle Kit Workflow

1. **Modificar schema** en `src/db/schema.ts`
2. **Generar migration:**
   ```bash
   npx drizzle-kit generate:pg
   ```
3. **Revisar migration SQL** en `drizzle/migrations/`
4. **Aplicar migration:**
   ```bash
   npx drizzle-kit push:pg
   ```

### Ejemplo Migration File

```sql
-- drizzle/migrations/0001_add_recurrence_to_tasks.sql
ALTER TABLE tasks
ADD COLUMN recurrence TEXT CHECK (recurrence IN ('daily', 'weekly', 'monthly')),
ADD COLUMN recurrence_rule JSONB;
```

---

## 🔒 Row Level Security (RLS) Summary

**Política general:**
- Usuarios solo pueden ver/editar/eliminar **sus propios datos**
- Admin (futuro) puede ver todos los datos

**Implementado en:**
- ✅ users
- ✅ boards
- ✅ tasks
- ✅ subscriptions
- ✅ invoices
- ✅ notifications

**Beneficio:**
Seguridad a nivel de base de datos, incluso si hay bug en aplicación.

---

## 📊 Indexes Summary

### Critical Indexes (Performance)

| Tabla | Index | Propósito |
|-------|-------|-----------|
| users | email | Login rápido |
| boards | user_id | Listar tableros de usuario |
| tasks | user_id, status, due_date | Dashboard queries |
| tasks | board_id | Filtrar tareas por tablero |
| subscriptions | user_id | Buscar suscripción activa |
| invoices | user_id, created_at DESC | Historial de pagos ordenado |
| invoices | mp_payment_id | Webhook lookup (validar pago) |
| notifications | status, scheduled_for | Cron job (enviar pendientes) |

---

## 🧪 Test Data (Seeds)

Script para popular DB con data de prueba.

```sql
-- seed.sql
INSERT INTO users (id, email, full_name, plan) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'test@flime.ai', 'Test User', 'free'),
('550e8400-e29b-41d4-a716-446655440001', 'premium@flime.ai', 'Premium User', 'premium');

INSERT INTO boards (user_id, name, color, icon) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Trabajo', '#3b82f6', 'briefcase'),
('550e8400-e29b-41d4-a716-446655440000', 'Personal', '#10b981', 'home');

INSERT INTO tasks (board_id, user_id, title, priority, due_date) VALUES
((SELECT id FROM boards WHERE name = 'Trabajo'), '550e8400-e29b-41d4-a716-446655440000', 'Terminar reporte Q4', 'high', CURRENT_DATE + INTERVAL '1 day'),
((SELECT id FROM boards WHERE name = 'Personal'), '550e8400-e29b-41d4-a716-446655440000', 'Llamar a mamá', 'medium', CURRENT_DATE);
```

---

## 📝 Backup & Recovery

### Supabase Automated Backups
- **Daily backups** automáticos (Supabase Pro)
- **Point-in-time recovery** hasta 7 días
- Restore via Supabase dashboard

### Manual Backup (PostgreSQL)
```bash
pg_dump -h db.xxx.supabase.co -U postgres -d postgres > backup.sql
```

### Restore
```bash
psql -h db.xxx.supabase.co -U postgres -d postgres < backup.sql
```

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

## 📝 Design Decisions & Future Considerations

### Why NO roles/permissions table in MVP?

**Decision:** User roles are simple enum in `users.plan` field (`free`, `premium`, `team`)

**Rationale:**
- MVP solo tiene 2 roles de usuario (FREE/PREMIUM)
- No hay workspaces compartidos en MVP
- Permissions se validan con lógica simple en aplicación
- Evita over-engineering

**Cuando agregar roles/permisos:**
- **Fase 2 (Plan TEAM)** cuando necesitemos:
  - Workspace owners
  - Team members con diferentes permisos
  - Granular permissions (read/write/admin)

**Schema futuro:**
```sql
-- Fase 2: Cuando lanzar Plan TEAM
CREATE TABLE workspace_roles (
  id UUID PRIMARY KEY,
  workspace_id UUID REFERENCES workspaces(id),
  user_id UUID REFERENCES users(id),
  role TEXT CHECK (role IN ('owner', 'admin', 'member', 'viewer')),
  permissions JSONB, -- { "boards": "write", "tasks": "write", "members": "read" }
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### Why invoices table added to MVP?

**Decision:** Added `invoices` table even though it's not strictly necessary for MVP

**Rationale:**
- ✅ **Legal compliance** (DIAN Colombia requiere facturas)
- ✅ **User transparency** (mostrar historial de pagos)
- ✅ **Contabilidad** (necesario para declarar impuestos)
- ✅ **Soporte** (troubleshooting payment issues)
- ✅ **Professional** (startup seria = facturación seria)

**Alternativa no tomada:**
- Confiar solo en reportes de Mercado Pago
- **Por qué no:** Limitado, no tienes control, puede cambiar

### Tables Count Summary

**MVP (6 tablas):**
1. users
2. boards
3. tasks
4. subscriptions
5. invoices ← agregada v1.1
6. notifications

**Post-MVP (agregar cuando necesario):**
- workspaces (Team plan)
- workspace_roles (Team permissions)
- integrations (Google/MS Calendar)
- webhooks_log (debugging integraciones)

---

**Última actualización:** Diciembre 30, 2024 (v1.1 - Added invoices table)
**Próxima revisión:** Post Sprint 4 (ajustar si hay cambios en features)
