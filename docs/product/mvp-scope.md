# Flime.ai - MVP Scope Definition

**Versión:** 2.0 (Actualizado con Schema Real)
**Fecha:** Diciembre 31, 2024
**Timeline:** 10 semanas (14 horas/semana = 140 horas totales)
**Status Sprint 1:** ✅ Completado

---

## 🎯 MVP Objective

**Lanzar una versión funcional de Flime.ai que permita a usuarios con ADHD/ADD:**
1. Organizarse con tareas y etiquetas (tags)
2. Recibir recordatorios confiables por email
3. Pagar por una suscripción Premium si les gusta el producto

**Criterio de éxito:** 10 usuarios beta completan onboarding y crean al menos 5 tareas sin asistencia.

**Cambio importante:** Reemplazamos boards rígidos por **tags flexibles** (mejor UX para ADHD)

---

## ✅ IN SCOPE (Must Have)

### 1. Autenticación & Usuarios

#### 1.1 Registro/Login
- [ ] Registro con email + password
- [ ] Login con email + password
- [ ] Verificación de email (Supabase Auth)
- [ ] OAuth con Google (Fase 2)
- [ ] Logout
- [ ] Password reset (forgot password flow)

#### 1.2 Perfil de Usuario
- [ ] Nombre completo (users.fullName)
- [ ] Email (read-only)
- [ ] Avatar (iniciales generadas automáticamente)
- [ ] Zona horaria (users.timezone - auto-detectada, editable)
- [ ] Idioma (users.language: es/en, default: es)

#### 1.3 Onboarding (Primera vez)
- [ ] Wizard de 3 pasos:
  1. Bienvenida + preferencias básicas
  2. Crear primera tarea
  3. Configurar preferencias de notificación
- [ ] Skip option (ir directo al dashboard)

---

### 2. Tareas (Tasks) - ADHD-Friendly

#### 2.1 CRUD Tareas
- [ ] **Crear tarea**
  - Título (obligatorio, max 200 chars)
  - Descripción (opcional, max 1000 chars)
  - Fecha de vencimiento (date picker, opcional)
  - Prioridad (Baja/Media/Alta/Urgente, default: Media)
  - **Estimated Duration** (minutos) - ADHD: time blindness
  - Tags (opcional, multi-select)

- [ ] **Ver tareas**
  - Vista de lista agrupada por status
  - Vista de calendario (mensual)
  - Filtros: Estado, Prioridad, Tags
  - Ordenar: Fecha, Prioridad, Alfabético

- [ ] **Editar tarea**
  - Modal con todos los campos
  - **Actualizar Actual Duration** al completar

- [ ] **Completar tarea**
  - Checkbox → marca como "Completed"
  - Animación de celebración (confetti leve)
  - Registra completedAt timestamp

- [ ] **Eliminar tarea**
  - Confirmación simple
  - Hard delete (MVP)

#### 2.2 Estados de Tarea
- [ ] **Pending** (pendiente) - default
- [ ] **In Progress** (en progreso)
- [ ] **Completed** (completada)
- [ ] **Archived** (archivada)

#### 2.3 Campos ADHD-Friendly
- [ ] **estimatedDuration** - Cuánto crees que tomará (minutos)
- [ ] **actualDuration** - Cuánto realmente tomó
- [ ] **parentTaskId** - Soporte para subtareas
- [ ] **order** - Ordenamiento manual de tareas
- [ ] **isRecurring** - Tareas recurrentes (Premium)
- [ ] **recurrencePattern** - RRULE format (Premium)

#### 2.4 Límites por Plan
- [ ] **FREE:** Máximo 15 tareas activas (pending + in_progress)
- [ ] **PREMIUM:** Ilimitado
- [ ] Tareas completadas NO cuentan para el límite

---

### 3. Tags (Etiquetas) - Reemplazo de Boards

**Decisión de diseño:** Tags flexibles en lugar de boards rígidos

