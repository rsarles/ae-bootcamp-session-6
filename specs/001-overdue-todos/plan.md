# Implementation Plan: Overdue Todo Items

**Branch**: `001-overdue-todos` | **Date**: 2026-08-11 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-overdue-todos/spec.md`

**Note**: This template is filled in by the `/speckit-plan` command; its definition describes the execution workflow.

## Summary

Add a derived, non-persisted "overdue" indicator to the existing todo list UI.
A todo is overdue when it has a due date strictly before today's local date
and is not marked complete. The status is computed at render time in the
frontend (no new backend fields, endpoints, or schema changes) and displayed
as a small "Overdue" text badge styled with the existing Danger color role,
positioned next to the due date on `TodoCard`.

## Technical Context

**Language/Version**: JavaScript (ES2020+), Node.js runtime for tooling

**Primary Dependencies**: React 18.2 (frontend), Express.js (backend,
unaffected by this feature)

**Storage**: N/A — no new stored fields; overdue status is computed, not
persisted

**Testing**: Jest + `@testing-library/react` (frontend, `packages/frontend`),
Jest + Supertest (backend, unaffected)

**Target Platform**: Web browser (desktop-focused, per functional requirements)

**Project Type**: Web application (existing `frontend` + `backend` npm
workspaces monorepo)

**Performance Goals**: N/A beyond existing render performance — overdue
computation is an O(1) date comparison per todo, no measurable impact

**Constraints**: Client-side date comparison only (single-user app, no
server-side timezone handling per spec Assumptions); must reuse existing
Danger color role (`--danger-color` in `theme.css`) rather than introducing
new colors

**Scale/Scope**: Single component change (`TodoCard`) plus colocated tests;
no new components, routes, or API endpoints

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **I. Code Quality & Consistency**: PASS. Change is confined to
  `TodoCard.js` plus a small CSS addition in `App.css`/`theme.css`, following
  existing camelCase/PascalCase and import-ordering conventions. No
  duplicated logic — the overdue check is a single small helper function.
- **II. Test-First & Comprehensive Coverage**: PASS. New behavior (overdue
  badge rendering per acceptance scenarios 1-5) will be covered by tests
  added to `TodoCard.test.js`, following Arrange-Act-Assert, before/alongside
  implementation.
- **III. Consistent, Accessible UI/UX**: PASS. Reuses the existing Danger
  color role for the badge and adds a text label (not color-only), satisfying
  WCAG AA / non-color-cue requirements already called out in the spec. No
  new animation introduced.
- **IV. Simplicity & Scope Discipline (YAGNI)**: PASS. No sorting, filtering,
  grouping, or new stored fields are introduced — strictly a display-time
  derived indicator, matching the spec's Assumptions.
- **V. Monorepo Structure & Separation of Concerns**: PASS. Change is
  entirely within `packages/frontend`; no backend changes, no reach-through
  into backend internals.

No violations — Complexity Tracking table is not needed.

## Project Structure

### Documentation (this feature)

```text
specs/001-overdue-todos/
├── plan.md              # This file (/speckit-plan command output)
├── research.md          # Phase 0 output (/speckit-plan command)
├── data-model.md        # Phase 1 output (/speckit-plan command)
├── quickstart.md        # Phase 1 output (/speckit-plan command)
└── tasks.md             # Phase 2 output (/speckit-tasks command - NOT created by /speckit-plan)
```

No `contracts/` directory is generated for this feature — no API, route, or
schema changes are introduced (see research.md, "No external contract
changes").

### Source Code (repository root)

```text
packages/frontend/
├── src/
│   ├── components/
│   │   ├── TodoCard.js              # MODIFIED: add overdue computation + badge
│   │   └── __tests__/
│   │       └── TodoCard.test.js     # MODIFIED: add overdue scenario tests
│   └── styles/
│       └── theme.css                # existing --danger-color reused, no changes expected
└── src/App.css                      # MODIFIED: add .overdue-badge styles

packages/backend/                     # UNCHANGED — no backend work in this feature
```

**Structure Decision**: Existing Option 2 (Web application: `frontend` +
`backend` npm workspaces). This feature only touches `packages/frontend`;
`packages/backend` requires no changes since overdue status is a
non-persisted, render-time derivation from data the API already returns
(`dueDate`, `completed`).

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

No violations recorded — table intentionally left empty.
