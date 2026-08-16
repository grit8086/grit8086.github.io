```text
                     ███   █████     ████████      █████     ████████    ████████
                    ░░░   ░░███     ███░░░░███   ███░░░███  ███░░░░███  ███░░░░███
  ███████ ████████  ████  ███████  ░███   ░███  ███   ░░███░███   ░███ ░███   ░░░
 ███░░███░░███░░███░░███ ░░░███░   ░░████████  ░███    ░███░░████████  ░█████████
░███ ░███ ░███ ░░░  ░███   ░███     ███░░░░███ ░███    ░███ ███░░░░███ ░███░░░░███
░███ ░███ ░███      ░███   ░███ ███░███   ░███ ░░███   ███ ░███   ░███ ░███   ░███
░░███████ █████     █████  ░░█████ ░░████████   ░░░█████░  ░░████████  ░░████████
 ░░░░░███░░░░░     ░░░░░    ░░░░░   ░░░░░░░░      ░░░░░░    ░░░░░░░░    ░░░░░░░░
 ███ ░███
░░██████
 ░░░░░░
```

A personal grimoire of offensive security research, technical writing, and whatever else I find interesting or silly enough to document.

This repository contains the source for my personal static site, where I keep notes, write-ups, research, experiments, and other assorted bits of silliness.

**Live site:** [grit8086.github.io](https://grit8086.github.io)

## Stack
* **[Hugo](https://gohugo.io/)** - static site generator (extended)
* **Custom theme**               - completely hand-rolled, no third-party Hugo theme
* **Chroma**                     -  syntax highlighting, recolored to match the `gigglycat` Vim colorscheme
* **GitHub Actions**             - builds and deploys the site to GitHub Pages on every push to `main`

## Development
### Prerequisites
* Hugo Extended
* Git

### Run locally
```bash
hugo server -D
```

The development server will be available at:

```text
http://localhost:1313
```

Drafts are included and changes are automatically reloaded.

## Structure

```text
.
├── content/
│   └── posts/          # Writings, notes, and research
├── layouts/             # Custom Hugo templates
│   ├── ...
│   └── ...
├── assets/
│   └── css/             # Site styles and syntax highlighting
├── static/              # Fonts, favicons, robots.txt, etc.
└── ...
```

## Deployment
The site is automatically deployed to GitHub Pages through GitHub Actions whenever changes are pushed to **main**.

```text
push -> GitHub Actions -> Hugo build -> GitHub Pages
```

## License
The site's source code and configuration are available for reference. Individual writings and research may have their own licensing or usage terms where specified.
