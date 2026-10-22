# Main documentation tasks

This list owns main documentation preparation. Product implementation tasks belong to `TASKS.md`. Complete these tasks in order, reusing valid existing artifacts and completion evidence when resuming.

## Completion and tracking

Outside the project-specific iteration mode below, each document task and its issue track development through the document’s first human approval. After that approval, mark the task complete. Later document revisions normally leave this task complete and its issue closed. An explicit preapproval may satisfy the human checkpoint within its stated scope; it does not waive QC or publication. The user may preapprove future checkpoints at any time.

Include the checked task in `DOCS_TASKS.md` in the same commit that submits the approved document and records its first approval or the active iteration's acceptance. If the reviewed document was already published unchanged, retain that exact document state in the acceptance commit; do not rewrite it merely to create a diff. Push and verify remote containment before closing the corresponding issue or advancing.

When tracking is enabled, each `DOCS-*` task maps to one issue in the `sdd-docs-main` milestone. Verify the milestone, all nine issues and their associations before modifying the repository. Reuse their stable identities on resume. Close issues only after the corresponding completion commit is published and verified. With tracking disabled, apply the same local task and checkpoint requirements.

The running SDD Manager package's `assets/DOCS_TRACKING_TEMPLATE.json` supplies the fixed milestone title/description and all nine issue definitions, including dependencies. Use those definitions through sdd-forge; this Markdown remains the local completion owner. Neither asset records provider handles or proves runtime eligibility. Resolve source/content conflicts before projection rather than replacing this list or inventing issue text.

## Active iteration 2 scope and authorization

- On 2026-10-22 the user extended the complete target with hold, ghost pieces, seven-bag randomization, wall kicks, hard drop, and full-gravity-interval locking from grounding. Hard drop follows that policy by default, with a selectable immediate-hard-drop alternative.
- The user explicitly preapproved human checkpoints through completed DOCS-008 and instructed this pass to start at DOCS-002. The grant covers PROJECT, ARCHITECTURE, DECOMPOSITION, SPEC with its children, PLAN, layout, and TASKS, including their required owner review/QC and tracked completion. It does not waive technical gates or publication/readback.
- Stop after DOCS-008 completion is recorded, published, and its issue closure verified. Wait for the user to request another loop or approve advancing. DOCS-009, preparation integration, phase projection, implementation, and releases are outside this pass.
- Current position: DOCS-002 PROJECT is reviewed and accepted under this preapproval. Its completion publication and issue #2 closure must be verified before DOCS-003. Remaining documents use the retained accepted versions as their starting point; affected first-cycle QC is historical until rechecked for the extension.

## Preparation

- [x] DOCS-001 — Initialize the repository for SDD Manager.
    - Inspect root `AI_DISCLOSURE.md`, `SDD-MANAGER.md`, `AGENTS.md` and `SKILL_DISCOVERY_PROTOCOL.md` before copying. Preserve existing files and copy only missing files from the bundled templates.
    - Record which root files existed and which were copied; verify copied bytes and preserved content.
    - Establish/reconcile `docs/dev/HANDOFF.md` as generated checkpoint metadata, outside the four-file presence/copy test. Include it with this task's completion and initialization result, push and verify; then close and verify its issue when tracking is enabled.
    - Report initialization. If some but not all of the four root files existed initially, warn with the retained/copied filenames and end the turn before document work, unless the user explicitly preapproved continuation through this checkpoint. If none or all existed, continue without an additional approval pause.
- [x] DOCS-002 — Prepare and accept PROJECT.
    - Establish the project brief: purpose, users, outcomes, scope, non-goals and consequential constraints; identify material assumptions and unresolved decisions.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [ ] DOCS-003 — Prepare and accept ARCHITECTURE.
    - Define major system blocks, relationships, dependency direction and consequential structural decisions from the accepted brief.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [ ] DOCS-004 — Prepare and accept DECOMPOSITION.
    - Define component responsibilities, collaboration, ownership and design-level interface boundaries consistently with ARCHITECTURE.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [ ] DOCS-005 — Prepare and accept SPEC.
    - Specify observable behavior, interfaces, error contracts, required qualities and acceptance conditions from the accepted design.
    - Complete required SPEC QC and its human acceptance checkpoint.
