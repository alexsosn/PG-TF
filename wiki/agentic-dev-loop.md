# Automatic agentic development loop

The repository is structured so that a new agent can enter with no conversational history and make a bounded, reviewable contribution.

## 1. Select work

Start from GitHub issues, not from an open-ended request to improve the repository.

Before claiming an issue:

- read the issue and all comments;
- search open PRs for the same issue, title, files, or acceptance criteria;
- check whether another issue already implements the same behavior;
- if work is already in progress, contribute review or a follow-up issue rather than create a competing PR.

Prefer the smallest unblocked issue whose acceptance criteria can be verified automatically.

## 2. Reconstruct context

Read `AGENTS.md`, `research.md`, the relevant wiki pages, and adjacent implementation/tests. Resolve source-format questions against the exact upstream version named in the issue.

Write down, in the issue or local work notes:

- observable goal;
- inputs and outputs;
- acceptance criteria;
- invariants that must remain true;
- out-of-scope behavior;
- external-data/provenance assumptions;
- likely failure modes.

If upstream evidence contradicts the wiki, preserve the evidence and update the documentation as part of the change.

## 3. Test first

For implementation work, establish the failing behavior before production code.

Preferred sequence:

1. add or refine a minimal fixture;
2. add a failing test for one acceptance criterion;
3. confirm that it fails for the intended reason;
4. implement the smallest behavior that makes it pass;
5. repeat for the next criterion;
6. refactor only after tests are green.

Never make a parser permissive merely to make a fixture pass. Unknown schema should normally produce a precise error.

## 4. Preserve layers

Mechanical source conversion and scholarly enrichment are separate concerns.

The lossless layer must preserve:

- source token order;
- exact token strings;
- raw lemma/normalized fields as supplied;
- raw GREgORI tag;
- original source IDs;
- source version/checksum provenance.

Corrections, morphological decomposition, work segmentation, TLG/CTS mappings, and Migne-column mappings belong in explicit derived layers with their own tests and provenance.

## 5. Validate at three levels

### Unit

Test parser/data-model behavior with small fixtures and edge cases.

### Integration

Convert at least one representative real or licensed fixture and load the resulting Text-Fabric dataset.

### Invariants

Compare source and output counts/order/hashes where applicable. Examples:

- source token count == TF slot count;
- source word ID order == generated slot order;
- every page/line node spans exactly the intended slots;
- no raw feature changes Unicode code points;
- repeated builds from identical input are deterministic.

## 6. Self-review before PR

Review the diff as a skeptical maintainer:

- Is any source assumption undocumented?
- Is data silently normalized, corrected, or dropped?
- Can malformed input be localized to file/line/token?
- Did scope expand beyond the issue?
- Are tests asserting behavior rather than implementation details?
- Could a second agent understand the new feature without chat history?
- Did licensing/provenance handling change?

## 7. PR contract

The PR description should contain:

- issue reference;
- concise behavior summary;
- acceptance criteria checked off;
- tests run;
- source data/version used for validation;
- schema/provenance changes;
- known limitations and explicit follow-ups.

Avoid vague descriptions such as “improves parser.” State observable behavior.

## 8. Review and repair

Treat review findings as evidence. Reproduce alleged bugs with a test where possible, then fix them. Do not patch around a failing assertion without deciding whether the assertion or implementation represents the correct contract.

If review reveals a larger unrelated problem, open a follow-up issue rather than expanding the PR indefinitely.

## 9. Handoff

At the end of a loop, repository state must be sufficient for the next agent. Update one or more of:

- tests;
- issue status/comments;
- `research.md`;
- `wiki/decisions.md`;
- `wiki/backlog.md`.

Do not leave critical assumptions only in PR chat.

## Recommended issue template

Every implementation issue should answer:

- **Problem:** what is currently missing/wrong?
- **Evidence:** what source/example demonstrates it?
- **Acceptance criteria:** what observable outcomes define success?
- **Tests:** what new tests/fixtures are expected?
- **Dependencies:** what must exist first?
- **Out of scope:** what adjacent work should not be attempted?
- **Provenance/licensing:** does this ingest or transform external data?

## Concurrency rule

One active implementation path per issue. An agent must not open a second implementation PR for an issue that already has a viable active PR. Review, test, or create a clearly scoped follow-up instead.
