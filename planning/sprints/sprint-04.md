# Sprint 4: UI/UX Enhancements & Advanced Task Features

**Duración:** 1 semana (14 horas)
**Inicio:** Enero 10, 2026
**Fin:** Enero 16, 2026
**Status:** 📋 Planificado

---

## 🎯 Objetivo del Sprint

Mejorar significativamente la experiencia de usuario con funcionalidades avanzadas como edición inline, ordenamiento drag-and-drop, selectores de duración y fecha-hora optimizados, subtareas jerárquicas y tareas recurrentes, además de implementar archivado automático y optimizar el diseño responsive de la lista de tareas.

**Cambios clave respecto al plan original:**
- Prioridad en UX/UI sobre nuevas features complejas
- Edición inline en lugar de modales para mejorar flujo de trabajo
- Ordenamiento persistente en BD (no localStorage)
- Archivado automático para tareas antiguas
- Reducción de límite Premium de 15 a 10 tareas

---

## 📋 Tareas

### 1. Duration Picker Component (2h)

**Descripción:** Reemplazar input manual de minutos con un picker intuitivo que soporte múltiples unidades de tiempo

**Subtareas:**
- [ ] Crear `src/components/ui/duration-picker.tsx`
  - Dropdown con opciones predefinidas:
    - Segundos: 30s, 45s, 60s
    - Minutos: 5m, 10m, 15m, 30m, 45m
    - Horas: 1h, 2h, 3h, 4h, 6h, 8h
    - Días: 1d, 2d, 3d, 5d
    - Semanas: 1w, 2w, 3w, 4w
  - Modo "Custom" con:
    - Input numérico
    - Select de unidad (segundos/minutos/horas/días/semanas)
  - Convertir todo a minutos internamente para BD
  - Display formateado en UI (ej: "2h 30m", "1d", "30m")
- [ ] Crear utility `src/lib/utils/duration.ts`
  - `formatDuration(minutes: number): string` - Convertir minutos a display
  - `parseDuration(value: number, unit: string): number` - Convertir a minutos
  - Ejemplos:
    - 120 min → "2h"
    - 90 min → "1h 30m"
    - 1440 min → "1d"
- [ ] Integrar en `task-form-dialog.tsx`
  - Reemplazar input de estimatedDuration con DurationPicker
  - Reemplazar input de actualDuration con DurationPicker
- [ ] Actualizar display en `task-item.tsx`
  - Mostrar duración formateada (no solo "Xm")
  - Tooltip con valor exacto en minutos

**Criterios de aceptación:**
- ✅ Picker muestra opciones predefinidas organizadas por unidad
- ✅ Modo custom permite ingresar valores personalizados
- ✅ Conversión a minutos funciona correctamente
- ✅ Display formateado en lista de tareas es legible
- ✅ Integración con form validation (Zod)
- ✅ UX fluida, mejor que input manual

**Archivos:**
- `src/components/ui/duration-picker.tsx`
- `src/lib/utils/duration.ts`
- `src/components/tasks/task-form-dialog.tsx` (actualizar)
- `src/components/tasks/task-item.tsx` (actualizar)

---

### 2. Date-Time Picker Component (2h)

**Descripción:** Mejorar selector de fecha para incluir hora, similar a Outlook/Google Calendar

**Subtareas:**
- [ ] Revisar schema de BD:
  - Verificar que `tasks.dueDate` es `timestamp with timezone`
  - ✅ Ya está configurado correctamente en schema
- [ ] Crear `src/components/ui/date-time-picker.tsx`
  - Usar shadcn/ui Popover + Calendar
  - Agregar Time selector:
    - Select de hora (00-23)
    - Select de minutos (00, 15, 30, 45)
    - Toggle AM/PM (formato 12h, opcional según locale)
  - Display: "08 ene 2026, 5:00 PM"
  - Botones rápidos:
    - "Hoy a las 9:00"
    - "Mañana a las 9:00"
    - "En 3 días a las 9:00"
    - "Próxima semana"
  - Clear button para quitar fecha
- [ ] Actualizar validación Zod:
  - `dueDate` debe incluir hora
  - Default hora: 09:00 si solo se selecciona fecha
- [ ] Integrar en `task-form-dialog.tsx`
  - Reemplazar date picker actual con date-time-picker
- [ ] Actualizar display en `task-item.tsx`
  - Si es hoy: "Hoy, 5:00 PM"
  - Si es mañana: "Mañana, 9:00 AM"
  - Si es esta semana: "Mié, 3:00 PM"
  - Si es después: "15 ene, 10:00 AM"
  - Tooltip con fecha-hora completa

