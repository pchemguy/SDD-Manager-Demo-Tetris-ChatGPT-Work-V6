# Architecture

This document defines the intended structure of the browser Tetris game described in [PROJECT.md](PROJECT.md), including its classic foundation and modern extensions. Document acceptance and preparation progress are recorded in [DOCS_TASKS.md](DOCS_TASKS.md).

## Technology and rationale

Use TypeScript with strict checking, Canvas 2D for the board, ghost, next-piece and held-piece drawing, and semantic HTML/CSS for controls, instructions, scores, and status. The application runs entirely in the browser and is distributed as static assets.

| Approach considered | Tradeoff and decision |
| --- | --- |
| TypeScript with Canvas and HTML controls | Recommended. Explicit state and command types support rule maintenance; Canvas fits grid and piece drawing, while HTML keeps controls and status accessible to ordinary browser tools. |
| JavaScript with Canvas and HTML controls | Reduces compiler configuration, but provides fewer static checks when changing board, piece, and session state. |
| TypeScript with a DOM cell grid | Makes individual cells directly inspectable, but adds cell elements and CSS layout responsibilities to the view. It remains a viable rendering alternative if cell-level semantics become a requirement. |

Use Vite for development serving and production asset generation. Run the TypeScript compiler separately for type checking because Vite's TypeScript transform does not perform that check. Use Vitest for deterministic unit and controller tests and Playwright for actual browser interactions and rendering checks.

These choices keep the production application limited to game code and browser APIs. Tool versions, the compatible Node runtime, and the dependency lockfile are established during setup before implementation relies on them.

## Major blocks and dependency direction

| Block | Responsibility and state ownership | Dependencies |
| --- | --- | --- |
| Game core | Own board, active/next/held pieces and their variants, bag remainder, hold eligibility, penetrator bonuses, gameplay status, gravity/grounded timers, all six validated settings, score, lines and level. Apply transitions and derive presentation snapshots. Pure configuration values/validation/resolution share this browser-free boundary. | Plain TypeScript data and explicit inputs; no browser APIs. |
| Session controller | Hold current core state, route commands and setting changes, convert browser timestamps into elapsed-time inputs, and coordinate presentation and persistence callbacks. Own lifecycle handles, the clock anchor and nonfatal save feedback. | Game core and narrow callbacks for browser services and presentation. |
| Browser adapters | Translate keyboard/button/setting events and animation timestamps into controller inputs. Supply production random samples, read/write setting cookies and URL parameters, and attach/detach bindings under the controller's lifecycle ownership. | Browser APIs and the controller's command boundary. |
| Presentation | Draw board/ghost/preview snapshots with piece variants, update HTML metrics/status, bonus symbols, all six configuration controls and save feedback, and size drawing surfaces for the desktop layout. Own visual styling and drawing resources. | Read-only core snapshots, Canvas 2D, and the DOM. |

Compose these blocks through direct function calls and narrow callbacks. Browser adapters and presentation depend on the core's public data contracts; the core has no dependency on either. Detailed component boundaries and interface shapes belong in DECOMPOSITION.

## State and execution flow

Browser startup resolves HTML elements and drawing contexts, creates the production random source, resolves per-setting startup configuration through the browser adapter and pure configuration model, initializes the core with those settings, and connects the controller to input, timing, persistence and presentation. Resolve settings before the first snapshot; no default-state flash or later reload transition is required.

Input adapters submit game commands or a typed change to one setting. The controller passes commands and explicit elapsed time into the core, stores the resulting state, and presents its snapshot. The core owns collision, kicked rotation, hold, bag consumption, penetrator activation/traversal/bonus accounting, hard drop, both general locking policies, line removal, scoring, progression and game over. Presentation does not reproduce those decisions.

Random samples enter through an injected numeric source. The core owns seven-bag shuffling and consumption; production supplies JavaScript randomness and tests supply fixed or seeded samples. Keeping the bag in core state makes restart, hold-related draws, and replay deterministic without hidden adapter state. An external stateful piece generator would weaken that ownership and is unnecessary. The core reads neither ambient randomness nor wall-clock time.

The controller owns the browser clock anchor and animation-loop lifecycle; the core owns gravity progress and grounded-lock elapsed time. Pause, resume, and restart coordinate these owners so browser scheduling does not become a second source of gameplay state. The single frame loop advances both timers; browser timeouts do not independently lock pieces. SPEC defines precise event ordering and timing policies.

The board's logical coordinates remain independent of CSS size and device-pixel scaling. Presentation sizes its drawing buffer and cell geometry from the viewport and draws from the same logical snapshot.

## Placement and policy ownership

One core landing projection, parameterized by the piece variant, supplies both ghost geometry and the hard-drop destination. Regular pieces use the collision-limited descent; penetrators search through intervening occupied cells for the lowest valid nonoverlapping placement. Board operations keep inside-bounds, transient traversal and final-placement checks distinct. The engine permits traversal without changing locked cells and guarantees every active penetrator can still reach a valid final placement. It derives a position without changing board, timers, bag, or hold state. The renderer consumes that result, so visual guidance and executed placement cannot use different collision algorithms.

