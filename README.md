# 747 Front St — Deployment Bundle

This folder is a **self-contained, GitHub-Pages-ready** copy of the 747 Front St investor offering page. Everything the live page needs (HTML, CSS, images, fonts via CDN) is here. Drag this folder into a GitHub repo and turn on Pages — that's the whole deployment.

## What's in here

```
747-front-st/
├── index.html          ← the page (entry point GitHub Pages serves)
├── tokens.css          ← brand design tokens
├── components.css      ← reusable component styles
└── assets/
    ├── logo-white.svg / logo-navy.svg / mark-tones.svg
    ├── favicon-512.png
    ├── hero.jpg            ← 747 building exterior (CoStar)
    ├── gallery-aerial.jpg / gallery-building.jpg
    └── portfolio/          ← 9 past Pendulum deals
        ├── liberty-station.jpg
        ├── stonecrest.jpg
        ├── peters-landing.jpg
        ├── 505-n-brand.jpg
        ├── the-mix.jpg
        ├── culver-connect.jpg
        ├── jamboree-business-ctr.jpg
        ├── axis.jpg
        └── 2901-w-alameda.jpg
```

External resources loaded via CDN (no action needed):
- **Google Fonts** — Manrope, Quicksand, Nunito Sans
- **Leaflet** — map library, lazy-loaded only when the Location section enters viewport
- **Carto Positron tiles** — map background, free for non-commercial use

## Password gate

The page loads behind a soft password gate. **Password: `pendulum`** (case-insensitive). The check is client-side (FNV-1a hash, bypassable via DevTools — this is *obscurity, not security*). Adequate for "do not share publicly" review with Pendulum's team. **Do not rely on it to protect anything that matters legally.**

To change the password, regenerate the hash:

```bash
python3 -c "
def fnv1a(s):
    h = 0x811c9dc5
    for c in s.lower().encode('utf-8'):
        h ^= c
        h = (h * 0x01000193) & 0xFFFFFFFF
    return f'{h:x}'
print(fnv1a('YourNewPassword'))
"
```

Then in `index.html`, find the line `const EXPECTED = 'a6335e47';` and replace the hash.

## Deploy to GitHub Pages — step-by-step

You have two paths. Pick whichever you're comfortable with.

### Path A — Web UI (no command line needed)

1. **Create a new repo** at <https://github.com/new>.
   - Name it something like `747-front-st-preview` or `pendulum-offerings`
   - Set it to **Public** (Pages requires public on free plans, OR a paid GitHub Pro account for private)
   - Don't initialize with a README — we already have one
   - Click "Create repository"
2. **Upload the bundle.** On the empty repo's landing page, click "uploading an existing file" or drag-drop the **contents** of this `747-front-st/` folder (not the folder itself — open it and select everything inside).
3. Commit with a message like "Initial deploy".
4. **Enable Pages.** Repo → **Settings → Pages** → under "Build and deployment", set Source to **"Deploy from a branch"**, Branch to **`main`** + `/ (root)`. Save.
5. Wait ~60 seconds. Pages builds and serves at:
   `https://YOUR-GITHUB-USERNAME.github.io/REPO-NAME/`
6. Send that URL + the password to Pendulum.

### Path B — Git CLI (faster on subsequent updates)

```bash
cd "/Users/codyleivas/Library/CloudStorage/Dropbox-Personal/04_Business/Capistrano Real Estate Advisors/CAPO - Pendulum/Pendulum/06_Deployment/747-front-st"

git init
git branch -M main
git add .
git commit -m "Initial 747 Front St deploy"
git remote add origin git@github.com:YOUR-USERNAME/REPO-NAME.git
git push -u origin main
```

Then enable Pages in repo Settings as in step 4 above.

## After it's live

- **Test** the URL in an incognito window first (clears any localhost session). You should hit the password gate, type `pendulum`, and see the full page.
- **Send the link** with a short note like:

  > *Subject: 747 Front St investor preview*
  > Kevin / Dan / Xander —
  > Here's the live preview of the 747 Front St offering page: `https://YOUR-USER.github.io/REPO-NAME/`
  > Password: `pendulum`
  > Still in draft — feedback welcome on copy, layout, anything that doesn't feel right.

## Updating the page later

If you change `index.html` or any asset:

**Web UI:** drop the changed file into the repo (it'll overwrite). Commit. Pages re-deploys in ~60 seconds.

**CLI:**
```bash
git add .
git commit -m "Description of the change"
git push
```

## When you're ready for a real Pendulum URL

Two options to upgrade from `*.github.io` to a real URL:

1. **Custom subdomain** (recommended). Have Pendulum's web admin add a `CNAME` record:
   - `invest.pendulumpp.com` → `YOUR-USERNAME.github.io`
   - In your repo's `Settings → Pages`, set "Custom domain" to `invest.pendulumpp.com`. GitHub will provision an SSL cert automatically.
   - Resulting URL: `https://invest.pendulumpp.com/` (no repo name in the path).

2. **Move to Pendulum's WordPress.** Their webmaster pastes the HTML into a WordPress page. More work but keeps everything under `pendulumpp.com`. Worth it once the page is final.

## Caveats / known issues

- **Map tiles** load from `cartocdn.com`. If a corporate firewall blocks it, the map will appear blank. Most consumer browsers will be fine.
- **Leaflet** loads on scroll into the Location section. First render takes ~500ms. Small loading skeleton might be a future polish.
- **OG image** for social shares currently points to `assets/hero.jpg`. After deploy, GitHub Pages serves it correctly. If the link preview looks bad on Slack/email, we can design a custom OG card later.