**Criterios de aceptación:**
- ✅ Picker permite seleccionar fecha Y hora
- ✅ Botones rápidos funcionan correctamente
- ✅ Default hora es 09:00 si solo se elige fecha
- ✅ Display en lista de tareas muestra fecha-hora
- ✅ Zona horaria del usuario se respeta
- ✅ Integración con Zod validation
- ✅ UX similar a Outlook/Google Calendar

**Archivos:**
- `src/components/ui/date-time-picker.tsx`
- `src/components/tasks/task-form-dialog.tsx` (actualizar)
- `src/components/tasks/task-item.tsx` (actualizar)
- `src/lib/validations/task.ts` (actualizar)

---

### 3. Actualizar Límite Premium (0.5h)

**Descripción:** Reducir límite de tareas activas del plan FREE de 15 a 10

**Subtareas:**
- [ ] Actualizar constante en `src/lib/utils/task-limits.ts`
  - Cambiar `FREE_TASK_LIMIT = 15` → `FREE_TASK_LIMIT = 10`
- [ ] Actualizar mensajes en UI:
  - `task-limit-dialog.tsx`: "Plan FREE: 10 tareas activas. Tienes X/10."
  - `upgrade-banner.tsx`: Mostrar si >= 7 tareas (en lugar de >= 10)
  - Badge en sidebar: "X/10 tareas"
- [ ] Actualizar página `/pricing`:
  - Tabla de comparación FREE vs PREMIUM
  - Cambiar "15 tareas activas" → "10 tareas activas"
- [ ] Actualizar documentación si existe

**Criterios de aceptación:**
- ✅ Límite de 10 tareas aplicado en backend
- ✅ UI refleja nuevo límite en todos los lugares
- ✅ Banner de upgrade aparece a partir de 7 tareas
- ✅ Mensajes de error son claros
- ✅ Premium users siguen sin límites

**Archivos:**
- `src/lib/utils/task-limits.ts`
- `src/components/tasks/task-limit-dialog.tsx`
- `src/components/dashboard/upgrade-banner.tsx`
- `src/app/pricing/page.tsx` (si existe)

---

### 4. Habilitar Status "In Progress" (1h)

**Descripción:** Permitir que las tareas se muevan al estado "in_progress"

**Subtareas:**
- [ ] Verificar schema BD:
  - ✅ `tasks.status` ya incluye enum 'in_progress'
- [ ] Crear Server Action en `src/lib/actions/tasks.ts`:
  - `updateTaskStatus(id, status)` - Cambiar status de tarea
  - Validación: Solo permitir transiciones válidas
    - pending → in_progress ✅
    - pending → completed ✅
    - in_progress → completed ✅
    - in_progress → pending ✅
    - completed → pending (reabrir) ✅
- [ ] Actualizar `task-item.tsx`:
  - Agregar botón/acción "Comenzar" en tareas pending
  - Agregar botón/acción "Pausar" en tareas in_progress
  - Dropdown menu con opciones:
    - Si pending: "Comenzar tarea"
    - Si in_progress: "Marcar como pendiente", "Completar"
    - Si completed: "Reabrir"
- [ ] Actualizar `task-list.tsx`:
  - Asegurar que grupo "In Progress" se muestra
  - Icono apropiado para in_progress (PlayCircle)
  - Color distintivo (naranja/amarillo)
- [ ] Agregar indicador visual en task card:
  - Barra lateral de color según status:
    - Pending: Azul
    - In Progress: Naranja
    - Completed: Verde

**Criterios de aceptación:**
- ✅ Usuario puede marcar tarea como "in progress"
- ✅ Usuario puede volver tarea in_progress a pending
- ✅ Grupo "In Progress" visible en lista
- ✅ Indicadores visuales claros (color, icono)
- ✅ Transiciones de estado validadas
- ✅ UI actualizada en tiempo real

**Archivos:**
- `src/lib/actions/tasks.ts` (agregar updateTaskStatus)
- `src/components/tasks/task-item.tsx` (actualizar dropdown)
- `src/components/tasks/task-list.tsx` (verificar grupo)
- `src/lib/hooks/mutations/use-task-mutations.ts` (nuevo hook)

---

### 5. Inline Task Creation (2h)

**Descripción:** Crear tareas directamente en la lista sin abrir modal, mejorando flujo de trabajo

