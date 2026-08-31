---
title: PG-TF source-format and conversion research
status: current
date: 2026-08-31
tags:
  - research
  - patrologia-graeca
  - cgpg
  - text-fabric
  - sketch-engine
  - ancient-greek
  - corpus-conversion
  - provenance
---

# PG-TF source-format and conversion research

## Scope

This document records the evidence gathered before implementation of the PG-TF converter. It distinguishes direct observations from design proposals so that future agents do not silently turn assumptions into format contracts.

Primary sources:

- CGPG LREC 2026 paper: https://aclanthology.org/2026.lrec-1.517/
- Tagged corpus, Zenodo V3: https://zenodo.org/records/19915273
- Raw aligned OCR: https://github.com/calfa-co/Patrologia-Graeca/tree/main
- TLG reference site: https://stephanus.tlg.uci.edu/
- Text-Fabric package: https://pypi.org/project/text-fabric/

Secondary source worth evaluating for future work segmentation only:

- Open Greek Corpus: https://github.com/open-greek/open-greek-corpus

## Executive finding

The linguistic Zenodo release is a strong candidate for direct conversion to Text-Fabric. Its files are Sketch Engine vertical (`.vert`) data: structural tags are interleaved with tab-separated token records. A basic lossless converter is technically straightforward. The difficult parts are scholarly structure and citation enrichment: identifying individual works inside multi-work PG volumes, mapping authors/work identifiers, and distinguishing PDF-page coordinates from canonical Migne column references.

Recommended implementation order:

1. lossless `.vert` parsing and validation;
2. physical `volume/page/line/word` Text-Fabric structure;
3. preservation of all upstream linguistic annotations and source IDs;
4. reproducible build/provenance metadata;
5. work/author segmentation as an independent enrichment layer;
6. TLG/CTS/Migne citation crosswalks only after their provenance and reuse constraints are explicitly resolved.

## Upstream corpus

The current CGPG repository describes 33 Patrologia Graeca volumes and roughly six million words. It states that the GitHub repository contains aligned OCR, while lemma/POS-tagged files are distributed through Zenodo V3. The raw OCR files use `$0` for volume, `$8` for PDF page, and `$9` for starting line.

The LREC 2026 paper describes the corpus as OCR of nineteenth-century polytonic Greek editions with subsequent linguistic annotation. It reports roughly six million annotated tokens and emphasizes traceability to OCR/layout information.

The upstream repository identifies the CGPG material as CC BY 4.0. PG-TF software is MIT-licensed, but derived corpus releases must retain the upstream attribution and provenance requirements.

## Zenodo V3 format

Zenodo describes the release as Sketch Engine XML files with linguistic markup. In practice, the inspected V3 file is a Sketch Engine vertical file: XML-like structural records plus tab-separated positional attributes.

Observed shape:

```text
<doc id="PG 5 - AI processed">
<w id="PDF Page 14, Line 1 - word id 1">
Τὰς\tτας\tὁ\tο\tDET
</w>
<w id="PDF Page 14, Line 1 - word id 2">
ἐπιστολὰς\tεπιστολας\tἐπιστολή\tεπιστολη\tN+Com
</w>
...
</doc>
```

The five observed positional columns are:

| column | provisional name | example | interpretation |
| --- | --- | --- | --- |
| 1 | `word` | `ἐπιστολὰς` | OCR/token surface form, punctuation may remain attached |
| 2 | `intuitive_form` | `επιστολας` | normalized/search-oriented form without polytonic diacritics |
| 3 | `lemma` | `ἐπιστολή` | annotated lemma |
| 4 | `intuitive_lemma` | `επιστολη` | normalized/search-oriented lemma |
| 5 | `pos` | `N+Com` | GREgORI morphosyntactic/POS tag |

Do not rename or decompose `pos` during the lossless conversion phase. Preserve the original tag verbatim. Derived grammatical features can be added later after the full GREgORI tagset has been studied and tested.

### Direct audit of one V3 `.vert` file

