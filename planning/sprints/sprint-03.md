# Sprint 3: Tags & Tasks CRUD (ADHD-Friendly)

**Duración:** 1 semana (14 horas)
**Inicio:** Enero 3, 2026
**Fin:** Enero 9, 2026
**Status:** 📋 Planificado

---

## 🎯 Objetivo del Sprint

Implementar el sistema completo de gestión de tags y tareas con campos ADHD-friendly, permitiendo a los usuarios organizar sus tareas de manera flexible usando etiquetas en lugar de tableros rígidos, con funcionalidades core como crear, editar, completar y eliminar tareas con soporte para estimación de tiempo (time blindness).

**Cambio fundamental:** Tags flexibles en lugar de boards rígidos (mejor UX para cerebros ADHD)

---

## 📋 Tareas

### 1. Dashboard Layout Base (2h)

**Descripción:** Crear el layout principal del dashboard con sidebar, header y área de contenido

**Subtareas:**
- [ ] Crear `src/app/dashboard/layout.tsx`
  - Sidebar izquierda (fija en desktop, drawer en mobile)
  - Header superior con user dropdown
  - Main content area responsiva
- [ ] Crear `src/components/layout/dashboard-sidebar.tsx`
  - Logo Flime
  - Search input (placeholder, funcional en tarea 6)
  - Sección "Vistas rápidas"
  - Sección "Tags" (lista dinámica)
  - Footer con links (Settings, Upgrade)
- [ ] Crear `src/components/layout/dashboard-header.tsx`
  - Saludo: "Buenos días, [Nombre]"
  - User dropdown (reusar de Sprint 2)
  - Botón mobile menu (hamburger)
- [ ] Responsive design:
  - Desktop: Sidebar visible siempre
  - Tablet/Mobile: Sidebar como drawer, toggle con botón
- [ ] Implementar estado de sidebar (open/closed) con Zustand o useState

**Criterios de aceptación:**
- ✅ Layout completo y responsive
- ✅ Sidebar funcional en mobile (drawer)
- ✅ Header con user dropdown funcionando
- ✅ Navegación entre secciones fluida
- ✅ Consistente con diseño shadcn/ui

**Archivos:**
- `src/app/dashboard/layout.tsx`
- `src/components/layout/dashboard-sidebar.tsx`
- `src/components/layout/dashboard-header.tsx`
- `src/lib/store/sidebar-store.ts` (opcional, si usas Zustand)

---

### 2. Tags CRUD - Backend (2h)

**Descripción:** API routes para gestión completa de tags

**Subtareas:**
- [ ] Crear `src/app/api/tags/route.ts`
  - **GET** - Listar tags del usuario autenticado
    - Query: SELECT * FROM tags WHERE user_id = auth.uid() ORDER BY name
    - Return: Array de tags con contador de tareas
  - **POST** - Crear nuevo tag
    - Body: { name, color }
    - Validación: Zod schema (name: 1-30 chars, color: hex)
    - Insert en tabla tags
- [ ] Crear `src/app/api/tags/[id]/route.ts`
  - **GET** - Obtener tag por ID (con RLS check)
  - **PUT** - Actualizar tag (name, color)
  - **DELETE** - Eliminar tag
    - Lógica: Desasociar de tareas (DELETE FROM task_tags WHERE tag_id = id)
    - No eliminar tareas asociadas
- [ ] Crear validaciones en `src/lib/validations/tag.ts`
  ```typescript
  export const createTagSchema = z.object({
    name: z.string().min(1).max(30),
    color: z.string().regex(/^#[0-9A-F]{6}$/i),
  })
  ```
- [ ] Server actions en `src/lib/actions/tags.ts` (alternativa a API routes)
  - createTag(data: CreateTagInput)
  - updateTag(id: string, data: UpdateTagInput)
  - deleteTag(id: string)
  - getTags()

