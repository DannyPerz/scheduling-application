# Flime.ai - Product Requirements Document (PRD)

**Versión:** 2.0 (Actualizado con Schema Real)
**Fecha:** Diciembre 31, 2025
**Owner:** Daniel Pérez
**Status:** ✅ Sprint 1 Completado

---

## 📋 Executive Summary

**Flime.ai** es una aplicación web SaaS de gestión de tareas y recordatorios diseñada específicamente para personas con TDA/TDAH y aquellos que buscan construir disciplina a través de organización simple y recordatorios multi-canal.

### Problema
Las personas con TDA/TDAH y aquellos con múltiples responsabilidades olvidan tareas importantes, citas y compromisos porque:
- Las apps existentes son demasiado complejas (demasiadas features)
- Estructura rígida de "boards" no funciona para cerebros ADHD
- Time blindness: subestiman cuánto toman las tareas
- Los recordatorios son fáciles de ignorar

### Solución
Una aplicación web simple que permite:
- Organizar tareas con **tags flexibles** (no boards rígidos)
- **Campos ADHD-friendly**: estimated vs actual duration
- Recordatorios multi-canal (email, web push, WhatsApp, SMS)
- Subtareas para dividir grandes tareas
- Precio accesible para mercado LATAM

**Cambio importante:** Reemplazamos boards rígidos por **tags flexibles** basado en investigación ADHD.

### Objetivos del Producto
1. **MVP en 10 semanas** (2.5 meses)
2. **100 usuarios registrados** en primeros 3 meses post-launch
3. **10 usuarios premium** en primeros 6 meses
4. **NPS > 40** en fase inicial

---

## 🎯 Goals & Success Metrics

| Métrica | MVP (3 meses) | Año 1 | Año 2 |
|---------|---------------|-------|-------|
| Usuarios registrados | 100 | 1,000 | 10,000 |
| Usuarios premium | 10 | 100 | 1,000 |
| MRR (Monthly Recurring Revenue) | $50 | $500 | $5,000 |
| Retention 30 días | 30% | 50% | 60% |
| NPS | 40+ | 50+ | 60+ |

---

## 👥 User Personas

### Persona 1: Carlos (Primaria)
- **Demografía:** 28 años, desarrollador, TDA diagnosticado
- **Frustración:** Olvida reuniones, deadlines, subestima tiempo de tareas
- **Comportamiento:** Tiene 5 apps instaladas, usa ninguna consistentemente
- **Necesidad:** Sistema flexible que refleje cómo funciona su cerebro
- **Quote:** "Necesito algo que entienda que no sé cuánto tiempo me va a tomar algo"

### Persona 2: Ana (Secundaria)
- **Demografía:** 34 años, madre + freelancer diseñadora
- **Frustración:** Demasiadas cosas en la cabeza, estructura rígida la agobia
- **Comportamiento:** Post-its por toda la casa, se pierden
- **Necesidad:** Externalizar su mente, organización simple sin categorías rígidas
- **Quote:** "Las boards me limitan, a veces una tarea es trabajo Y personal"

### Persona 3: Equipo Startup (Terciaria - Fase 2)
- **Demografía:** 3-5 personas, startup tech
- **Frustración:** Tareas asignadas que se olvidan
- **Necesidad:** Workspaces compartidos, asignaciones claras
- **Quote:** "Necesitamos algo más simple que Jira pero más robusto que Google Tasks"

---

## 🏗️ Product Architecture

### Modelo Freemium

#### FREE Plan (Entry Point)
**Objetivo:** Enganchar usuarios, permitir exploración del producto

**Límites:**
- 15 tareas activas simultáneas (pending + in_progress)
- Tags ilimitados (cambio vs plan original)
- Recordatorios básicos (sin recurrencia)
- Sin temas personalizados

**CTA para Premium:** "Desbloquea tareas y recordatorios ilimitados"

#### PREMIUM Plan - $5 USD/mes o $50 USD/año
**Objetivo:** Usuarios comprometidos que necesitan sin límites

**Incluye:**
- ✅ Tareas ilimitadas
- ✅ Tags ilimitados
- ✅ Recordatorios ilimitados
- ✅ Recordatorios recurrentes (isRecurring + recurrencePattern)
- ✅ Subtareas (parentTaskId)
- ✅ Campos ADHD-friendly (actualDuration tracking)
- ✅ Exportar datos (CSV/JSON)
- ✅ Soporte prioritario (email)
- ✅ Sincronización Google/Microsoft Calendar (Fase 2)

#### TEAM Plan - $12 USD/mes (Fase 2)
**Objetivo:** Equipos pequeños, startups

