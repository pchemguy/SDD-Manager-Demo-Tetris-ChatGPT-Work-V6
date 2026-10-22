# System specification

The application is a single-player browser Tetris game for desktop keyboard play, with the classic foundation and modern extensions in [PROJECT.md](PROJECT.md). [ARCHITECTURE.md](ARCHITECTURE.md) and [DECOMPOSITION.md](DECOMPOSITION.md) define the responsible structural units.

[spec/GAMEPLAY.md](spec/GAMEPLAY.md) canonically defines board/piece data, core states, spawning, kicked rotation, gravity/grounded locking, scoring, bags, hold, ghost/hard-drop placement, and core rejection behavior. This root owns browser/session behavior, presentation, snapshots, runtime failures, distribution, and system acceptance. It specifies the complete target; PLAN identifies each delivery phase's supported subset and exit evidence.

## R01 — Session and controls

The page opens in the gameplay contract's `ready` state, with an empty board and zero score/lines at level 1. The playfield is a named, keyboard-focusable region and receives initial focus after successful startup.

| Visible control | Availability | Action |
| --- | --- | --- |
| Start | Enabled in ready; disabled otherwise | Start a game and focus the playfield. |
| Pause / Resume | Enabled in playing/paused; disabled otherwise | Pause when playing; resume when paused. Button activation resumes focus on the playfield only for Resume. |
| Restart | Enabled in playing/paused/over; disabled in ready | Start a fresh game and focus the playfield. |
| Hold | Enabled only in playing when `canHold` is true | Apply G08 and focus the playfield. |
| Hard drop | Enabled only in playing | Apply G09 and focus the playfield. |
| Lock immediately after hard drop | Checkbox enabled in every ordinary game state | Select `immediate` when checked and `gravity` when unchecked. Update core policy without moving focus or triggering a drop. |

The displayed status is Ready, Playing, Paused, or Game over. The hard-drop policy initially is `gravity`; restart preserves the selected policy within the mounted session, and reload restores ready/default policy. No game or setting persistence is required. A policy change affects future hard-drop commands only: checking it does not lock a currently grounded piece or change its elapsed lock delay. A paused board, ghost, next, and held preview remain visible. Game over retains the final locked board, next preview, and held piece, with no active or ghost cells.

## R02 — Keyboard and focus

Game key bindings operate only while the playfield has keyboard focus. Buttons retain native keyboard activation. Clicking the playfield focuses it.

| Keyboard code | Action | Repeated keydown |
| --- | --- | --- |
| ArrowLeft / ArrowRight | Move one column left/right while playing | Accepted |
| ArrowDown | One soft-drop attempt while playing | Accepted |
| ArrowUp / Space | One clockwise rotation while playing | Ignored |
| KeyC | Hold while playing and eligible | Ignored |
| KeyD | Hard drop while playing | Ignored |
| KeyP / Escape | Pause playing or resume paused | Ignored |
| KeyR | Restart playing, paused, or over | Ignored |
| Enter | Start ready or restart over | Ignored |

Recognized playfield keys prevent their browser default, including when their game action is ignored by the current state. Tab and unrecognized keys keep normal behavior. The game-key binding ignores events outside playfield focus; native button activation and checkbox operation still perform R01 actions. Movement, rotation, hold, and drop input in ready, paused, or over is ignored without consuming randomness. Space remains clockwise rotation on the playfield; hard drop uses D.

Movement repeat follows browser-delivered keydown events; each accepted event produces one command. There is no separate held-key movement timer. Pause is explicit: losing playfield focus stops game-key handling but does not change the playing status. Visibility changes do not automatically resume or pause a game.

## R03 — Browser time and resource lifecycle

The controller uses injected `now()` samples for elapsed gameplay time, on both frame callbacks and command/policy callbacks. Samples use the same monotonic origin as the browser frame source and must be numeric, finite, and nondecreasing across samples. Delivered frame timestamps must independently be numeric, finite, and nondecreasing across frames in the mounted session. Validate a frame timestamp before sampling its clock or advancing time. Do not compare an earlier scheduled frame timestamp with an intervening input's `now()` sample; they are separate ordered streams.

While playing, each callback first advances the core by `min(250, max(0, now - anchor))` and updates the anchor to that sample. It then applies the callback's command or policy update to the resulting state, in callback arrival order. Thus an expired grounded deadline locks before the command acts; the command may address a newly spawned piece. Frames have no subsequent command. A malformed/decreasing clock or frame value takes R05's failure path before elapsed submission or anchor replacement.