- [ ] DOCS-006 — Prepare and accept PLAN.
    - Define delivery phases, milestones, dependencies and exit conditions against SPEC.
    - Complete PLAN QC, then present PLAN for human approval.
- [ ] DOCS-007 — Prepare and accept layout.
    - Allocate source, tests, documentation and artifacts to physical paths consistently with the accepted design and PLAN.
    - Review layout against the design and PLAN, update the PLAN review report, then present layout for human approval.
- [ ] DOCS-008 — Prepare and accept TASKS.
    - Derive executable tasks, dependencies and required milestone/phase review units from accepted SPEC, PLAN and layout.
    - Complete required task-list QC and its human acceptance checkpoint. Do not start implementation or project implementation-phase objects yet.
- [ ] DOCS-009 — Pass documentation package QC.
    - Require all preceding tasks complete and their issue closures verified when tracking is enabled.
    - Review the entire document set, necessary children and QC reports for consistency, traceability, delivery readiness and unresolved blockers. Check initialization, orientation, human acceptance and publication evidence.
    - Resolve blockers through their owning documents and recheck affected readiness and acceptance. Retain the package QC report with the preparation documents.
    - Refresh `docs/dev/HANDOFF.md` with the package outcome, evidence and next boundary, retaining later integration/milestone effects as requiring confirmation. Commit the handoff, passing report and this task marked complete together, push and verify; then close and verify the final QC issue.

## Integration and final checkpoint

After DOCS-009, explicitly merge the verified documentation branch into the actual default branch. Verify the merged result, commit, push and confirm remote containment. Then close and verify the `sdd-docs-main` milestone when tracking is enabled. Failed or uncertain integration or milestone closure remains pending; checked tasks alone do not complete preparation.

Report the package QC result, published merge and tracking status. Confirm the final checkpoint before implementation and obtain the authorized implementation range. Proceed without another pause only when explicit preapproval covers that checkpoint and implementation scope is already established.

On interruption, recover the earliest unmet operation from documents, acceptance decisions, Git and hosted evidence. Preserve a pending mixed-initialization decision. Do not regenerate the list, infer acceptance from file presence or repeat completed work. Honor explicit narrower scope, local-only or no-merge instructions and report any remaining completion steps.

## Active docs-iteration protocol

The user activated this project-specific protocol on 2026-10-22 after accepting TASKS, and clarified that the protocol is not part of SDD yet. It overrides the normal first-approval-only lifecycle for DOCS-002–DOCS-009 in this repository. SDD Manager itself is unchanged.

1. Record and publish the current proposal's acceptance before resetting the preparation state. Preserve its exact accepted content and acceptance history.
2. Keep DOCS-001 complete. Uncheck DOCS-002–DOCS-009 for the new iteration, reopen their existing issues, and retain the existing preparation milestone and issue identities. Keep the milestone open while preparation is incomplete.
3. Start from the currently accepted document versions, including necessary children. Review and revise those versions in task order; do not regenerate them, rewind Git, or erase prior acceptance/QC evidence. Reassess affected QC under the owning procedure and retain unchanged valid evidence with its source identities.
4. Record each new iteration's review and human decision separately from the retained baseline. Historical approvals do not complete the new iteration. Normal human checkpoints, publication/readback, and issue-closure requirements still apply; honor only the explicitly recorded scope of any future-checkpoint preapproval.
5. Activation alone stops ready at DOCS-002. A later instruction may authorize a bounded document pass; retain its actual task range and stopping checkpoint. Apply another reset only when the user requests it.

### Iteration 2 activation

