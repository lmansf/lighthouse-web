# lighthouse-web

The download landing page for [Lighthouse](https://github.com/lmansf/lighthouse) —
a single static page with a one-click Windows download and a link to the repo.

## What it is

- `index.html` — the whole page (inline CSS/JS, no build step).
- `favicon.svg` — the lighthouse mark.
- `vercel.json` — security headers + clean static serving.

The big **Download for Windows** button is self-healing and never lands on a
404. On load the page links it (and the "other platforms" link) to the releases
**index** (`/releases`), which returns 200 even when no release exists yet. It
then queries the GitHub API for the latest release:

```
https://api.github.com/repos/lmansf/lighthouse/releases/latest
```

- If the latest release has a `.exe` asset, the button points straight at that
  installer's download URL and the "other platforms" link points at that
  release's page.
- If GitHub reports no published release (404) or the latest release has no
  `.exe` asset, the button is greyed out and relabelled **"Windows build coming
  soon"** while still linking to the releases index.
- If the API call fails transiently (rate limit, 5xx, network/CORS), the button
  keeps the safe releases-index link and its normal label.

This means publishing a release with a Windows installer automatically upgrades
the button — no edit to this page required.

To change the repo, edit the `REPO` constant in the `<script>` at the bottom of
`index.html` — every link derives from it. The GitHub API URL (`API_LATEST`) is
spelled out separately in the same block, so update it to match.

## Deploy to Vercel

No framework, no build. Either:

- **Dashboard:** New Project → import this repo → Framework Preset **Other** →
  Deploy. (Root directory `/`, no build command, output is the repo root.)
- **CLI:** `npm i -g vercel && vercel --prod`

## Publishing an installer for the download button to find

In the `lighthouse` repo, build the Windows installer and attach it to a
release. The button finds the first asset whose name ends in `.exe`, so any
`.exe` name works (e.g. `Lighthouse-Setup.exe`):

```sh
npm run dist               # produces release/Lighthouse-Setup.exe (Windows)
gh release create v0.1.0 "release/Lighthouse-Setup.exe" --title "Lighthouse 0.1.0"
```

(Building the Windows `.exe` requires running `npm run dist` on Windows, or a
Windows CI runner.)