**Criterios de aceptación:**
- ✅ API routes funcionando correctamente
- ✅ Validación con Zod
- ✅ RLS aplicado (usuarios solo ven sus tags)
- ✅ Errores manejados apropiadamente
- ✅ TypeScript types correctos

**Archivos:**
- `src/app/api/tags/route.ts`
- `src/app/api/tags/[id]/route.ts`
- `src/lib/validations/tag.ts`
- `src/lib/actions/tags.ts` (opcional)

---

### 3. Tags CRUD - Frontend (2h)

**Descripción:** UI para crear, editar y eliminar tags desde sidebar

**Subtareas:**
- [ ] Crear `src/components/tags/tag-list.tsx`
  - Lista de tags con:
    - Color indicator (círculo o badge)
    - Nombre del tag
    - Contador de tareas (ej: "8")
    - Botón edit (icono)
  - Click en tag → filtrar tareas por ese tag
  - Botón "+ Nuevo tag" al final de la lista
- [ ] Crear `src/components/tags/tag-form-dialog.tsx`
  - Modal con React Hook Form + Zod
  - Input: Nombre (max 30 chars)
  - Color picker: 12 colores predefinidos + selector custom
  - Modo: Crear o Editar (mismo componente)
  - Botones: Guardar / Cancelar
- [ ] Crear `src/components/tags/delete-tag-dialog.tsx`
  - AlertDialog de confirmación
  - Mensaje: "¿Eliminar tag [nombre]? Se desasignará de X tareas"
  - Botones: Cancelar / Eliminar
- [ ] Integrar con TanStack Query:
  - useQuery para `getTags()`
  - useMutation para `createTag`, `updateTag`, `deleteTag`
  - Invalidación de cache al mutar
- [ ] Toast notifications (Sonner):
  - "Tag creado"
  - "Tag actualizado"
  - "Tag eliminado"

**Criterios de aceptación:**
- ✅ Lista de tags se muestra en sidebar
- ✅ Crear tag funciona
- ✅ Editar tag funciona
- ✅ Eliminar tag funciona con confirmación
- ✅ UI responsive y accesible
- ✅ Loading states y error handling

**Archivos:**
- `src/components/tags/tag-list.tsx`
- `src/components/tags/tag-form-dialog.tsx`
- `src/components/tags/delete-tag-dialog.tsx`
- `src/lib/hooks/use-tags.ts` (TanStack Query hooks)

---

### 4. Tasks CRUD - Backend (3h)

**Descripción:** API routes para gestión completa de tareas con campos ADHD-friendly

**Subtareas:**
- [ ] Crear `src/app/api/tasks/route.ts`
  - **GET** - Listar tareas del usuario
    - Query params: status, priority, tagId, search
    - Join con task_tags y tags para incluir tags
    - Order by: dueDate ASC, priority DESC, order ASC
    - Return: Array de tasks con tags asociados
  - **POST** - Crear nueva tarea
    - Body: { title, description, dueDate, priority, estimatedDuration, tagIds[] }
    - Validación: Zod schema
    - Verificar límite FREE (15 tareas activas)
    - Insert en tabla tasks
    - Insert relaciones en task_tags (si hay tagIds)
- [ ] Crear `src/app/api/tasks/[id]/route.ts`
  - **GET** - Obtener tarea por ID (con tags)
  - **PUT** - Actualizar tarea
    - Actualizar campos de tasks
    - Sincronizar task_tags (delete antiguas + insert nuevas)
  - **PATCH** - Completar/reabrir tarea
    - Update status, completedAt
    - Si completando: Pedir actualDuration
  - **DELETE** - Eliminar tarea (hard delete en MVP)
- [ ] Crear validaciones en `src/lib/validations/task.ts`
  ```typescript
  export const createTaskSchema = z.object({
    title: z.string().min(1, 'El título es requerido').max(200),
    description: z.string().max(1000).optional(),
    dueDate: z.string().datetime().optional(),
    priority: z.enum(['low', 'medium', 'high', 'urgent']).default('medium'),
    estimatedDuration: z.number().int().positive().optional(),
    tagIds: z.array(z.string().uuid()).optional(),
  })

  export const completeTaskSchema = z.object({
    actualDuration: z.number().int().positive().optional(),
  })
  ```
