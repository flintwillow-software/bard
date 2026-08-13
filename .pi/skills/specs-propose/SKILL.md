---
name: specs-propose
description: >
  Scaffold a new versioned change proposal. Creates the change directory under
  docs/changes/<version>/<NN>-<kebab>-S<code>/ with proposal.md, design.md, tasks.md, and specs/
  future-state copies of the affected spec files. Use when the user has a clear idea of what to build.
allowed-tools: Bash(read, grep, find, ls, mkdir, cp, mv)
---

# specs-propose

> Scaffold a new change proposal. The atomic unit of traceability from idea to versioned release.

## Spec Code Convention

Spec codes are 6 digits = `component.feature.subfeature` flattened (`00.01.00` → `000100`). Bare in frontmatter (`spec-refs: [000000]`), `S`-prefixed in display and folder names (`S000000`). Unique within a version directory.

## Steps

### 1. Determine the target version

- Read `docs/changes/README.md` for the current active version.
- If no active version exists, use the next semver bump from the latest git tag (or `0.0.0` if none).
- If changes already exist in a version dir, use that version.

### 2. Determine the primary spec code and folder name

- Read `docs/specs/README.md` to find the next available component/feature/subfeature slot.
- The primary spec code is the first subfeature this change creates or modifies.
- The folder name: `<NN>-<kebab-name>-S<primary-code>` — NN is the next number in the version (00, 01, …), kebab name is a short descriptive slug.

### 3. Scaffold the change directory

```bash
mkdir -p docs/changes/<version>/<NN>-<kebab>-S<code>/{specs/}
```

### 4. Copy template files

```bash
cp docs/templates/change/proposal.md docs/changes/<version>/<NN>-<kebab>-S<code>/proposal.md
cp docs/templates/change/design.md   docs/changes/<version>/<NN>-<kebab>-S<code>/design.md
cp docs/templates/change/tasks.md    docs/changes/<version>/<NN>-<kebab>-S<code>/tasks.md
```

### 5. Fill proposal.md

- `s-code`: the primary spec code with S prefix (matches folder name)
- `version`: the target version directory
- `spec-refs`: array of all spec codes this change touches (bare 6-digit form)
- Why / What Changes / Out of Scope / Spec Impact table

### 6. Populate specs/ with future-state copies

For each affected spec file, copy it from `docs/specs/` into the same path under `specs/`, then apply the amendment. The file must be the **complete future state** — no delta markers.

```bash
mkdir -p docs/changes/<version>/<NN>-<kebab>-S<code>/specs/NN-component
cp docs/specs/NN-component/NN-feature.md docs/changes/<version>/<NN>-<kebab>-S<code>/specs/NN-component/NN-feature.md
# Then edit the copy to add/modify/remove subfeatures
```

### 7. Update `docs/changes/README.md` tracker

Add an entry to the Active Changes table.

## Related

- `specs-explore` — think through the idea first
- `specs-apply` — implement the change after proposal review
- `docs/specs/README.md` — full conventions
- `docs/templates/change/` — template files