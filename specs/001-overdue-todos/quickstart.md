# Quickstart: Validate Overdue Todo Items

## Prerequisites

- Node.js and npm installed (see repository root `package.json` for npm
  workspaces setup).
- Dependencies installed: `npm install` from the repository root.

## Run the app locally

```bash
# From repository root — starts backend and frontend (see package.json scripts)
npm start
```

Or run frontend only for UI validation:

```bash
cd packages/frontend
npm start
```

## Automated test validation

```bash
cd packages/frontend
npm test
```

Expect `TodoCard.test.js` to include and pass cases covering the acceptance
scenarios in [spec.md](./spec.md):

1. Incomplete todo, due date in the past → "Overdue" badge rendered.
2. Completed todo, due date in the past → no badge.
3. Todo with no due date → no badge.
4. Incomplete todo, due date today or future → no badge.
5. Marking an overdue todo complete → badge disappears without reload.

## Manual validation scenarios

1. **Overdue, incomplete todo**: Create a todo with a due date before today
   and leave it unchecked. Confirm the todo list shows an "Overdue" text
   badge (Danger color) next to its due date.
2. **Overdue, completed todo**: Check the box on the todo from step 1.
   Confirm the badge disappears immediately, with no page reload.
3. **No due date**: Create a todo without a due date. Confirm it never shows
   an overdue badge.
4. **Due today / future**: Create a todo with today's date and one with a
   future date, both incomplete. Confirm neither shows the badge.
5. **Accessibility check**: Confirm the badge is a text label (not a
   color-only cue) and meets WCAG AA contrast using the existing Danger
   color role (see [data-model.md](./data-model.md) and
   `docs/ui-guidelines.md`).

## Reference

- Derivation logic: [data-model.md](./data-model.md)
- Design decisions: [research.md](./research.md)
- No API/contract changes — see research.md, "No external contract changes"
