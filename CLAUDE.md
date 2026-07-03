# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Personal academic website for **Zhengchu Zhang** (PhD candidate, SMU), built on the
**Hugo Blox "Academic CV"** theme. The theme is pulled in as a **Hugo Go module** (see `go.mod`),
so the theme's templates/partials are NOT in this repo — see "Customizing the theme" below.
Live site: https://zhengchuzhang.netlify.app/

## Commands

```bash
# Local dev — drafts + future-dated content visible (NOTE: hides the build gotcha below)
hugo server -D --buildFuture

# Production-parity build (what to run before pushing to catch errors)
hugo --gc --minify

# Update the theme module
hugo mod get -u
```

There are no tests or linters. "Verifying a change" means running `hugo --gc --minify`
(exit 0 = templates compiled) and inspecting the generated `public/index.html`.

## Deployment

`git push origin main` triggers **two** independent deploys:
- **Netlify** (`netlify.toml`): `hugo --gc --minify -b $URL`, **Hugo 0.123.3** → the live `.netlify.app` site.
- **GitHub Pages** (`.github/workflows/publish.yaml`): `hugo --minify`, **Hugo 0.124.1 extended**.

There is no separate "deploy" step — pushing to `main` is publishing.

### ⚠️ Build gotcha: no `--buildFuture` in production
Neither production build passes `--buildFuture`. **Any page whose `date` is later than the build
date is silently dropped from the live site**, even though `hugo server -D --buildFuture` shows it
locally. Always keep content `date` ≤ today. (This bites when copying an entry and bumping its year.)

## Homepage architecture — the key mental model

The homepage is **assembled from blocks**, not written as prose. `content/_index.md`
(`type: landing`) lists `sections:`; each block renders content sourced from *elsewhere*.
To edit what appears on the homepage you usually edit the source, not `_index.md`:

| Homepage block (`content/_index.md`)     | Where the content actually lives                          |
|------------------------------------------|-----------------------------------------------------------|
| `about.biography`                        | `content/authors/admin/_index.md` (name, role, interests, education, social icons, CV link, bio body) |
| `collection` → folder `publication`      | `content/publication/<slug>/index.md` — **card** view     |
| `collection` → folder `wp`               | `content/wp/<slug>/index.md` — **citation** view (Working Papers) |
| `collection` → folder `wip`              | `content/wip/<slug>/index.md` — **citation** view (Work in Progress) |
| `markdown` (Useful Links)                | edited inline in `content/_index.md`                      |
| `contact`                                | edited inline in `content/_index.md`                      |

Global, non-block settings:
- Navbar links: `config/_default/menus.yaml`
- Theme/colors/fonts/header/footer/SEO/analytics/map: `config/_default/params.yaml`
- Avatar: `content/authors/admin/avatar.png` · CV PDF: `static/uploads/`

### Collection block behavior (non-obvious)
- **`featured_only`**: the Publication and Working Paper blocks only show entries with
  `featured: true` in front matter. The Work-in-Progress block has no such filter and shows all.
- **Display order** is by `date` descending — even in `citation` view, which only *prints the
  year*. To reorder same-year entries, change their `date` (month/day), not just the year.
- **`card` view summary** resolves to: `summary` → else `abstract` → else Hugo auto-summary.
  To show an entry on a card with **no abstract text**, set `summary: ' '` (a single space is
  truthy, so it wins over `abstract` yet renders blank). Keep the real text in `abstract:` so the
  detail page still has it.

## Project-specific theme customizations

These local files intentionally override theme defaults — understand them before changing display:

- **`config/_default/params.yaml` → `publications.date_format: '2006'`**: publication dates render
  as **year only** (theme default is `"January, 2006"`).
- **Per-page `show_date: false`**: hides the date on a publication entirely (used for "in press").
- **`layouts/partials/views/citation.html`** (override of the theme's citation view, used by the
  `wp` and `wip` blocks): paper **titles are plain text, not links** to a detail page; and the
  **year is omitted for the `wip` section** (logic keys off `$item.Section == "wip"`). Editing this
  affects Working Papers + Work in Progress, but NOT Publications (those use the `card` view).
- **`assets/scss/custom.scss`**: the theme's `main.scss` does `@import "custom"` against an empty
  placeholder; this project file overrides it. Currently hides the orphan `·` divider that the
  metadata partial leaves before the journal name when the date is hidden (`show_date: false`).

## Customizing the theme (where the originals live)

Theme partials are in the **Hugo module cache**, not this repo and not the standard Go mod cache:
`%LOCALAPPDATA%\hugo_cache\modules\filecache\modules\pkg\mod\github.com\!hugo!blox\hugo-blox-builder\modules\blox-bootstrap\v5@<version>\layouts\...`
(`hugo env` prints the cache dir; the `!hugo!blox` escaping is Go's lowercasing of `HugoBlox`).
To customize a partial, **copy it into this repo's `layouts/` at the same relative path** —
project `layouts/` override module `layouts/`. Same for `assets/`.

## Content authoring conventions

- **Author lists**: the author partial joins list items with `", "` only (no automatic "and").
  Existing entries bundle the conjunction into the last item to control output, e.g.
  `authors: ["Hongjun Xie", "Xiao Chen, and Zhengchu Zhang"]` → "Hongjun Xie, Xiao Chen, and
  Zhengchu Zhang". The site author's name is only auto-highlighted if listed as its own exact item.
- **New publications** can be bulk-imported: pushing a `publications.bib` to the repo root triggers
  `.github/workflows/import-publications.yml`, which runs the Academic CLI to generate
  `content/publication/*.md` and opens a PR. (Hand-authored `index.md` files work too — see existing
  entries for the front-matter shape.)
- `.github/workflows/updater-wip.yml` is gated to `repository_owner == 'HugoBlox'`, so it never runs
  on this fork — ignore it.