#### 3.1 CRUD Tags
- [ ] **Crear tag**
  - Nombre (obligatorio, max 30 chars)
  - Color (selector hex, default: #3b82f6)

- [ ] **Ver tags**
  - Lista en sidebar
  - Contador de tareas por tag

- [ ] **Editar tag**
  - Cambiar nombre y color

- [ ] **Eliminar tag**
  - Confirmación: "¿Eliminar tag X? Se desasignará de Y tareas"
  - Las tareas NO se eliminan, solo pierden el tag

#### 3.2 Asignación de Tags a Tareas
- [ ] Relación many-to-many (tabla `task_tags`)
- [ ] Una tarea puede tener múltiples tags
- [ ] Select multi en formulario de tarea
- [ ] Filtrar tareas por tag en vista principal

#### 3.3 Tags Sugeridos (onboarding)
- 🏢 Trabajo
- 🏠 Personal
- 💪 Salud
- 📚 Estudio
- 💰 Finanzas

---

### 4. Recordatorios (Reminders)

#### 4.1 Sistema de Recordatorios
- [ ] **Crear recordatorio** para una tarea
  - sendAt (timestamp cuando enviar)
  - channel (email, web_push, whatsapp, sms)
  - Enviado automáticamente por cron job

- [ ] **Múltiples recordatorios** por tarea
  - Ejemplo: 1 día antes + 1 hora antes

- [ ] **Estados:**
  - sent: false (pendiente)
  - sent: true (enviado)

#### 4.2 Canales de Alerta (MVP)
- [ ] **Email** (Resend)
  - Template limpio, branded
  - CTA: "Ver tarea" → link directo

- [ ] **Web Push Notifications** (Fase 2)
  - Solo si usuario tiene sesión abierta

**Out of scope MVP:**
- ❌ WhatsApp (Fase 2)
- ❌ SMS (Fase 2)

#### 4.3 Preferencias de Notificación
- [ ] Toggle: Email notifications (users.emailNotifications)
- [ ] Toggle: Web push (users.webPushNotifications)
- [ ] Daily Summary (users.dailySummary)
- [ ] Daily Summary Time (users.dailySummaryTime)

#### 4.4 Límites por Plan
- [ ] **FREE:** Sin límite en recordatorios (MVP)
- [ ] **PREMIUM:** Recordatorios recurrentes

---

### 5. Calendario

#### 5.1 Vista de Calendario
- [ ] Vista mensual (mes actual)
- [ ] Navegación mes anterior/siguiente
- [ ] Tareas mostradas en día correspondiente
- [ ] Color-coded por tag principal
- [ ] Click en día → crear tarea con fecha pre-filled
- [ ] Click en tarea → editar tarea
- [ ] Indicador de "hoy"

#### 5.2 Responsive
- [ ] Desktop: Grilla completa 7x5
- [ ] Mobile: Vista compacta, scroll horizontal

---

### 6. Dashboard Principal

#### 6.1 Layout
**Sidebar:**
- [ ] Logo Flime
- [ ] Búsqueda de tareas
- [ ] "Hoy"
- [ ] "Próximas 7 días"
- [ ] Lista de tags
- [ ] "+ Nuevo tag"
- [ ] "Configuración"
- [ ] "Upgrade to Premium" (si FREE)

**Header:**
- [ ] Saludo: "Buenos días, [Nombre]"
- [ ] Avatar (dropdown: Perfil, Logout)

**Main:**
- [ ] Quick stats:
  - Tareas pendientes hoy
  - Tareas completadas semana
  - Tiempo estimado vs real (ADHD insight)
- [ ] Tabs: Lista / Calendario

**FAB:**
- [ ] Botón "+" → Nueva tarea

#### 6.2 Búsqueda
- [ ] Input en sidebar
- [ ] Buscar por título
- [ ] Resultados en tiempo real (debounce 300ms)

---

### 7. Freemium & Planes

#### 7.1 Plan FREE
**Límites:**
- [ ] 15 tareas activas
- [ ] Sin recordatorios recurrentes
- [ ] Sin temas (solo light mode)
- [ ] Sin exportar datos

#### 7.2 Plan PREMIUM
**Pricing:**
- $5 USD/mes
- $50 USD/año (17% descuento)

**Beneficios:**
- Tareas ilimitadas
- Tags ilimitados
- Recordatorios ilimitados
- Recordatorios recurrentes
- Subtareas (parentTaskId)
- Exportar datos (CSV/JSON)

---

### 8. Pagos (Mercado Pago)

#### 8.1 Integración
- [ ] SDK Mercado Pago
- [ ] Crear preferencia de pago
- [ ] Webhooks
- [ ] Actualizar users.plan
- [ ] Crear registro en `payments`
- [ ] Crear registro en `subscriptions`

#### 8.2 Gestión
- [ ] Página "Mi Plan":
  - Plan actual (users.plan)
  - Precio
  - Próxima renovación (subscriptions.currentPeriodEnd)
- [ ] Cancelar suscripción

#### 8.3 Historial
- [ ] Lista de pagos (tabla `payments`)
- [ ] NO facturas formales (Fase 2)

---

### 9. Configuración

#### 9.1 Perfil
- [ ] Editar nombre
- [ ] Zona horaria
- [ ] Idioma (es/en)

#### 9.2 Preferencias
- [ ] Email notifications
- [ ] Web push
- [ ] Daily summary
- [ ] Week starts on (domingo/lunes)

#### 9.3 Cuenta
- [ ] Ver plan y upgrade
- [ ] Historial de pagos
- [ ] Exportar datos (Premium)
- [ ] Eliminar cuenta

---

### 10. Responsive & Accesibilidad

- [ ] Mobile-first
- [ ] Breakpoints: 640/768/1024px
- [ ] WCAG AA compliance
- [ ] Keyboard navigation

---

### 11. Performance

- [ ] LCP < 2.5s
- [ ] CLS < 0.1
- [ ] Lighthouse > 90
- [ ] Image optimization
- [ ] Code splitting
- [ ] TanStack Query caching

---

### 12. SEO & Marketing

- [ ] Landing page
- [ ] Meta tags optimizados
- [ ] Open Graph
- [ ] Favicon

---

### 13. Legal

- [ ] Términos de Servicio
- [ ] Política de Privacidad
- [ ] Cookie consent

---

### 14. Testing

- [ ] Manual testing
- [ ] Cross-browser
- [ ] Mobile testing
- [ ] 10 usuarios beta

---

### 15. Deployment ✅

#### Sprint 1 Completado:
- [x] Vercel deployment
- [x] CI/CD (GitHub Actions)
- [x] Sentry monitoring
- [x] Database schema (7 tablas)
- [x] Row Level Security

---

## ❌ OUT OF SCOPE

### Cambios vs Plan Original
- ❌ **Boards** → ✅ Tags flexibles
- ❌ **Notifications log** → reminders.sent
- ❌ **Invoices table** → payments es suficiente

### Fase 2
- ❌ Google/MS Calendar
- ❌ WhatsApp/SMS
- ❌ Web push
- ❌ Dark mode
- ❌ Plan TEAM
- ❌ Vista Kanban
- ❌ Templates
- ❌ App móvil

---

## 📊 Success Metrics

| Métrica | Target (3 meses) |
|---------|------------------|
| Usuarios registrados | 100 |
| Usuarios activos | 60 |
| Usuarios premium | 10 |
| MRR | $50 USD |
| Retention 30d | 30% |
| NPS | 40+ |

---

## 📅 Timeline

| Sprint | Semanas | Foco | Status |
|--------|---------|------|--------|
| 1 | 1 | Setup & Foundation | ✅ Completado |
| 2 | 2 | Auth & User Management | 🔄 Siguiente |
| 3-4 | 3-4 | Tasks & Tags CRUD | 📋 Planificado |
| 5-6 | 5-6 | Calendar & Reminders | 📋 Planificado |
| 7-8 | 7-8 | Freemium & Payments | 📋 Planificado |
| 9-10 | 9-10 | Polish + Launch | 📋 Planificado |

---

## 🛠️ Database Schema

**7 tablas (Sprint 1 ✅):**
1. users - Usuarios con preferencias
2. tasks - Tareas ADHD-friendly
3. reminders - Recordatorios
4. tags - Etiquetas
5. task_tags - Many-to-many
6. subscriptions - Suscripciones
7. payments - Historial

Ver [database-schema.md](../architecture/database-schema.md)

---

## ✅ Definition of Done

1. ✅ Funcionalidad completa
2. ✅ Lighthouse > 85
3. ✅ Cross-browser tested
4. ✅ 10 usuarios beta
5. ✅ Legal docs publicados
6. ✅ Deploy estable

---

**Aprobado por:** Daniel Pérez
**Sprint 1:** ✅ Diciembre 31, 2024
**Target launch:** Marzo 15, 2025

---

## Notas Finales

**Cambio importante:**
- ❌ NO boards → ✅ **Tags flexibles** (mejor para ADHD)
- ✅ Campos ADHD-friendly (estimatedDuration, actualDuration)
- ✅ Soporte subtareas (parentTaskId)

**Mantra:** "Lanza antes de estar listo. Itera basado en feedback."