**Subtareas:**
- [ ] Crear `src/components/tasks/inline-task-creator.tsx`
  - Input inline que aparece al final de cada grupo de status
  - Enter → crear tarea rápida (solo título, defaults para resto)
  - Campos inline opcionales (expandibles):
    - Título (siempre visible)
    - Tags (combobox compacto)
    - Due date (date picker compacto)
    - Priority (select compacto)
  - Click fuera → cancelar (si no hay contenido)
  - Botón "+" para expandir campos opcionales
  - Botón "Más opciones" → abrir modal completo
- [ ] Integrar en cada grupo de `task-list.tsx`:
  - Botón "+ Agregar tarea" al final de cada grupo
  - Click → mostrar inline creator
  - Defaults según grupo:
    - Si está en "Pending" → status = pending
    - Si está en "In Progress" → status = in_progress
- [ ] Crear Server Action optimizado:
  - `createQuickTask(title, status)` - Versión simplificada
  - Solo requiere título y status
  - Defaults: priority = medium, sin fecha, sin tags
- [ ] Animación de creación:
  - Fade in de nueva tarea en la lista
  - Auto-scroll si es necesario
- [ ] Mantener FAB y botón header para modal completo:
  - Para usuarios que prefieren form completo desde inicio

**Criterios de aceptación:**
- ✅ Inline creator aparece en cada grupo
- ✅ Enter crea tarea rápida
- ✅ Campos expandibles funcionan
- ✅ "Más opciones" abre modal completo
- ✅ Auto-save al presionar Enter
- ✅ Cancelar funciona correctamente
- ✅ UX fluida, sin fricción
- ✅ Coexiste con FAB/botón header

**Archivos:**
- `src/components/tasks/inline-task-creator.tsx`
- `src/components/tasks/task-list.tsx` (integrar)
- `src/lib/actions/tasks.ts` (createQuickTask)
- `src/lib/hooks/mutations/use-task-mutations.ts` (hook)

---

### 6. Inline Editing (Hover to Edit) (2.5h)

**Descripción:** Editar campos de tarea directamente en la lista mediante hover, sin abrir modal

**Subtareas:**
- [ ] Actualizar `task-item.tsx` con modo editable:
  - Hover sobre tarea → mostrar iconos de edición por campo
  - Click en campo → convertir a input inline
  - Campos editables inline:
    - **Título:** Click → Input text
    - **Tags:** Click → Combobox multi-select
    - **Due date:** Click → Date-time picker compacto
    - **Priority:** Click → Select compacto
    - **Estimated duration:** Click → Duration picker compacto
  - Auto-save al:
    - Presionar Enter
    - Click fuera del campo (blur)
    - Seleccionar valor en picker/select
  - ESC para cancelar cambios
- [ ] Optimistic updates:
  - UI actualiza inmediatamente
  - Rollback si falla el save
  - Loading indicator sutil (spinner pequeño)
- [ ] Validación inline:
  - Si título vacío → restaurar valor anterior
  - Si formato inválido → mostrar error inline
- [ ] Reusar componentes existentes:
  - DateTimePicker (del punto 2)
  - DurationPicker (del punto 1)
  - Tag multi-select (del sprint 3)
- [ ] Mantener opción de editar en modal:
  - Botón "..." → dropdown → "Editar (modal completo)"
  - Para usuarios que prefieren ver todos los campos

**Criterios de aceptación:**
- ✅ Hover muestra indicadores de edición
- ✅ Click en campo habilita edición inline
- ✅ Auto-save funciona correctamente
- ✅ ESC cancela cambios
- ✅ Validación inline funciona
- ✅ Optimistic updates sin glitches
- ✅ UX fluida, rápida
- ✅ Funciona en todos los campos mencionados

**Archivos:**
- `src/components/tasks/task-item.tsx` (refactor mayor)
- `src/components/tasks/inline-fields/` (componentes reutilizables)
  - `inline-title-editor.tsx`
  - `inline-tags-editor.tsx`
  - `inline-date-editor.tsx`
  - `inline-priority-editor.tsx`
  - `inline-duration-editor.tsx`
- `src/lib/hooks/mutations/use-inline-update.ts` (hook optimista)

---

### 7. Drag and Drop Ordering (2h)

**Descripción:** Permitir reordenar tareas manualmente con drag-and-drop, persistiendo en BD

**Subtareas:**
- [ ] Verificar schema BD:
  - ✅ `tasks.order` ya existe (integer, default: 0)
- [ ] Instalar dependencia:
  - `@dnd-kit/core`, `@dnd-kit/sortable`, `@dnd-kit/utilities`
  - Librería moderna, accesible, mejor que react-beautiful-dnd
