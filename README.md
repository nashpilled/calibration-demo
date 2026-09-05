# Calibration Trainer — static demo

The full game with the server replaced by an in-browser engine: a frozen
question bank (`demo_bank.json`, 654 verified questions), scoring, adversarial
selection, betting and statistics ported to JavaScript, and your record in your
browser's localStorage. No backend, no API calls, no accounts — deployable to
any static host.

Rules: optimized v2 (absolute log median error, tail and median diagnostics
with Wilson intervals, residual-based betting lines with no cold-start edge,
proper scores as the headline) plus adversarial selection v2.1 (threshold
targeting from one-bin reliability and Brier excess, worst-coverage-first
difficulty by Thompson sampling, interval-score pressure, magnitude-band
targeting, base-rate skew flags). See `OPTIMIZED_RULES.md` in the game repo.

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
  but selection still adapts: domain weighting, worst-first difficulty and
  magnitude sampling, and the adversarial betting line all run locally off
  the player's record.
- Existing records from the previous build are kept (same localStorage key);
  the new diagnostics are recomputed from the stored raw answers.

## Rebuilding

From the project root:

```
python3 freeze_bank.py --include-answered   # bank.db questions -> demo/demo_bank.json
python3 build_demo.py                       # static/studio.html + engine -> demo/index.html
```

Then copy `demo/index.html` and `demo/demo_bank.json` here.

`build_demo.py` reuses a skin's UI JavaScript verbatim and swaps only the
transport, so the demo cannot drift from the real game's presentation. The
demo is built from the **professor skin** (a refurbished 1990s department
homepage: navy banner, Times, blue links, outset buttons, bordered tables);
`python3 build_demo.py --skin studio` builds the studio look instead.
