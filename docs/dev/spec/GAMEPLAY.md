# Gameplay contracts

This child of [SPEC.md](../SPEC.md) owns the complete core gameplay contract. Browser input mapping, clock adaptation, presentation, runtime failure handling, and system acceptance remain in the parent.

## G01 — Board and tetromino data

The visible board has 10 columns and 20 rows, with integer x increasing right from 0 through 9 and y increasing down from 0 through 19. There are no hidden rows. Each row-major cell is null or `{kind, variant}`, where kind is one of `I, J, L, O, S, T, Z` and variant is `regular` or `penetrator`. Variant preserves locked-block appearance only; every non-null cell obstructs regular pieces equally. The active piece is separate from the locked board.

A piece placement has kind, variant, orientation and integer origin x/y. Origins may be negative when all occupied cells fit. A **valid** placement has all four occupied cells inside the board and on empty board cells. Regular active placements must be valid. A penetrator active placement may overlap occupied cells transiently, but all four cells must be in bounds and there must be at least one valid placement at its current origin y or below, keeping x/orientation fixed. Call this **traversable**. G10 owns penetrator movement; locking always requires valid cells. Only the active piece can traverse; locked board cells never move through one another.

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

Core state contains status, board, active placement, next kind/variant, held kind/variant, ordered bag remainder, hold-used flag, settings from CONFIGURATION C01, penetrator bonus count, score, lines, level, gravity milliseconds, and grounded milliseconds or null. Stable states have no completed board row. Score/lines are nonnegative integers and level follows G05. In playing/paused, active is valid if regular or traversable if penetrator, next is present, and gravity time is finite in `[0, interval)`. Grounded means the next one-row descent is not admissible under G03/G10. Grounded time is finite in `[0, interval]` exactly when grounded, otherwise null. It tracks contact age under both general policies, capped at the current interval; at that cap the interval policy locks on the next time advance, including zero, before consuming elapsed time. Stable interval-policy states reach that cap only through a general-policy selection; ordinary advancement resolves its deadline atomically. Bonuses are integers 0–3. Ready/over have no active piece and zero gravity/null grounded time. Bag remainder contains distinct canonical kinds; G07 defines consumption.

| Status | State and permitted session actions |
| --- | --- |
| ready | Empty board/bag, null active/next/held, unused hold flag, zero bonuses, score/lines 0, level 1, zero gravity/null grounded time. Start begins play. |
| playing | Gameplay commands and time advances apply. Pause preserves the game; Restart starts a fresh game. |
| paused | Board, pieces, bag, hold flag, metrics, and both partial timers are preserved. Resume returns to playing; Restart starts fresh. |
| over | Final locked board and counts remain, active is null, gravity accumulator is 0, and next retains the failed-spawn candidate. Restart starts fresh. |

Start is ignored outside ready. Restart is accepted in playing/paused/over and ignored in ready. Pause/Resume apply only in playing/paused respectively. Other inapplicable actions are ignored. Valid ignored inputs consume no random samples and change no rule state. A valid one-key setting update applies in every ordinary state under CONFIGURATION C03; selecting the same value changes no core state. All other inapplicable actions consume no samples, bonuses or points.

Playing/paused next is always regular; over may retain a failed held penetrator candidate. An empty held slot has null kind/variant. Creating ready state consumes no samples and uses the supplied validated settings, or C01 defaults when omitted. Start/Restart reset board/counts/level/timers, empty the bag and held slot, clear hold-used and bonuses, consume two G07 kinds in order, spawn the first, and retain the second as next. Preserve all selected settings. Restart uses the current injected numeric source without rewinding it; the fresh bag is independent of the discarded remainder.

A new queue piece is regular. A spawned piece has orientation 0, origin y=0, and origin x=`floor((10-size)/2)`. A valid regular spawn resets gravity to zero and grounded time to zero if already grounded, otherwise null. After a lock and line/progression updates, promote next. If valid, clear hold-used and consume one replacement next kind. If blocked, enter over, retain that candidate as next, and consume no replacement kind/sample. No overlapping active piece is exposed. G08 defines variant-preserving hold replacement at the same canonical spawn geometry; a held penetrator uses the traversable spawn condition.

