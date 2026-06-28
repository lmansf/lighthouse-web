# lighthouse-web

The download landing page for [Lighthouse](https://github.com/lmansf/lighthouse) —
a single static page with a one-click Windows download and a link to the repo.

## What it is

- `index.html` — the whole page (inline CSS/JS, no build step).
- `favicon.svg` — the lighthouse mark.
- `vercel.json` — security headers + clean static serving.

The big **Download for Windows** button points at the latest GitHub Release
asset:

```
https://github.com/lmansf/lighthouse/releases/latest/download/Lighthouse-Setup.exe
```

That URL always resolves to the newest installer **as long as each release
attaches an asset named exactly `Lighthouse-Setup.exe`** (the app's
electron-builder config sets `nsis.artifactName` to that stable name). Until the
first release is published with that asset, the button will 404.

To change the repo, edit the `REPO` constant in the `<script>` at the bottom of
`index.html` — every link derives from it.

### Paid subscriptions

The page can advertise a paid plan (`$14.99` per seat / month, anchored at half
the price of Copilot). It is off by default. To enable it, in the same
`<script>` set `PAID_ENABLED = true` and point `SUBSCRIBE_URL` at your Stripe
Payment Link (or checkout). The subscribe link under the download button only
appears when both are set; otherwise it stays hidden.

## Deploy to Vercel

No framework, no build. Either:

- **Dashboard:** New Project → import this repo → Framework Preset **Other** →
  Deploy. (Root directory `/`, no build command, output is the repo root.)
- **CLI:** `npm i -g vercel && vercel --prod`

## Publishing an installer for the download button to find

In the `lighthouse` repo, build the Windows installer and attach it to a
release whose asset is named `Lighthouse-Setup.exe`:

```sh
npm run dist               # produces release/Lighthouse-Setup.exe (Windows)
gh release create v0.1.0 "release/Lighthouse-Setup.exe" --title "Lighthouse 0.1.0"
```

(Building the Windows `.exe` requires running `npm run dist` on Windows, or a
Windows CI runner.)