- [ ] Implementar DnD en `task-list.tsx`:
  - Envolver grupos con DndContext
  - Cada task-item es un SortableItem
  - Handle visual (icono de 6 puntos) al hacer hover
  - Permitir drag solo dentro del mismo grupo de status
  - No permitir drag entre grupos (pending → completed)
- [ ] Server Action: `updateTaskOrder(id, newOrder)`
  - Actualizar tasks.order
  - Lógica: Re-ordenar tasks del mismo status
  - Algoritmo:
    - Tarea movida: order = newOrder
    - Tareas afectadas: order += 1 (si se mueve hacia arriba) o -= 1 (hacia abajo)
  - Transaction para consistencia
- [ ] Optimistic updates:
  - UI reordena inmediatamente
  - Backend sincroniza en background
  - Rollback si falla
- [ ] Loading state:
  - Skeleton mientras se cargan tareas
  - Orden se respeta en primera carga
- [ ] Query actualizado:
  - `getTasks()` debe ordenar por `order ASC` dentro de cada status

**Criterios de aceptación:**
- ✅ Drag and drop funciona fluidamente
- ✅ Solo se puede reordenar dentro del mismo status
- ✅ Orden persiste en BD
- ✅ Orden se respeta al recargar página
- ✅ Optimistic updates funcionan
- ✅ Handle visual es accesible (también con teclado)
- ✅ Performance es buena con 50+ tareas

**Archivos:**
- `src/components/tasks/task-list.tsx` (implementar DnD)
- `src/components/tasks/task-item.tsx` (agregar handle)
- `src/lib/actions/tasks.ts` (updateTaskOrder)
- `src/lib/hooks/mutations/use-task-mutations.ts` (hook)
- `package.json` (dependencias)

---

### 8. Archive Section (1.5h)

**Descripción:** Archivar automáticamente tareas antiguas para no saturar vista principal

**Subtareas:**
- [ ] Definir criterio de archivado automático:
  - Tareas completadas hace más de 30 días → status = 'archived'
  - Ejecutar con cron job o scheduled function (Vercel Cron)
- [ ] Crear Server Action `archiveOldTasks()`:
  - Query: UPDATE tasks SET status = 'archived' WHERE status = 'completed' AND completed_at < NOW() - INTERVAL '30 days'
  - Return: Número de tareas archivadas
- [ ] Actualizar filtros en `task-list.tsx`:
  - Por default: NO mostrar tareas archived
  - Agregar toggle "Mostrar archivadas" (opcional)
- [ ] Crear vista dedicada (opcional):
  - Página `/dashboard/tasks/archive`
  - Lista de tareas archivadas
  - Opción de "Restaurar" (cambiar a pending)
  - Opción de eliminar permanentemente
- [ ] Configurar Vercel Cron (si es posible):
  - Archivo `vercel.json` con cron config
  - Endpoint: `/api/cron/archive-tasks`
  - Schedule: Diario a las 00:00 UTC
  - Alternativamente: Manual trigger desde settings
- [ ] UI: Notificación si hay tareas archivadas:
  - Badge en sidebar: "X tareas archivadas"
  - Click → ir a vista de archivo

**Criterios de aceptación:**
- ✅ Tareas completadas hace >30 días se archivan
- ✅ Archivado automático funciona (cron o manual)
- ✅ Vista principal NO muestra archived por default
- ✅ Usuario puede ver tareas archivadas en sección dedicada
- ✅ Usuario puede restaurar tarea archivada
- ✅ Notificación clara si hay tareas archivadas

**Archivos:**
- `src/lib/actions/tasks.ts` (archiveOldTasks)
- `src/app/api/cron/archive-tasks/route.ts` (cron endpoint)
- `src/app/dashboard/tasks/archive/page.tsx` (vista opcional)
- `src/components/tasks/task-filters.tsx` (toggle)
- `vercel.json` (cron config)

---

### 9. Responsive Task List (1h)

**Descripción:** Optimizar diseño de lista de tareas para mobile y tablet

**Subtareas:**
- [ ] Refactor `task-item.tsx` para responsive:
  - **Desktop (>= 768px):**
    - Layout horizontal (checkbox - título - tags - metadata - actions)
    - Todos los campos visibles
  - **Tablet (640px - 767px):**
    - Layout horizontal compacto
    - Tags limitados a 2 visibles (+ "N más")
    - Metadata en tooltip al hacer hover
  - **Mobile (< 640px):**
    - Layout vertical apilado
    - Checkbox + título en primera fila
    - Tags + due date en segunda fila
    - Priority como barra de color lateral (no texto)
    - Actions en dropdown (icono "...")
