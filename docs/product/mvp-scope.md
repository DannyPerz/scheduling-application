# Flime.ai - MVP Scope Definition

**Versión:** 1.0
**Fecha:** Diciembre 30, 2024
**Timeline:** 10 semanas (14 horas/semana = 140 horas totales)

---

## 🎯 MVP Objective

**Lanzar una versión funcional de Flime.ai que permita a usuarios:**
1. Organizarse con tableros y tareas
2. Recibir recordatorios confiables por email
3. Pagar por una suscripción Premium si les gusta el producto

**Criterio de éxito:** 10 usuarios beta completan onboarding y crean al menos 5 tareas sin asistencia.

---

## ✅ IN SCOPE (Must Have)

### 1. Autenticación & Usuarios

#### 1.1 Registro/Login
- [x] Registro con email + password
- [x] Login con email + password
- [x] Verificación de email (código 6 dígitos via Resend)
- [x] OAuth con Google
- [x] Logout
- [x] Password reset (forgot password flow)

#### 1.2 Perfil de Usuario
- [x] Nombre completo
- [x] Email (read-only)
- [x] Avatar (iniciales generadas automáticamente)
- [x] Zona horaria (auto-detectada, editable)
- [x] Idioma (Español por default, opción Inglés)

#### 1.3 Onboarding (Primera vez)
- [x] Wizard de 4 pasos:
  1. Bienvenida
  2. Crear primer tablero
  3. Crear primera tarea
  4. Configurar preferencias de notificación
- [x] Skip option (ir directo al dashboard)

---

### 2. Tableros (Boards)

#### 2.1 CRUD Tableros
- [x] **Crear tablero**
  - Nombre (obligatorio, max 50 chars)
  - Color (6 opciones predefinidas)
  - Icono (12 opciones: trabajo, personal, salud, finanzas, etc.)
  - Descripción (opcional, max 200 chars)

- [x] **Ver tableros**
  - Lista en sidebar
  - Grid de cards en vista principal
  - Contador de tareas por tablero

- [x] **Editar tablero**
  - Cambiar nombre, color, icono, descripción

- [x] **Eliminar tablero**
  - Confirmación: "¿Eliminar tablero X con Y tareas?"
  - Hard delete (MVP - no hay recuperación)

#### 2.2 Límites por Plan
- [x] **FREE:** Máximo 2 tableros
- [x] **PREMIUM:** Ilimitado
- [x] Mensaje cuando se alcanza el límite: "Upgrade para crear más tableros"

---

### 3. Tareas (Tasks)

#### 3.1 CRUD Tareas
- [x] **Crear tarea**
  - Título (obligatorio, max 200 chars)
  - Descripción (opcional, textarea simple, max 1000 chars)
  - Tablero (select, obligatorio)
  - Fecha de vencimiento (date picker, opcional)
  - Hora (time picker, opcional)
  - Prioridad (Alta/Media/Baja, default: Media)
  - Recordatorio (select: Sin recordatorio, 15min, 30min, 1h, 1 día antes)

- [x] **Ver tareas**
  - Vista de lista agrupada por tablero
  - Vista de calendario (mensual)
  - Filtros: Estado, Prioridad, Tablero
  - Ordenar: Fecha, Prioridad, Alfabético

- [x] **Editar tarea**
  - Modal con todos los campos
  - Guardar cambios

- [x] **Completar tarea**
  - Checkbox → marca como "Done"
  - Animación de celebración (confetti leve)
  - Mueve a sección "Completadas"

- [x] **Eliminar tarea**
  - Confirmación simple
  - Hard delete (MVP)

#### 3.2 Estados de Tarea
- [x] Todo (pendiente) - default
- [x] Done (completada)

**Out of scope MVP:**
- ❌ In Progress (Fase 2)
- ❌ Archived (Fase 2)

#### 3.3 Límites por Plan
- [x] **FREE:** Máximo 15 tareas activas (estado: Todo)
- [x] **PREMIUM:** Ilimitado
- [x] Tareas completadas NO cuentan para el límite

---

### 4. Calendario

#### 4.1 Vista de Calendario
- [x] Vista mensual (mes actual)
- [x] Navegación mes anterior/siguiente
- [x] Tareas mostradas en día correspondiente
- [x] Color-coded por tablero
- [x] Click en día → abrir modal "Crear tarea" con fecha pre-filled
- [x] Click en tarea → abrir modal "Editar tarea"
- [x] Indicador de "hoy"

#### 4.2 Responsive
- [x] Desktop: Grilla completa 7x5
- [x] Mobile: Vista compacta, scroll horizontal

---

### 5. Recordatorios & Notificaciones

#### 5.1 Email Notifications (Resend)
- [x] Enviar email X minutos/horas antes de la tarea
- [x] Template HTML branded (logo Flime, colores)
- [x] Contenido:
  - Título de la tarea
  - Descripción (si hay)
  - Fecha/hora de vencimiento
  - Tablero
  - CTA: "Ver tarea" (link directo a la app)
