# Delivery plan

This plan delivers the complete browser game in [PROJECT.md](PROJECT.md), using the accepted [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md), and [gameplay contracts](spec/GAMEPLAY.md). [DOCS_TASKS.md](DOCS_TASKS.md) records preparation decisions; TASKS owns executable implementation progress.

## Strategy and phase boundary

Two bounded phases deliver a usable game first and then the complete modern target:

| Phase | Release | Included capability and explicit boundary |
| --- | --- | --- |
| 1 — Classic foundation | 0.1.0 | Continuous desktop play, score/levels, next preview, pause/restart, static delivery, and the required bag and full-grounded-interval rules. Rotation uses G03's zero-offset candidate only. Hold, translated kicks, ghost, hard drop, and the policy checkbox are scheduled in Phase 2. |
| 2 — Modern play | 0.2.0 | Hold and held preview, ordered kicks and ghost, hard drop and immediate-lock choice, integrated with and regressing the foundation. Completion covers the entire SPEC. |

Every playable foundation needs piece selection and a locking rule. G07's small explicit bag boundary and G04's requested default interval supply those core responsibilities from the outset; they add no extra interaction mode or presentation subsystem. Hold, translated placement, ghost, and hard-drop interaction have independent user-facing paths and fit the later phase. No backend, persistence, extra settings panel, or additional product mode is scheduled.

Each phase grows the same application through four integrated delivery milestones and a separate final review. Phase 2 remains bounded to four related mechanics on the established core/controller/view path, with working hold, placement guidance, and drop-policy demonstrations before its release review. TASKS must preserve localized verifiable units; excessive scope returns to PLAN. Intermediate milestones are demonstrations, not releases or complete-target claims.

## Phase 1 — Classic foundation

Predecessor: the accepted documentation package, verified/published integration into the actual default branch, and the human's authorized implementation range. Before execution, establish the complete eligible phase's GitHub label, milestones, task issues, and verified associations through SDD Manager. Documentation preparation does not activate these objects.

| ID and milestone | Integrated outcome and included scope | Dependencies and objective exit |
| --- | --- | --- |
| 1.1 — Interactive browser slice | Establish the pinned toolchain and policy-compliant browsers. The actual application opens ready; Start uses the numeric-source seven-bag, displays active/next geometry and HTML values, and accepts focused horizontal/clockwise movement. Rotation attempts the zero-offset candidate. | Preparation and phase activation. Geometry/bag/start/movement checks, type check/build, native Start/movement and Canvas inspection pass in both browsers. Establish provisioning/execution before dependent work. |
| 1.2 — Continuous classic play | Add gravity, soft drop, grounded lock timing, promotion, simultaneous row clearing, scoring, levels, and game over using G02/G04/G07. | 1.1 reviewed/closed. Demonstrate repeated play, delayed locking, clearing and game over. Verify applicable canonical cases, sample counts, timer ties/remainder, replay/immutability, and native integration; regress 1.1 in both browsers. |
| 1.3 — Reliable session controls | Complete baseline pause/restart/keyboard/focus/repeat, sampled-clock synchronization, resize, disposal, and readable failure. Modern commands/setting remain scheduled in Phase 2. | 1.2 reviewed/closed. A03–A05/A08 baseline portions pass with controlled core/controller time and native/fault cases. Check both partial timers, independent clock streams, callback cap/order, restart and stale-callback cleanup. |
| 1.4 — Desktop distribution | Finish baseline viewport/DPR presentation, static root/prefix launch, repeatable guidance, CI, version exposure, and release-note preparation. | 1.3 reviewed/closed. Baseline A06, A09 and pre-integration A10 pass; inspect real Canvas/HTML, observed CI, built entry flows, and a clean-checkout rehearsal. Held/ghost/policy presentation is not claimed complete. |
| 1.5 — Phase review | One phase code review/testing/report covering the complete foundation and its explicit deferred contracts. | All 1.1–1.4 work/reviews and issues are complete/closed. Repair blockers, run the foundation regression/production matrix, retain its phase report/TODO aggregation, and close this review milestone. Integration/release then follow the shared gates. |

The delivery count is four milestones; 1.5 is the excluded dedicated phase review milestone and contains exactly one phase review task. Each of 1.1–1.4 ends with its own explicit milestone code review/testing/report task. TASKS assigns task IDs and concrete edit/check scopes.

### Increment boundaries and feedback

Milestone 1.1 proves the real core/controller/view path with G01 geometry, G02 start, G07 bags, and G03 horizontal/zero-offset rotation. Elapsed play, soft drop, locking/promotion, clearing/progression, game over, and complete session/failure behavior remain in 1.2–1.3. No mock engine substitutes for production components. The milestone is insufficient for the playable foundation release.

