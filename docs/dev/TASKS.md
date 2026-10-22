# Implementation tasks

This list derives the complete classic-game implementation from [PROJECT.md](PROJECT.md), [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md) with [gameplay contracts](spec/GAMEPLAY.md), [PLAN.md](PLAN.md), and [layout.md](layout.md). SPEC owns behavior; PLAN owns milestone outcomes/exits; layout owns paths. [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation acceptance.

All implementation boxes start unchecked. No code, setup, browser capability, CI, or release completion is claimed. New task-list acceptance precedes hosted phase projection or execution. Whole-package QC, verified/published preparation integration, the final human checkpoint, an authorized implementation range, and verified phase tracking must precede the first implementation task.

## Execution and review rules

Execute only the selected range in listed order while honoring prerequisites. Each task owns its implementation, relevant checks, documentation, completion evidence, and initial checkbox in one verified commit; publish and confirm it before advancing. Close/read back task issues after publication. A later milestone enters only after its predecessor's review and verified closure; a partial range stops at its requested boundary.

Each review task includes read-only code assessment through sdd-verify, appropriate tests, blocker repairs through the implementation owner, and an evidence-backed report. Passing tests alone do not complete code review. Its completion commit includes the report, checked task, and refreshed HANDOFF; subsequent hosted closure/integration effects remain pending until confirmed. Reports include TODOs (None when empty); contractual failures stay blockers, while eligible deferred findings retain IDs, impact, rationale, options, owner, and provenance.

Use [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) for all isolated browser setup/execution/diagnostics. Chromium is primary and Playwright-managed Firefox secondary. Establish T-001 capability evidence before dependent browser work. Test fixtures use production components; actual production-entry/native/Canvas checks remain distinct from synthetic setup or controlled callback evidence.

The shared unit/browser checks grow with their contracts; every task selects meaningful affected checks and required regressions. Descriptions below are acceptance obligations, not fixed test implementations or executed results. Future paths are allocated by layout and created only during authorized implementation.

## Phase 1 — Classic game

- [ ] Phase 1 — Classic game
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
        - [ ] T-003 — Implement ready/start and placement commands
            Scope: Initial src/core/board.ts and src/core/engine.ts, core tests, and useful deterministic fixtures.
            Depends on: T-002
            Contracts: G02 ready/start and draw consumption; G03 horizontal/rotation; R05 snapshots; relevant K02/K03/K07.
            Evidence: Verify ready consumes no draws, Start consumes the pair, legal/rejected horizontal/rotation outcomes, canonical snapshot cells, and unchanged prior state/snapshots. Vertical play and full session transitions remain scheduled below.
        - [ ] T-004 — Render board, preview, and initial HTML state
            Scope: src/view/canvas.ts, src/view/status.ts, index.html, src/styles.css, and test-only browser fixture entries.
            Depends on: T-003
            Contracts: R04/R05 initial presentation; PLAN 1.1 visual slice.
            Evidence: Use real core-projected snapshots to inspect board/preview shapes, HTML metrics/status, and stable piece colors in both browsers. Rendering consumes no piece draw or gameplay time. Final viewport/DPR acceptance remains T-019.
        - [ ] T-005 — Connect core state to an injected session controller
            Scope: src/session/controller.ts and tests/unit/session/controller.test.ts.
            Depends on: T-003
            Contracts: DECOMPOSITION controller/callback boundaries; R01 start and R03/R05 initial lifecycle.
            Evidence: Controlled bindings/renderers establish one current-state owner, correct Start/command/snapshot order, and one binding lifetime with basic disposal. Elapsed gameplay and complete session/failure cases are completed in milestones 1.2–1.3.
        - [ ] T-006 — Wire the real browser entry and initial controls
            Scope: src/browser/input.ts, src/browser/frame.ts, src/browser/pieces.ts, src/browser/bootstrap.ts, src/main.ts, adapter unit checks, and native browser integration checks.
            Depends on: T-004, T-005
            Contracts: A01; A02 rotation portion; G02 production selection; R01/R02 initial controls; R04/R05 integration.
            Evidence: The built entry opens ready; native Start/Enter and focused left/right/clockwise input update the real Canvas/HTML in both browsers. Test production piece mapping with controlled random values, including legal repeats. Reuse actual core/controller/views and retain the explicit PLAN 1.1 limits.
        - [ ] T-007 — Review, test, and report milestone 1.1
            Scope: Code review of the integrated slice and dependencies; relevant type/unit/build/native-input/Canvas checks; blocker repairs; report and HANDOFF.
            Depends on: T-001, T-002, T-003, T-004, T-005, T-006
            Contracts: PLAN 1.1 exits and first demonstration/feedback boundary.
            Evidence: Record actual setup and game-slice evidence separately, assess focus/shapes/input and composition, preserve scheduled obligations, apply the review contract, and verify all delivery issues before milestone closure.
            Report: `docs/dev/reports/phases/1/1.1.md`.
    - [ ] Milestone 1.2 — Continuous classic play
        - [ ] T-008 — Implement board placement and row compaction
            Scope: src/core/board.ts and tests/unit/core/board.test.ts.
            Depends on: T-007 Entry gate: Milestone 1.1 complete and closed.
            Contracts: G01 board legality; G04 placement/compaction and K05 board results.
            Evidence: Verify collision-safe placement, simultaneous 1–4-row removal, survivor order, retained height, and immutable prior boards with focused valid fixtures.
        - [ ] T-009 — Implement atomic soft-drop locking and progression
            Scope: src/core/engine.ts, engine tests, and deterministic board/piece fixtures.
            Depends on: T-008
            Contracts: G02 promotion/game over, G03 soft drop, G04 lock/clear, G05 score/level, and K02/K05.
            Evidence: Verify legal soft-drop points and blocked-downward locking as one complete transition: place, clear, pre-lock-level scoring, progression, spawn/preview draws, and blocked-spawn game over. Source failures leave the caller's prior state unchanged.
        - [ ] T-010 — Advance gravity through the browser clock boundary
            Scope: Core engine time advancement, initial controller frame-to-time routing, their unit checks, and native gravity/drop integration.
            Depends on: T-009
            Contracts: G04 gravity/carryover, G06 elapsed input, R03 playing clock, and K04.
            Evidence: Check exact interval boundaries, landing versus failed-downward lock, cross-spawn/new-level carryover, over-state remainder disposal, and the browser frame cap/start anchor. Native frames and ArrowDown drive the same core path in both browsers.
        - [ ] T-011 — Verify and complete continuous browser play
            Scope: Browser input/status/bootstrap integration as needed, core/controller tests, and continuous-play browser scenarios.
            Depends on: T-010
            Contracts: G01–G06/K01–K05/K07 applicable to continuous play; A02/A07 and prior A01 regressions.
            Evidence: Demonstrate repeated pieces, clearing, score/level changes, preview promotion, and game over through production components. Check specified command/time/source errors, deterministic replay, immutable snapshots, and native rotation/drop. Pause/restart obligations remain in 1.3.
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
            Evidence: Verify pause/over input inactivity, preserved partial rule time, valid state-specific Start/Pause/Resume/Restart behavior, and fresh restart state using the next two draws without resetting the supplied source. Check specified invalid command/time inputs before the inactive-state ignored-input rule.
        - [ ] T-014 — Complete controller timing and disposal guarantees
            Scope: src/session/controller.ts and controlled callback/time checks.
            Depends on: T-013
            Contracts: R03, R05 coordination, and controlled portions of A03/A04/A08.
            Evidence: Verify start/restart/resume anchors, pause without wall-clock catch-up, preserved core partial time, frame cap, malformed/decreasing timestamps before elapsed submission, ten restarts without duplicate bindings/chains, idempotent disposal, and inert late commands/callbacks.
        - [ ] T-015 — Complete native session controls and keyboard focus
            Scope: src/browser/input.ts, src/view/status.ts, src/browser/bootstrap.ts, and native browser checks.
            Depends on: T-014
            Contracts: R01/R02 and A03–A05.
            Evidence: In both browsers verify enabled states/labels, all keys and repeat rules, initial/click/Resume focus, button Space/Enter activation, playfield-only commands, native Tab/unmapped behavior, and explicit pause semantics. Exercise restart from playing/paused/over and regress continuous play.
        - [ ] T-016 — Contain startup and runtime failures
            Scope: Browser bootstrap, controller failure callback/cleanup, HTML failure view, and composition fault fixtures.
            Depends on: T-015
            Contracts: R05 failure boundaries and A08.
            Evidence: Inject missing HTML/Canvas, partial-assembly, source/engine/render failures through the real components. One readable alert remains visible even without normal status, controls are disabled, owned resources stop, late callbacks stay inert, and reload remains the recovery path in both browsers.
        - [ ] T-017 — Redraw on resize without changing gameplay
            Scope: Bootstrap resize wiring, Canvas sizing/redraw coordination, and unit/browser resize/lifecycle checks.
            Depends on: T-016
            Contracts: R03/R04 resize independence and A08.
            Evidence: Resize redraws the latest snapshot without changing board/score/status/rule time or draw consumption. Disposed and failed sessions cannot redraw through stale resize callbacks; both browsers retain the real board/preview path.
        - [ ] T-018 — Review, test, and report milestone 1.3
            Scope: Code review of session/lifecycle/failure changes; controlled timing plus native focus/fault/resize regressions; repairs; report and HANDOFF.
            Depends on: T-013, T-014, T-015, T-016, T-017
            Contracts: PLAN 1.3 exits and preserved 1.1–1.2 behavior.
            Evidence: Establish A03–A05/A08 through their distinct controlled/native evidence routes. Apply the review contract and verify all constituent issues before milestone closure.
            Report: `docs/dev/reports/phases/1/1.3.md`.
    - [ ] Milestone 1.4 — Desktop distribution
        - [ ] T-019 — Complete desktop layout and drawing-buffer scaling
            Scope: index.html, src/styles.css, view/canvas.ts, relevant status presentation, and browser visual/usability checks.
            Depends on: T-018 Entry gate: Milestone 1.3 complete and closed.
            Contracts: R04 and A06.
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
            Evidence: Rehearse documented setup/build/check commands from a clean checkout and record runtime/browser evidence limits. Document supported controls/classic exclusions and version 0.1.0. Prepare accurate notes with phase review/integration/release publication still pending; finalization follows phase completion. Do not tag or publish a release here.
        - [ ] T-023 — Review, test, and report milestone 1.4
            Scope: Code/document/build/CI review, production visual/hosting regressions, observed CI outcomes, repairs, report and HANDOFF.
            Depends on: T-019, T-020, T-021, T-022
            Contracts: PLAN 1.4 exits, A06/A09, and pre-integration A10.
            Evidence: Require actual successful CI evidence on published product changes, clean-checkout evidence, both-browser built flows, correct version/notes status, and preserved session/core behavior. Apply the review contract before delivery milestone closure.
            Report: `docs/dev/reports/phases/1/1.4.md`.
    - [ ] Milestone 1.5 — Phase review
        - [ ] T-024 — Review, test, and report phase 1 and final implementation
            Scope: Cross-milestone code review, full SPEC acceptance/regressions, blocker repairs, phase/final implementation reports, and HANDOFF.
            Depends on: T-007, T-012, T-018, T-023 Entry gate: All delivery milestones 1.1–1.4 and their issues/reviews complete and closed; do not depend on closure of this task's own milestone.
            Contracts: PLAN phase review; all A01–A09 and pre-integration A10; complete G/R/K coverage.
            Evidence: Review the integrated game and current CI evidence; run the required type/unit/build/browser matrix and verify documented setup. Aggregate unresolved/deferred milestone/phase TODOs with IDs, options, ownership, and resolution references. Record integration/tag/GitHub publication as pending until read back. Apply the review contract; then close/read back milestone 1.5 and perform the authorized phase-completion transition below.
            Report: `docs/dev/reports/phases/1/PHASE-REPORT.md` and `docs/dev/reports/IMPLEMENTATION-REPORT.md`.

## Delivery and acceptance map

| PLAN outcome and contracts | Executable coverage |
| --- | --- |
| 1.1 initial browser slice; G01, G02 start/selection, G03 horizontal/rotation; R01/R02/R04/R05 initial portions; K01–K03/K07; A01/A02 initial portions | T-001–T-006 delivery; T-007 review and early input/Canvas feedback |
| 1.2 continuous play; G02–G06; K02–K05/K07; A02/A07 | T-008–T-011 delivery; T-012 review and progression/game-over feedback |
| 1.3 session/lifecycle/failures; R01–R05, G02, K06; A03–A05/A08 | T-013–T-017 delivery; T-018 review and earlier capability regressions |
| 1.4 desktop/static delivery; R04/R06; A06/A09 and pre-integration A10 | T-019–T-022 delivery; T-023 review including actual published CI evidence |
| 1.5 full phase review and all remaining acceptance | T-024 only; phase/final reports and complete coverage assessment |
| A10 published integration, tag, and GitHub release | The phase-completion transition below, after T-024 and verified review-milestone closure |

There are 19 delivery tasks and five dedicated review tasks: 24 tasks total. Delivery counts per milestone are 6, 4, 5, and 4. The six-task first milestone separates tooling/capability, data/geometry, discrete rules, views, controller, and concrete browser integration; its scope is the accepted early slice. The adjacent [TASKS-REVIEW-REPORT.md](TASKS-REVIEW-REPORT.md) assesses those boundaries and prerequisites. No future feature task list exists or is created here.

## Phase-completion transition

After T-024 and verified closure of milestone 1.5, sdd-manage coordinates the complete phase transition under PLAN and the user's release instruction:

1. Integrate into the actual default branch, verify the merged result, publish, and confirm remote containment.
2. Finalize `RELEASE-NOTES.md` with completed scope and actual validation/limits. Persist and verify release preparation; package/built version stays 0.1.0.
3. Publish `v0.1.0` at the verified published release commit and create/read back the GitHub release with finalized notes; verify declared assets if any.
4. Complete A10 publication evidence and report implementation, tracking, integration, and release outcomes separately. Reconcile uncertain effects before claiming completion.

These are the existing workflow's integration/release operations, not an additional review task or permission to exceed a selected range. Partial ranges preserve their earlier checkpoint. Subsequent functionality/fix releases use the accepted minor/patch policy when such work is authorized.
