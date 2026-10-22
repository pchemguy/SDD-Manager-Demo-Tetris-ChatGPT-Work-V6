# System specification

The application is a classic single-player browser Tetris game for desktop keyboard play, with the scope in [PROJECT.md](PROJECT.md). [ARCHITECTURE.md](ARCHITECTURE.md) and [DECOMPOSITION.md](DECOMPOSITION.md) define the responsible structural units.

[spec/GAMEPLAY.md](spec/GAMEPLAY.md) canonically defines board/piece data, core states, spawning, movement, rotation, locking, gravity, scoring, and core rejection behavior. This root owns browser/session behavior, presentation, cross-component snapshots, runtime failures, distribution, and system acceptance.

## R01 — Session and controls

The page opens in the gameplay contract's `ready` state, with an empty board and zero score/lines at level 1. The playfield is a named, keyboard-focusable region and receives initial focus after successful startup.

| Visible control | Availability | Action |
| --- | --- | --- |
| Start | Enabled in ready; disabled otherwise | Start a game and focus the playfield. |
| Pause / Resume | Enabled in playing/paused; disabled otherwise | Pause when playing; resume when paused. Button activation resumes focus on the playfield only for Resume. |
| Restart | Enabled in playing/paused/over; disabled in ready | Start a fresh game and focus the playfield. |

The displayed status is Ready, Playing, Paused, or Game over, corresponding to the core status. Reload resets to ready; game state and settings are not persisted. A paused board and preview remain visible. Game over retains the final locked board and next preview, with no active piece.

## R02 — Keyboard and focus

Game key bindings operate only while the playfield has keyboard focus. Buttons retain native keyboard activation. Clicking the playfield focuses it.

| Keyboard code | Action | Repeated keydown |
| --- | --- | --- |
| ArrowLeft / ArrowRight | Move one column left/right while playing | Accepted |
| ArrowDown | One soft-drop attempt while playing | Accepted |
| ArrowUp / Space | One clockwise rotation while playing | Ignored |
| KeyP / Escape | Pause playing or resume paused | Ignored |
| KeyR | Restart playing, paused, or over | Ignored |
| Enter | Start ready or restart over | Ignored |

Recognized playfield keys prevent their browser default, including when their game action is ignored by the current state. Tab and unrecognized keys keep their normal browser behavior. Keyboard events on buttons or elsewhere do not produce game commands. Movement/rotation/drop input in ready, paused, or over is ignored without consuming a piece draw.

Movement repeat follows browser-delivered keydown events; each accepted event produces one command. There is no separate held-key movement timer. Pause is explicit: losing playfield focus stops game-key handling but does not change the playing status. Visibility changes do not automatically resume or pause a game.

## R03 — Browser time and resource lifecycle

The controller converts animation-frame timestamps to milliseconds of gameplay input. Its first frame after startup, start, restart, or resume establishes an anchor and advances zero time. A later playing frame supplies `min(250, max(0, timestamp - anchor))` milliseconds and updates the anchor. Timestamps must be numeric, finite, and nondecreasing across all delivered frames in the mounted session. A malformed or decreasing timestamp causes the R05 runtime-failure path before submitting elapsed time or updating the anchor. Paused/ready/over frames do not advance core time.

Browser scheduling gaps therefore contribute at most 250 ms per frame. Core advancement itself follows GAMEPLAY G04 without that browser cap. Input commands are handled in callback arrival order and do not independently sample wall-clock time. Resume preserves the core's accumulated partial gravity interval and establishes a fresh browser anchor.

Each mounted session owns at most one input binding set and one animation-frame scheduling chain. Restart replaces game state within that lifecycle. Resize redraws the last snapshot using new presentation dimensions and leaves core state and piece draws unchanged.

Disposal releases the session's event/scheduling handles, is safe to call twice, and makes later commands or queued callbacks inert. No disposed session may mutate state or update the view.

## R04 — Visible output and usability

The board shows its 10 columns and 20 rows, empty-cell/grid boundaries, locked cells, and the active piece. Each piece kind has a stable color within a build. The next preview depicts the core's next kind in its initial orientation. Score, Lines, Level, Next kind, status, controls, and keyboard instructions use HTML text.

A handled input's resulting snapshot appears by the next rendered frame. Canvas and HTML derive from the same snapshot. Preview, score, and status agree with core state; rendering cannot advance gameplay or draw from the piece source.

At 100% browser zoom, 1280×800 and 800×600 viewports must show the entire board, preview, metrics, instructions, and controls without clipping or page scrolling. Drawing-buffer scaling must preserve the logical grid at device pixel ratios 1 and 2. Controls expose accessible names, ordinary tab navigation, and visible focus indication.

## R05 — Cross-component data and failures

The controller retains core state and applies the canonical gameplay inputs. The engine alone changes board, active/next pieces, rule time, score, lines, level, and core status. Caller-supplied time and piece draws make results reproducible.

A presentation snapshot contains:

