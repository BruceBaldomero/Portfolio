# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static personal portfolio site for Bruce Baldomero (brucebaldomero.com). Built on the Strata template by HTML5 UP (CCA 3.0), heavily customised with 600+ lines of additional CSS. No build step — files are served as-is via GitHub Pages.

**Stack:** HTML5 · CSS3 (custom properties) · Vanilla JS + jQuery · Font Awesome 6 · DM Sans / DM Mono (Google Fonts) · Formspree (contact form)

## No Build Process

There are no npm packages, no bundler, no compilation step. Open `index.html` directly in a browser to develop. The SASS files in `assets/sass/` are source references for the base template only — they are **not compiled on deploy**. `assets/css/main.css` is already the compiled output and is committed to git.

## Deployment

GitHub Pages serves from `main` branch root. `CNAME` sets the custom domain to `brucebaldomero.com`. Merging to `main` = live on the site within ~1 minute.

**Always ask the user before merging anything to `main`.**

## CSS Architecture

Two CSS files load in order:

1. **`assets/css/main.css`** — Compiled base template. Do not edit directly. Contains breakpoint system, layout, and the dark/light theme toggle button.
2. **`assets/css/custom.css`** — All custom work goes here. Loads last so it overrides the base.

### Design Tokens (defined in `custom.css` `:root`)

```css
--accent: #4f9cf9;               /* Blue — primary highlight colour (light mode: #1a5fb4 for contrast) */
--accent-dim / --accent-border / --accent-hover
--card-bg / --card-border / --tag-bg / --tag-border / --divider
--text-soft: rgba(255,255,255,0.80);  /* secondary running text */
--muted: rgba(255,255,255,0.60);      /* meta: dates, labels, captions (≥4.5:1) */
--fs-label 13px · --fs-small 14px · --fs-body 16px · --fs-text 17px · --fs-lead 18px · --fs-h2 32px
--ease: cubic-bezier(0.22,1,0.36,1); --t-press: 140ms; --t-hover: 180ms;
--link-color: var(--accent);     /* overrides the template's yellow #ffb300 (links + theme toggle) */
```

Use these tokens instead of opacity on text: faded text (opacity 0.45–0.6) was the main readability problem. Hover styles sit inside `@media (hover: hover) and (pointer: fine)`; pressable elements get a small `scale()` on `:active`. Responsive overrides live in one block near the end of `custom.css`, after the base rules, so source order doesn't undo them.

Background is `#242424` (dark mode default, from `main.css`). Light mode overrides these variables via `body.light-mode { }`.

### Responsive Breakpoints (custom.css)

| Breakpoint | What changes |
|---|---|
| `max-width: 1280px` | Nav tabs shrink |
| `max-width: 1100px` | About/skills/contact collapse to single column |
| `max-width: 980px` | Desktop nav tabs hidden; burger menu button visible |
| `max-width: 736px` | Timeline stacks; projects go single column |
| `max-width: 480px` | Skills grid single column; further text scaling |

## HTML Structure (`index.html`)

Single page, five scroll sections:

```
#header          — Avatar, name, job title, .header-pitch one-liner, desktop #nav-tabs (hidden <980px)
#main
  #one           — About (2-col: text + 2×2 stats/certs)
  #work          — Selected Work: case studies (.case, problem / what I did / result) + live QA check card (#run-checks)
  #skills        — Skills grid (6 cards) + Timeline (dates column, whole entry highlights on hover, tool tags per role) + Education
  #two           — Projects: 2 featured cards (ISKO, Mukha) + compact "Earlier projects" list (.work-list), all with poptrox lightbox
  #three         — Contact (2-col: quick links + Formspree form)
#burger-btn      — Hamburger button, fixed top-right, visible <980px only
#burger-menu     — Dropdown nav (5 links + theme toggle), opens from burger-btn
#footer          — Social icons + copyright
#theme-toggle    — Floating circular dark/light button, hidden on mobile
```

## Case study pages

- `status-board.html` — "Live service status board with Teams alerts". Same sidebar, stylesheets and scripts as `index.html`; its own small inline script (theme, reveal, nav, burger). The sidebar name is a `<p class="header-name">` so the case title can be the page's only `<h1>`.
- Linked from the lead card (`.case--feature`) in the Selected Work section of `index.html`.
- **Privacy rules for work case studies:** no employer name/domains or the board's real URL, no client/retailer names, no colleague names (say "the CIO"), no supplier brand names (describe by type), no webhook/IP/internal URLs, no repo links, no screenshots — use the HTML mock-ups labelled "Illustrative – names changed" with `.example.com` names.

