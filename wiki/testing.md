# Testing and validation

PG-TF uses test-driven development because conversion bugs can look plausible while silently corrupting scholarly data.

## Test pyramid

### Parser unit tests

Cover at least:

- one valid document with one token;
- multiple tokens on one line;
- page/line transitions;
- exact five-column V3 token records;
- tabs/empty fields where valid;
- malformed field count;
- malformed or unbalanced `<doc>`/`<w>` tags;
- malformed `w id` coordinates;
- non-contiguous source IDs (detected and reported according to contract);
- punctuation attached to source words;
- combining marks and Greek Unicode normalization differences;
- GREgORI compound tags;
- unexpected additional columns.

Errors should identify the source file and source line where possible.

### Writer unit tests

Cover:

- one source token -> one word slot;
- deterministic slot order;
- page/line/volume span creation;
- raw feature preservation;
- no Unicode normalization of raw fields;
- metadata/provenance emission;
- stable output for repeated identical builds.

### Integration tests

Use a tiny representative `.vert` fixture to run parser -> converter -> TF load. Assert through the Text-Fabric API rather than only checking generated files as strings.

A small real-source excerpt may be used when necessary and properly attributed, but synthetic fixtures are preferred for edge cases.

## Corpus invariants

For a complete build, validation must include:

- source token count equals TF slot count;
- source token order equals TF slot order;
- all raw positional fields are present in output according to the feature contract;
- every source token belongs to exactly one volume, page, and line in the physical hierarchy;
- source-coordinate parse failures are zero unless an explicitly documented source exception exists;
- no page/line span is empty;
- generated TF dataset can be loaded successfully;
- build manifest names the exact Zenodo record/version and hashes;
- two builds from byte-identical input and converter revision produce semantically identical TF output.

## Golden tests

Golden files are appropriate for small feature-schema outputs but should remain readable and minimal. Do not commit megabytes of generated TF merely to compare directories.

When using a golden fixture, document why individual changes are expected. Never refresh a golden result blindly after a failing test.

## Unicode tests

Include cases that are visually identical but differ in code points. Raw features must preserve source bytes decoded as UTF-8; normalization, if introduced, belongs in explicitly named derived features.

Tests should exercise:

- precomposed vs combining diacritics;
- breathing/accent combinations;
- iota subscript;
- punctuation attached to a Greek word;
- apostrophe/elision-like characters;
- unusual but valid combining sequences.

## Morphology tests

The first converter only preserves the raw tag. If a morphology decoder is added later, its tests must be built from documented GREgORI examples and include unknown-tag behavior. Unknown values must never be silently coerced to a guessed category.

## Work-boundary tests

When work segmentation is introduced:

- boundary data must name its source;
- each work span must be token-addressable in the authoritative CGPG stream;
- overlapping or adjacent boundaries need explicit tests;
- mid-line/mid-page starts and ends must work;
- cross-volume work continuation must be modeled deliberately;
- importing a boundary must not alter token text.

## Property/invariant testing candidates

As implementation matures, consider property tests for:

- parser round-trip of raw token fields;
- monotonic source token ordinals;
- node span containment for physical hierarchy;
- arbitrary Unicode preservation;
- deterministic conversion independent of filesystem iteration order.

## CI expectations

Every PR should run the complete fast test suite. Full-corpus conversion may be too large for every PR and can be a separate validation job or release workflow, but parser and representative integration tests must remain fast enough for normal CI.

A PR that changes source parsing, feature schema, or provenance behavior must include corresponding tests and documentation changes.
