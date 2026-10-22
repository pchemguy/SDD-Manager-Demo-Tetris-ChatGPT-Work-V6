# Delivery plan

This plan delivers the classic browser game in [PROJECT.md](PROJECT.md), using the accepted [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md), and [gameplay contracts](spec/GAMEPLAY.md). [DOCS_TASKS.md](DOCS_TASKS.md) records preparation decisions; the executable task list will own implementation progress.

## Strategy and phase boundary

One phase delivers the complete classic-only target as version **0.1.0**. The bounded outcome is one desktop keyboard game: the seven-piece board loop, line clearing, score/progression, next preview, and basic session controls, with the browser and static-distribution guarantees in SPEC. No backend, persistent settings, modern mechanics, or additional product mode is scheduled.

A single phase is appropriate for this compact game. Core play, score/progression, preview, and start/pause/restart form the selected starter experience; their state and display dependencies are direct. The accepted structure uses a pure core, one controller, browser adapters, and two views. Four milestones expose integration and usability evidence before the release boundary. Setup, failure handling, browser compatibility, and distribution support that same game outcome.

Milestone 1.1 is an early interactive subset, with explicit limits below. Phase 1 completion requires the entire accepted SPEC. No intermediate milestone is a release or a claim that the full game is usable. TASKS derivation must preserve bounded work within these milestones; excessive scope discovered there returns to PLAN for reassessment.

## Phase 1 — Classic game

Predecessor: the accepted documentation package, verified/published integration into the actual default branch, and the human's authorized implementation range. Before execution, establish the complete eligible phase's GitHub label, milestones, task issues, and verified associations through SDD Manager. Documentation preparation does not activate these objects.

| ID and milestone | Integrated outcome and included scope | Dependencies and objective exit |
| --- | --- | --- |
| 1.1 — Interactive browser slice | Establish the pinned TypeScript/Vite/Vitest/Playwright environment and policy-compliant browser capability. The actual application opens ready; Start draws the active/next pair, displays canonical board/preview geometry and HTML values, and accepts focused left/right/clockwise rotation. | Preparation and phase activation. Type checking, relevant geometry/start/movement unit checks, a production build, and native Start/movement plus Canvas inspection pass in both browsers. Establish browser provisioning/execution before browser-dependent work. |
| 1.2 — Continuous classic play | Extend the same application through gravity, soft drop, collision/failed-downward locking, promotion, simultaneous row clearing, scoring, levels, and game over. Production piece selection and draw consumption follow G02. | 1.1 reviewed and closed. Demonstrate repeated piece play through locking, clearing and game over; verify canonical G01–G06/K01–K07 cases applicable to these inputs, deterministic replay/immutability, and native rotation/drop integration. Preserve 1.1 behavior in both browsers. |
| 1.3 — Reliable session controls | Complete pause/resume, restart, all keyboard/focus/repeat rules, browser/core time coordination, resize independence, disposal, and readable startup/runtime failure. Controls and views use the same snapshots and lifecycle. | 1.2 reviewed and closed. A03–A05/A08 pass through controlled core/controller checks and the specified native browser/fault cases. Verify restart from playing/paused/over, preserved partial time, stale-callback safety, and failure cleanup; regress continuous play in both browsers. |
| 1.4 — Desktop distribution | Complete required viewport/DPR presentation and ship reproducible static assets at root and non-root prefixes. Finish user/developer instructions, CI validation, version exposure, and release-note preparation. | 1.3 reviewed and closed. A06/A09 and all pre-integration portions of A10 pass. Inspect Canvas/HTML output in both browsers; execute the production-entry flows, local/CI checks, and a clean-checkout setup/build/check rehearsal. |
| 1.5 — Phase review | One dedicated phase code review, testing, and report outcome covering the integrated game and complete SPEC. | Every delivery milestone 1.1–1.4 is complete and closed, including its review. Repair blockers, run the full relevant regression/production matrix, retain the phase and final implementation reports/TODO aggregation, then close this review milestone. Integration and release follow the gates below. |

The delivery count is four milestones; 1.5 is the excluded dedicated phase review milestone and contains exactly one phase review task. Each of 1.1–1.4 ends with its own explicit milestone code review/testing/report task. TASKS assigns task IDs and concrete edit/check scopes.

### Increment boundaries and feedback

Milestone 1.1 proves the real core-to-controller-to-view path with native input and drawing. It includes G01, G02 initialization/selection/start, G03 horizontal/rotation behavior, and their initial snapshot/browser seams. Elapsed gameplay, soft drop, locking/promotion, clearing/progression, game over, and full session/failure behavior remain scheduled in 1.2–1.3. No mock gameplay engine substitutes for the production components. The milestone is intentionally insufficient for the phase's playable release.

At 1.1, demonstrate start, visible shapes, focus, and movement; record whether input/Canvas behavior supports continuing with the selected composition and rendering. At 1.2, demonstrate line clearing/progression and game over; record whether gravity, feedback, and controls support the accepted classic experience. Those observations inform a human decision to continue, amend, simplify, or stop. Milestone reporting surfaces evidence and material concerns; routine progression within an already authorized range requires no additional approval. Amendments and changes of implementation range remain human decisions.

