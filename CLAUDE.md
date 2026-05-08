# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Hugo static site using [Blowfish theme](https://blowfish.page/) as Git submodule (`themes/blowfish/`). Requires Hugo Extended >= 0.141.0.

## Commands
```bash
hugo server -disableFastRender --noHTTPCache  # dev
hugo --gc --minify                             # build
hugo new projects/my-new-project.md           # new project page
git submodule update --remote --merge themes/blowfish  # update theme
```

## Config (`config/_default/`)
- `hugo.toml` — baseURL, theme, taxonomies, outputs
- `params.toml` — color scheme (`noir`/dark), homepage layout, article options
- `languages.en.toml` — site title, `[params.author]`, social links
- `menus.en.toml` — header nav with dropdown (use `parent =` for sub-items); CV links via `url =` not `pageRef`
- `markup.toml` — Goldmark/syntax highlighting

## Customization (never edit `themes/blowfish/` directly)
- `layouts/partials/` — partial overrides (e.g., `home/background.html`, `favicons.html`)
- `layouts/shortcodes/timelineItem.html` — custom shortcode wrapping Blowfish's `{{< timeline >}}`; params: `icon`, `header`, `subheader`, `badge`, `md` (bool, enables markdown in `.Inner`)
- `assets/css/custom.css` — CSS overrides (TOC with vertical guide line)
- `assets/` — `background.svg`, `logo.png`, `IMG_2023.JPEG`; images here get Hugo image processing
- `static/` — files served at root as-is (e.g., `cv.pdf` → `/cv.pdf`), no processing

## Content
- Pages: `content/*.md` (flat, single pages); Projects: `content/projects/*.md` (list section)
- Shortcodes: `tabs`/`tab`, `keywordList`/`keyword`, `alert`, `lead`, `button`, `timeline`, `timelineItem` (custom)
- Homepage: `background` layout — set in `params.toml` `[homepage]`, background image via `defaultBackgroundImage`
- Project pages use `weight` for ordering in the Projects dropdown menu
