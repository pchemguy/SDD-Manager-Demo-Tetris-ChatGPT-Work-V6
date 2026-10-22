# Project handoff

Checkpoint: Main-document preparation, iteration 2 extended-scope pass, DOCS-002. Recover later completed tasks and external effects from [DOCS_TASKS.md](DOCS_TASKS.md), Git, and hosted readback; this checkpoint need not match the branch tip.

## Workflow and sources

- Workflow/stage/owner: Main development, existing preparation documents extended in place. sdd-manage coordinates DOCS-002–DOCS-008; sdd-design owns PROJECT, ARCHITECTURE, and DECOMPOSITION, followed by the SPEC, PLAN/layout, and TASKS owners.
- Governing sources: [AGENTS.md](../../AGENTS.md), [discovery protocol](../../SKILL_DISCOVERY_PROTOCOL.md), [browser policy](../../BROWSER_DEV_POLICY.md), and the bundled [SDD Manager entry](../../.agents/plugins/sdd-manager/PLUGIN.md).
- Project-specific docs-iteration protocol, current status, source identities, human decisions, and prior acceptance history: [DOCS_TASKS.md](DOCS_TASKS.md). The protocol remains a project override; the bundled plugin is unchanged.
- Required package/setup availability: Repository-bundled SDD Manager 0.15.1; Linux host, Node 24, Python 3.12. Accepted TypeScript/Canvas/HTML, Vite, Vitest, and Playwright choices are being reassessed against the expanded scope. Product tooling is not established and no game code exists.

## Scope and authorization

- Complete target: Classic desktop browser Tetris plus hold, ghost, seven-bag, wall kicks, hard drop, and default full-gravity-interval lock delay from grounding. Hard drop uses the same delay unless immediate hard-drop locking is selected.
- User grant on 2026-10-22: Start at DOCS-002, extend each document through completed DOCS-008, and treat those human checkpoints as preapproved. Keep owner review/QC, publication, and tracking gates. This is a bounded document pass, not implementation authorization.
- Stop: Record and publish DOCS-008 completion, verify issue #8 closure, and wait for the user's decision on another loop or advancing. DOCS-009 and preparation integration are outside the current pass. No future checkpoint beyond DOCS-008 is preapproved.
- Version/release policy: First playable phase 0.1.0, semantic increments reflecting delivered changes, and a published tagged GitHub release with notes after every completed, verified, integrated implementation phase. No implementation phase or release is complete.
- Starting documents: Accepted baseline commit 1fc48c722bf55628af69e646b16f8483598498e7, retained through loop activation at 81ac5ebf530a9748ccf91e02c1cd90587442da7c. Reuse those versions and preserve historical evidence while extending them; current accepted content is recorded per task.

## Branches, tracking, and continuation

- Working branch: design-docs/main. Creation parent and eventual preparation integration target: main. Branch-off: b0597bf0f02c5e4ed5329adb606acafab63c15a5. No suspended child campaign.
- Tracking remains enabled. Native milestone #1, sdd-docs-main, is open; all nine exact issue definitions and associations were verified before writes. DOCS-001/issue #1 remain complete. DOCS-002–DOCS-008 close individually after each verified published completion; DOCS-009/issue #9 remain open.
- DOCS-002 is locally reviewed and accepted under scoped preapproval in this checkpoint's commit. Publish/read back the commit and close/read back issue #2 before ARCHITECTURE. Later task evidence in DOCS_TASKS supersedes this entry snapshot.
- Preserve the current range and stop after interruption. Push/read back every owned commit before further work. Do not infer completion from an old Ready label, unchanged file presence, or historical acceptance. Materially changed inputs require the affected downstream QC to be rechecked.

Refresh this checkpoint at the DOCS-008 stopping boundary; intervening document completion evidence remains in DOCS_TASKS.