Start, restart, and resume establish the playing anchor at their command's sampled time; that action contributes no additional elapsed time after its transition. Ready/paused/over callbacks advance zero core time. Pause preserves gravity and grounded accumulators; Resume establishes a fresh anchor without catch-up. Restart while playing follows the same advance-before-command ordering, then resets gameplay. Browser gaps contribute at most 250 ms per callback; core G04 accepts uncapped elapsed inputs. No browser timeout separately locks a piece.

Each mounted session owns at most one input binding set and one animation-frame scheduling chain. Restart replaces game state within that lifecycle. Resize redraws the last snapshot using new presentation dimensions and leaves core state and piece draws unchanged.

Disposal releases the session's event/scheduling handles, is safe to call twice, and makes later commands or queued callbacks inert. No disposed session may mutate state or update the view.

## R04 — Visible output and usability

The board shows 10 columns and 20 rows, grid boundaries, locked cells, a distinguishable outlined or translucent ghost, and the active piece. Draw the ghost behind the active piece so coincident cells do not obscure it. Ghost cells follow G09 and remain visible when paused. Each piece kind has a stable color within a build. Next and Hold previews use initial-orientation geometry; an empty hold slot says Empty. Score, Lines, Level, Next kind, Held kind/Empty, hold availability, status, controls, and keyboard instructions use HTML text.

A handled input's resulting snapshot appears by the next rendered frame. Canvas and HTML derive from the same snapshot. Ghost, both previews, hold availability, policy checkbox, metrics, and status agree with core state. Rendering cannot advance time, consume randomness, or calculate a different landing position.

At 100% browser zoom, 1280×800 and 800×600 viewports must show the entire board, both previews, metrics, instructions, controls, and policy choice without clipping or page scrolling. Drawing-buffer scaling preserves the grid at device pixel ratios 1 and 2. Controls expose accessible names, tab navigation, and visible focus. The checkbox's accessible name is Lock immediately after hard drop.

## R05 — Cross-component data and failures

The controller retains core state and applies canonical gameplay inputs. The engine alone changes board, active/next/held pieces, bag remainder, hold eligibility, rule timers, policy, metrics, and status. Caller-supplied elapsed time and numeric random samples make results reproducible.

A presentation snapshot contains:

- `status`, `score`, `lines`, and `level` with the gameplay values;
- the row-major board of locked cells;
- `activeCells`: occupied absolute board coordinates with piece kind, sorted by row then column;
- `ghostCells`: the G09 landing cells in the same absolute/sorted format;
- `nextKind` and `previewCells`: initial-orientation cells normalized to minimum local x/y zero, sorted by row then column;
- `heldKind` and `heldPreviewCells`: null/empty when no held piece, otherwise normalized/sorted initial geometry;
- `canHold`: true exactly when playing and G08 permits hold; and `hardDropPolicy`: `gravity` or `immediate`.

The snapshot and earlier state values remain unchanged by later transitions or rendering. Ready has null next/held kinds and empty cell arrays. Paused retains active/ghost/preview geometry but `canHold` is false. Over has empty active/ghost arrays and retains the failed-spawn candidate as next preview and the final held kind. Projection consumes no randomness and changes no bag, timers, eligibility, or policy.

The engine rejects invalid inputs as specified in GAMEPLAY G06 before publishing a new state. Normal collisions and game over use ordinary outcomes. If a source, engine, or renderer throws during browser operation, the controller disposes its bindings/scheduling and the browser boundary displays an HTML alert stating that the game cannot run and the page should be reloaded. All game controls and the policy choice are disabled. Repeated callbacks must not produce repeated alerts or restart the loop.

Missing required HTML elements or a Canvas 2D context produce the same readable startup failure and cleanup of acquired resources. The alert must remain visible even if the normal status element is unavailable. Reload is the recovery path. Raw stack traces and tool configuration are not part of the game interface.

## R06 — Build, browser targets, and releases

The production application consists of locally served static HTML, CSS, and JavaScript assets, with no runtime backend, CDN, external font, or account dependency. It works from an HTTP server at the host root and below a non-root URL prefix. Direct `file://` loading is outside the supported launch contract.

TypeScript checking, unit tests, production build, and browser checks have documented repeatable commands using the committed compatible toolchain and lockfile. Production output excludes fixtures and runner dependencies.

[BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) governs selection, isolation, provisioning, diagnostics, and recovery. Chromium is primary and Playwright-managed Firefox is secondary; default acceptance uses their headless modes. The policy's required capability evidence precedes dependent browser work.

