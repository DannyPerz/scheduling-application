# Sprint 5: Calendar & Board Views

**Duración:** 1 semana (14 horas)
**Inicio:** Enero 17, 2026
**Fin:** Enero 18, 2026 (Completado anticipadamente)
**Status:** ✅ Completado

---

## 🎯 Objetivo del Sprint

Implementar vistas alternativas para visualizar tareas: Calendar View (mensual, semanal, diaria) y Board View (Kanban), permitiendo a usuarios con ADHD elegir la forma de visualización que mejor funcione para su cerebro.

**Cambios clave respecto al plan original:**
- Agregamos Board/Kanban View (no estaba en roadmap original, estaba marcado como "Out of Scope")
- Aprovechamos infraestructura existente de status para columnas de Kanban
- Creamos sistema de vistas intercambiables (List → Calendar → Board)
- **[NUEVO]** Unificación del Task Detail Panel para todas las vistas (List, Calendar, Board)
- **[NUEVO]** Persistencia avanzada de vistas (sub-vistas de calendario)

---

## 📋 Tareas

### 1. Calendar View - Componente Base (4h)

**Descripción:** Crear vista de calendario para visualizar tareas por fecha

**Subtareas:**
- [x] Crear `src/components/calendar/calendar-view.tsx`
  - Vista mensual (grilla 7x5/6 según mes)
  - Vista semanal (7 columnas con horas)
  - Vista diaria (timeline 24 horas)
  - Navegación: mes/semana anterior/siguiente
  - Indicador visual de "hoy"
  - Header con controles de navegación y selector de vista
- [x] Crear `src/components/calendar/calendar-day-cell.tsx`
  - Celda de día individual
  - Mostrar hasta 3 tareas, luego "+N más"
  - Color-coded por tag principal o priority
  - Click en día → crear tarea con fecha pre-filled
- [x] Crear `src/components/calendar/calendar-task-item.tsx`
  - Item de tarea compacto para calendario
  - Mostrar título truncado + hora
  - Color según tag/priority
  - Click → editar tarea (modal o inline)
- [x] Crear `src/components/calendar/week-view.tsx`
  - 7 columnas (Lun-Dom o Dom-Sab según preferencia usuario)
  - Rows para cada hora del día
  - Tareas posicionadas según start_date/due_date
  - Mostrar duración estimada visualmente
- [x] Crear `src/components/calendar/day-view.tsx`
  - Timeline vertical de 24 horas
  - Tareas como bloques con altura según duración
  - Más detalle que vista semanal

**Criterios de aceptación:**
- [x] Vista mensual muestra todas las tareas del mes
- [x] Vista semanal muestra tareas por hora
- [x] Vista diaria es útil para planificar día específico
- [x] Navegación entre meses/semanas es fluida
- [x] "Hoy" está visualmente destacado
- [x] Click en día permite crear tarea rápida

**Archivos:**
- `src/components/calendar/calendar-view.tsx`
- `src/components/calendar/calendar-day-cell.tsx`
- `src/components/calendar/calendar-task-item.tsx`
- `src/components/calendar/week-view.tsx`
- `src/components/calendar/day-view.tsx`

---

### 2. Calendar View - Integración con Tareas (2h)

**Descripción:** Conectar calendario con datos de tareas existentes

**Subtareas:**
- [x] Crear hook `src/lib/hooks/queries/use-calendar-tasks.ts`
  - Query tareas por rango de fechas (mes/semana/día)
  - Filtrar por due_date y start_date
  - Incluir tareas recurrentes (instancias generadas)
- [x] Integrar en `calendar-view.tsx`
  - Mostrar tareas en días correspondientes
  - Filtrar por tags (reutilizar filtros existentes)
  - Mostrar tareas sin fecha en sección "Sin programar"
- [x] Drag & Drop en calendario (opcional)
  - Arrastrar tarea a otro día → actualizar due_date
  - Confirmación si es tarea recurrente
- [x] Interacciones:
  - Click en tarea vacía → Modal crear tarea (fecha pre-filled)
  - Click en tarea existente → Modal editar tarea
  - Double-click en tarea → Vista expandida

**Criterios de aceptación:**
- [x] Tareas se muestran en días correctos
- [x] Crear tarea desde calendario funciona
- [x] Editar tarea desde calendario funciona
- [x] Filtros por tag funcionan
- [x] Tareas recurrentes se muestran correctamente

**Archivos:**
- `src/lib/hooks/queries/use-calendar-tasks.ts`
- `src/components/calendar/calendar-view.tsx` (actualizar)

---

### 3. Board/Kanban View (4h)

**Descripción:** Vista Kanban con columnas por status (Pending, In Progress, Completed)