Rotation geometry and ordered kick candidates are core rule data. Hold eligibility and held-piece replacement are core transitions, including spawn collision and random-consumption rules. Components remain ordinary functions and immutable data; these features do not require a plugin framework or a class per piece.

The default grounded interval, failed-downward-attempt alternative and hard-drop override belong to core policy. Feature-off choices are enforced by the core as well as reflected in controls; hiding a preview does not change piece selection or hold storage. Conditional ghost visibility is a snapshot decision based on the shared landing, not a second placement algorithm in the view. Bonus earning/spending and active/held penetrator identity belong to the engine; the renderer uses explicit variants to render white penetrators while keeping regular colors distinct.

## Configuration boundary

A pure configuration model owns setting identities, allowed values, defaults and independent URL/cookie/default resolution. The core retains the resulting typed settings and validates setting-update inputs; it never reads a document, URL, cookie or ambient location.

A browser configuration adapter owns extracting URL/cookie strings and persisting individual values. Composition uses it at startup; the controller invokes it after accepting a player setting change. Explicit valid URL values are saved to their cookies, including defaults. Missing URL values retain cookies. Setting changes update that setting's cookie and URL parameter without changing gameplay history, resetting the game or erasing unrelated URL data. SPEC owns exact names, encoding, precedence, invalid-value behavior and live policy transitions.

Cookie and URL write/read failures return bounded save feedback rather than throwing into the gameplay failure path. The controller retains that feedback separately from core state and supplies a presentation frame containing the game snapshot and save status. One failed destination does not suppress the other save attempt; selected in-memory settings remain usable. This adds a small browser adapter and pure value module, not a second state manager, backend or generic settings service. No gameplay board, score or bonus state is persisted.

## System invariants and failure boundaries

- Given the same initial state, commands, elapsed time, policy choices, and random samples, the core produces the same result.
- A rendering pass consumes a presentation frame without advancing gameplay, selecting a piece, or modifying the board.
- One controller owns each game's current state, event registrations, and animation-loop lifecycle.
- Game over is an ordinary core state. The browser composition boundary owns startup failures, including an unavailable drawing context, and presents a readable failure state.
- Settings have one validated in-memory owner; persistence mirrors selected values and does not become a second gameplay state source.
- Controls, instructions, score, bonuses and status use semantic HTML. Canvas pixels are not the authoritative source of textual state.

## Verification and delivery

| Boundary | Verification approach |
| --- | --- |
| Game core | Vitest exercises geometry/kicks, bags and hold consumption, regular/penetrator landing and traversal, bonus/hold identity, both general/drop lock policies, setting validation and chronological gravity/lock contracts using controlled state, elapsed time, and random samples. |
| Session controller | Tests supply a controlled clock and callbacks to check command/setting routing, elapsed ordering, independent save attempts/feedback, pause/restart and lifecycle coordination. |
| Configuration | Pure resolution/validation cases plus browser cookies, URL/history updates, reload/reopen, invalid/omitted/default values and independent adapter failures. |
| Browser integration | Playwright drives the served application with real keyboard/button input and checks DOM state plus Canvas output. |
| Production build | Explicit TypeScript checking, Vite build, and browser checks against the generated static application. |

Apply [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) for browser selection, isolated provisioning, headless modes, diagnostics, and recovery. Chromium is primary and Playwright-managed Firefox is secondary. Representative provisioning and execution must be established before browser-dependent implementation relies on those facilities. Document-only planning establishes no browser capability evidence.

Test fixtures use the core's input boundaries and the application's composition seams. The production bundle excludes test fixtures and runner dependencies. Browser verification must exercise the application's real entry and server path; core tests and synthetic provisioning probes supply separate evidence.

GitHub provides source hosting, tracking, CI, and release publication. Gameplay has no service dependency. Build and test commands must be repeatable from the committed toolchain and lockfile.

Package metadata supplies the authoritative application version. Built version information, release tag, and release notes must agree. Phase completion, verification, integration, and release publication follow the accepted PROJECT policy.

## Subsequent design boundaries

DECOMPOSITION defines cohesive components, ownership details, and interfaces for commands, settings/resolution/persistence, presentation frames, random supply, timing, piece variants/bonuses and rendering. SPEC owns exact gameplay and failure contracts. PLAN owns delivery order and phase exits, and layout owns physical paths. This architecture requires no focused child documents.

## Technical sources

- [TypeScript strict checking](https://www.typescriptlang.org/tsconfig/strict.html)
- [Vite TypeScript support and separate type checking](https://vite.dev/guide/features#typescript)
- [Canvas API and semantic-content considerations](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- [Vitest guide](https://vitest.dev/guide/)