## G03 — Movement and rotation

A left/right command proposes origin x minus/plus one, keeping y/orientation. For regular pieces, commit only a valid candidate; penetrators use G10's traversable condition. Clockwise rotate proposes the next G01 orientation and tests the following origin offsets in order, using board coordinates with positive y downward. Commit the first admissible candidate under the piece's variant; all failures preserve the original placement, score, and timers and consume no randomness. These tables and G01's visible-board rule define the complete kick contract; there are no counterclockwise or 180-degree controls. O rotation is a no-op.

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

A soft-drop command attempts origin y+1. An admissible move advances one row and adds one point. A blocked attempt adds no drop points: with `generalLock=attempt` it atomically locks the current valid piece; with `generalLock=interval` it preserves placement and grounded age. Penetrators use G10 admissibility, so an occupied next row alone is not a blocked attempt. Movement/rotation/soft-drop inputs are ignored outside playing. Successful manual placement changes preserve gravity progress and reconcile grounding under G04. Pure command rejection does not advance time; browser elapsed-before-command processing is defined separately in R03.

## G04 — Gravity, locking, and line removal

The gravity interval in milliseconds is `max(100, 1000 - 100*(level-1))`. At level 1 it is 1000 ms; at level 10 and above it is 100 ms.

A time advance accepts finite, nonnegative milliseconds. Gravity progress and, while grounded, grounded elapsed time advance together. At each completed gravity interval, reset gravity progress to zero and attempt one downward move. An admissible gravity move adds no drop points. A blocked attempt locks immediately with `generalLock=attempt`; with `generalLock=interval` it preserves placement and grounded age.

Becoming grounded starts grounded elapsed at zero immediately, including after spawn, movement, a kick, soft drop, or hard drop. Track contact age under both policies, capping at one full current interval. With `generalLock=interval`, lock when age reaches that interval, including the exact boundary. With `generalLock=attempt`, age alone does not lock; the first failed gravity or soft-drop attempt locks. Every grounded penetrator is valid because no lower traversable position remains. A move that remains grounded preserves elapsed delay, even if the supporting cells change; blocked moves and zero-distance delayed hard drops do not restart it. Becoming airborne cancels grounded elapsed; later grounding starts a new full interval. There is no move-reset count or independent browser lock timeout.

Process time chronologically to the next gravity event or enabled interval-lock deadline, then continue with remaining elapsed time. A due interval lock, including a capped age preserved by a policy change, is processed before any supplied milliseconds, even for advance(0). An interval lock wins an exact tie with gravity; an attempt-policy blocked gravity event locks once. The same piece is never processed twice at that timestamp. A gravity move that first grounds a piece starts its timer at that event time, not at the end of the supplied advance. Pause preserves both accumulators. G09 defines hard drop under both general policies and the immediate override. Selecting settings does not itself advance time or lock. General-policy changes preserve gravity/grounded age; if interval locking is selected at capped age, the next playing advance resolves the due lock before a browser action under R03.

Locking requires valid cells, writes the four active kind/variant cells to the board, removes all completed rows simultaneously, updates counts/score/level under G05 and awards the G10 bonus, and attempts the next spawn under G02. Surviving rows keep their order; empty rows are inserted at the top to retain 20 rows. A transition exposes only its completed result.

A successful spawn resets the new piece's timers under G02. Unconsumed elapsed time continues on that piece using its resulting level interval. A partial interval remains with the current piece. Entering over discards remaining advance time. Advance outside playing preserves rule state and consumes no samples.

## G05 — Score and progression

A successful soft-drop cell adds one point; hard drop adds two points per cell actually descended. A blocked/zero-distance drop adds zero drop points. Hold, penetrator activation, ghost projection, setting changes, horizontal movement, rotation/kicks, and gravity add no points.

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