- `status`, `score`, `lines`, and `level` with the gameplay values;
- the row-major board of locked cells;
- `activeCells`: occupied absolute board coordinates with piece kind, sorted by row then column;
- `nextKind` and `previewCells`: initial-orientation cells normalized to minimum local x/y zero, sorted by row then column.

The snapshot and earlier state values remain unchanged by later transitions or rendering. Ready has null next kind and empty active/preview arrays. Paused retains the playing geometry. Over has no active cells and retains the failed-spawn candidate as its next preview. Canonical geometry is projected by the core.

The engine rejects invalid inputs as specified in GAMEPLAY G06 before publishing a new state. Normal collisions and game over use ordinary outcomes. If a source, engine, or renderer throws during browser operation, the controller disposes its bindings/scheduling and the browser boundary displays an HTML alert stating that the game cannot run and the page should be reloaded. Gameplay controls are disabled. Repeated callbacks must not produce repeated alerts or restart the loop.

Missing required HTML elements or a Canvas 2D context produce the same readable startup failure and cleanup of acquired resources. The alert must remain visible even if the normal status element is unavailable. Reload is the recovery path. Raw stack traces and tool configuration are not part of the game interface.

## R06 — Build, browser targets, and releases

The production application consists of locally served static HTML, CSS, and JavaScript assets, with no runtime backend, CDN, external font, or account dependency. It works from an HTTP server at the host root and below a non-root URL prefix. Direct `file://` loading is outside the supported launch contract.

TypeScript checking, unit tests, production build, and browser checks have documented repeatable commands using the committed compatible toolchain and lockfile. Production output excludes fixtures and runner dependencies.

[BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) governs selection, isolation, provisioning, diagnostics, and recovery. Chromium is primary and Playwright-managed Firefox is secondary; default acceptance uses their headless modes. The policy's required capability evidence precedes dependent browser work.

Package metadata is the authoritative version; built version information, the `v<version>` Git tag, and release notes agree. The first playable phase release is 0.1.0. Later minor/patch increments reflect delivered functionality/fixes. Each implementation phase release requires completed phase work, verification, integration into the published default branch, corresponding tag, and GitHub release notes describing delivered changes and validation.

## Structural traceability

| Contract group | Responsible components from DECOMPOSITION |
| --- | --- |
| GAMEPLAY G01–G06 | Core model/geometry, board operations, rules engine, and piece source |
| R01–R03 | Rules engine, session controller, input bindings, frame source, and composition root |
| R04–R05 | Snapshot projection, board/preview renderer, status/control view, controller, and composition root |
| R06 | Browser entry, build/test composition, and release orchestration |

These contracts preserve the accepted classic-only exclusions in PROJECT. Exact physical allocation and delivery sequencing belong to layout and PLAN.

## System acceptance

All conditions below are requirements to verify during implementation; this document records no executed product tests.

| ID | Objective condition | Evidence scope |
| --- | --- | --- |
| A01 | Load shows ready and zero counts; Start or focused-playfield Enter creates a playing game with active/next pieces; one right command moves a legal active piece one column. | Production application in both browsers |
| A02 | ArrowUp and Space rotate clockwise; rotation repeat is ignored; ArrowDown drops one cell when legal. At an obstruction, outcomes match G03–G04. | Native browser input plus deterministic core boundary cases |
| A03 | Pause freezes board, score, and rule time over at least two level-1 intervals; movement input is ignored; Resume continues the preserved partial interval without wall-clock catch-up. | Both browsers plus controlled core/controller time |
| A04 | Restart from playing, paused, and over resets board/counts/level and draws the new pair. Ten restarts leave one command delivery per non-repeated keydown and one scheduling chain. | Browser restart flow plus controller lifecycle checks |
| A05 | Space on a focused button performs native button activation without rotating; game commands outside playfield focus do not alter game state. Tab navigation and focus indicators work. | Both browsers |
| A06 | Board, preview, text, and controls are legible and unclipped at both required viewport sizes/DPR 1; representative DPR 2 output retains the grid and shapes. | Inspected or asserted Canvas output in both browsers |
| A07 | Canonical G01–G06 cases cover spawn, collision, rotation rejection, gravity/locking, 1–4-line clears, progression, pause/over, deterministic draws, and invalid inputs. | Core unit evidence |
| A08 | Resize changes no gameplay state/draws; disposal is idempotent and stops stale callbacks; malformed/decreasing frame timestamps submit no elapsed input; unavailable Canvas/HTML and injected runtime failure show one alert and disable controls. | Controller checks and browser fault/resize checks through composition seams |
| A09 | The built application starts at root and a non-root prefix, makes no external runtime requests, and completes A01–A03 against built assets. | Both browsers; production entry/server path |
| A10 | Type checking/unit/build/browser checks pass, the integrated phase source is published, and package/built/tag/release-note versions match for each phase release. | Build/Git/GitHub evidence |

Both browser targets run the production flow and visual/focus/failure cases attributed to them. Deterministic unit cases run once in the compatible Node environment. Fixture-based browser faults use the same production components; production-entry smoke checks separately establish real application integration.
