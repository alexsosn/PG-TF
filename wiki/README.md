# PG-TF wiki

This directory is the durable project memory for humans and automated agents. GitHub issues track concrete work; this wiki records the assumptions, architecture, workflow, and staged plan needed to perform that work consistently.

## Documents

- [`agentic-dev-loop.md`](agentic-dev-loop.md) — how an automated agent selects, implements, validates, and hands off work.
- [`architecture.md`](architecture.md) — source pipeline, Text-Fabric data model, component boundaries, and provenance rules.
- [`testing.md`](testing.md) — TDD policy, fixtures, invariants, integration validation, and corpus-level checks.
- [`backlog.md`](backlog.md) — staged implementation plan and suggested issue decomposition.
- [`decisions.md`](decisions.md) — durable architecture/data decisions and unresolved decision points.
- [`../research.md`](../research.md) — tagged evidence about the CGPG source data and conversion difficulty.

## Source of truth hierarchy

When documents disagree, use this order:

1. current upstream source evidence for the exact pinned CGPG release;
2. accepted GitHub issue/PR decisions and tests;
3. `wiki/decisions.md`;
4. `wiki/architecture.md`;
5. older research notes.

A source-format discovery must be recorded rather than silently encoded in implementation.

## What belongs where

Use GitHub issues for finite tasks and acceptance criteria. Use PRs for implementation/review discussion. Use `research.md` for externally verifiable source observations. Use `decisions.md` when the project chooses one design among alternatives. Use `backlog.md` for sequencing, not as a substitute for actionable issues.

## Current phase

The repository is in **Phase 0: scaffold and source-contract definition**. No production converter has been implemented yet.
