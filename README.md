# TL Mechs Guide

A static site documenting Throne and Liberty dungeon mechanics, built with Astro and Starlight.

Most of this codebase is generated with [Claude Code](https://claude.com/claude-code).

Vibe coders welcome.

## Tech Stack

- **Astro 5** — static site generator
- **Starlight 0.36** — documentation theme
- **Deployment**: GitHub Pages

## Development

### Prerequisites

- Node 20+
- Git

### Common Commands

```bash
# Install dependencies
npm install

# Start local development server (http://localhost:4321/)
npm run dev

# Build the site
npm run build

# Preview built output
npm run preview
```

## Deployment

The site is automatically deployed to GitHub Pages via GitHub Actions on every push to `main`.

- **Live Site**: https://tl-mechs.com/
- **Workflow**: `.github/workflows/deploy.yml`

The workflow can also be triggered manually from the Actions tab in GitHub.

## Project Structure

- `src/content/docs/` — Markdown/MDX content files (mirrors URL structure)
- `src/components/` — Custom Astro components (YouTubeEmbed, MirrorsSim)
- `src/styles/custom.css` — Global custom CSS
- `public/` — Static assets (images, CNAME, 404.html)
- `astro.config.mjs` — Astro + Starlight configuration, sidebar definition
- `dist/` — Build output (not committed)
