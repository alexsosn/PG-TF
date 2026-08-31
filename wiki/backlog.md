# Backlog and implementation phases

This file is the roadmap and dependency map. **GitHub issues are the executable work units.** Do not implement roadmap prose directly when a corresponding issue exists.

## How an automated agent should choose work

1. Search open PRs and issues first; never duplicate active implementation.
2. Prefer the lowest-numbered **unblocked** issue in the core path.
3. Parallel work is allowed only where dependencies below explicitly permit it.
4. Read the full issue: acceptance criteria and out-of-scope sections are authoritative.
5. If new source evidence invalidates an issue assumption, update `research.md` / `wiki/decisions.md` and refine the issue before expanding implementation scope.
6. Do not pull enrichment work into the core converter merely because it is convenient.

## Dependency graph

```text
CORE
#1 strict V3 parser ───────┐
                          ├──> #3 minimal TF conversion ──> #4 manifest + validator ──> #5 full V3 build ──> #6 alpha release
#2 acquisition + audit ───┘              │                         │
                                         │                         ├──> #9 work pilot ──> #10 corpus-wide works
                                         │                         │         │
                                         │                         │         └──> #12 identifier policy/pilot
                                         │                         │
                                         │                         └──> #11 Migne-column pilot
                                         │
#2 acquisition + audit ──> #7 GREgORI schema research ──> #8 morphology decoder
```

`#7–#12` are enrichment tracks. They must not block the first lossless alpha corpus unless an issue explicitly changes that policy.

## Phase 0 — repository and source contract

Status: **complete**

Delivered:

- repository scaffold and CI;
- tagged source-format research;
- MIT / upstream-data license separation;
- `AGENTS.md` and agentic-loop documentation;
- architecture and testing contracts;
- executable GitHub issue DAG.

Exit condition met: a new agent can select initial work from repository state alone.

## Phase 1 — strict source boundary

Status: **ready / parallelizable**

- [#1 — Implement strict streaming parser for CGPG V3 `.vert` files](https://github.com/alexsosn/PG-TF/issues/1)
- [#2 — Add reproducible Zenodo V3 acquisition and archive-wide schema audit](https://github.com/alexsosn/PG-TF/issues/2)

These two issues may proceed in parallel. #1 owns semantic parsing; #2 owns acquisition and corpus-wide evidence. #2 must reuse #1 once available rather than grow a second parser.

Exit condition: the exact V3 release can be acquired/audited and source records can be parsed strictly with useful diagnostics and no silent schema recovery.

## Phase 2 — minimal lossless Text-Fabric corpus

Status: **blocked on Phase 1**

- [#3 — Convert parsed V3 tokens into a minimal loadable Text-Fabric corpus](https://github.com/alexsosn/PG-TF/issues/3)
- [#4 — Add deterministic build manifest and corpus invariant validator](https://github.com/alexsosn/PG-TF/issues/4)

#3 is the first vertical TF slice and should remain deliberately small: `word` slots plus physical `volume/page/line` structure and raw source features. #4 proves fidelity independently of successful TF serialization.

Exit condition: a fixture can be converted, loaded, and verified token-for-token with deterministic provenance metadata.

## Phase 3 — full corpus and first release

Status: **blocked on Phase 2**

- [#5 — Build and validate the complete Zenodo V3 corpus](https://github.com/alexsosn/PG-TF/issues/5)
- [#6 — Package and publish the first reproducible alpha corpus release](https://github.com/alexsosn/PG-TF/issues/6)

Do not combine these. #5 proves that the converter works on all released data. #6 packages already-validated output and decides where large derived artifacts live.

Exit condition: a clean environment can reproduce and load a fully validated alpha corpus with exact upstream attribution and checksums.

## Phase 4 — GREgORI morphology enrichment

Status: **non-blocking enrichment; #7 waits for #2**

- [#7 — Inventory GREgORI tags and specify a lossless derived morphology schema](https://github.com/alexsosn/PG-TF/issues/7)
- [#8 — Implement GREgORI morphology decoder as additive TF features](https://github.com/alexsosn/PG-TF/issues/8)

The research/specification and decoder are intentionally separate. Unknown semantics remain unknown; raw `pos` is never replaced.

Exit condition: structured morphology is additive, versioned, evidence-backed, coverage-reported, and preserves every original tag.

## Phase 5 — works and authors

Status: **non-blocking enrichment; pilot first**

- [#9 — Add a provenance-aware work/author segmentation pilot for one composite PG volume](https://github.com/alexsosn/PG-TF/issues/9)
- [#10 — Expand verified work/author segmentation across the corpus](https://github.com/alexsosn/PG-TF/issues/10)

#9 must establish the boundary representation, provenance model, and validation strategy before #10 scales it. `work` is an independent slot-spanning structure and must not be forced into the physical page hierarchy.

Exit condition: work/author queries are supported with reviewable boundary evidence, while gaps/ambiguities remain explicit and the authoritative CGPG token stream is unchanged.

## Phase 6 — scholarly citations and identifiers

Status: **non-blocking enrichment; bounded pilots**

- [#11 — Map CGPG PDF coordinates to Migne columns for one pilot volume](https://github.com/alexsosn/PG-TF/issues/11)
- [#12 — Define permissible scholarly identifier sources and add a pilot CTS/TLG-style crosswalk](https://github.com/alexsosn/PG-TF/issues/12)

These are deliberately different problems. #11 is coordinate alignment. #12 is identifier assertion/provenance/licensing. Neither may treat PDF pages as canonical PG columns or assume that TLG-visible content is redistributable.

Future issues should be created only after the pilots establish stable contracts, e.g. corpus-wide Migne mapping, full identifier coverage, or work-internal citation hierarchies.

Exit condition: citation coordinate systems and identifier assertions are explicit, source-backed, and separately provenance-tracked.

## Phase 7 — optional OCR/layout layer

Status: **deferred; no ticket yet**

Create an implementation issue only when a concrete research/query use case justifies the additional data volume and model complexity. Candidate capabilities include page regions, line geometry, bounding boxes, confidence metadata, and links to OCR ground-truth/image coordinates.

Benchmark first; linked annotation may be preferable to dense TF features.

## Explicitly deferred

These are not permitted to expand the scope of current issues unless a new, independently justified ticket is created:

- OCR correction workflow;
- replacing Migne text with newer critical editions;
- generic Ancient Greek normalization beyond upstream fields;
- automatic authorship attribution;
- semantic embeddings;
- web UI;
- TLG text/content ingestion.
