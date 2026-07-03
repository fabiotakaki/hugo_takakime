# Project Conventions: hugo_takakime (takaki.me)

Enforcement level: **Moderate**.

## What This Project Is

Personal blog/portfolio for Fabio S. Takaki at takaki.me. Content-first Hugo site with a custom theme. There is no package.json, no JS framework, no tests, and no linters — keep it that way unless explicitly asked.

## File Placement Rules

| Change type | Where |
|---|---|
| Bio / homepage text | `content/_index.md` |
| New blog post | `content/posts/YYYY-MM-DD-slug.md` |
| Page structure / HTML | `themes/takakime/layouts/` |
| Site header (name, social links, profile pic) | `themes/takakime/layouts/partials/header.html` |
| Footer copyright | `themes/takakime/layouts/partials/footer.html` |
| CSS | `themes/takakime/static/css/style.css` (single file) |
| Images | `static/img/` |
| Site config, languages, highlighting | `config.toml` |
| CI/deploy | `.github/workflows/deploy.yml` |

## Naming

- Posts: `YYYY-MM-DD-kebab-slug.md`
- Front matter keys as used today: `layout`, `title`, `date`, `Categories` (capitalized)

## Verification Gate

There are no tests. Verify changes with:

```bash
hugo --minify   # must build cleanly
hugo server     # visual check at http://localhost:1313
```

## Deploy Awareness

- Pushing to `master` deploys to production (takaki.me) automatically.
- Never commit the `public/` directory.
- The `CNAME` file at repo root is required for the custom domain.

## Social/Identity Facts (for content edits)

- LinkedIn: linkedin.com/in/fabiotakaki · GitHub: fabiotakaki · Email: fabio.stakaki@gmail.com
- Current role (as of 2026-07): AI Software Engineer at Intapp, working on Intapp Celeste.