**Incluye:**
- ✅ Todo Premium +
- ✅ Workspaces compartidos
- ✅ Asignación de tareas a miembros
- ✅ Comentarios en tareas
- ✅ 5 miembros incluidos
- ✅ +$2 USD por miembro adicional
- ✅ Admin dashboard

---

## 📱 Core Features (MVP v1.0)

### 1. Autenticación & Onboarding

#### 1.1 Registro/Login (Sprint 2)
- **Descripción:** Sistema de autenticación seguro
- **Métodos:**
  - Email + Password (Supabase Auth)
  - Google OAuth (Fase 2)
- **Database:** Sync con `auth.users` → `public.users` vía trigger
- **Flujo:**
  1. Usuario ingresa email + password
  2. Supabase Auth valida
  3. Trigger crea registro en `public.users`
  4. Redirige a onboarding

#### 1.2 Onboarding (Primera vez)
- **Objetivo:** Educar y configurar perfil
- **Pasos:**
  1. **Bienvenida:** "¡Hola! Vamos a organizar tu vida en 3 pasos"
  2. **Crear primera tarea:** "Agreguemos tu primera tarea"
  3. **Configurar tags:** "Organiza con etiquetas: #trabajo #personal"
  4. **Preferencias:** Timezone, idioma, notificaciones
  5. **Dashboard:** Redirige a vista principal

---

### 2. Tareas (Tasks) - ADHD-Friendly

**Decisión de diseño:** Removimos boards rígidos por tags flexibles

#### 2.1 CRUD Tareas
**Crear tarea:**
- Título (max 200 chars) - `tasks.title`
- Descripción (max 1000 chars) - `tasks.description`
- Fecha de vencimiento (opcional) - `tasks.dueDate`
- Prioridad (Baja/Media/Alta/Urgente) - `tasks.priority`
- **Estimated Duration** (minutos) - `tasks.estimatedDuration` - ADHD feature
- **Tags** (multi-select) - Relación `task_tags`
- **Parent Task** (opcional) - `tasks.parentTaskId` - Para subtareas

**Ver tareas:**
- Vista de lista agrupada por status
- Vista de calendario (mensual)
- Filtros: Estado, Prioridad, Tags
- Ordenar: Fecha, Prioridad, Manual (tasks.order)

**Editar tarea:**
- Modal con todos los campos
- **Actualizar Actual Duration** al completar - `tasks.actualDuration`

**Completar tarea:**
- Checkbox → marca como "Completed"
- Registra `tasks.completedAt`
- (Premium) Muestra insight: "Estimaste 30min, tomó 45min"

#### 2.2 Estados de Tarea
**Database:** `tasks.status` enum
- **Pending** (default)
- **In Progress**
- **Completed**
- **Archived**

#### 2.3 Campos ADHD-Friendly
**Database schema:**
```typescript
{
  estimatedDuration: integer,  // Cuánto crees que tomará (minutos)
  actualDuration: integer,     // Cuánto realmente tomó (auto o manual)
  parentTaskId: uuid,          // Soporte para subtareas
  order: integer,              // Ordenamiento manual
  isRecurring: boolean,        // Tarea recurrente (Premium)
  recurrencePattern: text,     // RRULE format (Premium)
}
```

**Por qué estos campos ayudan a ADHD:**
- `estimatedDuration`: Combate time blindness, aprende a estimar
- `actualDuration`: Feedback loop para mejorar estimaciones
- `parentTaskId`: Divide tareas grandes en subtareas (menos abrumador)
- `isRecurring`: Automatiza rutinas (ej: tomar medicación diaria)

#### 2.4 Límites por Plan
- **FREE:** 15 tareas activas (pending + in_progress)
- **PREMIUM:** Ilimitado
- Tareas completed/archived NO cuentan

---

### 3. Tags (Etiquetas) - Reemplazo de Boards