Core inputs are the session/gameplay commands above, a one-key setting update using CONFIGURATION C01's typed key/value pairs, or a time advance carrying milliseconds. Board/state/placement/bag values satisfy G01–G02, G07 and G10; valid fixtures may construct them directly. Internal values are caller preconditions: malformed constructed state is outside the supported boundary, with no required runtime detection or exception class. Board helpers may receive full rows for compaction; stable engine states are post-clear states.

Unknown commands, malformed setting updates/unknown keys/unsupported values, nonnumeric advances, or nonnumeric random results throw TypeError. Negative/NaN/infinite numeric advances, or numeric random samples outside finite `[0,1)`, throw RangeError. Input shape/value validation precedes the status-dependent ignored-input rule. A random-source exception propagates. Validate random results when a sample is actually required; ignored actions and held-piece swaps do not sample or probe the source.

Rejected input or a failed source call publishes no resulting state; the caller's prior state/snapshots remain unchanged. External source side effects are not rolled back. A valid transition may reuse unchanged read-only values, but may not mutate an earlier state/snapshot.

Given identical valid initial state, command/setting/time sequence, and numeric random samples, results are identical. Core rules read neither browser time nor ambient randomness. Browser operation catches failures as required by R05; direct core callers observe these specified errors.

## G07 — Seven-bag selection

To consume a kind, remove the first element of the current bag remainder. If empty, start with `[I,J,L,O,S,T,Z]` and shuffle: for i from 6 down through 1, sample u, set j=`floor(u*(i+1))`, and swap positions i/j. This uses exactly six validated numeric samples per refill; no seventh sample is required. Consume from the resulting front. Within one round, each complete consecutive group of seven queue-consumed kinds is a permutation; bag boundaries may repeat a kind. Restart starts a new round and restarts this grouping. Holding or locking does not reshuffle a nonempty remainder.

Start consumes active and next; a successful next promotion consumes one replacement; an empty-slot hold consumes one replacement only after its regular spawn is valid. An occupied-slot swap, blocked spawn, rejected/ignored command, rendering, ghost projection, or pause consumes none. These rules count queue kinds separately from random samples and from pieces placed on the board: hold can change placement order. Restart discards the old remainder and fills a fresh bag.

## G08 — Hold

Hold applies only while playing, `hold=on` and hold-used is false. Save the outgoing active kind and variant, not its position, orientation or timers. An activated piece retains penetrator identity while held and when retrieved, until it locks; holding neither refunds nor spends a bonus. With an empty slot, use next as the replacement; with an occupied slot, use held kind/variant and leave the ordinary next queue untouched. Attempt replacement at G02's canonical spawn with fresh timers. A valid regular or traversable penetrator replacement succeeds. A successful empty-slot replacement then consumes one replacement next; a successful occupied-slot swap consumes no kind/sample. Set the held kind/variant to the outgoing piece and mark hold-used true.

If replacement spawn is blocked, enter over without a replacement draw, with no active/ghost cells, the outgoing kind/variant in Hold, and the failed candidate in Next. The locked board/counts remain unchanged. Hold does not lock or score the outgoing active piece. A successful lock/promotion permits hold again; a hold replacement itself does not. Restart empties Hold and resets eligibility. Disabled/paused/used/inactive hold is an ignored action with no state or random-consumption change.

## G09 — Ghost and hard drop

For a regular active placement, landing is the greatest nonnegative vertical distance d for which every step through d is valid. For a penetrator, enumerate all in-bounds origins at the current y or below with unchanged x/orientation, and choose the greatest y whose cells are valid. Do not stop at the first obstruction or first valid cavity. G10 guarantees such a destination exists. One variant-aware landing result supplies both ghost and hard drop.

Ghost derivation is read-only: no time, samples, bonus, eligibility or state change. In playing/paused, `ghost=show` exposes the landing, `ghost=hide` exposes none, and `ghost=dynamic` exposes it only when the smallest occupied landing-cell y is at least 10. Rows 0–9 are the upper half, rows 10–19 the lower half; a straddling ghost is hidden. Recompute from current board/placement/settings after every transition, including movement, rotation, activation and hold. Visibility changes never affect placement. Ready/over expose no ghost.

