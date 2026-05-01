# FARCO Marine Website

Modern, fast, static website for FARCO Marine — a freight forwarding company specializing in sea freight and global logistics.

**Site**: [farcomarine.net](https://farcomarine.net)
**Stack**: Astro 5 + Tailwind CSS 3
**Hosting**: Hostinger Business Shared

## Quick Start

```bash
# Install dependencies
npm install

# Start dev server (http://localhost:4321)
npm run dev

# Build for production (outputs to dist/)
npm run build

# Preview production build
npm run preview
```

## Pages

| Page | Route | Description |
|---|---|---|
| Home | `/` | Hero, services overview, stats, why choose us |
| About | `/about` | Company story, mission, vision, values |
| Services | `/services` | Sea freight, road transport, supporting services |
| Routes | `/routes` | Global network map, destinations by region |
| Quote | `/quote` | Multi-step quote request form |
| Contact | `/contact` | Contact form, details, business hours |

## Design

- **Theme**: Dark maritime (deep navy, ocean blue, amber accents)
- **Framework**: Zero-JS static output via Astro
- **CSS**: Tailwind CSS with purged production bundle (~20KB)
- **Total build size**: ~208KB for all 6 pages

## Deployment

Build outputs static HTML/CSS to `dist/`. Upload contents to Hostinger's `public_html/`:

```bash
npm run build
# Upload dist/* to domains/farcomarine.net/public_html/
```

## Project Structure

```
src/
├── components/     # Reusable Astro components
├── layouts/        # Base page layout
├── pages/          # Route pages
└── styles/         # Global CSS + Tailwind
public/             # Static assets (favicon, robots.txt)
```

## Legacy Documentation

Previous WordPress project documentation is archived in `docs/`.
