# Phase 1 Data Model: Overdue Todo Items

## Entities

### Todo Item (existing entity — unchanged persisted shape)

No new persisted fields are introduced by this feature. Existing fields
relevant to this feature:

| Field       | Type              | Notes                                                        |
|-------------|-------------------|---------------------------------------------------------------|
| `id`        | number/string      | Existing identifier, unchanged                                |
| `title`     | string             | Existing, unchanged                                           |
| `dueDate`   | string (`YYYY-MM-DD`) or `null` | Existing, optional. Basis for overdue computation |
| `completed` | number/boolean (`0`/`1`) | Existing. Basis for overdue computation           |

### Derived value: `isOverdue` (not persisted)

A computed, view-only value derived at render time — never sent to or
received from the backend, never stored in component state beyond a single
render pass.

**Computation** (pseudocode, date-only comparison per research.md):

```text
isOverdue(todo, today = startOfDay(new Date())):
  if todo.completed is truthy: return false
  if todo.dueDate is null/empty: return false
  return startOfDay(parse(todo.dueDate)) < today
```

**Validation rules** (from spec Functional Requirements):

- FR-001 / FR-002 / FR-003: `isOverdue` is `true` only when `dueDate` is set,
  `dueDate` is strictly before today's date, and `completed` is falsy.
- FR-005: Recomputed on every render of the todo list — no caching across
  renders.
- FR-007: Toggling `completed` to `true` (via existing `onToggle` flow)
  causes `isOverdue` to become `false` on the next render, removing the
  badge immediately (no extra state management needed — this falls out of
  the derivation being computed from current props on each render).

### State transitions

No new state machine is introduced. `isOverdue` is a pure function of
existing state (`dueDate`, `completed`) and the current date — it has no
transitions of its own; it simply re-evaluates whenever its inputs
(`completed`) or the ambient clock (render time) change.
