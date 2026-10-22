# System specification

The application is a single-player browser Tetris game for desktop keyboard play, with the classic foundation and modern extensions in [PROJECT.md](PROJECT.md). [ARCHITECTURE.md](ARCHITECTURE.md) and [DECOMPOSITION.md](DECOMPOSITION.md) define the responsible structural units.

[spec/GAMEPLAY.md](spec/GAMEPLAY.md) canonically defines board/piece data, core states, spawning, kicked rotation, gravity/grounded locking, scoring, bags, hold, ghost/hard-drop placement, penetrator traversal/bonuses, and core rejection behavior. [spec/CONFIGURATION.md](spec/CONFIGURATION.md) canonically defines six setting values/defaults, live effects and independent cookie/URL persistence. This root owns browser/session behavior, presentation, snapshots, runtime failures, distribution, and system acceptance. It specifies the complete target; PLAN identifies each delivery phase's supported subset and exit evidence.

## R01 — Session and controls

The page opens in the gameplay contract's `ready` state, with an empty board and zero score/lines at level 1. The playfield is a named, keyboard-focusable region and receives initial focus after successful startup.

| Visible control | Availability | Action |
| --- | --- | --- |
| Start | Enabled in ready; disabled otherwise | Start a game and focus the playfield. |
| Pause / Resume | Enabled in playing/paused; disabled otherwise | Pause when playing; resume when paused. Button activation resumes focus on the playfield only for Resume. |
| Restart | Enabled in playing/paused/over; disabled in ready | Start a fresh game and focus the playfield. |
| Hold | Enabled only in playing when `canHold` is true, including hold=on | Apply G08 and focus the playfield. |
| Hard drop | Enabled only in playing with hardDrop=on | Apply G09 and focus the playfield. |
| Activate penetrator | Enabled in playing when `canActivatePenetrator` is true | Apply G10 and focus the playfield. |
| Game configuration | All six controls enabled in every ordinary game state | Apply CONFIGURATION C01–C05 without moving focus or restarting. |

The displayed status is Ready, Playing, Paused, or Game over. Configuration resolves before this first visible state; Start/Restart preserve it and reload/reopen resolves it again into a fresh ready game. A paused board/active piece remains visible with ghost/previews filtered by selected settings. Game over retains the final locked board and enabled next/held previews, without active or ghost cells. G10 owns bonus state and CONFIGURATION owns save feedback; neither game progress nor bonuses are persisted.

## R02 — Keyboard and focus

Game key bindings operate only while the playfield has keyboard focus. Buttons retain native keyboard activation. Clicking the playfield focuses it.

| Keyboard code | Action | Repeated keydown |
| --- | --- | --- |
| ArrowLeft / ArrowRight | Move one column left/right while playing | Accepted |
| ArrowDown | One soft-drop attempt while playing | Accepted |
| ArrowUp / Space | One clockwise rotation while playing | Ignored |
| KeyC | Hold while playing and eligible | Ignored |
| KeyD | Hard drop while playing and enabled | Ignored |
| KeyX | Activate penetrator while playing and available | Ignored |
| KeyP / Escape | Pause playing or resume paused | Ignored |
| KeyR | Restart playing, paused, or over | Ignored |
| Enter | Start ready or restart over | Ignored |

Recognized playfield keys prevent their browser default, including when their game action is ignored by the current state. Tab and unrecognized keys keep normal behavior. The game-key binding ignores events outside playfield focus; native button activation and checkbox operation still perform R01 actions. Movement, rotation, hold, drop and penetrator input in ready, paused, or over is ignored without consuming randomness or bonuses. Space remains clockwise rotation on the playfield; hard drop uses D.

Movement repeat follows browser-delivered keydown events; each accepted event produces one command. There is no separate held-key movement timer. Pause is explicit: losing playfield focus stops game-key handling but does not change the playing status. Visibility changes do not automatically resume or pause a game.

## R03 — Browser time and resource lifecycle

The controller uses injected `now()` samples for elapsed gameplay time, on both frame callbacks and command/setting callbacks. Samples use the same monotonic origin as the browser frame source and must be numeric, finite, and nondecreasing across samples. Delivered frame timestamps must independently be numeric, finite, and nondecreasing across frames in the mounted session. Validate a frame timestamp before sampling its clock or advancing time. Do not compare an earlier scheduled frame timestamp with an intervening input's `now()` sample; they are separate ordered streams.

While playing, each callback first advances the core by `min(250, max(0, now - anchor))` and updates the anchor to that sample. It then applies the callback's command or setting update to the resulting state, in callback arrival order. Thus an expired grounded deadline locks before the command acts; the command may address a newly spawned piece. Frames have no subsequent command. An accepted setting update is followed by CONFIGURATION C03–C05 save attempts; save status does not enter core state. A malformed/decreasing clock or frame value takes R05's failure path before elapsed submission or anchor replacement.

