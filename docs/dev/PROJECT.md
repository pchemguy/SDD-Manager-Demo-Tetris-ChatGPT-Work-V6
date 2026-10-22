# Classic browser Tetris

Status: Proposal awaiting first human approval.

## Purpose and outcome

Deliver a compact single-player Tetris game for individual play in a desktop browser with a keyboard. A player can start a game, move and rotate falling pieces, clear lines, follow their score and progress, pause, and restart after game over. The result should be straightforward to run, responsive to play, and independently testable.

## Confirmed direction

- The user requested a browser-based Tetris game and selected the classic-only target.
- Manage project versions and publish a tagged GitHub release with release notes after each implementation phase has been completed, verified, and integrated.

## Proposed classic scope

- A 10-column by 20-row visible playfield and the seven standard tetromino shapes.
- Piece spawning, horizontal movement, rotation, gravity, and soft drop.
- Collision detection, piece locking, completed-line removal, and game over.
- Score, cleared-line count, levels, increasing difficulty, and a next-piece preview.
- Visible keyboard instructions, start, pause/resume, and restart controls.
- Clear game-state feedback and a legible board at ordinary desktop browser sizes.
- Automated gameplay checks and browser interaction/rendering checks appropriate to the accepted behavior.

These baseline details are proposed for approval.

## Scope boundaries

The classic-only target excludes hold, ghost pieces, seven-bag randomization, wall kicks, and hard drop. The proposed project also excludes multiplayer, accounts, server leaderboards, mobile touch controls, audio, themes, and settings persistence. Those capabilities require a separate scope decision.

The game should run entirely in the browser without a runtime backend. Development tooling and a local server may be used to build and test it. The development language, rendering approach, and test tools will be selected during architecture work.

## Quality and delivery constraints

Keep gameplay rules separate enough from browser presentation to support deterministic checks. Provide repeatable setup, build, and validation instructions. Follow the repository's [browser development policy](../../BROWSER_DEV_POLICY.md).

Use semantic versioning, with `0.1.0` for the first playable phase release. Increase the minor version for subsequent functionality increments and the patch version for compatible fixes. Every phase release requires completed phase work, verification, published integration, a corresponding version tag, and release notes describing delivered changes and validation.

## Decisions for later stages

ARCHITECTURE will settle technology and rendering choices. SPEC will define exact rotation and locking rules, piece selection, scoring, level/gravity progression, keyboard mappings, and pause behavior. PLAN will bound useful delivery phases. These decisions must remain within the classic-only target.
