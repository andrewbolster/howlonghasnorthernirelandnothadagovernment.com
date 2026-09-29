# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> **History of this repository.** This site has been going since 26 April 2018 (the first commit), and every line of it was hand-written until **29 September 2026**. That is the day this file was added and Claude Code started making changes alongside the maintainers. Anything committed before that date is hand-written.

## Overview

A static, zero-build website with three pages: `index.html` (the counter), `about.html` (how the counting works) and `history.html` (a timeline of every period an Executive was in office). It is served by GitHub Pages, and `CNAME` sets the custom domain. There is no package.json, build step, linter or test suite. Preview by opening the HTML files in a browser, or run `python3 -m http.server`. Pull requests get a Netlify Deploy Preview (`netlify.toml` publishes the repo root as it is and marks previews `noindex`). `README.md` explains the site for visitors.

## When the government falls or returns

Three places need editing, and they are separate:

1. `index.html`: change `noGovDate` (months are zero-indexed, so February is `1`) and the status wording at the top.
2. `history.html`: end the current period in the `PERIODS` array and add a new one, and add a `SOURCES` entry for the new date.
3. `about.html`: add a dated update as a new `<h1>` at the top.

## Architecture

- **`index.html`** holds all CSS and JS inline. The script counts days since `noGovDate` with `Date.daysSince` and fills `#days-container` on `DOMContentLoaded`. It also rewrites the `og:title` and `og:description` meta tags with the current count.
- **The salary code** (`salaryCount`, `updateCounter`, `displayCurrency`) is intentionally kept but unused. It targets `#salary-container` and `#mla-container`, which are not in the markup, and `updateCounter` returns quietly when neither exists. Don't remove it unless asked.
- **`about.html`** is static prose: dated updates (newest first), the counting methodology (the Belgium 589-day comparison) and events.
- **`history.html`** draws everything in the browser from data at the top of its script (see "History page" below).
- Every page carries its own inline copy of the CSS, so **a visual change usually has to be made in every file**, and the copies are not identical. Find things with `grep -n` rather than trusting line numbers.
- All pages load the Oswald font from Google Fonts and include the same Google Analytics 4 snippet (measurement ID `G-N73RZEJ1WN`) at the end of `<body>`.

## Shared conventions

- **Header line:** every page starts `#main` with `<p class="nav">Home · About · History</p>`, with the current page as plain text and the other two as links. About and History use 80% (about 16px); the homepage uses 45% of its larger base, left-aligned, so it also comes out at about 16px. Keep the same text, position and size if you add a page.
- **Doctype and charset:** every page starts `<!DOCTYPE html>`. Only `history.html` has `<meta charset="utf-8">`; on the other two, write accented characters as entities (`&eacute;`).
- **Head boilerplate to copy:** the title (`Days since Northern Ireland had a government`, with ` : About` or ` : History` added), the Open Graph tags, the `viewport` meta, the Oswald `<link>`, an inline `<style>`, and the analytics snippet.
- **Adding a page:** copy `about.html` for text or `history.html` for charts, pick a new pastel and darker link colour (below), and add it to the header line on every page.

## Visual style

Match this exactly when adding or changing anything. The values are computed styles from real renders at 1280px and 390px.

**Overall look:** one flat, pastel colour block with white Oswald text and one muted, darker link colour. There are no images, borders, shadows, cards or buttons. The only chrome is the header line at the top and, on the homepage, a small credits footer. New content should stay that plain.

**Structure:**
- The page is one `<div id="main">` inside a bare `<body>`. Only `#main` is styled, so the browser's default 8px body margin shows as a thin white border, and text outside `#main` would render in Times.
- `#main` is `width: 80%; padding: 10%` (content-box, so it fills the body width) with `color: #fff` and `font-family: 'Oswald', sans-serif`. The padding is a percentage, about 126px each side on desktop and 37px on a phone.
- The block's height comes from its content, not the viewport, so a strip of white can show below it. Don't add `min-height` or `100vh` unless asked.
- There is no `max-width`, no `line-height` (browser default) and no CSS reset. Spacing is browser-default margins on `p`, `h1`, `h2` and `ul`.
- The pages render in standards mode. That changes line-box height for lines that hold only `<small>` text, which is why the homepage footer paragraph has its own `.footer` class (50% size, `2em` bottom margin, with `<small>` inside it reset to 100%). Any new paragraph of `<small>` text needs the same treatment. Re-render and compare geometry after a change like this.

**Colours (each page is a pastel with a darker link colour of the same hue and white text):**

| | `index.html` | `about.html` | `history.html` |
|---|---|---|---|
| Background | `#dd9ca6` (dusty pink) | `#9ca6dd` (periwinkle) | `#d4c5a9` (warm sand) |
| Link colour | `#7d4c56` (mauve) | `#4c567d` (slate) | `#6b5f45` (umber) |

- The blue is the pink with the RGB channels rotated once. Sand is not a rotation; it was chosen because green and orange carry political associations in Northern Ireland and should be avoided, and other pastels sat too close to the pink or blue.
- Links use the browser default underline, with no hover style (apart from the small source arrows on the history page).
- White text on these pastels is low contrast. It is the existing style, so flag it before changing it.

