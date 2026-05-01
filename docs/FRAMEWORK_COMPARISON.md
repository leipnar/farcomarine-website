# Framework Comparison: FARCO Marine Website Rebuild

**Date**: May 2026
**Context**: Replacing broken WordPress site on Hostinger with a modern Node.js-based solution
**Site**: farcomarine.net (Freight Forwarding / Sea Transport Company)

---

## Evaluation Criteria

| Criterion | Weight | Notes |
|---|---|---|
| Build output (static vs server) | High | Hostinger shared hosting has limited Node.js support |
| Performance / Lighthouse scores | High | Must be fast, good Core Web Vitals |
| Learning curve | Medium | Solo/small team development |
| Ease of deployment on Hostinger | High | Shared hosting is the constraint; VPS is upgrade path |
| Ability to add dynamic features later | High | Forms, shipment tracking, quote requests |
| DX / development speed | Medium | Time to market matters |
| Community / ecosystem | Medium | Long-term maintainability |
| Zero-JS-by-default | Medium | Performance, minimal design |

---

## 1. Next.js (SSG/SSR)

**Overview**: Full-stack React framework by Vercel. Supports static export (`output: 'export'`), SSR, ISR, API routes, and server actions.

### Build Output
- **Static export**: Yes, via `next export` / `output: 'export'` in `next.config.js`. Produces plain HTML/CSS/JS files.
- **Server mode**: Needs a Node.js runtime for SSR/ISR/API routes.
- **Static export caveats**: API routes, ISR, middleware, image optimization (`next/image`), and rewrites do NOT work in static export mode. You lose the main reasons to use Next.js.

### Learning Curve
- **Moderate to High**. Requires understanding React, the App Router (Server Components, Client Components, Suspense boundaries), and Next.js-specific conventions. The mental model has gotten significantly more complex since the App Router introduction.
- Significant boilerplate for a simple marketing site.

### Performance Out of the Box
- **Moderate**. Ships React runtime even in SSG mode. A minimal page sends ~80-100KB of JS (React + Next.js runtime).
- Good Lighthouse scores are achievable but you fight the framework's defaults.
- `next/image` is excellent for optimization but doesn't work in static export mode.
- HTTP Archive data: **30%** of Next.js sites pass Core Web Vitals (lowest of all options).

### Deployment on Hostinger
- **Static export**: Works perfectly. Upload `out/` folder to `public_html/`. No server needed.
- **Server mode**: Hostinger shared hosting does NOT support long-running Node.js processes. Would need VPS or external hosting (Vercel, Railway).
- **Mixed reality**: If you start with static export and later need API routes (tracking, forms), you MUST migrate to a VPS or change hosting.

### Adding Dynamic Features Later
- **Easy if you stay in server mode** (API routes, server actions, form handling built-in).
- **Painful if you started with static export**: You must switch to server mode, which changes your deployment entirely. Or use external APIs (Formspree, Supabase, etc.) to keep static.

### Verdict for FARCO
| Aspect | Rating |
|---|---|
| Build output flexibility | 7/10 |
| Performance | 6/10 |
| Learning curve | 5/10 |
| Hostinger deployment | 7/10 (static only) |
| Dynamic features later | 8/10 (if server mode) / 4/10 (if static export) |
| **Overall fit** | **6/10** |

**The problem**: Next.js is overkill for a freight forwarding company website. It's designed for complex web apps. Using it in static export mode strips away its best features. Using it in server mode requires VPS hosting from day one.

---

## 2. Astro

**Overview**: Modern static-first framework designed for content-driven websites. Ships zero JS by default. Supports "islands" for selective interactivity. Can use React, Vue, Svelte, or plain HTML/JS.

### Build Output
- **Static**: Default mode. Produces pure HTML/CSS with zero JavaScript unless you opt in.
- **Hybrid/Server mode**: `output: 'server'` or `output: 'hybrid'` for SSR pages. Requires a Node.js adapter.
- **Best of both worlds**: Start static, flip individual pages to server-rendered as needed.