Start, restart, and resume establish the playing anchor at their command's sampled time; that action contributes no additional elapsed time after its transition. Ready/paused/over callbacks advance zero core time. Pause preserves gravity and grounded accumulators; Resume establishes a fresh anchor without catch-up. Restart while playing follows the same advance-before-command ordering, then resets gameplay. Browser gaps contribute at most 250 ms per callback; core G04 accepts uncapped elapsed inputs. No browser timeout separately locks a piece.

Each mounted session owns at most one input binding set and one animation-frame scheduling chain. Restart replaces game state within that lifecycle. Resize redraws the last snapshot using new presentation dimensions and leaves core state and piece draws unchanged.

Disposal releases the session's event/scheduling handles, is safe to call twice, and makes later commands or queued callbacks inert. No disposed session may mutate state or update the view.

## R04 — Visible output and usability

The board shows 10 columns and 20 rows, grid boundaries, locked cells, a distinguishable outlined or translucent visible ghost, and the active piece. Draw locked cells, then ghost, then active cells. Penetrator overlap during travel may cover occupied pixels temporarily; the locked board data and its later appearance remain intact. Draw the ghost behind the active piece so coincident cells do not obscure it. Paused visibility follows the same settings.

Regular piece colors are I cyan (#00BCD4), J blue (#2962FF), L orange (#FF9800), O yellow (#FDD835), S green (#43A047), T purple (#AB47BC), and Z red (#E53935). Every penetrator active/held/locked cell and visible ghost uses white (#FFFFFF); a ghost may use white outlines or alpha to remain distinguishable. Regular pieces never use white. Next and held previews use initial-orientation geometry and their variant; an enabled empty hold slot says Empty. CONFIGURATION C03 governs hidden preview geometry/text. Score, Lines, Level, status, enabled Next/Held kind, availability, controls and keyboard instructions use HTML text.

Show available bonuses in the side panel as exactly one lightning symbol (⚡) per bonus, zero through three. Also expose the accessible text Penetrator bonuses: N of 3; do not rely on symbols/color alone. Update symbols, count and activation availability in the resulting frame when a bonus is earned or spent. Instructions include X for penetrator and explain that four-line clears earn bonuses.

A handled input's resulting presentation frame appears by the next rendered frame. Canvas and HTML use the same game snapshot and controller save status. Ghost, preview visibility, variants, bonuses, all six controls, metrics and status agree with state. Rendering cannot advance time, consume randomness/bonuses or calculate a different landing.

At 100% browser zoom, 1280×800 and 800×600 viewports must show the entire board, enabled previews, metrics, instructions, bonus indicator, game controls and all six configuration controls without clipping or page scrolling. Drawing-buffer scaling preserves the grid at device pixel ratios 1 and 2. Controls expose accessible names, tab navigation and visible focus; CONFIGURATION C01 supplies the six setting names. An ordinary settings save notice must also fit without covering the board or controls.

## R05 — Cross-component data and failures

The controller retains core state and applies canonical gameplay inputs. The engine alone changes board, active/next/held pieces, bag remainder, hold eligibility, rule timers, settings, bonuses, metrics, and status. Caller-supplied elapsed time and numeric random samples make results reproducible.

A presentation snapshot contains:

- `status`, `score`, `lines`, and `level` with the gameplay values;
- the row-major board of locked cells;
- `activeCells`: occupied absolute board coordinates with piece kind/variant, sorted by row then column;
- `ghostCells`: the G09 landing cells in the same absolute/sorted format;
- `nextKind`, `nextVariant` and `previewCells`: initial-orientation cells normalized to minimum local x/y zero, sorted by row then column;
- `heldKind`, `heldVariant` and `heldPreviewCells`: null/empty when no held piece, otherwise normalized/sorted initial geometry;
- `canHold`: true exactly when playing, hold is on and G08 permits hold;
- complete `settings` with C01 values and derived `nextVisible`/`holdVisible` flags;
- `bonuses` in 0–3, `activeVariant`, and `canActivatePenetrator`: true exactly when playing with a regular active piece and bonuses > 0.

`activeVariant` is null in ready/over and otherwise matches active cells. `nextVisible` and `holdVisible` equal their setting's enabled value; an empty enabled preview still has no cells. The snapshot and earlier state values remain unchanged by later transitions or rendering. Ready has null next/held kinds/variants and empty cell arrays. Paused retains active geometry and setting-filtered ghost/preview geometry, but both activation and hold availability are false. Over has empty active/ghost arrays and retains the failed-spawn candidate as next preview and the final held kind/variant. When a hold spawn failed, next kind/variant describes that failed candidate, including a penetrator. Hidden next/held previews have empty cell arrays but keep internal kind/variant data; views display Off instead of that text. Projection consumes no randomness and changes no bag, timers, eligibility, settings or bonuses.

The controller presents `{game: snapshot, saveStatus}`; saveStatus is clear or a C05 nonfatal persistence notice. Browser save errors never alter core state. A visible ghost has the active variant and G09 geometry; next/held preview cells also carry their variant.

The engine rejects invalid inputs as specified in GAMEPLAY G06 before publishing a new state. Normal collisions and game over use ordinary outcomes. If a source, engine, or renderer throws during browser operation, the controller disposes its bindings/scheduling and the browser boundary displays an HTML alert stating that the game cannot run and the page should be reloaded. All game and configuration controls are disabled. Repeated callbacks must not produce repeated alerts or restart the loop.

Persistence read/write failures use CONFIGURATION C05 instead of this fatal path. Missing required HTML elements or a Canvas 2D context produce the same readable startup failure and cleanup of acquired resources. The alert must remain visible even if the normal status element is unavailable. Reload is the recovery path. Raw stack traces and tool configuration are not part of the game interface.

## R06 — Build, browser targets, and releases

The production application consists of locally served static HTML, CSS, and JavaScript assets, with no runtime backend, CDN, external font, or account dependency. It works from an HTTP server at the host root and below a non-root URL prefix. Direct `file://` loading is outside the supported launch contract.

TypeScript checking, unit tests, production build, and browser checks have documented repeatable commands using the committed compatible toolchain and lockfile. Production output excludes fixtures and runner dependencies.

[BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) governs selection, isolation, provisioning, diagnostics, and recovery. Chromium is primary and Playwright-managed Firefox is secondary; default acceptance uses their headless modes. The policy's required capability evidence precedes dependent browser work.

Package metadata is the authoritative version; built version information, the `v<version>` Git tag, and release notes agree. The first playable phase release is 0.1.0. Later minor/patch increments reflect delivered functionality/fixes. Each implementation phase release requires completed phase work, verification, integration into the published default branch, corresponding tag, and GitHub release notes describing delivered changes and validation.

## R07 — Configuration entry

Apply [CONFIGURATION C01–C06](spec/CONFIGURATION.md) for all six side-panel controls, startup URL/cookie/default resolution, explicit URL-value cookie saves, player change persistence, restart/reload behavior and nonfatal save failures. Cookie omission is not a request to reset a setting. Resolve before core initialization and route player changes through the timed controller boundary; no view reads or writes persistence independently.

## Structural traceability

| Contract group | Responsible components from DECOMPOSITION |
| --- | --- |
| GAMEPLAY G01–G10 | Core model/geometry, board and bag operations, rules engine, and random source |
| R01–R03 | Rules engine, session controller, input bindings, clock/frame source, and composition root |
| R04–R05 | Snapshot projection, board/preview renderer, status/control view, controller, and composition root |
| R07 / CONFIGURATION C01–C06 | Pure configuration model, rules engine, browser persistence adapter, session controller, control view and composition root |
| R06 | Browser entry, build/test composition, and release orchestration |

These contracts cover PROJECT's complete modern scope while preserving its exclusions, including saved games and persistent scores; the six setting values are persisted as specified. Exact physical allocation and delivery sequencing belong to layout and PLAN.

## System acceptance

All conditions below are requirements to verify during implementation; this document records no executed product tests.

| ID | Objective condition | Evidence scope |
| --- | --- | --- |
| A01 | Load shows ready and zero counts; Start or focused-playfield Enter creates a playing game with active/next pieces; one right command moves a legal active piece one column. | Production application in both browsers |
| A02 | ArrowUp and Space rotate clockwise; rotation repeat is ignored; ArrowDown drops one cell when legal. At an obstruction, outcomes match G03–G04. | Native browser input plus deterministic core boundary cases |
| A03 | Pause freezes board, score, gravity and grounded time over at least two level-1 intervals; gameplay input is ignored; Resume continues both partial intervals without catch-up. | Both browsers plus controlled core/controller time |
| A04 | Restart from playing, paused, and over resets board/counts/level, hold and bag, retaining all six settings. Ten restarts leave one command delivery per physical one-shot key and one scheduling chain. | Browser restart flow plus controller lifecycle checks |
| A05 | Space on a focused button/checkbox performs native operation without rotation or penetrator activation; raw mapped keys outside playfield focus are ignored. Tab navigation/focus work. | Both browsers |
| A06 | Board/visible ghost, enabled previews, text, bonuses and all six controls fit both required viewports/DPR 1; representative DPR 2 output retains the grid and shapes. | Inspected or asserted Canvas output in both browsers |
| A07 | Canonical G01–G10/K01–K17 cases cover core behavior, boundary/error cases, random consumption and state immutability. | Core unit evidence |
| A08 | Resize changes no state/randomness; disposal stops stale callbacks; malformed/decreasing frame or sampled clock values submit no elapsed input; interleaved valid streams are accepted; missing Canvas/HTML and injected failures show one alert and disable controls. | Controlled controller checks plus browser fault/resize cases through composition seams |
| A09 | The built application starts at root and a non-root prefix, makes no external runtime requests, and completes A01–A03 against built assets. | Both browsers; production entry/server path |
| A10 | Type checking/unit/build/browser checks pass, the integrated phase source is published, and package/built/tag/release-note versions match for each phase release. | Build/Git/GitHub evidence |
| A11 | C and Hold store an empty slot or swap a held kind, with canonical respawn and one hold per lock cycle with variant retention. Disabled/paused/used hold is rejected without consuming samples; blocked replacement ends the game. | Both browsers plus controlled core hold/collision cases |
| A12 | The visible ghost updates after movement, kicked rotation, hold and gravity; with hard drop enabled and follow/interval selected, its cells equal the resulting dropped active cells on the same state. It remains view-only and never obscures the active piece. | Both browsers plus deterministic core/projection checks |
| A13 | Within a round, each complete group of seven consumed queue kinds is a shuffled permutation, refills use G07's sample count, and hold/swap follow their exact queue-consumption rules. | Controlled bag/core evidence plus browser next/hold flow |
| A14 | Rotation selects the first admissible candidate from G03, including wall/floor cases; all-blocked rotation preserves placement and rule timers. Up/Space retain clockwise-only control. | Core candidate cases and representative native-browser fixtures in both browsers |
| A15 | With interval policy, a grounded piece remains active until exactly its full interval, including after blocked soft drop or zero-distance follow hard drop. Grounded moves preserve elapsed delay; airborne movement cancels it; regrounding starts a new interval; pause freezes it. | Controlled core/controller boundaries and representative play in both browsers |
| A16 | D/Hard drop, when enabled, moves to the landing and obeys the selected general policy or immediate override, including distance zero. Repeated C/D/X keydowns are ignored. Changing settings does not itself lock/reset timers; restart retains them, and reload resolves C02 precedence. | Both browsers plus exact core/controller policy cases |
| A17 | Dynamic ghost hides when its smallest landing y is 9, including a midline straddle, and shows at y=10. Movement/rotation/activation can hide/show it again. Always hide/show work while playing/paused and remain independent of hard-drop enablement. | Both browsers with controlled landing fixtures plus core/projection cases |
| A18 | X/Activate penetrator spends one available bonus, turns the current piece white, permits nondestructive overlap in travel and reaches the lowest valid landing through blockers. A visible ghost matches that hard drop; hopeless/boundary proposals are rejected and every lock is in bounds without overlap. | Both browsers plus controlled core/traversal/landing cases |
| A19 | A four-line clear awards one bonus up to three; other clears award none. The side panel shows exactly N symbols and accessible N of 3; spend/earn updates immediately. Active, held, ghost and locked penetrator appearance is white and regular palette excludes white. Hold preserves ability until lock; restart clears it and bonuses. | Both browsers with inspected/asserted Canvas and indicator output plus core cases |
| A20 | All six native controls apply their stated effects in ready/playing/paused/over. Hold/drop toggles gate hotkeys/buttons/core commands; preview toggles retain queue/held state. Both general locks and both hard-drop policies agree, including live changes, contact age, failed attempts and zero-distance drops. | Both browsers plus deterministic core/controller ordering |
| A21 | For every setting, URL beats cookie beats default independently; omitted parameters retain saved cookies, explicit defaults overwrite them, and invalid/mixed-source values follow C02. Each player change writes its cookie and query value; reload and a new page without query restore saved choices. Other query data/path/hash/history state remain intact without navigation. | Pure resolution cases and real cookies/history/reload/reopen in both browsers at root/prefix |
| A22 | Cookie/URL read/write/refusal faults are independent and nonfatal: settings remain active, viable saves occur, feedback is accessible and later success clears it. Startup saves only valid explicit URL values; normal gameplay/render/disposal performs no save. Cookie lifetime/path/HTTPS attributes match C04. | Deterministic adapter/controller faults plus both-browser persistence/feedback cases |

Both browser targets run the production flow and visual/focus/failure cases attributed to them. Deterministic unit cases run once in the compatible Node environment. Fixture-based browser faults use the same production components; production-entry smoke checks separately establish real application integration.