**Typography:** Oswald, a condensed sans-serif, loaded with `css?family=Oswald`. That requests weight 400 only, so the bold `h1` and `h2` are browser-synthesised bold. Adding a real bold weight changes how the headings look, so ask first.
- `index.html`: centred, base `2.25em` (36px). `#days-container` is `display: block` at `calc(300% + 1vw)` and is the visual focus. `<small>` is 50% (18px) for the credits. `p` margins are 36px. It is one centred stack: link, sentence, huge number, "days!", small links.
- `about.html` and `history.html`: left-aligned, base `1.25em` (20px), `h1` 40px bold, `h2` 30px bold, `p`, `li` and links 20px, tables 16px. The text column is about 1010px wide on desktop.

**Responsive behaviour:** the layout is fluid and every page fits a 390px viewport without sideways scrolling. `history.html` also has a `@media (max-width: 600px)` block (see below). Leftovers that do nothing: the `.record` rule (both files), the `@media (min-width: 575px) { article {...} }` rule and `small` rule in `about.html`, and the `#mla-container` and `#salary-container` rules in `index.html`.

**Voice of the copy:** short, plain and neutral. `about.html` says the site is "purely informational, without commentary". The homepage uses an exclamation ("days!"). Dates read like "Saturday Feb 3rd at 1400 hours". Each update is a bold heading followed by a short factual paragraph with a linked source. Keep new content in that register and add sources as links.

**Markup habits on `about.html`:** an update is an `<h1>YYYY Update: Title</h1>` followed by text (the two existing updates are bare text with no `<p>`; prefer `<p>` for new prose). Dated facts use `<ul><li>` with `<strong>` for the date and `<a>` for "(more)". The two `<li>` items in the NI/Belgium list end in stray `</p>` tags that browsers tolerate, so don't copy them.

## History page

- **Data:** `PERIODS` is an array of `{ start, end, inOffice, note }` (`end` is `null` for the current period, dates are `"YYYY-MM-DD"`). The summary line, timeline bar, year-by-month grid and table are all generated from it, using the visitor's clock as "today", so the current period and the statistics grow on their own. Year ticks are generated every five years and grid labels every second year (every fourth on phones), so nothing is fixed to a particular year.
- **Look:** three views of the same data: a timeline bar (white for "In office", hatched for "No Executive", so meaning never depends on colour alone), a grid with one column per year and one row per month, and a full table. On phones the in-bar words and the Note column are hidden.
- **Source links:** `SOURCES` maps a boundary date (the same string used in `PERIODS`) to `{ url, label }`. A date with an entry gets a superscript arrow (code point U+2197 followed by U+FE0E, which forces the text form rather than an emoji; drawn in a system font, because Oswald has no arrows) after it in both the "To" cell of one row and the "From" cell of the next, since they are the same event. `sourceLink()` builds every such link, including the launch legend's. Links open in a new tab with a descriptive `aria-label`.
- **Launch marker:** `LAUNCH` (`{ date: "2018-04-26", ... }`, the first commit to this repository) is drawn as a small ringed dot centred on the timeline bar, a smaller dot in that month's grid cell, and a muted legend line linking to the original pull request. Keep it small and quiet; the history of the Executive is the subject, not the repository. The bar segment the dot sits in has no wording of its own, because they would overlap.
- **Data caveats:** dates come from Wikipedia's article on the Northern Ireland Executive, checked where possible against legislation.gov.uk, the Northern Ireland Assembly's pages and news reports. The weakest links are 16 May 2011 (an Irish Times report published at 01:00 on 17 May), and 16 May and 26 May 2016 (Wikipedia pages; the 4th Executive page's own infobox says 6 May 2016, and Assembly minutes show the departmental ministers taking office on 25 May 2016). The 2011 and 2016 "election period" gaps are a deliberate simplification: research suggests the First Minister and deputy First Minister stayed in office through both elections under the Northern Ireland Act 1998 as it stood before 2022, and only the departmental posts were briefly empty. That reading was not checked in full. No BBC links are used, because those pages could not be fetched when the sources were checked.

## Checking changes

Render the pages in headless Chromium (for example with Playwright) at 1280x900 and 390x844, and check that nothing scrolls sideways (`scrollWidth == clientWidth`) and that there are no script errors.
- Wait for the Oswald font before judging layout. `document.fonts.check('16px Oswald')` returns true even when no font loaded, so check that an Oswald face in `document.fonts` has status `loaded`. If the Google Fonts request is blocked (for example behind a proxy), text silently falls back to a default sans-serif.
- Set the viewport explicitly. `chrome --headless --screenshot` enforces a minimum window width of about 500px and crops phone-width shots.
- Compare page geometry (for example the height of `#main`) before and after a change to spot layout drift.
- Analytics requests fail in a sandbox, which is expected.

## Workflow

- Every push to a PR branch triggers a Netlify Deploy Preview, so batch changes into a few local commits and push once. Ask before pushing or opening a PR.
- `CLAUDE.md` is tracked and public, and the README links to it. Write it so anyone reading the repository can follow it, not only Claude.
