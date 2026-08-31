# AGENTS.md

This repository is designed for an automated, issue-driven development loop. This file is the entry point for coding agents.

## Read first

Before changing code, read:

1. `README.md`
2. `research.md`
3. `wiki/README.md`
4. `wiki/agentic-dev-loop.md`
5. `wiki/architecture.md`
6. `wiki/testing.md`
7. the issue being implemented and any linked PR/discussion

## Non-negotiable rules

- Work from a GitHub issue with explicit acceptance criteria.
- Before starting, search for an existing open PR or active issue work covering the same scope. Do not create concurrent duplicate implementations.
- Use TDD for behavior changes: establish a failing test/fixture first, implement the smallest correct change, then refactor.
- Preserve upstream CGPG data exactly in the lossless layer. Normalized or corrected data must be additional features/layers with provenance.
- Never equate PDF page coordinates with canonical Migne column citations.
- Never reinterpret GREgORI tags without a documented mapping and tests; keep the raw tag.
- Never normalize Greek Unicode destructively. Preserve source strings verbatim.
- Fail explicitly on unknown source schema. Do not silently drop extra fields or malformed records.
- Keep builds deterministic and record upstream version/checksums.
- Do not ingest or redistribute TLG data without an explicit, documented decision about source and terms.
- Keep unrelated cleanup out of implementation PRs.

## Definition of done

An implementation PR is complete only when:

- acceptance criteria are met;
- tests covering the changed behavior pass;
- malformed/error cases have tests where relevant;
- documentation is updated if an assumption, public interface, feature schema, or build step changed;
- `research.md` or `wiki/decisions.md` is updated if new evidence changes a source-format or architecture assumption;
- the PR explains provenance implications and known limitations;
- the change can be understood and resumed by another agent from repository state alone.

## Preferred issue decomposition

Keep issues small enough to review independently. Separate mechanical conversion from scholarly enrichment. In particular, do not combine basic `.vert` parsing with work-boundary inference, Migne-column alignment, TLG crosswalks, or OCR correction unless an issue explicitly requires it.

## Repository map

- `src/pg_tf/`: converter implementation
- `tests/`: unit/integration tests and small synthetic or appropriately licensed fixtures
- `research.md`: evidence about upstream formats and conversion feasibility
- `wiki/architecture.md`: intended component/data model
- `wiki/decisions.md`: durable decisions and their rationale
- `wiki/backlog.md`: staged implementation plan
- `wiki/testing.md`: validation and TDD requirements
- `wiki/agentic-dev-loop.md`: exact automated loop

## Source data

Do not commit the full upstream Zenodo archive by default. Tests should prefer tiny synthetic fixtures that reproduce format edge cases. If a real upstream excerpt is required, verify licensing/attribution, keep it minimal, and document its source and exact record/version.
