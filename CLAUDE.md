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

**Cover images**: The Minima theme does NOT render the `image:` front matter field as a visual element — it only populates SEO meta tags (`og:image`, `twitter:card`). To display the image visually in the post:
1. Generate via `imagen-prompt-architect` skill (Gemini Imagen 3, 16:9 infographic)
2. Upload to Cloudinary → get `secure_url`
3. Add `image: [cloudinary_url]` to front matter (for SEO)
4. Embed `<figure>` with the Cloudinary URL directly in the post body (after intro, before first `##`)

Always use the absolute Cloudinary URL in `<figure>` — never `relative_url` for externally hosted images.

Search Cloudinary before generating (`mcp__cloudinary__cloudinary_search`) to avoid duplicates.

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
| **Workflows** | Instructions (SOPs) | `workflows/publish-post.md` (exists), `workflows/update-theme.md` (pending), `workflows/new-post-from-draft.md` (pending) |
| **Agents** | Decision-making | Claude root (coordination) + `ai-agent-editor` subagent (content) + `frontend-design` subagent (visual) |
| **Tools** | Execution | MCP tools for I/O; `~/.claude/skills/` for complex multi-step tasks; `tools/*.py` for local computation |

**Before starting any multi-step task**: check `workflows/` for an existing SOP. If one exists, follow it. If not, complete the task, then evaluate whether it's repeatable enough to warrant creating a workflow.

**Workflow triggers:**
- `publish-post.md` — writing and publishing a new post end-to-end (exists)
- `update-theme.md` — CSS, layout, or design changes (pending)
- `new-post-from-draft.md` — Pedro provides raw text; format and publish it (pending)

---

## Publishing Pipeline — Sequência obrigatória

Every new post follows this sequence. Do not skip steps.

```
1. ai-agent-editor subagent
   └── WebSearch (4-6 fontes)
   └── Lê _posts/ (link building interno)
   └── Escreve post completo
   └── Salva _posts/YYYY-MM-DD-slug.md
   └── Retorna HANDOFF (file_path, article_type, h2_sections, key_concepts, cover_brief)

2. Root agent → Skill imagen-prompt-architect (modo infográfico)
   └── Gera infográfico 16:9 em assets/images/
   └── Root agent faz upload Cloudinary → captura secure_url
   └── Root agent insere <figure> no body do post (após intro, antes do 1º ##)
   └── Root agent adiciona image: [cloudinary_url] ao front matter

3. Root agent apresenta post completo ao Pedro para aprovação

4. Root agent publica via GitHub MCP (preferência) ou git push
```

**Subagent limitation**: `ai-agent-editor` does not have access to Cloudinary, Gemini image, or GitHub MCP tools. Image generation and publishing are always the root agent's responsibility.

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

- **`ai-agent-editor`** — writing posts, editing drafts, brainstorming topics, checking technical accuracy of AI content. Returns a structured HANDOFF block; root agent handles image and publishing after.
- **`frontend-design`** — CSS changes, layout modifications, visual component evaluation, custom HTML

**Skills (run in root agent context, have full MCP access):**
- **`imagen-prompt-architect`** — infographic and cover image generation via Gemini Imagen 3 + Cloudinary upload
- **`ai-blog-editor`** — alternative full pipeline skill (research + write + image + publish) for cases where the subagent flow is not needed

These agents and skills carry domain context that the root agent does not. Route to them early, not as a fallback. After delegation, root agent always handles: image upload to Cloudinary, `<figure>` embedding, front matter `image:` field, and push.

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