A PG 5 V3 file supplied for inspection was audited programmatically.

- file size: 5,045,170 bytes
- SHA-256: `fd785e5b92c01068ebc2a6c2ff9c9df52e2be024f40b39bbc885f4c00227a705`
- document tag: `PG 5 - AI processed`
- token records: 47,788
- `<w>` records: 47,788
- every token record has exactly 5 tab-separated fields
- every `<w>` ID matched `PDF Page <n>, Line <n> - word id <n>`
- word IDs are contiguous from 1 through 47,788
- observed PDF pages: 14 through 736
- observed line numbers: 1 through 56

This establishes the structure of the inspected file, not a guarantee that every file in the V3 archive is identical. The converter must validate each file and report schema drift rather than assume all files are clean.

### `headword` discrepancy

Sketch Engine configuration published with an earlier release enumerated a sixth positional attribute named `headword`. The inspected V3 PG 5 file contains five fields consistently and no sixth `headword` column.

Implementation consequence: do not hard-code an undocumented sixth field, and do not silently discard extra fields if another V3 file contains one. The parser should have an explicit schema detector/validator and tests for both expected and unexpected field counts.

## Parsing strategy

Treat the source as a streaming vertical format rather than loading it as a generic XML DOM.

Reasons:

- the semantic unit is a sequence of token rows plus lightweight structural markers;
- one token is wrapped in one `<w>` block;
- the useful content is tab-separated positional data;
- a streaming parser makes validation, error localization, and large-corpus processing simple;
- it avoids implying stronger XML semantics than the format actually uses.

The parser should still validate tag balancing and attributes. Malformed input must fail with a source filename and line number.

## Proposed Text-Fabric model

### Slot type

`word`

Each source token becomes one slot. Slot order must equal source token order exactly.

### Physical nodes

- `volume`
- `page`
- `line`
- `word` slot

The natural physical hierarchy is `volume -> page -> line -> word`.

The `<w id>` string should be parsed into numeric source features, for example:

- `pdf_page`
- `line_number`
- `source_word_id`

The original source ID should also be retained verbatim, for auditability.

### Word features

Core lossless features:

- `word`
- `intuitive_form`
- `lemma`
- `intuitive_lemma`
- `pos`
- `source_id`

Potential later derived features, only after a tagset-specific design decision:

- broad part of speech
- subtype
- case
- gender
- number
- person
- tense
- voice
- mood
- degree

Do not overwrite the raw `pos` value with a normalized representation.

### Work and author structure

A PG volume can contain several authors and works, and work boundaries may occur inside a page. Therefore `work` should not be forced into the physical `volume/page/line` section hierarchy.

Recommended model:

- physical nodes describe the edition/layout hierarchy;
- `work` nodes independently span their word slots;
- author/title/TLG/CTS identifiers are features on work nodes, or linked enrichment metadata;
- overlapping physical and intellectual structures remain possible.

A single unified PG Text-Fabric corpus is preferable to one corpus per individual Greek work unless performance measurements prove otherwise. It supports cross-work queries and avoids forcing clients to discover/load hundreds of separate corpora.

## Work-boundary enrichment

The upstream CGPG repository is volume-oriented and does not by itself provide complete machine-readable work boundaries for every multi-work volume.

The Open Greek Corpus project currently contains curated machinery for carving a number of multi-work CGPG volumes into works, including plans, row ranges, incipit verification, and identifier crosswalk work. This is promising secondary evidence, but PG-TF should not automatically replace CGPG source text with Open Greek Corpus text because that project also performs corrections, deduplication, substitutions, and other editorial operations.

Recommended use:

- treat CGPG V3 as the authoritative token/annotation source for PG-TF;
- investigate Open Greek Corpus metadata as a candidate source of work-boundary hints;
- verify boundaries against the original CGPG V3 token stream;
- record provenance for every imported boundary/crosswalk.

## Citation problem

