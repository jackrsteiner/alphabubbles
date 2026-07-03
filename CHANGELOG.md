# Changelog

All notable changes to Alphabubbles are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the project uses [Semantic Versioning](https://semver.org/): while the
game is pre-1.0, MINOR bumps mark new features or content and PATCH bumps
mark fixes and tweaks. The current version lives in one place — the
`VERSION` constant in `index.html` — and is shown as the small badge in the
bottom-right corner of the screen.

Versions before 0.7.0 were assigned retroactively from the git history.

## [0.7.0] - 2026-07-03

### Added
- Semantic versioning: `VERSION` constant in `index.html`, this changelog,
  and a small always-visible version badge in the bottom-right corner.
- Versioning policy in `CLAUDE.md`: every merged change bumps the version
  and adds a changelog entry.

## [0.6.0] - 2026-07-03

### Added
- Cumulative celebration: when a word is completed, every
  previously-learned word's character pops up along the meadow and
  celebrates too, so the party grows with each word until the whole cast
  cheers together at QUACK. (#16)

## [0.5.0] - 2026-07-02

### Changed
- MOM, DAD and INDI portraits redrawn from the family photo: mom's blonde
  hair and blue eyes, dad's curls, beard and glasses, Indi's fringe and
  orange shirt. (#15)

## [0.4.0] - 2026-07-01

### Added
- Recorded parent-voice letter sounds (`sounds/a.mp3` … `z.mp3`), with the
  existing TTS phonetics kept as an automatic fallback. (#14)

## [0.3.0] - 2026-06-30

### Changed
- Game file renamed to `index.html` so GitHub Pages serves it. (#12)
- The active cloud gap shows a pulsing gold outline instead of a gold
  fill, so an empty gap can't be mistaken for a found letter. (#13)

### Fixed
- Speech synthesis hardened against Chromium silence bugs (cancelled
  utterances, wedged-paused engine, GC'd pending utterances). (#13)

## [0.2.0] - 2026-06-29

### Added
- Hand-drawn word pictures that come alive on completion, the full ANSI
  wireframe keyboard with lit "learned" letters, and a richer
  word-completion celebration (jingle + sounded-out spelling). (#11)

### Fixed
- Audio and cloud-rendering bugs from the first playtest. (#11)

## [0.1.0] - 2026-06-28

### Added
- First playable version of Alphabubbles: letter-shaped bubbles rise from
  a glowing keyboard into letter-shaped gaps in a word cloud.
