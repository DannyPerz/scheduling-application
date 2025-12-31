# Flime.ai - Product Requirements Document (PRD)

**Versión:** 1.0
**Fecha:** Diciembre 30, 2024
**Owner:** Fundador
**Status:** Draft → En revisión

---

## 📋 Executive Summary

**Flime.ai** es una aplicación web SaaS de gestión de tareas y recordatorios diseñada específicamente para personas con TDA/TDAH y aquellos que buscan construir disciplina a través de organización simple y recordatorios multi-canal.

### Problema
Las personas con TDA/TDAH y aquellos con múltiples responsabilidades olvidan tareas importantes, citas y compromisos porque:
- Las apps existentes son demasiado complejas
- Los recordatorios son fáciles de ignorar
- No hay separación clara entre áreas de vida (trabajo, personal, educación)
- No hay sincronización confiable con calendarios externos

### Solución
Una aplicación web simple que permite:
- Crear tableros personalizables por área de vida
- Configurar tareas con recordatorios agresivos
- Recibir alertas por múltiples canales (email, notificaciones web, WhatsApp, SMS)
- Sincronizar bidireccionalmente con Google y Microsoft Calendar
- Precio accesible para mercado LATAM

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
- **Frustración:** Olvida reuniones, deadlines, llamadas importantes
- **Comportamiento:** Tiene 5 apps instaladas, usa ninguna consistentemente
- **Necesidad:** Recordatorios imposibles de ignorar
- **Quote:** "Necesito que algo me obligue a hacer las cosas, mi cerebro no es confiable"

### Persona 2: Ana (Secundaria)
- **Demografía:** 34 años, madre + freelancer diseñadora
- **Frustración:** Demasiadas cosas en la cabeza, nada escrito
- **Comportamiento:** Post-its por toda la casa, se pierden
- **Necesidad:** Externalizar su mente, organizar por contextos
- **Quote:** "Necesito sacar todo de mi cabeza a algo que me recuerde"

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
- 2 tableros máximo
- 15 tareas activas simultáneas
- 1 alerta por email al día
- Vista de calendario básica
- Sin integraciones externas
- Sin temas personalizados

**CTA para Premium:** "Desbloquea tableros y tareas ilimitadas"

#### PREMIUM Plan - $5 USD/mes o $50 USD/año
**Objetivo:** Usuarios comprometidos que necesitan sin límites

**Incluye:**
- ✅ Tableros ilimitados
- ✅ Tareas ilimitadas
- ✅ Alertas ilimitadas (email + web push)
- ✅ Recordatorios recurrentes
- ✅ Sincronización Google/Microsoft Calendar (Fase 2)
- ✅ Vista de analytics/productividad
- ✅ Temas personalizados
- ✅ Soporte prioritario (email)
- ✅ Exportar datos (CSV/JSON)

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

#### 1.1 Registro/Login
- **Descripción:** Sistema de autenticación seguro
- **Métodos:**
  - Email + Password (validación con código de verificación)
  - Google OAuth
  - (Futuro: Microsoft OAuth)
- **Flujo:**
  1. Usuario ingresa email
  2. Recibe código de verificación (Resend)
  3. Crea password
  4. Redirige a onboarding

#### 1.2 Onboarding (Primera vez)
- **Objetivo:** Educar y configurar perfil
- **Pasos:**
  1. **Bienvenida:** "¡Hola! Vamos a organizar tu vida en 3 pasos"
  2. **Pregunta TDA:** "¿Tienes TDA/TDAH?" (opcional, para personalización)
  3. **Crear primer tablero:** "¿Qué quieres organizar primero?" (Trabajo, Personal, Estudios)
  4. **Crear primera tarea:** "Agreguemos tu primera tarea"
  5. **Configurar recordatorio:** "¿Cómo quieres que te recordemos?"
  6. **Dashboard:** Redirige a vista principal

---

### 2. Tableros (Boards)

#### 2.1 CRUD Tableros
- **Crear tablero:**
  - Nombre (ej: "Trabajo", "Personal", "Gym")
  - Color/icono (6 opciones predefinidas)
  - Descripción (opcional)