- [ ] Server actions en `src/lib/actions/tasks.ts`
  - getTasks(filters?: TaskFilters)
  - createTask(data: CreateTaskInput)
  - updateTask(id: string, data: UpdateTaskInput)
  - completeTask(id: string, actualDuration?: number)
  - deleteTask(id: string)
- [ ] Helper: Validar límite FREE
  ```typescript
  async function validateTaskLimit(userId: string, userPlan: string) {
    if (userPlan !== 'free') return true

    const { count } = await db
      .select({ count: sql`count(*)` })
      .from(tasks)
      .where(
        and(
          eq(tasks.userId, userId),
          or(
            eq(tasks.status, 'pending'),
            eq(tasks.status, 'in_progress')
          )
        )
      )

    if (count >= 15) {
      throw new Error('Límite de 15 tareas activas alcanzado. Upgradea a Premium.')
    }
  }
  ```

**Criterios de aceptación:**
- ✅ CRUD completo de tareas funcionando
- ✅ Relación many-to-many con tags funciona
- ✅ Validación con Zod correcta
- ✅ Límite FREE aplicado (15 tareas activas)
- ✅ RLS aplicado correctamente
- ✅ Campos ADHD-friendly (estimatedDuration, actualDuration) funcionando
- ✅ Errores manejados apropiadamente

**Archivos:**
- `src/app/api/tasks/route.ts`
- `src/app/api/tasks/[id]/route.ts`
- `src/lib/validations/task.ts`
- `src/lib/actions/tasks.ts`
- `src/lib/utils/task-limits.ts`

---

### 5. Tasks CRUD - Frontend (3h)

**Descripción:** UI para crear, editar, completar y eliminar tareas

**Subtareas:**
- [ ] Crear `src/components/tasks/task-form-dialog.tsx`
  - Modal grande con React Hook Form + Zod
  - Campos:
    - **Título** (input text, obligatorio, max 200 chars)
    - **Descripción** (textarea, opcional, max 1000 chars)
    - **Fecha de vencimiento** (date picker con shadcn/ui, opcional)
    - **Prioridad** (select: Baja/Media/Alta/Urgente, default: Media)
    - **Duración estimada** (number input en minutos, con helpers: 15min, 30min, 1h, 2h)
    - **Tags** (multi-select con checkboxes o combobox)
  - Modo: Crear o Editar (mismo componente)
  - Botones: Guardar / Cancelar
  - Loading state al guardar
- [ ] Crear `src/components/tasks/task-list.tsx`
  - Lista de tareas agrupadas por status:
    - **Pending** (por hacer)
    - **In Progress** (en progreso)
    - **Completed** (completadas - colapsado por default)
  - Cada tarea muestra:
    - Checkbox (completar/reabrir)
    - Título (clickable para editar)
    - Tags (badges con colores)
    - Due date (si existe, con indicador "vence hoy" / "vencida")
    - Priority indicator (color o icono)
    - Estimated duration (si existe)
  - Botón "..." para acciones:
    - Editar
    - Eliminar
    - Mover a In Progress / Pending
- [ ] Crear `src/components/tasks/task-complete-dialog.tsx`
  - Dialog al completar tarea
  - Mensaje: "¡Tarea completada! 🎉"
  - Input opcional: "¿Cuánto tiempo tomó? (minutos)"
  - Si hay estimatedDuration, mostrar comparación:
    - "Estimaste 30 min, tomó 45 min"
  - Botones: Confirmar / Cancelar
  - Confetti animation (opcional, con react-confetti o canvas-confetti)
