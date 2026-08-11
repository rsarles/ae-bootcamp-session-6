<!--
Sync Impact Report
Version change: [template] → 1.0.0 (initial ratification)
Modified principles: n/a (first concrete adoption of all 5 principle slots)
Added sections:
  - Core Principles: I. Code Quality & Consistency, II. Test-First & Comprehensive
    Coverage, III. Consistent, Accessible UI/UX, IV. Simplicity & Scope Discipline
    (YAGNI), V. Monorepo Structure & Separation of Concerns
  - Technology Stack Requirements
  - Review & Quality Gates
  - Governance
Removed sections: none
Deferred placeholders: none
Templates requiring follow-up: none found referencing prior placeholder principle
  names (spec-template.md, plan-template.md, tasks-template.md, checklist-template.md
  reviewed; no direct principle-name references to update).
-->

# Todo App Constitution

## Core Principles

### I. Code Quality & Consistency (NON-NEGOTIABLE)
All code MUST follow the conventions in `docs/coding-guidelines.md`: 2-space
indentation, camelCase for variables/functions, PascalCase for components and
classes, UPPER_SNAKE_CASE for constants, and import ordering of external
libraries → internal modules → styles. Code MUST apply DRY, KISS, and SOLID
principles; duplicated logic MUST be extracted into shared utilities or
components rather than copy-pasted. All linting errors and warnings MUST be
resolved before a pull request is opened. Comments MUST explain "why", not
"what" the code already shows.
Rationale: Consistent style and adherence to established quality principles
keep the codebase maintainable and readable across contributors.

### II. Test-First & Comprehensive Coverage (NON-NEGOTIABLE)
Every feature and bug fix MUST include automated tests colocated in
`__tests__/` directories, following the Arrange-Act-Assert pattern with
descriptive test names. Tests MUST verify behavior, not implementation
details, MUST be isolated (no shared state), and MUST mock external
dependencies. The project MUST maintain 80%+ code coverage across the
frontend and backend packages, with critical todo workflows (create, view,
update, delete) covered at 100%. All tests MUST pass before a pull request is
merged.
Rationale: Comprehensive, isolated tests catch regressions early and document
expected behavior, per `docs/testing-guidelines.md`.

### III. Consistent, Accessible UI/UX
All UI work MUST conform to `docs/ui-guidelines.md`: the defined color
palette, typography scale, 8px spacing grid, and Material Design-inspired
component patterns (cards, elevation, 4-8px border radius). Interactive
elements MUST be keyboard accessible, meet WCAG AA color contrast, and use
properly associated labels/aria-attributes. Destructive actions (e.g.,
delete) MUST require confirmation. Motion MUST remain minimal per the
no-animation requirement.
Rationale: A consistent, accessible interface delivers a predictable
experience aligned with the app's design system.

### IV. Simplicity & Scope Discipline (YAGNI)
Implementation MUST stay within the boundaries defined in
`docs/functional-requirements.md`. Features explicitly marked out of scope
(authentication, multi-user support, priorities/categories, recurring todos,
reminders, undo/redo, bulk operations, advanced search/filtering,
mobile-specific optimization) MUST NOT be implemented without a documented,
approved change to the functional requirements. Prefer the simplest solution
that satisfies a requirement; avoid speculative abstraction or premature
optimization.
Rationale: The project is a focused single-user todo app for bootcamp
learning; scope creep undermines its purpose as a teaching tool.

### V. Monorepo Structure & Separation of Concerns
The project MUST retain its npm-workspaces monorepo layout with
`packages/frontend` (React) and `packages/backend` (Express.js) kept
independently runnable and testable. Frontend code MUST access backend
functionality only through the defined service layer (e.g.,
`services/todoService.js`), never by reaching into backend internals.
Backend logic MUST be organized by responsibility (routes, services,
middleware) as described in `docs/coding-guidelines.md`. Changes spanning
both packages MUST keep both packages' test suites passing.
Rationale: Clear separation between frontend and backend, enforced by the
workspace structure, keeps the codebase navigable and prevents tight
coupling.

## Technology Stack Requirements

Frontend: React with Jest and `@testing-library/react` for testing. Backend:
Node.js with Express.js and Jest for testing. Dependency management MUST use
npm workspaces from the repository root. New runtime dependencies,
state-management libraries, or backend frameworks MUST NOT be introduced
without a documented justification tied to a functional requirement.

## Review & Quality Gates

All changes MUST go through a pull request with at least one review before
merging. Before opening a pull request, contributors MUST run linting and the
full test suite (`npm test` from the repository root, or the relevant
workspace) and ensure both succeed. Commit messages MUST be descriptive and
atomic, following the conventional style shown in `docs/coding-guidelines.md`
(e.g., `feat: add ability to edit todo title`).

## Governance

This constitution supersedes ad hoc practices for this repository.
Amendments require: (1) a documented rationale referencing the affected
`docs/` guideline, (2) an update to this file with a version bump per
semantic versioning (MAJOR: backward-incompatible principle removal or
redefinition; MINOR: new principle or materially expanded guidance; PATCH:
clarifications and non-semantic refinements), and (3) a refreshed Sync Impact
Report. All pull requests MUST verify compliance with these principles during
review; unresolved conflicts between a proposed change and a principle MUST
block merge until resolved by adjusting the change or formally amending the
constitution. Use `docs/coding-guidelines.md`, `docs/testing-guidelines.md`,
and `docs/ui-guidelines.md` for detailed runtime development guidance.

**Version**: 1.0.0 | **Ratified**: 2026-08-11 | **Last Amended**: 2026-08-11
