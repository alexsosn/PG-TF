# Backlog and implementation phases

This file is a sequencing guide. Concrete work should be represented by GitHub issues with narrower acceptance criteria.

## Phase 0 — repository and source contract

Status: **in progress**

- repository scaffold;
- tagged source-format research;
- license separation between converter and upstream data;
- agentic development rules;
- architecture/testing documentation;
- Python package skeleton and CI smoke test;
- create initial implementation issues from the milestones below.

Exit condition: a new agent can identify the first converter task, its evidence, and its tests without relying on chat history.

## Phase 1 — strict V3 vertical parser

Suggested issues:

1. Define typed source records and explicit V3 schema.
2. Parse `<doc>` and `<w>` records in a streaming reader.
3. Parse `PDF Page N, Line N - word id N` coordinates.
4. Add precise source-file/source-line diagnostics.
5. Add schema-drift detection for field counts and unknown structure.
6. Add Unicode and punctuation preservation tests.

Exit condition: the parser can validate representative V3 files and expose raw records without Text-Fabric concerns.

## Phase 2 — core Text-Fabric conversion

Suggested issues:

1. Define initial TF feature metadata and naming.
2. Map source tokens to `word` slots.
3. Create `volume`, `page`, and `line` nodes/spans.
4. Preserve the five observed V3 token fields and original source IDs.
5. Build/load an integration fixture with Text-Fabric.
6. Add deterministic build manifest and checksums.
7. Add corpus invariant validator.

Exit condition: a pinned V3 release can be converted mechanically into a loadable, provenance-rich TF corpus with matching source/output token counts.

## Phase 3 — archive-wide validation and release engineering

Suggested issues:

1. Audit every V3 file for actual schema variants.
2. Decide how legitimate variants such as possible `headword` should be represented.
3. Measure conversion runtime, memory, and output size.
4. Add reproducible acquisition command for exact Zenodo record.
5. Define generated-data repository/release strategy.
6. Add citation metadata and upstream attribution to TF metadata.
7. Produce first alpha corpus release.

Exit condition: full V3 builds are reproducible and documented, with no silently skipped source records.

## Phase 4 — morphology enrichment

Suggested issues:

1. Locate/version authoritative GREgORI tag documentation.
2. Enumerate tag inventory across V3.
3. Design lossless raw-to-derived morphological feature mapping.
4. Implement decoder with unknown-tag reporting.
5. Validate mapped features statistically and with documented examples.

Exit condition: structured morphology is additive, documented, tested, and never replaces raw `pos`.

## Phase 5 — works and authors

Suggested issues:

1. Inventory single-work vs multi-work PG volumes.
2. Evaluate Open Greek Corpus CGPG carve metadata as boundary evidence.
3. Design work-boundary provenance schema.
4. Import/verify one multi-work volume as a pilot.
5. Add author/title metadata and identifier fields.
6. Expand to all volumes with reviewable boundary evidence.

Exit condition: users can query by work/author without changing the authoritative CGPG token stream.

## Phase 6 — scholarly citation crosswalks

Suggested issues:

1. Map CGPG PDF pages to Migne columns for a pilot volume.
2. Model column subdivisions where meaningful.
3. Investigate work-internal citation sources.
4. Determine permissible and reproducible use of TLG identifiers/metadata.
5. Add CTS/TLG crosswalks from open/licensed sources where appropriate.
6. Validate citation round trips against sampled printed/digital editions.

Exit condition: citations are explicit about coordinate system and provenance; no PDF page is mislabeled as a PG column.

## Phase 7 — optional OCR/layout layer

Only pursue if there is a concrete research use case.

Potential work:

- link OCR ground-truth/layout datasets;
- page regions and line geometry;
- bounding boxes;
- confidence/recognition metadata;
- image/PDF coordinate linking.

This may be better as linked annotation data than as a dense core TF feature layer. Benchmark before committing to a representation.

## Explicitly deferred

The following should not block the core converter:

- OCR correction workflow;
- replacing Migne text with newer critical editions;
- generic Ancient Greek normalization beyond upstream fields;
- automatic authorship attribution;
- semantic embeddings;
- web UI;
- TLG content ingestion.
