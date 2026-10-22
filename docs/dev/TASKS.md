# Implementation tasks

This list derives the complete browser-game implementation from [PROJECT.md](PROJECT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md) with [gameplay contracts](spec/GAMEPLAY.md), [PLAN.md](PLAN.md), and [layout.md](layout.md). SPEC owns behavior; PLAN owns milestone outcomes/exits; layout owns paths. [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation acceptance and the current stop after DOCS-008.

All implementation boxes start unchecked. No code, setup, browser capability, CI, or release completion is claimed. New task-list acceptance precedes hosted phase projection or execution. Whole-package QC, verified/published preparation integration, the final human checkpoint, an authorized implementation range, and verified phase tracking must precede the first implementation task.

## Execution and review rules

Execute only the selected range in listed order while honoring prerequisites. Each task owns its implementation, relevant checks, documentation, completion evidence, and initial checkbox in one verified commit; publish and confirm it before advancing. Close/read back task issues after publication. A later milestone enters only after its predecessor's review and verified closure; a partial range stops at its requested boundary.

Task IDs are stable identities. T-025 precedes its dependent T-003 in milestone 1.1; the listed hierarchy and prerequisites define execution order. Phase 1 delivers PLAN's foundation subset at 0.1.0; Phase 2 completes modern play at 0.2.0. Phase-specific deferrals do not establish complete-target acceptance.

Each review task includes read-only code assessment through sdd-verify, appropriate tests, blocker repairs through the implementation owner, and an evidence-backed report. Passing tests alone do not complete code review. Its completion commit includes the report, checked task, and refreshed HANDOFF; subsequent hosted closure/integration effects remain pending until confirmed. Reports include TODOs (None when empty); contractual failures stay blockers, while eligible deferred findings retain IDs, impact, rationale, options, owner, and provenance.

Use [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) for all isolated browser setup/execution/diagnostics. Chromium is primary and Playwright-managed Firefox secondary. Establish T-001 capability evidence before dependent browser work. Test fixtures use production components; actual production-entry/native/Canvas checks remain distinct from synthetic setup or controlled callback evidence.

The shared unit/browser checks grow with their contracts; every task selects meaningful affected checks and required regressions. Descriptions below are acceptance obligations, not fixed test implementations or executed results. Future paths are allocated by layout and created only during authorized implementation.

## Phase 1 — Classic foundation

- [ ] Phase 1 — Classic foundation
    - [ ] Milestone 1.1 — Interactive browser slice
        - [ ] T-001 — Establish pinned tooling and isolated browser capability
            Scope: Root package/lockfile, compiler/build/unit/browser configuration, generated-file ignores, a minimal production entry, and setup guidance in README/AGENTS.
            Depends on: phase entry gates; no predecessor task.
            Contracts: R06 and the PLAN 1.1 enabling gate.
            Evidence: Pin compatible tooling/runtime and version 0.1.0; establish explicit type/unit/build commands and run type/build checks. Verify unit-runner configuration with a bounded setup probe; product rule tests begin in T-002, so an empty match provides no product-test coverage. Follow BROWSER_DEV_POLICY for both isolated browsers, repeated session/input/Canvas/teardown probes, and the actual project runner/server path. Record versions, paths, commands, and capability limits before dependent browser work; probes establish setup, not game acceptance.
        - [ ] T-002 — Define canonical model and piece geometry
            Scope: src/core/model.ts, src/core/geometry.ts, and tests/unit/core/geometry.test.ts.
            Depends on: T-001
            Contracts: G01 and K01; core public value contracts.
            Evidence: Check all seven catalogues, four-turn restoration/O behavior, occupied coordinates and origin/bounds examples. Types remain browser-independent and geometry has one owner.
        - [ ] T-025 — Implement explicit seven-bag operations
            Scope: src/core/bag.ts, bag/random contracts in src/core/model.ts, tests/unit/core/bag.test.ts, and reusable numeric-sample fixtures where useful.
            Depends on: T-002
            Contracts: G07, G06 sample validation, K07/K08, and the PLAN 1.1 piece-supply boundary.
            Evidence: Verify permutations, front consumption/refill, six validated samples per shuffle, canonical-order example, boundary repeats and immutable bag results. Check nonnumeric/out-of-range samples and thrown sources without partial returned state. Engine-owned start/promotion/reset consumption is exercised by its transition tasks.
        - [ ] T-003 — Implement ready/start and placement commands
            Scope: Initial src/core/board.ts and src/core/engine.ts, core tests, and useful deterministic fixtures.
            Depends on: T-002, T-025
            Contracts: G02/G07 ready/start consumption; G03 horizontal/zero-offset rotation; R05 foundation snapshots; applicable K01/K02/K07.
            Evidence: Ready consumes no samples; Start consumes two bag kinds with canonical spawn. Verify horizontal and zero-offset rotation acceptance/rejection, grounding reconciliation, initial snapshot cells, and unchanged prior state. Translated candidates and K03's kicked example are scheduled in T-030; elapsed play and full sessions remain below.
        - [ ] T-004 — Render board, preview, and initial HTML state
            Scope: src/view/canvas.ts, src/view/status.ts, index.html, src/styles.css, and test-only browser fixture entries.
            Depends on: T-003
            Contracts: R04/R05 initial presentation; PLAN 1.1 visual slice.
            Evidence: Use core-projected foundation snapshots to inspect board/next shapes, HTML metrics/status and stable colors in both browsers. Rendering consumes no samples or time. Foundation viewport/DPR acceptance remains T-019; held/ghost/policy presentation belongs to Phase 2.
        - [ ] T-005 — Connect core state to an injected session controller
            Scope: src/session/controller.ts and tests/unit/session/controller.test.ts.
            Depends on: T-003
            Contracts: DECOMPOSITION controller/callback boundaries; R01 start and R03/R05 initial lifecycle.
            Evidence: Controlled input/now/frame/render callbacks establish one state owner, Start/command/snapshot order and binding lifetime/basic disposal. Define the clock seam without ambient time in tests. Elapsed and complete session/failure cases follow in 1.2–1.3.
        - [ ] T-006 — Wire the real browser entry and initial controls
            Scope: src/browser/input.ts, src/browser/frame.ts, src/browser/random.ts, src/browser/bootstrap.ts, src/main.ts, and native browser integration checks.
            Depends on: T-004, T-005
            Contracts: A01; A02 zero-offset rotation; G02/G07 production sampling; R01/R02 initial controls; R04/R05 foundation integration.
            Evidence: The built entry opens ready; native Start/Enter and focused horizontal/clockwise input update real Canvas/HTML in both browsers. Supply numeric randomness and same-origin now/frame services, with bag rules in core. Controlled fixtures reuse production components; production-entry checks exercise concrete adapters. Do not add duplicate tests for a thin sampling wrapper.
        - [ ] T-007 — Review, test, and report milestone 1.1
            Scope: Code review of the integrated slice and dependencies; relevant type/unit/build/native-input/Canvas checks; blocker repairs; report and HANDOFF.
            Depends on: T-001, T-002, T-025, T-003, T-004, T-005, T-006
            Contracts: PLAN 1.1 exits and first demonstration/feedback boundary.
            Evidence: Record actual setup and game-slice evidence separately, assess focus/shapes/input and composition, preserve scheduled obligations, apply the review contract, and verify all delivery issues before milestone closure.
            Report: `docs/dev/reports/phases/1/1.1.md`.
    - [ ] Milestone 1.2 — Continuous classic play
        - [ ] T-008 — Implement board placement and row compaction
            Scope: src/core/board.ts and tests/unit/core/board.test.ts.
            Depends on: T-007 Entry gate: Milestone 1.1 complete and closed.
            Contracts: G01 board legality; G04 placement/compaction and K05 board results.
            Evidence: Verify collision-safe placement, simultaneous 1–4-row removal, survivor order, retained height, and immutable prior boards with focused valid fixtures.
        - [ ] T-009 — Implement grounded locking and atomic progression
            Scope: src/core/engine.ts, engine tests, and deterministic board/piece fixtures.
            Depends on: T-008
            Contracts: G02/G07 promotion, G03 soft drop, G04 grounded expiry/lock/clear, G05 progression, and K02/K04/K05.
            Evidence: Legal soft drop scores one point; blocked soft drop preserves the grounded delay. Advance an already-grounded fixture through partial and exact full-interval expiry to verify atomic placement, simultaneous clear, pre-lock-level points, progression, bag/next promotion and blocked spawn. Source failure publishes no partial state. General airborne gravity/event carryover follows in T-010.
        - [ ] T-010 — Advance gravity through the browser clock boundary
            Scope: Core engine chronological time advancement, controller now/frame-to-time routing, their unit checks, and native gravity/soft-drop integration.
            Depends on: T-009
            Contracts: G04 gravity/grounded chronology, G06 elapsed input, R03 playing clock, K04/K12 foundation portions.
            Evidence: Check first grounding, full-delay boundaries, grounded-move preservation, airborne cancellation/regrounding, lock-before-gravity ties, cross-spawn/new-level remainder and over disposal. Synchronize sampled time before commands with the per-callback cap and independent frame/now validation. Native frames/ArrowDown use the same core path in both browsers.
        - [ ] T-011 — Verify and complete continuous browser play
            Scope: Browser input/status/bootstrap integration as needed, core/controller tests, and continuous-play browser scenarios.
            Depends on: T-010
            Contracts: G01–G07 foundation subset; K01/K02/K04/K05/K07/K08/K12 applicable portions; A02/A07 and A01 regressions.
            Evidence: Demonstrate bag-driven repeated play, delayed locking, clearing, progression, preview and game over through production components. Check current command/time/sample errors, replay, immutability and native zero-offset rotation/soft drop. Translated kicks/modern actions remain Phase 2; pause/restart follow in 1.3.
        - [ ] T-012 — Review, test, and report milestone 1.2
            Scope: Code review of continuous play and dependencies; deterministic rules and both-browser regressions; repairs; report and HANDOFF.
            Depends on: T-008, T-009, T-010, T-011
            Contracts: PLAN 1.2 exits and gameplay feedback boundary.
            Evidence: Record line-clear/progression/game-over demonstrations and assess gravity/control feedback. Apply the review contract and confirm delivery issue closures before milestone closure.
            Report: `docs/dev/reports/phases/1/1.2.md`.
    - [ ] Milestone 1.3 — Reliable session controls
        - [ ] T-013 — Implement pause, resume, and restart core transitions
            Scope: src/core/engine.ts and focused core tests.
            Depends on: T-012 Entry gate: Milestone 1.2 complete and closed.
            Contracts: G02 session states, G06 validation before ignored inputs, and K06.
            Evidence: Verify inactive commands, both preserved partial timers, valid state-specific sessions and fresh restart board/counts/bag state using a new bag without rewinding numeric samples. Check validation before ignored input. The session's selected policy is retained; its user-facing choice is delivered in Phase 2.
        - [ ] T-014 — Complete controller timing and disposal guarantees
            Scope: src/session/controller.ts and controlled callback/time checks.
            Depends on: T-013
            Contracts: R03, R05 coordination, and controlled portions of A03/A04/A08.
            Evidence: Verify sampled start/restart/resume anchors, pause without catch-up, both partial timers, per-callback cap, elapsed-before-command/deadline ordering and independently ordered frame/now streams. Reject malformed/decreasing values before elapsed submission; accept legitimate interleaving. Ten restarts keep one binding/chain; disposal is idempotent and late callbacks inert.
        - [ ] T-015 — Complete native session controls and keyboard focus
            Scope: src/browser/input.ts, src/view/status.ts, src/browser/bootstrap.ts, and native browser checks.
            Depends on: T-014
            Contracts: R01/R02 and A03–A05.
            Evidence: In both browsers verify foundation controls/keys/repeats, initial/click/Resume focus, native button Space/Enter, playfield-only bindings, Tab/unmapped behavior and explicit pause. Exercise all restart states and regress play. C/Hold, ghost and D/drop/checkbox are delivered in Phase 2.
        - [ ] T-016 — Contain startup and runtime failures
            Scope: Browser bootstrap, controller failure callback/cleanup, HTML failure view, and composition fault fixtures.
            Depends on: T-015
            Contracts: R05 failure boundaries and A08.
            Evidence: Inject missing HTML/Canvas, partial-assembly, source/engine/render failures through the real components. One readable alert remains visible even without normal status, controls are disabled, owned resources stop, late callbacks stay inert, and reload remains the recovery path in both browsers.
        - [ ] T-017 — Redraw on resize without changing gameplay
            Scope: Bootstrap resize wiring, Canvas sizing/redraw coordination, and unit/browser resize/lifecycle checks.
            Depends on: T-016
            Contracts: R03/R04 resize independence and A08.
            Evidence: Resize redraws the latest snapshot without changing board/metrics/status, bag, policy or either timer. Disposed/failed sessions cannot redraw through stale callbacks; both browsers retain the real board/next path.
        - [ ] T-018 — Review, test, and report milestone 1.3
            Scope: Code review of session/lifecycle/failure changes; controlled timing plus native focus/fault/resize regressions; repairs; report and HANDOFF.
            Depends on: T-013, T-014, T-015, T-016, T-017
            Contracts: PLAN 1.3 exits and preserved 1.1–1.2 behavior.
            Evidence: Establish A03–A05/A08 through their distinct controlled/native evidence routes. Apply the review contract and verify all constituent issues before milestone closure.
            Report: `docs/dev/reports/phases/1/1.3.md`.
    - [ ] Milestone 1.4 — Desktop distribution
        - [ ] T-019 — Complete desktop layout and drawing-buffer scaling
            Scope: index.html, src/styles.css, src/view/canvas.ts, relevant status presentation, and browser visual/usability checks.
            Depends on: T-018 Entry gate: Milestone 1.3 complete and closed.
            Contracts: R04 and A06 foundation portions; complete modern presentation follows in T-040.
            Evidence: Inspect/verify the full board, preview, metrics, instructions, controls, focus visibility, and logical grid at both required viewports/DPR 1 and representative DPR 2 in both browsers, without clipping or page scrolling. Preserve resize/session behavior.
        - [ ] T-020 — Verify static root/prefix hosting and built version
            Scope: Build/package configuration, version presentation, owned static-server helpers if needed, and production-entry browser checks.
            Depends on: T-019
            Contracts: R06, A09, and build/version portions of A10.
            Evidence: Serve produced assets at root and a non-root prefix; run A01–A03 against them in both browsers, check no external runtime requests, fixture/runner exclusion, and package/built version agreement at 0.1.0. Test-only fixture serving remains separate.
        - [ ] T-021 — Configure the CI validation pipeline
            Scope: .github/workflows/ci.yml and existing runner/package helpers only where required for the same repeatable pipeline.
            Depends on: T-020
            Contracts: PLAN 1.4 CI validation and R06/A10.
            Evidence: Validate job-equivalent type/unit/build/browser commands locally with pinned dependencies and isolated browsers before persistence. Publish the workflow under ordinary task rules; observed CI run evidence is required at the milestone review after publication, rather than claimed for an unpublished commit.
        - [ ] T-022 — Validate setup instructions and prepare release notes
            Scope: README.md, AGENTS.md, root RELEASE-NOTES.md preparation, and clean-checkout rehearsal evidence.
            Depends on: T-020, T-021
            Contracts: R06, PLAN 1.4 documentation/reproducibility/release preparation, and pre-integration A10.
            Evidence: Rehearse setup/build/check commands from a clean checkout and record evidence limits. Document the delivered foundation, bag/default delay, and explicitly scheduled modern scope at version 0.1.0. Prepare accurate notes with phase review/integration/release pending; tags and GitHub release publication follow phase completion.
        - [ ] T-023 — Review, test, and report milestone 1.4
            Scope: Code/document/build/CI review, production visual/hosting regressions, observed CI outcomes, repairs, report and HANDOFF.
            Depends on: T-019, T-020, T-021, T-022
            Contracts: PLAN 1.4 exits, A06/A09, and pre-integration A10.
            Evidence: Require actual successful CI evidence on published product changes, clean-checkout evidence, both-browser built flows, correct version/notes status, and preserved session/core behavior. Apply the review contract before delivery milestone closure.
            Report: `docs/dev/reports/phases/1/1.4.md`.
    - [ ] Milestone 1.5 — Phase review
        - [ ] T-024 — Review, test, and report phase 1
            Scope: Cross-milestone code review, complete foundation acceptance/regressions, blocker repairs, phase report and HANDOFF.
            Depends on: T-007, T-012, T-018, T-023 Entry gate: All delivery milestones 1.1–1.4 and their issues/reviews complete and closed; do not depend on closure of this task's own milestone.
            Contracts: PLAN Phase 1 scoped coverage and pre-integration A10; modern requirements remain explicitly scheduled in Phase 2.
            Evidence: Review the usable foundation and published CI; run its required type/unit/build/browser matrix and setup checks. Aggregate phase TODOs and record each deferred modern contract without claiming full-target acceptance. Integration/tag/GitHub effects stay pending until read back. Apply the review contract, then close/read back 1.5 and perform the authorized transition. Global final reporting belongs to T-043.
            Report: `docs/dev/reports/phases/1/PHASE-REPORT.md`.

## Phase 2 — Modern play

- [ ] Phase 2 — Modern play
    - [ ] Milestone 2.1 — Hold and held preview
        - [ ] T-026 — Implement hold eligibility and replacement
            Scope: src/core/model.ts, src/core/engine.ts, focused core checks and valid fixtures.
            Depends on: T-024 Entry gate: Phase 1 complete, integrated/published and released; authorized Phase 2 range and verified phase tracking.
            Contracts: G02/G06–G08, K09 and core A11/A13.
            Evidence: Verify empty promotion versus occupied swap, one hold per lock, canonical respawn/timers, exact queue/sample consumption, blocked replacement game over, source-failure atomicity, inactive/used rejection and restart. Preserve board/score for non-locking hold and earlier state values.
        - [ ] T-027 — Project and render held-piece state
            Scope: Engine snapshot/model, src/view/canvas.ts, src/view/status.ts, index.html, src/styles.css, bootstrap resource wiring and focused presentation fixtures.
            Depends on: T-026
            Contracts: R04/R05 held kind/cells/availability and G08 presentation; no input binding yet.
            Evidence: Render Empty or normalized held geometry and text from the same snapshot as board/next, including pause/over. Wire held Canvas acquisition/cleanup coherently without changing rule state or sample counts. Hold button/key activation follows in T-028.
        - [ ] T-028 — Integrate native hold controls
            Scope: src/browser/input.ts, status/button/page/bootstrap consumers and native browser hold flows.
            Depends on: T-027
            Contracts: R01/R02 C/Hold availability/focus/repeat; A11 and hold-related A04/A05/A13.
            Evidence: In both browsers exercise C/button empty hold and swap, disabled used/paused states, ignored repeat, lock re-enablement, restart and collision game over. Confirm visible next/held agreement and foundation regressions through production components.
        - [ ] T-029 — Review, test, and report milestone 2.1
            Scope: Hold code/ownership review, core and native regressions, repairs, report and HANDOFF.
            Depends on: T-026, T-027, T-028
            Contracts: PLAN 2.1 and hold demonstration/feedback.
            Evidence: Confirm eligibility/consumption and held-view integration in both browsers. Apply the review contract and verify delivery issue closures before milestone closure.
            Report: `docs/dev/reports/phases/2/2.1.md`.
    - [ ] Milestone 2.2 — Kicked rotation and ghost
        - [ ] T-030 — Apply ordered clockwise kick candidates
            Scope: src/core/geometry.ts, engine rotation/grounding collaboration and core checks.
            Depends on: T-029 Entry gate: Milestone 2.1 complete and closed.
            Contracts: G01/G03/G04, K03/K11/K12 and core A14.
            Evidence: Verify both families/all transitions, first-legal order, documented wall/floor cases, O no-op, visible bounds, all-blocked preservation and grounded/airborne timer reconciliation. Keep existing clockwise commands and unrelated foundation behavior.
        - [ ] T-031 — Share landing projection and ghost snapshots
            Scope: src/core/board.ts, model/engine snapshot collaboration and focused core checks.
            Depends on: T-030
            Contracts: G09 landing/ghost, R05 ghost cells, K10 projection portion and A12.
            Evidence: Derive the greatest legal vertical landing on empty/obstructed boards and after hold/kicks; expose sorted cells only in playing/paused. Prove no bag/time/hold/policy mutation. Hard drop will reuse this same calculation in T-035.
        - [ ] T-032 — Draw ghost geometry through the existing view
            Scope: src/view/canvas.ts, src/styles.css and browser visual/resize fixtures.
            Depends on: T-031
            Contracts: R04/R05 ghost distinction and read-only drawing; visual A12/A06 portions.
            Evidence: In both browsers inspect visible outlined/translucent landing cells behind the active piece, coincident landing, paused/over output and DPR/resize. Renderer consumes snapshot geometry without collision or landing logic.
        - [ ] T-033 — Verify kicked placement and ghost integration
            Scope: Native browser scenarios, production-component fixtures, and bounded integration repairs where checks expose gaps.
            Depends on: T-032
            Contracts: A14 and pre-hard-drop A12; PLAN 2.2 demonstration.
            Evidence: Exercise native wall/floor/rejected rotation and ghost updates after movement, hold and gravity in both browsers. Preserve focus/session/source behavior; executed ghost/drop agreement remains for 2.3.
        - [ ] T-034 — Review, test, and report milestone 2.2
            Scope: Placement/ghost code review, core/native/Canvas regressions, repairs, report and HANDOFF.
            Depends on: T-030, T-031, T-032, T-033
            Contracts: PLAN 2.2 exits and placement-guidance feedback.
            Evidence: Confirm ordered kick correctness, one landing owner, view-only projection and hold/foundation regressions. Apply the review contract and verify delivery issue closures.
            Report: `docs/dev/reports/phases/2/2.2.md`.
    - [ ] Milestone 2.3 — Hard drop and lock policy
        - [ ] T-035 — Implement hard drop and both core policies
            Scope: src/core/model.ts, src/core/engine.ts, existing board landing helper and focused core checks.
            Depends on: T-034 Entry gate: Milestone 2.2 complete and closed.
            Contracts: G04–G06/G09, K10/K12 and core A12/A15/A16.
            Evidence: Reuse landing projection; verify 2*d scoring, preserved gravity, new versus already-running grounded delay, both policies and distance zero. Check immediate atomic lock/progression/spawn, ordinary soft/gravity delay, future-only policy updates, inactive validity/errors, restart retention and immutability.
        - [ ] T-036 — Route policy updates through sampled controller time
            Scope: src/session/controller.ts, its callback/session contracts and controlled unit checks.
            Depends on: T-035
            Contracts: R01/R03/R05, G06 policy input and controlled A08/A16.
            Evidence: Advance time before policy/command callbacks, including deadline expiry and independent timestamp streams. Expose the policy sink without another clock/timer owner; check paused/ready/over updates, restart retention, callback errors and disposal. A setting update cannot itself drop or reset a piece.
        - [ ] T-037 — Integrate hard-drop controls and native policy choice
            Scope: src/browser/input.ts, src/view/status.ts, index.html, bootstrap wiring and native browser flows.
            Depends on: T-036
            Contracts: R01/R02/R04/R05 and complete A12/A15/A16.
            Evidence: In both browsers exercise D/button, unchecked delay versus checked immediate behavior, distance zero, ignored C/D repeats, native checkbox Space/focus, policy change without retroactive lock, restart retention and reload default. Compare executed delayed-drop cells with the visible ghost and regress hold/kicks/sessions.
        - [ ] T-038 — Review, test, and report milestone 2.3
            Scope: Core/controller/input/policy review, exact timing and native cross-feature regressions, repairs, report and HANDOFF.
            Depends on: T-035, T-036, T-037
            Contracts: PLAN 2.3 and both-policy demonstration/feedback.
            Evidence: Confirm all drop/policy timing interactions and both-browser behavior without source/clock ownership drift. Apply the review contract and verify constituent issue closures.
            Report: `docs/dev/reports/phases/2/2.3.md`.
    - [ ] Milestone 2.4 — Modern release acceptance
        - [ ] T-039 — Verify complete cross-feature rule and controller flows
            Scope: Core/controller integration checks, reusable fixtures and contract-required focused repairs.
            Depends on: T-038 Entry gate: Milestone 2.3 complete and closed.
            Contracts: G01–G09/K01–K12 and controlled R01–R05/A07/A08/A11–A16.
            Evidence: Run existing evidence and add only uncovered interactions: hold→kick→ghost→drop→lock, bag refill and blocked replacement, timer lift/reground/pause, source failures and snapshot preservation. Confirm complete coverage without duplicating per-feature tests or mirroring implementation.
        - [ ] T-040 — Complete modern desktop and production-browser acceptance
            Scope: Existing view/page/bootstrap owners, browser suites/fault fixtures and build/runner helpers only where required.
            Depends on: T-039
            Contracts: R01–R06, A01–A09/A11–A16 and PLAN 2.4 browser scope.
            Evidence: Both browsers verify full held/ghost/control/checkbox layout at required viewports/DPR, native focus/repeat, clock/failure/resize behavior including added resources, root/prefix produced assets, no external requests and fixture exclusion. Run current local pipeline; observed published CI/version-final evidence is required at T-042.
        - [ ] T-041 — Prepare modern version and reproducible release guidance
            Scope: Package/lock/version metadata, README.md, AGENTS.md, RELEASE-NOTES.md preparation and clean-checkout evidence.
            Depends on: T-040
            Contracts: R06, PLAN 2.4 guidance/version and pre-integration A10.
            Evidence: Set package/built version 0.2.0, validate documented full controls/defaults/limits and setup/check commands from a clean checkout, and prepare notes from actual delivered changes/evidence. Phase review, integration, tag and GitHub release remain pending; release publication follows phase completion.
        - [ ] T-042 — Review, test, and report milestone 2.4
            Scope: Complete modern code/docs/distribution/CI review, production regressions, repairs, report and HANDOFF.
            Depends on: T-039, T-040, T-041
            Contracts: PLAN 2.4; all pre-integration A01–A16 and canonical K01–K12 evidence.
            Evidence: Require successful CI on published modern product changes, built version 0.2.0, current full browser/clean-checkout evidence, accurate notes and preserved foundation behavior. Apply the review contract before milestone closure.
            Report: `docs/dev/reports/phases/2/2.4.md`.
    - [ ] Milestone 2.5 — Phase review
        - [ ] T-043 — Review, test, and report phase 2 and final implementation
            Scope: Cross-milestone/full-target code review, acceptance/regressions, repairs, phase/final implementation reports and HANDOFF.
            Depends on: T-029, T-034, T-038, T-042 Entry gate: All 2.1–2.4 work/reviews and issues complete/closed; do not depend on this task's own milestone closure.
            Contracts: PLAN final phase review; R01–R06/G01–G09/K01–K12; all pre-integration A01–A16.
            Evidence: Confirm complete intended behavior, current CI and type/unit/build/both-browser matrix, documented commands and prior-phase regressions. Aggregate unresolved/deferred TODOs across both phases with provenance/resolution references. Record remaining integration/tag/release effects honestly, apply the review contract, then close/read back 2.5 before the authorized transition.
            Report: `docs/dev/reports/phases/2/PHASE-REPORT.md` and `docs/dev/reports/IMPLEMENTATION-REPORT.md`.

## Delivery and acceptance map

| PLAN outcome and contracts | Executable coverage |
| --- | --- |
| 1.1 initial slice; G01/G02/G07 and zero-offset G03; initial R01/R02/R04/R05; K01/K02/K07/K08; A01/A02 portions | T-001–T-006 and T-025 delivery; T-007 review and early input/Canvas feedback |
| 1.2 continuous play; G02–G07 foundation subset; K02/K04/K05/K07/K08/K12 portions; A02/A07 | T-008–T-011 delivery; T-012 review and progression/game-over feedback |
| 1.3 session/lifecycle/failures; R01–R05, G02, K06; A03–A05/A08 | T-013–T-017 delivery; T-018 review and earlier capability regressions |
| 1.4 foundation desktop/static; R04/R06; baseline A06/A09 and pre-integration A10 | T-019–T-022 delivery; T-023 review with actual published CI |
| 1.5 scoped foundation review and deferred-contract accounting | T-024 only; phase report, 0.1.0 transition and honest Phase 2 deferrals |
| 2.1 hold; G02/G06–G08, R01/R02/R04/R05, K09, A11/A13 | T-026–T-028 delivery; T-029 review |
| 2.2 kicks/ghost; G01/G03/G04/G09, K03/K10/K11/K12, A12/A14 | T-030–T-033 delivery; T-034 review; executed drop agreement completes in 2.3 |
| 2.3 drop/policy; G04–G06/G09, R01–R05, K10/K12, A12/A15/A16 | T-035–T-037 delivery; T-038 review |
| 2.4 complete target and distribution; R01–R06/G01–G09/K01–K12/A01–A16 pre-integration scope | T-039–T-041 delivery; T-042 review with published CI/full browser/version evidence |
| 2.5 final phase/implementation assessment | T-043 only; phase/final reports and global TODO aggregation |
| A10 integration, tag and GitHub release for each phase | The transition below after T-024/1.5 or T-043/2.5 respectively |

There are 33 delivery tasks and ten dedicated review tasks: 43 total. Phase 1 delivery counts are 7/4/5/4; Phase 2 counts are 3/4/3/3. The seven-unit 1.1 separates tooling, geometry, bag algorithm, discrete engine, views, injected controller and concrete browser assembly, with independent verification boundaries. [TASKS-REVIEW-REPORT.md](TASKS-REVIEW-REPORT.md) assesses semantic scope/counts and the stable-ID insertion. No feature task list is created.

## Phase-completion transition

After T-024/verified 1.5 closure for Phase 1, or T-043/verified 2.5 closure for Phase 2, sdd-manage coordinates that phase's transition under PLAN and the user's release instruction:

1. Integrate into the actual default branch, verify the merged result, publish, and confirm remote containment.
2. Finalize `RELEASE-NOTES.md` for actual delivered scope and validation/limits. Persist/verify release preparation; package/built version is 0.1.0 for Phase 1 or 0.2.0 for Phase 2.
3. Publish the corresponding `v0.1.0` or `v0.2.0` tag at the verified release commit and create/read back the GitHub release with finalized notes; verify declared assets if any.
4. Complete A10 publication evidence and report implementation, tracking, integration, and release outcomes separately. Reconcile uncertain effects before claiming completion.

These are the existing workflow's integration/release operations, not an additional review task or permission to exceed a selected range. Partial ranges preserve their earlier checkpoint. Subsequent functionality/fix releases use the accepted minor/patch policy when such work is authorized.
