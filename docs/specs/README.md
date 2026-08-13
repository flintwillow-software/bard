---
status: draft
created: '2025-08-13'
updated: '2025-08-13'
tags:
  - spec-tree
  - specification
---

# Specification Root

> Ordered specification tree. This index is maintained by the `specs-index` skill — see [templates/spec-tree](../../docs/templates/spec-tree/README.md) for the skeleton.

## Status

- Components: 0 (empty tree)
- Changes: 0 in-flight
- Latest version: none

## Conventions

### Spec Codes

Every subfeature has a **spec code**: 6 digits = `component.feature.subfeature` flattened (`00.01.02` → `000102`).

| Context | Format | Example |
|---------|--------|---------|
| Frontmatter / programmatic | bare 6 digits | `spec-refs: [000000, 000100]` |
| Inline links / display | `S` prefix | `[S000000 foo feature](docs/specs/00-core-component/00-foo-feature.md)` |
| File path | numbered `NN-` folders + files | `docs/specs/00-core-component/00-foo-feature.md` |

- `spec-refs` in a **feature file** lists the codes of the subfeatures it defines (self-declaration, so tooling never parses headings).
- `spec-refs` in a **component README** lists the codes of its features.
- `spec-refs` in a **change proposal** lists the codes the change touches.

### Numbering

```
docs/specs/00-core-component/00-foo-feature.md
                ↓                 ↓
               00                00   →  ## 00 subfeature  →  code 000000
```

- **Component**: 2-digit zero-padded folder (`00`, `01`, …).
- **Feature**: 2-digit zero-padded file (`NN-feature-name.md`).
- **Subfeature**: 2-digit zero-padded `##` heading — every feature file starts with `## 00`.
- Requirements use `### Requirement:` headings with one SHALL + GIVEN/WHEN/THEN/AND scenarios. Depth cap at 3 levels — if a feature outgrows its file, promote it to a component.

### Amendment Model

- **Default: append** — new subfeatures go at the end of a feature file (`000002`, `000003`…), new features at the end of the component, new components at the end of the tree.
- **Insertion**: when adjacency matters, the `specs-renumber` skill rewrites numbers + frontmatter codes + inline links + paths atomically in one commit.
- Numbers are **positional sort prefixes**; the kebab-name carries identity.

### Change Traceability

Each change is named for its primary spec code: `docs/changes/<version>/<NN>-<kebab>-S<primary-code>/`.

- **Change frontmatter**: `s-code: S000000` (primary, display form, matches folder name) + `spec-refs: [000000, 000100]` (all codes touched, bare form).
- **Spec file frontmatter**: `spec-refs: [000000, 000001]` — codes of subfeatures defined here. Appended on sync, never removed.
- **Uniqueness**: primary S-codes unique within a version directory; may repeat across versions (re-amendment).
- **Traceability chain**: spec code (`000000`) → change folder (`00-add-foo-S000000`) → version (`0.1.0`) → git tag (`v0.1.0`).

### Lifecycle

1. `specs-propose` → scaffolds `docs/changes/<version>/<NN>-<name>-S<code>/`
2. Propose → review → design → tasks → implement
3. `specs-sync` → copies `specs/` from change dir → `docs/specs/`, appends codes to feature frontmatter, deletes change dir, writes commit message from proposal, regenerates index
4. Git tag matches version directory

## Change Map

| Change | S-Code | Version | Spec Refs | Status |
|--------|--------|---------|-----------|--------|
| — | — | — | — | — |

## Components

| ID | Component | Status | Features | Spec Refs |
|----|-----------|--------|----------|-----------|
| — | — | — | — | — |