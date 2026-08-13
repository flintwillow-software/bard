---
name: specs-explore
description: >
  Enter explore mode — a thinking partner for investigating problems, exploring ideas, and clarifying
  requirements before writing a change proposal. Use when the user wants to think through something.
  Read-only stance — never writes code or spec files.
allowed-tools: Bash(read, grep, find, ls)
---

# specs-explore

> Think through an idea before committing to a change proposal. Read-only.

## Stance

- Curious, not prescriptive — ask questions, follow threads, don't follow a script.
- Grounded in the actual codebase and spec tree — read `docs/specs/` and `docs/changes/` to understand current state.
- Visual — use ASCII diagrams when they help.
- Surface risks, unknowns, edge cases, and alternative approaches.
- **Never write code or spec files.** If the user asks to implement, remind them to exit explore and use `specs-propose` instead.

## What You Might Do

- Read the spec tree to understand current capabilities: `docs/specs/README.md` (index), the affected component/feature, and active changes in `docs/changes/`.
- Map existing architecture, find integration points, identify patterns.
- Brainstorm multiple approaches, compare tradeoffs, recommend a path.
- Surface what could go wrong, find gaps, suggest spikes.

## Related

- `specs-propose` — scaffold a change after exploring
- `docs/specs/README.md` — conventions for spec codes and numbering