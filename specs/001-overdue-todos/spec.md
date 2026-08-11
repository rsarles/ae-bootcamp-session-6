# Feature Specification: Overdue Todo Items

**Feature Branch**: `001-overdue-todos`

**Created**: 2026-08-11

**Status**: Draft

**Input**: User description: "Support for Overdue Todo Items - As a todo application user, I want to easily identify and distinguish overdue tasks in my todo list, so that I can prioritize my work and quickly see which tasks are past their due date. Users need a clear, visual way to identify which todos have not been completed by their due date."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Visually identify overdue todos (Priority: P1)

As a todo application user, I want incomplete todos whose due date has passed to
stand out visually in my list, so that I can spot and prioritize them without
comparing each due date to today's date myself.

**Why this priority**: This is the entire scope of the feature and delivers the
full value on its own — a user can immediately see which tasks need attention.

**Independent Test**: Can be fully tested by creating todos with due dates in the
past, present, and future (some complete, some incomplete) and verifying only
incomplete todos with a past due date are shown with the overdue indicator.

**Acceptance Scenarios**:

1. **Given** a todo with a due date earlier than today and not marked complete,
   **When** the todo list is displayed, **Then** the todo is shown with a
   visually distinct overdue indicator.
2. **Given** a todo with a due date earlier than today that is marked complete,
   **When** the todo list is displayed, **Then** the todo is shown normally
   with no overdue indicator.
3. **Given** a todo with no due date set, **When** the todo list is displayed,
   **Then** the todo is never shown with an overdue indicator.
4. **Given** a todo with a due date of today or in the future and not marked
   complete, **When** the todo list is displayed, **Then** the todo is shown
   normally with no overdue indicator.
5. **Given** an incomplete, overdue todo, **When** the user marks it complete,
   **Then** the overdue indicator is removed immediately.

---

### Edge Cases

- A todo's due date is exactly today: it is NOT treated as overdue (only due
  dates strictly before today qualify).
- A completed todo has a due date in the past: it is never shown as overdue,
  regardless of when it was completed.
- A todo has no due date: it can never be overdue.
- The app remains open across midnight: the overdue indicator is recalculated
  the next time the list is rendered (e.g., on next load or state refresh), not
  necessarily in real time while the page is idle.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST treat a todo as overdue only when it has a due date
  earlier than the current date AND is not marked complete.
- **FR-002**: System MUST NOT treat a todo without a due date as overdue.
- **FR-003**: System MUST NOT treat a completed todo as overdue, regardless of
  its due date.
- **FR-004**: System MUST display incomplete, overdue todos with a visually
  distinct indicator (e.g., color/label) that differentiates them from
  non-overdue and completed todos.
- **FR-005**: System MUST recompute each todo's overdue status whenever the
  todo list is rendered, based on the current date at render time.
- **FR-006**: System MUST continue to display each todo's existing title, due
  date, and completion checkbox alongside the new overdue indicator.
- **FR-007**: System MUST remove the overdue indicator immediately when a user
  marks an overdue todo as complete.

### Key Entities

- **Todo Item**: Existing entity with title, optional due date, and completion
  status. This feature adds a derived, non-persisted "overdue" state computed
  from the existing due date and completion status at display time — no new
  stored fields are introduced.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of incomplete todos with a due date earlier than the
  current date display the overdue indicator when the list is rendered.
- **SC-002**: 0% of completed todos or todos without a due date are shown with
  the overdue indicator.
- **SC-003**: Users can identify all overdue todos in their list within 3
  seconds of viewing the page, without comparing any dates manually.
- **SC-004**: Marking an overdue todo complete removes its overdue indicator
  with no page reload required.

## Assumptions

- Overdue status is determined by comparing a todo's due date to the current
  local (client/browser) date; no server-side timezone handling is required
  since this remains a single-user application per
  `docs/functional-requirements.md`.
- The overdue indicator reuses the existing "Danger" color role defined in
  `docs/ui-guidelines.md` for visual consistency with other warning/destructive
  indicators, rather than introducing a new color.
- The existing todo list ordering (creation date, newest first, per
  `docs/functional-requirements.md`) is unchanged; this feature only adds a
  visual indicator and does not introduce sorting, grouping, or filtering by
  overdue status.
- No new backend fields, endpoints, or schema changes are required; overdue
  status is computed at render time from the existing due date and completion
  fields.
