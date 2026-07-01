# Alphabubbles — Design Decisions

A gentle alphabet + typing game for a ~3.5-year-old (Indi). This document
captures every design decision made so far so that future work (human or
agentic) has full context. It is the source of truth for *intent*; the code
is the source of truth for *implementation*.

---

## 1. Purpose & audience

- **Primary user:** a ~3.5-year-old learning the alphabet and where letters
  live on a keyboard. Pre-reader; recognizes some letter shapes; cannot yet
  locate keys on a QWERTY layout.
- **Secondary user:** the parent, who will keep editing the code (new words,
  real audio, difficulty tuning). Code readability/modifiability is a feature.
- **Single job of the game:** press the glowing key → a matching balloon
  floats up and completes the word in the cloud.

## 2. Core philosophy

- **No violence.** No shooting, no aliens, no killing. The Space Invaders
  "attack the letter" idea is *metaphor only*. Balloons float **up** into
  letter-shaped gaps; nothing is destroyed.
- **No failure, no pressure.** No timers, no scores, no losing, no scolding.
  Wrong presses are never punished.
- **The key→letter→sound bond is sacred.** Pressing a key must always make
  that letter appear/behave and always play its sound. Responsiveness is
  non-negotiable — sound fires on *every* press, even when balloons are capped.
- **Capitals only.** On-screen letters are uppercase so they match the
  physical keyboard exactly (which is the point — learning key positions).
  Capitals are also less ambiguous across fonts than lowercase.

## 3. Core gameplay loop

1. A word is shown as **letter-shaped gaps in a single cloud** at the top.
   Each gap faintly shows its target letter as a guide.
2. The **active slot** (next letter to fill) glows; the **matching key** on
   the wireframe keyboard glows too. Both the goal balloon/gap *and* the
   glowing key are shown — deliberate redundancy that helps a young child
   match on-screen shape to key position.
