# lighthouse-web

The download landing page for [Lighthouse](https://github.com/lmansf/lighthouse) —
a single static page with a one-click Windows download and a link to the repo.

## What it is

- `index.html` — the whole page (inline CSS/JS, no build step). The `<head>`
  carries the SEO surface: title/description, canonical, Open Graph + Twitter
  card tags, and JSON-LD `SoftwareApplication` structured data.
- `favicon.svg` — the lighthouse mark.
- `apple-touch-icon.png` — 180×180 icon for iOS bookmarks/shares.
- `og-image.png` — the 1200×630 social-share card (`og:image` / `twitter:image`).
- `robots.txt` / `sitemap.xml` — crawler directives + page list for search
  engines. After deploying changes, submit `sitemap.xml` in Google Search
  Console and Bing Webmaster Tools once; they re-crawl on their own after that.
- `vercel.json` — security headers + clean static serving.

The big **Download for Windows** button links to `/download/windows`, which
`vercel.json` redirects (a temporary `307`, since the redirect uses
`"permanent": false`) straight to GitHub's permanent latest-release asset URL:

```
https://github.com/lmansf/lighthouse/releases/latest/download/Lighthouse-Setup.exe
```

GitHub always resolves `releases/latest/download/<asset>` to the newest
published release, so the installer download starts directly with no client-side
JavaScript, no API calls, and no intermediate releases page.

Two requirements keep this working:

- The published installer asset MUST be named exactly `Lighthouse-Setup.exe`.
  The `lighthouse` repo's electron-builder `nsis.artifactName` is set to this
  stable, version-less name so the URL never changes between releases. If a
  release ships the installer under any other name, the redirect 404s.
- The `lmansf/lighthouse` repo MUST be public, so anonymous visitors can
  download the asset without authentication.

To change the repo, update the redirect `destination` in `vercel.json` and the
hardcoded `github.com/lmansf/lighthouse` links in `index.html`.

## Deploy to Vercel

No framework, no build. Either:

- **Dashboard:** New Project → import this repo → Framework Preset **Other** →
  Deploy. (Root directory `/`, no build command, output is the repo root.)
- **CLI:** `npm i -g vercel && vercel --prod`

## Publishing an installer for the download button to find

In the `lighthouse` repo, build the Windows installer and attach it to a
release. The asset MUST be named exactly `Lighthouse-Setup.exe` or the
`/download/windows` redirect 404s:

```sh
npm run dist               # produces release/Lighthouse-Setup.exe (Windows)
gh release create v0.1.0 "release/Lighthouse-Setup.exe" --title "Lighthouse 0.1.0"
```

(Building the Windows `.exe` requires running `npm run dist` on Windows, or a
Windows CI runner.)
