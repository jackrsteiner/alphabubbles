# Alphabubbles — Bugs found & fixed

A log of defects discovered (during the July 2026 feature round) and how they
were fixed. Issue numbers refer to the GitHub tracker.

---

## 1. Phonetic letter sound often silent on keypress (#4)

**Symptom:** pressing a letter key frequently produced no sound, breaking the
"key→sound bond is sacred" rule.

**Root causes** (all in `Audio.speak`):
1. `speechSynthesis.cancel()` immediately followed by `speak()` in the same
   tick silently drops the new utterance in Chromium — a long-standing
   browser bug. Every press cancelled the previous utterance, so fast or
   even moderate typing was mostly silent.
2. Chromium's synthesizer can wedge in a *paused* state after inactivity;
   queued utterances then never play.
3. No `lang`/voice was set on the utterance; some browsers stay silent until
   a voice is explicitly chosen after `voiceschanged`.

**Fix:** cancel only when something is actually speaking/pending and issue
the new `speak()` on the next tick; call `resume()` before every `speak()`;
set `lang = "en-US"` and pick a local English voice once voices load.
Word-completion speech uses `interrupt: false` so the sounded-out letters
queue instead of cancelling each other.

## 2. Cloud clipped flat on top (#8)

**Symptom:** on desktop the cloud's fluffy top lumps were sliced off flat.

**Root cause:** the hole-punching `<mask>` used a white "keep" rect of
exactly `0 0 width height`, but the cloud's upper lumps extend above
`y = 0` (a lump at `cy − 34` with `r ≈ 147` reaches ≈ −21 SVG units).
Anything outside the mask's white rect is masked to black, i.e. cropped —
even though the SVG itself had `overflow: visible`.

**Fix:** the mask (and its white rect) now extends one full viewBox beyond
the drawing on every side, so it only ever punches letter holes and can
never crop the silhouette.

## 3. Rapid double-press of the target key skipped a letter (#10)

**Symptom:** pressing the correct key twice quickly made the word skip its
next letter (e.g. both bubbles filled slot 0 of MOM and `advance()` ran
twice, jumping the active slot from 0 to 2).

**Root cause:** `handlePress` captured the slot at press time but only
advanced on bubble *arrival* (~1.4 s later), so two presses before landing
both counted as "the target".

**Fix:** a `state.inFlight` guard — while a correct bubble is rising,
further presses are treated as non-target (they float by as strays). The
flag clears on arrival, right before the slot advances.

## 4. Physical presses of unlearned letters were not inert (#10)

**Symptom:** DESIGN.md §4 says "blank (not-yet-learned) keys are inert —
pressing them does nothing", and on-screen taps honored that, but the
`keydown` handler forwarded **every** A–Z key to the game, so unlearned
letters played sounds and floated bubbles out of blank keys.

**Fix:** `handlePress` now checks the lit set (`Keyboard.isLit`) for both
input paths, so physical and on-screen presses behave identically.

## 5. Browser keyboard shortcuts were hijacked (#10)

**Symptom:** the `keydown` handler `preventDefault()`ed any A–Z key even
with Ctrl/Cmd/Alt held, so e.g. Cmd/Ctrl+R spawned a bubble and interfered
with the shortcut.

**Fix:** key events with a modifier held are ignored entirely (the modifier
keys themselves just nudge their decorative keycap).

## 6. Minor cleanups

- `font-family: var(--font-display)` was set as an SVG **presentation
  attribute**, where `var()` is invalid. It only worked by accident (the
  invalid attribute was ignored and the font inherited from the page CSS).
  It is now set via the `style` attribute on every SVG glyph, where custom
  properties are legal — the gap, guide, fill and bubble are guaranteed to
  use the same font.
- `launchStray` re-read candy colours from `getComputedStyle` on every
  press; it now reuses the cached `CANDY` palette.
- The filled-letter "settle" animation scaled around the SVG origin
  (letters visibly zoomed in from off-position); `transform-box: fill-box`
  pins the pop-in to the glyph's own centre.
- `CONFIG.bannerMs` was defined but never used; removed (the banner hides
  when the next level starts).
