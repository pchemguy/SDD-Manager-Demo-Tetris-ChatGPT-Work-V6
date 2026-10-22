# Repository layout

This map assigns physical ownership for the accepted [ARCHITECTURE.md](ARCHITECTURE.md), [DECOMPOSITION.md](DECOMPOSITION.md), [SPEC.md](SPEC.md), and [PLAN.md](PLAN.md). It describes the intended complete repository, not implementation progress.

The repository currently contains root guidance and development documents. Application, test, toolchain, CI, and generated-output locations below are proposed; they are not existing product files. [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation status. No layout children are needed for this scope.

## Application ownership

All production TypeScript and CSS belong under `src/`; root `index.html` is the production HTML entry.

| Component or responsibility | Intended location | Ownership boundary |
| --- | --- | --- |
| Core data contracts | `src/core/model.ts` | Plain kind/variant/cell/board/state/input/snapshot types and canonical data constants. Settings types are imported from their owner rather than duplicated. No browser or runner dependencies. |
| Canonical piece geometry and kicks | `src/core/geometry.ts` | Shape/orientation catalogue, ordered kick data, and occupied-cell projection used by board checks and the engine. |
| Board operations and landing | `src/core/board.ts` | Separate in-bounds/overlap/traversability checks, shared regular/penetrator landing projection for ghost/hard drop, safe final placement, row detection and compaction; uses model/geometry. |
| Bag operations | `src/core/bag.ts` | Explicit shuffle/refill/consume operations and numeric-source validation. The engine retains bag remainder and decides consumption timing. |
| Configuration values and resolution | `src/core/settings.ts` | Pure setting keys/values/defaults, full-object and one-key validation, independent URL/cookie/default resolution and provenance. No DOM, URL/history, cookie operations or engine imports. |
| Rules engine and snapshot projection | `src/core/engine.ts` | Complete transitions, hold/variant eligibility, gravity/grounded timers, both general/drop policies, all six settings/gates, penetrator activation/bonuses/traversal, spawning, score/progression, status and immutable snapshots. No browser timing or duplicated landing algorithm. |
| Session controller | `src/session/controller.ts` | Current core reference, sampled-clock/frame adaptation, command/setting routing, independent persistence callbacks/save status, presentation frames and lifecycle. Exports narrow callback/session contracts; elapsed-before-action ordering stays here. |
| Input bindings | `src/browser/input.ts` | Keyboard/button/select/checkbox subscriptions, focus/repeat handling, command/setting delivery, and cleanup. |
| Clock and frame source | `src/browser/frame.ts` | Same-origin now sampling, frame scheduling/timestamps and cancellation; elapsed conversion/stream validation stay in the controller. |
| Configuration persistence adapter | `src/browser/settings.ts` | Browser URL/cookie extraction and per-key load/save, cookie attributes/readback and history replacement, using pure core resolution. Returns independent nonfatal outcomes; no gameplay state owner. |
| Production random source | `src/browser/random.ts` | Numeric random samples through the injected source boundary; no bag, piece-selection or geometry state. |
| Board/preview renderer | `src/view/canvas.ts` | Board/ghost, next and held Canvas surfaces, buffer/cell sizing, regular palette/white variants and snapshot drawing. Shared drawing primitives remain here; no collision, landing, midline or bonus decisions. |
| HTML status/control view | `src/view/status.ts` | Metrics/status, held kind/availability, penetrator symbols/availability, all six native settings, nonfatal save feedback and readable fatal output. Event subscriptions stay in bindings; setting state stays in the core. |
| Browser composition root | `src/browser/bootstrap.ts` | Resolve resources and configuration before first core presentation, assemble concrete collaborators, wire resize/redraw, and handle startup/partial-assembly cleanup. Its reusable mounting boundary supports controlled browser fixtures. |
| Production startup | `src/main.ts` | Load application styling and invoke the browser composition root. Keeps automatic page startup separate from importing the reusable bootstrap in tests. |
| Page structure and presentation | `index.html`, `src/styles.css` | Semantic playfield/controls/metrics/instructions, enabled previews, bonus indicator and six-setting side panel, desktop layout/focus, regular palette/white variants and ghost styling. Rule data comes from core snapshots. |

Public values/functions are exported from their owning modules. This small application needs no barrel layer, generic service registry, shared miscellaneous module, or separate interface package.

## Dependency and lifecycle placement

Core modules import only core modules. settings owns independent primitive setting contracts/resolution; model imports its setting types. Geometry uses model data; board uses model/geometry; bag uses model and supplied numeric samples; engine uses these units. No settings-to-engine dependency or cycle is introduced. The controller imports core and receives browser/presentation services through narrow callbacks, without concrete DOM/Canvas imports.

Input/clock/frame/random/settings adapters use core data and controller callback types as needed. Views consume snapshot data and browser APIs; they do not import engine transitions or maintain rotation, landing or hold rules. `bootstrap.ts` assembles concrete collaborators, including drawing resources, typed setting sink, startup configuration load and later save callbacks. Presentation-frame/save-result callback types live with controller contracts; views may import them as types without depending on browser persistence implementations. `main.ts` provides automatic production startup separately from reusable test assembly.

Resize wiring belongs to bootstrap and the existing cleanup lifetime. It redraws the latest frame without a gameplay command, time advance or random sample. Established-session failure disposes through the controller; startup failure cleans acquired resources, including partial preview/control assembly. Status supplies readable HTML failure output even when the normal status element is missing. SPEC R03/R05 own outcomes and stale-callback guarantees.

## Checks and fixtures

| Evidence owner | Intended location | Boundary |
| --- | --- | --- |
| Geometry, board, bag, and engine unit checks | `tests/unit/core/` | Module-aligned Vitest checks for tables, landing, shuffle/consumption, hold/variants, both locking policies, settings/gates, bonus/traversal, progression and immutable transitions. |
| Pure configuration checks | `tests/unit/core/settings.test.ts` | Schema/defaults, initialization/update validation, independent per-key precedence, omissions, explicit defaults and source provenance; no browser effects. |
| Controller checks | `tests/unit/session/controller.test.ts` | Controlled now/frame/input/setting/random/persistence/render callbacks, including interleaved timestamp streams and deadline-before-command behavior, save ordering/failure isolation and save-status lifetime. |
| Production adapter unit checks | `tests/unit/browser/` | Meaningful browser-independent adapter contracts where present. Thin wrappers may use production-entry coverage; avoid duplicate units that mirror delegation. Native event delivery remains browser evidence. |
| Persistence adapter checks | `tests/unit/browser/settings.test.ts` | Cookie parsing/formatting/readback, first-parameter extraction, URL preservation and independent operation failures through injected I/O; real cookie/history behavior remains browser evidence. |
| Shared deterministic fixture data | `tests/fixtures/` | Valid boards/state/bag values, numeric sample sequences, controlled clocks and recording helpers where reusable. Product code never imports these fixtures. |
| Native browser and production-entry checks | `tests/browser/` | Playwright `*.spec.ts` files own input/focus, Canvas variants/visibility/bonus output, all six controls, real cookies/history/reload/reopen, lifecycle/faults and built root/prefix acceptance. |
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

`docs/dev/` owns PROJECT, ARCHITECTURE, DECOMPOSITION, SPEC with `spec/GAMEPLAY.md` and `spec/CONFIGURATION.md`, PLAN, this layout, implementation/preparation task records, and their adjacent QC reports. `TASKS.md` owns executable implementation progress; `DOCS_TASKS.md` owns preparation progress. `HANDOFF.md` remains a checkpoint recovered with later owning records and Git.

Milestone reports belong at `docs/dev/reports/phases/<phase>/<milestone-id>.md`, with phase values 1, 2 and 3; each phase report is `PHASE-REPORT.md` in its phase directory. The final implementation report at `docs/dev/reports/IMPLEMENTATION-REPORT.md` is owned by the last phase review, 3.5. Review tasks own their evidence and TODO aggregation under PLAN.

The root `RELEASE-NOTES.md` owns finalized release text when release preparation begins. Package metadata owns the version; Git tags and GitHub releases publish the corresponding verified source and notes under the accepted release gates. Built output remains generated. No extra distribution asset is required by this layout.

## Placement review boundary

The source map covers every decomposed component and its checks; reusable bootstrap/test-only entry support early production-path integration and controlled failures. Phase 1 creates its supported foundation paths and controls; Phase 2 extends the same owners for held/ghost/kick/drop behavior and its hard-drop override select. Phase 3 adds the small pure settings/browser persistence modules and completes controls/conditional visibility/penetrators in existing engine/controller/views; no second ruleset or placeholder controls are shipped. Unit/browser separation, output isolation and report/release homes support all three phases without changing PLAN or SPEC.

[PLAN-REVIEW-REPORT.md](PLAN-REVIEW-REPORT.md) owns combined strategy/layout QC. Human layout acceptance precedes TASKS derivation. A later required file may be assigned within its existing owner; a material new owner, dependency, or phase boundary requires the appropriate document review.
