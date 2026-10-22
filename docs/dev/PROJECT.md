# Browser Tetris

Acceptance and preparation progress are recorded in [DOCS_TASKS.md](DOCS_TASKS.md).

## Purpose and outcome

Deliver a compact single-player Tetris game for individual play in a desktop browser with a keyboard. A player can start a game, move and rotate falling pieces, clear lines, follow their score and progress, pause, and restart after game over. The complete target includes modern piece-management and placement aids. The result should be straightforward to run, responsive to play, and independently testable.

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
- Show a ghost piece at the active piece's landing position.
- Select pieces with seven-bag randomization.
- Support wall kicks during rotation.
- Move the active piece to its landing position with hard drop.
- By default, lock after one full gravity interval from becoming grounded. Hard drop follows that same policy unless the player selects immediate hard-drop locking.

## Scope boundaries

The project excludes multiplayer, accounts, server leaderboards, mobile touch controls, audio, themes, and settings persistence. The immediate-hard-drop choice is a session setting; it does not introduce a general configuration panel or persistence requirement.

The game runs entirely in the browser without a runtime backend. Development tooling and a local server may be used to build and test it. The accepted TypeScript, Canvas/HTML, and test-tool choices are defined in ARCHITECTURE.

## Quality and delivery constraints

Keep gameplay rules separate enough from browser presentation to support deterministic checks. Provide repeatable setup, build, and validation instructions. Follow the repository's [browser development policy](../../BROWSER_DEV_POLICY.md).

Use semantic versioning, with `0.1.0` for the first playable phase release. Increase the minor version for subsequent functionality increments and the patch version for compatible fixes. Every phase release requires completed phase work, verification, published integration, a corresponding version tag, and release notes describing delivered changes and validation.

## Decisions for later stages

ARCHITECTURE defines structural and technology choices. SPEC defines exact hold, ghost, bag, kick, hard-drop, locking, scoring, level/gravity, input, and pause contracts. PLAN bounds useful delivery phases, beginning with the simplest sensible playable foundation and adding the remaining capabilities in explicit increments. The complete project must cover every modern extension; an early phase may deliver a clearly identified subset.
