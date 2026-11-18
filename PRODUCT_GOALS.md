# Product goals — PG-TF

## Users and purpose

Researchers of patristic and late-antique Greek literature should be able to query the annotated CGPG / Patrologia Graeca source at corpus scale, recovering every result's original Greek reading, source work, page/line coordinates, lemma and GREgORI morphosyntactic annotation.

## Representative research journeys

1. Load a reproducible native TF corpus built from the selected official CGPG Zenodo release.
2. Search attested Greek token forms, normalized forms, lemmas and original morphosyntactic tags without substituting guessed analyses.
3. Locate hits by author/work and by physical volume/page/line, respecting their potentially overlapping structures.
4. Inspect noisy OCR, provenance and uncertain annotations instead of treating machine annotation as verified philological fact.
5. Cite the exact source release and original coordinates for reproducible passage extraction.

## Minimum usable first milestone

The repository is presently a research scaffold, not a released converter. First implement a small **real vertical (.vert) input → native word-slot TF** slice that conserves original token order, text/annotation values and source coordinates, with independent TF reload and one executable scholar query. Expand to full corpus only after that path works. Record the real supported source version and data-attribution boundary.

## Non-goals and priority

Do not rebuild CGPG OCR/annotation pipelines, redistribute TLG content, create a generic TEI framework or overengineer source certification. Reuse upstream vertical format and existing TF writer/build primitives where justified. The next development effort must produce usable converted Greek data, not only additional scaffolding.

## Autonomous-development decision rules

- Select work by the researcher journeys above, not issue count or the ease of adding tests. Prefer finishing active PRs and releases over opening new tickets.
- Before introducing a new adapter, workflow, registry, protocol, or validation layer, demonstrate that an existing mechanism cannot meet a concrete acceptance need.
- Preserve source fidelity, access controls, security, and applicable upstream rights. Do not invent inferred text or silently weaken correctness gates.
- Batch repetitive data changes and reuse parameterized tests. Keep independent adversarial review proportional to risk, and validate the actual researcher path.
- If two successive cycles do not advance a journey or remove an observed blocker, reconsider the approach. Stop when the scoped deliverable works instead of generating optional infrastructure work.
