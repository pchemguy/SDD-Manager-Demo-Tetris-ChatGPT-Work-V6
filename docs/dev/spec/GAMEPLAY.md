# Gameplay contracts

This child of [SPEC.md](../SPEC.md) owns the complete core gameplay contract. Browser input mapping, clock adaptation, presentation, runtime failure handling, and system acceptance remain in the parent.

## G01 — Board and tetromino data

The visible board has 10 columns and 20 rows, with integer x increasing right from 0 through 9 and y increasing down from 0 through 19. There are no hidden rows. Each row-major cell is null or one of `I, J, L, O, S, T, Z`, identifying a locked block. The active piece is separate from the locked board.

A piece placement has kind, orientation, and integer origin x/y. Origins may be negative when the occupied cells still fit. Placement is legal exactly when all four occupied cells are inside the board and all corresponding board cells are empty.

Initial geometry is specified by local occupied coordinates:

| Kind | Square matrix size | Orientation-0 occupied cells |
| --- | --- | --- |
| I | 4 | (0,1), (1,1), (2,1), (3,1) |
| J | 3 | (0,0), (0,1), (1,1), (2,1) |
| L | 3 | (2,0), (0,1), (1,1), (2,1) |
| O | 2 | (0,0), (1,0), (0,1), (1,1) |
| S | 3 | (1,0), (2,0), (0,1), (1,1) |
| T | 3 | (1,0), (0,1), (1,1), (2,1) |
| Z | 3 | (0,0), (1,0), (1,1), (2,1) |

For I/J/L/S/T/Z, a clockwise quarter turn maps each local `(x,y)` to `(size-1-y,x)` in the same matrix. Orientations are 0–3; four quarter turns restore the initial cells. O retains orientation 0 and the same geometry for rotation input. All geometry comes from this catalogue.

## G02 — State, piece selection, and spawning

Core state contains status, board, active/next/held kinds and placement, ordered bag remainder, hold-used flag, hard-drop policy, score, lines, level, gravity milliseconds, and grounded milliseconds or null. Stable states have no completed board row. Score/lines are nonnegative integers and level follows G05. In playing/paused, active is legal, next is present, and gravity time is finite in `[0, interval)`. Grounded means moving the active piece down one cell is illegal: grounded time is finite in `[0, interval)` exactly when grounded, otherwise null. Ready/over have no active piece and zero gravity/null grounded time. Bag remainder contains distinct canonical kinds; G07 defines consumption.

| Status | State and permitted session actions |
| --- | --- |
| ready | Empty board/bag, null active/next/held, unused hold flag, score/lines 0, level 1, zero gravity/null grounded time. Start begins play. |
| playing | Gameplay commands and time advances apply. Pause preserves the game; Restart starts a fresh game. |
| paused | Board, pieces, bag, hold flag, metrics, and both partial timers are preserved. Resume returns to playing; Restart starts fresh. |
| over | Final locked board and counts remain, active is null, gravity accumulator is 0, and next retains the failed-spawn candidate. Restart starts fresh. |

Start is ignored outside ready. Restart is accepted in playing/paused/over and ignored in ready. Pause/Resume apply only in playing/paused respectively. Other inapplicable actions are ignored. Valid ignored inputs consume no random samples and change no rule state. A valid hard-drop policy update applies in every ordinary state and changes only the policy; setting the same value is a no-op.

Creating ready state consumes no samples and defaults policy to `gravity`. Start/Restart reset board/counts/level/timers, empty the bag and held slot, clear hold-used, consume two G07 kinds in order, spawn the first, and retain the second as next. Preserve the selected hard-drop policy. Restart uses the current injected numeric source without rewinding it; the fresh bag is independent of the discarded remainder.

A spawned piece has orientation 0, origin y=0, and origin x=`floor((10-size)/2)`. A legal spawn resets gravity to zero and grounded time to zero if already grounded, otherwise null. After a lock and line/progression updates, promote next. If legal, clear hold-used and consume one replacement next kind. If blocked, enter over, retain that candidate as next, and consume no replacement kind/sample. No overlapping active piece is exposed. G08 defines hold replacement at the same canonical spawn geometry.

## G03 — Movement and rotation

A left/right command proposes origin x minus/plus one, keeping y/orientation. Commit only a legal candidate. Clockwise rotate proposes the next G01 orientation and tests the following origin offsets in order, using board coordinates with positive y downward. Commit the first legal candidate; all failures preserve the original placement, score, and timers and consume no randomness. These tables and G01's visible-board rule define the complete kick contract; there are no counterclockwise or 180-degree controls. O rotation is a no-op.

| From → to | J/L/S/T/Z ordered offsets |
| --- | --- |
| 0 → 1 | (0,0), (-1,0), (-1,-1), (0,2), (-1,2) |
| 1 → 2 | (0,0), (1,0), (1,1), (0,-2), (1,-2) |
| 2 → 3 | (0,0), (1,0), (1,-1), (0,2), (1,2) |
| 3 → 0 | (0,0), (-1,0), (-1,1), (0,-2), (-1,-2) |

