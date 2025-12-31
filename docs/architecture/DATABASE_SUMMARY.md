# Flime.ai - Database Schema Summary

**Quick Reference** - Vista rápida del esquema de base de datos

---

## 📊 Schema Overview (v1.1)

### Total Tables: 6

```
┌─────────────────────────────────────────────────────────┐
│                   DATABASE SCHEMA                        │
│                      Flime.ai v1.1                       │
└─────────────────────────────────────────────────────────┘

1. users (Usuarios)
   ├── Autenticación (Supabase Auth)
   ├── Plan (free/premium/team)
   └── Preferences

2. boards (Tableros)
   ├── Customizable (color, icon)
   └── Límite FREE: 2 boards

3. tasks (Tareas)
   ├── Due dates, priorities
   ├── Recurrence (Premium)
   └── Límite FREE: 15 tasks

4. subscriptions (Suscripciones)
   ├── Mercado Pago
   └── Period tracking

5. invoices (Facturas) ← NEW in v1.1
   ├── Compliance legal
   ├── Invoice numbers auto
   └── Payment tracking

6. notifications (Notificaciones)
   └── Multi-channel logs
```

---

## 🔑 Primary Keys & Relationships

```
users (id)
  │
  ├──< boards (user_id) [1:N]
  │      │
  │      └──< tasks (board_id) [1:N]
  │
  ├──< tasks (user_id) [1:N]
  │
  ├──< subscriptions (user_id) [1:1]
  │      │
  │      └──< invoices (subscription_id) [1:N]
  │
  ├──< invoices (user_id) [1:N]
  │
  └──< notifications (user_id) [1:N]
         └── tasks (task_id) [N:1, optional]
```

---

## 📋 Table Details

### 1. users

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| email | TEXT | Unique, required |
| full_name | TEXT | Required |
| avatar_url | TEXT | Optional |
| **plan** | TEXT | 'free' \| 'premium' \| 'team' |
| timezone | TEXT | Default: 'America/Bogota' |
| language | TEXT | 'es' \| 'en' |
| email_notifications | BOOLEAN | Default: true |
| web_push_notifications | BOOLEAN | Default: true |
| daily_summary | BOOLEAN | Default: false |
| created_at | TIMESTAMPTZ | Auto |
| updated_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled
- ✅ Auto-update `updated_at` trigger
- ✅ Indexed: email

---

### 2. boards

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| user_id | UUID | FK → users |
| name | TEXT | Max 50 chars |
| color | TEXT | Default: '#3b82f6' |
| icon | TEXT | Default: 'folder' |
| description | TEXT | Max 200 chars, optional |
| order_index | INTEGER | For sorting |
| created_at | TIMESTAMPTZ | Auto |
| updated_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled
- ✅ FREE limit: 2 boards (enforced by trigger)
- ✅ Cascade delete with tasks
- ✅ Indexed: user_id, order_index

---

### 3. tasks

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| board_id | UUID | FK → boards |
| user_id | UUID | FK → users |
| title | TEXT | Max 200 chars |
| description | TEXT | Max 1000 chars, optional |
| due_date | DATE | Optional |
| due_time | TIME | Optional |
| **priority** | TEXT | 'high' \| 'medium' \| 'low' |
| **status** | TEXT | 'todo' \| 'done' |
| recurrence | TEXT | 'daily' \| 'weekly' \| 'monthly' (Premium) |
| recurrence_rule | JSONB | Complex recurrence rules |
| reminder_offset | INTEGER | Minutes before due |
| completed_at | TIMESTAMPTZ | Auto-set when done |
| created_at | TIMESTAMPTZ | Auto |
| updated_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled
- ✅ FREE limit: 15 active tasks (enforced by trigger)
- ✅ Auto-set `completed_at` on status change
- ✅ Indexed: user_id, board_id, status, due_date

---

