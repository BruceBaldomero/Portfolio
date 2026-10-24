# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static personal portfolio site for Bruce Baldomero (brucebaldomero.com). Hand-built (the original Strata template is no longer loaded). No build step — files are served as-is via GitHub Pages.

**Stack:** HTML5 · one stylesheet with CSS custom properties · vanilla JS (no jQuery) · Chivo (Google Fonts) · inline SVG icons · Formspree (contact form)

**Design direction — "the name as a monument":** the surname BALDOMERO is set as display type across the first screen and the O is a lens holding the portrait (a nod to the optical-retail work). On phones the word turns a quarter and runs down the right side. One family (Chivo 400/700/900), one ground, one ink, one gold accent; square corners; no monospace except real code; no eyebrow labels, gradients or resting shadows.

## No Build Process

There are no npm packages, no bundler, no compilation step. Open `index.html` directly in a browser (or serve the folder) to develop.

Legacy template files (`assets/css/main.css`, `assets/sass/`, `assets/js/*`, `assets/webfonts/`, `images/fulls/*.gif`, `images/thumbs/`, `images/bcg*.jpg`) are no longer referenced by `index.html`. They are kept for now and are safe to delete.

## Deployment

GitHub Pages serves from `main` branch root. `CNAME` sets the custom domain to `brucebaldomero.com`. Merging to `main` = live on the site within ~1 minute.

**Always ask the user before merging anything to `main`.**

## CSS (`assets/css/site.css`)

The only stylesheet. Sections are labelled with comment banners in page order.

### Design Tokens (`:root`)

```css
--bg: #0c0a08;        /* warm near-black ground (dark, default) */
--surface: #181512;   /* one tone step up: panels, fields */
--surface-2: #221e1a;
--ink: #ffffff;
--muted: #a19b94;     /* secondary text, ≥4.5:1 on --bg */
--accent: #c9a45a;    /* gold: primary button, active states, footer links */
--inv-*               /* the contact section inverts the page colours */
--step-small / --step-body / --step-lead / --step-head  /* the four text sizes; display type is fitted by JS */
--ease-out / --ease-expo / --ease-in-out, --t-press / --t-hover / --t-ui
```

Light mode is `:root[data-theme="light"]` (warm off-white `#f2f2f0`, darker gold `#7a5a1c`). The `data-theme` attribute is set by an inline script in `<head>` before first paint to avoid a flash.

### Responsive Breakpoints

| Breakpoint | What changes |
|---|---|
| `max-width: 900px` | Header links + theme icon hidden, "Menu" button shown; about/skills/contact collapse to one column; projects become picture-then-name cards (2 per row from 641px) |
| `641px–900px` | Taller hero so the name clears the intro |
| `max-width: 760px` | Experience rows stack (title, then company + dates) |
| `max-width: 640px` | Hero name runs vertically down the right side; single-column everything; contact email breaks after the @ |

## HTML Structure (`index.html`)

```
header.bar#bar        — Sticky bar: name, section links, #theme-toggle; #burger-btn ("Menu") + #burger-menu on ≤900px
main
  section.hero#top    — Lead sentence + CTA, facts (based in / currently / open to), h1.monument (fitted wordmark with .lens portrait)
  section.about       — "Hi, I'm Bruce." statement + paragraphs, track record + certifications lists
  section.lineup#experience — Roles as a one-size table (title / company / dates) with notes; Education part
  section.work        — Project index (big names) + sticky preview frame (desktop); each .work__item holds a <template> with description, tags and links
  section.skills      — Four modules: panel (live check runner / T-SQL sample / charge-run checklist / screenshot) + skill list
  section.contact     — Inverse colour field: heading, Formspree form, email as display type, links
footer.footer         — Fitted full-name wordmark, links, copyright + back to top
dialog#project-dialog — Project preview (video loop or still, description, tags, links)
```

Project media lives in `images/work/` — a still `.jpg` per project, plus an `.mp4` loop for the ones that had GIF recordings (converted with ffmpeg; ~9 MB total instead of ~125 MB of GIFs).

## Inline JavaScript (bottom of `index.html`)

- **Theme toggle** — `toggleTheme()` flips `data-theme` on `<html>` and stores `localStorage('theme')` as `dark-mode`/`light-mode` (same values as before). `updateThemeUI()` syncs `#theme-toggle`'s label, `#burger-theme-toggle`'s text and the `theme-color` meta.
- **Fit** — `fitAll()` sizes the hero monument (to the width, or to the hero height when vertical on phones) and the footer wordmark; re-runs on resize and after fonts load.
- **Scroll reveal** — `IntersectionObserver` adds `.is-in` to `[data-reveal]` once. Hidden states only apply under `html.js` and `prefers-reduced-motion: no-preference`, so content is never stuck hidden.
- **Nav active state** — `IntersectionObserver` sets `aria-current` on the matching `.nav-link` (desktop and menu).
- **Mobile menu** — `openBurger()` / `closeBurger()`; closes on outside click, Escape, or a link tap.
- **Projects** — hover/focus a row to set the active project (frame image swaps; desktop plays the project's `.mp4` if it has one). Clicking a row opens `#project-dialog` with the row's `<template>` content. Without JS, rows are plain links to the live site/code/thesis.
- **Check runner** — `#run-checks` runs eight real checks against the page (one h1, alt text, link targets, noopener, labelled fields, overflow, body contrast, load time) and reports results.
- **Contact form** — submits to Formspree with `fetch` and shows the result inline; without JS it posts normally.

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