### Learning Curve
- **Low to Moderate**. If you know HTML and basic JS, you can start immediately. `.astro` files are HTML with a frontmatter script section.
```astro
---
const services = await getServices();
---
<h1>{services.title}</h1>
```
- No framework lock-in. You can add React/Vue components only where needed.
- Component syntax is intuitive — like HTML with superpowers.

### Performance Out of the Box
- **Excellent**. Zero JS by default. Pages are pure HTML until you explicitly add client-side interactivity.
- Built-in image optimization (`<Image>` component) that works in static mode.
- Built-in view transitions (page animations without a SPA framework).
- HTTP Archive data: **66%** of Astro sites pass Core Web Vitals (highest of all frameworks, more than 2x Next.js).
- A typical Astro page ships **0KB of JavaScript** unless you opt in.

### Deployment on Hostinger
- **Static mode**: Perfect. `astro build` produces a `dist/` folder. Upload to `public_html/`. Done.
- **Hybrid mode**: Individual pages can be server-rendered. Requires a Node.js server for those pages only.
- **Migration path**: Start 100% static on shared hosting. When you need a tracking API or form handler, upgrade to Hostinger VPS and switch to hybrid mode. The transition is seamless — just change `output: 'hybrid'` and add a Node adapter.

### Adding Dynamic Features Later
- **Excellent**. Astro's hybrid mode lets you add server-rendered pages alongside static ones:
  - `/` — static landing page (pure HTML)
  - `/tracking` — server-rendered with real-time data
  - `/quote` — static form that posts to an Astro API endpoint
- Built-in `Astro.actions` for form handling (type-safe server functions callable from client).
- Can add React/Vue islands for interactive widgets (tracking map, quote calculator) without rewriting the site.
- Can use Astro API routes (`src/pages/api/`) for backend logic.

### DX / Development Speed
- Hot module reloading, excellent dev server.
- Content collections for managing services, routes, team members, etc.
- Markdown/MDX support out of the box.
- Rich ecosystem of integrations and themes.
- Official MCP server for AI-assisted development.

### Verdict for FARCO
| Aspect | Rating |
|---|---|
| Build output flexibility | 10/10 |
| Performance | 10/10 |
| Learning curve | 9/10 |
| Hostinger deployment | 10/10 (static) → 9/10 (hybrid on VPS) |
| Dynamic features later | 9/10 |
| **Overall fit** | **9.5/10** |

---

## 3. Plain Express.js + Static HTML + Alpine.js

**Overview**: Hand-roll everything. Express serves static HTML files. Alpine.js adds lightweight interactivity (~15KB). Full control, minimal dependencies.

### Build Output
- **Always requires a server**. Express.js must run as a Node.js process.
- No static export — the HTML files are served dynamically.
- Alternatively, you can build static HTML files manually and serve them without Express (just plain files), but then you lose templating.

### Learning Curve
- **Low for simple sites, High for complex ones**. 
- HTML/CSS/JS fundamentals are all you need to start.
- But: no component system, no build pipeline, no image optimization, no routing framework. You build everything from scratch.
- Template engines like EJS or Handlebars can help but add complexity.

### Performance Out of the Box
- **Depends entirely on your implementation**. No framework overhead, but also no framework optimization.
- Must manually implement: image optimization, code splitting, CSS minification, caching headers, lazy loading.
- Alpine.js is lightweight (~15KB) but doesn't provide build-time optimization.
- With careful hand-optimization, can match Astro's performance. But it's all manual work.

### Deployment on Hostinger
- **Shared hosting**: Express requires a running Node.js process. Hostinger shared hosting doesn't support this reliably. Some shared hosts offer "Node.js hosting" via Phusion Passenger, but it's fragile and limited.
- **VPS**: Works fine. Run Express behind Nginx with PM2.
- **The contradiction**: This approach is the most "lightweight" in theory but the hardest to deploy on shared hosting because it REQUIRES a running server.