- Accepted starting point: commit 1fc48c722bf55628af69e646b16f8483598498e7, published on design-docs/main with exact matching remote readback and confirmed process cleanup. DOCS-008 issue #8 was then closed and read back as completed before this reset.
- Activation state: DOCS-001 remained complete; DOCS-002–DOCS-009 were unchecked for iteration 2. Prior approvals below are retained baseline history. DOCS-009 has never passed; no whole-package QC report exists.
- Hosted transition verified: reset commit e11f520a4a9092cf1abd7bab40f183974089f043 was published with exact matching remote readback and confirmed process cleanup. Issues #2–#8 were reopened; issue #9 was already open and remains open. A final readback confirmed all eight open, issue #1 closed as completed, and every existing issue identity/association intact. Milestone #1 (sdd-docs-main) remains open. Reset comments link the published protocol; titles, bodies, labels, assignees, and milestone associations were preserved for each reset issue.
- Activation stopped ready for DOCS-002 / PROJECT review using the retained version. The later bounded-pass authorization below governs subsequent work. Documentation remains on design-docs/main, unmerged; implementation has not started.

The following accepted content identities are unchanged by activation:

| Document | Accepted Git blob |
| --- | --- |
| [PROJECT.md](PROJECT.md) | 01071a30988992a29a75e26be6577b0fcc6257e1 |
| [ARCHITECTURE.md](ARCHITECTURE.md) | e9aadc3553baf30230cbafd1b8735c51c71fa510 |
| [DECOMPOSITION.md](DECOMPOSITION.md) | f182fecfff88394c54948d124957335b9b014038 |
| [SPEC.md](SPEC.md) | bdf553202ed51a8983154a94b97cc5dd1b8b6672 |
| [spec/GAMEPLAY.md](spec/GAMEPLAY.md) | 4ab78ecd8ef23cc0438b391b45c862970a2364b0 |
| [PLAN.md](PLAN.md) | 2d02a3c4cc20a36937eeea797f6e1e3d871eada6 |
| [layout.md](layout.md) | 4f2643c49661a477ee23dfac91b4fe67a1b5d35e |
| [TASKS.md](TASKS.md) | b5c135676d04fac801e925191e09c727cc117c04 |

The associated accepted AGENTS navigation was blob 6ed692d7057d468ad070e3a8497bcba8af38daeb. At activation its entire content was retained as a prefix and the project protocol pointer was appended. Later authorized project-guidance changes are recorded with their owning document task. QC reports retain baseline findings and add iteration context, without claiming a fresh technical review.

## Iteration 2 document completion evidence

### DOCS-002 — PROJECT extension

- Extended the retained brief with all six requested modern requirements and the selectable immediate-hard-drop exception. Retained desktop keyboard use, classic foundation, deterministic checks, static delivery, browser-policy reference, and phase-release/version rules. Excluded unrelated configuration, persistence, and service scope.
- Updated README scope/navigation and the project description in AGENTS; original governing instructions are unchanged. Owner review checked the request against included scope, exclusions, intended outcomes, delegated SPEC details, delivery boundaries, and actual implementation status. No material question blocks architecture.
- Human decision basis: the user's explicit DOCS-002–DOCS-008 preapproval for this pass, not a new interactive approval. Accepted PROJECT blob: 979274fe1d8af394300b13eb53f96df1c2ec0c46; associated README blob: 082c9227e2e61c0dbf81f4fce5b96c39d99ab986; associated AGENTS blob: 4804c99fa5d2fd765d3745af5d89740df9513d99. This completion commit includes the checked DOCS-002 and reviewed content. Publication and issue closure require actual readback before the next task.

## Retained first-cycle preparation evidence

### DOCS-001 initialization

- Original state: all four root destinations existed as regular files. Retained AI_DISCLOSURE.md, SDD-MANAGER.md, AGENTS.md and SKILL_DISCOVERY_PROTOCOL.md byte for byte; copied none.
- Preservation was checked against baseline b0597bf0f02c5e4ed5329adb606acafab63c15a5. No mixed-initialization continuation pause applies.
- Created this checklist from the bundled SDD Manager 0.15.1 template and established HANDOFF.md. Added the credential exclusion to the existing root ignore file.
- Preparation tracking is selected by root AGENTS.md. The exact bundled sdd-docs-main milestone and all nine DOCS issue definitions/associations were read back before any repository write.
- Local verification: root-file preservation, checklist identity/order, checkpoint links, Markdown spacing, and credential exclusion. Initialization commit b79a8cd165ab6a9eed641bc84b9d076b1349dc6c was pushed to refs/heads/design-docs/main with exact matching remote readback and confirmed process cleanup. DOCS-001 issue #1 was then closed and read back as completed.