At 1.1, demonstrate start, visible shapes, focus, and movement; record whether input/Canvas behavior supports continuing with the selected composition and rendering. At 1.2, demonstrate line clearing/progression and game over; record whether gravity, feedback, and controls support the accepted classic experience. Those observations inform a human decision to continue, amend, simplify, or stop. Milestone reporting surfaces evidence and material concerns; routine progression within an already authorized range requires no additional approval. Amendments and changes of implementation range remain human decisions.

The sequence completes one shared application rather than isolated replacement prototypes. Regression checks accompany growth. Each milestone closes on its included contracts; its report identifies scheduled obligations that remain without claiming complete-SPEC acceptance.

## Phase 2 — Modern play

Predecessor: completed, verified, integrated and published Phase 1 with its release transition reconciled; an authorized Phase 2 implementation range and verified eligible-phase tracking. Reuse its toolchain, bag, grounded timer, controller lifecycle, views and static launch path.

| ID and milestone | Integrated outcome and included scope | Dependencies and objective exit |
| --- | --- | --- |
| 2.1 — Hold and held preview | Add G08 eligibility, empty-slot promotion/occupied swap, canonical respawn/collision, held snapshot/preview, C and Hold controls. | Phase entry. K09/A11 and affected G02/G06/G07/K02/K06–K08 cases pass; demonstrate hold and disabled repeat with visible queue/held agreement in both browsers. Preserve foundation play and lifecycle. |
| 2.2 — Kicked rotation and ghost | Complete G03's ordered translated candidates, G09's shared landing projection, and ghost snapshot/drawing. | 2.1 reviewed/closed. K03/K11 and ghost portions of K10/A12 plus A14 pass, including wall/floor/rejection and view-only behavior. Demonstrate kicked movement and ghost updates after hold in both browsers. Executed hard-drop agreement completes in 2.3. |
| 2.3 — Hard drop and lock policy | Complete G09 hard drop/scoring, both core policies, controller policy routing, D/Hard drop and the native checkbox. | 2.2 reviewed/closed. K10/K12, A15/A16 and remaining A12 agreement pass: delay versus immediate, distance zero, no timer reset/retroactive lock, pause/restart/reload and focus/repeat. Demonstrate both policies in both browsers and preserve hold/kick/ghost behavior. |
| 2.4 — Modern release acceptance | Complete cross-feature regression, desktop/built/CI acceptance, guidance, version 0.2.0 and release preparation for the full target. | 2.3 reviewed/closed. All pre-integration A01–A16 and K01–K12 evidence is current. Exercise hold→kick→ghost→drop→lock flow, failure cleanup for added resources, all viewport/DPR and built root/prefix cases, observed CI and clean-checkout commands. |
| 2.5 — Phase review | One phase code review/testing/report for complete modern play and the entire task list. | All 2.1–2.4 work/reviews and issues are complete/closed. Repair blockers, confirm complete SPEC coverage and foundation regressions, retain phase and final implementation reports with TODO aggregation, then close this review milestone. Integration/release follow shared gates. |

This phase has four delivery milestones plus excluded 2.5 containing exactly one phase review task. Each delivery milestone ends with its explicit review/testing/report task. At 2.1, demonstrate hold usability and piece consumption; at 2.2, demonstrate rotation/ghost agreement with the board; at 2.3, demonstrate the two drop policies and timing. Record evidence and material concerns for a human continue/amend/simplify/stop decision, preserving the authorized range without added routine approval pauses.

Phase 2 preserves the foundation's controls, score/level rules, bag, lock interval, session and distribution guarantees while completing the explicitly scheduled rotation and presentation extensions. Earlier zero-offset-only rejection expectations are scoped to Phase 1; final rotation acceptance follows G03's complete ordered tests. There is one shipped game per phase, without a second runtime ruleset or persistent feature configuration.

## Contract delivery coverage

| Accepted contracts | Delivery and final evidence route |
| --- | --- |
| G01/G02/G07; K01/K02/K08; A01/A07/A13 | 1.1 establishes geometry, numeric-source bag/start, basic snapshots/input/Canvas; 1.2 completes promotion/collision and bag consumption; 2.1 completes hold-related flow. |
| G03–G05; K03–K05/K12; A02/A07/A14/A15 | 1.1 supplies zero-offset rotation; 1.2 supplies full grounding/gravity/clear/progression; 1.3 supplies paused timing; 2.2 completes K03/K11 translated kicks and 2.4 verifies interactions. |
| G06/R05; K07; A08 | Rejection/replay/immutability grow with each transition; browser containment in 1.3. Added hold/drop/policy/sample failures complete in 2.1–2.4. |
| R01–R03; K06; A03–A05 | Start/focus in 1.1; baseline session, independent clock streams and cap/order in 1.3; modern controls in 2.1/2.3. Each phase repeats applicable built pause/restart flows. |
| R04–R05; A06/A08 | Baseline board/next/HTML and faults in 1.1/1.3/1.4; held preview in 2.1, ghost in 2.2, policy in 2.3, complete desktop/DPR/failure matrix in 2.4. |
| G08; K09; A11 | 2.1 completes hold eligibility, empty/swap consumption, canonical replacement, collision and reset; 2.4 checks combined play. |
| G03/G09; K10/K11; A12/A14 | 2.2 completes kick tables and read-only landing/ghost; 2.3 completes executed ghost/drop agreement. |
| G05/G09; K10/K12; A15–A16 | 2.3 completes hard-drop distance/scoring, default delay/immediate exception, zero-distance, future-only setting changes, native focus/repeat and retention/reload; 2.4 checks whole-game interaction. |
| R06; A09–A10 | 1.1 establishes tooling/provisioning/build; 1.4 and 2.4 verify current built root/prefix, CI, guidance and versions. Each phase's source/tag/GitHub evidence completes after its review during release. |