### Adding Dynamic Features Later
- **Maximum flexibility**. It's just Node.js. Add any route, any API, any database connection.
- But: you're building everything yourself. Form handling, validation, CSRF protection, rate limiting — all manual.
- No conventions or guardrails. Easy to create unmaintainable spaghetti.

### Verdict for FARCO
| Aspect | Rating |
|---|---|
| Build output flexibility | 4/10 (always needs server) |
| Performance | 7/10 (if manually optimized) |
| Learning curve | 6/10 (easy start, hard to scale) |
| Hostinger deployment | 3/10 (shared) / 8/10 (VPS) |
| Dynamic features later | 7/10 (flexible but manual) |
| **Overall fit** | **5/10** |

**The problem**: You end up rebuilding a worse version of Astro. The "simplicity" is illusory — you trade framework complexity for infrastructure complexity. And it can't deploy to shared hosting as static files.

---

## 4. Eleventy (11ty)

**Overview**: A simpler static site generator. Zero client-side JavaScript. Multi-template-language support (Nunjucks, Liquid, Markdown, JS, WebC). Build-only tool — produces pure static files.

### Build Output
- **Always static**. 11ty is a build tool, not a runtime. It produces HTML files in `_site/`.
- No server mode, no SSR, no API routes. If you need dynamic features, you need a separate backend.
- Zero client-side JS injected by the framework.

### Learning Curve
- **Low**. If you know HTML and a template language (Nunjucks or Liquid), you can be productive in hours.
- Convention over configuration. The defaults are sensible.
- No framework to learn — it's just a build pipeline for your HTML.

### Performance Out of the Box
- **Excellent**. Zero framework JS. Pure HTML output.
- Build speed is extremely fast (~2s for 4000 pages, vs ~70s for Next.js).
- HTTP Archive: 11ty sites consistently rank in the top tier for Core Web Vitals.
- No built-in image optimization (use the official `@11ty/eleventy-img` plugin).
- No built-in view transitions.

### Deployment on Hostinger
- **Perfect for shared hosting**. Upload `_site/` to `public_html/`. Done.
- Always static, always works with any hosting.
- No migration path complexity — it's just files.

### Adding Dynamic Features Later
- **Poor**. 11ty has no server mode. To add:
  - **Forms**: Must use external services (Formspree, Netlify Forms) or build a separate backend.
  - **Tracking**: Must build a separate Node.js/Express API server and deploy it on a VPS or serverless function.
  - **Quote calculator**: Must be purely client-side JS, or call an external API.
- The "add features later" story requires running two separate systems (11ty for frontend + Express/API for backend).

### DX / Development Speed
- Simple and stable. Very few breaking changes across versions.
- Excellent for content-heavy sites (blogs, documentation).
- Less suited for interactive marketing sites with dynamic components.
- No built-in component framework (WebC exists but is niche).

### Verdict for FARCO
| Aspect | Rating |
|---|---|
| Build output flexibility | 6/10 (static only) |
| Performance | 9/10 |
| Learning curve | 9/10 |
| Hostinger deployment | 10/10 |
| Dynamic features later | 4/10 |
| **Overall fit** | **7/10** |

**The problem**: Perfect for a brochure site, but the moment you need a tracking page, a quote form with backend logic, or any server-side functionality, you need a separate backend application. The "two-system" architecture adds significant complexity.

---

## 5. Nuxt.js

**Overview**: Vue.js equivalent of Next.js. Full-stack framework with SSR, SSG, ISR, and hybrid rendering.

### Build Output
- **Static**: `nuxt generate` produces static files. Good.
- **Server**: `nuxt build` + `nuxt preview` for SSR. Requires Node.js runtime.
- **Mixed**: `routeRules` allows per-page rendering mode.

