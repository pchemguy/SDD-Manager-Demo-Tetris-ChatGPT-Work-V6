# Main documentation tasks

This list owns main documentation preparation. Product implementation tasks belong to `TASKS.md`. Complete these tasks in order, reusing valid existing artifacts and completion evidence when resuming.

## Completion and tracking

Each document task and its issue track development through the document’s first human approval. After that approval, mark the task complete. Later document revisions leave this task complete and its issue closed. An explicit preapproval may satisfy the human checkpoint within its stated scope; it does not waive QC or publication. The user may preapprove future checkpoints at any time.

Include the checked task in `DOCS_TASKS.md` in the same commit that submits the approved document and records its first approval. If the reviewed document was already published unchanged, retain that exact document state in the acceptance commit; do not rewrite it merely to create a diff. Push and verify remote containment before closing the corresponding issue or advancing.

When tracking is enabled, each `DOCS-*` task maps to one issue in the `sdd-docs-main` milestone. Verify the milestone, all nine issues and their associations before modifying the repository. Reuse their stable identities on resume. Close issues only after the corresponding completion commit is published and verified. With tracking disabled, apply the same local task and checkpoint requirements.

The running SDD Manager package's `assets/DOCS_TRACKING_TEMPLATE.json` supplies the fixed milestone title/description and all nine issue definitions, including dependencies. Use those definitions through sdd-forge; this Markdown remains the local completion owner. Neither asset records provider handles or proves runtime eligibility. Resolve source/content conflicts before projection rather than replacing this list or inventing issue text.

## Preparation

- [x] DOCS-001 — Initialize the repository for SDD Manager.
    - Inspect root `AI_DISCLOSURE.md`, `SDD-MANAGER.md`, `AGENTS.md` and `SKILL_DISCOVERY_PROTOCOL.md` before copying. Preserve existing files and copy only missing files from the bundled templates.
    - Record which root files existed and which were copied; verify copied bytes and preserved content.
    - Establish/reconcile `docs/dev/HANDOFF.md` as generated checkpoint metadata, outside the four-file presence/copy test. Include it with this task's completion and initialization result, push and verify; then close and verify its issue when tracking is enabled.
    - Report initialization. If some but not all of the four root files existed initially, warn with the retained/copied filenames and end the turn before document work, unless the user explicitly preapproved continuation through this checkpoint. If none or all existed, continue without an additional approval pause.
- [x] DOCS-002 — Prepare and accept PROJECT.
    - Establish the project brief: purpose, users, outcomes, scope, non-goals and consequential constraints; identify material assumptions and unresolved decisions.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [x] DOCS-003 — Prepare and accept ARCHITECTURE.
    - Define major system blocks, relationships, dependency direction and consequential structural decisions from the accepted brief.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [x] DOCS-004 — Prepare and accept DECOMPOSITION.
    - Define component responsibilities, collaboration, ownership and design-level interface boundaries consistently with ARCHITECTURE.
    - Review the proposal with the human and complete its acceptance checkpoint.
- [x] DOCS-005 — Prepare and accept SPEC.
    - Specify observable behavior, interfaces, error contracts, required qualities and acceptance conditions from the accepted design.
    - Complete required SPEC QC and its human acceptance checkpoint.
- [x] DOCS-006 — Prepare and accept PLAN.
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

## Current preparation evidence

### DOCS-001 initialization

- Original state: all four root destinations existed as regular files. Retained AI_DISCLOSURE.md, SDD-MANAGER.md, AGENTS.md and SKILL_DISCOVERY_PROTOCOL.md byte for byte; copied none.
- Preservation was checked against baseline b0597bf0f02c5e4ed5329adb606acafab63c15a5. No mixed-initialization continuation pause applies.
- Created this checklist from the bundled SDD Manager 0.15.1 template and established HANDOFF.md. Added the credential exclusion to the existing root ignore file.
- Preparation tracking is selected by root AGENTS.md. The exact bundled sdd-docs-main milestone and all nine DOCS issue definitions/associations were read back before any repository write.
- Local verification: root-file preservation, checklist identity/order, checkpoint links, Markdown spacing, and credential exclusion. Initialization commit b79a8cd165ab6a9eed641bc84b9d076b1349dc6c was pushed to refs/heads/design-docs/main with exact matching remote readback and confirmed process cleanup. DOCS-001 issue #1 was then closed and read back as completed.

### Scope and release decisions

- 2026-10-22: The user selected the classic-only game target from the proposed scope options. This clarifies requirements; it does not approve a PROJECT document that has not yet been presented.
- 2026-10-22: The user instructed the agent to manage versioning and publish a corresponding tagged GitHub release with release notes after every completed, verified, integrated implementation phase.
- No future human checkpoint is preapproved for this repository. Layout first approval is the next human boundary.

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
- Presented PLAN content identity: blob 2d02a3c4cc20a36937eeea797f6e1e3d871eada6. The user accepted PLAN with “K” on 2026-10-22 in response to the approval request for published proposal commit 65bf300cfe6febdaab04fba05e3963e58da267a2. The acceptance commit retains its source blob, records human acceptance in the QC gate, and marks DOCS-006 complete. Publish and verify acceptance, then close/read back issue #6 before layout authoring. TASKS remains blocked until layout and combined QC/acceptance. Future checkpoints remain pending.
