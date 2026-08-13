---
name: specs-apply
description: >
  Implement the tasks in a change proposal. Reads the change's tasks.md and executes each task
  in order, verifying against the specs/ future-state copies. Use after a change proposal has been
  reviewed and approved.
allowed-tools: Bash(all)
---

# specs-apply

> Execute a change's task list, building the feature according to the spec.

## Steps

### 1. Identify the change

- The user specifies which change to apply, or you read `docs/changes/README.md` for the next unapplied change.
- A change path: `docs/changes/<version>/<NN>-<kebab>-S<code>/`

### 2. Read the change artifacts

- `proposal.md` — why, what, out of scope, spec impact
- `design.md` — technical approach, decisions
- `tasks.md` — numbered checklist 1.1, 1.2, …
- `specs/` — future-state copies of affected spec files (the target state)

### 3. Execute tasks in order

- Implement each task (`1.1`, `1.2`, `2.1`…) in sequence.
- Verify implementation against the future-state `specs/` files — the behavior should match the amended spec.
- Update `CHANGELOG.md` under `Unreleased` as you go.

### 4. Verify

- Run the test suite, resolve regressions.
- Confirm the implementation matches the spec by reading the future-state files against the code.

## Related

- `specs-sync` — merge the applied change into the spec tree after completion
- `specs-propose` — scaffold the change before applying