- [ ] Crear `src/components/tasks/delete-task-dialog.tsx`
  - AlertDialog de confirmación
  - Mensaje: "¿Eliminar tarea '[título]'?"
  - Warning: "Esta acción no se puede deshacer"
  - Botones: Cancelar / Eliminar
- [ ] Integrar con TanStack Query:
  - useQuery: `getTasks(filters)`
  - useMutation: `createTask`, `updateTask`, `completeTask`, `deleteTask`
  - Optimistic updates para mejor UX
  - Cache invalidation
- [ ] FAB (Floating Action Button):
  - Botón "+" fixed bottom-right
  - Click → abrir task-form-dialog en modo crear
- [ ] Empty states:
  - Sin tareas: Ilustración + "Crea tu primera tarea"
  - Sin tareas en filtro: "No hay tareas [filtro]"

**Criterios de aceptación:**
- ✅ Crear tarea funciona con todos los campos
- ✅ Editar tarea funciona
- ✅ Completar tarea funciona (con actualDuration opcional)
- ✅ Eliminar tarea funciona con confirmación
- ✅ Lista de tareas se muestra correctamente
- ✅ Tags multi-select funciona
- ✅ UI responsive y accesible
- ✅ Loading states y error handling
- ✅ Animación de confetti al completar (opcional pero deseable)

**Archivos:**
- `src/components/tasks/task-form-dialog.tsx`
- `src/components/tasks/task-list.tsx`
- `src/components/tasks/task-item.tsx`
- `src/components/tasks/task-complete-dialog.tsx`
- `src/components/tasks/delete-task-dialog.tsx`
- `src/lib/hooks/use-tasks.ts` (TanStack Query hooks)
- `src/components/ui/date-picker.tsx` (si no existe)

---

### 6. Filtros y Búsqueda (1.5h)

**Descripción:** Sistema de filtrado y búsqueda de tareas

**Subtareas:**
- [ ] Implementar búsqueda en sidebar:
  - Input con debounce (300ms)
  - Buscar por tasks.title (case insensitive)
  - Mostrar resultados en tiempo real
  - Clear button
- [ ] Crear `src/components/tasks/task-filters.tsx`
  - Filtros disponibles:
    - **Status:** Todos / Pending / In Progress / Completed
    - **Priority:** Todas / Baja / Media / Alta / Urgente
    - **Tag:** Todos / [tag específico]
  - UI: Tabs o Select (dependiendo del espacio)
  - State management: URL params o Zustand
- [ ] Vistas rápidas en sidebar:
  - **Hoy:** tasks.dueDate = today
  - **Próximos 7 días:** tasks.dueDate <= today + 7 days
  - Click → aplicar filtro automáticamente
- [ ] Integrar filtros con API:
  - Pasar filters a getTasks(filters)
  - Backend aplica WHERE clauses según filtros
- [ ] Indicador visual de filtros activos:
  - Badge con número de filtros
  - Botón "Limpiar filtros"

**Criterios de aceptación:**
- ✅ Búsqueda por título funciona con debounce
- ✅ Filtros se aplican correctamente
- ✅ Vistas rápidas (Hoy, Próximos 7 días) funcionan
- ✅ URL params reflejan filtros (opcional)
- ✅ Limpiar filtros funciona
- ✅ UX fluida y responsive

**Archivos:**
- `src/components/tasks/task-filters.tsx`
- `src/components/tasks/task-search.tsx`
- `src/lib/hooks/use-task-filters.ts`

---

### 7. Límites Freemium (1h)

**Descripción:** Aplicar límites de plan FREE y mostrar CTAs de upgrade

**Subtareas:**
- [ ] Validación en backend:
  - Verificar plan del usuario antes de crear tarea
  - Si FREE y >= 15 tareas activas → error 403
  - Error message: "Has alcanzado el límite de 15 tareas activas. Upgradea a Premium para tareas ilimitadas."
