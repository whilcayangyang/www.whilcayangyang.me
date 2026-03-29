# CLAUDE.md

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
- `params.toml` — color scheme, homepage layout, article options
- `languages.en.toml` — site title, `[params.author]`, social links
- `menus.en.toml` — header nav
- `markup.toml` — Goldmark/syntax highlighting

## Customization (never edit `themes/blowfish/` directly)
- `layouts/partials/` — partial overrides (e.g., `home/background.html`)
- `layouts/shortcodes/timelineItem.html` — custom timeline shortcode
- `assets/css/custom.css` — CSS overrides (TOC with vertical guide line)
- `assets/` — `background.svg`, `logo.png`, `IMG_2023.JPEG`

## Content
Shortcodes: `tabs`, `tab`, `keywordList`, `keyword`, `alert`, `lead`, `button`, `timelineItem` (custom).
Homepage: `background` layout — `defaultBackgroundImage = "background.svg"` in `params.toml`.
