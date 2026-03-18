# CLAUDE.md

## Project
inProduction — static marketing site for a Danish workflow automation consultancy.
Pure HTML + Tailwind CSS (CDN). No framework, no build step. Danish language (`lang="da"`).

## Before You Code
**Always sync with remote before making local changes.**
```bash
git fetch origin && git pull origin main
```
This repo is deployed via GitHub Pages on push to `main`. Local files can easily drift from production. Pull first, then branch, then work.

## Dev Server
```bash
npx serve -l 8080          # Local preview at http://localhost:8080
```
Or use Claude Preview: launch.json config name is `dev`.

## External Dependencies
Homepage hero animation uses GSAP 3.12 + ScrollTrigger (loaded via CDN `<script>` tags at bottom of `index.html`). No other pages use GSAP.

**Tailwind CDN + GSAP conflict:** Tailwind CDN's hot-reload re-executes all `<script>` blocks, which kills GSAP's `requestAnimationFrame` ticker. In the dev preview, GSAP `gsap.to()` animations will appear broken (tweens never progress). This does NOT affect production (GitHub Pages). Workarounds for local testing: (1) call `gsap.ticker.tick()` manually, or (2) avoid `requestAnimationFrame` wrappers around init code — call `initScattered()` / `setupScrollAnimation()` directly.

## Deployment
GitHub Pages. Push to `main` auto-deploys to inproduction.dk.
No build step — HTML files served directly.

## Architecture
```
/index.html                                    Homepage
/loesninger/index.html                         Solutions hub
/loesninger/enfocus-switch/index.html          Enfocus Switch product page
/loesninger/pitstop/index.html                 PitStop product page
/loesninger/pdf-workflows/index.html           PDF Workflows product page
/loesninger/grafisk-automatisering/index.html  Pillar guide page
/cases/index.html                              Case studies
/om-os/index.html                              About
/kontakt/index.html                            Contact
/partnere/index.html                           Partners
/egenudviklede-produkter-loesninger/index.html Custom development
/404.html                                      Error page
/inproduction-kontakt.html                     Legacy redirect (keep for old links)
/assets/styles.css                             Custom CSS (nav, animations, components)
/Logos/                                        Partner & product logos (SVG/PNG)
```

## Gotchas

**Nav is duplicated across all HTML files.** Every nav change must be applied to every file — desktop dropdown AND mobile overlay accordion. There is no templating.

**Tailwind config is inlined** in a `<script>` tag in every HTML `<head>`. Changes to design tokens must be synced across all files. Same for the Google Fonts `<link>` tag (currently loads Inter + DM Serif Display).

**Footer is duplicated** across all HTML files, same as nav. Any footer change must be applied to every file.

**Logo directory is `/Logos/`** (capital L). Reference paths are lowercase in HTML: `/logos/...`. GitHub Pages is case-insensitive but local servers may not be.

## Design Tokens
```
Brand:   accent #55b0d1 · mid #4a9ab8 · dark #333333 · warm #C4956A
Warm:    base #F7F4F0 · surface #F0ECE6 · muted #6B6560 · heading #1C1A18
Fonts:   Inter (400/500/600/700) — body, UI, nav, cards
         DM Serif Display (400, 400i) — h1 heroes, statement h2s, CTA headings, blockquotes
```
**Color usage rules:** Cyan `#55b0d1` for interactive elements (buttons, links, active nav, logo "in"). Warm copper `#C4956A` for decorative accents (blockquote borders, Guide badges, case eyebrow labels, `.btn-secondary` hover). Darker text variant `#9B7550` for warm accent on light backgrounds (WCAG AA).

## Patterns

**Section alternation:** `bg-[#F0ECE6]` → `bg-[#F7F4F0]` → `bg-[#1C1A18]` (dark) to create visual rhythm.

**Display headings:** `font-display font-normal tracking-[-0.02em]` on h1/h2 — uses DM Serif Display. Do NOT apply to card titles, eyebrows, nav, or text under ~1.25rem.

**Background blobs:** `.bg-blob.bg-blob-cyan` or `.bg-blob-warm` — decorative gradient circles. Parent needs `relative overflow-hidden`.

**Card component:** `bg-white border border-black/[0.08] rounded-2xl p-6`

**Eyebrow label:** `text-[0.7rem] font-bold uppercase tracking-[0.14em] text-[#55b0d1]`

**CTA button:** `bg-[#333] text-white px-6 py-3 rounded-full hover:bg-[#1C1A18]`

**Active nav:** `.is-active` class on the relevant `<a>` tag (desktop + mobile).

**Hero animation (homepage):** Two-step click-triggered interaction. Step 1: full-viewport problem state with scattered, 3D-tilted workflow nodes floating behind pain-point text + "Ja, det kender vi" trigger button. Step 2: clicking the button plays a GSAP timeline — nodes assemble into an organized grid with `back.out` bounce, glow connectors draw in with arrowheads, flow dots animate along paths, solution panel fades in. The animation uses `window.__playHero` as the trigger function (inline `onclick`). Node cards use CSS `perspective` + `transform-style: preserve-3d` for 3D depth, layered box-shadows, and gradient backgrounds. Connector SVG paths have a dual-layer glow effect (blurred wider path behind the main stroke).

## SEO
- Every page: `<title>`, `<meta description>`, OG tags, canonical URL, `lang="da"`
- Schema: Organization (homepage), BreadcrumbList (all pages), Service (solution pages), Article + HowTo (pillar page)
- Sitemap at `/sitemap.xml`, robots at `/robots.txt`

## Git Conventions
- Branch naming: `seo/feature-name`, `claude/descriptive-name`
- PR workflow: branch off main → push → `gh pr create`
- Commit style: imperative mood, descriptive body