- [ ] UI: Mostrar límite alcanzado:
  - Dialog al intentar crear tarea (#16):
    - Mensaje: "Límite alcanzado"
    - Explicación: "Plan FREE: 15 tareas activas. Tienes 15/15."
    - CTA: "Upgrade to Premium" → redirect a /pricing
    - Sugerencia: "Completa o archiva tareas existentes"
  - Botón "Cancelar" → cerrar dialog
- [ ] Indicador de límite en UI:
  - Badge en sidebar: "15/15 tareas" (FREE)
  - Progress bar visual (opcional)
  - Color: Verde (< 10), Amarillo (10-14), Rojo (15)
- [ ] Banner de upgrade (dismissable):
  - Mostrar si user.plan = 'free' y tiene >= 10 tareas
  - Mensaje: "¿Te estás quedando sin espacio? Upgradea a Premium para tareas ilimitadas."
  - Botón "Upgrade" → /pricing
  - Botón "x" → guardar en localStorage (no mostrar por 7 días)

**Criterios de aceptación:**
- ✅ Límite de 15 tareas activas aplicado en backend
- ✅ Dialog de límite alcanzado se muestra correctamente
- ✅ Indicador de límite visible en UI
- ✅ Banner de upgrade funciona y es dismissable
- ✅ Premium users NO ven límites ni banners

**Archivos:**
- `src/components/tasks/task-limit-dialog.tsx`
- `src/components/dashboard/upgrade-banner.tsx`
- `src/lib/utils/plan-limits.ts`
- Backend: Ya implementado en tarea 4

---

### 8. Dashboard Principal - Quick Stats (0.5h)

**Descripción:** Widgets con estadísticas rápidas en dashboard

**Subtareas:**
- [ ] Crear `src/components/dashboard/quick-stats.tsx`
  - Grid de 3 cards (responsive: 1 col mobile, 3 cols desktop)
  - **Card 1:** Tareas pendientes hoy
    - Número grande
    - Icono de calendario
    - Link: "Ver todas"
  - **Card 2:** Tareas completadas esta semana
    - Número grande
    - Icono de check
    - Mensaje: "¡Buen trabajo!"
  - **Card 3:** ADHD Insight (si tiene datos)
    - Tiempo estimado vs real esta semana
    - Ejemplo: "Estimaste 5h, tomó 7h"
    - Accuracy: "70% accurate"
    - Mensaje motivacional si mejora
  - Si no hay datos: Placeholder con mensaje educativo
- [ ] API: Calcular stats en backend o frontend (según performance)
  - Opción 1: Endpoint GET /api/stats (backend agrega queries)
  - Opción 2: Calcular en frontend con datos de getTasks()
- [ ] Responsive design:
  - Mobile: Stack vertical
  - Desktop: Grid horizontal

**Criterios de aceptación:**
- ✅ Quick stats se muestran correctamente
- ✅ Datos calculados correctamente
- ✅ ADHD Insight funciona (si hay datos)
- ✅ Responsive design
- ✅ Empty states si no hay datos

**Archivos:**
- `src/components/dashboard/quick-stats.tsx`
- `src/app/api/stats/route.ts` (opcional)
- `src/lib/utils/stats.ts` (cálculos)

---

## 🧪 Testing

**Manual Testing:**
- [ ] Tags:
  - Crear tag con nombre y color
  - Editar tag (nombre y color)
  - Eliminar tag (verificar desasociación de tareas)
  - Validar límites de caracteres
- [ ] Tareas:
  - Crear tarea completa (todos los campos)
  - Crear tarea mínima (solo título)
  - Asignar múltiples tags a una tarea
  - Completar tarea con actualDuration
  - Completar tarea sin actualDuration
  - Reabrir tarea completada
  - Editar tarea existente
  - Eliminar tarea con confirmación
- [ ] Filtros:
  - Buscar por título
  - Filtrar por status
  - Filtrar por priority
  - Filtrar por tag
  - Vista "Hoy"
  - Vista "Próximos 7 días"
  - Limpiar filtros
- [ ] Freemium:
  - Crear 15 tareas en plan FREE
  - Intentar crear tarea #16 (debe fallar)
  - Ver dialog de límite alcanzado
  - Ver banner de upgrade (a partir de tarea #10)
  - Dismissar banner y verificar localStorage
  - Login como Premium y verificar sin límites
- [ ] Dashboard:
  - Ver quick stats con datos
  - Ver quick stats sin datos (empty states)
  - ADHD Insight con al menos 3 tareas completadas

**Cross-browser:**
- [ ] Chrome
- [ ] Firefox
- [ ] Safari

**Mobile:**
- [ ] iOS Safari
- [ ] Chrome Android
- [ ] Sidebar drawer funcional
- [ ] Modales responsive
- [ ] FAB accesible

**Performance:**
- [ ] Lista de 50+ tareas renderiza fluidamente
- [ ] Búsqueda con debounce funciona sin lag
- [ ] Lighthouse score > 90

---

## 📦 Entregables

1. ✅ Dashboard layout completo y responsive
2. ✅ CRUD de tags funcionando (flexible, no rígido)
3. ✅ CRUD de tareas con campos ADHD-friendly
4. ✅ Sistema de filtros y búsqueda
5. ✅ Límites FREE aplicados (15 tareas activas)
6. ✅ Quick stats en dashboard
7. ✅ UI pulida y accesible
8. ✅ Tags multi-select funcionando (relación many-to-many)

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Usuario puede crear y gestionar tags sin límite
2. Usuario puede crear tareas con título, descripción, fecha, prioridad, duración estimada y tags
3. Usuario puede completar tareas y opcionalmente ingresar duración real
4. Usuario puede filtrar y buscar tareas eficientemente
5. Usuario FREE ve límite de 15 tareas activas aplicado
6. Usuario Premium NO ve límites
7. ADHD Insight muestra comparación estimated vs actual (si hay datos)
8. Dashboard es funcional y visualmente atractivo
9. Cero errores críticos
10. Todo funciona en mobile

---

## 📊 Métricas a Medir

- Tiempo promedio para crear primera tarea (target: < 30s)
- Número de tags creados por usuario (target: 3-5)
- Tasa de uso de estimatedDuration (target: > 50%)
- Tasa de completar tareas con actualDuration (target: > 30%)
- Errores en API de tasks/tags
- Performance de lista de tareas (target: < 100ms render)

---

## 🚧 Blockers Potenciales

| Blocker | Probabilidad | Mitigación |
|---------|--------------|------------|
| Many-to-many task_tags compleja | Media | Drizzle tiene buenos helpers para relaciones, revisar docs |
| Performance con 50+ tareas | Media | Implementar pagination o virtualized list si es necesario |
| Date picker UX confusa | Baja | Usar shadcn/ui date picker, testing con usuarios |
| Límite FREE genera fricción | Media | Messaging claro, CTA de upgrade no agresivo |
| ADHD Insight cálculos incorrectos | Baja | Unit tests para función de cálculo |

---

## 🔄 Dependencias

**Depende de:**
- ✅ Sprint 1 (Database schema con tables: tasks, tags, task_tags)
- ✅ Sprint 2 (Authentication, protected routes, user data)

**Bloquea a:**
- Sprint 4 (Subtareas y tareas recurrentes necesitan tasks CRUD)
- Sprint 5 (Calendario necesita tasks con dueDate)
- Sprint 6 (Reminders necesitan tasks)

---

## 📝 Notas Técnicas

### Relación Many-to-Many (task_tags)

**Schema ya implementado:**
```typescript
// src/db/schema/task-tags.ts
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

**Query con tags (Drizzle):**
```typescript
// Obtener tarea con sus tags
const taskWithTags = await db.query.tasks.findFirst({
  where: eq(tasks.id, taskId),
  with: {
    taskTags: {
      with: {
        tag: true,
      },
    },
  },
})

// Retorno:
// {
//   id: '...',
//   title: 'Mi tarea',
//   taskTags: [
//     { tag: { id: '...', name: 'Trabajo', color: '#3b82f6' } },
//     { tag: { id: '...', name: 'Urgente', color: '#ef4444' } }
//   ]
// }
```

**Actualizar tags de una tarea:**
```typescript
async function updateTaskTags(taskId: string, tagIds: string[]) {
  await db.transaction(async (tx) => {
    // 1. Eliminar tags antiguas
    await tx.delete(taskTags).where(eq(taskTags.taskId, taskId))

    // 2. Insertar tags nuevas
    if (tagIds.length > 0) {
      await tx.insert(taskTags).values(
        tagIds.map(tagId => ({ taskId, tagId }))
      )
    }
  })
}
```

### Validación de Límite FREE

```typescript
// src/lib/utils/plan-limits.ts
import { db } from '@/db'
import { tasks, users } from '@/db/schema'
import { and, eq, or, sql } from 'drizzle-orm'

export async function validateTaskLimit(userId: string) {
  // 1. Obtener plan del usuario
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
    columns: { plan: true },
  })

  // Premium = sin límites
  if (user?.plan !== 'free') return true

  // 2. Contar tareas activas (pending + in_progress)
  const result = await db
    .select({ count: sql<number>`count(*)::int` })
    .from(tasks)
    .where(
      and(
        eq(tasks.userId, userId),
        or(
          eq(tasks.status, 'pending'),
          eq(tasks.status, 'in_progress')
        )
      )
    )

  const activeTasksCount = result[0]?.count ?? 0

  if (activeTasksCount >= 15) {
    throw new Error('LIMIT_REACHED')
  }

  return true
}
```

### ADHD Insight Calculation

```typescript
// src/lib/utils/stats.ts
interface Task {
  estimatedDuration?: number
  actualDuration?: number
  completedAt?: Date
}

