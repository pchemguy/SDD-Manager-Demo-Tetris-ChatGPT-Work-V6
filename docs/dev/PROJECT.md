# Browser Tetris

Acceptance and preparation progress are recorded in [DOCS_TASKS.md](DOCS_TASKS.md).

## Purpose and outcome

Deliver a compact single-player Tetris game for individual play in a desktop browser with a keyboard. A player can start a game, move and rotate falling pieces, clear lines, follow their score and progress, pause, and restart after game over. The complete target includes modern piece-management and placement aids, a penetrator bonus mechanic, and persistent player configuration. The result should be straightforward to run, responsive to play, and independently testable.

## Confirmed direction

- The complete target comprises the classic foundation and all modern extensions below.
- Manage project versions and publish a tagged GitHub release with release notes after each implementation phase has been completed, verified, and integrated.

## Classic foundation

- A 10-column by 20-row visible playfield and the seven standard tetromino shapes.
- Piece spawning, horizontal movement, rotation, gravity, and soft drop.
- Collision detection, piece locking, completed-line removal, and game over.
- Score, cleared-line count, levels, increasing difficulty, and a next-piece preview.
- Visible keyboard instructions, start, pause/resume, and restart controls.
- Clear game-state feedback and a legible board at ordinary desktop browser sizes.
- Automated gameplay checks and browser interaction/rendering checks appropriate to the accepted behavior.

## Modern extensions

- Hold a piece for later use.
- Offer ghost visibility modes: always hide, hide dynamically while the projected landing is above the board midline, or always show.
- Select pieces with seven-bag randomization.
- Support wall kicks during rotation.
- Move the active piece to its landing position with hard drop.
- By default, lock after one full gravity interval from becoming grounded. Also offer locking on a failed downward movement attempt. Hard drop follows the selected general policy unless immediate hard-drop locking is selected.
- Activate a penetrator on the current falling piece with a hotkey when a bonus is available. It passes through occupied cells without destroying them until it locks, with its final cells entirely inside the board and free of overlap. Penetrator hard drop reaches the lowest valid landing through obstructions, and a visible ghost previews that same destination.
- Award one penetrator bonus for a four-line clear, accumulate at most three, spend one per activation, and update one side-panel symbol per available bonus when earned or spent. Penetrator pieces are entirely white; regular pieces never use white.

## Game configuration and persistence

Provide a side panel with all six controls:

| Setting | Choices |
| --- | --- |
| Ghost visibility | Always hide; dynamically hide above the board midline; always show |
| Hard drop | On; off |
| Next-piece preview | On; off |
| Hard-drop locking | Follow the selected general policy; lock immediately after hard drop |
| General locking | Failed downward movement attempt; one full gravity interval from grounding |
| Hold | On; off |

Restore each setting independently on reload or reopening, using an explicit URL parameter value first, its saved cookie value second, and a sensible default otherwise. Every player change saves the corresponding cookie and updates that URL parameter. Explicit URL values are saved to cookies even when equal to default. An omitted parameter retains its saved cookie value rather than replacing it with default. These are settings only; saved games and scores are outside scope.

## Scope boundaries

The project excludes multiplayer, accounts, server leaderboards, mobile touch controls, audio, selectable themes, saved games, and persistent scores. Configuration persistence is limited to the six settings above; gameplay has no server dependency.

The game runs entirely in the browser without a runtime backend. Development tooling and a local server may be used to build and test it. The accepted TypeScript, Canvas/HTML, and test-tool choices are defined in ARCHITECTURE.

## Quality and delivery constraints

Keep gameplay rules separate enough from browser presentation to support deterministic checks. Provide repeatable setup, build, and validation instructions. Follow the repository's [browser development policy](../../BROWSER_DEV_POLICY.md).

Use semantic versioning, with `0.1.0` for the first playable phase release. Increase the minor version for subsequent functionality increments and the patch version for compatible fixes. Every phase release requires completed phase work, verification, published integration, a corresponding version tag, and release notes describing delivered changes and validation.

## Decisions for later stages

ARCHITECTURE defines structural and technology choices. SPEC defines exact hold, ghost/midline, penetrator traversal/locking, bonus, bag, kick, hard-drop, general locking, scoring, input, and pause contracts, plus configuration defaults, identifiers, validation, lifecycle and persistence failure behavior. PLAN bounds useful delivery phases, beginning with the simplest sensible playable foundation and adding the remaining capabilities in explicit increments. The complete project must cover every modern extension; an early phase may deliver a clearly identified subset.