## Verification, reports, and completion

Each delivery milestone's final review assesses its changed capability, component boundaries, code quality, relevant unit/controller/browser checks, failures, and regressions. Repair confirmed blockers before completion and persist the review/report with its owning task. Passing tests alone do not replace code review.

Use milestone reports under `docs/dev/reports/phases/<phase>/`, with that phase's `PHASE-REPORT.md` there. The final `docs/dev/reports/IMPLEMENTATION-REPORT.md` is produced at 2.5 when the whole task list completes. Reports contain a TODO section, including None when empty. Preserve justified non-blocking findings with IDs, evidence, impact, options, and ownership; required failures remain blockers. Phase/final reports aggregate unresolved findings and resolution references.

Chromium is primary and Playwright-managed Firefox secondary. [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) owns isolation, provisioning, modes, diagnostics, and recovery. Establish and retain its capability evidence during 1.1 before relying on browser-dependent implementation. Missing required capability blocks affected work; it cannot be replaced by a mocked acceptance claim.

Unit/controller evidence uses supplied state, numeric random sequences, clock/time and callbacks. Browser acceptance uses native events, real composition and produced assets, plus pixel/shape inspection for Canvas. Fault fixtures exercise production components and remain outside shipped assets. Each SPEC condition retains its stated evidence scope; core checks run once in the compatible Node environment. Phase 2 reuses the established isolated browser facility and rechecks capability if its environment changes.

Each phase review starts after its four delivery milestones and issues/reviews close. Phase 1 verifies its scoped coverage and identifies the scheduled modern obligations; Phase 2 verifies all A01–A16 and K01–K12. Both require documented commands, observed CI and pre-integration A10 evidence. A10's source/tag/release checks complete separately for each version:

1. Integrate the complete verified phase into the actual default branch using SDD Manager's authorized integration procedure. Verify the merged result, publish it, and confirm remote containment.
2. Finalize root `RELEASE-NOTES.md` for the actual phase scope, validation and material limits. Persist and verify the release commit; package and built versions agree with 0.1.0 for Phase 1 or 0.2.0 for Phase 2.
3. Publish the corresponding `v0.1.0` or `v0.2.0` tag at the verified release commit and create the GitHub release with those notes. Read back tag target, release version/body and any declared asset integrity.
4. Report implementation, integration, tracking, and release outcomes separately. Interrupted or uncertain effects remain pending and are reconciled before claiming phase release complete.

The user's release instruction authorizes each phase's release after its gates. A partial implementation range stops at its requested boundary with published task results; phase completion does not expand that range.

## Risks and next preparation boundary

| Risk or dependency | Planned control and gate |
| --- | --- |
| Isolated browser/runner capability | Resolve during 1.1 under the central policy, before dependent browser work. Retain exact versions and observed capabilities; diagnose each required browser independently. |
| Timing, bag or hold-consumption errors | Explicit core inputs, independent clock-stream checks, sample-count and grounded-boundary cases, and cross-feature regressions in 1.2–1.3/2.1–2.4. |
| Canvas usability or prefix-hosting failures | Early real drawing/input in 1.1; both-browser viewport/DPR and built root/prefix checks in 1.4 and 2.4 for their delivered capabilities. |
| Task derivation exposes an oversized phase or milestone | Assess both phases' semantic scope and unit counts; return strategy changes to PLAN before implementation without deleting accepted scope or adding padding. |
| Hosting, CI, integration, or release failure | Preserve verified local/published evidence and reconcile the specific pending transition. Required evidence remains a gate. |

No gameplay or architectural decision is deferred by this plan. Physical ownership remains for `layout.md`, which is prepared after human PLAN acceptance and receives combined PLAN/layout QC before its own acceptance. TASKS follows accepted layout; implementation follows whole-package QC, published preparation integration, and the authorized range. This proposal performs no implementation, installation, hosted phase activation, or release.
