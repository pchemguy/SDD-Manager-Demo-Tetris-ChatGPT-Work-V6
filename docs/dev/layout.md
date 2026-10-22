# Repository layout

This map assigns physical ownership for the accepted [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md), and [PLAN.md](PLAN.md). It describes the intended complete repository, not implementation progress.

The repository currently contains root guidance and development documents. Application, test, toolchain, CI, and generated-output locations below are proposed; they are not existing product files. [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation status. No layout children are needed for this scope.

## Application ownership

All production TypeScript and CSS belong under `src/`; root `index.html` is the production HTML entry.

| Component or responsibility | Intended location | Ownership boundary |
| --- | --- | --- |
| Core data contracts | `src/core/model.ts` | Plain piece/cell/board/state/input/snapshot types and canonical data constants. No browser or runner dependencies. |
| Canonical piece geometry | `src/core/geometry.ts` | Shape catalogue, orientation geometry, and occupied-cell projection used by board checks and engine snapshots. |
| Board operations | `src/core/board.ts` | Bounds/overlap, placement, row detection, and compaction; uses core data/geometry. |
| Rules engine and snapshot projection | `src/core/engine.ts` | Initialization and complete transitions, including rule time, selection, score/progression, status, and immutable presentation snapshots. Scoring remains in this cohesive rule owner. |
| Session controller | `src/session/controller.ts` | Current core-state reference, browser-time adaptation, injected callbacks, presentation coordination, and session lifecycle. Exports its narrow callback/session contracts here. |
| Input bindings | `src/browser/input.ts` | DOM keyboard/button subscriptions, focus/repeat interpretation, command delivery, and binding cleanup. |
| Frame source | `src/browser/frame.ts` | Browser frame scheduling and cancellation; elapsed-time conversion stays in the controller. |
| Production piece source | `src/browser/pieces.ts` | Production randomness through the canonical piece-kind boundary; no second geometry catalogue. |
| Board/preview renderer | `src/view/canvas.ts` | Both Canvas surfaces, drawing-buffer/cell sizing, colors, and drawing from snapshot cells. Shared drawing primitives remain here. |
| HTML status/control view | `src/view/status.ts` | Metrics, status, control labels/enabled state, and readable failure presentation. Event subscriptions remain with browser bindings. |
| Browser composition root | `src/browser/bootstrap.ts` | Resolve resources, assemble concrete collaborators, wire resize/redraw, and handle startup/partial-assembly cleanup. Its reusable mounting boundary supports controlled browser fixtures. |
| Production startup | `src/main.ts` | Load application styling and invoke the browser composition root. Keeps automatic page startup separate from importing the reusable bootstrap in tests. |
| Page structure and presentation | `index.html`, `src/styles.css` | Semantic playfield/controls/metrics/instructions and desktop layout/focus styling. Rule data comes from the core and views. |

Public values/functions are exported from their owning modules. This small application needs no barrel layer, generic service registry, shared miscellaneous module, or separate interface package.

## Dependency and lifecycle placement

Core modules import only core modules. Geometry uses model data; board operations use model/geometry; the engine uses those core units. The controller imports the core and receives browser/presentation services through narrow callbacks. It does not import concrete DOM/Canvas adapters.

Input/frame/piece adapters use core data and the controller's public callback types as needed. Views consume snapshot data and browser drawing/DOM APIs; they do not import engine transitions or maintain rotation/spawn rules. `bootstrap.ts` is the intentional assembly point allowed to import concrete collaborators across these areas. `main.ts` provides production startup rather than reusable test assembly.

Resize wiring belongs to bootstrap and participates in the existing session/composition cleanup lifetime. It redraws through presentation coordination without a gameplay command or piece draw. Established-session failure disposes through the controller; startup failure cleans resources already acquired. The status view supplies readable HTML failure output, including the missing-normal-status case. SPEC R03/R05 remain authoritative for outcomes and stale-callback guarantees.

## Checks and fixtures

| Evidence owner | Intended location | Boundary |
| --- | --- | --- |
| Geometry, board, and engine unit checks | `tests/unit/core/` | Vitest `*.test.ts` files follow the relevant core module names and test canonical contracts with controlled values. |
| Controller checks | `tests/unit/session/controller.test.ts` | Controlled input/frame/piece/render callbacks; no real browser clock dependency. |
| Production adapter unit checks | `tests/unit/browser/` | Deterministic checks for browser-independent adapter behavior, especially production piece selection with controlled random values. Native event/frame delivery stays in browser checks. |
| Shared deterministic fixture data | `tests/fixtures/` | Valid boards, piece sequences, and recording helpers only where reuse is useful. Product code never imports test fixtures. |
| Native browser and production-entry checks | `tests/browser/` | Playwright `*.spec.ts` files own input/focus, Canvas output, lifecycle/faults, and built root/prefix acceptance. |
| Controlled browser fixture entry | `tests/browser/fixtures/` | Test-only HTML/TypeScript entry and composition helpers import production bootstrap/components. They provide injected sources/faults without production test hooks or a substitute game engine. |

The unit runner selects the unit area; Playwright selects the browser area. Browser fixture entries are served through test-only runner/server setup. Production-entry checks separately serve the produced `dist/` assets at the required URL roots. The production build starts from root `index.html`; production imports and output exclude test entries, fixtures, and runner dependencies. Test server setup has no gameplay-service role.

## Tooling, packaging, and generated files

| Owner | Intended location and rule |
| --- | --- |
| Package/version/dependency authority | Root `package.json` and npm `package-lock.json`. Setup establishes compatible pinned versions, runtime requirements, and validated scripts. |
| Compiler/build/unit/browser configuration | Root `tsconfig.json`, `vite.config.ts`, `vitest.config.ts`, and `playwright.config.ts`. Explicit type checking and runner selection remain distinct; build configuration owns version injection and static asset paths. |
| Small execution helpers | `scripts/` only when a build/check/server helper has a real need beyond configuration. It owns tooling concerns, with no gameplay rules or duplicated browser policy. |
| CI | `.github/workflows/ci.yml` uses the committed runtime/dependencies and the same documented validation entry points. Browser setup follows the central policy. |
| Dependencies and build output | `node_modules/` and `dist/` are generated and untracked. `dist/` contains the shipped static application. |
| Isolated browser assets/state | Project-managed `.cache/playwright/` is the local browser home; installation and every runner process use its resolved absolute path. A CI job may use its own documented isolated equivalent under the central policy. |
| Test output | `test-results/`, `playwright-report/`, and optional `coverage/` are generated and untracked. Maintained fixture expectations stay under the test-owned areas. |

Root `.gitignore` must exclude generated locations before they are populated and retain its existing credential exclusion. [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) owns provisioning, isolation, diagnostics, and recovery; layout assigns artifact homes without duplicating that procedure. [WINDOWS_SHELL_POLICY.md](../../WINDOWS_SHELL_POLICY.md) applies on Windows hosts.

## Documentation and release ownership

[README.md](../../README.md) owns the user-facing project status, supported game capabilities/controls, launch/build/check instructions, and navigation. Commands are added as validated during implementation. Root [AGENTS.md](../../AGENTS.md) provides concise governing-document/owner navigation and points to established setup/check guidance.

`docs/dev/` owns PROJECT, ARCHITECTURE, DECOMPOSITION, SPEC with `spec/GAMEPLAY.md`, PLAN, this layout, implementation/preparation task records, and their adjacent QC reports. The future `TASKS.md` owns executable implementation progress; `DOCS_TASKS.md` owns preparation progress. `HANDOFF.md` remains a checkpoint recovered with later owning records and Git.

Milestone reports belong at `docs/dev/reports/phases/1/<milestone-id>.md`; the phase report at `docs/dev/reports/phases/1/PHASE-REPORT.md`; the final implementation report at `docs/dev/reports/IMPLEMENTATION-REPORT.md`. Review tasks own their evidence and TODO aggregation under PLAN.

The root `RELEASE-NOTES.md` owns finalized release text when release preparation begins. Package metadata owns the version; Git tags and GitHub releases publish the corresponding verified source and notes under the accepted release gates. Built output remains generated. No extra distribution asset is required by this layout.

## Placement review boundary

The source map covers every decomposed component and its checks; the reusable bootstrap and test-only entry support early production-path integration and controlled failures. Unit/browser separation, production import boundaries, generated-file isolation, and documentation/release homes support all four delivery milestones without changing PLAN or SPEC.

[PLAN-REVIEW-REPORT.md](PLAN-REVIEW-REPORT.md) owns combined strategy/layout QC. Human layout acceptance precedes TASKS derivation. A later required file may be assigned within its existing owner; a material new owner, dependency, or phase boundary requires the appropriate document review.