| From → to | I ordered offsets |
| --- | --- |
| 0 → 1 | (0,0), (-2,0), (1,0), (-2,1), (1,-2) |
| 1 → 2 | (0,0), (-1,0), (2,0), (-1,-2), (2,1) |
| 2 → 3 | (0,0), (2,0), (-1,0), (2,-1), (-1,2) |
| 3 → 0 | (0,0), (1,0), (-2,0), (1,2), (-2,-1) |

A soft-drop command attempts origin y+1. A legal move advances one row and adds one point. A blocked attempt leaves placement/score unchanged and does not force a lock. Movement/rotation/soft-drop inputs are ignored outside playing. Successful manual placement changes preserve gravity progress and reconcile grounding under G04. Pure command rejection does not advance time; browser elapsed-before-command processing is defined separately in R03.

## G04 — Gravity, locking, and line removal

The gravity interval in milliseconds is `max(100, 1000 - 100*(level-1))`. At level 1 it is 1000 ms; at level 10 and above it is 100 ms.

A time advance accepts finite, nonnegative milliseconds. Gravity progress and, while grounded, grounded elapsed time advance together. At each completed gravity interval, reset gravity progress to zero and attempt one downward move. A legal gravity move adds no drop points; a blocked attempt preserves placement and the grounded timer.

Becoming grounded starts grounded elapsed at zero immediately, including after spawn, movement, a kick, soft drop, or hard drop. Lock when it reaches one full current gravity interval, including the exact boundary. A move that remains grounded preserves elapsed delay, even if the supporting cells change; blocked moves and zero-distance delayed hard drops do not restart it. Becoming airborne cancels grounded elapsed; later grounding starts a new full interval. There is no move-reset count or independent browser lock timeout.

Process time chronologically to the next gravity or lock deadline, then continue with remaining elapsed time. A lock wins an exact tie with gravity, so the same piece is not processed twice. A gravity move that first grounds a piece starts its timer at that event time, not at the end of the supplied advance. Pause preserves both accumulators. G09's selected immediate hard drop is the sole command that bypasses the grounded delay.

Locking writes the four active cells to the board, removes all completed rows simultaneously, updates counts/score/level under G05, and attempts the next spawn under G02. Surviving rows keep their order; empty rows are inserted at the top to retain 20 rows. A transition exposes only its completed result.

A successful spawn resets the new piece's timers under G02. Unconsumed elapsed time continues on that piece using its resulting level interval. A partial interval remains with the current piece. Entering over discards remaining advance time. Advance outside playing preserves rule state and consumes no samples.

## G05 — Score and progression

A successful soft-drop cell adds one point; hard drop adds two points per cell actually descended. A blocked/zero-distance drop adds zero drop points. Hold, ghost projection, policy changes, horizontal movement, rotation/kicks, and gravity add no points.

| Rows cleared by one lock | Base points |
| --- | --- |
| 0 | 0 |
| 1 | 40 |
| 2 | 100 |
| 3 | 300 |
| 4 | 1200 |

Line-clear points equal base points multiplied by the level immediately before that lock. Then increment total lines and set `level = 1 + floor(totalLines/10)`. The promoted piece uses the resulting level's gravity interval. Score and counts are nonnegative integers. There are no combo, spin, or consecutive-clear bonuses.

For example, a two-line clear from totalLines 9 at level 1 adds 100 line points, produces totalLines 11 and level 2, and gives the new piece a 900 ms interval.

## G06 — Boundary and rejection guarantees

Core inputs are the session/gameplay commands above, an explicit hard-drop policy update (`gravity` or `immediate`), or a time advance carrying milliseconds. Board/state/placement/bag values satisfy G01–G02 and G07; valid fixtures may construct them directly. Internal values are caller preconditions: malformed constructed state is outside the supported boundary, with no required runtime detection or exception class. Board helpers may receive full rows for compaction; stable engine states are post-clear states.

Unknown commands, malformed policy updates/unsupported policy values, nonnumeric advances, or nonnumeric random results throw TypeError. Negative/NaN/infinite numeric advances, or numeric random samples outside finite `[0,1)`, throw RangeError. Input shape/value validation precedes the status-dependent ignored-input rule. A random-source exception propagates. Validate random results when a sample is actually required; ignored actions and held-piece swaps do not sample or probe the source.

Rejected input or a failed source call publishes no resulting state; the caller's prior state/snapshots remain unchanged. External source side effects are not rolled back. A valid transition may reuse unchanged read-only values, but may not mutate an earlier state/snapshot.

Given identical valid initial state, command/policy/time sequence, and numeric random samples, results are identical. Core rules read neither browser time nor ambient randomness. Browser operation catches failures as required by R05; direct core callers observe these specified errors.

## G07 — Seven-bag selection

To consume a kind, remove the first element of the current bag remainder. If empty, start with `[I,J,L,O,S,T,Z]` and shuffle: for i from 6 down through 1, sample u, set j=`floor(u*(i+1))`, and swap positions i/j. This uses exactly six validated numeric samples per refill; no seventh sample is required. Consume from the resulting front. Within one round, each complete consecutive group of seven queue-consumed kinds is a permutation; bag boundaries may repeat a kind. Restart starts a new round and restarts this grouping. Holding or locking does not reshuffle a nonempty remainder.