### Scope and release decisions

- 2026-10-22: The user selected the classic-only game target from the proposed scope options. This clarifies requirements; it does not approve a PROJECT document that has not yet been presented.
- 2026-10-22: The user instructed the agent to manage versioning and publish a corresponding tagged GitHub release with release notes after every completed, verified, integrated implementation phase.
- No future human checkpoint is preapproved for this repository. The user accepted TASKS and requested a project-specific documentation iteration reset, followed by a stop ready at DOCS-002.

### DOCS-002 PROJECT proposal

- Prepared PROJECT.md and the root README with scope/progress navigation and SDD Manager/disclosure links.
- Decision basis: the user's classic-only selection and phase-release instruction. The brief proposes desktop keyboard play, a 10-by-20 board, seven tetrominoes, scoring/levels, next preview and session controls. Additional non-goals are stated as proposed scope boundaries.
- Agent-selected initial release version: 0.1.0. Subsequent phase releases use semantic version increments reflecting delivered changes; every release follows phase completion, verification and integration.
- Owner review checked purpose and intended outcome, principal scope and exclusions, confirmed versus proposed decisions, testability/delivery constraints, centralized browser-policy reference and later-stage decision boundaries. Markdown spacing and local links passed. This is brief review, not SPEC or whole-package QC.
- Presented PROJECT content identity: blob 01071a30988992a29a75e26be6577b0fcc6257e1. The user approved this exact proposal with “Approve” on 2026-10-22, as published in commit f5b9ceab26a97fdc56b4d3421a6d44920846467b. The acceptance commit retains its exact PROJECT blob and marks DOCS-002 complete. Acceptance commit 596eda31b7e4bdf1804ab441bf2ee0c5714ba46b was pushed with exact matching remote readback and confirmed cleanup. Issue #2 was then closed and read back as completed before ARCHITECTURE authoring. This accepts PROJECT only; no future checkpoint is preapproved.

### DOCS-003 ARCHITECTURE proposal

- Proposed TypeScript with strict checking, Canvas 2D and HTML controls, Vite plus a separate TypeScript check, Vitest and Playwright. Compared JavaScript/Canvas and TypeScript/DOM alternatives and retained their tradeoffs in ARCHITECTURE. These technology choices were accepted with the architecture approval recorded below.
- Defined game-core, session-controller, browser-adapter and presentation ownership, directed dependencies, explicit time/randomness inputs, snapshot flow, lifecycle ownership and failure boundaries.
- Kept exact game contracts in SPEC, logical interface detail in DECOMPOSITION, delivery sequencing in PLAN and physical allocation in layout. Browser provisioning and recovery remain governed by the root policy.
- Owner review checked PROJECT alignment, classic-only scope, cohesive ownership, dependency direction, determinism, test seams, appropriate document boundaries and source-backed tooling claims. Local Markdown/link and content-identity checks are recorded with the proposal commit. Browser execution and implementation are not claimed.
- Presented ARCHITECTURE content identity: blob e9aadc3553baf30230cbafd1b8735c51c71fa510. The user accepted this published architecture with “k” on 2026-10-22 in response to its approval request. Presented commit: 64ebe9fc76b162ff509a25952846177421c91526. The acceptance commit retains the exact ARCHITECTURE blob and marks DOCS-003 complete. Acceptance commit b1955e1afdeda60044064994cac702b89a140f55 was pushed with exact matching readback and confirmed cleanup. Issue #3 was then closed and read back as completed before DECOMPOSITION authoring. Future checkpoints remain pending.

### DOCS-004 DECOMPOSITION proposal