The sequence completes one shared application rather than isolated replacement prototypes. Regression checks accompany growth. Each milestone closes on its included contracts; its report identifies scheduled obligations that remain without claiming complete-SPEC acceptance.

## Contract delivery coverage

| Accepted contracts | Delivery and final evidence route |
| --- | --- |
| G01 geometry/board; G02 selection/start; G03 horizontal/rotation; K01–K03; A01/A02 start/rotation portions | 1.1 establishes canonical geometry, explicit piece supply, immutable snapshots, and native input/Canvas integration. 1.2 completes spawn/collision/drop boundaries; 1.4 repeats applicable production-entry checks. |
| G02–G05 continuous rules/progression; K02–K05; A02/A07 gameplay portions | 1.2 establishes deterministic rule cases plus real continuous play. 1.3 checks session interactions; 1.5 reviews the complete rule coverage. |
| G06 rejection/determinism; R05 data/source guarantees; K07 | Core checks grow with their transitions in 1.1–1.2. 1.3 adds browser failure containment. 1.5 confirms complete contract coverage and unchanged prior snapshots. |
| R01–R03; K06; A03–A05 | Initial start/focus in 1.1; complete pause/restart, repeat/focus, frame cap/anchors, and lifecycle in 1.3. Built application pause/resume is repeated under A09 in 1.4. |
| R04–R05; A06/A08 | Initial board/preview/HTML drawing in 1.1; snapshot/failure/resize/disposal guarantees in 1.3; both viewport sizes and representative DPR coverage in 1.4. |
| R06; A09–A10 | Compatible pinned tooling/provisioning/build in 1.1; production hosting, documentation, CI, and version preparation in 1.4; complete acceptance in 1.5 and verified default-branch/tag/GitHub publication at phase release. |

## Verification, reports, and completion

Each delivery milestone's final review assesses its changed capability, component boundaries, code quality, relevant unit/controller/browser checks, failures, and regressions. Repair confirmed blockers before completion and persist the review/report with its owning task. Passing tests alone do not replace code review.

Use the SDD Manager lifecycle report locations: milestone reports under `docs/dev/reports/phases/1/`, the phase's `PHASE-REPORT.md` there, and final `docs/dev/reports/IMPLEMENTATION-REPORT.md`. Reports contain a TODO section, including None when empty. Preserve justified non-blocking findings with IDs, evidence, impact, options, and ownership; required failures remain blockers. The phase/final report aggregates unresolved milestone/phase findings and resolution references.

Chromium is primary and Playwright-managed Firefox secondary. [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) owns isolation, provisioning, modes, diagnostics, and recovery. Establish and retain its capability evidence during 1.1 before relying on browser-dependent implementation. Missing required capability blocks affected work; it cannot be replaced by a mocked acceptance claim.

Unit/controller evidence uses supplied state, time, piece sequences, and callbacks. Browser acceptance uses native events, real composition and produced assets, plus suitable pixel/shape inspection for Canvas. Fault fixtures exercise production components through their declared seams and remain outside shipped assets. Each SPEC condition retains its stated browser/evidence scope; deterministic core checks run once in the compatible Node environment.

The phase review starts after the four delivery milestones and their issues/reviews are closed. It verifies all A01–A09 and the pre-integration A10 obligations, including the documented commands and CI outcomes. A10's published-source, tag, and release checks complete during the following transition:

1. Integrate the complete verified phase into the actual default branch using SDD Manager's authorized integration procedure. Verify the merged result, publish it, and confirm remote containment.
2. Finalize the root `RELEASE-NOTES.md` with delivered scope, version, validation, and material limits. Persist release preparation through the release workflow and verify the exact published release commit. Package metadata and built version information remain 0.1.0.
3. Publish tag `v0.1.0` at that verified commit and create the GitHub release using the finalized notes. Read back the tag target, release version/body, and any declared asset integrity.
4. Report implementation, integration, tracking, and release outcomes separately. Interrupted or uncertain effects remain pending and are reconciled before claiming phase release complete.

The user's release instruction authorizes this phase release after its gates. A partial implementation range stops at its requested boundary with published task results; phase completion does not expand that range.

## Risks and next preparation boundary

| Risk or dependency | Planned control and gate |
| --- | --- |
| Isolated browser/runner capability | Resolve during 1.1 under the central policy, before dependent browser work. Retain exact versions and observed capabilities; diagnose each required browser independently. |
| Timing or piece-draw errors crossing component boundaries | Explicit core inputs, controlled controller time, source-consumption checks, immutable snapshots, and continuous integration/regression evidence in 1.2–1.3. |
| Canvas usability or prefix-hosting failures | Early real drawing/input in 1.1; both-browser viewport/DPR and built root/prefix checks in 1.4. |
| Single-phase scope proves too broad during task derivation | Review actual task scope/dependencies and return material strategy changes to PLAN before implementation; retain all accepted SPEC scope. |
| Hosting, CI, integration, or release failure | Preserve verified local/published evidence and reconcile the specific pending transition. Required evidence remains a gate. |

No gameplay or architectural decision is deferred by this plan. Physical ownership remains for `layout.md`, which is prepared after human PLAN acceptance and receives combined PLAN/layout QC before its own acceptance. TASKS follows accepted layout; implementation follows whole-package QC, published preparation integration, and the authorized range. This proposal performs no implementation, installation, hosted phase activation, or release.
