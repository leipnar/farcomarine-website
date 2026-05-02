# Development Guide — FARCO Marine Website

All context, decisions, and instructions for developing this project. Read this file first.

## Project Overview

- **Site**: [farcomarine.net](https://farcomarine.net)
- **Stack**: Astro 5 + Tailwind CSS 3 (static output, zero client-side JS framework)
- **Hosting**: Hostinger Business Shared Hosting
- **Deployment**: Push to `main` on GitHub → Hostinger auto-deploys. NO SFTP needed.
- **GitHub**: `https://github.com/leipnar/farcomarine-website` (account: `leipnar`)
- **GitHub Auth**: Token in 1password (`GitHub Personal Access Token`), item ID `gltznvsnnpt4ofgmamsyu527ry`
- **Cloudflare**: Used ONLY for Workers OAuth proxy. Domain NOT on Cloudflare.
  - Account ID: `32d4cc1243b2a418176cbe31af55ceed`
  - API Token in 1password (`Cloudflare API Token`), item ID `qyrfkkapx5qq5xhaxacunlnv6y`

## Quick Start

```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # outputs to dist/
```

ALWAYS run `npm run build` after changes to verify the build passes before committing.

## CMS (Decap CMS)

- **Admin URL**: `farcomarine.net/admin`
- **CMS**: Decap CMS (formerly Netlify CMS) — Git-based headless CMS
- **Backend**: GitHub, via OAuth proxy on Cloudflare Workers
- **OAuth Proxy**: `https://farco-cms-auth.ditrust.workers.dev`
- **GitHub OAuth App**: Client ID `Ov23li7WgSfaUDZC7ojW`, callback URL set to proxy
- **OAuth Scopes**: `public_repo,read:user` (minimal — no private repo or email access)
- **Content workflow**: Edit in CMS → commits to GitHub → Hostinger auto-deploys

### Editable Content (via CMS)

| CMS Collection | File | What's Editable |
|---|---|---|
| Site Settings | `src/content/settings.json` | Company name, email, phone, address, hours, tagline, notice banner (toggle on/off, message, link) |
| Homepage Hero | `src/content/hero.json` | Badge, headline, subtitle, CTA buttons, stats |
| About Page | `src/content/about.json` | Paragraphs, headline, mission, vision, core values |
| Routes & Ports | `src/content/routes.json` | Regions, ports per region, color themes |

### CMS Files

- `public/admin/index.html` — CMS admin shell (loads Decap CMS JS)
- `public/admin/config.yml` — CMS collections, fields, and backend config
- `src/content/*.json` — Content data files (CMS reads/writes these)

## Architecture

### Content Flow

Pages read content from JSON files in `src/content/`:
- `settings.json` → Layout (notice banner), Footer, Contact, Quote, Legal pages
- `hero.json` → Homepage hero + stats bar
- `about.json` → About page
- `routes.json` → Routes page

The CMS writes to these JSON files. Astro reads them at build time.

### Theme System

- **Dark mode**: Default. Light mode toggle available.
- **Mechanism**: `darkMode: 'class'` in Tailwind config. `.dark` class on `<html>`.
- **CSS Variables**: `--navy-*` colors swap via CSS vars in `global.css` based on `.dark` class.
- **Theme text classes**: `.text-heading`, `.text-body`, `.text-muted`, `.text-faint` — use `--theme-text*` CSS variables.
- **Persistence**: `localStorage` key `farco-theme` (`dark` or `light`).
- **Theme init**: Inline `<script>` in Layout `<head>` sets `.dark` before paint.
- **Toggle logic**: Single IIFE in Layout `<body>`, NOT in ThemeToggle component.

### Notice Banner

- Controlled by `settings.json` → `notice.enabled` (boolean)
- When enabled: fixed at top, header positioned below it dynamically
- Dismissible via close button, persisted in `localStorage` key `farco-notice-closed`
- Header's `top` is adjusted via JS when notice is visible/hidden
- To remove permanently: set `notice.enabled` to `false` in CMS or `settings.json`

### Key Components

| File | Role |
|---|---|
| `src/layouts/Layout.astro` | Base layout: theme init, notice banner, scroll observer, theme toggle |
| `src/components/Header.astro` | Fixed header, mobile menu, `positionHeader()` adjusts for notice |
| `src/components/Footer.astro` | Reads `settings.json` for contact info |
| `src/components/ThemeToggle.astro` | HTML-only button (no script) |

### Performance Optimizations

- Self-hosted WOFF2 Inter font (Latin subset, ~13KB per weight): `public/fonts/inter-{500,600,700}-subset.woff2`
- WebP images with responsive srcset: `public/images/` (hero has 480w, 960w, 1920w variants)
- Preload: hero image + critical font in Layout `<head>`
- `fetchpriority="high"` on hero image
- Width/height on all images to prevent CLS

## Design Decisions

- **No social media links** — removed from footer (LinkedIn, Facebook, email icons)
- **Email**: `info@farcomarine.net` (NOT .com)
- **Address**: Tehran, Gholhak, Yakhchal St.
- **Phone**: +98 21 26358241
- **Forms**: JS `mailto:` link construction, NO `action="mailto:..."` attribute
- **Hero overlay**: Directional gradient via `--theme-hero-overlay` CSS variable (dark left → transparent right)
- **Transit times**: NOT shown on routes — meaningless without origin port
- **Focus regions**: East Asia, SE Asia, India & Central Asia, Middle East, Europe, Africa — NO Americas

## Routes & Ports

Six regions defined in `src/content/routes.json`:
1. East Asia (37 ports) — China, Taiwan, South Korea, Japan
2. South & Southeast Asia (11 ports) — Malaysia, Indonesia, Vietnam, Singapore, Thailand, Myanmar
3. India & Central Asia (5 ports) — India, Pakistan, Bangladesh, Sri Lanka
4. Middle East (7 ports) — Iran, UAE, Qatar, Oman
5. Europe (6 ports) — Italy, Netherlands, Belgium, Spain, Romania, Turkey
6. Africa (5 ports) — Kenya, Tanzania, Libya

## File Reference

```
src/
├── content/
│   ├── settings.json      # Company info, notice banner
│   ├── hero.json           # Homepage hero content
│   ├── about.json          # About page content
│   └── routes.json         # Routes/ports data
├── layouts/
│   └── Layout.astro        # Base layout, theme, notice, scripts
├── components/
│   ├── Header.astro        # Fixed header + mobile menu
│   ├── Footer.astro        # Footer, reads settings.json
│   ├── ThemeToggle.astro   # HTML-only toggle button
│   ├── ServiceCard.astro
│   ├── ValueCard.astro
│   ├── PageHero.astro
│   └── CTABanner.astro
├── pages/
│   ├── index.astro         # Home (reads hero.json)
│   ├── about.astro         # About (reads about.json)
│   ├── services.astro      # Services
│   ├── routes.astro        # Routes (reads routes.json)
│   ├── quote.astro         # Quote form (reads settings.json)
│   ├── contact.astro       # Contact form (reads settings.json)
│   ├── privacy-policy.astro
│   └── terms-of-service.astro
├── styles/
│   └── global.css          # CSS vars, @font-face, theme system
public/
├── admin/
│   ├── index.html          # Decap CMS admin panel
│   └── config.yml          # CMS config (collections, backend)
├── fonts/                  # Self-hosted WOFF2 subset fonts
└── images/                 # JPG originals + WebP variants
```

## Cloudflare Worker (OAuth Proxy)

- **Name**: `farco-cms-auth`
- **URL**: `https://farco-cms-auth.ditrust.workers.dev`
- **Source**: Based on [sveltia/sveltia-cms-auth](https://github.com/sveltia/sveltia-cms-auth)
- **Local copy**: `/tmp/opencode/sveltia-cms-auth/index.js`
- **Env vars**: `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`
- **Scope**: `public_repo,read:user` (overridden from default `repo,user`)

To redeploy the worker after changes:
```bash
# Use the Cloudflare Workers API with the token from 1password
# See "Cloudflare API Token" in 1password
python3 << 'PYEOF'
import urllib.request, json, io
cf_token = "<FROM_1PASSWORD>"
cf_account = "32d4cc1243b2a418176cbe31af55ceed"
# ... upload worker script via multipart/form-data to
# PUT /accounts/{account_id}/workers/scripts/farco-cms-auth
PYEOF
```

## Development History

### Session 1 — Initial Build
- Created Astro + Tailwind project with 8 pages
- Dark/light mode with CSS variable system
- Mobile-responsive design
- Deployed to Hostinger via GitHub auto-deploy

### Session 2 — Design Refinements
- Hero redesign: directional gradient overlay, trust badges
- Removed social media icons from footer
- Updated contact info (real address, phone, email)
- Theme-aware hamburger icon

### Session 3 — Performance Optimization
- Self-hosted WOFF2 Inter font (13KB per weight vs 318KB Google Fonts)
- WebP images with responsive srcset
- Reduced font weights from 7 to 3 (500/600/700)
- Preload hero image + critical font
- Mobile PageSpeed improved from 67% → 89%

### Session 4 — Routes Update
- Removed meaningless transit times from port cards
- Updated all routes with correct port names (6 regions, 70+ ports)
- Corrected spelling: Dar es Salaam, Zanzibar, Benghazi, Kaohsiung, etc.

### Session 5 — Notice Banner
- Added amber notice banner for Persian Gulf shipping delays
- Fixed positioning: `fixed` with `z-[60]`, header adjusts below it
- Dismissible with localStorage persistence
- Now controlled via `settings.json` → `notice.enabled`

### Session 6 — CMS Integration
- Added Decap CMS with GitHub backend
- Created `src/content/` JSON data files for editable content
- Refactored all pages to read from JSON instead of hardcoded values
- Deployed sveltia-cms-auth OAuth proxy on Cloudflare Workers
- Set up GitHub OAuth App with restricted scopes (`public_repo,read:user`)
- CMS admin at `farcomarine.net/admin`

## Known Issues / TODO

- Services page content is still hardcoded (not yet in CMS)
- Mobile PageSpeed ~89%, room for improvement
- No Google Maps on contact page
- No blog/news section
