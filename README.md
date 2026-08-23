# The 24th Group

Source for [the24thgroup-758593152.development.catalystserverless.com](https://the24thgroup-758593152.development.catalystserverless.com/app/index.html) — a single-page site for The 24th Group, a founder-led venture and operations company, and its brand collateral.

## Structure

```
index.html              The site — hero, about, and the project listings
logo/                    Logos for each listed project
client-package.json      Manifest required by Zoho Catalyst's Web Client Hosting

brand/
  brand-guide.html       Logo, color, typography and interface reference
  assets/                Standalone logo files (lockup + mark, black/white)
  templates/             Letterhead, memo and proposal cover (.docx)
```

The site is a single static HTML file by design — no build step, no framework. All styling is inline in `index.html`; the only external dependency is the Oswald headline typeface, loaded from Google Fonts.

## Projects listed on the site

| Project | Status | Notes |
|---|---|---|
| TheReplayMAG | Live | Links to [thereplaymag.com](https://thereplaymag.com) |
| Ogoro | In progress | Operations Intelligence Platform |
| Koroko | In progress | Location infrastructure for Nigeria |
| Oroko | In progress | Process mapping tool |

Each card links out to its project (with UTM tracking) except where noted.

## Local preview

No build tooling required — serve the folder with anything static, e.g.:

```bash
python3 -m http.server 8420
```

Then open `http://localhost:8420`.

## Deploying

The site deploys to [Zoho Catalyst](https://catalyst.zoho.com) Web Client Hosting. Two ways to push a build:

**Catalyst CLI** (what this project has been deployed with):

```bash
catalyst deploy --only client
```

Requires a local `.catalystrc` + `catalyst.json` linking this directory to the `The24thGroup` Catalyst project (id `8644000000555021`, org `758593152`) with a `client/` folder containing the site files. This isn't checked into the repo since it's machine/session-specific — set it up fresh by pulling the project details from the Catalyst console or API and pointing `catalyst.json`'s `client.source` at a folder containing `index.html`, `logo/`, and `client-package.json`.

**Console upload**: zip `index.html`, `logo/`, and `client-package.json` together (with `client-package.json` at the zip root) and upload via Catalyst Console → Web Client Hosting → Upload Your App.

## Brand guide & templates

`brand/brand-guide.html` documents the logo, the 7-color palette, the Oswald/Inter type system, and the interface patterns (status tags, cards) the site is built from — open it directly in a browser. The three templates in `brand/templates/` build on the same system; note that Word will substitute a system font on any machine without Oswald and Inter installed, since the templates reference them by name rather than embedding them.
