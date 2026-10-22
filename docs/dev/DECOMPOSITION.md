# Component decomposition

This document refines the accepted [ARCHITECTURE.md](ARCHITECTURE.md) into logical components for the [classic game scope](PROJECT.md). [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation progress and human decisions. Component names identify responsibilities; layout will assign their physical modules.

## Components and ownership

| Block and component | Owns and provides | Uses and boundary |
| --- | --- | --- |
| Core: model and piece geometry | Canonical piece kinds, cell/placement types, orientation geometry, and conversion of a piece placement into occupied cells. | Plain values. Supplies the geometry used by board checks, transitions, and snapshot projection. |
| Core: board operations | Bounds/overlap checks, placement, completed-row detection, and row compaction. Operations produce board values and cleared-line information. | Core cell and board types. The rules engine decides when placement occurs and how line removal affects progression. |
| Core: rules engine and snapshot projection | Complete game state, initialization, movement/rotation, gravity and locking, spawning, score/level progression, pause/session status, and game over. Projects read-only presentation data. | Geometry, board operations, explicit commands/elapsed time, and the supplied piece source. Contains the rule decisions. |
| Session: controller | The current core-state reference, browser clock anchor, input/frame binding lifetime, command routing, and presentation coordination. | Core transitions and injected input, frame, piece-source, and render callbacks. Treats visual resources as presentation-owned. |
| Browser: input bindings | Keyboard/button event subscriptions and translation into session commands. Return a cleanup handle for their registrations. | HTML event targets and a supplied command sink. Key mappings, repeat handling, and focus rules are specified in SPEC. |
| Browser: frame source | Animation-frame registration and monotonic timestamp delivery. Return a handle that stops the owned scheduling. | Browser scheduling APIs and a supplied frame callback. The controller converts timestamps to elapsed time; the core accumulates rule time. |
| Browser: piece source | Produce piece-kind draws using production randomness; tests supply deterministic sequences through the same boundary. | Canonical piece kinds. Draw distribution and consumption rules are specified in SPEC. |
| Presentation: board/preview renderer | Canvas drawing resources, drawing-buffer sizing, cell metrics, colors, and board/preview drawing. | Read-only snapshot cells and surface dimensions. Geometry in snapshots comes from the canonical core catalogue. |
| Presentation: status/control view | HTML score, line count, level, status, instructions, control labels, and enabled-state presentation. | Read-only snapshots and resolved HTML references. Input event handling belongs to input bindings. |
| Browser entry: composition root | Resolve required HTML/Canvas resources, assemble the components, start one session, and surface startup failures. | Concrete browser adapters, controller, and views. Retains access to session disposal and cleans up partially assembled resources on failure. |

Board operations and the rules engine remain cohesive units. Scoring and progression are rule-engine responsibilities. Drawing primitives shared by board and preview remain within their renderer.

## Public design contracts

These shapes establish collaboration and test seams. SPEC owns precise valid-input conditions, outcomes, error semantics, and gameplay constants.

| Contract | Producer and consumers | Design-level shape |
| --- | --- | --- |
| Piece placement and occupied cells | Geometry supplies cells to board operations and the rules engine. | Piece kind, orientation, board origin, and integer cell coordinates. |
| Board result | Board operations return results to the rules engine. | A board value and placement/line-removal information; rule timing and score effects stay with the engine. |
| Game input | Input bindings and controller supply inputs to the rules engine. | A session/gameplay command or an explicit elapsed-milliseconds advance. |
| Piece source | Production adapter or deterministic test fixture supplies draws to the engine. | A `next()` callback returning a canonical piece kind. |
| Core transition | The controller calls the engine with its current state and input. | Initialization creates a state; applying an input returns the resulting state using explicit piece draws when needed. |
| Game snapshot | The engine projects state for the controller's render callback. | Read-only board and active-piece cells, preview cells, score, line count, level, and session status. |
| Input/frame bindings | Browser adapters are injected into the controller. | Connect a supplied command or frame callback and return a disposal callback. Frame callbacks carry monotonic timestamps. |
| View update | The controller invokes one composed presentation callback. | `render(snapshot)` updates Canvas and HTML views from the same snapshot. |
| Session handle | The controller exposes the running session to its composition root. | Dispatch a command and dispose the session's bindings/scheduling. |

Core state is retained by the controller and updated only through core operations. Transitions preserve earlier state/snapshot values; unchanged read-only values may be shared. Presentation receives the snapshot contract rather than mutable engine internals. Snapshot projection owns derived drawing cells, so the renderer does not maintain another tetromino catalogue.

## Collaboration and lifecycle

1. The composition root resolves the drawing context and HTML references, creates the views and browser adapters, and assembles the controller with its callbacks.
2. The controller initializes the core with the supplied piece source, presents an initial snapshot, and owns the handles returned by input and frame bindings.
3. Input bindings emit commands. Frame bindings emit timestamps. The controller converts time into an elapsed input, applies the core transition, and passes one resulting snapshot to both views.
4. The core uses geometry and board operations to complete a transition before exposing its result. Presentation does not observe a partially applied lock, clear, or spawn operation.
5. Pause/resume and restart coordinate core status and the controller clock anchor. Restart creates fresh core state within the existing session lifecycle; it does not duplicate event or frame bindings.
6. Resize recomputes presentation dimensions and redraws the latest snapshot without advancing core time or consuming a piece draw.
7. Session disposal releases owned event and scheduling handles and prevents subsequent callbacks from changing or presenting that session. Startup cleanup uses the handles already acquired if assembly fails.

The core owns accumulated gameplay time; the controller owns its browser timestamp anchor. SPEC settles elapsed-time limits, pause/resume discontinuities, input ordering, and visibility/focus policy. Production timing and deterministic test timing use the same core input path.

## Dependency and failure boundaries

The rules engine depends on geometry and board operations. Those units depend on core data contracts. The controller depends on the engine and narrow callback contracts. Browser bindings implement those contracts, while presentation consumes snapshots. The composition root wires concrete collaborators. Callback delivery does not introduce a core dependency on browser implementations.

Normal blocked movement and game over are gameplay outcomes handled by the engine. The composition root owns missing HTML/Canvas initialization failures, and the controller owns cleanup of an established session. Precise handling of invalid inputs and unexpected failures belongs in SPEC.

Piece choices and elapsed time enter explicitly. The renderer cannot consume randomness or advance the game. Browser event state and drawing resources remain outside the core model.

## Verification seams

| Component boundary | Direct evidence to plan |
| --- | --- |
| Geometry and board operations | Known cells/orientations, collision boundaries, placements, and row-compaction cases through their value interfaces. |
| Rules engine | Deterministic transitions with controlled boards, piece sequences, commands, and time; checks that prior state/snapshots remain unchanged. |
| Controller | Controlled frame source, input sink, deterministic piece source, and recording renderer; check lifecycle and delivery without a browser clock. |
| Input/frame adapters | Browser-driven events and scheduling checks through the real application path, including cleanup and focus behavior once specified. |
| Canvas and HTML views | Snapshot-driven drawing and text/control updates, plus inspected or asserted Canvas output at representative desktop sizes. |
| Composition and production entry | Playwright exercises the built application, its initialization, keyboard/buttons, and visible output under the central browser policy. |

Core fixtures construct valid model values for focused scenarios. Browser fixtures use composition seams and the same core/renderer path. Fixture assembly and test dependencies remain outside production output. The exact test scenarios and browser acceptance matrix are derived from SPEC, with provisioning governed by [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md).

## Decisions handed to SPEC

SPEC must settle rotation/spawn geometry, piece selection, collision/locking outcomes, score/level/gravity rules, input/focus ordering, pause/restart behavior, snapshot-visible state, and failure handling. These contracts must fit the ownership above and the accepted classic-only scope.

No component requires a separate detailed child document at this scope. Delivery sequence and physical allocation remain with PLAN and layout.