- [x] Footer: Unsubscribe link + "Flime.ai"

#### 5.2 Web Push Notifications
- [x] Solicitar permiso en onboarding
- [x] Notificación nativa del navegador
- [x] Solo si usuario tiene sesión activa
- [x] Click → abre la app en la tarea

#### 5.3 Preferencias de Notificación
- [x] **Settings página:**
  - Toggle: Email notifications (on/off)
  - Toggle: Web push notifications (on/off)
  - Resumen diario: Email a las 8am con tareas del día (on/off)

#### 5.4 Límites por Plan
- [x] **FREE:** Máximo 1 email alert por día (prioriza por urgencia)
- [x] **PREMIUM:** Ilimitado

---

### 6. Dashboard Principal

#### 6.1 Layout
**Sidebar (desktop) / Bottom nav (mobile):**
- [x] Logo Flime + versión
- [x] Búsqueda de tareas (icono lupa)
- [x] "Hoy" - tareas con vencimiento hoy
- [x] "Próximas 7 días"
- [x] Divider
- [x] Lista de tableros (click → filtra tareas)
- [x] Divider
- [x] "Configuración" (icono engranaje)
- [x] "Upgrade to Premium" (si FREE plan) - destacado

**Header:**
- [x] Saludo personalizado: "Buenos días, [Nombre]"
- [x] Avatar del usuario (top right)
- [x] Dropdown: Perfil, Configuración, Logout

**Main Area:**
- [x] Quick stats (cards):
  - Tareas pendientes hoy
  - Tareas completadas esta semana
- [x] Vista de tareas (lista por default)
- [x] Tabs: Lista / Calendario

**Floating Action Button (FAB):**
- [x] Botón "+" flotante (bottom right)
- [x] Click → Modal "Nueva tarea"

#### 6.2 Búsqueda
- [x] Input en sidebar
- [x] Buscar por título de tarea
- [x] Resultados en tiempo real (debounce 300ms)
- [x] Mostrar tablero de cada resultado

---

### 7. Freemium & Planes

#### 7.1 Plan FREE (Default)
**Límites aplicados:**
- [x] 2 tableros máximo
- [x] 15 tareas activas
- [x] 1 email notification por día
- [x] Sin temas (solo light mode)
- [x] Sin exportar datos

**Banners de Upgrade:**
- [x] Banner top en dashboard (dismissable)
- [x] Modal al alcanzar límite: "Upgrade para desbloquear"

#### 7.2 Plan PREMIUM
**Pricing:**
- [x] $5 USD/mes
- [x] $50 USD/año (17% descuento)

**Beneficios:**
- [x] Tableros ilimitados
- [x] Tareas ilimitadas
- [x] Notificaciones ilimitadas
- [x] Recordatorios recurrentes (diario, semanal, mensual)
- [x] Exportar datos (CSV/JSON)
- [x] Soporte prioritario

#### 7.3 Página de Pricing
- [x] Comparación FREE vs PREMIUM (tabla)
- [x] FAQs
- [x] CTA: "Empezar gratis" / "Upgrade ahora"

---

### 8. Pagos (Mercado Pago)

#### 8.1 Integración Mercado Pago
- [x] SDK de Mercado Pago
- [x] Crear preferencia de pago
- [x] Checkout modal o redirect
- [x] Webhooks para confirmar pago
- [x] Actualizar plan del usuario (FREE → PREMIUM)

#### 8.2 Gestión de Suscripción
- [x] Página "Mi Plan" en settings:
  - Plan actual
  - Precio
  - Próxima fecha de renovación (si anual)
  - Método de pago
- [x] Botón "Cancelar suscripción"
  - Confirmación: "¿Seguro? Volverás a FREE"
  - No hay reembolsos (política clara)
- [x] Downgrade: Suscripción activa hasta fin del periodo pagado

#### 8.3 Facturas
- [x] Email con recibo de pago (Resend)
- [x] Historial de pagos en settings (lista simple)

---

### 9. Configuración & Settings

#### 9.1 Perfil
- [x] Editar nombre
- [x] Cambiar avatar (iniciales, no upload de imagen en MVP)
- [x] Zona horaria
- [x] Idioma (Español/Inglés)

#### 9.2 Preferencias
- [x] Notificaciones (ver sección 5.3)
- [x] Primer día de la semana (Domingo/Lunes)
- [x] Tema: Solo Light mode en MVP (Dark mode → Premium Fase 2)

#### 9.3 Cuenta
- [x] Plan actual y upgrade
- [x] Método de pago
- [x] Historial de facturación
- [x] Exportar datos (Premium only)
  - Botón "Exportar todas las tareas (CSV)"
  - Botón "Exportar todas las tareas (JSON)"