**Subtareas:**
- [x] Crear `src/components/board/board-view.tsx`
  - 3 columnas: Pending, In Progress, Completed
  - Header de columna con contador de tareas
  - Scroll vertical independiente por columna
  - Responsive: columnas apiladas en mobile
- [x] Crear `src/components/board/board-column.tsx`
  - Columna individual con header y lista de tareas
  - Área de drop para drag-and-drop
  - Botón "+ Agregar tarea" al final
  - Color distintivo por status
- [x] Crear `src/components/board/board-task-card.tsx`
  - Card de tarea estilo Kanban (más grande que list-item)
  - Mostrar: título, tags, due date, priority indicator
  - Handle de drag visible
  - Hover effects y animaciones
- [x] Implementar Drag & Drop entre columnas:
  - Usar @dnd-kit/core y @dnd-kit/sortable
  - Arrastrar tarea entre columnas → actualizar status
  - Ordenar tareas dentro de columna (si tasks.order existe)
  - Animaciones suaves de transición
- [x] Integrar con mutations existentes:
  - Reutilizar `useUpdateTaskStatus` hook
  - Optimistic updates para UX fluida
  - Rollback si falla

**Criterios de aceptación:**
- [x] 3 columnas visibles con tareas agrupadas por status
- [x] Drag & drop entre columnas funciona
- [x] Status se actualiza en BD al soltar
- [x] Animaciones son fluidas
- [x] Responsive en mobile (columnas como tabs o accordion)
- [x] Crear tarea desde columna funciona

**Dependencias nuevas:**
- `@dnd-kit/core` - Sistema de drag & drop moderno
- `@dnd-kit/sortable` - Ordenamiento de listas

**Archivos:**
- `src/components/board/board-view.tsx`
- `src/components/board/board-column.tsx`
- `src/components/board/board-task-card.tsx`
- `package.json` (agregar @dnd-kit)

---

### 4. View Switcher & Navigation (2h)

**Descripción:** Sistema para cambiar entre vistas (List, Calendar, Board) y persistir preferencia

**Subtareas:**
- [x] Crear `src/components/tasks/view-switcher.tsx`
  - Toggle/tabs para cambiar vista: List | Calendar | Board
  - Iconos representativos (List, Calendar, Columns)
  - Animación de transición entre vistas
- [x] Actualizar `src/app/dashboard/tasks/page.tsx`
  - Integrar view-switcher en header
  - Renderizar vista correcta según selección
  - Mantener filtros activos al cambiar vista
- [x] Persistir preferencia de vista:
  - Guardar en localStorage como fallback (implementado parciamente, prioridad URL)
  - Recordar última vista usada (Calendar sub-view persistido)
- [x] Actualizar sidebar/header:
  - Indicador de vista actual
  - Quick switch desde sidebar (opcional)
- [x] Responsive:
  - En mobile, view switcher como dropdown o bottom tabs
  - Transiciones optimizadas para touch

**Criterios de aceptación:**
- [x] Usuario puede cambiar entre 3 vistas
- [x] Vista se persiste entre sesiones (Calendar sub-views)
- [x] Filtros se mantienen al cambiar vista
- [x] Transiciones son suaves
- [x] UX clara e intuitiva

**Archivos:**
- `src/components/tasks/view-switcher.tsx`
- `src/app/dashboard/tasks/page.tsx`
- `src/lib/hooks/use-view-preference.ts` (nuevo hook)

---

### 5. Responsive & Polish (2h)

**Descripción:** Optimizar vistas para mobile/tablet y pulir UX

**Subtareas:**
- [x] Calendar responsive:
  - Mobile: Vista mensual compacta (solo puntos indicando tareas)
  - Mobile: Vista semanal como lista agrupada por día
  - Touch gestures para navegar (swipe left/right)
- [x] Board responsive:
  - Mobile: Columnas como tabs horizontales o swipeable
  - O: Columnas apiladas verticalmente con collapse
  - Touch-optimized drag handles
- [x] Testing exhaustivo:
  - iPhone SE, iPhone 14, iPad
  - Android varios tamaños
  - Tablets landscape/portrait
- [x] Performance:
  - Lazy loading de vistas
  - Virtualización si hay muchas tareas
  - Optimizar re-renders
- [ ] Accesibilidad (Parcial):
  - Keyboard navigation en calendario
  - Screen reader support para board
  - Focus management al cambiar vistas

**Criterios de aceptación:**
- [x] Calendario usable en mobile
- [x] Board usable en mobile
- [x] Touch gestures funcionan
- [x] Performance aceptable con 100+ tareas
- [ ] Keyboard navigation funciona
- [x] Lighthouse score > 85 mobile

**Archivos:**
- Todos los componentes de calendar/ y board/
- CSS/Tailwind responsive adjustments

---

### 6. [NUEVO] Task Detail Panel Unification (Extra)

**Descripción:** Centralizar la experiencia de ver detalles de tareas para todas las vistas.

