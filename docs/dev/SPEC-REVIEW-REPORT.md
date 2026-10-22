# SPEC review report

## Current gate

Technical QC: **Ready — iteration 2 extended scope**. Human SPEC acceptance: **accepted under scoped preapproval** on 2026-10-22 for DOCS-002–DOCS-008. [DOCS_TASKS.md](DOCS_TASKS.md) owns the actual grant, completion publication, and issue progression. This is specification/design QC, not product verification or whole-package QC.

Reviewed current sources:

| Artifact | Git blob |
| --- | --- |
| [SPEC.md](SPEC.md) | 765e6c865675dca7e8dab419e221315fdd448065 |
| [spec/GAMEPLAY.md](spec/GAMEPLAY.md) | 2e1139f2ff1d791af9676c4ce423a9c7bd0e1279 |

Accepted governing inputs at published DECOMPOSITION completion `04f966dd4b7f8c304b56e4fd2d63d2126793e91a`:

| Source | Git blob |
| --- | --- |
| [docs/dev/PROJECT.md](PROJECT.md) | 979274fe1d8af394300b13eb53f96df1c2ec0c46 |
| [docs/dev/ARCHITECTURE.md](ARCHITECTURE.md) | 821f53a68bc9e0ec20e518d63e4b1d0003628967 |
| [docs/dev/DECOMPOSITION.md](DECOMPOSITION.md) | c24ea1ba1f3e6712c65805f49842784e0f10da93 |
| [AGENTS.md](../../AGENTS.md) | 4804c99fa5d2fd765d3745af5d89740df9513d99 |
| [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) | ae71cfc01aaf694f5c4a7b055d2f15d469d7f7a7 |

Revision 2 covers the modern target, input-clock synchronization, cross-component state/errors, and unchanged static/browser/release requirements. S01–S02 remain resolved in their historical scope; S03–S04 are resolved below. No confirmed SPEC/design blocker or human exception remains. PLAN/layout and TASKS require extended-scope reviews before dependent use; historical Ready labels are insufficient.

## First-cycle accepted baseline

This section and Revision 1 are historical. Their references to Current gate identify this retained baseline, not the extended-scope gate above.

Technical QC: **Ready** for the reviewed specification. Human acceptance: **accepted** with “K” on 2026-10-22 for SPEC and its gameplay child presented in commit `3774c63e6795526afb5f384b5b59379a6322c520`. [DOCS_TASKS.md](DOCS_TASKS.md) owns the acceptance publication and issue progression evidence.

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

Both confirmed findings below are resolved in Revision 1. No technical blocker or design amendment remains. The human accepted the detailed gameplay defaults with SPEC under PROJECT's explicit delegation. Technical readiness and human acceptance are distinct gates. No product code, provisioning, or product-test result is certified.

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

## Revision 2 — Extended-scope contracts and recheck

Under the user's extended scope and DOCS-002–DOCS-008 preapproval, the author applying sdd-specify reviewed the retained SPEC/child against accepted PROJECT, ARCHITECTURE, and DECOMPOSITION. Preserved geometry, scoring/progression foundations, focus/pause, immutable boundaries, static delivery, browser policy, and release obligations; specified bags, hold, clockwise kicks, shared ghost/drop landing, grounded deadlines, and the session-only hard-drop choice.

| Group | Current coverage and conformance |
| --- | --- |
| Hold and bag | G02/G07/G08, R01/R02/R04/R05, K02/K06–K09, A04/A11/A13: explicit bag state/refill, sample versus queue consumption, one hold per lock, replacement/collision, and reset. |
| Rotation and placement | G01/G03/G09, R02/R04/R05, K01/K03/K10/K11, A02/A12/A14/A16: ordered family-specific candidates, shared landing, visible ghost, distance/score and both drop policies. Up/Space stay clockwise; C/D are one-shot hold/drop. |
| Timing and policy | G02–G04/G08/G09, R01/R03, K04/K06/K10/K12, A03/A08/A15/A16: full interval from grounding, no grounded movement reset, airborne cancellation/regrounding, ties/remainder, pause, restart and non-retroactive setting changes. |
| State, errors and ownership | G06, R03/R05, K07/A08: explicit numeric samples/time, immutable state/snapshots, failure atomicity, caller preconditions, independent clock streams, cleanup and readable failures. Core owns gameplay; views consume geometry. |
| Delivery and usability | R04/R06, A01–A10/A16: desktop sizes/DPR, native accessible controls, production root/prefix, isolated browsers, no service/persistence, and per-phase version/release requirements. Paths/phases/tasks remain downstream. |

| Finding | Located issue and consequence | Correction and objective recheck | Disposition |
| --- | --- | --- | --- |
| S03 | Retained R03 advanced only on frames, so input could act before an expired grounded deadline. Directly comparing frame timestamps with interleaved input clock samples could also reject a valid sequence. | Added now sampling and elapsed-before-command; independently validate the two ordered streams. Checked start/resume, pause gaps, input-before-frame, expired deadline, malformed values, restart and disposal against controller ownership. | Resolved in Revision 2 |
| S04 | The first extended G07/A13 draft stated a seven-kind permutation without scoping restart. Discarding a partial bag then filling a fresh one violates that cross-round claim. | Scope groups to one round and restart grouping on restart; distinguish consumed bags, refills, queue kinds and placement order. Rechecked G02/G07, K06/K08, and A04/A13. | Resolved in Revision 2 |

Local document-data checks passed: 6 R, 9 G, 16 A and 12 K definitions; seven four-cell shapes and legal spawns; four-turn geometry; eight five-candidate kick rows; 24 all-blocked rotation constructions; both documented I-kick examples; 512 six-sample bag permutations; canonical-order/scoring/progression arithmetic. Manual review covered hold collisions and eligibility, no-draw and failure paths, ghost/drop agreement, distance zero, grounded/airborne transitions, simultaneous deadlines, pause/resume, setting retention/reload, and design ownership. Final source identities are in Current gate.

These checks validate document data and examples, not an implemented engine. Markdown/link/identity checks accompany this checkpoint. No installation, browser capability, runtime test, CI, build, or release result is certified. No design amendment, deferred confirmed issue, or exception is needed for Ready.
