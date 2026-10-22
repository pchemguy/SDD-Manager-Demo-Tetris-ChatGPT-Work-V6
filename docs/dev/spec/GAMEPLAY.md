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

Core state contains status, board, active placement or null, next kind or null, score, total cleared lines, level, and accumulated gravity milliseconds. Stable states have no completed board row. Score and total lines are nonnegative integers, and level follows G05. In playing/paused, the active placement is legal, next kind is present, and accumulated gravity is finite and in `[0, current interval)`. Ready/over have no active piece.

| Status | State and permitted session actions |
| --- | --- |
| ready | Empty board, null active/next, score 0, lines 0, level 1, gravity accumulator 0. Start begins play. |
| playing | Gameplay commands and time advances apply. Pause preserves the game; Restart starts a fresh game. |
| paused | Board, active/next, score, lines, level, and partial gravity interval are preserved. Resume returns to playing; Restart starts fresh. |
| over | Final locked board and counts remain, active is null, gravity accumulator is 0, and next retains the failed-spawn candidate. Restart starts fresh. |

Start is ignored outside ready. Restart is accepted in playing/paused/over and ignored in ready. Pause and Resume are accepted only in playing and paused respectively. Other inapplicable session actions are ignored. Valid ignored inputs consume no piece draws and do not change rule state.

Creating ready state consumes no draws. Start/Restart reset board/counts/level/timer, consume two kinds in order, spawn the first, and retain the second as next. Restart uses the current supplied source; it does not reset that source's sequence.

Production draws are independent. The adapter maps a fresh random `u` in `[0,1)` to index `floor(7*u)` in `[I,J,L,O,S,T,Z]`. Repeats are legal; there is no bag/history constraint. Tests can supply any valid fixed kind sequence.

A spawned piece has orientation 0, origin y=0, and origin x=`floor((10-size)/2)`. After locking and line/progression updates, promote the current next kind. If its spawn is legal, make it active, reset its gravity accumulator, and draw one replacement next kind. If blocked, enter over, retain that candidate as next, and consume no replacement draw. No overlapping active piece is exposed.

## G03 — Movement and rotation

A left/right command proposes origin x minus/plus one, keeping y/orientation. A rotate command proposes the clockwise geometry from G01 at the same origin. Commit the candidate only when legal; otherwise leave placement, score, timer, and piece-source consumption unchanged. Rotation does not try translated positions or wall kicks.

A soft-drop command attempts origin y+1. A legal move advances one row and adds one score point. A blocked downward attempt immediately performs G04 locking. Gameplay movement/rotation/drop commands are ignored outside playing.

Successful manual movement/rotation/soft drop preserves the existing partial gravity interval. An O rotation is a no-op. Horizontal or rotation input never locks a piece.

## G04 — Gravity, locking, and line removal

The gravity interval in milliseconds is `max(100, 1000 - 100*(level-1))`. At level 1 it is 1000 ms; at level 10 and above it is 100 ms.

A time advance accepts finite, nonnegative milliseconds. While playing, accumulate time and perform one downward attempt at each completed current gravity interval, including an exact boundary. A legal gravity move advances one row without adding drop points. A blocked gravity attempt immediately locks.

Merely becoming grounded does not lock. The piece locks on the next failed downward attempt, whether caused by gravity or soft drop. There is no separate grounded lock-delay timer or hard-drop command.

Locking writes the four active cells to the board, removes all completed rows simultaneously, updates counts/score/level under G05, and attempts the next spawn under G02. Surviving rows keep their order; empty rows are inserted at the top to retain 20 rows. A transition exposes only its completed result.

A successful spawn resets that piece's gravity accumulator. Unconsumed time in the same advance continues on the new active piece using its new level interval. A partial interval remains with the current piece. Entering over discards remaining advance time. Advance outside playing preserves rule state and consumes no draws.

## G05 — Score and progression

A successful soft-drop cell adds one point; a blocked soft drop adds no drop point. Horizontal movement, rotation, and gravity moves add no points.

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

Core inputs are the session/gameplay commands described above or a time advance carrying milliseconds. Board/state/placement values must satisfy G01–G02; valid fixtures may construct such values directly. These internal values are caller preconditions: malformed constructed state is outside the supported core boundary, with no required runtime detection or exception class. Board helpers may receive full rows to exercise compaction; stable engine states are post-clear states.

Unknown commands, a nonnumeric advance value, or a piece-source result outside the seven kinds throw TypeError. Negative, NaN, or infinite numeric advances throw RangeError. Input shape/value validation precedes the status-dependent ignored-input rule. A piece-source exception propagates from the engine.

Rejected input or a failed source call publishes no resulting state; the caller's prior state/snapshots remain unchanged. External source side effects are not rolled back. A valid transition may reuse unchanged read-only values, but may not mutate an earlier state/snapshot.

Given identical valid initial state, command/time sequence, and piece-kind draws, results are identical. Core rules read neither browser time nor ambient randomness. Browser operation catches failures as required by parent R05; direct core callers observe these specified errors.

## Canonical acceptance cases

| ID | Required result |
| --- | --- |
| K01 | Every initial shape has four distinct cells. Four clockwise turns restore I/J/L/S/T/Z, and O is unchanged. Matrix origins and occupied-cell bounds follow G01. |
| K02 | With draws I,T,O, Start consumes I/T, and a successful subsequent promotion makes T active and O next using exactly one additional draw. A blocked T spawn consumes no O draw and exposes over with no active cells. |
| K03 | A vertical I at the left edge with origin x=-2 is legal; its candidate horizontal rotation beyond the wall is rejected at the same origin. Blocked left/right/rotation leave timer/score/draws unchanged. |
| K04 | At level 1, advances 999 then 1 ms produce exactly one downward attempt. A legal landing remains active; a following blocked gravity or soft-drop attempt locks immediately. |
| K05 | Valid 1–4-line fixtures award 40/100/300/1200 at level 1, preserve survivor-row order, and maintain board height. The 9-to-11-line example produces score +100 and level 2/900 ms. |
| K06 | Paused/over advances and ignored commands change no rule values/draws. Resume preserves the partial gravity interval. Restart clears game state and consumes the next two source kinds. |
| K07 | Invalid advance/command/source cases yield G06 errors without modifying prior state. Replaying the same valid inputs/draws yields the same state and snapshot values. |

These are behavioral acceptance cases, not evidence of implemented tests.
