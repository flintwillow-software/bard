---
status: active
created: '2025-08-13'
updated: '2025-08-13'
tags:
  - changes
  - versioning
---

# Changes

> Versioned change log. Each version directory (`0.0.0/`, `0.1.0/`, …) holds one or more change folders (`NN-<name>-S<code>/`). This index is maintained by the `specs-status` skill.

## Convention

```
docs/changes/
├── README.md            ← this file: tracker + conventions
├── 0.0.0/               ← first version
│   └── NN-<name>-S<code>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/       ← future-state copies of amended spec files
│           └── NN-component/NN-feature.md
└── 0.1.0/
    └── ...
```

- **Version directories**: semver (`0.0.0`, `0.1.0`, `1.0.0`). Git tag `v<version>` on release.
- **Change folders**: `NN-<kebab-name>-S<code>` — number (order within version), kebab name, and the **primary spec code** the change implements.
- **Spec codes**: 6 digits = `component.feature.subfeature` flattened. Bare in frontmatter (`spec-refs: [000000]`), `S`-prefixed in display/folder names (`S000000`). Unique within a version.
- **Lifecycle**: proposed → applied → synced → deleted. After sync, git history is the permanent record — no archive.

## Active Changes

| Name | S-Code | Version | Status | Spec Refs |
|------|--------|---------|--------|-----------|
| — | — | — | — | — |

## Completed Versions

| Version | Tag | Date | Changes |
|---------|-----|------|---------|
| — | — | — | — |