# Architecture

## Purpose

PG-TF converts the linguistically annotated CGPG Patrologia Graeca release into Text-Fabric while preserving a verifiable path back to the upstream data. Scholarly enrichments should be possible without contaminating the lossless source layer.

## Pipeline

```text
Zenodo V3 .vert files
        |
        v
source acquisition + manifest
        |
        v
streaming vertical parser
        |
        v
validated source records
        |
        +----------------------+
        |                      |
        v                      v
physical structure       raw token features
(volume/page/line)       (word/lemma/POS/etc.)
        |                      |
        +----------+-----------+
                   v
            Text-Fabric writer
                   |
                   v
          deterministic TF dataset
                   |
                   v
        optional enrichment layers
      (works, authors, Migne, IDs)
```

## Component boundaries

### Acquisition

Responsibilities:

- obtain a pinned upstream release;
- avoid implicit “latest” builds;
- record record/version/URL;
- calculate checksums;
- expose input files to the parser in deterministic order.

The acquisition layer must not modify corpus content.

### Vertical parser

Responsibilities:

- stream records from `.vert` files;
- parse `<doc>` and `<w>` markers;
- validate balanced structure;
- split token rows by tabs according to an explicit schema;
- parse `<w id>` source coordinates;
- return typed records with source filename/line diagnostics.

The parser should not create Text-Fabric nodes directly. Keeping parsing separate makes format validation testable without TF and prevents conversion logic from hiding malformed source data.

### Source model

A small internal model should represent at least:

- document/volume identity;
- token ordinal;
- original `w` ID;
- PDF page;
- source line;
- source word ID;
- the five raw positional fields observed in V3.

If the archive reveals legitimate schema variants, represent them explicitly rather than stuffing unknown columns into an untyped catch-all structure.

### Text-Fabric writer

Responsibilities:

- create deterministic slots and non-slot nodes;
- copy raw source features without destructive normalization;
- assign physical spans;
- emit TF metadata and build provenance;
- validate slot/node counts before declaring success.

Use the Text-Fabric conversion APIs rather than hand-writing opaque feature files unless measurements show a concrete reason otherwise.

## Core TF model

### Slots

`word` is the slot type. One validated source token maps to one slot.

### Physical nodes

`volume`, `page`, and `line` describe the edition/source hierarchy. They should be generated from source coordinates, not inferred from punctuation or typography.

### Raw word features

Initial names are expected to include:

- `word`
- `intuitive_form`
- `lemma`
- `intuitive_lemma`
- `pos`
- `source_id`
- `pdf_page`
- `line_number`
- `source_word_id`

Before implementation freezes names, verify Text-Fabric naming conventions and whether page/line coordinates are better attached to physical nodes than redundantly to every word. Raw semantic content must still remain recoverable.

## Intellectual structure

`work` is an overlapping layer, not part of the physical section hierarchy. A work can start or end mid-page, and a PG volume may contain several works/authors.

Possible work features:

- `title`
- `author`
- `pg_volume`
- external identifier(s)
- boundary source/provenance
- confidence/review status when boundaries are inferred or imported

Do not create one TF corpus per work by default. Start with one unified corpus and benchmark it before introducing fragmentation.

## Morphology

The source `pos` value is authoritative as an upstream annotation string. A future morphology module may derive structured features, but it must:

- retain `pos` unchanged;
- document the exact GREgORI tagset/version used;
- be reversible or auditable;
- test composite/polylexical tags and unknown tags;
- never invent grammatical values from incomplete tags.

## Provenance model

Every build should emit enough information to reproduce and audit it:

- PG-TF converter version/commit;
- upstream Zenodo record and version;
- input archive checksum;
- per-file checksums where practical;
- timestamp as informational metadata only, not as an input to deterministic content;
- enabled enrichment layers and their versions/sources;
- feature schema version.

Derived enrichment should carry its own source metadata. A work boundary imported from another project is not CGPG source data and must be distinguishable from it.

## Corrections policy

OCR correction is not part of the first converter layer. If correction support is added later:

- keep upstream form;
- add corrected form separately;
- record correction source/method;
- distinguish automated and reviewed corrections;
- never rewrite provenance as if the corrected token came from CGPG.

## Citation layers

Keep these concepts distinct:

1. PDF page/line coordinates from CGPG;
2. PG volume;
3. canonical Migne column/subcolumn coordinates;
4. work-internal references;
5. TLG/CTS or other scholarly identifiers.

A mapping can connect them, but one must never stand in for another without evidence.

## Non-goals for the first milestone

- OCR correction;
- full page-layout geometry;
- automatic work identification;
- TLG scraping or redistribution;
- Migne column reconstruction;
- replacement of GREgORI tags with another tagset;
- editorial deduplication or substitution of better editions.