Start consumes active and next; a successful next promotion consumes one replacement; an empty-slot hold consumes one replacement only after its spawn is legal. An occupied-slot swap, blocked spawn, rejected/ignored command, rendering, ghost projection, or pause consumes none. These rules count queue kinds separately from random samples and from pieces placed on the board: hold can change placement order. Restart discards the old remainder and fills a fresh bag.

## G08 — Hold

Hold applies only while playing and hold-used is false. Save only the outgoing active kind, not position or orientation. With an empty slot, use next as the replacement; with an occupied slot, use held kind and leave the ordinary next queue untouched. Attempt replacement at G02's canonical spawn with fresh timers. A legal empty-slot replacement then consumes one replacement next; a legal occupied-slot swap consumes no kind/sample. Set held kind to the outgoing kind and mark hold-used true.

If replacement spawn is blocked, enter over without a replacement draw, with no active/ghost cells, the outgoing kind in Hold, and the failed candidate in Next. The locked board/counts remain unchanged. Hold does not lock or score the outgoing active piece. A successful lock/promotion permits hold again; a hold replacement itself does not. Restart empties Hold and resets eligibility. Paused/used/inactive hold is an ignored action with no state or random-consumption change.

## G09 — Ghost and hard drop

For a legal active placement, landing is the greatest nonnegative vertical distance d for which moving down every step through d stays legal. Ghost cells are that placement's occupied cells. Derivation is read-only and uses the same landing calculation as hard drop; it consumes no time/randomness and changes no state. Playing and paused expose ghost geometry; ready/over expose none. Recompute the snapshot from the current placement/board after every successful transition.

Hard drop applies only in playing. Move the active placement down d, award G05's distance points, preserve gravity progress, and reconcile grounding under G04. With policy `gravity`, retain the active piece at landing and wait for its grounded interval; distance zero preserves any elapsed grounded delay. With `immediate`, perform G04's atomic lock/clear/progression/spawn immediately, even for distance zero. Changing policy by itself never locks or resets a timer. Immediate policy affects hard drop only; ordinary movement/soft drop/gravity still follow the grounded interval. Further elapsed processing and commands follow R03/G04 order.

## Canonical acceptance cases

| ID | Required result |
| --- | --- |
| K01 | Every initial shape has four distinct cells. Four clockwise turns restore I/J/L/S/T/Z, and O is unchanged. Matrix origins and occupied-cell bounds follow G01. |
| K02 | Samples that leave canonical bag order make Start consume I/J; a successful promotion makes J active and L next. The initial refill uses six samples; that promotion uses none. A blocked J spawn consumes no L kind/sample and exposes over without active cells. |
| K03 | A vertical orientation-1 I at origin x=-2 is legal; clockwise 1→2 rejects the first two candidates and accepts the +2-x kick on an empty board. All-blocked movement/rotation preserves placement, timers, score, bag and hold state. |
| K04 | At level 1, 999 then 1 ms produce exactly one gravity attempt. On first grounding, 999 ms leaves active and the next 1 ms locks. Blocked soft drop does not lock or reset the delay. The full interval is also honored at faster levels. |
| K05 | Valid 1–4-line fixtures award 40/100/300/1200 at level 1, preserve survivor-row order, and maintain board height. The 9-to-11-line example produces score +100 and level 2/900 ms. |
| K06 | Paused/over advances and ignored commands preserve rule values/samples. Resume preserves both partial timers. Restart clears board/hold/bag/counts, fills a fresh bag for active/next, and preserves the policy without rewinding the random source. |
| K07 | Invalid advance/command/policy/sample cases yield G06 errors without modifying prior state. Injected source failure during refill publishes no partial state; external source effects need not roll back. Identical inputs/samples replay identically. |
| K08 | Each shuffled bag contains every kind once; consuming two complete bags uses twelve samples and emits fourteen queue kinds. Boundary repeats are permitted and restart begins a new grouping. Start/lock/empty hold consume as G07; swaps, projections, rejections and blocked spawns consume none. |
| K09 | Empty hold stores and promotes; occupied hold swaps without consuming next; replacement resets orientation/position/timers. Used hold is ignored until a lock, canonical blocked replacement enters over, and restart empties the slot. |
| K10 | Ghost and delayed hard drop use identical landing cells on empty and obstructed boards and after kicks/hold. Hard drop awards 2*d, preserves gravity progress, and follows the selected delayed/immediate policy including d=0. Projection mutates nothing. |
| K11 | Every family/transition tries its five candidates in order. A horizontal I at origin (3,18) rotates 0→1 on an empty board using its fifth offset (1,-2); all-blocked cases reject. O is unchanged; no occupied cell may leave visible bounds. |
| K12 | Grounded lateral/kicked moves preserve elapsed delay; lifting cancels and regrounding starts at zero. Pause freezes both timers; an exact gravity/lock tie locks once; remaining time advances the promoted piece with its new interval. Policy changes alone neither lock nor reset. |

These are behavioral acceptance cases, not evidence of implemented tests.
