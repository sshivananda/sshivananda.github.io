# sshivananda.github.io — project context

Personal single-page portfolio for Shivananda Sahu, deployed to two targets
from one repo. This file is the source of truth for the plan; read it fully
before acting.

## Decisions already made (do not relitigate)

- Repo name is `sshivananda.github.io` (matches the GitHub username). This is
  deliberate: GitHub Pages serves a repo of that exact name at the root, with
  no `/subpath`, which means the Astro site needs **no `base` config** and the
  same build works identically on both deploy targets.
- Two deploy targets, one repo, both watching `main`:
  - **Cloudflare Pages** → serves the custom domain (the domain's DNS is
    already managed in Cloudflare). This is the canonical site.
  - **GitHub Pages** → serves the `https://sshivananda.github.io` mirror.
- The custom domain belongs to Cloudflare Pages **only**. The GitHub Pages
  "Custom domain" field must be left EMPTY. Setting it makes GitHub write a
  CNAME file and try to claim the domain via DNS, which collides with
  Cloudflare. This is the single most important gotcha.

## Current state

- A working static site already exists as a single self-contained HTML file.
  It should live as `index.html` at the repo root (rename from
  `shivanand-portfolio.html` if not done). Fonts load from Google Fonts;
  all CSS is inline in a `<style>` block. No build step, no dependencies.
- Optional `logos/firefly.svg` and `logos/athenahealth.svg`. If missing, a
  monogram fallback ("F" / "a") shows automatically via an `onerror` handler,
  so the site never looks broken.

## Phase 1 — ship the static HTML (do this first)

Goal: site live on both URLs with zero build tooling.

1. `git init`, commit `index.html` (and `logos/` if present) at repo root.
2. Create the GitHub repo named exactly `sshivananda.github.io` and push.
   (Human does the GitHub repo creation in the browser.)
3. GitHub repo → Settings → Pages: for plain HTML, set Source =
   "Deploy from a branch", branch `main`, folder `/ (root)`.
   Leave the Custom domain field EMPTY.
4. Cloudflare dashboard → Workers & Pages → create a Pages project → connect
   this repo. Build command: empty. Output directory: `/` (root).
   Then the project's Custom domains tab → add the domain.
   (Human does the Cloudflare dashboard steps.)

After this the site is live at both the custom domain and
`sshivananda.github.io`.

## Phase 2 — convert to Astro (later, only when asked)

Scaffold with `npm create astro@latest` using the **Empty** template.
Requires Node.js 18+ on the machine. Target structure:

```
sshivananda.github.io/
├── .github/workflows/deploy.yml   # GitHub Pages build + deploy
├── public/logos/{firefly,athenahealth}.svg
├── src/
│   ├── layouts/Base.astro         # <html>, <head>, fonts, global CSS link
│   ├── styles/global.css          # the CSS from the current <style> block
│   └── pages/index.astro          # the page body
├── astro.config.mjs
└── package.json
```

Guidance:

- Do NOT over-componentize. Put the document shell in `Base.astro` and the
  entire body in `index.astro`. Only split into Nav/Hero/Focus/About/Footer
  components later if it genuinely earns its keep. It usually doesn't for a
  one-pager.
- `astro.config.mjs`: set `site` to the custom domain (for canonical URLs /
  sitemap). Do NOT set `base` — the site is root-served on both targets.
- After switching to Astro: GitHub repo → Settings → Pages → Source =
  **GitHub Actions** (change it from "Deploy from a branch"). Add the
  workflow below. Verify the action versions against Astro's official
  "Deploy to GitHub Pages" docs page, since `withastro/action` and
  `actions/deploy-pages` bump periodically.
- Cloudflare Pages build settings → command `npm run build`,
  output directory `dist`.

`.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: withastro/action@v3
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

## Copy / content details — preserve verbatim during any port

- The hero text, the "Stack is a means. The problem is the point." pull-quote,
  the Sieve section, the closer line, and the about paragraphs are all
  intentional copy. Port them exactly; do not rewrite.
- `est. 2012` in the byline is a deliberate career-start anchor for texture,
  not a typo.
- The Sieve link is currently `href="#"` on purpose — no public URL exists
  yet. An HTML comment marks the spot. Leave it until a real URL is provided.
- The two company logos use an `onerror` monogram fallback. Keep that
  behavior intact when moving markup into Astro.