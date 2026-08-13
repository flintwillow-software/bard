---
name: specs-sync
description: >
  Merge a completed change into the spec tree. Copies the change's specs/ future-state files into
  docs/specs/, appends spec codes to feature file frontmatter, updates Status History, regenerates
  the index, deletes the change directory, and writes a commit message from the proposal. Use after
  a change's tasks have been applied and verified.
allowed-tools: Bash(cp, rm, mv, mkdir, grep, sed, find, git)
---

# specs-sync

> Merge a completed change into the spec tree, then delete the change dir. Git history is the permanent record — no archive.

## Steps

### 1. Read the change's proposal.md

- `s-code` — primary spec code (display form, e.g. `S000000`)
- `spec-refs` — all spec codes touched (bare form, e.g. `[000000, 000100]`)
- The Why section — becomes the commit message body

### 2. Copy future-state specs into the tree

For each file in `docs/changes/<version>/<NN>-<kebab>-S<code>/specs/`:

```bash
cp docs/changes/<version>/<NN>-<kebab>-S<code>/specs/NN-component/NN-feature.md \
   docs/specs/NN-component/NN-feature.md
```

### 3. Update spec file frontmatter

For each affected spec file, append any new subfeature codes to the `spec-refs` frontmatter array. For example, if the file previously had `spec-refs: [000000, 000001]` and the change adds subfeature 02, update to `spec-refs: [000000, 000001, 000002]`. **Append only** — never remove codes.

### 4. Update Status History

Add a row to each affected feature file's Status History table:

```markdown
| YYYY-MM-DD | S000000 | 0.1.0 | Added 000002, modified 000001 |
```

### 5. Regenerate the spec tree index

Call `specs-index` to regenerate `docs/specs/README.md`.

### 6. Update the change tracker

Add the change to the Completed Versions table in `docs/changes/README.md`. Remove from Active Changes.

### 7. Delete the change directory

```bash
rm -rf docs/changes/<version>/<NN>-<kebab>-S<code>
```

### 8. Write the commit message

```text
<version>: <change title>

<Proposal's Why section>

Spec refs: [000000, 000100]
S-code: S000000
```

### 9. Tag on version completion

If the version directory is now empty (all changes synced), `git tag v<version>`.

## Related

- `specs-apply` — implement the change before syncing
- `specs-index` — regenerate the spec tree index