### 4. subscriptions

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| user_id | UUID | FK → users, UNIQUE |
| **status** | TEXT | 'active' \| 'canceled' \| 'past_due' \| 'trial' |
| **plan_type** | TEXT | 'premium_monthly' \| 'premium_yearly' \| 'team_monthly' |
| amount | DECIMAL(10,2) | Price paid |
| currency | TEXT | Default: 'USD' |
| interval | TEXT | 'month' \| 'year' |
| current_period_start | TIMESTAMPTZ | Billing period start |
| current_period_end | TIMESTAMPTZ | Billing period end |
| cancel_at_period_end | BOOLEAN | Downgrade scheduled |
| canceled_at | TIMESTAMPTZ | When canceled |
| mp_subscription_id | TEXT | Mercado Pago ID |
| mp_payer_id | TEXT | Mercado Pago payer |
| created_at | TIMESTAMPTZ | Auto |
| updated_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled (read-only for user)
- ✅ 1:1 with users (UNIQUE constraint)
- ✅ Indexed: user_id, status, mp_subscription_id

---

### 5. invoices ⭐ NEW

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| user_id | UUID | FK → users |
| subscription_id | UUID | FK → subscriptions |
| **invoice_number** | TEXT | UNIQUE, auto-generated |
| **status** | TEXT | 'pending' \| 'paid' \| 'failed' \| 'refunded' |
| amount | DECIMAL(10,2) | Total paid |
| currency | TEXT | Default: 'USD' |
| payment_method | TEXT | 'credit_card' \| 'debit_card' \| 'pse' \| 'cash' |
| payment_provider | TEXT | Default: 'mercadopago' |
| mp_payment_id | TEXT | Mercado Pago payment ID |
| mp_preference_id | TEXT | Mercado Pago preference ID |
| billing_email | TEXT | Email snapshot |
| billing_name | TEXT | Name snapshot |
| paid_at | TIMESTAMPTZ | Payment timestamp |
| created_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled (read-only for user)
- ✅ Auto-generated invoice numbers (INV-2026-00001)
- ✅ Legal compliance (DIAN Colombia)
- ✅ Indexed: user_id, subscription_id, mp_payment_id, created_at

**Invoice Number Format:**
```
INV-YYYY-NNNNN

Examples:
INV-2026-00001
INV-2026-00002
INV-2027-00001  (resets each year)
```

---

### 6. notifications

| Column | Type | Notes |
|--------|------|-------|
| id | UUID | PK |
| user_id | UUID | FK → users |
| task_id | UUID | FK → tasks, nullable |
| **type** | TEXT | 'task_reminder' \| 'daily_summary' |
| **channel** | TEXT | 'email' \| 'web_push' \| 'whatsapp' \| 'sms' |
| **status** | TEXT | 'pending' \| 'sent' \| 'failed' |
| scheduled_for | TIMESTAMPTZ | When to send |
| sent_at | TIMESTAMPTZ | When actually sent |
| error_message | TEXT | If failed |
| created_at | TIMESTAMPTZ | Auto |

**Key Features:**
- ✅ RLS enabled (read-only for user)
- ✅ Used by cron jobs to send reminders
- ✅ Indexed: user_id, task_id, status, scheduled_for

---

## 🔒 Security (RLS)

**All tables have Row Level Security enabled.**

**Policy:** Users can only access their own data.

```sql
-- Example RLS policy (applied to all tables)
CREATE POLICY "Users can view own data"
  ON table_name FOR SELECT
  USING (auth.uid() = user_id);
```

**Benefits:**
- ✅ Security at database level
- ✅ Protection even if app has bugs
- ✅ Works with Supabase Auth

---

## 📈 Performance (Indexes)

**Critical indexes for fast queries:**

| Table | Index | Use Case |
|-------|-------|----------|
| users | email | Login |
| boards | user_id | List user's boards |
| boards | user_id, order_index | Sorted boards |
| tasks | user_id, status, due_date | Dashboard queries |
| tasks | board_id | Filter by board |
| subscriptions | user_id | Get active subscription |
| **invoices** | **user_id, created_at DESC** | **Payment history** |
| **invoices** | **mp_payment_id** | **Webhook lookup** |
| notifications | status, scheduled_for | Cron job (pending notifications) |