**Por qué tags > boards:**
- ✅ Más flexibles (una tarea puede ser #trabajo Y #urgente)
- ✅ Menos fricción mental para crear categorías
- ✅ Mejor para ADHD: menos estructura rígida
- ✅ Evita el problema de "¿En qué board va esto?"

#### 3.1 CRUD Tags
**Crear tag:**
- Nombre (max 30 chars) - `tags.name`
- Color (hex) - `tags.color` (default: #3b82f6)

**Ver tags:**
- Lista en sidebar con contador
- Color indicator

**Editar tag:**
- Cambiar nombre/color

**Eliminar tag:**
- Confirmación: "Se desasignará de X tareas"
- Tareas NO se eliminan

#### 3.2 Relación Many-to-Many
**Database:** Tabla `task_tags`
```sql
CREATE TABLE task_tags (
  task_id UUID REFERENCES tasks(id),
  tag_id UUID REFERENCES tags(id),
  PRIMARY KEY (task_id, tag_id)
)
```

**Features:**
- Una tarea puede tener múltiples tags
- Filtrar tareas por tag
- Buscar por tag

#### 3.3 Tags Sugeridos (onboarding)
- 🏢 Trabajo
- 🏠 Personal
- 💪 Salud
- 📚 Estudio
- 💰 Finanzas

---

### 4. Recordatorios & Alertas

**Decisión de diseño:** Tabla `reminders` separada (no embedded en tasks)

#### 4.1 Sistema de Recordatorios
**Database:** Tabla `reminders`
```typescript
{
  id: uuid,
  taskId: uuid,        // FK a tasks
  userId: uuid,        // FK a users
  sendAt: timestamp,   // Cuándo enviar
  sent: boolean,       // Ya enviado?
  channel: enum,       // email, web_push, whatsapp, sms
}
```

**Features:**
- Múltiples recordatorios por tarea
- Ejemplo: 1 día antes + 1 hora antes
- Cron job envía automáticamente cuando `sendAt <= now && sent = false`

#### 4.2 Canales de Alerta

**MVP v1.0:**
- ✅ **Email** (Resend)
  - Template limpio, branded
  - CTA: "Ver tarea" → link directo

**Fase 2:**
- 📱 **Web Push Notifications**
  - Solo si usuario tiene sesión abierta

- 📱 **WhatsApp** (Meta Business API)
  - Costo: ~$0.005 USD por mensaje

- 📱 **SMS** (Twilio)
  - Costo: ~$0.05 USD por mensaje

#### 4.3 Preferencias de Notificación
**Database:** Tabla `users`
```typescript
{
  emailNotifications: boolean,      // default: true
  webPushNotifications: boolean,    // default: true
  dailySummary: boolean,            // default: false
  dailySummaryTime: time,           // default: '08:00'
}
```

#### 4.4 Límites por Plan
- **FREE:** Sin límite de recordatorios (MVP simplificado)
- **PREMIUM:** Recordatorios recurrentes (`tasks.isRecurring`)

---

### 5. Calendario

#### 5.1 Vista de Calendario (MVP)
- Vista mensual (mes actual)
- Tareas mostradas por día (`tasks.dueDate`)
- Color-coded por tag principal
- Click en día → crear tarea con fecha pre-filled
- Click en tarea → editar

#### 5.2 Integraciones (Fase 2)
**Google Calendar / Microsoft Calendar:**
- OAuth 2.0
- Sincronización bidireccional
- Premium only

---

### 6. Dashboard Principal

#### 6.1 Layout
**Sidebar:**
- Logo Flime
- Búsqueda de tareas
- "Hoy" (tasks.dueDate = today)
- "Próximas 7 días"
- **Lista de tags** (no boards)
- "+ Nuevo tag"
- "Configuración"
- "Upgrade to Premium" (si FREE)

**Header:**
- Saludo: "Buenos días, [users.fullName]"
- Avatar (dropdown: Perfil, Logout)

**Main:**
- Quick stats:
  - X tareas pendientes hoy
  - X tareas completadas semana
  - **ADHD Insight:** Tiempo estimado vs real
    - "Esta semana estimaste 5h, tomó 7h"
- Tabs: Lista / Calendario

**FAB:**
- Botón "+" → Nueva tarea

#### 6.2 Búsqueda
- Buscar por `tasks.title`
- Resultados en tiempo real (debounce 300ms)
- Mostrar tags de cada resultado

---

### 7. Pagos (Mercado Pago)

#### 7.1 Integración
**Database:** 2 tablas
```typescript
// subscriptions
{
  userId: uuid,
  status: enum,                    // active, canceled, past_due, trial
  planType: enum,                  // premium_monthly, premium_yearly
  currentPeriodEnd: timestamp,
  mpSubscriptionId: text,
}

// payments (historial)
{
  userId: uuid,
  subscriptionId: uuid,
  amount: decimal,
  status: enum,                    // pending, approved, failed, refunded
  mpPaymentId: text,
  paidAt: timestamp,
}
```

**Features:**
- Crear preferencia de pago
- Webhooks para confirmar
- Actualizar `users.plan` (FREE → PREMIUM)
- Crear registro en `subscriptions` y `payments`

#### 7.2 Gestión de Suscripción
- Ver plan actual (`users.plan`)
- Próxima renovación (`subscriptions.currentPeriodEnd`)
- Cancelar suscripción
- NO facturas formales (payments es suficiente para MVP)

---

## 🚫 Out of Scope (Fase 2+)

### Cambios vs Plan Original
- ❌ **Boards** → ✅ Tags flexibles (schema change)
- ❌ **Notifications log table** → `reminders.sent` es suficiente
- ❌ **Invoices table** → `payments` cubre MVP

### No incluir en MVP
- ❌ App móvil nativa
- ❌ Google/MS Calendar sync
- ❌ WhatsApp/SMS
- ❌ Web push (postponed)
- ❌ Plan Team
- ❌ Vista Kanban
- ❌ Dark mode (Light only en MVP)
- ❌ API pública

---

## 🎨 UI/UX Requirements

### Design Principles
1. **Simplicidad extrema** - Menos es más
2. **ADHD-friendly** - Sin distracciones, colores calmantes
3. **Accesibilidad** - WCAG AA compliance
4. **Responsive** - Mobile-first
5. **Fast feedback** - Animaciones < 200ms

### Visual Style
- **Tipografía:** Geist Vercel (fuente moderna y limpia)
- **Colores:**
   /* Coastal Calm Palette - ADHD-Friendly Colors */
    --primary: 210 70% 58%; /* #4A90E2 - Soft Blue (calming, focus) */
    --secondary: 169 54% 65%; /* #7DD3C0 - Sage Mint (tranquility, growth) */
    --accent: 42 89% 58%; /* #F7B731 - Warm Yellow (positivity, energy) */
    --success: 139 63% 62%; /* #6BCF7F - Soft Green */
    --warning: 26 85% 69%; /* #F39C6B - Soft Orange */
    --danger: 353 76% 63%; /* #E85D75 - Soft Red */
- **Componentes:** shadcn/ui (Radix UI primitives)

---

## 🔐 Security & Privacy

### Autenticación
- Supabase Auth (bcrypt)
- JWT tokens (HttpOnly cookies)
- Server-side validation (proxy.ts)

### Autorización
- Row Level Security (Supabase RLS)
  - `auth.uid() = user_id` en todas las tablas
- Users solo ven sus propios datos

### Privacy
- GDPR compliant
- Exportar datos (Premium)
- Eliminar cuenta (CASCADE delete)

---

## 📊 Analytics & Tracking

**PostHog (postponed para Fase 2)**

**Eventos a trackear:**
- User signup
- Task created/completed/deleted
- Tag created
- Reminder sent
- Upgrade to Premium

**Métricas:**
- Task completion rate
- Estimated vs Actual duration accuracy (ADHD insight)
- Tags per user
- Reminders sent per user

---

## 📝 Technical Requirements

Ver [tech-stack.md](../architecture/tech-stack.md)

**Stack (Sprint 1 ✅):**
- Next.js 16.1.1 + TypeScript 5.9.3
- Tailwind CSS v4.1.18
- shadcn/ui v3.6.2
- Supabase (PostgreSQL 15, São Paulo)
- Drizzle ORM v0.45.1
- Sentry v10.32.1
- Vercel (São Paulo)

**Database Schema (7 tablas):**
1. users - Usuarios con preferencias
2. tasks - Tareas ADHD-friendly
3. reminders - Recordatorios
4. tags - Etiquetas
5. task_tags - Many-to-many
6. subscriptions - Suscripciones
7. payments - Historial

---

## 🗓️ Timeline & Milestones

| Sprint | Semanas | Foco | Status |
|--------|---------|------|--------|
| 1 | 1 | Setup & Foundation | ✅ Completado |
| 2 | 2 | Auth & User Management | 🔄 Siguiente |
| 3-4 | 3-4 | Tasks & Tags CRUD | 📋 Planificado |
| 5-6 | 5-6 | Calendar & Reminders | 📋 Planificado |
| 7-8 | 7-8 | Freemium & Payments | 📋 Planificado |
| 9-10 | 9-10 | Polish + Launch | 📋 Planificado |

**Total: 10 semanas**

---

## 📞 Support & Feedback

### Soporte MVP
- Email: support@flime.ai
- Respuesta: 24-48h (FREE), 12h (PREMIUM)

### Feedback
- In-app feedback widget
- NPS survey (mensual)

---

## ✅ Definition of Done (MVP)

El MVP está completo cuando:
- ✅ Usuario puede registrarse con email/Google
- ✅ Usuario puede crear tareas con tags
- ✅ Usuario puede ver calendario
- ✅ Usuario recibe recordatorios por email
- ✅ Usuario puede pagar Premium (Mercado Pago)
- ✅ Límites FREE aplicados
- ✅ Responsive mobile
- ✅ Lighthouse > 85
- ✅ 10 usuarios beta sin ayuda

---

**Aprobado por:** Daniel Pérez
**Fecha:** Diciembre 31, 2024
**Sprint 1:** ✅ Completado

---

## Change Log

| Versión | Fecha | Cambios |
|---------|-------|---------|
| 1.0 | 2024-12-30 | Versión inicial |
| 2.0 | 2024-12-31 | Schema actualizado: boards → tags, 7 tablas, campos ADHD |
