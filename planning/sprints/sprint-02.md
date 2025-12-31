# Sprint 2: Authentication & User Management

**Duración:** 1 semana (14 horas)
**Inicio:** Enero 2, 2025
**Fin:** Enero 8, 2025
**Status:** 🔄 En progreso

---

## 🎯 Objetivo del Sprint

Implementar el sistema completo de autenticación y gestión de usuarios usando Supabase Auth con **Magic Link** como método principal y **Google OAuth** como alternativa, permitiendo que los usuarios puedan registrarse, iniciar sesión, gestionar su perfil y completar el onboarding inicial.

**Cambio importante:** Signup/Login unificados con Magic Link (ADHD-friendly - sin passwords que olvidar)

---

## 📋 Tareas

### 1. Configuración de Supabase Auth (1h) ✅

**Descripción:** Configurar Supabase Auth para el proyecto

**Subtareas:**
- [x] Configurar Email Auth (Magic Link) en Supabase Dashboard
  - Habilitar Email provider
  - Configurar email template para Magic Link
  - Configurar redirect URLs (localhost + producción)
  - Deshabilitar "Confirm email" para desarrollo
- [x] Configurar Google OAuth provider
  - Crear OAuth credentials en Google Cloud Console
  - Configurar Client ID y Client Secret en Supabase
  - Configurar redirect URLs
- [x] Actualizar variables de entorno
  - NEXT_PUBLIC_SITE_URL
- [x] Verificar que trigger `handle_new_user()` está activo

**Criterios de aceptación:**
- ✅ Magic Link habilitado en Supabase
- ✅ Email template configurado
- ✅ Redirect URLs correctas (localhost + producción)
- ✅ Google OAuth configurado
- ✅ Trigger sincronizando auth.users → public.users

**Archivos:**
- `.env.local` - Variables de entorno
- Supabase Dashboard

---

### 2. Página de Autenticación Unificada (3h)

**Descripción:** Una sola página `/auth` para signup/login con Magic Link + Google OAuth

**Subtareas:**
- [ ] Crear `src/app/auth/page.tsx`
- [ ] Form con un solo campo: Email
- [ ] Botón "Continuar con Email" → Envía Magic Link
- [ ] Botón "Continuar con Google" → OAuth flow
- [ ] Validación con Zod (solo email)
- [ ] Loading states
- [ ] Manejo de errores
- [ ] Link a términos de servicio y privacidad

**Criterios de aceptación:**
- Form envía Magic Link correctamente
- Google OAuth funciona
- Validación client-side con Zod
- Errores mostrados claramente
- UX limpia y ADHD-friendly

**Archivos:**
- `src/app/auth/page.tsx`
- `src/lib/validations/auth.ts` (actualizar schema)
- `src/lib/actions/auth.ts` (server actions)

---

### 3. Página de Verificación (1h)

**Descripción:** Página que muestra después de enviar Magic Link

**Subtareas:**
- [ ] Crear `src/app/auth/verify/page.tsx`
- [ ] Mensaje: "Revisa tu email"
- [ ] Instrucciones claras
- [ ] Mostrar email ingresado
- [ ] Botón "Reenviar email" (con cooldown de 60s)
- [ ] Link para "Usar otro email" → volver a /auth

**Criterios de aceptación:**
- Mensaje claro y amigable
- Botón reenviar funciona
- Cooldown previene spam

**Archivos:**
- `src/app/auth/verify/page.tsx`

---

### 4. Callback Handler (2h)

**Descripción:** Manejar el redirect después de Magic Link o Google OAuth

**Subtareas:**
- [ ] Crear `src/app/auth/callback/route.ts`
- [ ] Extraer code de URL params
- [ ] Intercambiar code por session con Supabase
- [ ] Verificar si es nuevo usuario o existente
- [ ] Redirect según caso:
  - Nuevo usuario → /onboarding
  - Usuario existente → /dashboard
- [ ] Manejo de errores (link expirado, etc.)

**Criterios de aceptación:**
- Magic Link funciona correctamente
- Google OAuth redirect funciona
- Nuevos usuarios van a onboarding
- Usuarios existentes van a dashboard
- Errores manejados correctamente