Package metadata is the authoritative version; built version information, the `v<version>` Git tag, and release notes agree. The first playable phase release is 0.1.0. Later minor/patch increments reflect delivered functionality/fixes. Each implementation phase release requires completed phase work, verification, integration into the published default branch, corresponding tag, and GitHub release notes describing delivered changes and validation.

## Structural traceability

| Contract group | Responsible components from DECOMPOSITION |
| --- | --- |
| GAMEPLAY G01–G09 | Core model/geometry, board and bag operations, rules engine, and random source |
| R01–R03 | Rules engine, session controller, input bindings, clock/frame source, and composition root |
| R04–R05 | Snapshot projection, board/preview renderer, status/control view, controller, and composition root |
| R06 | Browser entry, build/test composition, and release orchestration |

These contracts cover PROJECT's complete modern scope while preserving its exclusions, including settings persistence. Exact physical allocation and delivery sequencing belong to layout and PLAN.

## System acceptance

All conditions below are requirements to verify during implementation; this document records no executed product tests.

| ID | Objective condition | Evidence scope |
| --- | --- | --- |
| A01 | Load shows ready and zero counts; Start or focused-playfield Enter creates a playing game with active/next pieces; one right command moves a legal active piece one column. | Production application in both browsers |
| A02 | ArrowUp and Space rotate clockwise; rotation repeat is ignored; ArrowDown drops one cell when legal. At an obstruction, outcomes match G03–G04. | Native browser input plus deterministic core boundary cases |
| A03 | Pause freezes board, score, gravity and grounded time over at least two level-1 intervals; gameplay input is ignored; Resume continues both partial intervals without catch-up. | Both browsers plus controlled core/controller time |
| A04 | Restart from playing, paused, and over resets board/counts/level, hold and bag, retaining selected hard-drop policy. Ten restarts leave one command delivery per physical one-shot key and one scheduling chain. | Browser restart flow plus controller lifecycle checks |
| A05 | Space on a focused button/checkbox performs native operation without rotation; raw mapped keys outside playfield focus are ignored. Tab navigation/focus work. | Both browsers |
| A06 | Board/ghost, both previews, text, controls and policy choice fit both required viewports/DPR 1; representative DPR 2 output retains the grid and shapes. | Inspected or asserted Canvas output in both browsers |
| A07 | Canonical G01–G09/K01–K12 cases cover core behavior, boundary/error cases, random consumption and state immutability. | Core unit evidence |
| A08 | Resize changes no state/randomness; disposal stops stale callbacks; malformed/decreasing frame or sampled clock values submit no elapsed input; interleaved valid streams are accepted; missing Canvas/HTML and injected failures show one alert and disable controls. | Controlled controller checks plus browser fault/resize cases through composition seams |
| A09 | The built application starts at root and a non-root prefix, makes no external runtime requests, and completes A01–A03 against built assets. | Both browsers; production entry/server path |
| A10 | Type checking/unit/build/browser checks pass, the integrated phase source is published, and package/built/tag/release-note versions match for each phase release. | Build/Git/GitHub evidence |
| A11 | C and Hold store an empty slot or swap a held kind, with canonical respawn and one hold per lock cycle. Paused/used hold is rejected without consuming samples; blocked replacement ends the game. | Both browsers plus controlled core hold/collision cases |
| A12 | The visible ghost updates after movement, kicked rotation, hold and gravity; its cells equal a delayed hard drop's resulting active cells on the same state. It remains view-only and never obscures the active piece. | Both browsers plus deterministic core/projection checks |
| A13 | Within a round, each complete group of seven consumed queue kinds is a shuffled permutation, refills use G07's sample count, and hold/swap follow their exact queue-consumption rules. | Controlled bag/core evidence plus browser next/hold flow |
| A14 | Rotation selects the first legal candidate from G03, including wall/floor cases; all-blocked rotation preserves placement and rule timers. Up/Space retain clockwise-only control. | Core candidate cases and representative native-browser fixtures in both browsers |
| A15 | A grounded piece remains active until exactly its full interval, including after blocked soft drop or zero-distance delayed hard drop. Grounded moves preserve elapsed delay; airborne movement cancels it; regrounding starts a new interval; pause freezes it. | Controlled core/controller boundaries and representative play in both browsers |
| A16 | D/Hard drop moves to the landing and obeys unchecked delay or checked immediate locking, including distance zero. Repeated C/D keydowns are ignored. Changing policy does not itself lock/reset timers; restart retains policy, reload restores unchecked. | Both browsers plus exact core/controller policy cases |

Both browser targets run the production flow and visual/focus/failure cases attributed to them. Deterministic unit cases run once in the compatible Node environment. Fixture-based browser faults use the same production components; production-entry smoke checks separately establish real application integration.
