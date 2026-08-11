---

description: "Task list for Overdue Todo Items feature implementation"
---

# Tasks: Overdue Todo Items

**Input**: Design documents from `/specs/001-overdue-todos/`

**Prerequisites**: [plan.md](./plan.md), [spec.md](./spec.md), [research.md](./research.md), [data-model.md](./data-model.md), [quickstart.md](./quickstart.md)

**Tests**: Test tasks are included — the plan's Constitution Check (Principle II: Test-First & Comprehensive Coverage) explicitly requires new behavior to be covered by tests in `TodoCard.test.js`, added before/alongside implementation.

**Organization**: This feature has a single user story (US1, P1) per spec.md, so all implementation tasks fall under one phase.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Maps the task to the user story it belongs to (US1)
- Exact file paths are included in each description

## Path Conventions

This is the existing web application monorepo (`packages/frontend` + `packages/backend`). This feature only touches `packages/frontend`; `packages/backend` requires no changes (see [plan.md](./plan.md), [research.md](./research.md)).

---

## Phase 1: Setup

**Purpose**: Confirm the existing project baseline before making changes. No new project initialization, dependencies, or tooling configuration is required — this feature reuses the existing frontend stack (React, Jest, `@testing-library/react`).

- [ ] T001 Run `npm test` in `packages/frontend` to confirm the existing test suite passes before any changes (baseline check; no code changes)

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented.

**N/A for this feature**: There is no shared infrastructure, new entity, or cross-story dependency to build. The feature is a single, self-contained derived-value + UI badge change confined to `TodoCard.js` and `App.css`, reusing the existing `--danger-color` CSS variable already defined in `theme.css`. Proceed directly to Phase 3.

---

## Phase 3: User Story 1 - Visually identify overdue todos (Priority: P1) 🎯 MVP

**Goal**: Incomplete todos whose due date has passed display a visually distinct "Overdue" text badge (Danger color) next to the due date, recomputed on every render, so users can spot and prioritize them without comparing dates manually.

**Independent Test**: Create todos with due dates in the past, present, and future (some complete, some incomplete) and verify only incomplete todos with a past due date show the "Overdue" badge.

### Tests for User Story 1 ⚠️

> **NOTE**: Write these tests FIRST, ensure they FAIL before implementation. All tasks below add cases to the same file, so they must run sequentially (no [P]).

- [ ] T002 [US1] Add test "renders Overdue badge for an incomplete todo with a due date in the past" in `packages/frontend/src/components/__tests__/TodoCard.test.js` (Acceptance Scenario 1)
- [ ] T003 [US1] Add test "does not render Overdue badge for a completed todo with a due date in the past" in `packages/frontend/src/components/__tests__/TodoCard.test.js` (Acceptance Scenario 2)
- [ ] T004 [US1] Add test "does not render Overdue badge for a todo with no due date" in `packages/frontend/src/components/__tests__/TodoCard.test.js` (Acceptance Scenario 3)
- [ ] T005 [US1] Add test "does not render Overdue badge for an incomplete todo due today or in the future" in `packages/frontend/src/components/__tests__/TodoCard.test.js` (Acceptance Scenario 4 + edge case: due date exactly today is not overdue)
- [ ] T006 [US1] Add test "removes the Overdue badge immediately after toggling an overdue todo to complete" in `packages/frontend/src/components/__tests__/TodoCard.test.js` (Acceptance Scenario 5)

### Implementation for User Story 1

- [ ] T007 [US1] Implement an `isOverdue(todo)` pure helper function in `packages/frontend/src/components/TodoCard.js` that returns `false` when `todo.completed` is truthy or `todo.dueDate` is null/empty, and otherwise compares the date-only parsed `dueDate` strictly before today's local date (per [data-model.md](./data-model.md) pseudocode) — depends on T002-T006 existing and failing
- [ ] T008 [US1] Render a conditional `<span className="overdue-badge">Overdue</span>` next to the due date in the non-editing view of `packages/frontend/src/components/TodoCard.js`, shown only when `isOverdue(todo)` is `true` — depends on T007
- [ ] T009 [P] [US1] Add an `.overdue-badge` CSS rule in `packages/frontend/src/App.css` (near the existing `.todo-due-date` rule) styled with `color: var(--danger-color)` and appropriate small-badge spacing/sizing, consistent with existing `.btn-danger`/`.btn-delete` usage of the Danger color role
- [ ] T010 [US1] Run `npm test` in `packages/frontend` to confirm tests T002-T006 now pass and no existing `TodoCard` tests regressed — depends on T007, T008, T009

**Checkpoint**: User Story 1 is fully functional and independently testable — overdue todos show the badge, non-overdue/completed/no-due-date todos do not, and the badge disappears immediately on toggle.

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: Final validation across the whole feature.

- [ ] T011 [P] Manually validate all scenarios in [quickstart.md](./quickstart.md) against the running app (`npm start`), including the accessibility check that the badge is a text label, not a color-only cue
- [ ] T012 Run the full frontend test suite (`npm test` in `packages/frontend`) to confirm no regressions across `TodoCard`, `TodoList`, `TodoForm`, `ConfirmDialog`, and `todoService` tests

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **Foundational (Phase 2)**: N/A for this feature — no blocking prerequisites
- **User Story 1 (Phase 3)**: Depends on Setup (Phase 1) completion
- **Polish (Phase 4)**: Depends on Phase 3 completion

### Within User Story 1

- Tests (T002-T006) MUST be written and FAIL before implementation (T007-T009)
- T007 (helper function) before T008 (badge rendering, which calls the helper)
- T009 (CSS) can proceed in parallel with T007/T008 (different file)
- T010 (verification) depends on T007, T008, and T009 all being complete

### Parallel Opportunities

- T009 (CSS in `App.css`) can be done in parallel with T007/T008 (logic/JSX in `TodoCard.js`) since they touch different files
- T002-T006 all edit the same test file and must be done sequentially, not in parallel
- T011 (manual quickstart validation) can run in parallel with T012 (automated test suite) in Phase 4

---

## Parallel Example: User Story 1

```bash
# T009 can run alongside T007/T008 since it's a different file:
Task: "Add .overdue-badge CSS rule in packages/frontend/src/App.css"
Task: "Implement isOverdue helper and badge rendering in packages/frontend/src/components/TodoCard.js"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

Since this feature has exactly one user story, completing Phase 3 **is** the MVP:

1. Complete Phase 1: Setup (baseline check)
2. Skip Phase 2: Foundational (N/A)
3. Complete Phase 3: User Story 1 (tests, `isOverdue` helper, badge rendering, CSS, verification)
4. **STOP and VALIDATE**: Run quickstart.md scenarios manually and confirm all automated tests pass
5. Complete Phase 4: Polish (full regression test run + manual accessibility check)

### Incremental Delivery

Given the single-story scope, there is no incremental story-by-story rollout — deliver Phase 3 as one complete, independently testable increment, then validate with Phase 4.

---

## Notes

- [P] tasks = different files, no dependencies
- [US1] label maps every implementation task to the feature's single user story for traceability
- Verify tests (T002-T006) fail before implementing T007-T009
- Commit after each task or logical group (e.g., after all tests are added and failing, then again after implementation makes them pass)
- No new components, routes, API endpoints, or persisted fields are introduced — scope is strictly `TodoCard.js` + `App.css`
