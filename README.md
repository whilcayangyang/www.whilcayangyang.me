# whilcayangyang.me

<p align="center">
  Personal website powered by <strong>Hugo</strong> and the <strong>Blowfish</strong> theme.
</p>

<p align="center">
  <a href="https://gohugo.io/"><img alt="Hugo" src="https://img.shields.io/badge/Hugo-0.141.0%2B-ff4088?logo=hugo&logoColor=white"></a>
  <a href="https://blowfish.page/"><img alt="Theme" src="https://img.shields.io/badge/Theme-Blowfish-2563eb"></a>
  <a href="https://git-scm.com/book/en/v2/Git-Tools-Submodules"><img alt="Theme Source" src="https://img.shields.io/badge/Theme%20Source-Git%20Submodule-16a34a"></a>
</p>

## Overview

This repository is a Hugo site configured for:

- `theme = "blowfish"` in `config/_default/hugo.toml`
- dark appearance with `colorScheme = "noir"` in `config/_default/params.toml`
- background-style homepage layout
- custom site assets (logo, profile image, background, favicon set)

## Prerequisites

- Git
- Hugo Extended `>= 0.141.0` (Blowfish minimum)

Check your Hugo version:

```bash
hugo version
```

## Quick Start (This Repo)

1. Clone with submodules:

```bash
git clone --recurse-submodules <your-repo-url>
cd www.whilcayangyang.me
```

If you already cloned without submodules:

```bash
git submodule update --init --recursive
```

2. Run local dev server:

```bash
hugo server -D
```

3. Open:

```text
http://localhost:1313
```

4. Build production output:

```bash
hugo --gc --minify
```

Generated site files go to `public/`.

## Initialize Blowfish Theme

This repo already wires Blowfish as a submodule via `.gitmodules`.

- Initialize theme files:

```bash
git submodule update --init --recursive
```

- Update Blowfish to the latest commit on its `main` branch:

```bash
git submodule update --remote --merge themes/blowfish
```

- Commit the updated submodule pointer:

```bash
git add themes/blowfish
git commit -m "chore(theme): update blowfish"
```

## Initialize Blowfish in a New Hugo Site

```bash
hugo new site mysite
cd mysite
git init
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
mkdir -p config/_default
cp -R themes/blowfish/config/_default/* config/_default/
```

Then set `theme = "blowfish"` in `config/_default/hugo.toml` and run:

```bash
hugo server -D
```

## Repo Map

| Path | Purpose |
| --- | --- |
| `config/_default/hugo.toml` | Core Hugo config (`baseURL`, theme, outputs, taxonomies) |
| `config/_default/params.toml` | Blowfish visual behavior and layout controls |
| `config/_default/languages.en.toml` | Site title, author profile, social links |
| `config/_default/menus.en.toml` | Header navigation and menu hierarchy |
| `config/_default/markup.toml` | Markdown/Goldmark and syntax highlighting settings |
| `content/` | Site pages and project content |
| `assets/` | Branding and custom assets (`logo.png`, `background.svg`, images) |
| `assets/css/custom.css` | Theme overrides and custom styles |
| `themes/blowfish/` | Blowfish theme source (git submodule) |

## Visual Customization

For fast aesthetic updates, edit these first:

- `config/_default/params.toml`:
  - `colorScheme`
  - `defaultAppearance`
  - `[homepage].layout`
  - `[header].layout`
- `config/_default/languages.en.toml`:
  - `[params].logo`
  - `[params.author]` profile and social links
- `assets/background.svg`, `assets/logo.png`, `assets/profile-mono.jpg`
- `assets/css/custom.css` for style overrides

## Content Workflow

Create a new project page:

```bash
hugo new projects/my-new-project.md
```

Create a new standalone page:

```bash
hugo new my-page.md
```

## Troubleshooting

- `themes/blowfish` is empty:
  - Run `git submodule update --init --recursive`
- Build fails with theme features missing:
  - Confirm you are using Hugo Extended and at least `0.141.0`
- Styles or config changes not visible:
  - Restart `hugo server` and hard-refresh browser cache
