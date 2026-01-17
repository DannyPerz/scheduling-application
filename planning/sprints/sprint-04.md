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

### 1. Duration Picker Component (2h) ✅ COMPLETADO

**Descripción:** ~~Reemplazar input manual de minutos con un picker intuitivo que soporte múltiples unidades de tiempo~~ **Implementado con input de texto estilo Jira/ClickUp** - Parser de texto con formato natural (ej: "30m", "2h", "1d").

**Cambios de implementación respecto al plan original:**
- **❌ Dropdown con opciones predefinidas** → **✅ Text input con parsing inteligente**
- **❌ Modo "Custom" con select de unidad** → **✅ Parsing automático de texto natural**
- **✅ Agregado:** Help popover con ejemplos e instrucciones
- **✅ Agregado:** Auto-formateo al perder foco (blur)
- **✅ Agregado:** Parsing en tiempo real (real-time feedback)
- **✅ Agregado:** Soporte para duraciones compuestas (ej: "1h 30m")

**Subtareas:**
- [x] Crear `src/components/ui/duration-picker.tsx`
  - ✅ Input de texto con icono de reloj
  - ✅ Parsing de múltiples formatos:
    - Segundos: "30s", "45sec", "60 segundos"
    - Minutos: "5m", "10min", "15 minutos"
    - Horas: "1h", "2hr", "3 horas"
    - Días: "1d", "2day", "3 días"
    - Semanas: "1w", "2week", "3 semanas", "1sem"
    - Compuesto: "1h 30m", "2d 4h"
    - Solo número: "90" → se interpreta como minutos
  - ✅ Help popover opcional con ejemplos visuales
  - ✅ Auto-formateo al blur (ej: "90" → "1h 30m")
  - ✅ Conversión a minutos internamente para BD
  - ✅ Display formateado en UI (ej: "2h", "1h 30m", "1d")
- [x] Crear utility `src/lib/utils/duration.ts`
  - ✅ `formatDuration(minutes: number): string` - Convertir minutos a display
  - ✅ `parseDurationString(input: string): number | null` - Parsear texto a minutos
  - ✅ `parseDuration(value: number, unit: string): number` - Convertir unidades a minutos
  - ✅ `minutesToUnit(minutes: number, unit: string): number` - Convertir minutos a unidad
  - ✅ Regex inteligente con orden de precedencia para evitar conflictos (sem antes que s, min antes que m)
  - Ejemplos:
    - 120 min → "2h"
    - 90 min → "1h 30m"
    - 1440 min → "1d"
    - "30m" → 30
    - "2h" → 120
    - "1d" → 1440
    - "1h 30m" → 90
    - "1sem" → 10080 (7 días)
- [x] Integrar en `task-form-dialog.tsx`
  - ✅ Reemplazado input de estimatedDuration con DurationPicker
  - ✅ Label: "Duración estimada"
  - ✅ Help popover habilitado (`showHelp={true}`)
- [x] Integrar en `task-complete-dialog.tsx`
  - ✅ Reemplazado input de actualDuration con DurationPicker
  - ✅ Label: "Duración real (opcional)"
  - ✅ Help popover habilitado (`showHelp={true}`)
  - ✅ Botón "Omitir" cambiado a "Cancelar"
  - ✅ actualDuration ahora es opcional (no obligatorio)
- [x] Actualizar display en `task-item.tsx`
  - ✅ Mostrar duración formateada (no solo "Xm")
  - ✅ Tooltip con valor exacto en minutos

**Criterios de aceptación:**
- ✅ Input de texto natural es más rápido que dropdown
- ✅ Parsing funciona con múltiples formatos y unidades
- ✅ Parsing de "1sem"/"1semana" funciona correctamente (no se confunde con "1s")
- ✅ Help popover enseña al usuario cómo usar el input
- ✅ Conversión a minutos funciona correctamente
- ✅ Display formateado en lista de tareas es legible
- ✅ Integración con React Hook Form (Controller)
- ✅ Auto-formateo al blur mejora UX
- ✅ Real-time parsing da feedback instantáneo
- ✅ UX fluida, mejor que input manual
- ✅ Funciona tanto en task-form como en task-complete dialog

**Archivos modificados:**
- ✅ `src/components/ui/duration-picker.tsx` (creado)
- ✅ `src/lib/utils/duration.ts` (creado)
- ✅ `src/components/tasks/task-form-dialog.tsx` (actualizado)
- ✅ `src/components/tasks/task-complete-dialog.tsx` (actualizado)
- ✅ `src/components/tasks/task-item.tsx` (actualizado - display formateado)

