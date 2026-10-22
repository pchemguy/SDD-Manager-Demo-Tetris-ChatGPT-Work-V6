# Project handoff

Checkpoint: Main-document preparation, iteration 3, completed DOCS-008 review/acceptance recorded. This completion must be published/read back and issue #8 closed/read back before the bounded pass is fully stopped. Recover subsequent effects from [DOCS_TASKS.md](DOCS_TASKS.md), Git and hosted readback; this snapshot's containing commit identifies the document state.

## Workflow and sources

- Workflow/owner: Main development, project-specific docs-iteration pass coordinated by sdd-manage. Design, specification, plan/layout and task owners extended the accepted documents in order with all four additions together.
- Governing sources: [AGENTS.md](../../AGENTS.md), [discovery protocol](../../SKILL_DISCOVERY_PROTOCOL.md), [browser policy](../../BROWSER_DEV_POLICY.md) and bundled [SDD Manager entry](../../.agents/plugins/sdd-manager/PLUGIN.md).
- [DOCS_TASKS.md](DOCS_TASKS.md) owns iteration 3 scope/authorization, accepted identities, completion evidence and prior histories. The docs-iteration protocol remains a project override; SDD Manager itself is unchanged.
- Available environment: Bundled SDD Manager 0.15.1; Linux, Node 24, Python 3.12. Product tooling/browser capability has not been established. No game or product test implementation exists.

## Target and accepted preparation

- Complete target: Desktop browser Tetris with classic play, hold, ghost, seven-bag, clockwise wall kicks, hard drop, conditional ghost visibility, penetrator bonuses/mode and all six persisted settings. Defaults are ghost show, hard drop/next/hold on, general interval locking and hard-drop follow. Up/Space rotate, C holds, D drops and X activates an available penetrator.
- A dynamic ghost is visible only when its uppermost landing row is at least 10. Penetrators traverse occupied cells nondestructively, keep a reachable valid landing, use the deepest valid landing for ghost/drop, and lock only inside empty board cells. Four-line clears earn one bonus up to three; activation spends one. Variant survives Hold until lock; white locked appearance remains an ordinary obstacle.
- Configuration resolves each setting from valid explicit URL, then cookie, then default. Explicit URL defaults save to cookies; omissions retain cookies. Player changes save cookie/URL independently; failures keep settings active and show nonfatal feedback. CONFIGURATION owns exact formats and lifecycle.
- PROJECT, ARCHITECTURE, DECOMPOSITION, SPEC with GAMEPLAY/CONFIGURATION, PLAN, layout and TASKS have been extended and accepted under the instructed loop's scoped preapproval. SPEC/design, PLAN strategy/combined layout and TASKS QC passed with prior evidence retained. DOCS-009 whole-package QC is still outstanding.
- PLAN: 0.1.0 classic foundation, 0.2.0 regular modern play, 0.3.0 configurable penetrator play; each has four delivery milestones and one phase review. TASKS contains 64 unchecked tasks (49 delivery, 15 review); all 43 prior IDs/parents are retained. T-025 remains before dependent T-003; T-064/3.5 owns final implementation reporting.
- The user's version/release instruction remains: publish a corresponding tag and GitHub release with notes after every completed, verified, integrated implementation phase. These versions are planned; no phase or release is delivered.

## Authorization and exact stop

- The user requested another loop with four additions and clarified “You are to proceed with the loop with specified scope extension.” Continue the established DOCS-002–DOCS-008 pass with its scoped checkpoint preapproval. QC, publication/readback and issue closure remain required; no new interactive approval is claimed.
- After DOCS-008 publication and verified issue #8 closure, stop and await the user's next loop/advancement decision. DOCS-009, preparation integration, implementation-phase projection, implementation and releases remain outside this pass.

## Branches, tracking and pending effects

- Working branch: design-docs/main. Creation parent/eventual preparation integration target: main. Branch-off: b0597bf0f02c5e4ed5329adb606acafab63c15a5. No suspended child campaign; preparation remains unmerged.
- Tracking is enabled. Native milestone #1 (sdd-docs-main) and all nine definitions/associations were verified before entry writes. Reset 0f02b85dd34a8ae0720019d2fb37b7dddb40ba7a was published/read back and issues #2–#8 reopened. DOCS-002–007 completions were each published/read back and their issues closed/read back before dependent authoring.
- Last confirmed prior transition: layout completion b0854a6c01b0a87fe6cfc527cee353696cf77290 was published with exact remote readback/confirmed cleanup; issue #7 then closed/read back as completed. Issues #1–#7 are closed/completed, #8/#9 open and milestone #1 open as of this checkpoint. DOCS-001–008 are checked locally, DOCS-009 unchecked; issue #8 awaits this completion's publication before closure.
- Push/read back this completion, close/read back issue #8 with evidence, verify final checklist/hosted states and stop. Reconcile interrupted effects before replay; preserve all issue identities and the exact stopping boundary.

Implementation remains gated by DOCS-009, published preparation integration, a later advancement/range decision and eligible-phase tracking.