- [ ] Optimizar touch targets en mobile:
  - Checkbox mínimo 44x44px
  - Botones y links mínimo 44px height
  - Spacing generoso entre elementos interactivos
- [ ] Optimizar inline editing en mobile:
  - Click en campo (no hover) para editar
  - Pickers optimizados para touch
  - Teclado virtual no oculta campos
- [ ] Optimizar drag and drop en mobile:
  - Long press para iniciar drag
  - Visual feedback claro
  - Haptic feedback si está disponible
- [ ] Testing exhaustivo:
  - iPhone SE (small screen)
  - iPhone 14 Pro
  - iPad
  - Android phones (varios tamaños)

**Criterios de aceptación:**
- ✅ Lista legible y usable en todos los tamaños de pantalla
- ✅ Touch targets adecuados (>= 44px)
- ✅ Inline editing funciona en mobile
- ✅ Drag and drop funciona en mobile
- ✅ No hay overflow horizontal
- ✅ Performance fluida en dispositivos reales
- ✅ Lighthouse score mobile > 85

**Archivos:**
- `src/components/tasks/task-item.tsx` (refactor responsive)
- `src/components/tasks/task-list.tsx` (ajustes)
- Todos los componentes inline-editing (responsive)

---

### 10. Subtasks (Hierarchical) (3h)

**Descripción:** Permitir crear subtareas dentro de tareas, con vista jerárquica

**Subtareas:**
- [ ] Verificar schema BD:
  - ✅ `tasks.parentTaskId` ya existe
- [ ] Actualizar `getTasks()` Server Action:
  - Query recursiva o dos queries:
    - Query 1: Tareas principales (parentTaskId IS NULL)
    - Query 2: Subtareas (parentTaskId IS NOT NULL)
  - Agrupar subtareas con su tarea padre
  - Return: Array de tareas con campo `subtasks[]`
- [ ] UI: Mostrar subtareas en `task-item.tsx`:
  - Tarea padre tiene icono de expand/collapse
  - Click → expandir y mostrar subtareas indentadas
  - Subtareas tienen:
    - Indentación visual (padding-left: 32px)
    - Línea vertical conectando con padre
    - Todos los campos de tarea normal
  - Checkbox de tarea padre:
    - Completar padre NO completa hijos automáticamente
    - Mostrar progreso: "2/5 subtareas completadas"
- [ ] Crear subtarea:
  - Botón "+ Subtarea" en dropdown de tarea padre
  - Abrir inline creator o modal
  - Campo `parentTaskId` se setea automáticamente
  - Subtarea hereda tags del padre (opcional)
- [ ] Editar/eliminar subtarea:
  - Mismo flujo que tarea normal
  - Opción "Convertir en tarea principal" (remover parentTaskId)
- [ ] Mover subtarea a otra tarea:
  - Dropdown → "Mover a..."
  - Selector de tarea padre
  - Validación: No permitir ciclos (tarea no puede ser subtarea de sí misma)
- [ ] Límites:
  - Máximo 2 niveles (tarea → subtarea, NO subtarea → sub-subtarea)
  - Validación en backend

**Criterios de aceptación:**
- ✅ Usuario puede crear subtareas
- ✅ Subtareas se muestran indentadas bajo tarea padre
- ✅ Expand/collapse funciona
- ✅ Progress de subtareas visible en tarea padre
- ✅ Completar padre NO completa hijos
- ✅ Subtareas tienen todas las funcionalidades de tareas
- ✅ Máximo 2 niveles de jerarquía
- ✅ UI jerárquica clara y accesible

**Archivos:**
- `src/lib/actions/tasks.ts` (actualizar getTasks, agregar moveSubtask)
- `src/components/tasks/task-item.tsx` (soporte subtareas)
- `src/components/tasks/subtask-creator.tsx` (inline creator)
- `src/lib/validations/task.ts` (validar parentTaskId)

---

### 11. Recurring Tasks (Premium) (2.5h)

**Descripción:** Tareas que se repiten automáticamente según un patrón (feature Premium)

**Subtareas:**
- [ ] Verificar schema BD:
  - ✅ `tasks.isRecurring` (boolean)
  - ✅ `tasks.recurrencePattern` (text, RRULE format)
