# Component decomposition

This document refines the accepted [ARCHITECTURE.md](ARCHITECTURE.md) into logical components for the [complete game scope](PROJECT.md). [DOCS_TASKS.md](DOCS_TASKS.md) owns preparation progress and human decisions. Component names identify responsibilities; layout will assign their physical modules.

## Components and ownership

| Block and component | Owns and provides | Uses and boundary |
| --- | --- | --- |
| Core: model and piece geometry | Canonical kinds, cell/placement types, orientation geometry, ordered kick data, and conversion of a placement into occupied cells. | Plain values. Supplies geometry/candidates; the engine chooses a valid kicked placement using board checks. |
| Core: board operations | Bounds/overlap checks, landing projection, placement, completed-row detection, and row compaction. | Core cell/board types. One landing calculation serves ghost and hard drop; timing and score effects stay with the engine. |
| Core: bag operations | Seven-bag shuffle/refill and consume operations on explicit bag values, with controlled random consumption. | Canonical kinds and injected random samples. Returns piece/bag results; the rules engine retains bag state and decides when to consume. |
| Core: rules engine and snapshot projection | Complete game state: active/next/held pieces, bag, hold eligibility, gravity/grounded timers, hard-drop policy, score/progression, and session status. Applies all gameplay transitions and derives snapshots. | Geometry, board and bag operations, explicit commands/policy/time, and supplied randomness. Owns atomic lock/clear/spawn and hold transitions. |
| Session: controller | Current core-state reference, browser clock anchor, binding lifetime, command/policy routing, and presentation coordination. | Core transitions and injected input, clock/frame, random, and render callbacks. Synchronizes time before timed actions; owns no rule timer. |
| Browser: input bindings | Keyboard/button/setting subscriptions and translation into commands or a hard-drop policy update. Return cleanup handles. | HTML targets and supplied sinks. Mappings, repeats, focus, and setting semantics are specified in SPEC. |
| Browser: clock and frame source | Monotonic current-time sampling and animation-frame timestamp delivery from the same time origin. Return a handle that stops owned scheduling. | Browser timing APIs. The controller converts timestamp differences into elapsed inputs; the core accumulates rule time. |
| Browser: random source | Produce numeric random samples; tests supply fixed or seeded samples through the same boundary. | No piece, bag, or hold state. Validation and exact shuffle/consumption contracts belong to SPEC and core bag operations. |
| Presentation: board/preview renderer | Canvas resources, buffer sizing, cell metrics/colors, board/ghost drawing, and next/hold previews. | Read-only snapshot cells and dimensions. Does not compute landing, kicks, or hold eligibility. |
| Presentation: status/control view | HTML metrics/status/instructions, hold availability, control labels/enabled states, and immediate-hard-drop choice. | Read-only snapshots and resolved HTML references. Input handling belongs to bindings; policy state belongs to the core. |
| Browser entry: composition root | Resolve required HTML/Canvas resources, assemble the components, start one session, and surface startup failures. | Concrete browser adapters, controller, and views. Retains access to session disposal and cleans up partially assembled resources on failure. |

Board operations and the rules engine remain cohesive units. Bag operations have a distinct algorithm and random-consumption verification boundary. Hold and lock-policy transitions stay within the engine; separate hold/timer managers would duplicate state ownership. Drawing primitives shared by board and previews remain within their renderer.

## Public design contracts

These shapes establish collaboration and test seams. SPEC owns precise valid-input conditions, outcomes, error semantics, and gameplay constants.

| Contract | Producer and consumers | Design-level shape |
| --- | --- | --- |
| Piece placement and occupied cells | Geometry supplies cells to board operations and the rules engine. | Piece kind, orientation, board origin, and integer cell coordinates. |
| Board result | Board operations return results to the rules engine. | A board value and placement/line-removal information; rule timing and score effects stay with the engine. |
| Game input | Input bindings and controller supply inputs to the rules engine. | A session/gameplay command, hard-drop policy update, or explicit elapsed-milliseconds advance. |
| Random source and bag result | Adapter/fixture supplies samples; bag operations return results to the engine. | A numeric `sample()` callback and explicit bag-remainder/piece values; no hidden generator state in browser adapters. |
| Core transition | The controller calls the engine with its current state and input. | Initialization creates state; each input returns a complete next state, consuming supplied randomness only when rules require it. |
| Game snapshot | The engine projects state for the controller's render callback. | Read-only board, active/ghost/next/held cells, hold availability, hard-drop policy, metrics, and status. Bag/timer internals are not view-owned state. |
| Input/clock/frame bindings | Browser adapters are injected into the controller. | Connect supplied command/policy/frame callbacks and return cleanup. A `now()` callback and frame timestamps share a monotonic time origin. |
| View update | The controller invokes one composed presentation callback. | `render(snapshot)` updates Canvas and HTML views from the same snapshot. |
| Session handle | The controller exposes the running session to its composition root. | Dispatch a command, update hard-drop policy, and dispose bindings/scheduling. |