**Archivos:**
- `src/app/auth/callback/route.ts`

---

### 5. Protected Routes & Middleware (2h)

**Descripción:** Proteger rutas que requieren autenticación

**Subtareas:**
- [ ] Actualizar `src/proxy.ts` para validar sesión
- [ ] Redirect a /auth si no autenticado
- [ ] Redirect a /dashboard si ya autenticado (en /auth)
- [ ] Helper `getUser()` en server components
- [ ] Hook `useUser()` en client components (TanStack Query)

**Criterios de aceptación:**
- Rutas protegidas funcionan
- Redirect automático funciona
- Session persiste en cookies
- User data accesible en componentes

**Archivos:**
- `src/proxy.ts` (actualizar)
- `src/lib/supabase/server.ts` (helper getUser)
- `src/lib/hooks/use-user.ts` (nuevo)

---

### 6. Wizard de Onboarding (3h)

**Descripción:** Wizard de 3 pasos para nuevos usuarios

**Subtareas:**
- [ ] Crear `src/app/onboarding/page.tsx`
- [ ] Layout: Route group `(onboarding)` para layout especial
- [ ] Paso 1: Bienvenida + Nombre completo
  - Input: Full Name
  - Auto-detectar timezone con `Intl.DateTimeFormat().resolvedOptions().timeZone`
  - Idioma default: español
- [ ] Paso 2: Crear primera tarea (OPCIONAL)
  - Form simplificado (solo título y descripción)
  - Skip button prominente
- [ ] Paso 3: Preferencias de notificación
  - Toggle: Email notifications (default: true)
  - Toggle: Daily summary (default: false)
  - Time picker: Daily summary time (default: 08:00)
- [ ] Actualizar users table con preferencias
- [ ] Redirect a /dashboard al finalizar
- [ ] Indicador de progreso (1/3, 2/3, 3/3)

**Criterios de aceptación:**
- Wizard guía al usuario paso a paso
- Preferencias se guardan en users table
- Puede hacer skip del paso 2
- Redirect funciona al finalizar
- UX amigable, ADHD-friendly

**Archivos:**
- `src/app/(onboarding)/onboarding/page.tsx`
- `src/app/(onboarding)/layout.tsx`
- `src/components/onboarding/` (componentes del wizard)

---

### 7. User Profile & Settings (2h)

#### 7.1 Página de Perfil

**Descripción:** Ver y editar perfil del usuario

**Subtareas:**
- [ ] Crear `src/app/dashboard/profile/page.tsx`
- [ ] Mostrar datos actuales:
  - Full Name
  - Email (read-only)
  - Avatar (iniciales generadas - círculo con color)
  - Timezone
  - Language (es/en)
- [ ] Form para editar:
  - Full Name
  - Timezone (select con zonas comunes)
  - Language (select: Español/English)
- [ ] Server action para actualizar users table
- [ ] Toast de confirmación (Sonner)

**Criterios de aceptación:**
- Datos se muestran correctamente
- Edición funciona
- Cambios se persisten en users table
- UI consistente con diseño

**Archivos:**
- `src/app/dashboard/profile/page.tsx`
- `src/lib/actions/user.ts` (server actions)

---

#### 7.2 Settings: Notificaciones

**Descripción:** Configurar preferencias de notificación

**Subtareas:**
- [ ] Crear `src/app/dashboard/settings/page.tsx`
- [ ] Sección Notificaciones:
  - Toggle: Email notifications (users.emailNotifications)
  - Toggle: Web push notifications (users.webPushNotifications)
  - Toggle: Daily summary (users.dailySummary)
  - Time picker: Daily summary time (users.dailySummaryTime)
  - Week starts on (users.weekStartsOn: 0=Domingo, 1=Lunes)
- [ ] Server action para actualizar users table
- [ ] Validación: daily summary time solo si daily summary = true

**Criterios de aceptación:**
- Toggles funcionan
- Cambios se guardan en users table
- Validación correcta
- UI clara y accesible