- [ ] Crear UI para recurrencia en `task-form-dialog.tsx`:
  - Toggle "Repetir tarea" (solo visible para Premium)
  - Si FREE intenta activar → mostrar upgrade dialog
  - Opciones de patrón:
    - **Diario:** Cada N días
    - **Semanal:** Cada N semanas en días específicos (L, M, X, J, V, S, D)
    - **Mensual:** Día N de cada mes o "Primer lunes", etc.
    - **Anual:** Cada año en fecha específica
  - Selector visual (no raw RRULE):
    - Radio buttons para tipo
    - Inputs según tipo seleccionado
  - Preview: "Se repite cada semana los lunes"
- [ ] Crear validación Zod:
  - `recurrencePattern` debe ser RRULE válido
  - Solo Premium puede setear `isRecurring = true`
- [ ] Backend: Generar instancias de tareas recurrentes:
  - Server Action: `generateRecurringInstances(taskId)`
  - Lógica:
    - Parsear RRULE con librería `rrule`
    - Generar próximas N instancias (ej: próximos 30 días)
    - Crear tareas "hijas" con:
      - Mismo título, descripción, tags, priority, estimatedDuration
      - Campo `parentTaskId` apuntando a tarea recurrente original
      - `dueDate` según patrón
      - `isRecurring = false` (las instancias no son recurrentes)
  - Ejecutar:
    - Al crear/editar tarea recurrente
    - Diariamente con cron job (para generar nuevas instancias)
- [ ] Instalar dependencia:
  - `rrule` - Librería para parsear y generar recurrencias (RFC 5545)
- [ ] UI: Indicar tarea recurrente:
  - Icono de "repeat" en task card
  - Tooltip: "Se repite [patrón]"
- [ ] Completar instancia:
  - Completar solo afecta esa instancia
  - NO afecta tarea recurrente padre
  - NO detiene generación de futuras instancias
- [ ] Editar tarea recurrente:
  - Modal pregunta: "¿Editar esta instancia o todas las futuras?"
  - Si "Esta": Editar solo la instancia (desvincular de padre)
  - Si "Todas": Editar tarea padre y regenerar instancias futuras
- [ ] Eliminar tarea recurrente:
  - Modal pregunta: "¿Eliminar esta instancia o toda la serie?"
  - Si "Esta": Eliminar instancia
  - Si "Serie completa": Eliminar padre y todas las instancias
- [ ] Cron job:
  - Endpoint: `/api/cron/generate-recurring-tasks`
  - Schedule: Diario a las 00:00 UTC
  - Lógica: Para cada tarea con `isRecurring = true`, generar instancias faltantes

**Criterios de aceptación:**
- ✅ Solo Premium puede crear tareas recurrentes
- ✅ FREE ve upgrade dialog al intentar
- ✅ Selector de patrón es intuitivo (no requiere saber RRULE)
- ✅ Instancias se generan correctamente según patrón
- ✅ Completar instancia no afecta serie
- ✅ Editar/eliminar pregunta "esta instancia" vs "toda la serie"
- ✅ Cron job genera instancias futuras diariamente
- ✅ Icono visual indica tarea recurrente

**Archivos:**
- `src/lib/actions/tasks.ts` (generateRecurringInstances)
- `src/components/tasks/task-form-dialog.tsx` (UI recurrencia)
- `src/components/tasks/recurrence-picker.tsx` (selector patrón)
- `src/lib/utils/rrule-helpers.ts` (helpers RRULE)
- `src/app/api/cron/generate-recurring-tasks/route.ts`
- `src/lib/validations/task.ts` (validar recurrencia)
- `package.json` (agregar rrule)

---

## 🧪 Testing

**Manual Testing:**
- [ ] Duration Picker:
  - Seleccionar opciones predefinidas
  - Usar modo custom
  - Verificar conversión a minutos
  - Verificar display formateado
- [ ] Date-Time Picker:
  - Seleccionar fecha y hora
  - Usar botones rápidos
  - Verificar formato de display
  - Verificar zona horaria correcta
- [ ] Límite Premium:
  - Crear 10 tareas como FREE
  - Intentar crear tarea #11 (debe fallar)
  - Ver banner de upgrade
  - Login como Premium y verificar sin límite
- [ ] Status "In Progress":
  - Mover tarea pending → in_progress
  - Mover in_progress → completed
  - Mover in_progress → pending
  - Verificar visuales (color, icono)
- [ ] Inline Creation:
  - Crear tarea rápida con Enter
  - Expandir campos opcionales
  - Usar "Más opciones" para modal
  - Crear en diferentes grupos de status
- [ ] Inline Editing:
  - Editar cada campo inline (título, tags, fecha, priority, duration)
  - Verificar auto-save
  - Cancelar con ESC
  - Verificar validación inline
