# Hugo Patterns (takaki.me)

Enforcement level: **Moderate** — follow these patterns; exceptions allowed with justification.

## Content

### Creating a Post

Location: `content/posts/YYYY-MM-DD-slug.md`

```yaml
---
layout: post
title:  "Post Title"
date:   2025-10-06 17:00:00 -0300
Categories: ["Articles"]
---
```

- Use `.md` for new posts (`.markdown` files are legacy Jekyll migrations — don't create new ones).
- `Categories` is capitalized in this project's front matter; keep it consistent.
- `date` includes the UTC offset (site timezone `America/Sao_Paulo`, `-0300`).
- Posts may be written in English or Portuguese; both live under `content/posts/`.

### Homepage

The bio is `content/_index.md`, rendered by `themes/takakime/layouts/index.html` via `{{.Content}}`. Edit the markdown file for text changes, the layout only for structural changes.

## Templates (Go HTML)

- Layouts live in `themes/takakime/layouts/` — never in a root-level `layouts/` override (none exists today; keep it that way unless deliberately overriding the theme).
- `index.html` — homepage (bio + latest posts). `_default/single.html` — post page. `_default/list.html` — section/term lists. Partials: `head.html`, `header.html`, `footer.html`.
- Reading time convention, used in both list views:
  ```go-html-template
  {{ math.Round (div (countwords .Content) 220.0) }}min
  ```
- Show draft labels in lists: `{{ if .Draft }}<span class="draft-label"> | DRAFT</span>{{ end }}`
- Date formats in use: `Mon, Jan 2, 2006` (homepage), `Mon Jan 2, 2006` (single), `2006-01-02` (list).

## Styling

- Single stylesheet: `themes/takakime/static/css/style.css`. Add to it rather than creating new files.
- Icons: Font Awesome classes (`fab fa-linkedin`, etc.) in partials.
- Code highlighting is configured in `config.toml` (`monokai`, `lineNos = true`, `noClasses = true`) — don't add highlight CSS manually.

## Build & Deploy

- Local dev: `hugo server` (add `-D` for drafts).
- Production build: `hugo --minify` — this is exactly what CI runs.
- Deploy is automatic: push to `master` → GitHub Actions builds and pushes `public/` to `fabiotakaki/fabiotakaki.github.io`. Never commit `public/`.
- `CNAME` at repo root must be preserved (copied into `public/` by CI).

## Recommendations (not enforced)

- Resolve unused multilingual config: adopt `.pt-br.md` content files or remove `[languages.pt-BR]` from `config.toml`.
- Consider migrating CSS to `assets/` + Hugo Pipes for minification/fingerprinting.
- Use shortcodes instead of raw `<style>`/`<script>` blocks in new posts.