3. Child finds and presses the glowing key. A **letter-shaped bubble** (the
   letter's own glyph, drawn glossy and translucent) rises from the key and
   **nests into its matching gap, squishing into place and becoming a solid
   bright letter.** The phonetic sound plays on press; a soft **squish sound**
   plays on landing.
4. The glow advances to the next slot. Repeat until the word is complete.
5. On completion: one **bigger celebration** (banner + the whole word spoken).
   Reward is concentrated at word completion, not scattered per letter.
6. Next level begins.

### Matching model
- **Press picks the destination; balloon flies to it; it lands.** Because
  there is only ever one active slot, there's no ambiguity about which balloon
  counts, even amid chaos. (This resolved an earlier "press = progress" vs
  "collision = progress" debate — the single-active-slot design gives the
  satisfying cause-and-effect of a real landing without the ambiguity.)

### Wrong / extra keys
- Any non-target key still **plays its sound** and floats a balloon that
  **drifts up and off-screen** — no landing, **no pop**, no reward, no penalty.
- Rationale: popping wrong letters would over-reward mistakes. The asymmetry
  (correct = purposeful landing; incorrect = drifts away to nothing) is the
  intended reinforcement.
- A child mashing keys gets a gentle, joyful rain of letters + sounds. This
  "fun clutter" is a feature, not a bug.

## 4. The keyboard

- **Full physical QWERTY layout is always present as a wireframe** (every key
  in its true position), but **only "learned" letters show their letter and
  are pressable.** This teaches the real spatial map without 26+ symbols of
  clutter.
- **Blank (not-yet-learned) keys are inert** — pressing them does nothing.
  Keeps the world small and the target unmissable early on.
- **Persistence-lit:** once a letter is learned it stays lit forever. Derived
  from the level list in code, so it can never drift out of sync.
- **Difficulty curve is emergent:** as more letters light up, the "find the
  glowing key" hunt naturally gets harder. No separate difficulty system.
- The **target key glows/pulses** as a hint. Kept always-on for this age; the
  growing number of lit keys is what raises challenge, not removing the hint.
- A decorative, inert **space bar** completes the familiar keyboard shape.

## 5. The cloud & word structure

- **One cloud = one word.** Currently one cloud per level. Future multi-word
  phrases should use **one cloud per word** (each cloud is a word-unit), which
  teaches word separation visually.
- **Letter-shaped gaps** (not round holes): the gaps are the actual letter
  silhouettes cut out of the cloud.
- **Filled state:** the bubble *becomes* the letter — a solid, bright,
  colored letter plugging the gap. As the word fills, the cloud visibly
  transforms from faint outlines into a bright completed word ("look what I
  built").
- **Counters are cloud-islands:** the enclosed spaces of A/B/D/O/P/Q/R contain
  a suspended piece of cloud. This is intentional and charming, and falls out
  naturally from using the font glyph as the mask.

## 6. Audio

- **Phonetic sounds, not letter names** (A = "ah", not "ay") — better for
  reading readiness.
- **Sourcing strategy:** recorded clips are the eventual *primary* source
  (ideally a parent's voice — meaningful and phonetically correct, since
  browsers can't reliably synthesize clean phonemes). **Browser TTS is an
  automatic per-letter fallback** so the game is fully playable *now* with no
  assets and never blocks on recording.
- **Drop-in contract:** the game looks for `sounds/a.mp3 … sounds/z.mp3`.
  Missing/failed files fall back to TTS per-letter, so a half-filled folder
  works. No code changes needed to upgrade the audio.
- **Autoplay:** browsers block audio until a user gesture, so the game opens
  with a **"Tap to start"** gate that unlocks audio.
- **Squish sound** (bubble landing) is **synthesized** via the Web Audio API
  (a short downward pitch bloop through a lowpass filter) — no audio file
  needed. The Web Audio context is created/resumed on the "Tap to start" tap.

## 7. Bubbles (the floating letters)

- The floating thing is a **letter-SHAPED bubble** — the letter's own glyph,
  rendered glossy and translucent — **not a balloon with a letter printed on
  it.** This makes the game cohere: the bubble *is* the letter.
- Because the bubble uses the **same glyph and size as the cloud gap** (sized
  via `Cloud.scale()`), it **fits the hole exactly** when it lands — alignment
  is guaranteed by construction, not by hand-tuning.
- On landing, the bubble does a quick **squish** animation and plays a soft
  synthesized **squish/squelch sound**, then hands off to the solid filled
  letter popping into the gap.
- **Multiple bubbles at once are desirable** — visual appeal / joyful chaos.
- **Live cap (~14)** keeps it from choking. Over the cap, new bubbles stop
  spawning but **sound still plays** (the key→sound bond must never feel dead).
- Correct bubble: rises from the key to its gap, squishes, triggers the fill.
- Stray bubble: drifts up on a lazy diagonal and off the top, then vanishes.
  No landing, no squish, no reward.
- The **target gap/bubble stays visually dominant** so the goal is never lost
  in the clutter.
- Naming note: the game is **Alphabubbles**; the code module is still named
  `Balloons` internally (functional legacy name) — cosmetic only.

## 8. Curriculum (level order)

Design rule: **each level adds at most 1–2 new letters and reuses old ones.**
Persistence-lit means the keyboard fills in gradually with no spikes. Words are
meaningful to the child before they are frequent; his **name comes third**,
right after the two most emotionally loaded words (MOM, DAD).

```
1. MOM    (+M, +O)          11. COW    (+W)
2. DAD    (+D, +A)          12. FOX    (+F, +X)
3. INDI   (+I, +N)  ← name  13. RUN    (+R)
4. CAT    (+C, +T)          14. LEG    (+L)
5. DOG    (+G)              15. JAM    (+J)
6. PIG    (+P)              16. KID    (+K)
7. SUN    (+S, +U)          17. YES    (+Y)
8. BUS    (+B)              18. VAN    (+V)
9. HAT    (+H)              19. QUACK  (+Q)  ← finale, a duck noise
10. BED   (+E)
```

- All 26 letters covered; never more than 2 new at once.
- **INDI** (nickname for Indigo): adds only I, N (D already lit from DAD); the
  repeated I gives a bonus rep on a brand-new letter.
- **QUACK** finale: chosen over QUIZ because it's a *sound the child can make*
  (concrete, playful, tied to a duck). By level 19 it adds only Q.
- Optional idea: **zero-new-letter "reinforcement" levels** (TOP, CUP, DOT,
  MOO) can be dropped in anywhere as confidence breathers.
- Variable word length + repeated letters (INDI, QUACK) must be supported by
  the slot UI (they are).

## 9. Visual / aesthetic direction

- **Setting:** a soft daytime sky (periwinkle → warm peach horizon) over a
  gentle green meadow that the keyboard sits on. Warm, playful, tactile — not
  a flat CSS-blue gradient.
- **Bubbles & filled letters:** a candy palette, cycled by slot index so each
  word is cheerfully multi-colored.
- **Type:** a chunky, rounded, friendly display font (Fredoka) with a system
  rounded fallback for offline use. Big, unambiguous letterforms matter at 3.5.
- **Signature moment:** the letter-shaped bubble rising and *becoming* the
  letter that plugs a hole in the cloud — the sky spelling the child's word
  back to him.
- **Motion:** gentle. Small settle/pop per letter; the one big celebration at
  word completion. `prefers-reduced-motion` is respected.

## 10. Platform notes

- **Primary target is a real computer with a physical keyboard** — that's the
  point of the game (learning key positions).
- **Tablet/touch support exists** (on-screen keys are tappable and routed
  through the same handler) but is a low priority; keep it only while it costs
  little. Do not optimize the design around touch.

## 11. Implementation notes (for future agents)

- **Single self-contained HTML file**, vanilla JS (no framework), so a parent
  can open and edit it easily. SVG for the cloud/letters, DOM/CSS for balloons
  and keyboard, `Audio` + `speechSynthesis` for sound.
- **Everything data-driven & tunable:**
  - `LEVELS` array = the whole curriculum (edit to change words/order).
  - `litLettersUpTo()` derives lit keys from `LEVELS` (never hand-maintained).
  - `CONFIG` object = every knob (balloon cap, rise speeds, TTS rate/pitch,
    celebration timing, sounds path).
  - `PHONETIC` map = TTS fallback hints per letter.
- **Single source of truth for glyph placement:** `LetterLayout` — the gap
  (mask), the faint guide, and the filled letter all read the same position and
  size, guaranteeing perfect alignment by construction.
- **Modules:** `Audio`, `LetterLayout`, `Cloud`, `Keyboard`, `Balloons`,
  `Game` (state machine). Each is small and single-purpose.
- **Known trade-off:** the display font loads from Google Fonts; fully offline
  it falls back to a system rounded font (game still works, letters look
  slightly different). Embedding the font would bloat the single file.

## 12. Open / future ideas (not yet built)

- Real recorded phoneme audio (parent's voice) dropped into `sounds/`.
- Multi-word phrases (one cloud per word).
- Optional zero-new-letter reinforcement levels.
- Animal words (DOG, PIG, COW, FOX) could tie in pictures / animal sounds.
- Tuning `balloonRiseMs` after watching the child — is the rise rewarding or
  too slow for his patience?
- Fade the target-key hint as the child improves (deliberately deferred).