**Archivos:**
- `src/app/dashboard/settings/page.tsx`
- `src/lib/actions/user.ts`

---

### 8. Logout & Session Management (1h)

**Descripción:** Función de logout y gestión de sesión

**Subtareas:**
- [ ] Crear componente `src/components/layout/user-dropdown.tsx`
- [ ] Dropdown con:
  - Avatar + nombre
  - Link a /dashboard/profile
  - Link a /dashboard/settings
  - Divider
  - Botón "Logout"
- [ ] Server action para logout
- [ ] Llamar a `supabase.auth.signOut()`
- [ ] Limpiar cookies
- [ ] Redirect a /auth
- [ ] Confirmación visual (toast)

**Criterios de aceptación:**
- Logout funciona correctamente
- Cookies se limpian
- Redirect a /auth
- No puede acceder a rutas protegidas después de logout

**Archivos:**
- `src/components/layout/user-dropdown.tsx`
- `src/lib/actions/auth.ts` (agregar signOut action)

---

## 🧪 Testing

**Manual Testing:**
- [ ] Auth con Magic Link completo
- [ ] Auth con Google OAuth
- [ ] Magic Link expirado (error handling)
- [ ] Reenviar Magic Link
- [ ] Onboarding wizard (todos los pasos)
- [ ] Skip onboarding paso 2
- [ ] Editar perfil
- [ ] Cambiar settings de notificaciones
- [ ] Logout
- [ ] Protected routes (intentar acceder sin auth)

**Cross-browser:**
- [ ] Chrome
- [ ] Firefox
- [ ] Safari

**Mobile:**
- [ ] iOS Safari
- [ ] Chrome Android

---

## 📦 Entregables

1. ✅ Sistema de autenticación con Magic Link + Google OAuth
2. ✅ Página `/auth` unificada (ADHD-friendly)
3. ✅ Callback handler funcionando
4. ✅ Onboarding wizard de 3 pasos
5. ✅ Página de perfil editable
6. ✅ Configuración de notificaciones
7. ✅ Protected routes funcionando
8. ✅ Session management robusto
9. ✅ Logout funcional

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Usuario puede autenticarse con Magic Link SIN password
2. Usuario puede autenticarse con Google OAuth
3. Usuario completa onboarding sin fricción
4. Usuario puede editar su perfil y settings
5. Protected routes funcionan correctamente
6. Logout funciona
7. Cero errores críticos
8. Todo funciona en mobile
9. UX es ADHD-friendly (simple, sin fricción)

---

## 📊 Métricas a Medir

- Tiempo promedio de auth → dashboard (target: < 30s)
- Tasa de completación de onboarding (target: > 80%)
- Errores en auth flow
- Performance de páginas auth (Lighthouse > 90)

---

## 🚧 Blockers Potenciales

| Blocker | Probabilidad | Mitigación |
|---------|--------------|------------|
| Magic Link en spam | Media | Instrucciones claras, testing con múltiples proveedores (Gmail, Outlook) |
| Session cookies issues | Media | Revisar Next.js 16 docs, proxy.ts setup correcto |
| Google OAuth redirect issues | Baja | Verificar URLs en Google Cloud Console |
| Timezone auto-detection fallos | Baja | Fallback manual, validación |

---

## 🔄 Dependencias

**Depende de:**
- ✅ Sprint 1 (Database schema, Supabase setup, RLS policies, triggers)

**Bloquea a:**
- Sprint 3-4 (Tasks & Tags CRUD necesita auth)

---

## 📝 Notas Técnicas

### Magic Link Flow
```typescript
// Server action: Enviar Magic Link
'use server'

export async function signInWithMagicLink(email: string) {
  const supabase = await createClient()

  const { data, error } = await supabase.auth.signInWithOtp({
    email,
    options: {
      emailRedirectTo: `${process.env.NEXT_PUBLIC_SITE_URL}/auth/callback`,
      shouldCreateUser: true,
    },
  })

  if (error) throw error
  return { success: true }
}
```

