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
| Game core | Own board, active/next/held pieces, bag remainder, hold eligibility, gameplay status, gravity and grounded-lock timers, hard-drop policy, score, lines, and level. Apply transitions and derive presentation snapshots. | Plain TypeScript data and explicit inputs; no browser APIs. |
| Session controller | Hold current core state, route commands and the hard-drop policy choice, convert browser timestamps into elapsed-time inputs, and coordinate snapshots. Own lifecycle handles and the clock anchor. | Game core and narrow callbacks for browser services and presentation. |
| Browser adapters | Translate keyboard/button/setting events and animation timestamps into controller inputs. Supply production random samples and attach/detach bindings under the controller's lifecycle ownership. | Browser APIs and the controller's command boundary. |
| Presentation | Draw board, ghost, next and hold snapshots, update HTML metrics/status/setting state, and size drawing surfaces for the desktop layout. Own visual styling and drawing resources. | Read-only core snapshots, Canvas 2D, and the DOM. |

Compose these blocks through direct function calls and narrow callbacks. Browser adapters and presentation depend on the core's public data contracts; the core has no dependency on either. Detailed component boundaries and interface shapes belong in DECOMPOSITION.

## State and execution flow

Browser startup resolves HTML elements and drawing contexts, creates the production random source, initializes the core, and connects the controller to input, timing, and presentation.

Input adapters submit game commands or the immediate-hard-drop choice. The controller passes commands and explicit elapsed time into the core, stores the resulting state, and presents its snapshot. The core owns collision, kicked rotation, hold, bag consumption, hard drop, locking, line removal, scoring, progression, and game over. Presentation does not reproduce those decisions.

Random samples enter through an injected numeric source. The core owns seven-bag shuffling and consumption; production supplies JavaScript randomness and tests supply fixed or seeded samples. Keeping the bag in core state makes restart, hold-related draws, and replay deterministic without hidden adapter state. An external stateful piece generator would weaken that ownership and is unnecessary. The core reads neither ambient randomness nor wall-clock time.

The controller owns the browser clock anchor and animation-loop lifecycle; the core owns gravity progress and grounded-lock elapsed time. Pause, resume, and restart coordinate these owners so browser scheduling does not become a second source of gameplay state. The single frame loop advances both timers; browser timeouts do not independently lock pieces. SPEC defines precise event ordering and timing policies.

The board's logical coordinates remain independent of CSS size and device-pixel scaling. Presentation sizes its drawing buffer and cell geometry from the viewport and draws from the same logical snapshot.

## Placement and policy ownership

One core landing projection supplies both ghost geometry and the hard-drop destination. It derives a position without changing board, timers, bag, or hold state. The renderer consumes that result, so visual guidance and executed placement cannot use different collision algorithms.

Rotation geometry and ordered kick candidates are core rule data. Hold eligibility and held-piece replacement are core transitions, including spawn collision and random-consumption rules. Components remain ordinary functions and immutable data; these features do not require a plugin framework or a class per piece.

The default grounded lock interval and the selectable immediate-hard-drop exception belong to core policy. The setting is exposed through semantic HTML and routed by the controller; it is retained for the application's current session without cookies, URL parameters, or storage. Rendering or changing a view cannot reset a gameplay timer. Exact timer-reset, pause, restart, and setting-change semantics belong to SPEC.

## System invariants and failure boundaries

- Given the same initial state, commands, elapsed time, policy choices, and random samples, the core produces the same result.
- A rendering pass consumes a snapshot without advancing gameplay, selecting a piece, or modifying the board.
- One controller owns each game's current state, event registrations, and animation-loop lifecycle.
- Game over is an ordinary core state. The browser composition boundary owns startup failures, including an unavailable drawing context, and presents a readable failure state.
- Controls, instructions, score, and status use semantic HTML. Canvas pixels are not the authoritative source of textual state.

## Verification and delivery

| Boundary | Verification approach |
| --- | --- |
| Game core | Vitest exercises geometry/kicks, bags and hold consumption, shared landing projection, hard-drop policies, and chronological gravity/lock contracts using controlled state, elapsed time, and random samples. |
| Session controller | Tests supply a controlled clock and callbacks to check command/setting routing, pause/restart, and lifecycle coordination. |
| Browser integration | Playwright drives the served application with real keyboard/button input and checks DOM state plus Canvas output. |
| Production build | Explicit TypeScript checking, Vite build, and browser checks against the generated static application. |

Apply [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md) for browser selection, isolated provisioning, headless modes, diagnostics, and recovery. Chromium is primary and Playwright-managed Firefox is secondary. Representative provisioning and execution must be established before browser-dependent implementation relies on those facilities. Document-only planning establishes no browser capability evidence.

Test fixtures use the core's input boundaries and the application's composition seams. The production bundle excludes test fixtures and runner dependencies. Browser verification must exercise the application's real entry and server path; core tests and synthetic provisioning probes supply separate evidence.

GitHub provides source hosting, tracking, CI, and release publication. Gameplay has no service dependency. Build and test commands must be repeatable from the committed toolchain and lockfile.

Package metadata supplies the authoritative application version. Built version information, release tag, and release notes must agree. Phase completion, verification, integration, and release publication follow the accepted PROJECT policy.

## Subsequent design boundaries

DECOMPOSITION defines cohesive components, ownership details, and interfaces for commands, snapshots, random supply, timing, policy, and rendering. SPEC owns exact gameplay and failure contracts. PLAN owns delivery order and phase exits, and layout owns physical paths. This architecture requires no focused child documents.

## Technical sources

- [TypeScript strict checking](https://www.typescriptlang.org/tsconfig/strict.html)
- [Vite TypeScript support and separate type checking](https://vite.dev/guide/features#typescript)
- [Canvas API and semantic-content considerations](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
- [Vitest guide](https://vitest.dev/guide/)
