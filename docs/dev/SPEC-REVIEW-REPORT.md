# SPEC review report

## Current gate

Technical QC: **Ready** for the reviewed specification. Human acceptance: **pending**. Dependent PLAN authoring remains blocked by that human checkpoint.

Scope: [SPEC.md](SPEC.md), including [spec/GAMEPLAY.md](spec/GAMEPLAY.md). Current reviewed Git blobs:

| Artifact | Reviewed blob |
| --- | --- |
| SPEC.md | bdf553202ed51a8983154a94b97cc5dd1b8b6672 |
| spec/GAMEPLAY.md | 4ab78ecd8ef23cc0438b391b45c862970a2364b0 |

Governing sources were inspected at published acceptance commit `2ba431411629f5213bb3f63df51e359ff0d65a63`:

| Source | Blob |
| --- | --- |
| [PROJECT.md](PROJECT.md) | 01071a30988992a29a75e26be6577b0fcc6257e1 |
| [ARCHITECTURE.md](ARCHITECTURE.md) | e9aadc3553baf30230cbafd1b8735c51c71fa510 |
| [DECOMPOSITION.md](DECOMPOSITION.md) | f182fecfff88394c54948d124957335b9b014038 |
| [AGENTS.md](../../AGENTS.md) | 2163357a7e0db0bc0900a87c9564dfe50a52776e |
| [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) | ae71cfc01aaf694f5c4a7b055d2f15d469d7f7a7 |

Both confirmed findings below are resolved in Revision 1. No technical blocker or design amendment remains. Detailed gameplay defaults are proposed for SPEC approval under PROJECT's explicit delegation; technical readiness does not accept those choices on the human's behalf. No product code, provisioning, or product-test result is certified.

## Initial review

Reviewed on 2026-10-22 by the author applying sdd-specify and the bundled development-document QC criteria. Initial pending blobs: SPEC `e227ec0d9c088794e35f8f2ee6460c3e2cbd6ae3`; GAMEPLAY `7347bbbaaba56d40437ddc95fd53d9bc56c03aae`.

| Coverage group | Conformance and acceptance assessment |
| --- | --- |
| Scope and classic rules: G01–G05, K01–K06 | Covers the accepted board, seven pieces, collisions, clockwise rotation, soft drop, locking, clears, next preview, game over, scoring, and progression. Preserves all PROJECT exclusions. Exact geometry, independent draws, failed-downward locking, and score/gravity values are explicit proposed defaults, not claims of historical rule fidelity. |
| State and errors: G02/G06, R05, K07, A07–A08 | Immutable transition/snapshot guarantees and deterministic inputs preserve core/controller ownership. Initial state-precondition ambiguity is S01 below. Source failure is atomic for caller-owned state; external draw effects have no rollback guarantee. |
| Browser/session lifecycle: R01–R03, A01–A05/A08 | Focus, key repeat, explicit pause, restart, clock adaptation, binding ownership, resize, and disposal have objective outcomes. The 250 ms browser cap is separate from core advancement. Initial malformed-timestamp gap is S02 below. |
| Presentation and failure: R04–R05, A06/A08 | Core projects canonical drawing geometry; Canvas and HTML consume one snapshot. Viewport/DPR, native controls/focus, readable startup/runtime failure, and cleanup have acceptance routes. |
| Distribution and releases: R06, A09–A10 | Static hosting at root/prefix, pinned tooling, separate type checking, production-entry verification, both browser targets, version agreement, and phase publication follow the accepted architecture and release policy. |
| Document ownership and seams | Parent owns browser/cross-component contracts; child owns core rules. Traceability names accepted components without inventing physical modules. PLAN/layout retain sequencing/path ownership; central browser policy retains setup/recovery ownership. |

| Finding | Initial location and consequence | Owner and objective recheck | Current disposition |
| --- | --- | --- | --- |
| S01 | GAMEPLAY G02/G06 did not distinguish malformed constructed state from input errors with guaranteed exception classes; partial-gravity validity was implicit. Fixtures and callers could assume incompatible validation guarantees. | sdd-specify: define valid state invariants and separate caller preconditions from checked command/time/source errors. Recheck state table, G04/G05, G06, and K07 together. | Resolved in Revision 1 |
| S02 | SPEC R03 required finite monotonic timestamps without defining failure behavior. An adapter/controller defect had no specified rejection path. | sdd-specify: specify rejection before time/anchor changes, route failure through R05, and include it in A08. Recheck first-frame, pause/resume, and disposal obligations. | Resolved in Revision 1 |

## Revision 1

The author corrected S01 in G02/G06: playing/paused accumulators are finite and below the current interval, count/progression invariants are explicit, and malformed internally constructed state violates a caller precondition without a promised runtime exception. Guaranteed command/time/source errors retain their specified classes and prior-state protection.

The author corrected S02 in R03/A08: nonnumeric, nonfinite, or decreasing timestamps trigger the existing runtime-failure path before an elapsed input or anchor update. Monotonicity covers all delivered frames in the mounted session.

Rechecked the final blobs listed in Current gate against the unchanged governing sources. Manual review found no conflicting ownership, lifecycle, scoring, draw-consumption, or parent/child rule. Local Python checks passed heading spacing, relative links, unique/complete R01–R06/G01–G06/A01–A10/K01–K07 definitions, all seven shape/spawn bounds, four-turn geometry restoration, the negative-origin vertical-I rejection example, and documented score/gravity arithmetic.

The initial ID-check pattern counted an R06 traceability reference as a definition. Restricting contract definitions to headings removed that checker false positive; no document correction was needed for it.

These checks validate documents and examples. Canvas rendering, native browser interactions, production hosting, runtime failures, and build/release execution remain required implementation evidence. No exception or deferred confirmed issue is used to obtain Ready.
