# Calibration Trainer — static demo

The full game with the server replaced by an in-browser engine: a frozen
question bank (`demo_bank.json`), scoring/selection/betting/statistics ported
to JavaScript, and your record in your browser's localStorage. No backend, no
API calls, no accounts — deployable to any static host.

## Deploy

**GitHub Pages:** put `index.html` + `demo_bank.json` in a repo (root or
`/docs`), enable Pages. Done.

**Vercel / Netlify:** drop this directory in as a static project. No build
step, no functions.

Anything that serves two static files over HTTP works. (Opening `index.html`
via `file://` does NOT work — the bank is fetched, so it needs an HTTP
server; locally: `python3 -m http.server` in this directory.)

## Properties

- Each visitor's record is private to their own browser (localStorage).
  "reset demo" (top right) wipes it and returns every question to play.
- Answers ride to the browser base64-encoded: a spoiler guard for honest
  play, not security. A determined devtools user can decode them — they are
  only cheating themselves at a calibration game.
- After a reset, round two includes questions the player has seen answered;
  stats will flatter them a little. The reset dialog says so.
- The adaptive question *generation* loop is absent by design (fixed bank),
  but selection still adapts: domain weighting and the adversarial betting
  line both run locally off the player's record.

## Rebuilding

From the project root:

```
python3 freeze_bank.py            # bank.db fresh questions -> demo/demo_bank.json
python3 build_demo.py             # static/studio.html + engine -> demo/index.html
```

`build_demo.py` reuses the studio skin's UI JavaScript verbatim and swaps only
the transport, so the demo cannot drift from the real game's presentation.
