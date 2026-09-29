# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> **History of this repository.** This site has been going since 26 April 2018 (the first commit), and every line of it was hand-written until **29 September 2026**. That is the day this file was added and Claude Code started making changes alongside the maintainers. Anything committed before that date is hand-written.

## Overview

A static, zero-build website (served via GitHub Pages; `CNAME` sets the custom domain) with two hand-written pages: `index.html` and `about.html`. There is no package.json, build step, linter, or test suite. To preview, open the HTML files in a browser or run `python3 -m http.server`.

## Architecture

- `index.html` holds all CSS and JS inline. The script counts days since `noGovDate` (`new Date(2024, 1, 3, 13, 0)`, months are zero-indexed, so this is 3 Feb 2024) via `Date.daysSince`, and fills `#days-container` on `DOMContentLoaded`. It also rewrites the `og:title` and `og:description` meta tags with the current count.
- The salary code (`salaryCount`, `updateCounter`, `displayCurrency`) is intentionally kept but currently unused. It targets `#salary-container` and `#mla-container`, which are not in the markup. `updateCounter` now returns quietly when neither element exists, and updates whichever exist if they are restored (tested by injecting both elements). Don't remove it unless asked.
- `about.html` is static prose. It has a dated "Update" section at the top, then the counting methodology (the Belgium 589-day record comparison) and events. New developments are added as a new `<h1>` update at the top.
- Both pages carry their own copy of the styling (`index.html` is pink `#dd9ca6`, `about.html` is blue `#9ca6dd`) and load the Oswald font from Google Fonts. Both include the same Google Analytics snippet.

## Visual style (verified by rendering both pages at 1280px and 390px)

Match this exactly when adding or changing anything. Values are computed styles from a real render, not guesses.

**Overall look:** one flat, pastel colour block with white Oswald text and a single muted, darker link colour. There are no images, borders, shadows, cards, buttons, nav bar or footer element. The pages have almost no chrome, and new content should stay that way.

**Structure (both pages):**
- The whole page is one `<div id="main">` inside a bare `<body>`. Only `#main` is styled. `body` has no CSS, so the browser's default 8px margin shows as a thin white border around the block. Text outside `#main` would render in Times.
- `#main { width: 80%; padding: 10%; color: #fff; font-family: 'Oswald', sans-serif; }`. This is content-box, so 80% plus 10% padding on each side equals 100% of the body width. Padding is percentage-based, so it is about 126px each side at desktop and about 37px on a phone.
- The block's height comes from its content, not the viewport. A strip of white shows below it on tall windows. Don't add `min-height` or `100vh` unless asked.
- There is no `max-width`, no `line-height` (browser default `normal`) and no CSS reset. Spacing is browser-default margins on `p`, `h1`, `h2` and `ul`.
- Both files start with `<!DOCTYPE html>` and so render in standards mode (they were in quirks mode until the 2026 update). The switch changes line-box height for lines that contain only `<small>` text, which is why the homepage footer paragraph has its own `.footer` class. Any new `<small>` block at the paragraph level needs the same treatment to keep the small, tight line spacing. Re-render the pages and compare geometry before and after any change like this.

**Colours (the two pages are a matched pair):**

| | `index.html` | `about.html` |
|---|---|---|
| Background | `#dd9ca6` (dusty pink) | `#9ca6dd` (periwinkle) |
| Link colour | `#7d4c56` (mauve) | `#4c567d` (slate) |
| Text | `#fff` | `#fff` |

- The blue pair is the pink pair with the RGB channels rotated. `#dd9ca6` becomes `#9ca6dd` and `#7d4c56` becomes `#4c567d`. A new page should follow the same rule: a pastel background, a darker link colour of the same hue, and white text.
- Links use the browser default underline. There is no hover style.
- White on the pastel backgrounds is low contrast. I did not measure it. Treat this as existing style and flag it before changing it.

**Typography:**
- Font: Oswald, a condensed sans-serif, loaded with `<link href='https://fonts.googleapis.com/css?family=Oswald' ...>`. That URL requests weight 400 only, so the bold h1 and h2 on `about.html` are browser-synthesised bold. If you add a real bold weight (`css?family=Oswald:700`), the headings will change appearance, so ask first.
- `index.html`, centred, base `2.25em` (36px):
  - `#days-container` is `display: block`, `calc(300% + 1vw)` (about 121px at 1280px, 112px at 390px). It is the visual focus.
  - The intro line and the "We have a government!" link are 36px.
  - `<small>` is 50% (18px) for the "More info", credits and attribution lines.
  - `p` margins are 36px, giving airy vertical spacing.
  - The layout is one centred stack: link, sentence, huge number, "days!", small links.