- [ ] Drag and Drop:
  - Reordenar tareas dentro del mismo status
  - Verificar que orden persiste al recargar
  - Intentar drag entre status diferentes (debe bloquearse)
  - Testing con 50+ tareas
- [ ] Archive:
  - Completar tarea y modificar `completed_at` a -31 días (manual en BD)
  - Ejecutar `archiveOldTasks()`
  - Verificar que tarea desaparece de vista principal
  - Ver tareas archivadas en sección dedicada
  - Restaurar tarea
- [ ] Responsive:
  - Mobile (< 640px)
  - Tablet (640-767px)
  - Desktop (>= 768px)
  - Touch targets
  - Inline editing en mobile
  - Drag and drop en mobile
- [ ] Subtasks:
  - Crear subtarea desde tarea padre
  - Expandir/colapsar
  - Completar subtarea
  - Verificar progreso en tarea padre
  - Mover subtarea a otra tarea
  - Convertir en tarea principal
- [ ] Recurring Tasks (Premium):
  - Crear tarea recurrente diaria
  - Crear tarea recurrente semanal
  - Verificar generación de instancias
  - Completar instancia
  - Editar "esta instancia"
  - Editar "toda la serie"
  - Eliminar instancia
  - Eliminar serie completa
  - FREE intenta crear recurrente (debe mostrar upgrade)

**Cross-browser:**
- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge

**Mobile Testing:**
- [ ] iOS Safari
- [ ] Chrome Android
- [ ] Varios tamaños de pantalla

**Performance:**
- [ ] Lista con 100+ tareas renderiza sin lag
- [ ] Drag and drop fluido
- [ ] Inline editing responsive
- [ ] Lighthouse score > 85 (desktop y mobile)

---

## 📦 Entregables

1. ✅ Duration Picker con múltiples unidades funcionando
2. ✅ Date-Time Picker con hora integrado
3. ✅ Límite Premium actualizado a 10 tareas
4. ✅ Status "In Progress" habilitado y visible
5. ✅ Inline task creation en lista
6. ✅ Inline editing (hover to edit) en todos los campos
7. ✅ Drag and drop ordering persistente en BD
8. ✅ Archive section para tareas antiguas
9. ✅ Lista de tareas completamente responsive
10. ✅ Subtasks con jerarquía de 2 niveles
11. ✅ Recurring tasks (Premium only) con RRULE

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Duration picker es más usable que input manual
2. Date-time picker permite seleccionar hora fácilmente
3. Límite de 10 tareas aplicado correctamente para FREE
4. Usuarios pueden mover tareas a "in progress" sin fricción
5. Inline creation reduce tiempo de creación de tareas
6. Inline editing permite edición rápida sin abrir modales
7. Drag and drop funciona fluidamente y orden persiste
8. Tareas antiguas se archivan automáticamente
9. Lista es completamente usable en mobile
10. Subtasks funcionan y se muestran jerárquicamente
11. Recurring tasks generan instancias correctamente (Premium)
12. Cero errores críticos
13. Performance no se degrada con features nuevas
14. UX general ha mejorado significativamente

---

## 📊 Métricas a Medir

- Tiempo promedio para crear tarea (target: < 15s con inline creator)
- % de uso de inline editing vs modal (target: > 60% inline)
- % de uso de drag and drop (target: > 40% de usuarios lo usan)
- Tasa de upgrade FREE → Premium (target: +5% por límite reducido)
- % de tareas con estimated duration (target: > 70% con nuevo picker)
- Errores en nuevas features
- Performance de lista con 100+ tareas (target: < 200ms render)
- Lighthouse score mobile (target: > 85)

---

## 🚧 Blockers Potenciales

| Blocker | Probabilidad | Mitigación |
|---------|--------------|------------|
| DnD performance con 100+ tareas | Media | Virtualización con react-window si es necesario |
| Inline editing confuso en mobile | Media | Testing exhaustivo con usuarios reales |
| RRULE parsing complejo | Media | Usar librería rrule bien documentada |
| Cron jobs no disponibles en Vercel free | Alta | Implementar trigger manual como fallback |
| Subtasks lógica recursiva compleja | Baja | Limitar a 2 niveles, queries simples |
| Date-time picker UX | Media | Inspirarse en Google Calendar, testing |

---

## 🔄 Dependencias

**Depende de:**
- ✅ Sprint 1 (Database schema con tasks.order, parentTaskId, isRecurring, recurrencePattern)
- ✅ Sprint 2 (Authentication, plan detection)
- ✅ Sprint 3 (Tasks CRUD, tags, task-form-dialog, task-item, task-list)