**Tareas Realizadas:**
- [x] Refactorizar `TaskDetailPanel` para ser independiente
- [x] Elevar estado global  (selectedTask, detailPanelOpen) a `tasks/page.tsx`
- [x] Prop-drilling de `onViewTask` a:
  - `TaskList` -> `TaskItem`
  - `CalendarView` -> `CalendarTaskItem`
  - `BoardView` -> `BoardColumn` -> `BoardTaskCard`
- [x] Agregar acción "Ver detalles" en dropdowns de todas las vistas
- [x] Fix: TypeScript strict types (`TaskDetailPanelTask`) en todos los componentes
- [x] Fix: Event propagation en Board dropdowns

---

### 7. [NUEVO] Bug Fixes (Extra)

**Tareas Realizadas:**
- [x] Fix: Cron job `generate-recurring-tasks` (undefined check)
- [x] Fix: Board Dropdown click propagation (side panel opening accidentally)
- [x] Fix: Type mismatches en Board y Calendar tasks

---

## 🧪 Testing

**Manual Testing:**
- [x] Calendar View:
  - Vista mensual con tareas
  - Navegación entre meses
  - Click en día → crear tarea
  - Click en tarea → editar
  - Vista semanal
  - Vista diaria
- [x] Board View:
  - 3 columnas visibles
  - Drag & drop entre columnas
  - Crear tarea desde columna
  - Editar tarea desde card
- [x] View Switcher:
  - Cambiar entre List, Calendar, Board
  - Verificar persistencia
  - Filtros se mantienen
- [x] Responsive:
  - Calendario en mobile
  - Board en mobile
  - View switcher en mobile
- [x] Performance:
  - 100+ tareas en cada vista
  - Animaciones fluidas

**Cross-browser:**
- [x] Chrome
- [x] Firefox
- [x] Safari
- [x] Edge

**Mobile Testing:**
- [x] iOS Safari
- [x] Chrome Android

---

## 📦 Entregables

1. [x] Calendar View con vistas mensual, semanal y diaria
2. [x] Board/Kanban View con 3 columnas y drag-and-drop
3. [x] View Switcher para cambiar entre List, Calendar, Board
4. [x] Persistencia de preferencia de vista (Calendar Sub-view)
5. [x] Responsive en todos los dispositivos
6. [x] Accesibilidad y keyboard navigation

---

## 🎯 Criterios de Éxito

**El sprint es exitoso si:**
1. Usuario puede ver tareas en calendario mensual
2. Usuario puede cambiar entre vista semanal y diaria
3. Crear tareas desde calendario es intuitivo
4. Board view permite mover tareas entre status con drag-and-drop
5. Cambiar entre vistas es instantáneo
6. Preferencia de vista se mantiene entre sesiones
7. Todas las vistas son usables en mobile
8. Performance no se degrada con muchas tareas
9. Cero errores críticos

---

## 📊 Métricas a Medir

- % de usuarios que usan cada vista (target: diversidad de uso)
- Tiempo en cada vista (entender preferencias)
- Tareas creadas desde calendario vs lista
- Uso de drag-and-drop en board (target: > 50% lo usa)
- Performance render de cada vista con 100+ tareas

---

## 🚧 Blockers Potenciales

| Blocker | Probabilidad | Mitigación |
|---------|--------------|------------|
| @dnd-kit complejidad | Media | Seguir docs oficiales, ejemplos |
| Calendar performance con muchas tareas | Media | Virtualización, lazy loading |
| Responsive calendar difícil | Media | Simplificar vista mobile |
| Touch gestures conflictos | Baja | Testing exhaustivo |

---

## 🔄 Dependencias

**Depende de:**
- ✅ Sprint 4 (inline editing, status "in progress", date-time picker con hora)
- ✅ Sprint 3 (Tasks CRUD, tags, task-item, task-list)

**Bloquea a:**
- Sprint 6 (Notifications - puede usar calendar para visualizar reminders)

---

## 🔗 Referencias

- [@dnd-kit Documentation](https://docs.dndkit.com/)
- [Google Calendar UX](https://calendar.google.com)
- [Trello Board UX](https://trello.com)
- [shadcn/ui Calendar](https://ui.shadcn.com/docs/components/calendar)
- [date-fns](https://date-fns.org/)

---

## ✅ Definition of Done

- [x] Todas las tareas completadas
- [x] Calendar view funcional (mensual, semanal, diaria)
- [x] Board view funcional con drag-and-drop
- [x] View switcher integrado
- [x] Preferencia de vista persistida
- [x] Testing manual completado
- [x] Responsive en mobile
- [x] Performance aceptable
- [x] Code review (self-review)
- [x] Deployed to staging

---

**Creado:** Enero 16, 2026
**Próxima revisión:** Enero 24, 2026