## Inline JavaScript (bottom of `index.html`)

All JS logic lives in a single `<script>` block at the bottom of `index.html`:

- **Theme toggle** — Reads/writes `localStorage('theme')`, applies `dark-mode`/`light-mode` class to `body`. Updates both the floating `#theme-toggle` button and the `#burger-theme-toggle` inside the burger menu simultaneously via `updateThemeUI()`.
- **Scroll reveal** — `IntersectionObserver` adds `.visible` to `.reveal` elements as they enter the viewport.
- **Nav active state** — Second `IntersectionObserver` tracks which section is in view and syncs `.active` class on both `.nav-tab` (desktop) and `.burger-nav-tab` (mobile) elements.
- **Burger menu** — Toggle `.open` on `#burger-btn` and `#burger-menu`; close on outside click or nav link click. `closeBurger()` is a named function (hoisted) so it's callable from the theme toggle handler above it.
- **Lightbox thumbnails** — poptrox removes the `href` from `#two .work-item a.image`, so on load they get `role="button"`, `tabindex="0"`, an aria-label and Enter/Space handling (keyboard users can open previews).
- **Live QA checks** — `#run-checks` runs eight real checks against the page (one h1, alt text, link targets, noopener, labelled fields, overflow, body contrast, load time) and reports pass/fail per row. Keep the page passing them.
- **Smooth scroll** — Intercepts clicks on all nav tabs and scrolls to the target section (instant under reduced motion).

## Project media

- Featured thumbnails: `images/iskothumb.png`, `images/mukhafaceyoga.jpeg`; earlier projects use `images/thumbs/*.jpg`.
- Lightbox previews for the screen recordings are animated WebP in `images/previews/` (converted from the GIFs in `images/fulls/`, ~157 MB → ~20 MB). PHP and UI/UX still use their original GIFs because those were already small.
- To convert a new recording: `ffmpeg -i in.gif -vf "fps=8,scale='min(960,iw)':-2:flags=lanczos" -c:v libwebp -lossless 0 -q:v 50 -compression_level 6 -loop 0 -an out.webp`

## Mobile Navigation

The burger menu (`#burger-btn` + `#burger-menu`) replaces both the sidebar nav and the floating theme toggle on mobile:

- `#theme-toggle` is hidden via `display: none` inside the `max-width: 980px` media query in `custom.css`
- The theme toggle is duplicated as `#burger-theme-toggle` (a `<button>` inside `#burger-menu`)
- The burger menu dropdown is a compact card anchored top-right — not full-screen

## Git Workflow

- Feature branches are named `claude/<description>-<id>`
- All changes go to a feature branch first, then a PR is created and merged to `main`
- **Always ask the user for approval before merging a PR to `main`**
- Squash merge is the standard merge method

## LinkedIn Banner

`linkedin-banner.html` + `linkedin-banner.png` are in the repo root. To regenerate the PNG after editing the HTML:

```bash
node -e "
const { chromium } = require('/opt/node22/lib/node_modules/playwright');
(async () => {
  const browser = await chromium.launch({ executablePath: '/opt/pw-browsers/chromium-1194/chrome-linux/chrome' });
  const page = await browser.newPage();
  await page.setViewportSize({ width: 1584, height: 396 });
  await page.goto('file:///home/user/Portfolio/linkedin-banner.html');
  await page.waitForTimeout(1500);
  await page.screenshot({ path: '/home/user/Portfolio/linkedin-banner.png', clip: { x:0, y:0, width:1584, height:396 } });
  await browser.close();
})();
"
```

## Key People & Content

- **Owner:** Bruce Baldomero — Technical Operations & QA Specialist, London
- **Employer:** Adaro Optics
- **Clients:** Vision Express, David Clulow, Boots, Specsavers, independent optical practices across the UK
- **Key tools on site:** Playwright, SQL/T-SQL, Freshdesk, Confluence, Jira, Stripe, Smart Debit
- **Certifications:** Cisco CCNA (Intro to Networks + Routing & Switching)
- **Contact form:** Formspree backend (action URL in the form element)
