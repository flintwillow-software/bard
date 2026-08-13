---
name: specs-status
description: >
  Show the current state of the spec tree and change pipeline: active changes, completed versions,
  and component health. Use when the user wants to know what's in flight or what shipped.
allowed-tools: Bash(grep, find, ls, sort, sed)
---

# specs-status

> Report the current state of the spec tree and change pipeline.

## Steps

### 1. Read the change tracker

Read `docs/changes/README.md` — the Active Changes and Completed Versions tables.

### 2. Walk the change tree

For each `docs/changes/<version>/` directory:

- List active change folders with their S-codes, status, and spec-refs.
- `grep` the frontmatter of each `proposal.md` for status, s-code, spec-refs.

### 3. Walk the spec tree

For each component in `docs/specs/`, count features and their statuses.

### 4. Present the report

```text
## Spec Tree
00-core-component: 2 features (1 draft, 1 in-review)
01-secondary-component: 1 feature (draft)

## In-Flight Changes
0.1.0: 2 changes
  [00-add-foo-S000000] proposed → 000000, 000001
  [01-add-bar-S000100] in-progress → 000100

## Completed Versions
0.0.0: 2 changes (tagged v0.0.0)
```

## Related

- `specs-index` — generates the permanent spec tree index
- `docs/changes/README.md` — the live change tracker