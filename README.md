# COTTY — website

Landing page for [Cotty](https://github.com/tylerbroqs/cotty), the multiplayer terminal.

A single self-contained `index.html` — no build step, no dependencies. Open it
in a browser, or serve it from any static host (GitHub Pages, Netlify, Vercel,
Cloudflare Pages…).

```sh
python3 -m http.server 8000   # then open http://localhost:8000
```

## What's inside

- Animated ANSI-shadow wordmark (the same one the `cotty` CLI prints), decoded in with a glitch effect
- A scripted multiplayer session demo: presence, view-only guests, `cotty ctl allow`/`kick`, a live audit trail and the ciphertext the relay sees
- Feature bento grid with an interactive permissions toggle
- End-to-end encryption explainer (key in the URL fragment, relay sees noise)
- Architecture diagram, tabbed command reference with copy buttons

## Deploying to GitHub Pages

Settings → Pages → *Deploy from a branch* → pick the branch and `/ (root)`.