- Refined the accepted four architectural blocks into cohesive geometry/model, board, rules-engine, controller, browser-binding, renderer/view and composition responsibilities. Component names remain logical; physical allocation is deferred to layout.
- Defined provisional cell/board/input/piece-source/state/snapshot/binding/view/session collaboration contracts and single ownership of core state, rule time, browser timestamps, bindings and drawing resources.
- Reviewed dependency direction, snapshot immutability, canonical geometry, deterministic time/randomness seams, complete transition publication, restart/resize/disposal and startup failure ownership against accepted ARCHITECTURE and PROJECT.
- Kept gameplay constants and exact contracts for SPEC, sequence for PLAN, and file ownership for layout. No architecture or scope amendment was required. Local Markdown/link, content identity and preparation-state checks accompany the proposal commit. This is design review; no product execution is claimed.
- Presented DECOMPOSITION content identity: blob f182fecfff88394c54948d124957335b9b014038. The user accepted the published decomposition with “k” on 2026-10-22 in response to its approval request. Presented commit: 9d90fcaff860a0743e34104480cf6c4316eeeb76. The acceptance commit retains the exact DECOMPOSITION blob and marks DOCS-004 complete. Acceptance commit 2ba431411629f5213bb3f63df51e359ff0d65a63 was pushed with exact matching remote readback and confirmed cleanup. Issue #4 was then closed and read back as completed before SPEC authoring. Future checkpoints remain pending.

### DOCS-005 SPEC proposal

- Prepared [SPEC.md](SPEC.md) with a focused [gameplay child](spec/GAMEPLAY.md). The root owns session/input/time, snapshots/presentation, failure, distribution/release, and system acceptance contracts; the child owns canonical geometry and core rules.
- Proposed exact classic defaults within the accepted design's delegation: independent piece draws, fixed-matrix clockwise rotation without kicks, locking on failed downward movement, line-clear scoring multiplied by the pre-lock level, ten-line level increments, and 1000-to-100 ms gravity. Browser keys, explicit pause/focus behavior, a 250 ms frame-gap cap, restart and failure cleanup are specified for review.
- Completed scoped design/contract QC and corrected state-precondition and malformed-timestamp gaps. [SPEC-REVIEW-REPORT.md](SPEC-REVIEW-REPORT.md) retains the initial findings, Revision 1, exact governing/reviewed blobs, grouped coverage, actual document checks, and implementation evidence limits. Current technical gate: Ready. No product tests or browser provisioning are claimed.
- Presented content identities: SPEC blob bdf553202ed51a8983154a94b97cc5dd1b8b6672; gameplay child blob 4ab78ecd8ef23cc0438b391b45c862970a2364b0. The user accepted SPEC and its gameplay child with “K” on 2026-10-22 in response to the approval request for published proposal commit 3774c63e6795526afb5f384b5b59379a6322c520. The acceptance commit retains both source blobs, records human acceptance in the QC gate, and marks DOCS-005 complete. Acceptance commit a897264bc265c4a86838fa5ef5cb86d82113b7b4 was pushed with exact matching remote readback and confirmed cleanup. Issue #5 was then closed and read back as completed before PLAN authoring. Future checkpoints remain pending.

### DOCS-006 PLAN proposal

- Prepared [PLAN.md](PLAN.md) for one bounded classic-game phase, version 0.1.0. Four delivery milestones establish an interactive browser slice, continuous play, reliable session controls, and desktop distribution. A fifth milestone contains exactly one final phase review task. Each delivery milestone also ends with its own review/testing/report task.
- Assessed the complete classic-only target as a small coherent first release, with direct state/view dependencies and no independent enhancement scope. Defined included/deferred milestone contracts, early demonstrations and human feedback, regressions, browser provisioning, failures, CI, integration and release gates. Task derivation must return excessive scope to PLAN rather than hide it in broad tasks.
- [PLAN-REVIEW-REPORT.md](PLAN-REVIEW-REPORT.md) records governing source identities, semantic/count assessments, complete grouped R/G/A/K contract coverage, actual document checks, and evidence limits. Technical gate: Ready for strategy only. No confirmed strategy finding remains; placement and combined PLAN/layout QC are pending.
- Presented PLAN content identity: blob 2d02a3c4cc20a36937eeea797f6e1e3d871eada6. The user accepted PLAN with “K” on 2026-10-22 in response to the approval request for published proposal commit 65bf300cfe6febdaab04fba05e3963e58da267a2. The acceptance commit retains its source blob, records human acceptance in the QC gate, and marks DOCS-006 complete. Acceptance commit 5c3591ec29d282de217ae16ef8a5171b74b0bbac was pushed with exact matching remote readback and confirmed cleanup. Issue #6 was then closed and read back as completed before layout authoring. TASKS remains blocked until layout and combined QC/acceptance. Future checkpoints remain pending.

