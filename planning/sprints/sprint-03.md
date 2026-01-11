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

#### 1 Dashboard Layout Base (2h) ✅

**Subtareas:**
- [x] Crear `src/app/dashboard/layout.tsx`
  - Sidebar izquierda (fija en desktop, drawer en mobile)
  - Header superior con user dropdown
  - Main content area responsiva
- [x] Crear `src/components/layout/dashboard-sidebar.tsx`
  - Logo Flime
  - Search input (placeholder, funcional en tarea 6)
  - Sección "Vistas rápidas"
  - Sección "Tags" (lista dinámica)
  - Footer con links (Settings, Upgrade)
- [x] Crear `src/components/layout/dashboard-header.tsx`
  - Saludo: "Buenos días, [Nombre]"
  - User dropdown (reusar de Sprint 2)
  - Botón mobile menu (hamburger)
- [x] Responsive design:
  - Desktop: Sidebar visible siempre
  - Tablet/Mobile: Sidebar como drawer, toggle con botón
- [x] Implementar estado de sidebar (open/closed) con useState

**Criterios de aceptación:**
- ✅ Layout completo y responsive
- ✅ Sidebar funcional en mobile (drawer)
- ✅ Header con user dropdown funcionando
- ✅ Navegación entre secciones fluida
- ✅ Aplicando Coastal Calm palette

**Archivos:**
- `src/app/dashboard/layout.tsx`
- `src/components/layout/dashboard-sidebar.tsx`
- `src/components/layout/dashboard-header.tsx`


---

### 2. Tags CRUD - Backend (2h) ✅

**Descripción:** Server Actions con Supabase para gestión completa de tags

**Subtareas:**
- [x] Crear validaciones en `src/lib/validations/tag.ts`
  ```typescript
  export const createTagSchema = z.object({
    name: z.string().min(1).max(30),
    color: z.string().regex(/^#[0-9A-F]{6}$/i),
  })
  export const updateTagSchema = z.object({
    name: z.string().min(1).max(30).optional(),
    color: z.string().regex(/^#[0-9A-F]{6}$/i).optional(),
  })
  ```
- [x] Crear Server Actions en `src/lib/actions/tags.ts`
  - **getTags()** - Listar tags del usuario autenticado
    - Query: SELECT * FROM tags WHERE user_id = auth.uid() ORDER BY name
    - Return: Array de tags
  - **getTagById(id)** - Obtener tag por ID (con RLS check)
  - **createTag(data)** - Crear nuevo tag
    - Body: { name, color }
    - Validación: Zod schema (name: 1-30 chars, color: hex)
    - Verificar duplicados (case insensitive)
    - Insert en tabla tags
  - **updateTag(id, data)** - Actualizar tag (name, color)
    - Validación con Zod
    - Verificar duplicados (case insensitive)
  - **deleteTag(id)** - Eliminar tag
    - Lógica: CASCADE automático en task_tags elimina relaciones
    - No eliminar tareas asociadas
  - **getTagTaskCount(tagId)** - Contar tareas asociadas a un tag

**Criterios de aceptación:**
- ✅ Server Actions funcionando correctamente
- ✅ Validación con Zod
- ✅ RLS aplicado (usuarios solo ven sus tags)
- ✅ Validación de nombres duplicados
- ✅ Errores manejados apropiadamente
- ✅ TypeScript types correctos
- ✅ Build exitoso (pnpm build)

**Archivos:**
- `src/lib/validations/tag.ts` ✅
- `src/lib/actions/tags.ts` ✅

---

### 3. Tags CRUD - Frontend (2h)

**Descripción:** UI para crear, editar y eliminar tags desde sidebar

**Subtareas:**
- [x] Crear `src/components/tags/tag-list.tsx`
  - Lista de tags con:
    - Color indicator (círculo o badge)
    - Nombre del tag
    - Contador de tareas (ej: "8")
    - Botón edit (icono)
  - Click en tag → filtrar tareas por ese tag
  - Botón "+ Nuevo tag" al final de la lista
- [x] Crear `src/components/tags/tag-form-dialog.tsx`
  - Modal con React Hook Form + Zod
  - Input: Nombre (max 30 chars)
  - Color picker: 12 colores predefinidos + selector custom
  - Modo: Crear o Editar (mismo componente)
  - Botones: Guardar / Cancelar