- **Ver tableros:**
  - Vista de cards en grid
  - Sidebar con lista de tableros

- **Editar tablero:**
  - Cambiar nombre, color, icono

- **Eliminar tablero:**
  - Confirmación: "¿Seguro? Esto eliminará X tareas"
  - Soft delete (30 días de recuperación para Premium)

#### 2.2 Límites por Plan
- **FREE:** 2 tableros máximo
- **PREMIUM:** Ilimitado
- **TEAM:** Ilimitado + compartidos

---

### 3. Tareas (Tasks)

#### 3.1 Crear Tarea
**Campos obligatorios:**
- Título (max 200 caracteres)
- Tablero asignado

**Campos opcionales:**
- Descripción (rich text básico)
- Fecha de vencimiento (date picker)
- Hora específica (time picker)
- Prioridad (Alta, Media, Baja) - visual con colores
- Recurrencia (diaria, semanal, mensual) - Premium only
- Etiquetas/tags (max 5)

**Estados:**
- Todo (pendiente)
- In Progress (en progreso) - opcional
- Done (completada)
- Archived (archivada)

#### 3.2 Vista de Tareas

**Vistas disponibles:**
1. **Lista** (default)
   - Agrupadas por tablero
   - Filtros: Estado, Prioridad, Fecha
   - Ordenar: Fecha, Prioridad, Alfabético

2. **Calendario** (Premium)
   - Vista mensual
   - Tareas por día
   - Drag & drop para cambiar fechas

3. **Kanban** (Fase 2)
   - Columnas por estado
   - Drag & drop entre columnas

#### 3.3 Completar/Editar/Eliminar
- **Completar:** Checkbox → marca como Done
- **Editar:** Modal con todos los campos
- **Eliminar:** Confirmación simple
- **Archivar:** Oculta sin eliminar (recuperable)

#### 3.4 Límites por Plan
- **FREE:** 15 tareas activas
- **PREMIUM:** Ilimitado

---

### 4. Recordatorios & Alertas

#### 4.1 Configuración de Recordatorios

**Por tarea:**
- Sin recordatorio
- 15 minutos antes
- 30 minutos antes
- 1 hora antes
- 1 día antes
- Personalizado (Premium)

**Recordatorios recurrentes (Premium):**
- Diario a las X hora
- Semanal (elegir días)
- Mensual (día específico)

#### 4.2 Canales de Alerta

**MVP v1.0:**
- ✅ **Email** (Resend)
  - Template limpio, branded
  - CTA: "Ver tarea" → link directo a la app

- ✅ **Web Push Notifications**
  - Solo si usuario tiene sesión abierta
  - Navegador nativo (Chrome, Firefox, Safari)

**Fase 2:**
- 📱 **WhatsApp** (Meta Business API)
  - Costo: ~$0.005 USD por mensaje
  - Solo para Premium

- 📱 **SMS** (Twilio)
  - Costo: ~$0.05 USD por mensaje
  - Solo para Premium, pack de créditos

#### 4.3 Centro de Notificaciones
- Inbox dentro de la app
- Muestra últimas 50 alertas
- Marca como leída
- Histórico (Premium)

#### 4.4 Límites por Plan
- **FREE:** 1 email alert por día
- **PREMIUM:** Ilimitado

---

### 5. Calendario

#### 5.1 Vista de Calendario (MVP)
- Vista mensual (mes actual)
- Tareas mostradas por día
- Color-coded por tablero
- Click en día → crear tarea nueva
- Click en tarea → abrir modal de edición

#### 5.2 Integraciones (Fase 2)
**Google Calendar:**
- OAuth 2.0
- Sincronización bidireccional
  - Tareas de Flime → Eventos en Google
  - Eventos de Google → Tareas en Flime (opcional)
- Configuración: elegir calendarios a sincronizar

**Microsoft Calendar:**
- OAuth 2.0 (Microsoft Graph API)
- Misma lógica que Google

---

### 6. Dashboard Principal

#### 6.1 Layout
**Sidebar izquierdo:**
- Logo Flime
- Búsqueda de tareas
- "Hoy" (tareas de hoy)
- "Próximas" (7 días)
- Lista de tableros
- "Configuración"
- "Upgrade to Premium" (si FREE)