Hard drop applies only in playing with `hardDrop=on`. Move the active placement to the shared landing, award 2*d points, preserve gravity progress and reconcile grounding under G04. With `hardDropLock=immediate`, atomically lock even for d=0. With `hardDropLock=follow` and `generalLock=interval`, retain the active piece until its grounded interval; d=0 preserves contact age. With `follow` and `generalLock=attempt`, the drop's final failed downward probe constitutes the failed attempt and locks immediately, including d=0. Penetrator probes use G10, passing intervening occupied cells. No hard drop destroys cells on its path. Disabled hard drop changes nothing, consumes nothing and scores nothing; ghost availability is independent of this toggle.

Changing the hard-drop override affects future hard-drop commands only, never a piece already at landing. Configuration C03 defines other live changes. Immediate hard-drop locking overrides only that command; ordinary gravity and soft drop follow the selected general policy.

## G10 — Penetrator activation, traversal and bonuses

Start/Restart set bonuses to zero. A lock clearing exactly four rows awards one bonus after removal, `bonuses = min(3, bonuses+1)`, regardless of the locked piece's variant. A 0–3-row clear earns none. Spend one bonus atomically to turn the current regular active piece into a penetrator, only while playing with bonuses > 0. Activation is ignored without a bonus, outside playing or when already a penetrator; ignored/repeated activation spends nothing. Successful activation changes only variant, bonus count and reconciled grounding/derived availability; it adds no points, moves no cells and consumes no randomness. The hotkey is defined in R02.

Left/right, one-row gravity/soft drop, and ordered rotation candidates for penetrators require traversable cells under G01: inside bounds and at least one valid destination at or below that proposed placement. Occupied cells are allowed during travel and are never removed, recolored or moved. Reject a proposal with no reachable valid destination, even if it is in bounds. This prevents falling beyond the last valid cavity into an un-lockable bottom overlap. G03's candidate order remains authoritative. Regular movement never gains these permissions.

Penetrator grounding means no traversable one-row descent exists, equivalently its current origin is the greatest valid origin at or below it. It therefore locks only on empty cells, through either G04 general policy or G09 hard drop. Reconcile grounding after activation/movement/rotation/hold: an airborne result cancels contact age; remaining grounded preserves it. A penetrator may keep travelling through overlap until the selected general policy locks at its valid landing. It cannot cross board bounds, move upward with hard drop or tunnel horizontally beyond the ordinary one-column command. Kicks may change x/y within the same admissibility rule.

A penetrator's identity follows the piece through Hold. On locking the ability ends; its locked cells retain the white visual variant but behave as ordinary obstacles. New queue pieces are regular (a failed held spawn in over retains its candidate variant for next preview); bonuses are not refunded by locking, holding, game over or disabling features. Restart clears active/held variants and bonuses. Locked penetrator cells, active/held penetrators and a visible penetrator ghost are white under R04. Bonuses alone, a hidden ghost and all settings have no random-consumption effect.

For a concrete traversal case, place locked cells at x=4,5 on rows 8 and 19 and an O at origin (4,0). Regular landing is y=6. Penetrator landing is y=17 (rows 17–18); y=7 may overlap row 8 during travel because a valid lower landing exists. A proposed y=18 is rejected because row 19 is occupied and no lower valid O fits. Hard drop reaches y=17 for 34 drop points and leaves all four original locked cells unchanged. Its dynamic ghost is visible; the regular y=6 ghost is hidden.

## Canonical acceptance cases