### Learning Curve
- **Moderate to High**. Requires Vue.js knowledge (Composition API, `<script setup>`), Nuxt conventions, and the Nitro server engine.
- Simpler mental model than Next.js App Router, but still a full framework.
- Vue's template syntax is approachable, but Nuxt adds significant complexity on top.

### Performance Out of the Box
- **Moderate**. Ships Vue runtime even in SSG mode (~40-60KB JS for a minimal page).
- Better than Next.js out of the box (smaller runtime), worse than Astro or 11ty.
- HTTP Archive data: **28%** of Nuxt sites pass Core Web Vitals (second worst, barely ahead of WordPress).
- `@nuxt/image` provides good image optimization.

### Deployment on Hostinger
- **Static**: Works. Upload `.output/public/` to `public_html/`.
- **Server**: Requires Node.js runtime. Same constraint as Next.js — needs VPS.
- Nuxt's Nitro engine does support more deployment targets (Cloudflare Workers, Deno, etc.), but Hostinger shared hosting isn't one of them.

### Adding Dynamic Features Later
- **Good**. Nuxt has server API routes (`server/api/`), server middleware, and full backend capabilities.
- Same migration path issue as Next.js: static → server requires hosting change.
- Vue ecosystem is rich for interactive components.

### Verdict for FARCO
| Aspect | Rating |
|---|---|
| Build output flexibility | 8/10 |
| Performance | 6/10 |
| Learning curve | 6/10 |
| Hostinger deployment | 7/10 (static) |
| Dynamic features later | 8/10 |
| **Overall fit** | **7/10** |

**The problem**: Same overkill issue as Next.js but with Vue. Adds unnecessary framework weight for a marketing/corporate site. Vue's ecosystem is great, but Astro can use Vue components too (as islands) while shipping zero JS for the rest.

---

## Summary Comparison Table

| Criterion | Next.js | **Astro** | Express+Alpine | Eleventy | Nuxt.js |
|---|---|---|---|---|---|
| **Build output** | Static or Server | **Static or Hybrid** | Server only | Static only | Static or Server |
| **JS shipped (default)** | ~80-100KB | **0KB** | 0-15KB (manual) | 0KB | ~40-60KB |
| **Core Web Vitals pass rate** | 30% | **66%** | Varies | ~60%+ | 28% |
| **Learning curve** | Hard | **Easy** | Easy start | Easy | Moderate |
| **Shared hosting deploy** | Static only | **Perfect (static)** | No (needs server) | **Perfect** | Static only |
| **VPS deploy** | Yes | **Yes** | Yes | N/A (static) | Yes |
| **Add forms/tracking later** | Good (server) | **Excellent (hybrid)** | Manual | Poor (needs separate backend) | Good (server) |
| **Component reuse** | React only | **Any framework** | None | WebC (niche) | Vue only |
| **Image optimization** | Yes (server only) | **Yes (static+server)** | Manual | Plugin | Yes |
| **Content management** | Manual | **Content Collections** | Manual | Data cascade | Manual |
| **Ecosystem maturity** | Excellent | **Very Good** | N/A | Good | Very Good |
| **Time to first deploy** | Days | **Hours** | Days | Hours | Days |
| **OVERALL FIT FOR FARCO** | 6/10 | **9.5/10** | 5/10 | 7/10 | 7/10 |

---

## Recommendation: **Astro**

### Why Astro is the clear winner for FARCO Marine

**1. Perfect deployment story for Hostinger**
- Start with 100% static output on shared hosting (upload `dist/` to `public_html/`)
- When you need server features (tracking, forms), upgrade to Hostinger VPS and switch to hybrid mode
- No rewrite needed. Change one config line: `output: 'hybrid'`

**2. Best-in-class performance**
- 66% Core Web Vitals pass rate (2x better than Next.js, 2.3x better than Nuxt)
- Zero JavaScript by default — pages load as pure HTML
- Built-in image optimization, view transitions, and prefetching

