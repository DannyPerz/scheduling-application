# Sprint 6: Notifications System

**Duración:** 1 semana (14 horas)
**Inicio:** Enero 19, 2026
**Fin:** Enero 25, 2026
**Status:** 📋 Planificado

---

## 🎯 Objetivo del Sprint

Implementar un sistema robusto de notificaciones para aumentar la retención y el engagement. El foco principal son los recordatorios por email para tareas próximas y vencidas, utilizando Resend como proveedor de infraestructura.

---

## 📋 Tareas

### 1. Infraestructura de Email (Resend) (2h)

**Descripción:** Configuración inicial del proveedor de email.

**Subtareas:**
- [ ] Configurar cuenta de Resend y obtener API Keys
- [ ] Configurar dominio de envío (DNS records)
- [ ] Crear cliente de email en `src/lib/email/client.ts`
- [ ] Configurar variables de entorno (`RESEND_API_KEY`, `EMAIL_FROM`)

**Criterios de aceptación:**
- [ ] Se puede enviar un "Hello World" email desde la API route de prueba
- [ ] Dominio verificado en dashboard de Resend

**Archivos:**
- `src/lib/email/client.ts`
- `.env.local`

---

### 2. Email Templates (React Email) (3h)

**Descripción:** Diseñar correos transaccionales hermosos y funcionales.

**Subtareas:**
- [ ] Setup `@react-email/components`
- [ ] Crear `TaskReminderEmail` template:
  - Mostrar tarea con fecha/hora
  - Call to action "Ver tarea" o "Completar"
  - Diseño responsive y clean (dark mode compatible si es posible)
- [ ] Crear `DailySummaryEmail` template:
  - Lista de tareas para hoy
  - Resumen de tareas vencidas
  - Frase motivacional (opcional)

**Criterios de aceptación:**
- [ ] Templates renderizan correctamente en clientes principales (Gmail, Outlook, Apple Mail)
- [ ] Preview funcional en entorno de desarrollo

**Archivos:**
- `src/emails/task-reminder.tsx`
- `src/emails/daily-summary.tsx`

---

### 3. Backend & Cron Jobs (4h)

**Descripción:** Lógica para detectar cuándo enviar notificaciones.

**Subtareas:**
- [ ] **[SCHEMA READY]** Utilizar tabla `reminders` existente para gestionar recordatorios y evitar duplicados.
- [ ] Crear Cron Job `/api/cron/send-reminders`:
  - Frecuencia: Cada 15/30 mins
  - Query: Tareas con `due_date` próximo (ej. en 1 hora) sin registro en `reminders` (o con recordatorio no enviado).
  - Action: Enviar email usando template `TaskReminderEmail`
  - Update: Marcar recordatorio como `is_sent=true` en tabla `reminders`.
- [ ] Crear Cron Job `/api/cron/send-daily-summary`:
  - Frecuencia: Diario
  - Query: Tareas `due_date` = Hoy
  - Action: Enviar resumen
- [ ] Validar límites FREE (ej. máx 1 email al día o solo reminders críticos)

**Criterios de aceptación:**
- [ ] Cron job se ejecuta correctamente (via Vercel Cron)
- [ ] Emails llegan a tiempo
- [ ] No se envían correos duplicados
- [ ] Se registran los envíos en la tabla `reminders`

**Archivos:**
- `src/app/api/cron/send-reminders/route.ts`
- `src/app/api/cron/send-daily-summary/route.ts`
- `src/db/schema/reminders.ts` (Existente)

---

### 4. Settings de Notificaciones (3h)

**Descripción:** UI para que el usuario controle qué recibe.

**Subtareas:**
- [ ] **[SCHEMA READY]** Utilizar campos existentes en `users`: `email_notifications`, `daily_summary`, `daily_summary_time`.
- [ ] Crear componente `src/components/settings/notifications-settings.ts`
  - Toggles para cada tipo de notificación
  - Input para hora del resumen diario
- [ ] Integrar en página `/dashboard/settings`
- [ ] Conectar con Server Actions para guardar preferencias

**Criterios de aceptación:**
- [ ] Usuario puede desactivar todas las notificaciones
- [ ] backend respeta estas preferencias antes de enviar

**Archivos:**
- `src/components/settings/notifications-settings.tsx`
- `src/lib/actions/settings.ts`

---

### 5. Navegación & Polish (2h)

**Descripción:** Mejoras generales y Web Push (Exploratorio)

**Subtareas:**
- [ ] Investigar API Notification nativa del navegador (Web Push)
- [ ] (Opcional) Implementar botón "Activar notificaciones de escritorio"
- [ ] Pruebas end-to-end del flujo de notificación

**Archivos:**
- `src/components/notifications/push-manager.tsx`

---

## 🧪 Testing

**Manual Testing:**
- [ ] Crear tarea para dentro de 1 hora -> Esperar email
- [ ] Cambiar settings -> Verificar que NO llega email
- [ ] Verificar links dentro del email (llevan a la tarea correcta)

---

## 📦 Entregables

1. [ ] Integración con Resend operativa
2. [ ] Templates de email (Reminder y Summary)
3. [ ] Cron jobs configurados en Vercel
4. [ ] Panel de preferencias de notificación

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Los usuarios reciben recordatorios confiables de sus tareas.
2. Los emails tienen un diseño profesional y acorde a la marca.
3. El usuario tiene control total sobre si recibir o no correos (GDPR/Privacidad).
4. El sistema no supera los límites gratuitos de Resend/Vercel por error (bucles infinitos).

---

## 🚧 Blockers Potenciales

| Blocker | Probabilidad | Mitigación |
|---------|--------------|------------|
| Vercel Cron limits (Hobby) | Media | Usar GitHub Actions como trigger alternativo si es necesario |
| Resend domain verification | Baja | Acceso a DNS provider es requerido |
| Timezone handling | Alta | Guardar timezone del usuario en registro o login |

---

## 🔗 Referencias

- [Vercel Cron Jobs](https://vercel.com/docs/cron-jobs)
- [Resend Documentation](https://resend.com/docs)
- [React Email](https://react.email/)

---

**Creado:** Enero 18, 2026
**Próxima revisión:** Enero 24, 2026