The source IDs in the inspected `.vert` file give PDF page and line coordinates. The raw CGPG repository likewise documents PDF page markers. These must not be mislabeled as canonical Patrologia Graeca page/column citations.

A research-grade corpus should eventually distinguish at least:

- source PDF page
- source OCR line
- PG volume
- Migne column, when available
- column subdivision such as A/B/C/D, when recoverable
- work-internal book/chapter/section references, where available
- scholarly identifiers such as TLG or CTS, where legally and technically appropriate

This is a separate enrichment task from basic TF conversion.

## TLG

TLG is valuable for author/work identification and scholarly crosswalks, but PG-TF currently contains no TLG-derived data. Do not scrape, copy, or redistribute TLG content merely because a work can be identified there. Any future TLG integration must document exactly which identifiers/metadata are used, their source, and applicable terms.

## Text reconstruction and punctuation

The inspected first column preserves punctuation attached to some tokens, while the normalized form may omit it. Before promising exact plain-text round trips, implementation must test:

- whitespace reconstruction;
- punctuation attachment;
- whether source line boundaries imply spacing;
- whether empty or non-word records occur elsewhere in V3;
- Unicode normalization forms;
- combining-mark preservation.

A lossless token-feature conversion does not automatically imply byte-for-byte reconstruction of the original `.vert` file.

## Unicode policy

Ancient Greek diacritics make silent Unicode normalization especially risky. The converter should preserve source strings exactly by default. Any NFC/NFD-normalized feature should be additional and documented, never a replacement for the source value.

Tests should include visually identical strings with different code-point sequences.

## Expected conversion difficulty

| target | difficulty | main reason |
| --- | ---: | --- |
| lossless V3 token/features -> TF | 2/10 | regular vertical format, explicit token IDs |
| volume/page/line structure | 3/10 | coordinates already encoded but require validation |
| robust morphology exposure | 4/10 | GREgORI tag semantics need explicit mapping |
| reliable work segmentation | 6/10 | multi-work volumes and mid-page boundaries |
| research-grade identifiers/citations | 7/10 | Migne/TLG/CTS alignment and provenance |
| full OCR/layout geometry | 8/10 | separate layout/ground-truth information and more complex node/edge design |

## Known risks and open questions

1. **Archive-wide schema drift.** Only one V3 file has been exhaustively checked locally so far.
2. **Published schema mismatch.** Earlier Sketch Engine settings mention `headword`; the inspected V3 file does not contain it.
3. **Custom annotation semantics.** GREgORI tags must not be naively mapped to Universal Dependencies or a conventional POS inventory.
4. **Work boundaries.** Many PG volumes are composite editions.
5. **Citation semantics.** PDF page is not Migne column.
6. **OCR errors are data.** Corrections must be separated from faithful source conversion and carry provenance.
7. **Unicode.** Normalization can destroy evidence or make round-trip checks misleading.
8. **Licensing layers.** MIT for converter code does not relicense CC BY 4.0 upstream data or third-party metadata.
9. **Reproducibility.** Builds must pin the exact Zenodo record/version and record input hashes.
10. **Scale.** Roughly six million slots should be practical, but performance and generated feature sizes should be measured before adding rich layout layers.

## Initial acceptance criteria for the converter

The first production milestone should not claim scholarly enrichment. It should prove a faithful mechanical conversion:

- parse every V3 `.vert` file without silent recovery;
- validate positional field counts and structural tag balance;
- preserve token order and every raw token field;
- parse source page/line/word coordinates while retaining the original ID;
- create deterministic TF nodes/features;
- verify input token count equals TF slot count;
- produce a machine-readable build manifest with source URL/version/checksums;
- fail clearly on malformed or novel schema rather than guessing;
- test representative Greek Unicode, punctuation, and GREgORI compound tags.

## Research policy for future agents

When new evidence changes a format assumption, update this document and add or amend a decision in `wiki/decisions.md`. Cite the exact upstream version/commit/record. Do not remove contradictory observations: record the discrepancy and the resolution.