**Área principal:**
- Header: "Buenos días, [Nombre]"
- Quick stats:
  - X tareas pendientes hoy
  - X tareas completadas esta semana
  - Racha de días activos (Premium)

- Vista de tareas (lista/calendario)

**Quick actions (FAB):**
- + Nueva tarea (botón flotante)

#### 6.2 Búsqueda
- Buscar por título
- Buscar por descripción
- Filtrar por tablero
- Filtrar por etiquetas

---

### 7. Configuración & Perfil

#### 7.1 Perfil de Usuario
- Nombre completo
- Email (no editable)
- Avatar (upload imagen o iniciales)
- Zona horaria (auto-detectada)
- Idioma (Español/Inglés)

#### 7.2 Preferencias
- Notificaciones:
  - Email on/off
  - Web push on/off
  - Resumen diario (email a las 8am)

- Tema:
  - Light mode
  - Dark mode (Premium)
  - Auto (sistema)

- Primer día de la semana (Domingo/Lunes)

#### 7.3 Cuenta & Facturación
- Plan actual (FREE/PREMIUM)
- Botón "Upgrade to Premium"
- Historial de pagos (Premium)
- Método de pago (Mercado Pago)
- Cancelar suscripción

#### 7.4 Exportar Datos
- Exportar todas las tareas (CSV)
- Exportar todas las tareas (JSON)
- GDPR compliance

---

## 🚫 Out of Scope (Fase 2+)

**No incluir en MVP:**
- ❌ App móvil nativa (iOS/Android)
- ❌ Integraciones Google/Microsoft Calendar
- ❌ WhatsApp/SMS notifications
- ❌ Plan Team (workspaces compartidos)
- ❌ Comentarios en tareas
- ❌ Adjuntar archivos a tareas
- ❌ Subtareas
- ❌ Vista Kanban
- ❌ Templates de tareas
- ❌ Reportes avanzados/analytics
- ❌ API pública
- ❌ Integraciones con Slack, Telegram, etc.
- ❌ Dark mode (será Premium post-MVP)

---

## 🎨 UI/UX Requirements

### Design Principles
1. **Simplicidad extrema** - Menos es más
2. **Sin distracciones** - Colores calmantes
3. **Accesibilidad** - WCAG AA compliance
4. **Responsive** - Mobile-first approach
5. **Fast feedback** - Animaciones < 200ms

### Visual Style
- **Tipografía:** Inter o Geist (sans-serif moderna)
- **Colores primarios:**
  - Azul: #3B82F6 (acción, confianza)
  - Verde: #10B981 (éxito, completado)
  - Amarillo: #F59E0B (advertencia, alta prioridad)
  - Rojo: #EF4444 (urgente, eliminar)

- **Espaciado:** Sistema de 8px
- **Bordes:** Border radius 8px (cards), 6px (buttons)
- **Sombras:** Sutiles, elevation system

### Componentes (shadcn/ui)
- Buttons
- Input fields
- Date picker
- Time picker
- Modals/Dialogs
- Toast notifications
- Dropdown menus
- Tabs
- Cards
- Badges

---

## 🔐 Security & Privacy

### Autenticación
- Passwords hasheados con bcrypt
- JWT tokens (HttpOnly cookies)
- Refresh token rotation
- 2FA (Fase 2)

### Autorización
- Row Level Security (Supabase RLS)
- Users solo ven sus propios datos
- API rate limiting

### Privacy
- GDPR compliant
- Política de privacidad clara
- Exportar datos on-demand
- Eliminar cuenta (hard delete después de 30 días)
- No vendemos datos a terceros

### Backups
- Daily automated backups (Supabase)
- Point-in-time recovery (7 días)

---

## 📊 Analytics & Tracking

### Product Analytics (PostHog o Mixpanel)
**Eventos a trackear:**
- User signup
- User login
- Task created
- Task completed
- Task deleted
- Board created
- Notification sent
- Upgrade to Premium
- Churn (downgrade/cancel)