- [x] Crear `src/components/tags/delete-tag-dialog.tsx`
  - AlertDialog de confirmación
  - Mensaje: "¿Eliminar tag [nombre]? Se desasignará de X tareas"
  - Botones: Cancelar / Eliminar
- [x] Integrar con TanStack Query:
  - useQuery para `getTags()`
  - useMutation para `createTag`, `updateTag`, `deleteTag`
  - Invalidación de cache al mutar
- [x] Toast notifications (Sonner):
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

**Descripción:** Server Actions con Supabase para gestión completa de tareas con campos ADHD-friendly

**Subtareas:**
- [x] Crear validaciones en `src/lib/validations/task.ts`
  ```typescript
  export const createTaskSchema = z.object({
    title: z.string().min(1, 'El título es requerido').max(200),
    description: z.string().max(1000).optional(),
    dueDate: z.string().datetime().optional(),
    priority: z.enum(['low', 'medium', 'high', 'urgent']).default('medium'),
    estimatedDuration: z.number().int().positive().optional(),
    tagIds: z.array(z.string().uuid()).optional(),
  })

  export const updateTaskSchema = z.object({
    title: z.string().min(1).max(200).optional(),
    description: z.string().max(1000).optional(),
    dueDate: z.string().datetime().optional(),
    priority: z.enum(['low', 'medium', 'high', 'urgent']).optional(),
    estimatedDuration: z.number().int().positive().optional(),
    tagIds: z.array(z.string().uuid()).optional(),
  })

  export const completeTaskSchema = z.object({
    actualDuration: z.number().int().positive().optional(),
  })
  ```
- [x] Crear Server Actions en `src/lib/actions/tasks.ts`
  - **getTasks(filters?)** - Listar tareas del usuario
    - Filtros opcionales: status, priority, tagId, search
    - Join con task_tags y tags para incluir tags
    - Order by: dueDate ASC, priority DESC
    - Return: Array de tasks con tags asociados
  - **getTaskById(id)** - Obtener tarea por ID (con tags, con RLS check)
  - **createTask(data)** - Crear nueva tarea
    - Body: { title, description, dueDate, priority, estimatedDuration, tagIds[x] }
    - Validación: Zod schema
    - Verificar límite FREE (15 tareas activas)
    - Insert en tabla tasks
    - Insert relaciones en task_tags (si hay tagIds)
  - **updateTask(id, data)** - Actualizar tarea
    - Validación con Zod
    - Actualizar campos de tasks
    - Sincronizar task_tags (delete antiguas + insert nuevas)
  - **completeTask(id, actualDuration?)** - Completar tarea
    - Update status = 'completed', completedAt = now()
    - Update actualDuration si se proporciona
  - **reopenTask(id)** - Reabrir tarea completada
    - Update status = 'pending', completedAt = null
  - **deleteTask(id)** - Eliminar tarea (hard delete en MVP)
    - CASCADE automático en task_tags elimina relaciones
- [x] Crear helper `src/lib/utils/task-limits.ts`
  - **validateTaskLimit(userId)** - Validar límite FREE
    - Si plan !== 'free' → return true
    - Contar tareas con status IN ('pending', 'in_progress')
    - Si count >= 15 → throw error con código 'LIMIT_REACHED'

**Criterios de aceptación:**
- ✅ Server Actions funcionando correctamente
- ✅ Relación many-to-many con tags funciona
- ✅ Validación con Zod correcta
- ✅ Límite FREE aplicado (15 tareas activas)
- ✅ RLS aplicado correctamente
- ✅ Campos ADHD-friendly (estimatedDuration, actualDuration) funcionando
- ✅ Errores manejados apropiadamente
- ✅ TypeScript types correctos
- ✅ Build exitoso

**Archivos:**
- `src/lib/validations/task.ts`
- `src/lib/actions/tasks.ts`
- `src/lib/utils/task-limits.ts`

---

### 5. Tasks CRUD - Frontend (3h)

**Descripción:** UI para crear, editar, completar y eliminar tareas

