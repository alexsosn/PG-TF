# ADR-0001: Represent zero-span textual entities with explicit empty slots

Status: Accepted  
Scope: project-family Text-Fabric architecture

## Decision

A source entity that **belongs to the textual sequence** and has an independent source position/order but no ordinary semantic slot MUST remain inside the Text-Fabric warp through an explicit empty/synthetic slot.

Do not move textual zero-span entities into a sidecar merely because Text-Fabric requires every non-slot node to span at least one slot. The empty slot is a technical positional anchor, not an assertion that a real character, token, or word exists in the source.

## Scope boundary

- Textual zero-span entities with their own source position/order get an explicit empty/synthetic slot.
- Ancestor/container nodes reuse descendant slots, including descendant empty slots; do not create one synthetic slot per ancestor.
- Genuinely non-textual nodes with no independent textual position (for example metadata, provenance, manuscript/resource abstractions) should anchor through their occurrence/locus or a documented O(1) technical anchor when required. That anchor must not be presented as textual content.
- Sidecars are for data genuinely outside the TF graph/API contract. **Zero span alone is not sufficient reason for a sidecar.**

## Required modelling contract

1. Keep the corpus's normal TF slot type and mark empty anchors explicitly (`type=empty`, `is_gap=1`, and/or `synthetic=1`).
2. Reports distinguish semantic/source slots, synthetic empty slots, and total TF slots.
3. Empty textual units receive an empty slot at their source position.
4. Empty containers receive an empty slot only if no descendant already supplies an anchor.
5. A wholly empty textual document/work receives an empty slot only if it would otherwise have no slot and the source model gives it a textual position.
6. Ancestors span descendant real/empty slots through normal `oslots`.
7. Never borrow a neighbouring real slot for an independently positioned textual entity.
8. Never fabricate visible Unicode/token/lexical content for an empty anchor.
9. Non-textual technical anchors must be documented and must not leak into APIs as fabricated content.

## Precedent

ETCBC/DSS creates empty slots for otherwise signless words/vacat clusters, and Nino-cunei `tfFromAtf` creates `cv.slot()` anchors for otherwise-empty textual lines/documents in Old Babylonian / Old Assyrian. The reusable principle is: **empty slots preserve textual position; they do not fabricate philological content.**

## Agent rule

Autonomous implementation/review agents MUST treat this ADR as the default architecture. Before proposing a zero-span sidecar, classify the object as textual, non-textual-but-in-graph, or genuinely outside the TF graph. A sidecar proposal whose only justification is empty `oslots` is an architectural error. Deviating requires a corpus-specific ADR and independent review.

## Tests

Pin deterministic/source-ordered empty anchors, semantic-vs-total slot counts, normal TF reachability, ancestor reuse, no borrowed real slots, no fabricated visible content, and non-textual-anchor rendering behavior.