**Lecciones aprendidas:**
1. **Text input > Dropdown:** Input de texto estilo Jira/ClickUp es más rápido y flexible que dropdown con opciones predefinidas
2. **Regex order matters:** Al parsear con `startsWith()`, siempre verificar patrones más largos PRIMERO (sem antes de s, min antes de m)
3. **Help popover crucial:** Los usuarios necesitan ejemplos visuales para entender el formato de texto
4. **Auto-formateo en blur:** Mejora la UX al "limpiar" el input después de escribir
5. **Real-time parsing:** Dar feedback instantáneo es mejor que esperar a blur/submit

---

### 2. Date-Time Picker Component (2h) ✅ COMPLETADO

**Descripción:** Mejorar selector de fecha para incluir hora, similar a Outlook/Google Calendar

**Cambios de implementación respecto al plan original:**
- **✅ Agregado:** Soporte para rango de fechas (start date + end date)
- **✅ Agregado:** Time picker con select cada 15 minutos (mejor UX que botones)
- **✅ Agregado:** Formato 12h con AM/PM para mejor legibilidad
- **✅ Agregado:** Validación automática: end date no puede ser antes de start date
- **✅ Agregado:** Clear button individual para cada fecha
- **✅ Agregado:** Display inteligente con "Hoy", "Mañana"
- **❌ Removido:** Botones rápidos predefinidos (no se implementaron)

**Subtareas:**
- [x] Revisar schema de BD:
  - ✅ `tasks.startDate` es `timestamp with timezone`
  - ✅ `tasks.dueDate` es `timestamp with timezone`
  - ✅ Ya está configurado correctamente en schema
- [x] Crear `src/components/ui/date-time-picker.tsx`
  - ✅ Usar shadcn/ui Popover + Calendar
  - ✅ Agregar Time selector:
    - ✅ Select de hora en formato 12h (1-12 AM/PM)
    - ✅ Select de minutos (intervalos de 15: 00, 15, 30, 45)
    - ✅ Generación dinámica de opciones de tiempo
  - ✅ Display inteligente:
    - "Hoy, 5:00 PM" si es hoy
    - "Mañana, 9:00 AM" si es mañana
    - "08 ene 2026, 5:00 PM" para otras fechas
  - ✅ Soporte para start date y end date
  - ✅ Clear button para quitar fechas
  - ✅ Validación: end date debe ser después de start date
  - ✅ Default hora: 9:00 AM cuando se selecciona fecha sin hora
- [x] Actualizar validación Zod:
  - ✅ `startDate` y `dueDate` incluyen hora (datetime with offset)
  - ✅ Validación: startDate debe ser anterior a dueDate
  - ✅ Soporte para valores null y empty string
- [x] Integrar en `task-form-dialog.tsx`
  - ✅ Reemplazado date picker con DateTimePicker
  - ✅ Soporte para start date y due date simultáneos
  - ✅ Nested Controller pattern para manejar ambas fechas
  - ✅ Sugerencia automática de duración basada en ventana de tiempo
- [x] Actualizar display en `task-item.tsx`
  - ✅ Display muestra rango: "8 ene → 12 ene"
  - ✅ Display de fecha única si solo hay due date
  - ✅ Indicador visual si fecha está vencida (fondo rojo)
  - ✅ Formato corto legible: "d MMM" (ej: "8 ene")

**Criterios de aceptación:**
- ✅ Picker permite seleccionar fecha Y hora
- ✅ Picker permite seleccionar rango (start → end)
- ✅ Default hora es 09:00 si solo se elige fecha
- ✅ Display en lista muestra rango de fechas
- ✅ Zona horaria del usuario se respeta (timestamp with timezone)
- ✅ Integración con Zod validation funcionando
- ✅ UX similar a Outlook/Google Calendar
- ✅ Validación de end date >= start date
- ✅ Time picker con intervalos de 15 minutos
- ✅ Formato 12h con AM/PM

**Archivos modificados:**
- ✅ `src/components/ui/date-time-picker.tsx` (creado)
- ✅ `src/components/tasks/task-form-dialog.tsx` (actualizado - DateTimePicker integrado)
- ✅ `src/components/tasks/task-item.tsx` (actualizado - display de rango de fechas)
- ✅ `src/lib/validations/task.ts` (actualizado - validación datetime con offset)
- ✅ `src/db/schema/tasks.ts` (ya tenía timestamp with timezone)