- [x] Eliminar cuenta
  - Confirmación seria: "Esto es permanente"
  - Hard delete after 7 días (período de gracia)

---

### 10. Responsive & Accesibilidad

#### 10.1 Responsive Design
- [x] Mobile-first approach
- [x] Breakpoints: 640px (sm), 768px (md), 1024px (lg)
- [x] Sidebar → Bottom navigation en mobile
- [x] Calendario adaptado a pantalla pequeña

#### 10.2 Accesibilidad
- [x] WCAG AA compliance
- [x] Keyboard navigation
- [x] Focus states visibles
- [x] Alt text en imágenes
- [x] Labels en inputs
- [x] ARIA attributes donde necesario

---

### 11. Performance

#### 11.1 Métricas Objetivo
- [x] First Contentful Paint (FCP) < 1.5s
- [x] Largest Contentful Paint (LCP) < 2.5s
- [x] Time to Interactive (TTI) < 3s
- [x] Cumulative Layout Shift (CLS) < 0.1
- [x] Lighthouse Performance Score > 90

#### 11.2 Optimizaciones
- [x] Next.js Image optimization
- [x] Code splitting automático
- [x] Lazy loading de modales
- [x] TanStack Query para caching
- [x] Debounce en búsquedas

---

### 12. SEO & Marketing

#### 12.1 Landing Page (Temporal con Next.js)
- [x] Hero section: "Tu cerebro externo. Tu disciplina digital."
- [x] Features (3 cards)
- [x] Pricing simple
- [x] CTA: "Empezar gratis"
- [x] Footer: Links legales

**Nota:** En Fase 2 se migra a Astro.js

#### 12.2 Meta Tags
- [x] Title, description optimizados
- [x] Open Graph tags (Facebook/LinkedIn)
- [x] Twitter Card
- [x] Favicon

---

### 13. Legal & Compliance

#### 13.1 Documentos Legales
- [x] Términos de Servicio (simple, template adaptado)
- [x] Política de Privacidad (GDPR friendly)
- [x] Política de Cookies

#### 13.2 GDPR Compliance
- [x] Cookie consent banner
- [x] Derecho a exportar datos
- [x] Derecho a eliminar cuenta
- [x] No vender datos a terceros (declarar)

---

### 14. Testing & QA

#### 14.1 Testing Mínimo
- [x] Unit tests: Auth flows, CRUD operations
- [x] Manual testing: Signup → Create task → Complete → Upgrade
- [x] Cross-browser: Chrome, Firefox, Safari, Edge
- [x] Mobile testing: iOS Safari, Chrome Android
- [x] Accessibility audit (Lighthouse)

#### 14.2 Beta Testing
- [x] 10 usuarios beta (amigos, familia, comunidad TDA)
- [x] Formulario de feedback
- [x] Iterar bugs críticos antes de launch público

---

### 15. Deployment & DevOps

#### 15.1 Environments
- [x] **Development:** localhost
- [x] **Staging:** Vercel preview (cada PR)
- [x] **Production:** Vercel producción (rama main)

#### 15.2 CI/CD
- [x] GitHub Actions:
  - Lint + Type check en cada commit
  - Tests automáticos
  - Deploy automático a Vercel

#### 15.3 Monitoring
- [x] Error tracking: Sentry (free tier)
- [x] Analytics: PostHog (free tier) o Vercel Analytics
- [x] Uptime monitoring: UptimeRobot (free tier)

---

## ❌ OUT OF SCOPE (Explícitamente NO en MVP)

### Fase 2 (Post-MVP)
- ❌ Integración Google Calendar
- ❌ Integración Microsoft Calendar
- ❌ WhatsApp notifications
- ❌ SMS notifications
- ❌ Recordatorios recurrentes avanzados (FREE plan)
- ❌ Dark mode
- ❌ Plan TEAM (workspaces compartidos)
- ❌ Asignación de tareas a otros usuarios
- ❌ Comentarios en tareas
- ❌ Adjuntar archivos a tareas
- ❌ Subtareas
- ❌ Vista Kanban
- ❌ Templates de tareas
- ❌ Etiquetas/tags personalizables
- ❌ Filtros avanzados
- ❌ Reportes de productividad
- ❌ Analytics de uso detallado para usuarios
- ❌ App móvil nativa (iOS/Android)
- ❌ Desktop app (Electron)
- ❌ API pública
- ❌ Webhooks
- ❌ Integraciones con Zapier, Make, etc.
- ❌ Migración desde otras apps (import)
- ❌ 2FA (autenticación dos factores)
- ❌ SSO (Single Sign-On)
- ❌ Custom domains
- ❌ White-label
- ❌ Afiliados/referral program
- ❌ Landing page con Astro.js (temporal con Next.js)

---

## 📊 Success Metrics (MVP Launch)