- `about.html`, left-aligned, base `1.25em` (20px):
  - `h1` is 40px bold (one per dated update, newest first) and `h2` is 30px bold.
  - `p`, `li` and links are 20px, with 20px paragraph margins.
  - Lists are default bullets. Emphasis uses `<strong>` and `<em>`.
  - The text column is about 1010px wide on desktop.

**Responsive behaviour:** there are no media queries that take effect. It is a fluid layout, and both pages fit a 390px viewport without horizontal scroll (checked `scrollWidth == clientWidth`). The `@media (min-width: 575px) { article { ... } }` rule in `about.html` is dead code, because no `<article>` element exists. The unused `.record` class and the `#mla-container` and `#salary-container` rules in `index.html` are also leftovers.

**Voice of the copy:** short, plain and neutral. `about.html` states that the site is "purely informational, without commentary". The homepage uses an exclamation ("days!"). Dates are written like "Saturday Feb 3rd at 1400 hours", and each update is a bold heading followed by a short factual paragraph with a linked source. Keep new content in this register and add sources as links.

**Head boilerplate to copy:** title `Days since Northern Ireland had a government` (about page adds ` : About`), the Open Graph tags, `viewport` meta, the Oswald `<link>`, inline `<style>`, and the Google Analytics snippet at the end of `<body>`.

## Where each style lives in the code

Line numbers are as of this writing. Re-check with `grep -n` before editing, because they drift. Both files keep all CSS in one inline `<style>` block in `<head>` and there is no shared stylesheet. **A visual change usually has to be made in both files**, and the two copies are not identical.

| Style entity | `index.html` | `about.html` | Notes |
|---|---|---|---|
| Doctype | line 1 | line 1 | `<!DOCTYPE html>`. |
| Oswald `<link>` | line 16 | line 15 | Weight 400 only. Change the URL in both files. |
| `<style>` block | lines 17-62 | lines 16-47 | |
| `#main` (background, width, padding, font, size, alignment) | 29-37 | 17-25 | Differs per page: `#dd9ca6`, centred, `2.25em` versus `#9ca6dd`, left, `1.25em`. |
| `a` (link colour) | 45-47 | 33-35 | `#7d4c56` versus `#4c567d`. This is the only link rule, with no hover or visited styles. |
| `small` | 49-51 | 37-39 | `font-size: 50%`. It is only used on `index.html` (lines 76-81), and the rule is dead in `about.html`. |
| `.footer`, `.footer small` | 54-61 | not present | Homepage footer paragraph is sized at 50% with a `2em` bottom margin, and `<small>` inside it is reset to 100%. Together they reproduce the old quirks-mode spacing. |
| `#days-container` (big number) | 18-21 | not present | `display: block; font-size: calc(300% + 1.0vw)`. It is a `<span>` at line 71 filled by JS, so keep the span and id. |
| `#mla-container`, `#salary-container` | 23-27 | not present | Unused: no matching elements exist (the JS handles that quietly). |
| `.record` | 39-43 | 27-31 | Dead in both files. |
| `@media (min-width: 575px) { article {...} }` | not present | 40-46 | Dead: no `<article>` element. The obvious use would be a `max-width: 550px` centred column, but it doesn't apply today. |
| Page wrapper | `<div id="main">` at 67 | `<div id="main">` at 52 | Everything visible goes inside it. |

Markup patterns to reuse:
- **Homepage headline link:** `<span><a href="">We have a government!</a></span>` (`index.html:68`), followed by the sentence and the `#days-container` span (69-73), then the `<p class="footer">` of `<small>` lines (75-82). Add new footer or credit lines as another `<small>` in that paragraph, matching lines 76-81.
- **`about.html` update entry:** an `<h1>YYYY Update: Title</h1>` followed by plain text or `<p>`, with the newest entry first. The first `<h1>` is at line 53 and the 2022 entry at 59. Add a new one directly under `<div id="main">` (line 52). Sub-sections use `<h2>` (line 66) and the "Events" section is an `<h1>` (line 102).
- **Bullet lists and emphasis:** the NI/Belgium dates list in `about.html` (lines 83-89) uses `<ul><li>` with `<strong>` for the date and `<a>` for "(more)". Reuse that pattern for dated facts with sources. The two `<li>` items (lines 86 and 88) end with stray `</p>` tags that have no opening `<p>`. Browsers tolerate this, so don't copy the stray tags into new markup.
- **Update entries have no `<p>` wrapper:** the 2024 and 2022 entries (lines 54-57 and 60-64) are bare text directly under the `<h1>`. Later sections use `<p>`. Either renders the same, but prefer `<p>` for new prose.
- **`<title>`:** `index.html:5`, `about.html:5` (` : About` suffix). Open Graph tags are at lines 8-12 of both files. `index.html` overwrites `og:title` and `og:description` from JS at lines 181-182.
- **Analytics snippet:** the last block in `<body>` (`index.html:186-193`, `about.html:116-123`). A new page needs a copy of it.
- **Counting logic:** `noGovDate` is at `index.html:88`. It is the only JS that needs to change when the government status changes, together with the wording at lines 68-72.

