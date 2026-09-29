# How long has Northern Ireland not had a government?

A small, static website that counts the days Northern Ireland has had (or has not had) a functioning power-sharing government.

Live at <https://howlonghasnorthernirelandnothadagovernment.com>.

It is a bit of fun and not the paper of record. If you'd like to argue with a date or a reference, please [raise an issue](https://github.com/andrewbolster/howlonghasnorthernirelandnothadagovernment.com/issues).

## Pages

| Page | What it is |
|---|---|
| `index.html` | The big counter: days since the Executive was last restored. |
| `about.html` | How the counting works, the updates each time the government has fallen or returned, and links to related events. |
| `history.html` | A timeline, a year-by-month grid and a table of every period since 1999 when an Executive was in office or not, with a source link on each date. |

Each page is a single ["hand-written"](CLAUDE.md) HTML file with its own inline CSS and JavaScript. There is no build step, no package manager and no test suite.

## Running it locally

Open the HTML files in a browser, or serve the folder:

```sh
python3 -m http.server
```

then visit <http://localhost:8000>.

## Updating it when the government falls or returns

1. **`index.html`**: change `noGovDate` (months are zero-indexed, so February is `1`) and the status wording near the top.
2. **`history.html`**: edit the `PERIODS` list at the top of the script (end the current period and add a new one), and add a `SOURCES` entry for the new date. The counts, timeline, grid and table all draw themselves from those two lists.
3. **`about.html`**: add a dated update as a new `<h1>` at the top.

The counter on the homepage and the history page are separate, so both need editing.

## How the history dates were chosen

The dates started from Wikipedia's article on the [Northern Ireland Executive](https://en.wikipedia.org/wiki/Northern_Ireland_Executive) and were checked where possible against legislation.gov.uk, the Northern Ireland Assembly's own pages and news reports. Each date on the history page links to the source used. Some sources are weaker than others, and a few dates around the 2011 and 2016 elections come from Wikipedia alone.

## Hosting and previews

The live site is served from this repository by GitHub Pages, and `CNAME` sets the custom domain.

Pull requests get a Netlify Deploy Preview, configured by `netlify.toml`. It publishes the repository root as it is, with no build, and marks previews `noindex` so they stay out of search results. It also hides `README.md` and `CLAUDE.md` there, since those are for people reading the repository.

## Credits

- With thanks to [hearmecode/days-since](https://github.com/hearmecode/days-since), as credited on the homepage.
- Built by [NITD #Politics](https://nitech.slack.com).
- The font is [Oswald](https://fonts.google.com/specimen/Oswald), loaded from Google Fonts.

## Licence

Public domain, under the [Unlicense](LICENSE).
