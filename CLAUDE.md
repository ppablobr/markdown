# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Operating Framework

This project operates under the **WAT framework** (Workflows, Agents, Tools). Read `WAT.md` as the operating manual. This file documents only the blog-specific delta: how the three layers map to the actual tools, which workflows exist, and how to route between subagents.

---

## Commands

```bash
bundle install                              # Install Ruby dependencies
bundle exec jekyll serve                   # Local dev server (hot reload) at localhost:4000
bundle exec jekyll build --baseurl "/markdown"  # Production build (mirrors CI)
```

> Local server runs at `http://localhost:4000/markdown/` due to the `/markdown` baseurl.

---

## Architecture

This is a **Jekyll 4.4.1** static site hosted on **GitHub Pages** under `https://ppablobr.github.io/markdown`.

**Deployment**: GitHub Actions (`.github/workflows/jekyll.yml`) automatically builds and deploys on every push to `main`. No manual deployment step needed. When operating as an agent, prefer publishing via GitHub MCP (`mcp__github__create_or_update_file`, `mcp__github__push_files`) rather than `git push`.

**Theme**: Minima 2.5 with `skin: auto` (light/dark). Layouts (`home`, `page`, `post`) come from the gem — there are no local overrides in `_layouts/` or `_includes/` yet.

**Plugins**: `jekyll-feed` (RSS at `/feed.xml`) and `jekyll-seo-tag` (SEO meta tags via `_config.yml` fields).

---

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

**Cover images**: generated via Gemini MCP, uploaded to Cloudinary, referenced in front matter as `image: https://res.cloudinary.com/dgqhglwo5/...`. Search Cloudinary before generating a new image (`mcp__cloudinary__cloudinary_search`) to avoid duplicates.

**Static pages** use `layout: page` and live at the repo root (`about.markdown`, etc.).

---

## Configuration

Key `_config.yml` settings:
- `baseurl: "/markdown"` — required for all internal links to work correctly on GitHub Pages
- `url: "https://ppablobr.github.io"` — used by `jekyll-seo-tag`
- Social links for GitHub are active; Twitter/LinkedIn are commented out

---

## WAT Layers — This Project

| Layer | Role | Assets |
|-------|------|--------|
| **Workflows** | Instructions (SOPs) | `workflows/publish-post.md`, `workflows/update-theme.md`, `workflows/new-post-from-draft.md` |
| **Agents** | Decision-making | Claude (coordination) + `ai-agent-editor` (content) + `frontend-design` (visual) |
| **Tools** | Execution | MCP tools for I/O; `tools/*.py` for local computation (none yet) |

**Before starting any multi-step task**: check `workflows/` for an existing SOP. If one exists, follow it. If not, complete the task, then evaluate whether it's repeatable enough to warrant creating a workflow.

**Workflow triggers:**
- `publish-post.md` — writing and publishing a new post end-to-end
- `update-theme.md` — CSS, layout, or design changes
- `new-post-from-draft.md` — Pedro provides raw text; format and publish it

---

## Tool Registry

Check here before building anything new. Only create tools that don't already exist.

**GitHub MCP**
- `mcp__github__create_or_update_file` — create or update a post file in the repo
- `mcp__github__push_files` — push multiple files in one commit
- `mcp__github__create_pull_request` — open a PR for review before merging

**Cloudinary MCP**
- `mcp__cloudinary__cloudinary_upload_file` — upload a generated image
- `mcp__cloudinary__cloudinary_search` — find existing images before generating new ones

**Gemini MCP**
- `mcp__gemini-image__gemini_generate_image` — generate post cover images

**Python tools (`tools/`)**
- None yet. Create here for local computation (markdown validation, front matter linting, batch operations) that doesn't require network credentials or API calls.

---

## Subagent Delegation Rules

Do not handle content creation or design decisions directly. Delegate:

- **`ai-agent-editor`** — writing posts, editing drafts, brainstorming topics, checking technical accuracy of AI content
- **`frontend-design`** — CSS changes, layout modifications, visual component evaluation, custom HTML

These subagents carry domain context that the root agent does not. Route to them early, not as a fallback.

---

## Self-Improvement

1. When a publishing step fails or requires an unexpected workaround, update the relevant workflow in `workflows/` before ending the session.
2. When a new Python script is added to `tools/`, register it in the Tool Registry section above.
3. Do not overwrite or delete workflow files without asking first.

---

## File Structure

```
_posts/           # Published blog posts (YYYY-MM-DD-slug.md)
workflows/        # Blog SOPs — read before starting multi-step tasks
tools/            # Python scripts for local computation (empty for now)
.tmp/             # Temporary files — regenerated as needed, gitignored
assets/           # SCSS and static assets
.env              # API keys (never commit, never echo contents)
.claude/agents/   # Subagent definitions (ai-agent-editor, frontend-design)
mcp.json          # MCP server declarations (GitHub, Cloudinary)
gemini-image-mcp-server/  # Gemini image MCP (local Node server)
WAT.md            # Operating framework — read this
```