### Objetivos Primeros 3 Meses
| Métrica | Target |
|---------|--------|
| Usuarios registrados | 100 |
| Usuarios activos (crearon al menos 1 tarea) | 60 |
| Usuarios premium | 10 |
| MRR (Monthly Recurring Revenue) | $50 USD |
| Retention 30 días | 30% |
| NPS (Net Promoter Score) | 40+ |
| Bugs críticos reportados | < 5 |

### Definition of Success
**El MVP es exitoso si:**
1. 10 usuarios pagan por Premium en los primeros 3 meses
2. Usuarios completan onboarding sin ayuda
3. NPS > 40 (usuarios recomendarían Flime)
4. Cero downtime crítico
5. Validación del problema: Usuarios usan la app 3+ veces por semana

---

## 🚀 Launch Checklist

### 2 Semanas Antes del Launch
- [ ] Beta testing completado
- [ ] Bugs críticos resueltos
- [ ] Mercado Pago en modo producción y probado
- [ ] Resend configurado con dominio personalizado
- [ ] Términos de servicio + privacidad publicados
- [ ] Landing page funcional
- [ ] Analytics configurado (PostHog/Vercel)
- [ ] Sentry configurado
- [ ] Dominio flime.ai apuntando a Vercel
- [ ] SSL certificado activo

### Launch Day
- [ ] Deploy a producción
- [ ] Smoke test completo (signup → pay → use)
- [ ] Monitoreo activo (errores, performance)
- [ ] Post redes sociales personales
- [ ] Post en r/ADHD (Reddit)
- [ ] Post en comunidades de productividad
- [ ] Email a beta testers agradeciendo

### Primera Semana Post-Launch
- [ ] Responder todos los mensajes/feedback
- [ ] Daily check de analytics
- [ ] Fix hot fixes si hay bugs
- [ ] Iterar en onboarding si hay fricción
- [ ] Documentar learnings

---

## 🛠️ Tech Stack Summary

Ver [docs/architecture/tech-stack.md](../architecture/tech-stack.md) para detalles.

**Core:**
- Next.js 15 (App Router)
- TypeScript
- Tailwind CSS v4
- shadcn/ui

**Backend:**
- Supabase (PostgreSQL + Auth + Realtime)
- Drizzle ORM

**Integraciones:**
- Resend (emails)
- Mercado Pago (pagos)
- Vercel (deploy)

---

## 📅 Timeline

**Total: 10 semanas**

| Sprint | Semanas | Foco |
|--------|---------|------|
| 1-2 | 1-2 | Setup + Auth |
| 3-4 | 3-4 | Boards + Tasks |
| 5-6 | 5-6 | Calendar + Notifications |
| 7-8 | 7-8 | Freemium + Payments |
| 9-10 | 9-10 | Polish + Testing + Launch |

**Horas totales:** 14h/semana × 10 semanas = **140 horas**

---

## ✅ Definition of Done (MVP)

**El MVP está completo y listo para launch cuando:**

1. ✅ **Funcionalidad completa:**
   - Signup/Login funciona
   - CRUD de tableros y tareas funciona
   - Calendario muestra tareas correctamente
   - Emails se envían correctamente
   - Límites FREE/PREMIUM se aplican
   - Pago con Mercado Pago funciona end-to-end

2. ✅ **Calidad:**
   - Cero bugs críticos (que impidan usar la app)
   - Lighthouse score > 85
   - Funciona en Chrome, Firefox, Safari, Edge
   - Funciona en mobile (iOS/Android browsers)
   - Accesibilidad básica (keyboard nav, labels)

3. ✅ **Testing:**
   - 10 usuarios beta completaron onboarding exitosamente
   - Test de pago real completado (al menos 1 pago recibido)
   - Cross-browser testing done

4. ✅ **Legal & Compliance:**
   - Términos de servicio publicados
   - Política de privacidad publicada
   - Cookie consent funcionando

5. ✅ **Deploy:**
   - Producción en Vercel estable
   - Dominio flime.ai activo
   - SSL activo
   - Monitoring configurado

6. ✅ **Marketing básico:**
   - Landing page funcional
   - Meta tags optimizados
   - Al menos 1 canal de adquisición activo (Reddit/redes)

---

**Aprobado por:** [Pendiente]
**Fecha inicio desarrollo:** Enero 2, 2025
**Fecha target launch:** Marzo 15, 2025

---

## Notas Finales

Este MVP es **intencionalmente minimalista**. El objetivo es lanzar rápido, validar el problema, y conseguir usuarios pagos que validen el modelo de negocio.

**Después del MVP**, iteraremos basado en feedback real de usuarios. Las features de Fase 2 (integraciones, WhatsApp, plan Team) solo se construyen si hay demanda validada.

**Mantra:** "Lanza antes de estar listo. Itera basado en feedback."
