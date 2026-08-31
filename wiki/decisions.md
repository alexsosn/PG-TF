# Architecture and data decisions

This is the durable decision log. A decision may be revised when new evidence appears, but the old rationale should remain visible.

## D001 — `word` is the Text-Fabric slot type

**Status:** accepted for initial implementation

**Decision:** one validated V3 token becomes one Text-Fabric `word` slot.

**Rationale:** source files are token-oriented, provide one `<w>` ID per record, and supply token-level linguistic fields. This gives a direct count/order invariant between source and TF.

**Consequence:** punctuation attached to a source token remains part of its raw `word` feature unless a later derived layer explicitly tokenizes punctuation separately. The core converter must not retokenize upstream data.

## D002 — physical and intellectual structures are separate

**Status:** accepted

**Decision:** `volume/page/line` form the physical source structure. `work` spans word slots independently and is not nested into the same hierarchy.

**Rationale:** composite PG volumes contain several works/authors; work boundaries can occur inside pages or lines.

**Consequence:** section/navigation conventions must not assume every work aligns to page boundaries.

## D003 — preserve upstream linguistic fields verbatim

**Status:** accepted

**Decision:** raw V3 fields (`word`, `intuitive_form`, `lemma`, `intuitive_lemma`, `pos`) are copied without destructive normalization or reinterpretation.

**Rationale:** conversion and linguistic reinterpretation are separate scholarly operations. The GREgORI tagset is custom and source Unicode is evidence.

**Consequence:** normalized morphology and corrected text, if added, use additional feature names/layers.

## D004 — parse V3 as a strict streaming vertical format

**Status:** accepted

**Decision:** implement a streaming parser aware of `<doc>`/`<w>` structure and tab-separated positional fields rather than a generic XML-DOM conversion.

**Rationale:** the data model is a token stream with lightweight structural markers; streaming gives predictable memory use and better source-line diagnostics.

**Consequence:** parser must still enforce structural balance and quote/attribute expectations. “Not using an XML DOM” does not mean ignoring malformed markup.

## D005 — unknown schema is an error, not a guess

**Status:** accepted

**Decision:** unexpected field counts, unrecognized structural patterns, or malformed source coordinates fail validation unless a versioned schema variant is explicitly supported.

**Rationale:** earlier published Sketch Engine configuration mentions a sixth `headword` field while the inspected V3 PG 5 file has five fields. Silent tolerance could shift columns or drop data.

**Consequence:** archive-wide audit may lead to multiple explicit source schemas.

## D006 — PDF page is not PG/Migne column

**Status:** accepted

**Decision:** source PDF page and line coordinates are stored under names that identify them as PDF/source coordinates. Canonical Migne column data is a separate future layer.

**Rationale:** upstream documentation explicitly describes its page marker as page number from the PDF.

**Consequence:** user-facing citation APIs must name the coordinate system.

## D007 — prefer one unified PG corpus initially

**Status:** accepted pending performance validation

**Decision:** target one Text-Fabric corpus for all converted CGPG volumes rather than one corpus per work or volume.

**Rationale:** cross-work/cross-volume querying is a primary benefit of Text-Fabric and corpus fragmentation creates discovery/loading complexity.

**Revisit if:** full-corpus benchmarks show unacceptable load/query/build characteristics.

## D008 — CGPG V3 is the authoritative token source

**Status:** accepted for PG-TF core

**Decision:** downstream projects may provide useful work boundaries or crosswalks, but the core PG-TF token stream is derived from the pinned CGPG V3 release.

**Rationale:** edited aggregations can contain corrections, deduplication, or replacement texts that make offsets/text diverge from CGPG.

**Consequence:** external work-boundary metadata must be verified against CGPG and carry independent provenance.

## D009 — license code and derived data separately

**Status:** accepted

**Decision:** PG-TF code/original documentation uses MIT. CGPG-derived corpus data retains applicable upstream CC BY 4.0 attribution/provenance; third-party data keeps its own terms.

**Rationale:** a software license on the converter cannot relicense its inputs.

**Consequence:** generated releases need their own license/attribution metadata rather than relying solely on root `LICENSE`.

## D010 — TLG is reference-only until explicitly integrated

**Status:** accepted

**Decision:** the repository may link to TLG as a scholarly reference, but no TLG data is ingested or redistributed in the initial converter.

**Rationale:** identifier usefulness does not itself establish a data acquisition/reuse policy.

**Consequence:** a future TLG integration requires a specific issue/decision describing fields, provenance, access method, and terms.

## Open decisions

The following should be resolved by evidence/tests before the corresponding implementation milestone:

- exact TF feature names and metadata conventions;
- whether PDF page/line values belong only on physical nodes or are also denormalized onto word slots;
- exact acquisition mechanism for a pinned Zenodo V3 artifact;
- handling of any real V3 files that contain six positional columns or other variants;
- corpus release/versioning scheme;
- representation of work-boundary confidence and provenance;
- source(s) for Migne column mappings;
- permissible and useful TLG identifier crosswalk strategy.
