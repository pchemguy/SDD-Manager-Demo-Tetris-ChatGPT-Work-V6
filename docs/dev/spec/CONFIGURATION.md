# Configuration contracts

This child of [SPEC.md](../SPEC.md) owns all six setting values, side-panel configuration behavior, per-setting resolution and cookie/URL persistence. [GAMEPLAY.md](GAMEPLAY.md) owns the gameplay effects, including locking, hold and ghost projection. Browser resources and save feedback retain the boundaries in ARCHITECTURE and DECOMPOSITION.

## C01 — Setting schema and defaults

Keys, URL parameter names and values are case-sensitive. The internal key and query parameter are identical. Boolean choices use the strings `on` and `off`, not truthiness or numeric aliases.

| Key / URL parameter | Cookie name | Allowed values | Default | Accessible control name |
| --- | --- | --- | --- | --- |
| ghost | tetris_ghost | show, dynamic, hide | show | Ghost visibility |
| hardDrop | tetris_hardDrop | on, off | on | Hard drop enabled |
| next | tetris_next | on, off | on | Next-piece preview |
| hardDropLock | tetris_hardDropLock | follow, immediate | follow | Hard-drop locking policy |
| generalLock | tetris_generalLock | interval, attempt | interval | General locking policy |
| hold | tetris_hold | on, off | on | Hold enabled |

Use selects for ghost and both locking policies, and checkboxes for the three on/off settings. Ghost labels are Always show, Hide above midline, Always hide. Hard-drop locking labels are Follow general policy and Lock immediately. General locking labels are Full gravity interval and Failed downward attempt. All six controls live in a named Game configuration side-panel group and remain enabled in ready, playing, paused and over; R05 fatal failure disables them.

Core initialization accepts a complete validated C01 settings object, or uses defaults when the entire argument is omitted. Missing/unknown keys or invalid values in a supplied object throw TypeError before any sample or state publication.

Defaults preserve enabled hold/drop/next, visible ghost and full-interval grounding with the hard-drop override following the general policy. Selection of a general policy is independent of the override. Disabling hard drop leaves both policy controls usable and retains their values for re-enabling.

## C02 — Independent startup resolution

Before core initialization or first presentation, extract each parameter's first value with URLSearchParams.get semantics; absence is distinct from an explicit string equal to default. Later duplicate values do not participate. Extract each exact cookie name from document.cookie by splitting semicolon-separated pairs, trimming surrounding name/value whitespace and decoding its value once. If duplicate exact cookie names are present, use the first pair. Unknown keys/cookies are ignored.

For each of the six keys independently:

1. If the first URL value is allowed by C01, select it and enqueue a write of that value to its corresponding cookie, even if it equals default or the cookie already matches.
2. Otherwise, if the saved cookie value is allowed, select it.
3. Otherwise select the C01 default.

An empty, unsupported, incorrectly cased or malformed value is invalid for that key and falls through to the next source; it does not affect any other key. A malformed cookie escape makes that cookie invalid. URL extraction follows standard URLSearchParams decoding; perform no additional percent decoding. Validation never coerces a value. An invalid first URL value does not permit using a later duplicate.

Only valid explicit URL values cause startup cookie writes. Omission or an invalid URL value must not overwrite any saved cookie with a default. Startup does not insert missing parameters, rewrite the URL, or create default cookies. Valid cookie selections remain saved as they were. Resolve all values even if one read/write fails; C05 governs unavailable browser operations.

For example, with cookies ghost=hide, hold=off and next=off (using their C01 cookie names), opening `?ghost=show&hardDrop=off` selects show/off/off/follow/interval/off in C01 row order. It writes only tetris_ghost=show and tetris_hardDrop=off. The omitted next and hold parameters retain off; the explicit default ghost=show replaces the older hide cookie. A subsequent open without query parameters restores those saved choices.

## C03 — Live setting changes

A native change event supplies one typed key and allowed value. The controller first processes R03 elapsed time under the old settings, then applies that one core update, then attempts that key's cookie write and URL update independently, and presents one frame with the selected value and resulting save feedback. Direct core updates validate key/value before status-dependent handling under G06. A same-value core selection is a no-op, but an explicit player setting event still saves the selected value to both destinations.

The setting update itself does not restart the game, consume a piece/sample/bonus, award points or move focus from the edited control. The preceding elapsed advance can perform ordinary gameplay transitions under the old settings. Effects apply to the resulting snapshot and future commands:

- ghost changes only visibility; dynamic uses GAMEPLAY G09's uppermost landing cell, including for penetrators.
- hardDrop=off disables the Hard drop action and ignores D/drop commands; it leaves ghost calculation available.
- next=off hides both next geometry and next-kind text, displaying Next: Off; the queue and random-consumption rules remain intact.
- hardDropLock affects future drops only; selecting immediate never locks a piece already at landing.
- generalLock preserves gravity progress and grounded age. It does not itself lock. With interval selected at capped age, the next playing advance (even zero) resolves the due lock under G04; under attempt, only a failed downward attempt or immediate hard drop locks.
- hold=off disables Hold/C and hides held geometry/kind with Hold: Off, preserving the stored kind/variant and hold-used eligibility. Re-enabling restores that state; it grants no extra hold.

