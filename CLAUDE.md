# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Joe Palmer's personal portfolio — a fully static site served via **GitHub Pages** at the custom domain in `CNAME` (`www.joepalmer.co.uk`). There is no build step, no package manager, no test suite, and no dependencies installed locally. Everything ships as hand-written HTML/CSS/JS. Pushing to the default branch deploys.

## Running locally

Open `index.html` directly in a browser, or serve the repo root over HTTP (needed so the relative `css/` and `projects/` paths and the CDN scripts resolve correctly):

```
python3 -m http.server 8000   # then visit http://localhost:8000
```

There are no lint or test commands — verify changes visually in the browser.

## Architecture

- **`index.html`** — the single-page landing site (nav, hero, professional work, fun projects, about, contact). Inline `<script>` at the bottom drives all behavior.
- **`css/style.css`** — the shared design system for every page. All colors/spacing come from CSS custom properties defined in `:root` (`--bg`, `--surface`, `--accent`, `--text`, `--text-secondary`, `--border`, etc.). Reuse these variables rather than hardcoding values, so the project pages stay visually consistent with the landing page.
- **`projects/*.html`** — standalone sub-pages (`chess.html`, `art.html`, `gan.html`), each linked from the "Fun Projects" cards. They link back to the shared `../css/style.css` and add page-specific rules in a scoped inline `<style>` block. Images live in `projects/images/`.

### Conventions that span multiple files

- **Scroll-reveal animations:** elements get the class `reveal` in markup; an `IntersectionObserver` in `index.html`'s script adds `revealed` when they scroll into view. CSS handles the transition. The `.skill-pill` elements are revealed with a staggered delay via a second observer. Keep the `<noscript>` block in the `<head>` (it force-shows hidden content when JS is off) when adding new revealed elements.
- **Fonts:** every page loads DM Sans + Playfair Display from Google Fonts via the same `<link preconnect>` + stylesheet pattern in the `<head>`.
- **Nav:** shared markup pattern across pages — a `scrolled` class is toggled on `#nav` past 50px scroll, and a mobile hamburger toggles `.open` on `#navLinks`.

### The chess page is special

`projects/chess.html` is a self-contained React app with **no build tooling**. React 17, ReactDOM, `@babel/standalone`, and the TypeScript compiler are all pulled from unpkg CDNs. The app source lives in a `<script type="text/typescript">` block and is transpiled **at runtime in the browser**: TypeScript → JS via `ts.transpile`, then JSX via `Babel.transform`, then `eval`'d (see the script near the bottom of the file). To change game logic, edit the TypeScript inside that script block directly — there is nothing to compile or bundle.