export function calculateADHDInsight(tasks: Task[]) {
  // Filtrar tareas completadas esta semana con durations
  const oneWeekAgo = new Date()
  oneWeekAgo.setDate(oneWeekAgo.getDate() - 7)

  const completedThisWeek = tasks.filter(
    (task) =>
      task.completedAt &&
      task.completedAt >= oneWeekAgo &&
      task.estimatedDuration &&
      task.actualDuration
  )

  if (completedThisWeek.length === 0) {
    return null // No hay datos
  }

  const totalEstimated = completedThisWeek.reduce(
    (sum, task) => sum + (task.estimatedDuration ?? 0),
    0
  )
  const totalActual = completedThisWeek.reduce(
    (sum, task) => sum + (task.actualDuration ?? 0),
    0
  )

  const accuracy = totalEstimated > 0
    ? Math.round((Math.min(totalEstimated, totalActual) / Math.max(totalEstimated, totalActual)) * 100)
    : 0

  return {
    estimatedHours: (totalEstimated / 60).toFixed(1),
    actualHours: (totalActual / 60).toFixed(1),
    accuracy,
    message: getInsightMessage(accuracy),
  }
}

function getInsightMessage(accuracy: number): string {
  if (accuracy >= 90) return '¡Excelente! Estás estimando muy bien 🎉'
  if (accuracy >= 70) return '¡Bien! Estás mejorando en tus estimaciones'
  if (accuracy >= 50) return 'Vas bien, sigue practicando'
  return 'Las estimaciones mejoran con práctica 💪'
}
```

### Filtros con URL Params (Opcional)

```typescript
// src/lib/hooks/use-task-filters.ts
'use client'

