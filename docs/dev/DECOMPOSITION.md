# Component decomposition

This document refines the accepted [ARCHITECTURE.md](ARCHITECTURE.md) into logical components for the [complete game scope](PROJECT.md). [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation progress and human decisions. Component names identify responsibilities; layout will assign their physical modules.

## Components and ownership

| Block and component | Owns and provides | Uses and boundary |
| --- | --- | --- |
| Core: model and piece geometry | Canonical kinds, piece variants, cell/placement types, orientation geometry, ordered kick data, and conversion of a placement into occupied cells. | Plain values. Supplies geometry/candidates; the engine chooses a valid kicked placement using board checks. |
| Core: board operations | Separate in-bounds/final-overlap/traversal checks, variant-aware landing projection, safe final placement, completed-row detection and row compaction. | Core cell/board types. One regular/penetrator landing calculation serves ghost and hard drop. Traversal never edits occupied cells; final placement requires empty cells. Timing, bonuses and score stay with the engine. |
| Core: bag operations | Seven-bag shuffle/refill and consume operations on explicit bag values, with controlled random consumption. | Canonical kinds and injected random samples. Returns piece/bag results; the rules engine retains bag state and decides when to consume. |
| Core: configuration values and resolution | Typed keys/allowed values/defaults, setting validation and independent URL/cookie/default resolution from supplied optional strings, with source provenance. | Plain values only; returns selected settings and explicit valid URL values to save. No cookie, URL/history, DOM or game-state effects. |
| Core: rules engine and snapshot projection | Complete game state: active/next/held pieces, bag, hold eligibility, gravity/grounded timers, all six settings, active/held penetrator identity and bonus count, score/progression, and session status. Applies all gameplay transitions and derives snapshots. | Geometry, board and bag operations, explicit commands/settings/time, and supplied randomness. Owns atomic activation/spend, lock/clear/bonus award/spawn and hold transitions, feature gates, and conditional ghost visibility. |
| Session: controller | Current core-state reference, browser clock anchor, binding lifetime, command/setting routing, persistence callback ordering/save feedback and presentation coordination. | Core transitions and injected input, clock/frame, random, persistence and render callbacks. Synchronizes time before timed actions; owns no rule timer or duplicate settings model. Save feedback stays outside core state. |
| Browser: input bindings | Keyboard/button/setting subscriptions and translation into commands or a typed one-setting update. Return cleanup handles. | HTML targets and supplied sinks. Mappings, repeats, focus and control semantics are specified in SPEC. |
| Browser: clock and frame source | Monotonic current-time sampling and animation-frame timestamp delivery from the same time origin. Return a handle that stops owned scheduling. | Browser timing APIs. The controller converts timestamp differences into elapsed inputs; the core accumulates rule time. |
| Browser: configuration persistence adapter | Read URL/cookie strings, call pure resolution, save explicit URL values to cookies at startup, and persist each player change to its cookie and URL parameter. | Injected browser read/write/history operations and the pure model. Return per-destination outcomes; catch storage/history failures, preserve unrelated URL data and never own gameplay settings. |
| Browser: random source | Produce numeric random samples; tests supply fixed or seeded samples through the same boundary. | No piece, bag, or hold state. Validation and exact shuffle/consumption contracts belong to SPEC and core bag operations. |
| Presentation: board/preview renderer | Canvas resources, buffer sizing, cell metrics/regular palette/white variants, board/ghost drawing and enabled next/hold previews. | Read-only snapshot cells and dimensions. Does not compute landing, midline visibility, kicks, bonuses or hold eligibility. |
| Presentation: status/control view | HTML metrics/status/instructions, hold/penetrator availability, bonus symbols, all six controls and save-status feedback. | Read-only presentation frames and resolved HTML references. Input belongs to bindings; selected settings/bonus state belong to the core; save status belongs to the controller. |
| Browser entry: composition root | Resolve required HTML/Canvas resources, resolve configuration before core initialization, assemble the components, start one session and surface startup failures. | Concrete browser adapters, controller, and views. Retains access to session disposal and cleans up partially assembled resources on failure. |

Board operations and the rules engine remain cohesive units. Bag operations have a distinct algorithm and random-consumption verification boundary. Hold, penetrator/bonus and lock-policy transitions stay within the engine; separate hold/bonus/timer managers would duplicate state ownership. Configuration values have a distinct pure validation/resolution boundary; browser persistence has a distinct side-effect/failure boundary. Drawing primitives shared by board and previews remain within their renderer.

## Public design contracts

These shapes establish collaboration and test seams. SPEC owns precise valid-input conditions, outcomes, error semantics, and gameplay constants.

| Contract | Producer and consumers | Design-level shape |
| --- | --- | --- |
| Piece placement and occupied cells | Geometry supplies cells to board operations and the rules engine. | Piece kind, regular/penetrator variant, orientation, board origin and integer cell coordinates. Settled cells retain their visual variant; variant never grants traversal to locked cells. |
| Board result | Board operations return results to the rules engine. | A board value and placement/line-removal information; rule timing and score effects stay with the engine. |
| Game input | Input bindings and controller supply inputs to the rules engine. | A session/gameplay command (including penetrator activation), typed key/value setting update or explicit elapsed-milliseconds advance. |
| Configuration resolution | Pure configuration model consumes extracted optional URL and cookie values. | Complete validated settings plus source provenance and explicit URL selections to save, resolved independently per key. |
| Persistence result | Browser adapter supplies results to composition/controller. | Startup load or one-key save; independent cookie/URL success or failure and safe feedback, with no engine-state mutation. |
| Random source and bag result | Adapter/fixture supplies samples; bag operations return results to the engine. | A numeric `sample()` callback and explicit bag-remainder/piece values; no hidden generator state in browser adapters. |
| Core transition | The controller calls the engine with its current state and input. | Initialization receives resolved settings and creates ready state; each input returns a complete next state, consuming supplied randomness only when rules require it. |
| Game snapshot | The engine projects state for the controller's render callback. | Read-only board and active/ghost/next/held cells with variants, visibility/availability, settings, bonus count, metrics and status. Bag/timer internals are not view-owned state. |
| Input/clock/frame bindings | Browser adapters are injected into the controller. | Connect supplied command/setting/frame callbacks and return cleanup. A `now()` callback and frame timestamps share a monotonic time origin. |
| View update | The controller invokes one composed presentation callback. | `render(frame)` updates Canvas and HTML from one game snapshot plus controller-owned save status. |
| Session handle | The controller exposes the running session to its composition root. | Dispatch a command, update one setting and dispose bindings/scheduling. |

Core state is retained by the controller and updated only through core operations. Transitions preserve earlier state/snapshot values; unchanged read-only values may be shared. Presentation receives the snapshot contract rather than mutable engine internals. Snapshot projection owns derived drawing cells, so the renderer does not maintain another tetromino catalogue.

## Collaboration and lifecycle

1. The composition root resolves the drawing context and HTML references, creates browser adapters, resolves initial configuration and saves explicit URL values, then assembles views/controller with resolved settings and save feedback.
2. The controller initializes the core with the supplied random source and validated settings, presents an initial frame, and owns the input/frame binding handles.
3. Input bindings emit commands or setting changes. The controller uses clock samples and frame timestamps to advance elapsed time before applying timed actions, then sends the completed frame to both views. An accepted setting change invokes independent cookie/URL save attempts; failures update feedback without rolling back selected settings or terminating play. SPEC defines ordering and limits.
4. The core uses geometry and board operations to complete a transition before exposing its result. Presentation does not observe a partially applied activation/spend, lock, clear/award or spawn operation.
5. Pause/resume and restart coordinate core status and the clock anchor. Restart resets gameplay, bag/hold/penetrator state and bonuses within the existing session lifecycle, retaining selected settings as specified in SPEC; it does not duplicate bindings.
6. Resize recomputes presentation dimensions and redraws the latest snapshot without advancing core time, consuming randomness, or recalculating gameplay decisions in a view.
7. Session disposal releases owned event and scheduling handles and prevents subsequent callbacks from changing or presenting that session. Startup cleanup uses the handles already acquired if assembly fails.

The core owns accumulated gameplay time; the controller owns its browser timestamp anchor. SPEC settles elapsed-time limits, pause/resume discontinuities, input ordering, and visibility/focus policy. Production timing and deterministic test timing use the same core input path.

## Dependency and failure boundaries

The rules engine depends on geometry and board operations. Those units depend on core data contracts. The controller depends on the engine and narrow callback contracts. Browser bindings implement those contracts, while presentation consumes snapshots. The composition root wires concrete collaborators. Callback delivery does not introduce a core dependency on browser implementations.

Normal blocked movement and game over are gameplay outcomes handled by the engine. The composition root owns missing HTML/Canvas initialization failures, and the controller owns cleanup of an established session. Precise handling of invalid inputs and unexpected failures belongs in SPEC.

Random samples and elapsed time enter explicitly. Bag order is explicit core state. Settings are read at startup and then changed through the core input boundary; persistence is a mirror and does not subscribe a second gameplay state source. Ghost projection, setting resolution and rendering cannot consume randomness, change hold eligibility, or reset gravity/grounded timers. Browser event state and drawing resources remain outside the core model.

## Verification seams

| Component boundary | Direct evidence to plan |
| --- | --- |
| Geometry, board, and bag operations | Orientation/kick candidates, regular/penetrator traversal and final-placement safety, matching ghost/drop landing, compaction, bag permutations/refills, and random-consumption counts. |
| Configuration | Per-key precedence, explicit default values, omissions, malformed values and provenance without browser effects. |
| Rules engine | Controlled boards, samples, commands/settings and time; check bonus cap/spend/award, held variant retention, traversal/landing, conditional visibility, feature gates, both general locks and hard-drop exceptions, atomic transitions, and prior-state/snapshot preservation. |
| Controller | Controlled clock/frame source, input/setting sinks, deterministic random source, persistence callbacks and recording renderer; check timed-action ordering, save failure isolation, lifecycle and frame delivery without browser timing. |
| Persistence adapter | Cookie attributes/reading and URL extraction/update/reload through real browser APIs, with independent failure injection and no navigation/game reset. |
| Input/frame adapters | Browser-driven events and scheduling checks through the real application path, including cleanup and focus behavior once specified. |
| Canvas and HTML views | Frame-driven drawing, white/regular palette, bonus symbols, visibility and text/control/save-feedback updates, plus inspected or asserted Canvas output at representative desktop sizes. |
| Composition and production entry | Playwright exercises the built application, its initialization, keyboard/buttons, and visible output under the central browser policy. |

Core fixtures construct valid model values for focused scenarios. Browser fixtures use composition seams and the same core/renderer path. Fixture assembly and test dependencies remain outside production output. The exact test scenarios and browser acceptance matrix are derived from SPEC, with provisioning governed by [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md).

## Decisions handed to SPEC

SPEC must settle rotation/spawn geometry and kicks; bag/hold consumption and held penetrator identity; admissible transient traversal versus safe locking; lowest-valid landing and dynamic midline classification; bonus award/cap/spend and white rendering; both general and hard-drop lock policies; scoring/gravity; all six defaults, identifiers and live changes; cookie/URL precedence, validation and failures; input/focus/repeat; pause/restart; and frame/state contracts. These contracts must fit the ownership above and the complete PROJECT scope.

No component requires a separate detailed child document at this scope. Delivery sequence and physical allocation remain with PLAN and layout.