If you add a new page, copy `about.html` for text pages (left-aligned) or `index.html` for a big-number style page. Rotate the colour pair as described above, and link it from `index.html` next to "More info" (line 76).

## History page (`history.html`, added in PR #30)

- Third page, in warm sand: `#d4c5a9` background, `#6b5f45` links, white text, hatched "No Executive". The colour rule (a pastel with a darker link colour of the same hue) is the same, but sand is not a channel rotation of pink and blue. Green and orange are ruled out for political reasons, and lavender was rejected as too close to the pink.
- Everything is drawn in the browser from the `PERIODS` array at the top of the page's script. Each entry has `start`, `end` (`null` for the current period), `inOffice` and `note`. The summary, timeline bar, year grid and table are all generated from it, and "today" is the visitor's clock. **When the government collapses or is restored, edit `PERIODS` (end the current period, add a new one) and also `noGovDate` in `index.html`.** They are separate.
- Source links: the `SOURCES` object in `history.html` maps a boundary date (`"YYYY-MM-DD"`, same string as in `PERIODS`) to `{ url, label }`. A date with an entry gets a superscript `↗︎` link (U+2197 plus the text-presentation selector, system font because Oswald has no arrows) in both the "To" cell of one row and the "From" cell of the next. Every date now has an entry, but not all are equally strong. The user chose to keep the 2011 and 2016 election gaps as they were ("a bit of fun, not the paper of record"). The weaker links are: 16 May 2011 (Irish Times, published 17 May 2011 at 01:00, about the ministers' vote), and 16 May 2016 and 26 May 2016 (Wikipedia pages for the 4th and 5th Executives; the 4th page's own infobox says 6 May 2016, and the Assembly minutes show the departmental ministers took office on 25 May 2016). Research found the First Minister and deputy First Minister stayed in office through both elections (Northern Ireland Act 1998 s.16A before 2022), which is why the user was offered merging the gaps; they declined. BBC pages could not be fetched from the sandbox, so no BBC link is confirmed.
- Launch marker: `LAUNCH` in `history.html` (`{ date: "2018-04-26", label: "Site launched" }`, the date of the first commit to this repo). It shows as a small ringed dot on the top edge of the timeline bar, a smaller dot in that month's grid cell, and a muted legend line. The user wants it "pretty subtle; we're not the story", so keep it small, with no text label on the charts themselves.
- Year ticks are generated every five years, and the grid labels every second year (every fourth on phones), so nothing is hardcoded to a year.
- It uses `@media (max-width: 600px)` for phones, with the in-bar words and the Note column hidden. The `about.html` line numbers in the table above are +2 below `<div id="main">` because of the history link added there. The homepage footer link is on the `More info` line.
- The page has `<meta charset="utf-8">`. The other two pages do not, so use `&eacute;` style entities for accented characters there.
- Dates come from Wikipedia's Northern Ireland Executive article. Not independently confirmed: the 1999 start, the 2011 election dates and the 2016 end of the Executive. The homepage's PR previews come from Netlify (`netlify.toml`).

## Rendering the site to check changes

Chromium is preinstalled (`/opt/pw-browsers/chromium-1194/chrome-linux/chrome`) and Playwright is installed globally for Node (`require(execSync('npm root -g') + '/playwright')`).
- Launch with `executablePath` set to the Chromium above, `args: ['--no-sandbox']`, `proxy: { server: process.env.HTTPS_PROXY }` and `ignoreHTTPSErrors: true`. Without the proxy, the Google Fonts request fails and text silently falls back to a default sans-serif.
- Set the viewport explicitly (1280x900 and 390x844) and wait about 2.5s for the font. Don't use `chrome --headless --screenshot` for phone widths, because headless Chrome enforces a minimum window width of about 500px and crops the image.
- `document.fonts.check('16px Oswald')` returns true even when no font loaded. Check that `[...document.fonts]` contains an Oswald face with status `loaded`.
- Analytics requests fail in the sandbox, which is expected.

## Notes

- When the government status changes (collapse or restoration), update `noGovDate` and the "We have a government!" / status wording in `index.html`, and add an update entry in `about.html`.
- **Do not push until the user says to.** Every push to a PR branch triggers a Netlify deploy preview, so batch changes into local commits and push once on request. Don't open PRs or push branches unprompted. Local commits are fine.
- All three pages share the same header line (`<p class="nav">`: `Home · About · History`, current page unlinked, about 16px at the top of `#main`). Keep it identical if you add a page.
- `CLAUDE.md` is tracked and public, and the README links to it. Write it so anyone reading the repository can follow it, not only Claude.