import { useSearchParams, usePathname, useRouter } from 'next/navigation'

export function useTaskFilters() {
  const searchParams = useSearchParams()
  const pathname = usePathname()
  const router = useRouter()

  const filters = {
    status: searchParams.get('status') || 'all',
    priority: searchParams.get('priority') || 'all',
    tagId: searchParams.get('tag') || 'all',
    search: searchParams.get('search') || '',
  }

  const setFilter = (key: string, value: string) => {
    const params = new URLSearchParams(searchParams.toString())

    if (value === 'all' || value === '') {
      params.delete(key)
    } else {
      params.set(key, value)
    }

    router.push(`${pathname}?${params.toString()}`)
  }

  const clearFilters = () => {
    router.push(pathname)
  }

  return { filters, setFilter, clearFilters }
}
```

---

## 🎨 UI/UX Considerations

**Dashboard Layout:**
- Sidebar width: 280px (desktop), full screen (mobile drawer)
- Header height: 64px
- Main content: padding 24px, max-width 1200px centered
- FAB: bottom-right 24px, z-index 50

**Tags en Sidebar:**
- Lista con scroll si > 10 tags
- Hover effect en cada tag
- Color indicator: Círculo 12px a la izquierda del nombre
- Contador de tareas: Badge gris a la derecha

**Task List:**
- Agrupadas por status con headers colapsables
- Card por tarea con shadow-sm, hover:shadow-md
- Checkbox grande (24px) para fácil click
- Tags como badges pequeños (max 3 visibles, "+ N más")
- Due date con color:
  - Gris: Futura
  - Amarillo: Hoy
  - Rojo: Vencida
- Priority indicator:
  - Low: Sin indicador
  - Medium: Barra azul a la izquierda
  - High: Barra amarilla
  - Urgent: Barra roja

**Task Form:**
- Modal width: 600px (desktop), full screen (mobile)
- Campos apilados verticalmente
- Labels claros y prominentes
- Helper text para explicar "Duración estimada"
  - "¿Cuánto tiempo crees que tomará? Ayuda a combatir time blindness"
- Tags: Combobox con búsqueda (mejor que checkboxes múltiples)

**Complete Task Dialog:**
- Small modal: 400px width
- Confetti animation sutil (opcional)
- Input de actualDuration opcional con botones rápidos:
  - [15 min] [30 min] [1h] [2h] o custom
- Si estimatedDuration existe:
  - Mostrar comparison: "Estimaste Xmin, tomó Ymin"
  - Emoji según accuracy: 😊 (bien), 😅 (un poco off), 🤔 (muy off)

**Empty States:**
- Ilustración simple (puede ser icono grande)
- Headline: "No hay tareas todavía"
- Subtext: "Crea tu primera tarea para empezar a organizarte"
- CTA: Botón "Crear tarea"

**Loading States:**
- Skeleton screens para task list
- Spinner en botones al guardar
- Disabled state mientras loading

**Responsive:**
- Breakpoints: 640px (mobile), 768px (tablet), 1024px (desktop)
- Mobile: FAB en lugar de botón en header
- Mobile: Filtros en drawer o bottom sheet

---

## 🔗 Referencias

- [Drizzle Relations](https://orm.drizzle.team/docs/rls)
- [shadcn/ui Date Picker](https://ui.shadcn.com/docs/components/date-picker)
- [TanStack Query Mutations](https://tanstack.com/query/latest/docs/react/guides/mutations)
- [React Hook Form](https://www.react-hook-form.com/)
- [canvas-confetti](https://www.npmjs.com/package/canvas-confetti) (opcional)

---

## ✅ Definition of Done

- [ ] Todas las tareas completadas
- [ ] Testing manual completado sin bugs críticos
- [ ] CRUD de tags funciona en producción
- [ ] CRUD de tareas funciona en producción
- [ ] Filtros y búsqueda funcionan
- [ ] Límites FREE aplicados correctamente
- [ ] Quick stats muestran datos correctos
- [ ] Code review (self-review)
- [ ] Deployed to staging
- [ ] Lighthouse score > 90 en dashboard
- [ ] Accesibilidad: keyboard navigation funciona
- [ ] Mobile responsive verificado
- [ ] No hay errores en Sentry

---

**Creado:** Enero 2, 2026
**Actualizado:** Enero 2, 2026
**Próxima revisión:** Enero 9, 2026