### Google OAuth Flow
```typescript
// Server action: Iniciar OAuth
'use server'

export async function signInWithGoogle() {
  const supabase = await createClient()

  const { data, error } = await supabase.auth.signInWithOAuth({
    provider: 'google',
    options: {
      redirectTo: `${process.env.NEXT_PUBLIC_SITE_URL}/auth/callback`,
    },
  })

  if (error) throw error
  return data.url // Redirect URL
}
```

### Callback Handler
```typescript
// app/auth/callback/route.ts
import { NextRequest, NextResponse } from 'next/server'
import { createClient } from '@/lib/supabase/server'

export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url)
  const code = searchParams.get('code')

  if (code) {
    const supabase = await createClient()
    const { error } = await supabase.auth.exchangeCodeForSession(code)

    if (!error) {
      // Check if new user
      const { data: { user } } = await supabase.auth.getUser()
      const { data: profile } = await supabase
        .from('users')
        .select('full_name')
        .eq('id', user!.id)
        .single()

      // Redirect según caso
      const redirectTo = !profile?.full_name ? '/onboarding' : '/dashboard'
      return NextResponse.redirect(new URL(redirectTo, request.url))
    }
  }

  // Error fallback
  return NextResponse.redirect(new URL('/auth?error=auth-error', request.url))
}
```

### Protected Route Pattern
```typescript
// Server Component
import { createClient } from '@/lib/supabase/server'
import { redirect } from 'next/navigation'

export default async function ProtectedPage() {
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser()

  if (!user) {
    redirect('/auth')
  }

  return <div>Protected content</div>
}
```

### useUser Hook (Client)
```typescript
// src/lib/hooks/use-user.ts
'use client'

import { useQuery } from '@tanstack/react-query'
import { createClient } from '@/lib/supabase/client'

export function useUser() {
  return useQuery({
    queryKey: ['user'],
    queryFn: async () => {
      const supabase = createClient()
      const { data: { user }, error } = await supabase.auth.getUser()
      if (error) throw error
      return user
    },
  })
}
```

---

## 🎨 UI/UX Considerations

**Auth Page:**
- Clean, minimal design (centered card)
- Headline: "Bienvenido a Flime"
- Subheadline: "Tu cerebro externo para recordar todo"
- Input email grande y claro
- Botón "Continuar con Email" (primary)
- Divider "o"
- Botón "Continuar con Google" (con logo de Google)
- Footer: Links a términos y privacidad

**Verify Page:**
- Icono de email grande
- Headline: "Revisa tu bandeja"
- Mensaje: "Te enviamos un link a [email]"
- Instrucciones: "Haz click en el link para continuar"
- Botón "Reenviar email" (disabled por 60s)
- Link "Usar otro email"

**Onboarding:**
- Progress bar arriba (1/3, 2/3, 3/3)
- Un paso a la vez (no tabs)
- Botón "Siguiente" destacado
- Botón "Skip" en paso 2 (secondary)
- Animaciones suaves entre pasos
- Confetti al finalizar paso 3 ✨

**Profile/Settings:**
- Layout con sidebar (desktop) o tabs (mobile)
- Forms con labels claros
- Save button solo habilitado si hay cambios
- Toast confirmando guardado (Sonner)

---

## 🔗 Referencias

- [Supabase Auth - Magic Link](https://supabase.com/docs/guides/auth/auth-email-passwordless)
- [Supabase Auth - OAuth](https://supabase.com/docs/guides/auth/social-login/auth-google)
- [Next.js 16 Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations)
- [React Hook Form](https://www.react-hook-form.com/)

---

## ✅ Definition of Done

- [ ] Todas las tareas completadas
- [ ] Testing manual completado
- [ ] Cero bugs críticos
- [ ] Magic Link funciona en producción
- [ ] Google OAuth funciona en producción
- [ ] Code review (self-review)
- [ ] Deployed to staging
- [ ] Lighthouse score > 90 en auth pages
- [ ] Accesibilidad: keyboard navigation funciona
- [ ] Mobile responsive

---

**Creado:** Diciembre 31, 2024
**Actualizado:** Diciembre 31, 2024 (Magic Link approach)
**Próxima revisión:** Al finalizar Sprint 2
