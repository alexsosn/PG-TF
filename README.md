# PG-TF

Text-Fabric conversion project for the **CGPG / Patrologia Graeca** corpus produced by Calfa and GREgORI.

> **Status:** repository scaffold and source-format research. The converter itself is not implemented yet.

## Goal

PG-TF will build a reproducible, source-aware Text-Fabric dataset from the linguistically annotated CGPG release. The first implementation target is a lossless conversion of the Zenodo V3 Sketch Engine vertical (`.vert`) files, preserving token order, source coordinates, lemmas, normalized forms, and the original GREgORI morphosyntactic tags.

The intended core model is:

- `word` as the Text-Fabric slot type;
- physical source structure as `volume` → `page` → `line`;
- work/author structure as an independent layer that may overlap physical pages;
- upstream annotation preserved verbatim before any derived normalization;
- stable provenance back to the CGPG release and source PDF coordinates.

See [`research.md`](research.md) for the source-format audit and [`wiki/`](wiki/) for the development plan and agentic workflow.

## Upstream and reference projects

PG-TF is downstream of, or uses identifiers/context from, the following projects:

- **CGPG paper (LREC 2026):** [The Patrologia Graeca Corpus: OCR, Annotation, and Open Release of Noisy Nineteenth-Century Polytonic Greek Editions](https://aclanthology.org/2026.lrec-1.517/)
- **Tagged CGPG corpus, Zenodo V3:** [Patrologia Graeca (OCRized and analyzed texts), record 19915273](https://zenodo.org/records/19915273)
- **CGPG raw OCR repository:** [calfa-co/Patrologia-Graeca](https://github.com/calfa-co/Patrologia-Graeca/tree/main)
- **Thesaurus Linguae Graecae (TLG):** [stephanus.tlg.uci.edu](https://stephanus.tlg.uci.edu/)

TLG is listed as a scholarly identifier/reference resource. PG-TF does not currently include or redistribute TLG data.

## Repository layout

```text
.
├── AGENTS.md             # entry point for coding agents
├── research.md           # tagged source-format and conversion research
├── wiki/
│   ├── README.md         # documentation map
│   ├── agentic-dev-loop.md
│   ├── architecture.md
│   ├── backlog.md
│   ├── decisions.md
│   └── testing.md
├── src/pg_tf/            # converter package
└── tests/                # tests and future source fixtures
```

## Development

The project is deliberately TDD-first. Every implementation issue should define observable acceptance criteria and add a failing test or validation fixture before production code. Agents should check for existing work before starting an issue and should leave the repository in a state that another agent can resume from GitHub issues, PRs, tests, and the wiki alone.

See [`wiki/agentic-dev-loop.md`](wiki/agentic-dev-loop.md) and [`AGENTS.md`](AGENTS.md).

## Licensing

The **PG-TF software and original repository documentation** are released under the [MIT License](LICENSE).

The **upstream CGPG corpus and annotations are separate material** and are currently identified by the upstream project as **CC BY 4.0**. Any generated Text-Fabric data derived from CGPG must retain the required attribution and provenance and must not be treated as MIT-licensed merely because it is produced by this repository.

Third-party resources such as TLG have their own terms and are not relicensed by PG-TF.

## Citation and provenance

Until a PG-TF release is published, cite the upstream CGPG dataset and the LREC 2026 paper. Generated corpora should record the exact upstream Zenodo record/version and, where feasible, checksums of the input archive/files used for the build.
