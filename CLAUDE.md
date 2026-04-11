# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
bundle install                              # Install Ruby dependencies
bundle exec jekyll serve                   # Local dev server (hot reload) at localhost:4000
bundle exec jekyll build --baseurl "/markdown"  # Production build (mirrors CI)
```

> Local server runs at `http://localhost:4000/markdown/` due to the `/markdown` baseurl.

## Architecture

This is a **Jekyll 4.4.1** static site hosted on **GitHub Pages** under `https://ppablobr.github.io/markdown`.

**Deployment**: GitHub Actions (`.github/workflows/jekyll.yml`) automatically builds and deploys on every push to `main`. No manual deployment step needed.

**Theme**: Minima 2.5 with `skin: auto` (light/dark). Layouts (`home`, `page`, `post`) come from the gem — there are no local overrides in `_layouts/` or `_includes/` yet.

**Plugins**: `jekyll-feed` (RSS at `/feed.xml`) and `jekyll-seo-tag` (SEO meta tags via `_config.yml` fields).

## Content

**Posts** live in `_posts/` and must follow the naming format:

```
YYYY-MM-DD-slug.md
```

Required front matter:

```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS +0000
categories: blog pessoal   # creates URL /blog/pessoal/YYYY/MM/DD/slug/
tags: [tag1, tag2]
---
```

**Static pages** use `layout: page` and live at the repo root (`about.markdown`, etc.).

## Configuration

Key `_config.yml` settings:
- `baseurl: "/markdown"` — required for all internal links to work correctly on GitHub Pages
- `url: "https://ppablobr.github.io"` — used by `jekyll-seo-tag`
- Social links for GitHub are active; Twitter/LinkedIn are commented out