**Subtareas:**
- [x] Crear `src/components/tasks/task-form-dialog.tsx`
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
- [x] Crear `src/components/tasks/task-list.tsx`
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
- [x] Crear `src/components/tasks/task-complete-dialog.tsx`
  - Dialog al completar tarea
  - Mensaje: "¡Tarea completada! 🎉"
  - Input opcional: "¿Cuánto tiempo tomó? (minutos)"
  - Si hay estimatedDuration, mostrar comparación:
    - "Estimaste 30 min, tomó 45 min"
  - Botones: Confirmar / Cancelar
  - Confetti animation (opcional, con react-confetti o canvas-confetti)
- [x] Crear `src/components/tasks/delete-task-dialog.tsx`
  - AlertDialog de confirmación
  - Mensaje: "¿Eliminar tarea '[título]'?"
  - Warning: "Esta acción no se puede deshacer"
  - Botones: Cancelar / Eliminar
- [x] Integrar con TanStack Query:
  - useQuery: `getTasks(filters)`
  - useMutation: `createTask`, `updateTask`, `completeTask`, `deleteTask`
  - Optimistic updates para mejor UX
  - Cache invalidation
- [x] FAB (Floating Action Button):
  - Botón "+" fixed bottom-right
  - Click → abrir task-form-dialog en modo crear
- [x] Empty states:
  - Sin tareas: Ilustración + "Crea tu primera tarea"
  - Sin tareas en filtro: "No hay tareas [filtro]"

**Criterios de aceptación:**
- ✅ Crear tarea funciona con todos los campos
- ✅ Editar tarea funciona
- ✅ Completar tarea funciona (con actualDuration opcional)
- ✅ Eliminar tarea funciona con confirmación
- ✅ Lista de tareas se muestra correctamente
-  Tags multi-select funciona
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
- [x] Implementar búsqueda en sidebar:
  - Input con debounce (300ms)
  - Buscar por tasks.title (case insensitive)
  - Mostrar resultados en tiempo real
  - Clear button
- [x] Crear `src/components/tasks/task-filters.tsx`
  - Filtros disponibles:
    - **Status:** Todos / Pending / In Progress / Completed
    - **Priority:** Todas / Baja / Media / Alta / Urgente
    - **Tag:** Todos / [tag específico]
  - UI: Tabs o Select (dependiendo del espacio)
  - State management: URL params o Zustand (preferible url params)
- [x] Vistas rápidas en sidebar:
  - **Hoy:** tasks.dueDate = today
  - **Próximos 7 días:** tasks.dueDate <= today + 7 days
  - Click → aplicar filtro automáticamente
- [x] Integrar filtros con API:
  - Pasar filters a getTasks(filters)
  - Backend aplica WHERE clauses según filtros
- [x] Indicador visual de filtros activos:
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
- `src/lib/hooks/mutations/use-task-filters.ts`
- `src/lib/hooks/queries/use-task-filters.ts`

---

### 7. Límites Freemium (1h)

**Descripción:** Aplicar límites de plan FREE y mostrar CTAs de upgrade

**Subtareas:**
- [x] Validación en backend:
  - Verificar plan del usuario antes de crear tarea
  - Si FREE y >= 15 tareas activas → error 403
  - Error message: "Has alcanzado el límite de 15 tareas activas. Upgradea a Premium para tareas ilimitadas."
- [x] UI: Mostrar límite alcanzado:
  - Dialog al intentar crear tarea (#16):
    - Mensaje: "Límite alcanzado"
    - Explicación: "Plan FREE: 15 tareas activas. Tienes 15/15."
    - CTA: "Upgrade to Premium" → redirect a /pricing
    - Sugerencia: "Completa o archiva tareas existentes"
  - Botón "Cancelar" → cerrar dialog
- [x] Indicador de límite en UI:
  - Badge en sidebar: "15/15 tareas" (FREE)
  - Progress bar visual (opcional)
  - Color: Verde (< 10), Amarillo (10-14), Rojo (15)
- [x] Banner de upgrade (dismissable):
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
- [x] Crear `src/components/dashboard/quick-stats.tsx`
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
- [x] Calcular stats:
  - Opción: Calcular en frontend con datos de getTasks()
  - Usar `src/lib/utils/stats.ts` para cálculos
  - No requiere Server Action adicional (usar getTasks existente)
- [x] Responsive design:
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

- [Supabase JavaScript Client](https://supabase.com/docs/reference/javascript/introduction)
- [Supabase Joins and Nested Tables](https://supabase.com/docs/guides/api/joins-and-nested-tables)
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
**Próxima revisión:** Enero 11, 2026
