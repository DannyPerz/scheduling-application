# Database Schema Changelog

Registro de cambios en el esquema de base de datos de Flime.ai.

---

## [1.1] - 2024-12-30

### Added
- **Tabla `invoices`** - Sistema de facturación y recibos de pago
  - Tracking completo de pagos recibidos
  - Invoice numbers auto-generados (formato: INV-YYYY-NNNNN)
  - Integración con Mercado Pago (mp_payment_id, mp_preference_id)
  - Billing info snapshot (email, nombre al momento de compra)
  - Estados: pending, paid, failed, refunded
  - Índices optimizados para queries comunes
  - Row Level Security (RLS) aplicado

### Database Functions Added
- `generate_invoice_number()` - Genera números de factura secuenciales por año
- `set_invoice_number()` - Trigger que auto-asigna invoice number en INSERT

### Why This Change?
**Compliance y profesionalismo:**
- DIAN Colombia requiere registro de facturas para declaración de impuestos
- Transparencia con usuarios (pueden ver historial completo)
- Facilita soporte técnico (troubleshooting de pagos)
- Contabilidad ordenada desde el inicio

**Business value:**
- Necesario para operar legalmente en Colombia
- Evita refactor futuro (agregarlo después es más complejo)
- Professional appearance (startup seria)

### Migration Script
```sql
-- Create invoices table
CREATE TABLE invoices (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  subscription_id UUID REFERENCES subscriptions(id) ON DELETE SET NULL,

  invoice_number TEXT UNIQUE NOT NULL,
  status TEXT NOT NULL CHECK (status IN ('pending', 'paid', 'failed', 'refunded')),

  amount DECIMAL(10, 2) NOT NULL,
  currency TEXT NOT NULL DEFAULT 'USD',

  payment_method TEXT,
  payment_provider TEXT DEFAULT 'mercadopago',
  mp_payment_id TEXT,
  mp_preference_id TEXT,

  billing_email TEXT,
  billing_name TEXT,

  paid_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Create indexes
CREATE INDEX idx_invoices_user_id ON invoices(user_id);
CREATE INDEX idx_invoices_subscription_id ON invoices(subscription_id);
CREATE INDEX idx_invoices_status ON invoices(status);
CREATE INDEX idx_invoices_mp_payment_id ON invoices(mp_payment_id);
CREATE INDEX idx_invoices_created_at ON invoices(created_at DESC);

-- Enable RLS
ALTER TABLE invoices ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view own invoices"
  ON invoices FOR SELECT
  USING (auth.uid() = user_id);

-- Auto-generate invoice numbers
CREATE OR REPLACE FUNCTION generate_invoice_number()
RETURNS TEXT AS $$
DECLARE
  year TEXT;
  sequence_num INTEGER;
  invoice_num TEXT;
BEGIN
  year := TO_CHAR(NOW(), 'YYYY');

  SELECT COALESCE(MAX(
    CAST(SUBSTRING(invoice_number FROM 'INV-' || year || '-(\d+)') AS INTEGER)
  ), 0) + 1
  INTO sequence_num
  FROM invoices
  WHERE invoice_number LIKE 'INV-' || year || '-%';

  invoice_num := 'INV-' || year || '-' || LPAD(sequence_num::TEXT, 5, '0');

  RETURN invoice_num;
END;
$$ LANGUAGE plpgsql;

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

### Updated ERD
```
users
  ├── boards → tasks
  ├── subscriptions → invoices (NEW)
  └── notifications
```

---

## [1.0] - 2024-12-30

### Initial Schema
**5 tablas principales:**

1. **users** - Usuarios del sistema
   - Auth (Supabase Auth integration)
   - Plan (free/premium/team)
   - Preferences (notifications, timezone, language)

2. **boards** - Tableros de organización
   - Customizable (color, icon, name)
   - User-owned
   - FREE limit: 2 boards

3. **tasks** - Tareas
   - Linked to boards
   - Due dates, priorities, status
   - Recurrence (premium)
   - Reminders
   - FREE limit: 15 active tasks

4. **subscriptions** - Suscripciones Premium
   - Mercado Pago integration
   - Plan types (premium_monthly, premium_yearly, team_monthly)
   - Period tracking
   - Cancellation handling

5. **notifications** - Log de notificaciones
   - Email, web push, WhatsApp, SMS
   - Status tracking (pending, sent, failed)
   - Scheduled delivery

### Security
- Row Level Security (RLS) en todas las tablas
- Users solo acceden a sus propios datos
- Policies para SELECT, INSERT, UPDATE, DELETE

### Database Functions & Triggers
- `update_updated_at_column()` - Auto-update timestamps
- `check_board_limit()` - Enforce FREE plan board limit
- `check_task_limit()` - Enforce FREE plan task limit
- `set_completed_at()` - Auto-set completion timestamp

---

## Decisiones de Diseño

### ❌ NO incluido en MVP: Roles/Permissions table

**Por qué:**
- MVP solo tiene 2 planes simples (FREE/PREMIUM)
- No hay workspaces compartidos
- Permissions = simple checks en código
- Evita over-engineering

**Cuándo agregar:**
- Fase 2, cuando lancemos Plan TEAM
- Necesitaremos: workspace_roles, granular permissions

### ✅ Incluido en MVP: Invoices table

**Por qué:**
- Compliance legal (DIAN Colombia)
- Profesionalismo
- User transparency
- Facilita soporte

**Alternativa rechazada:**
- Solo usar reportes de Mercado Pago
- Problema: No tienes control, puede cambiar

---

## Próximos Cambios Planeados

### Post-MVP (Fase 2)

**Team Plan:**
- `workspaces` table
- `workspace_roles` table
- Granular permissions (JSONB)

**Calendar Integrations:**
- `integrations` table
- OAuth tokens storage
- Sync logs

**Debugging:**
- `webhooks_log` table
- API request/response logging

---

## Migration Strategy

**Development:**
```bash
# Generate migration
npx drizzle-kit generate:pg

# Review migration
cat drizzle/migrations/0001_*.sql

# Apply to local DB
npx drizzle-kit push:pg
```

**Production:**
```bash
# Test en staging primero
DATABASE_URL=staging_url npx drizzle-kit push:pg

# Backup antes de aplicar
pg_dump production_db > backup_pre_migration.sql

# Aplicar en producción
DATABASE_URL=production_url npx drizzle-kit push:pg
```

---

## Schema Evolution Philosophy

**Principios:**
1. **Start simple** - Solo lo necesario para MVP
2. **Add when needed** - No anticipar demasiado
3. **Backwards compatible** - Migrations nunca rompen data existente
4. **Document decisions** - Explicar por qué (no solo qué)

**Anti-patterns evitados:**
- ❌ Over-normalized (demasiadas tablas desde el inicio)
- ❌ Premature optimization (índices innecesarios)
- ❌ Soft deletes everywhere (solo donde tenga sentido)
- ❌ EAV (Entity-Attribute-Value) madness

---

**Maintained by:** Fundador Flime
**Last updated:** Diciembre 30, 2024
