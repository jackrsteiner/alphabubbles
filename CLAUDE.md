# Alphabubbles — project notes

A single-file alphabet + typing game for a toddler. All game code lives in
`index.html`; design rationale is in `DESIGN.md`; known issues in `BUGS.md`.

## Versioning (required on every change)

The project uses [Semantic Versioning](https://semver.org/). The current
version lives in **one place**: the `VERSION` constant near the top of the
`<script>` in `index.html`. It is rendered as the small badge in the
bottom-right corner of the screen.

Every change that will be merged MUST, in the same branch:

1. Bump `VERSION` in `index.html` — pre-1.0 rules:
   - **MINOR** (0.X.0) for a new feature, new content (words, art,
     sounds), or a behavior change;
   - **PATCH** (0.0.X) for a bug fix, tweak, or docs-only change that
     ships with the file.
2. Add a matching entry to `CHANGELOG.md` (Keep a Changelog format:
   Added / Changed / Fixed) with the date and PR number.
3. Reference the new version number in the PR title or body.

If several changes land in one branch, one combined bump is fine — use the
highest applicable bump level.
