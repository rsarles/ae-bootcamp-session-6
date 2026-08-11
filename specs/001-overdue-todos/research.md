# Phase 0 Research: Overdue Todo Items

All Technical Context items were resolvable directly from the existing
codebase and spec Assumptions — no NEEDS CLARIFICATION items remain.

## Decision: Compute overdue status client-side at render time

- **Decision**: Overdue status is derived in the frontend from the existing
  `todo.dueDate` and `todo.completed` fields, recomputed every render. No new
  field is added to the backend model or API response.
- **Rationale**: Spec Assumptions explicitly state this is a single-user app
  with no server-side timezone handling required, and FR-005 requires
  recomputation "whenever the todo list is rendered." Computing in the
  component avoids storage/staleness issues entirely (edge case: app open
  across midnight).
- **Alternatives considered**:
  - *Backend-computed `isOverdue` field*: rejected — adds persistence/schema
    complexity and a server-timezone dependency for a single-user app where
    the spec assumes client-local date comparison.
  - *Global `setInterval` re-render timer*: rejected — spec edge case only
    requires recompute "next time the list is rendered," not real-time
    while idle; unnecessary complexity (violates YAGNI / Principle IV).

## Decision: Comparison logic — date-only, strict "before today"

- **Decision**: Compare `dueDate` (parsed as a calendar date) against the
  current local calendar date; a todo is overdue only if `dueDate < today`
  (strictly before), matching FR-001 and the edge case that a due date of
  today is NOT overdue.
- **Rationale**: Existing `dueDate` values are plain date strings (e.g.
  `YYYY-MM-DD`) with no time component (see `TodoCard.formatDate`), so
  comparison must be date-only to avoid off-by-time-of-day errors.
- **Alternatives considered**:
  - *Full `Date` object comparison with current timestamp*: rejected — risks
    treating "due today" as overdue depending on time of day, violating the
    documented edge case.

## Decision: Badge implementation — inline element + CSS class, no new library

- **Decision**: Render a small `<span>` badge with text "Overdue" and a CSS
  class (e.g. `overdue-badge`) next to the due date inside `TodoCard`,
  styled using the existing `--danger-color` CSS variable in
  `theme.css`/`App.css`. No new dependency is introduced.
- **Rationale**: Constitution Principle III requires reuse of the existing
  design system and Principle IV/Technology Stack Requirements prohibit new
  runtime dependencies without justification. A styled `<span>` matches the
  existing component patterns (see `.todo-due-date`, `.btn-icon` classes).
- **Alternatives considered**:
  - *Third-party badge/tooltip component library*: rejected — unjustified
    new dependency for a simple text label.
  - *Color-only indicator (e.g., red border)*: rejected — spec FR-004
    explicitly requires a text badge as a non-color cue for accessibility.

## No external contract changes

- **Decision**: No `contracts/` artifacts are generated for this feature.
- **Rationale**: The feature does not add, modify, or remove any API
  endpoint, request/response shape, or database schema. The backend
  (`packages/backend`) is unaffected; `todoService.js` (frontend) requires no
  changes since it already returns `dueDate` and `completed` for each todo.