**3. Lowest learning curve with highest ceiling**
- Start with just HTML and CSS in `.astro` files
- Add Alpine.js or vanilla JS for simple interactivity (hamburger menu, animations)
- Add React or Vue components only where needed (tracking map, quote calculator)
- Never forced to learn a framework you don't need

**4. Purpose-built for exactly this use case**
- Astro's tagline: "The web framework for content-driven websites"
- FARCO's website IS a content-driven website (services, routes, company info, news)
- Not overkill like Next.js/Nuxt, not limited like 11ty

**5. Seamless feature growth path**
```
Phase 1 (Now):     Static marketing site on shared hosting
                    - Homepage, About, Services, Routes, Contact
                    - Pure HTML, zero JS, perfect Lighthouse scores
                    
Phase 2 (Months):  Add interactivity with islands
                    - Animated hero, scroll effects (Alpine.js)
                    - Quote request form (React island)
                    - Newsletter signup
                    - Still static, still on shared hosting
                    
Phase 3 (Later):   Hybrid mode on VPS
                    - Shipment tracking page (server-rendered)
                    - Client portal (authenticated pages)
                    - CMS integration for content updates
                    - API endpoints for third-party integrations
```

### Recommended Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Framework | Astro 6.x | All reasons above |
| Styling | Tailwind CSS v4 | Rapid, minimal CSS output, modern utility-first |
| Interactivity | Alpine.js or vanilla JS | Lightweight (~15KB), perfect for menus/animations |
| Complex widgets | Preact or React (islands) | Only if needed for tracking map, etc. |
| Content | Astro Content Collections | Type-safe Markdown for services, routes, team |
| Forms (Phase 1) | Formspree or Resend | Zero backend needed on shared hosting |
| Forms (Phase 3) | Astro Actions + API routes | When on VPS with hybrid mode |
| Deployment (Phase 1) | Static files to Hostinger shared | Simplest possible |
| Deployment (Phase 3) | Hostinger VPS + PM2 + Nginx | When server features needed |
| Analytics | Plausible or Umami | Privacy-friendly, lightweight |

### Quick Start

```bash
npm create astro@latest farco-website -- --template minimal
cd farco-website
npx astro add tailwind
npm run dev
```

### Project Structure (Recommended)

```
farco-website/
├── src/
│   ├── components/       # Reusable UI components
│   │   ├── Header.astro
│   │   ├── Footer.astro
│   │   ├── Hero.astro
│   │   ├── ServiceCard.astro
│   │   └── TrackingWidget.jsx   # React island (Phase 2)
│   ├── layouts/
│   │   └── BaseLayout.astro     # HTML shell, meta tags, nav, footer
│   ├── pages/
│   │   ├── index.astro          # Homepage
│   │   ├── about.astro
│   │   ├── services.astro
│   │   ├── routes.astro         # Shipping routes
│   │   ├── contact.astro
│   │   └── tracking.astro       # Phase 3: server-rendered
│   ├── content/
│   │   ├── services/            # Markdown files for each service
│   │   └── routes/              # Markdown files for shipping routes
│   └── styles/
│       └── global.css           # Tailwind imports + custom styles
├── public/
│   ├── images/
│   └── favicon.svg
├── astro.config.mjs
├── tailwind.config.mjs
└── package.json
```

---

## Final Thoughts

The choice ultimately comes down to **Astro vs Eleventy** for a site like FARCO. Both produce static files, both perform excellently, both deploy trivially to shared hosting. The deciding factor is the **growth path**:

- **Eleventy** says: "We're a build tool. If you need a server, go build one separately."
- **Astro** says: "Start static. When you need a server, flip this switch. We've got you."

For a freight forwarding company that will inevitably need shipment tracking, quote forms with backend logic, and possibly a client portal, Astro's unified approach is far superior to managing two separate systems with Eleventy + Express.

The runner-up (11ty) is a fine choice if you're absolutely certain the site will never need server-side functionality. But that's not a safe bet for FARCO.
