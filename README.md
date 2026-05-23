# whilcayangyang.me

<p align="center">
  Personal website and portfolio powered by <strong>Hugo</strong> and the <strong>Blowfish</strong> theme.
</p>

<p align="center">
  <a href="https://gohugo.io/"><img alt="Hugo" src="https://img.shields.io/badge/Hugo-0.141.0%2B-ff4088?logo=hugo&logoColor=white"></a>
  <a href="https://blowfish.page/"><img alt="Theme" src="https://img.shields.io/badge/Theme-Blowfish-2563eb"></a>
  <a href="https://git-scm.com/book/en/v2/Git-Tools-Submodules"></a>
</p>

## Prerequisites

- Hugo Extended `>= 0.141.0`

```bash
hugo version
```

## Quick Start

Clone with submodules:

```bash
git clone --recurse-submodules <your-repo-url>
cd www.whilcayangyang.me
```

If already cloned without submodules:

```bash
git submodule update --init --recursive
```

Run local dev server:

```bash
hugo server --disableFastRender --noHTTPCache
```

Open `http://localhost:1313`.

Build for production:

```bash
hugo --gc --minify
```

Output goes to `public/`.

## Repo Map

| Path | Purpose |
|------|---------|
| `config/_default/hugo.toml` | Core Hugo config — `baseURL`, theme, outputs, taxonomies |
| `config/_default/params.toml` | Blowfish layout and visual controls (`colorScheme`, homepage layout, article options) |
| `config/_default/languages.en.toml` | Site title, `[params.author]` profile, social links |
| `config/_default/menus.en.toml` | Header nav and Projects dropdown hierarchy |
| `config/_default/markup.toml` | Goldmark and syntax highlighting settings |
| `content/` | Site pages — flat pages at root, project articles under `projects/` |
| `assets/css/custom.css` | TOC redesign and style overrides |
| `assets/background.svg` | Homepage background image |
| `assets/logo.png` | Site logo |
| `assets/profile.jpeg` | Author profile photo |
| `static/` | Files served at root as-is — favicon set, `cv.pdf` |
| `themes/blowfish/` | Blowfish theme source (git submodule — never edit directly) |

## Content Structure

```
content/
├── _index.md           # Homepage (background layout)
├── aboutme.md          # About — intro, work summary, career timeline
├── profile.md          # Technical profile — skills, capability depth
├── homelab.md          # Kubernetes homelab — GitOps, Talos, observability
├── privacy.md          # Privacy advocacy
└── projects/
    ├── aws-cloud.md        # AWS Modernization
    ├── platform-migration.md  # 88TB ShareFile → Dropbox migration
    ├── app-migration.md    # AWS EC2 Windows Server upgrade
    ├── networking.md       # Five-country network overhaul
    └── cis.md              # CIS Controls v8 enforcement
```

Page ordering in nav and prev/next navigation is controlled by `weight` in each file's front matter. Menu weights in `menus.en.toml` and page weights must be kept in sync.

## Adding a Project Page

```bash
hugo new projects/my-new-project.md
```

Set `weight` in the front matter to match its position in `menus.en.toml`, then add the menu entry:

```toml
[[main]]
name = "My Project"
parent = "Projects"
pageRef = "my-new-project"
weight = 26
```

## Theme Management

Update Blowfish to latest:

```bash
git submodule update --remote --merge themes/blowfish
git add themes/blowfish
git commit -m "chore: update blowfish theme"
```

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `themes/blowfish` is empty | `git submodule update --init --recursive` |
| Build fails with missing theme features | Confirm Hugo Extended >= 0.141.0 (`hugo version`) |
| Config or style changes not visible | Restart `hugo server` and hard-refresh the browser |
| Prev/next article order wrong | Sync page `weight` in front matter with menu `weight` in `menus.en.toml` |