**Lecciones aprendidas:**
1. **Rango de fechas crucial:** Start + end date da más contexto que solo due date
2. **Time picker con select:** Select cada 15 minutos es más rápido que inputs separados
3. **Formato 12h AM/PM:** Más familiar para usuarios que formato 24h
4. **Validación en tiempo real:** Prevenir end date antes de start date mejora UX
5. **Display inteligente:** "Hoy", "Mañana" son más legibles que fechas completas
6. **Nested Controllers:** Pattern útil para manejar campos relacionados en React Hook Form

---

### 3. Actualizar Límite Premium (0.5h) ✅ COMPLETADO

**Descripción:** Reducir límite de tareas activas del plan FREE de 15 a 10 (contando solo tareas pendientes y en progreso)

**Subtareas:**
- [x] Actualizar límite en `src/lib/utils/task-limits.ts`
  - ✅ Cambiado límite de 15 a 10 tareas activas
  - ✅ Actualizado comentario de documentación
- [x] Actualizar constante en `src/lib/utils/plan-limits.ts`
  - ✅ `maxActiveTasks: 15` → `maxActiveTasks: 10`
  - ✅ Actualizado comentario de indicador de color (70% threshold para 7+ tareas)
- [x] Actualizar umbral del banner de upgrade
  - ✅ Cambiado de `>= 10` a `>= 7` tareas
  - ✅ Banner aparece cuando el usuario tiene 7+ tareas (70% del límite)
- [x] Actualizar mensajes en UI:
  - ✅ `task-limit-dialog.tsx`: "Plan FREE: 10 tareas activas"
  - ✅ `src/lib/actions/tasks.ts`: Mensaje de error actualizado a 10 tareas
- [x] Verificar otros archivos
  - ✅ No existe página de pricing todavía
  - ✅ No hay otros archivos con el límite hardcodeado

**Criterios de aceptación:**
- ✅ Límite de 10 tareas aplicado en backend
- ✅ UI refleja nuevo límite en todos los lugares
- ✅ Banner de upgrade aparece a partir de 7 tareas (70% del límite)
- ✅ Mensajes de error son claros y consistentes
- ✅ Premium users siguen sin límites
- ✅ Indicador de color muestra naranja a partir de 7 tareas

**Archivos modificados:**
- ✅ `src/lib/utils/task-limits.ts` (límite 15 → 10)
- ✅ `src/lib/utils/plan-limits.ts` (constante y umbral del banner)
- ✅ `src/components/tasks/task-limit-dialog.tsx` (texto del diálogo)
- ✅ `src/lib/actions/tasks.ts` (mensaje de error)
- ✅ `src/components/layout/dashboard-sidebar.tsx` (progress bar threshold 10 → 7)

**Lecciones aprendidas:**
1. **Límite más restrictivo incentiva upgrade:** 10 tareas es suficiente para uso básico pero incentiva a usuarios activos a hacer upgrade
2. **Banner early warning:** Mostrar el banner a partir de 70% del límite (7 tareas) da tiempo al usuario para decidir
3. **Centralización de constantes:** Tener límites centralizados en `plan-limits.ts` facilita futuros cambios

---

### 4. Habilitar Status "In Progress" (1h)

**Descripción:** Permitir que las tareas se muevan al estado "in_progress"

**Subtareas:**
- [x] Verificar schema BD:
  - ✅ `tasks.status` ya incluye enum 'in_progress'
- [x] Crear Server Action en `src/lib/actions/tasks.ts`:
  - `updateTaskStatus(id, status)` - Cambiar status de tarea
  - Validación: Solo permitir transiciones válidas
    - pending → in_progress ✅
    - pending → completed ✅
    - in_progress → completed ✅
    - in_progress → pending ✅
    - completed → pending (reabrir) ✅
- [x] Actualizar `task-item.tsx`:
  - Agregar botón/acción "Comenzar" en tareas pending
  - Agregar botón/acción "Pausar" en tareas in_progress
  - Dropdown menu con opciones:
    - Si pending: "Comenzar tarea"
    - Si in_progress: "Marcar como pendiente", "Completar"
    - Si completed: "Reabrir"
- [x] Actualizar `task-list.tsx`:
  - Asegurar que grupo "In Progress" se muestra
  - Icono apropiado para in_progress (PlayCircle)
  - Color distintivo (naranja/amarillo)
