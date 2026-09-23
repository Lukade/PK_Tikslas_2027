# PK_Tikslas — Leaderboard

A single self-contained `index.html`: a team leaderboard with a shared goal meter.

## Hosting

- **Live version: GitHub Pages**, served as `index.html` from the repo root. Static and
  read-only for everyone who opens it.
- A copy was once published as a claude.ai artifact. That copy is not authoritative —
  GitHub Pages is.
- **There is no edit mode.** Viewers cannot change anything in the browser. All content
  changes are made by editing this file in the repo (that's my job — see below).

## File structure (`index.html`)

| Block | What it is |
| --- | --- |
| `<style id="css">` | All CSS. Theme colors are tokens on `:root`; dark mode via `prefers-color-scheme` **and** `[data-theme]`. Fonts: Bricolage Grotesque (display) + Figtree (body) from Google Fonts, with system fallbacks. |
| `<div id="root">` | Empty. Everything is rendered into it by JS. |
| `<script type="application/json" id="state">` | **All data lives here.** Edit this to change content. |
| `<script id="app">` | Vanilla JS renderer. No frameworks, no libraries, no build step. |

## State schema

```json
{
  "title": "Leaderboard",
  "goal": 100,
  "players": [
    { "id": "p1", "name": "Player One",
      "avatar": { "type": "emoji", "value": "🦊" },
      "color": "#FF7A45",
      "points": 12 }
  ]
}
```

- `id` must be unique. `points` are integers >= 0.
- `avatar` is either `{ "type": "emoji", "value": "🦊" }` or
  `{ "type": "img", "value": "<URL, repo path, or data: URI>" }`.
- Photos may be a relative repo path (`photos/tomas.jpg`) or a small `data:` URI
  (~96px square webp/jpeg). Keep them small — they live inside the HTML file.
- Player color palette:
  `#FF7A45` `#22B8A0` `#5B6CFF` `#FF5C93` `#F2A900` `#8E5BFF` `#2FA7E8` `#5CB82E`

## What the page renders

1. **Title** (`h1`), also used as `document.title`.
2. **Goal meter** — total of all points vs `goal`, as a progress bar split into segments
   colored per player (largest first), ticks every 10%, and a legend. Shows
   "N points to go", or "Goal reached! 🎉" with a gold outline once total >= goal.
3. **Leaderboard**, sorted by points descending (ties broken by name): rank
   (🥇🥈🥉 for the top three, ties share a rank), avatar, name, a "trail" of one small
   avatar token per point (capped at `TRAIL_CAP` = 300), and the score.
   Responsive: below 640px the trail wraps onto its own row.

## How to make changes

- **Scores / players:** edit only the JSON in `#state`, keeping it valid JSON.
  Escape any `<` inside it as the JSON escape `\u003c` (it sits inside a `<script>` tag).
- **Design:** edit `#css` / `#app`. Keep it one file — no build step, no external scripts
  (the Google Fonts stylesheet is the only outside request).
- User-supplied text (names, title) is inserted with `textContent`. **Never switch to
  `innerHTML` for it.**
- After a change, verify by rendering rather than by eye:
  `google-chrome-stable --headless --disable-gpu --window-size=1000,1400 --screenshot=shot.png --virtual-time-budget=3000 file://$PWD/index.html`
  (the browser extension can't open `file://` or `localhost` here).
  Also worth a syntax check of the `#app` script and a `JSON.parse` of `#state`.

## History

The original version carried a claude.ai artifact edit mode (+/− buttons, per-player edit
panel, add player, editable title/goal, self-republishing save). It was removed on
2026-09-23 since the page is view-only on GitHub Pages — don't reintroduce it unless asked.