### DOCS-007 layout proposal

- Prepared [layout.md](layout.md) with source/interface ownership for every decomposed component, a reusable browser bootstrap and thin production entry, unit/native-browser/fixture boundaries, build/configuration/CI ownership, generated-file isolation, and documentation/report/release locations. Proposed product paths are explicitly distinguished from existing documents.
- Appended project-document navigation to root AGENTS.md through the documentation owner. Preserved its existing instructions as an unchanged prefix, linked preparation acceptance ownership, and stated that product paths and validated application commands are not established yet.
- Extended [PLAN-REVIEW-REPORT.md](PLAN-REVIEW-REPORT.md) with Revision 1 combined coverage. Corrected adapter-unit-test placement and pending-proposal navigation wording; retained initial findings, source identities, actual rechecks, and the original strategy assessment. Technical gate: Ready for PLAN and layout. No strategy, gameplay, or architectural amendment was needed.
- Presented layout blob: 4f2643c49661a477ee23dfac91b4fe67a1b5d35e. Associated navigation blob: c1458f61a903d66979227b34418235324e19901a. The user accepted the layout proposal with “K” on 2026-10-22 in response to the approval request for published commit c571fbf134f3c5346fb00a6f77b7af5cdc1ebacd. The acceptance commit retains both source blobs, records human acceptance in combined QC, and marks DOCS-007 complete. Acceptance commit 03c845810506c0f59e8daf887b9b936bf9e26bb6 was pushed with exact matching remote readback and confirmed cleanup. Issue #7 was then closed and read back as completed before TASKS authoring. Future checkpoints remain pending.

### DOCS-008 TASKS proposal

- Prepared [TASKS.md](TASKS.md) with one phase, the accepted five milestone identities, and 24 unchecked executable tasks: 19 delivery tasks plus five dedicated reviews. Delivery counts are 6/4/5/4; the first milestone's six bounded units are explicitly assessed rather than compressed to meet a count target.
- Assigned edit owners, prerequisites, contracts, relevant unit/native/browser/build/CI/documentation evidence, milestone/phase review reports and HANDOFF updates. Preserved full classic scope, the accepted early-slice limits, source/fixture isolation, complete contract coverage, and post-phase integration/release gates. No implementation issue projection is authorized by this proposal.
- [TASKS-REVIEW-REPORT.md](TASKS-REVIEW-REPORT.md) retains exact governing/initial/final identities, semantic/count assessments, setup-probe/CI/report-path findings, Revisions 1–2 corrections/rechecks, and evidence limits. Technical gate: Ready for task decomposition; no confirmed issue remains. Root AGENTS navigation now links the task hierarchy while preserving its entire previous content.
- Presented TASKS blob: b5c135676d04fac801e925191e09c727cc117c04. Associated navigation blob: 6ed692d7057d468ad070e3a8497bcba8af38daeb. The user accepted the published proposal in commit 0bd92a885f1b6822b9cb7dc889a538297f63edb3 with “Record acceptance” on 2026-10-22. Acceptance commit 1fc48c722bf55628af69e646b16f8483598498e7 retains both source blobs, records human acceptance in the QC gate, and marks DOCS-008 complete for the first cycle. It was pushed with exact matching remote readback and confirmed cleanup. Issue #8 was then closed and read back as completed before the requested reset.
- The same request authorizes a project-specific docs-iteration loop: retain the accepted document contents, uncheck DOCS-002–DOCS-009, reopen their existing issues, and stop ready to repeat the cycle from DOCS-002. The user clarified that this protocol is not part of SDD yet. This request does not authorize package QC, preparation integration, implementation, or a release during the reset. No product code, installation, browser execution, CI run, hosted phase activation, or release is claimed.