- [x] Agregar indicador visual en task card:
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
- [x] Crear `src/components/tasks/inline-task-creator.tsx`
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
- [x] Integrar en cada grupo de `task-list.tsx`:
  - Botón "+ Agregar tarea" al final de cada grupo
  - Click → mostrar inline creator
  - Defaults según grupo:
    - Si está en "Pending" → status = pending
    - Si está en "In Progress" → status = in_progress
- [x] Crear Server Action optimizado:
  - `createQuickTask(title, status)` - Versión simplificada
  - Solo requiere título y status
  - Defaults: priority = medium, sin fecha, sin tags
- [x] Animación de creación:
  - Fade in de nueva tarea en la lista
  - Auto-scroll si es necesario
- [x] Mantener FAB y botón header para modal completo:
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
- [x] Actualizar `task-item.tsx` con modo editable:
  - Hover sobre tarea → mostrar iconos de edición por campo
  - Click en campo → convertir a input inline
  - Campos editables inline:
    - **Título:** Click → Input text
    - **Tags:** Click → Combobox multi-select(reutlizar el que ya tenemos en nueva tarea)
    - **Due date:** Click → Date-time picker compacto (reutilizar el que ya tenemos)
    - **Priority:** Click → Select compacto (reutilzar el que ya tenemos)
    - **Estimated duration:** Click → Duration picker compacto (reuqtilziar el que ya tenemos)
  - Auto-save al:
    - Presionar Enter
    - Click fuera del campo (blur)
    - Seleccionar valor en picker/select
  - ESC para cancelar cambios
- [x] Optimistic updates:
  - UI actualiza inmediatamente
  - Rollback si falla el save
  - Loading indicator sutil (spinner pequeño)
- [x] Validación inline:
  - Si título vacío → restaurar valor anterior
  - Si formato inválido → mostrar error inline
- [x] Reusar componentes existentes:
  - DateTimePicker (del punto 2)
  - DurationPicker (del punto 1)
  - Tag multi-select (del sprint 3)
- [x] Mantener opción de editar en modal:
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

### 7. Responsive Task List (1h)

**Descripción:** Optimizar diseño de lista de tareas para mobile y tablet

**Subtareas:**
- [x] Refactor `task-item.tsx` para responsive:
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
- [x] Optimizar touch targets en mobile:
  - Checkbox mínimo 44x44px
  - Botones y links mínimo 44px height
  - Spacing generoso entre elementos interactivos
- [x] Optimizar inline editing en mobile:
  - Click en campo (no hover) para editar
  - Pickers optimizados para touch
  - Teclado virtual no oculta campos
- [x] Optimizar drag and drop en mobile:
  - Long press para iniciar drag
  - Visual feedback claro
  - Haptic feedback si está disponible
- [x] Testing exhaustivo:
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

### 8. Recurring Tasks (Premium) (2.5h)

**Descripción:** Tareas que se repiten automáticamente según un patrón (feature Premium)

**Subtareas:**
- [x] Verificar schema BD:
  - ✅ `tasks.isRecurring` (boolean)
  - ✅ `tasks.recurrencePattern` (text, RRULE format)
- [x] Crear UI para recurrencia en `task-form-dialog.tsx`:
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
- [x] Crear validación Zod:
  - `recurrencePattern` debe ser RRULE válido
  - Solo Premium puede setear `isRecurring = true`
- [x] Backend: Generar instancias de tareas recurrentes:
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
- [x] Instalar dependencia:
  - `rrule` - Librería para parsear y generar recurrencias (RFC 5545)
- [x] UI: Indicar tarea recurrente:
  - Icono de "repeat" en task-item.tsx
  - Tooltip: "Se repite [patrón]"
- [x] Completar instancia:
  - Completar solo afecta esa instancia
  - NO afecta tarea recurrente padre
  - NO detiene generación de futuras instancias
- [x] Editar tarea recurrente:
  - Modal pregunta: "¿Editar esta instancia o todas las futuras?"
  - Si "Esta": Editar solo la instancia (desvincular de padre)
  - Si "Todas": Editar tarea padre y regenerar instancias futuras
- [x] Eliminar tarea recurrente:
  - Modal pregunta: "¿Eliminar esta instancia o toda la serie?"
  - Si "Esta": Eliminar instancia
  - Si "Serie completa": Eliminar padre y todas las instancias
- [x] Cron job:
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
- [x] Duration Picker:
  - Usar modo custom
  - Verificar conversión a minutos
  - Verificar display formateado