Core state is retained by the controller and updated only through core operations. Transitions preserve earlier state/snapshot values; unchanged read-only values may be shared. Presentation receives the snapshot contract rather than mutable engine internals. Snapshot projection owns derived drawing cells, so the renderer does not maintain another tetromino catalogue.

## Collaboration and lifecycle

1. The composition root resolves the drawing context and HTML references, creates the views and browser adapters, and assembles the controller with its callbacks.
2. The controller initializes the core with the supplied random source and session policy, presents an initial snapshot, and owns the input/frame binding handles.
3. Input bindings emit commands or policy choices. The controller uses clock samples and frame timestamps to advance elapsed time before applying timed actions, then sends the completed snapshot to both views. SPEC defines ordering and limits.
4. The core uses geometry and board operations to complete a transition before exposing its result. Presentation does not observe a partially applied lock, clear, or spawn operation.
5. Pause/resume and restart coordinate core status and the clock anchor. Restart resets gameplay and bag/hold state within the existing session lifecycle, retaining the session's selected hard-drop policy as specified in SPEC; it does not duplicate bindings.
6. Resize recomputes presentation dimensions and redraws the latest snapshot without advancing core time, consuming randomness, or recalculating gameplay decisions in a view.
7. Session disposal releases owned event and scheduling handles and prevents subsequent callbacks from changing or presenting that session. Startup cleanup uses the handles already acquired if assembly fails.

The core owns accumulated gameplay time; the controller owns its browser timestamp anchor. SPEC settles elapsed-time limits, pause/resume discontinuities, input ordering, and visibility/focus policy. Production timing and deterministic test timing use the same core input path.

## Dependency and failure boundaries

The rules engine depends on geometry and board operations. Those units depend on core data contracts. The controller depends on the engine and narrow callback contracts. Browser bindings implement those contracts, while presentation consumes snapshots. The composition root wires concrete collaborators. Callback delivery does not introduce a core dependency on browser implementations.

Normal blocked movement and game over are gameplay outcomes handled by the engine. The composition root owns missing HTML/Canvas initialization failures, and the controller owns cleanup of an established session. Precise handling of invalid inputs and unexpected failures belongs in SPEC.

Random samples and elapsed time enter explicitly. Bag order is explicit core state. Ghost projection and rendering cannot consume randomness, change hold eligibility, or reset gravity/grounded timers. Browser event state and drawing resources remain outside the core model.

## Verification seams

| Component boundary | Direct evidence to plan |
| --- | --- |
| Geometry, board, and bag operations | Orientation/kick candidates, collisions, matching ghost/drop landing, compaction, bag permutations/refills, and random-consumption counts. |
| Rules engine | Controlled boards, samples, commands/policy, and time; check hold/replacement, grounded deadlines, hard-drop exceptions, atomic transitions, and prior-state/snapshot preservation. |
| Controller | Controlled clock/frame source, input/policy sinks, deterministic random source, and recording renderer; check timed-action ordering, lifecycle, and delivery without browser timing. |
| Input/frame adapters | Browser-driven events and scheduling checks through the real application path, including cleanup and focus behavior once specified. |
| Canvas and HTML views | Snapshot-driven drawing and text/control updates, plus inspected or asserted Canvas output at representative desktop sizes. |
| Composition and production entry | Playwright exercises the built application, its initialization, keyboard/buttons, and visible output under the central browser policy. |

Core fixtures construct valid model values for focused scenarios. Browser fixtures use composition seams and the same core/renderer path. Fixture assembly and test dependencies remain outside production output. The exact test scenarios and browser acceptance matrix are derived from SPEC, with provisioning governed by [BROWSER_DEV_POLICY.md](../../BROWSER_DEV_POLICY.md).

## Decisions handed to SPEC

SPEC must settle rotation/spawn geometry and ordered kicks; seven-bag and hold consumption; ghost/hard-drop agreement; grounding, timer resets, and both hard-drop policies; scoring/gravity; input/focus/repeat ordering; pause/restart/setting behavior; snapshot state; and failure handling. These contracts must fit the ownership above and the complete PROJECT scope.

No component requires a separate detailed child document at this scope. Delivery sequence and physical allocation remain with PLAN and layout.