| ID | Required result |
| --- | --- |
| K01 | Every initial shape has four distinct cells. Four clockwise turns restore I/J/L/S/T/Z, and O is unchanged. Matrix origins and occupied-cell bounds follow G01. |
| K02 | Samples that leave canonical bag order make Start consume I/J; a successful promotion makes J active and L next. The initial refill uses six samples; that promotion uses none. A blocked J spawn consumes no L kind/sample and exposes over without active cells. |
| K03 | A vertical orientation-1 I at origin x=-2 is legal; clockwise 1→2 rejects the first two candidates and accepts the +2-x kick on an empty board. All-blocked movement/rotation preserves placement, timers, score, bag and hold state. |
| K04 | At level 1, 999 then 1 ms produce exactly one gravity attempt. On first grounding, 999 ms leaves active and the next 1 ms locks. With interval policy, blocked soft drop does not lock or reset the delay; attempt policy locks on that failure. The full interval is also honored at faster levels. |
| K05 | Valid 1–4-line fixtures award 40/100/300/1200 at level 1, preserve survivor-row order, and maintain board height. The 9-to-11-line example produces score +100 and level 2/900 ms. |
| K06 | Paused/over advances and ignored commands preserve rule values/samples. Resume preserves both partial timers. Restart clears board/hold/bag/counts, fills a fresh bag for active/next, and preserves settings without rewinding the random source. |
| K07 | Invalid advance/command/setting/sample cases yield G06 errors without modifying prior state. Injected source failure during refill publishes no partial state; external source effects need not roll back. Identical inputs/samples replay identically. |
| K08 | Each shuffled bag contains every kind once; consuming two complete bags uses twelve samples and emits fourteen queue kinds. Boundary repeats are permitted and restart begins a new grouping. Start/lock/empty hold consume as G07; swaps, projections, rejections and blocked spawns consume none. |
| K09 | Empty hold stores and promotes; occupied hold swaps without consuming next; replacement resets orientation/position/timers. Used hold is ignored until a lock, canonical blocked replacement enters over, and restart empties the slot; disabling hold preserves stored kind/variant and eligibility until enabled again. |
| K10 | Ghost and delayed hard drop use identical landing cells on empty and obstructed boards and after kicks/hold. Hard drop awards 2*d, preserves gravity progress, and follows the selected general/override policy including d=0. Projection mutates nothing. |
| K11 | Every family/transition tries its five candidates in order. A horizontal I at origin (3,18) rotates 0→1 on an empty board using its fifth offset (1,-2); all-blocked cases reject. O is unchanged; no occupied cell may leave visible bounds. |
| K12 | Grounded lateral/kicked moves preserve elapsed delay; lifting cancels and regrounding starts at zero. Pause freezes both timers; an exact gravity/lock tie locks once; remaining time advances the promoted piece with its new interval. Setting changes alone neither lock nor reset timers; a capped contact age becomes due on the next playing advance if interval locking is selected. |
| K13 | The G10 O fixture passes row-8 overlap, reaches y=17 with ghost/drop agreement and 34 drop points, rejects y=18, and preserves every original locked cell. Later valid cavities below earlier ones are considered; all final cells remain in bounds and empty. |
| K14 | Four-line locks by either variant award exactly one bonus at counts 0/1/2, stay at 3 when capped, and 0–3-line clears award none. Activation spends exactly one, repeated/already-active/zero-bonus/paused activation spends none, and Restart clears all bonuses. |
| K15 | Activated pieces keep their variant through empty hold and occupied swap without extra spending; a held penetrator can spawn traversably through overlap when a lower valid landing exists. A spawn with no valid landing enters over without a replacement sample. Lock ends traversal, preserves white locked cells, and promotes a regular queue piece. |
| K16 | A piece grounded by soft drop with gravity progress 600 ms locks on the next failed gravity attempt after 400 ms in attempt policy, but after 1000 grounded ms in interval policy. Soft-drop failure and follow hard drop lock under attempt; override always locks. Live policy changes preserve age/progress; capped age followed by selecting interval locks on advance(0). |
| K17 | Dynamic ghost is hidden for smallest landing y=9 (including a 9/10 straddle) and visible at y=10; show/hide override this independently of hard-drop enablement. Disabled hold/drop commands and preview/ghost changes leave queue, variants, bonuses, timers and metrics unchanged except their intended setting/projection. |

These are behavioral acceptance cases, not evidence of implemented tests.