- [x] Date-Time Picker:
  - Seleccionar fecha y hora
  - Usar botones rápidos
  - Verificar formato de display
  - Verificar zona horaria correcta
- [x] Límite Premium:
  - Login como FREE y crear 10 tareas
  - Intentar crear tarea #11 (debe fallar)
  - Ver banner de upgrade
  - Login como Premium y verificar sin límite
- [x] Status "In Progress":
  - Mover tarea pending → in_progress
  - Mover in_progress → completed
  - Mover in_progress → pending
  - Verificar visuales (color, icono)
- [x] Inline Creation:
  - Crear tarea rápida con Enter
  - Expandir campos opcionales
  - Usar "Más opciones" para modal
  - Crear en diferentes grupos de status
- [x] Inline Editing:
  - Editar cada campo inline (título, tags, fecha, priority, duration)
  - Verificar auto-save
  - Cancelar con ESC
  - Verificar validación inline
- [ ] Archive:
  - Completar tarea y modificar `completed_at` a -31 días (manual en BD)
  - Ejecutar `archiveOldTasks()`
  - Verificar que tarea desaparece de vista principal
  - Ver tareas archivadas en sección dedicada
  - Restaurar tarea
- [x] Responsive:
  - Mobile (< 640px)
  - Tablet (640-767px)
  - Desktop (>= 768px)
  - Touch targets
  - Inline editing en mobile
  - Drag and drop en mobile
- [x] Recurring Tasks (Premium):
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
- [x] Chrome
- [x] Firefox
- [x] Safari
- [x] Edge

**Mobile Testing:**
- [x] iOS Safari
- [x] Chrome Android
- [x] Varios tamaños de pantalla

**Performance:**
- [x] Lista con 100+ tareas renderiza sin lag
- [x] Inline editing responsive
- [x] Lighthouse score > 85 (desktop y mobile)

---

## 📦 Entregables

1. ✅ Duration Picker con múltiples unidades funcionando
2. ✅ Date-Time Picker con hora integrado
3. ✅ Límite Premium actualizado a 10 tareas
4. ✅ Status "In Progress" habilitado y visible
5. ✅ Inline task creation en lista
6. ✅ Inline editing (hover to edit) en todos los campos
7. ✅ Lista de tareas completamente responsive
8. ✅ Recurring tasks (Premium only) con RRULE

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Duration picker es más usable que input manual
2. Date-time picker permite seleccionar hora fácilmente
3. Límite de 10 tareas aplicado correctamente para FREE
4. Usuarios pueden mover tareas a "in progress" sin fricción
5. Inline creation reduce tiempo de creación de tareas
6. Inline editing permite edición rápida sin abrir modales
7. Lista es completamente usable en mobile
8. Recurring tasks generan instancias correctamente (Premium)
9. Cero errores críticos
10. Performance no se degrada con features nuevas
11. UX general ha mejorado significativamente

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
| Inline editing confuso en mobile | Media | Testing exhaustivo con usuarios reales |
| RRULE parsing complejo | Media | Usar librería rrule bien documentada |
| Cron jobs no disponibles en Vercel free | Alta | Implementar trigger manual como fallback |
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

- [RRULE RFC 5545](https://www.rfc-editor.org/rfc/rfc5545)
- [rrule.js Library](https://github.com/jakubroztocil/rrule)
- [Vercel Cron Jobs](https://vercel.com/docs/cron-jobs)
- [React Window (Virtualization)](https://react-window.vercel.app/)
- [shadcn/ui Date Picker](https://ui.shadcn.com/docs/components/date-picker)
- [Google Calendar UX Patterns](https://calendar.google.com)

---

## ✅ Definition of Done

- [x] Todas las tareas completadas
- [x] Testing manual completado sin bugs críticos
- [x] Duration picker funcionando en producción
- [x] Date-time picker funcionando en producción
- [x] Límite de 10 tareas aplicado correctamente
- [x] Status "in progress" visible y funcional
- [x] Inline creation funcionando
- [x] Inline editing funcionando en todos los campos
- [x] Lista responsive en todos los tamaños
- [x] Recurring tasks funcionando (Premium)
- [x] Cron jobs configurados (o fallback manual)
- [x] Code review (self-review)
- [x] Deployed to staging
- [x] Accesibilidad: keyboard navigation en todas las features
- [x] Performance aceptable con 100+ tareas

---

**Creado:** Enero 11, 2026
**Actualizado:** Enero 11, 2026
**Próxima revisión:** Enero 17, 2026