Pause permits all six changes without advancing gameplay. Start/Restart preserve selected settings; Restart resets gameplay, held variants and bonuses. Reload/reopen creates a fresh ready game and resolves settings again under C02; board, score, bag, held pieces and bonuses are never persisted. External URL editing/back navigation is read when the page is opened/reloaded; no live popstate synchronization is required.

## C04 — Cookie and URL writes

Store one cookie per key with the C01 name and canonical encoded value, `Path=/; Max-Age=31536000; SameSite=Lax`. Add Secure on HTTPS; omit it on supported HTTP development servers. Omit Domain to keep the cookie host-scoped. JavaScript must read the cookie; HttpOnly is not used. These cookies contain only setting values and work at both supported root and non-root application prefixes on that host.

After an accepted player event, set the corresponding parameter to the selected canonical value in the current URL, using URLSearchParams.set semantics so duplicate occurrences of that managed key collapse to one. Preserve origin, path, fragment and all unrelated parameter names/values, including other managed settings not being changed. Equivalent standard URL encoding/serialization is permitted; byte-identical query spelling is not required. Retain the existing history.state and use history.replaceState without navigation or a new history entry. A choice equal to default is written explicitly to both destinations; do not delete its parameter or cookie as an optimization.

Cookie and URL writes are attempted separately; failure of one never suppresses the other. After writing a cookie, read back its value to detect a silently refused write. A verified matching value counts as saved; inaccessible, missing or different readback counts as failure. Save attempts occur only at startup for valid URL values and on accepted player setting events, not on each frame, gameplay command or render.

## C05 — Unavailable persistence and validation failures

Cookie read failure treats cookies as unavailable and still resolves every key from its URL/default. URL read failure treats URL values as unavailable and still resolves cookies/defaults. Ordinary invalid strings fall through under C02 and do not produce a fatal alert. Startup explicit-URL cookie writes are independent per key; one failed write does not stop later writes or startup.

A cookie write/readback or history update failure preserves the selected in-memory value and the successfully saved destination. Never roll back a selected value, restart play, discard bonuses or enter R05's fatal state for a persistence failure. Expose a concise HTML status: Settings could not be fully saved; they remain active for this game. This status uses a polite live region and remains until a later explicit load/save operation completes with no persistence failure. If a failed key's cookie was stale but the URL save succeeded, reload of that URL still uses the explicit URL; opening without it can restore the older cookie. Do not promise persistence when a destination failed.

The adapter reports only safe per-destination outcomes and feedback, never raw exceptions or cookie dumps. The controller owns this save status outside the immutable gameplay state. Disposal prevents stale callbacks from changing settings, saving or rendering. Invalid direct core key/value inputs follow G06; browser extraction of invalid strings follows C02 instead.

## C06 — Configuration acceptance cases

| ID | Required result |
| --- | --- |
| P01 | With no URL/cookies all six defaults are selected and no startup save is issued. For each key, a valid URL wins over a conflicting cookie; a valid cookie wins when its parameter is omitted; no value uses default. Mixed-source resolution does not couple the keys. |
| P02 | Every explicit allowed URL value, including each default, is written to its corresponding cookie. Omitted keys are not written. Invalid/empty/case-mismatched/duplicate URL cases and malformed cookies follow C02 independently without erasing saved values. |
| P03 | Each of the six controls works natively in all ordinary states. A player change writes exactly its cookie and parameter, including defaults, preserves other query data/path/hash/history state, causes no navigation, and reload/reopen restores the selected values. |
| P04 | Hold/drop off gates both button and hotkey/core command. Next/hold off hide geometry and kind text while retaining queue/held variant/eligibility. Re-enabling restores that state. Ghost visibility and both general/drop locking choices agree with G04/G09 and the controls. |
| P05 | Cookie read/write/refusal and URL read/history failures are isolated. Each viable destination is still attempted; selected settings/gameplay remain active, feedback is accessible, and later successful explicit saves clear feedback. Fatal runtime failures retain R05 handling. |
| P06 | Restart preserves all settings but clears gameplay/bonuses/held variants; reload/reopen starts ready with per-key precedence. Cookie attributes and HTTP/HTTPS handling work at root/non-root prefixes. No state or default-cookie save is triggered by gameplay/rendering/disposal. |

Run pure resolution/validation and controller ordering/failure cases deterministically. Run native controls, real cookies/history, reload/reopen, URL preservation and accessible feedback through the production components in both required browsers; fault fixtures remain outside production assets.

## Browser API references

- [Document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)
- [URLSearchParams.get](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/get)
- [History.replaceState](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState)

These references describe browser mechanisms. C01–C06 define this project's selected values, precedence, lifecycle and failure policy.
