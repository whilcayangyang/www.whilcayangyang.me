# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Local development server (recommended: disables fast render and HTTP cache)
hugo server -disableFastRender --noHTTPCache

# Production build (outputs to public/)
hugo --gc --minify

# Create a new project page
hugo new projects/my-new-project.md

# Create a new standalone page
hugo new my-page.md

# Update Blowfish theme submodule to latest
git submodule update --remote --merge themes/blowfish
```

## Architecture

This is a **Hugo static site** using the [Blowfish theme](https://blowfish.page/) as a Git submodule (`themes/blowfish/`). Hugo Extended `>= 0.141.0` is required.

### Config layout

All config lives in `config/_default/`:
- `hugo.toml` — core Hugo settings (baseURL, theme, taxonomies, outputs)
- `params.toml` — Blowfish visual/layout controls (color scheme, homepage layout, article options)
- `languages.en.toml` — site title, author profile (`[params.author]`), and social links
- `menus.en.toml` — header navigation
- `markup.toml` — Goldmark/syntax highlighting settings

### Theme customization model

Blowfish is **never directly edited**. Customizations use Hugo's override system:

- `layouts/partials/` — overrides for Blowfish partials (e.g., `home/background.html` customizes the background homepage layout)
- `layouts/shortcodes/` — custom shortcodes (e.g., `timelineItem.html` for timeline UI components)
- `assets/css/custom.css` — CSS overrides for Blowfish styles (currently: TOC redesign with vertical guide line)
- `assets/` — site assets referenced by config (`background.svg`, `logo.png`, `IMG_2023.JPEG`)

### Content

`content/` pages use Blowfish shortcodes heavily (`{{< tabs >}}`, `{{< tab >}}`, `{{< keywordList >}}`, `{{< keyword >}}`, `{{< alert >}}`, `{{< lead >}}`, `{{< button >}}`). The `timelineItem` shortcode is custom (defined in `layouts/shortcodes/`).

Homepage layout is `background` — the full-screen background image + profile overlay. The profile partial renders from `layouts/partials/home/profile.html` (Blowfish built-in, not overridden).

### Key param: homepage image

`defaultBackgroundImage = "background.svg"` in `params.toml` sets the site-wide background. The `background.html` partial resolves it from `assets/`.