---

## ⚡ Database Functions

### Auto-Update Triggers

```sql
-- All tables with updated_at
update_updated_at_column()
  → users, boards, tasks, subscriptions
```

### Business Logic Triggers

```sql
-- Enforce FREE plan limits
check_board_limit()      → Max 2 boards for free users
check_task_limit()       → Max 15 active tasks for free users

-- Auto-set timestamps
set_completed_at()       → Auto-set when task.status = 'done'

-- Invoice generation
generate_invoice_number() → INV-YYYY-NNNNN format
set_invoice_number()      → Auto-assign on INSERT
```

---

## 🧪 Sample Queries

### Get user with subscription status
```sql
SELECT
  u.email,
  u.plan,
  s.status AS subscription_status,
  s.current_period_end
FROM users u
LEFT JOIN subscriptions s ON s.user_id = u.id
WHERE u.id = 'user-uuid';
```

### Get dashboard data
```sql
-- Tasks due today
SELECT * FROM tasks
WHERE user_id = 'user-uuid'
  AND status = 'todo'
  AND due_date = CURRENT_DATE
ORDER BY priority DESC, due_time ASC;
```

### Get payment history
```sql
SELECT
  invoice_number,
  amount,
  currency,
  status,
  paid_at
FROM invoices
WHERE user_id = 'user-uuid'
ORDER BY created_at DESC
LIMIT 10;
```

### Get pending notifications to send
```sql
SELECT * FROM notifications
WHERE status = 'pending'
  AND scheduled_for <= NOW()
ORDER BY scheduled_for ASC
LIMIT 100;
```

---

## 🔄 Migration Path

### Current: v1.1 (6 tables)
```
users, boards, tasks, subscriptions, invoices, notifications
```

### Future: v2.0 (Team Plan)
```
+ workspaces
+ workspace_roles
+ workspace_members
```

### Future: v2.1 (Integrations)
```
+ integrations (Google/MS Calendar)
+ webhooks_log
```

---

## 📊 Data Size Estimates

**Year 1 projection (1,000 users):**

| Table | Avg Records/User | Total Records | Size Estimate |
|-------|------------------|---------------|---------------|
| users | 1 | 1,000 | ~100 KB |
| boards | 3 | 3,000 | ~300 KB |
| tasks | 50 | 50,000 | ~10 MB |
| subscriptions | 0.1 | 100 | ~10 KB |
| invoices | 1.2 | 1,200 | ~100 KB |
| notifications | 100 | 100,000 | ~15 MB |
| **TOTAL** | | **~155,000** | **~26 MB** |

**Supabase Free Tier:** 500 MB (we're safe!)

---

## 🎯 Design Philosophy

### Principles Applied

1. ✅ **Start Simple**
   - Only 6 tables for MVP
   - No over-engineering

2. ✅ **Scalable**
   - UUIDs (can distribute)
   - Proper indexes
   - RLS for security

3. ✅ **Type-Safe**
   - ENUMs for status fields
   - Constraints on all columns
   - Drizzle ORM for type safety

4. ✅ **Compliance First**
   - Invoices table (legal requirement)
   - User data ownership (GDPR-friendly)
   - Audit trail (created_at everywhere)

---

## 📝 Next Steps

**For Sprint 1 (Setup):**
1. Create Supabase project
2. Run all CREATE TABLE scripts
3. Apply RLS policies
4. Create triggers & functions
5. Test with seed data

**For Sprint 8 (Payments):**
1. Implement invoice creation on payment success
2. Email invoice receipts
3. Display invoice history in settings

---

**See full documentation:** [database-schema.md](database-schema.md)
**See change log:** [CHANGELOG.md](CHANGELOG.md)

**Last updated:** Diciembre 30, 2024 (v1.1)
