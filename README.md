# khanh1ng.github.io

Personal research site. Built with plain Jekyll — GitHub Pages builds it automatically, no local tooling required.

## Deploy (one time, ~5 minutes)

1. On GitHub, create a **public** repo named exactly `khanh1ng.github.io` (must match your username).
2. Locally:
   ```bash
   cd khanh1ng.github.io
   git init
   git add .
   git commit -m "initial site"
   git branch -M main
   git remote add origin git@github.com:khanh1ng/khanh1ng.github.io.git
   git push -u origin main
   ```
3. On GitHub: repo → **Settings → Pages** → confirm Source is "Deploy from a branch", branch `main`, folder `/ (root)`.
4. Wait ~2 minutes. Site is live at **https://khanh1ng.github.io**.

## Adding a write-up

Drop a markdown file into `_posts/` named `YYYY-MM-DD-slug.md` with front matter:

```markdown
---
layout: post
title: "Delta-neutral funding-rate carry across six venues"
tag: "strategy"          # strategy / microstructure / macro / meta
summary: "One-line hook shown under the title."
reading_time: "8 min"
---

Body in markdown. Code blocks, tables, and images all work.
```

Push. It appears on the homepage and /writing/ automatically.

Images: put them in `assets/img/` and reference `/assets/img/name.png`.

## Editing

- Homepage content: `index.html` (project list is hand-edited — update links when repos go public)
- Colors/typography: `assets/css/main.css` (tokens at the top)
- The header "tape": `_layouts/default.html`

## Custom domain (optional, later)

Buy a domain, add a `CNAME` file containing the domain, set DNS A records per GitHub Pages docs.
