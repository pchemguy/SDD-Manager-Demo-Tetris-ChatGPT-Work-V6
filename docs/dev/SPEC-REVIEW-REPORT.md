# SPEC review report

## Current gate

Technical QC: **Ready — iteration 3 combined extension**. Human SPEC/root-and-children acceptance: **accepted under scoped preapproval** for the instructed DOCS-002–DOCS-008 loop on 2026-10-22. [DOCS_TASKS.md](DOCS_TASKS.md) owns the actual grant, accepted states, publication and issue evidence. This is design/specification QC, not product verification or DOCS-009.

| Reviewed artifact | Git blob |
| --- | --- |
| [SPEC.md](SPEC.md) | 24bf3f21ec51fc4f247510d6f870fd5d933de1f6 |
| [spec/GAMEPLAY.md](spec/GAMEPLAY.md) | 0a0ab4a87640886e2a5b03a1c3712fea89e55d86 |
| [spec/CONFIGURATION.md](spec/CONFIGURATION.md) | 6a73acc35202020f7ab8e9b760f3eaf8745cda37 |

Accepted governing inputs at published DECOMPOSITION completion b1dc6ef7d3f58382cd3760ed0c22ea478d856569:

| Source | Git blob |
| --- | --- |
| [PROJECT.md](PROJECT.md) | 1a72042d6f7244922b21de09ff15967e841f94f9 |
| [ARCHITECTURE.md](ARCHITECTURE.md) | 19a47420bbe1a66c810ad8206819d078a3130a61 |
| [DECOMPOSITION.md](DECOMPOSITION.md) | f46bbca88917a4efeaaa61ea6933475b901c0915 |
| [AGENTS.md](../../AGENTS.md) | 28dd2966297a912deda4f0cbf1c4e54e7a316aca |
| [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) | ae71cfc01aaf694f5c4a7b055d2f15d469d7f7a7 |

Revision 3 rechecks complete gameplay and configuration scope against those accepted inputs. S01–S04 retain their historical resolved dispositions; S05–S07 are resolved below. No confirmed blocker, deferred finding or exception remains. PLAN/layout and TASKS must reassess the combined scope before dependent use.

## Iteration 2 accepted baseline

The following former current-gate evidence and Revision 2 are retained history for the six-extension baseline. They do not certify iteration 3.

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

## Revision 3 — Combined configuration and penetrator scope

Reviewed on 2026-10-22 by the author applying sdd-specify and the development-document QC criteria. Starting SPEC/GAMEPLAY identities were 765e6c865675dca7e8dab419e221315fdd448065 / 2e1139f2ff1d791af9676c4ce423a9c7bd0e1279, retained from iteration 2. CONFIGURATION is a cohesive child added with the same DOCS-005 checkpoint; no extra preparation issue is created. Exact corrected sources/governing states are in Current gate.

| Coverage group | Conformance and objective evidence route |
| --- | --- |
| Conditional visibility: G09/K17, R04/A17 | One core landing/projection; uppermost landing row classifies 0–9 versus 10–19, straddling hides, and both regular/penetrator destinations follow the same rule independently of hard-drop enablement. |
| Penetrator: G01–G04/G08–G10, K13–K15, R02/R04–R05/A18–A19 | Core owns bounded transient traversal, reachable lowest valid landing, final nonoverlap, activation/spend/award/cap, held identity and reset. Views consume variants/bonus count; white penetrators and nonwhite regular palette have browser evidence. |
| Configuration: C01/C03, G02–G04/G06/G08–G09, K16–K17/A20 | Six typed settings/defaults and native controls; core enforces gates; live changes preserve timers/held state and follow explicit ordering. Both general locks and the override have zero-distance, failed-attempt and due-age cases. |
| Persistence: C02/C04–C06/P01–P06, R03/R05/R07/A21–A22 | Pure per-key resolution and source provenance; browser adapter alone reads/writes cookies/URL. Explicit defaults are saved, omissions retain cookies, writes preserve unrelated URL/history data, and independent nonfatal failures retain active settings. |
| Existing game/distribution: G01–G09/K01–K12, R01–R06/A01–A16 | Retains geometry, kicks, bag, score, time, focus/lifecycle, static root/prefix launch, browser policy and per-phase version/release guarantees, with explicit setting/variant interactions. No saved game, persistent score or runtime service is added. |

| Finding | Located conflict in the retained baseline or review | Correction owner / recheck | Disposition |
| --- | --- | --- | --- |
| S05 | G01/G02 required every active cell empty and G09 stopped at the first obstruction; using that contract for a penetrator would reject traversal or give the wrong ghost/drop destination. | sdd-specify: distinguish valid final placement from traversable active placement; require a reachable valid landing for every transient proposal. Recheck grounding, hold spawn, final locking and the obstruction/bottom fixture. | Resolved in Revision 3 |
| S06 | R01 explicitly required no setting persistence; the single-policy snapshot/control contract omitted five new choices and per-setting omission/default-write semantics. | sdd-specify: replace the conflicting browser contract with C01–C06, validate six independent mappings/defaults, startup/save ordering, malformed/duplicate values and failure isolation. | Resolved in Revision 3 |
| S07 | G04's interval-only timer and hard-drop exception did not specify failed-attempt locking or live changes between the general policies. Timer age could otherwise reset or become due without a defined outcome. | sdd-specify: preserve/cap contact age under both policies, enable only the selected lock trigger, resolve due age on the next advance including zero, and define follow-drop's final probe. Recheck K16 and chronological ties/remainders. | Resolved in Revision 3 |

Manual review traced every user requirement through the accepted design and 7 R / 10 G / 6 C groups to 22 system, 17 core and 6 configuration acceptance cases. It checked transient overlaps, last-cavity rejection, all movement/kick/hold/activation paths, color qualification, one-symbol-per-bonus output, general/drop policy combinations, pause/restart, source validation, immutability, startup and independent write failures. Corrected a draft white-color overgeneralization and a detached acceptance-table row; final tables and palette are coherent.

Local document-data checks passed the retained seven-shape/spawn/four-turn, eight ordered kick-row, 24 blocked-rotation, two I-kick, 512 bag-permutation and score/gravity checks. Added checks passed six unique key/cookie/query schemas and allowed defaults, nonwhite palette values, the O traversal fixture (regular y=6, penetrator y=17, 34 drop points, invalid y=18), midline 9/10, policy 400/1000-ms example, bonus cap and cookie-lifetime arithmetic. Markdown/link/source checks accompany this checkpoint. Primary browser API documentation was consulted for cookie attributes, first-parameter extraction and same-origin history replacement; the project-specific behavior is explicit in CONFIGURATION.

These are document and mathematical checks, not a game implementation, browser capability probe, production test, CI run or release. No design amendment, unresolved confirmed issue or exception is needed for Ready. The next owner is sdd-plan after DOCS-005 publication/issue closure; whole-package QC remains outside this pass.