**Bloquea a:**
- Sprint 5 (Calendar view necesita due date con hora)
- Sprint 6 (Reminders necesitan recurring tasks)

---

## 🔗 Referencias

- [@dnd-kit Documentation](https://docs.dndkit.com/)
- [RRULE RFC 5545](https://www.rfc-editor.org/rfc/rfc5545)
- [rrule.js Library](https://github.com/jakubroztocil/rrule)
- [Vercel Cron Jobs](https://vercel.com/docs/cron-jobs)
- [React Window (Virtualization)](https://react-window.vercel.app/)
- [shadcn/ui Date Picker](https://ui.shadcn.com/docs/components/date-picker)
- [Google Calendar UX Patterns](https://calendar.google.com)

---

## 📝 Notas Técnicas

### RRULE Example

```typescript
// Tarea que se repite cada lunes
const rrule = 'FREQ=WEEKLY;BYDAY=MO'

// Tarea que se repite cada 2 semanas
const rrule = 'FREQ=WEEKLY;INTERVAL=2'

// Tarea que se repite el día 1 de cada mes
const rrule = 'FREQ=MONTHLY;BYMONTHDAY=1'

// Parsear y generar instancias
import { RRule } from 'rrule'

const rule = RRule.fromString(rrule)
const instances = rule.between(startDate, endDate) // Array de fechas
```

### Duration Conversion

```typescript
// src/lib/utils/duration.ts

export function formatDuration(minutes: number): string {
  if (minutes < 60) return `${minutes}m`
  if (minutes < 1440) {
    const hours = Math.floor(minutes / 60)
    const mins = minutes % 60
    return mins > 0 ? `${hours}h ${mins}m` : `${hours}h`
  }
  const days = Math.floor(minutes / 1440)
  return `${days}d`
}

export function parseDuration(value: number, unit: 'seconds' | 'minutes' | 'hours' | 'days' | 'weeks'): number {
  const conversions = {
    seconds: value / 60,
    minutes: value,
    hours: value * 60,
    days: value * 1440,
    weeks: value * 10080,
  }
  return Math.round(conversions[unit])
}
```

### Drag and Drop Order Update

```typescript
// src/lib/actions/tasks.ts

export async function updateTaskOrder(taskId: string, newOrder: number, status: string) {
  const supabase = await createClient()
  const { data: { user } } = await supabase.auth.getUser()
  if (!user) return { error: 'No autenticado' }

  // Transaction para consistencia
  const db = getDb()

  await db.transaction(async (tx) => {
    // Update tarea movida
    await tx.update(tasks)
      .set({ order: newOrder })
      .where(eq(tasks.id, taskId))

    // Re-ordenar otras tareas del mismo status
    // (Simplificado - en producción usar lógica más robusta)
    const tasksInStatus = await tx.select()
      .from(tasks)
      .where(and(
        eq(tasks.userId, user.id),
        eq(tasks.status, status),
        ne(tasks.id, taskId)
      ))
      .orderBy(tasks.order)

    // Renumerar orders
    for (let i = 0; i < tasksInStatus.length; i++) {
      await tx.update(tasks)
        .set({ order: i >= newOrder ? i + 1 : i })
        .where(eq(tasks.id, tasksInStatus[i].id))
    }
  })

  return { success: true }
}
```

---

## ✅ Definition of Done

- [ ] Todas las tareas completadas
- [ ] Testing manual completado sin bugs críticos
- [ ] Duration picker funcionando en producción
- [ ] Date-time picker funcionando en producción
- [ ] Límite de 10 tareas aplicado correctamente
- [ ] Status "in progress" visible y funcional
- [ ] Inline creation funcionando
- [ ] Inline editing funcionando en todos los campos
- [ ] Drag and drop funcionando y persistiendo
- [ ] Archive section funcionando
- [ ] Lista responsive en todos los tamaños
- [ ] Subtasks funcionando jerárquicamente
- [ ] Recurring tasks funcionando (Premium)
- [ ] Cron jobs configurados (o fallback manual)
- [ ] Code review (self-review)
- [ ] Deployed to staging
- [ ] Lighthouse score > 85 (desktop y mobile)
- [ ] Accesibilidad: keyboard navigation en todas las features
- [ ] No hay errores en Sentry
- [ ] Performance aceptable con 100+ tareas

---

**Creado:** Enero 11, 2026
**Actualizado:** Enero 11, 2026
**Próxima revisión:** Enero 17, 2026