**Métricas:**
- DAU/MAU ratio
- Task completion rate
- Average tasks per user
- Average boards per user
- Time to first task
- Retention cohorts

### Business Analytics
- MRR tracking
- Churn rate
- LTV (Lifetime Value)
- CAC (Customer Acquisition Cost)
- Conversion FREE → PREMIUM

---

## 🧪 Testing Strategy

### Unit Tests
- Funciones críticas (auth, CRUD)
- Validaciones (Zod schemas)
- Coverage mínimo: 60%

### Integration Tests
- Flujos completos (signup → create task → complete)
- API endpoints
- Database operations

### E2E Tests (Playwright - Fase 2)
- Happy paths principales
- Signup/login
- Create/complete task
- Upgrade to Premium

### Manual Testing
- Cross-browser (Chrome, Firefox, Safari, Edge)
- Mobile responsive (iOS Safari, Chrome Android)
- Accessibility (screen readers)

---

## 🚀 Launch Plan

### Pre-Launch (2 semanas antes)
- [ ] Beta testing con 10 usuarios cercanos
- [ ] Fix bugs críticos
- [ ] Setup analytics
- [ ] Setup Mercado Pago producción
- [ ] Escribir términos de servicio + privacidad
- [ ] Crear landing page básica
- [ ] Setup dominio flime.ai

### Launch Day
- [ ] Deploy a producción
- [ ] Post en redes sociales personales
- [ ] Post en Reddit (r/ADHD, r/productivity)
- [ ] Post en Product Hunt (opcional)
- [ ] Email a lista de espera (si hay)

### Post-Launch (primera semana)
- [ ] Monitorear errores (Sentry)
- [ ] Responder feedback usuarios
- [ ] Iterar rápido en bugs
- [ ] Analizar métricas de uso

---

## 📝 Technical Requirements

Ver [docs/architecture/tech-stack.md](../architecture/tech-stack.md) para detalles completos.

**Stack:**
- Frontend: Next.js 15 + TypeScript + Tailwind CSS v4
- Backend: Next.js API Routes + Supabase
- Database: PostgreSQL (Supabase)
- ORM: Drizzle
- Auth: Supabase Auth
- Email: Resend
- Payments: Mercado Pago
- Deploy: Vercel + Supabase Cloud

**Performance Requirements:**
- First Contentful Paint < 1.5s
- Time to Interactive < 3s
- Lighthouse score > 90
- Core Web Vitals: Green

---

## 🗓️ Timeline & Milestones

Ver [planning/roadmap/2026-q1.md](../../planning/roadmap/2026-q1.md)

**Resumen:**
- **Sprint 1-2 (Sem 1-2):** Setup + Auth
- **Sprint 3-4 (Sem 3-4):** Core features (Boards + Tasks)
- **Sprint 5-6 (Sem 5-6):** Alerts + Dashboard
- **Sprint 7-8 (Sem 7-8):** Freemium + Payments
- **Sprint 9-10 (Sem 9-10):** Polish + Launch

**Total: 10 semanas (2.5 meses)**

---

## 📞 Support & Feedback

### Soporte MVP
- Email: support@flime.ai
- Respuesta en 24-48h (FREE)
- Respuesta en 12h (PREMIUM)

### Feedback Collection
- In-app feedback widget
- NPS survey (mensual)
- Feature requests (Canny o similar)

---

## ✅ Definition of Done (MVP)

El MVP está completo cuando:
- ✅ Usuario puede registrarse con email o Google
- ✅ Usuario puede crear tableros y tareas
- ✅ Usuario puede ver calendario con sus tareas
- ✅ Usuario recibe emails de recordatorio
- ✅ Usuario puede actualizar a Premium y pagar
- ✅ Plan FREE tiene límites aplicados correctamente
- ✅ App funciona en mobile (responsive)
- ✅ No hay errores críticos
- ✅ Lighthouse score > 85
- ✅ 10 usuarios beta completaron onboarding sin ayuda

---

**Aprobado por:** [Pendiente]
**Fecha de aprobación:** [Pendiente]

---

## Change Log

| Versión | Fecha | Cambios |
|---------|-------|---------|
| 1.0 | 2024-12-30 | Versión inicial |
