# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# Project Overview

Static site documenting Throne and Liberty dungeon mechanics, deployed to GitHub Pages at https://tl-mechs.com/. Migrated from Hugo + Book theme to **Astro + Starlight** for richer component support and better DX.

# Tech Stack

- **Astro 5** with **Starlight 0.36** — static site generator + documentation theme
- **MDX** — content files use `.mdx` when they import components, plain `.md` otherwise
- **Deployment**: GitHub Actions builds and deploys to GitHub Pages on push to `main`
- **Node 20** — required for builds

# Development Commands

```bash
npm run dev       # Start dev server at http://localhost:4321/
npm run build     # Build to dist/
npm run preview   # Preview built output
```

# Content Structure

All content lives under `src/content/docs/`, mirroring the URL structure:
- `altar-of-calanthia/` — Raid bosses: Dragaryle, Vulkan & Zairos, Calanthia
  - Each boss has `phase-1/`, `phase-2/` subdirectories
- `trials-4star/` — 4★ Trial dungeons: Colossal Coliseum, Fate's Abyss
- `todo/` — Placeholder section for guides not yet written
- `about.md`, `index.md` — Top-level pages

**Frontmatter conventions:**
- `title: "..."` — page title
- `sidebar:\n  order: N` — controls ordering within a section (replaces Hugo's `weight`)
- `sidebar:\n  collapsed: true` — collapses section in sidebar by default
- Section index files are `index.md` (not `_index.md`)

# Custom Components

Components live in `src/components/`. Import with a relative path based on nesting depth.

| Depth from `src/content/docs/` | Import prefix |
|---|---|
| 2 levels deep (e.g. `altar-of-calanthia/dragaryle/`) | `../../../../components/` |
| 3 levels deep (e.g. `altar-of-calanthia/calanthia/phase-1/`) | `../../../../../components/` |

## YouTubeEmbed

Embeds a responsive YouTube video.

```mdx
import YouTubeEmbed from '../../../../components/YouTubeEmbed.astro';

<YouTubeEmbed videoId="VIDEO_ID" />
<YouTubeEmbed videoId="VIDEO_ID" start={30} title="Custom title" />
```

## MirrorsSim

Canvas-based interactive simulator for the Calanthia mirrors mechanic. No props.

```mdx
import MirrorsSim from '../../../../../components/MirrorsSim.astro';

<MirrorsSim />
```

## Starlight built-ins

Use these directly in `.mdx` files without importing:

```mdx
:::tip[Title]
Tip content here.
:::

:::caution[Title]
Caution content here.
:::

:::note
Note content.
:::
```

For section index pages listing child pages, use Starlight's `<CardGrid>` and `<LinkCard>`:

```mdx
import { CardGrid, LinkCard } from '@astrojs/starlight/components';

<CardGrid>
  <LinkCard title="Page Name" href="/section/page-name/" />
</CardGrid>
```

## Images

Images are stored in `public/images/` following the content hierarchy.

Reference images using absolute paths: `/images/altar-of-calanthia/dragaryle/image.png`

```md
<img src="/images/altar-of-calanthia/dragaryle/image.png" loading="lazy" class="mech-diagram" />
```

The `mech-diagram` class (defined in `src/styles/custom.css`) centers the image and limits it to 50% width.

# Configuration

- **`astro.config.mjs`** — site URL, Starlight options, sidebar sections, custom CSS
- **`src/content.config.ts`** — Starlight content collection definition (do not modify)
- **`src/styles/custom.css`** — custom CSS loaded globally
- **`package.json`** — includes npm overrides to pin `zod-to-json-schema` and `@astrojs/sitemap` to Zod v3-compatible versions; do not remove these overrides or the build will break

# Deployment

- **Workflow**: `.github/workflows/deploy.yml`
- Automatically deploys on push to `main`
- Can be manually triggered from GitHub Actions tab
- Build output goes to `dist/` (gitignored); `public/` is committed static assets

# Git Workflow

## Branch Management

- **Always create new branches** for all work (even small typos)
  - For minor fixes on existing branches, use naming like `existing-branch-typo1`
  - Never reuse branches that have already been merged
  - Create branches automatically without asking permission

- **CRITICAL: Before creating a new branch:**
  ```bash
  git checkout main
  git pull
  # ALWAYS verify main is up-to-date with remote before branching
  # This prevents working on stale code when previous PRs have been merged
  ```

- **Branch naming:** Action-based, dash-separated, succinct (2-3 words), alphanumeric
  - Good: `add-elephant-mechs`, `fix-tower-images`, `update-dragaryle-guide`
  - Avoid prefixes like `feature/` or `fix/`

## Switching Between Work Streams

- When leaving a branch temporarily to work on something else:
  - Create a "wip" commit (not a stash) to save progress
  - When returning to a branch with a "wip" commit, ask if it should be reset so work can be recommitted with a proper message
  - Stashing is okay for temporary work within the same branch
  - Use temporary branches as needed for exploratory work

## Commits

- **Timing:** User will specify when to commit, but feel free to ask if substantial work has accumulated
- **Format:** Concise one-liner in past tense (e.g., "Added elephant boss mechanics")
- **Description:** Optional few sentences in commit body for context, but keep it brief

## Pull Requests

- **IMPORTANT: When pushing changes to GitHub, ALWAYS create a Pull Request**
  - Never just push without creating a PR
  - PRs provide visibility and review capability for the user
  - Exception: When explicitly told "just push" without PR mentioned

- **PR Creation Workflow:**
  1. Push the branch: `git push -u origin <branch-name>`
  2. Create PR using `gh pr create` with:
     - **Title:** Concise description (e.g., "Adding mechs for Elephant boss")
     - **Description:** Few bullets covering key additions or files modified
     - Don't include "table stakes" items like "Added tests" unless that was the branch's purpose
  3. **ALWAYS return a clickable link to the PR** so the user can review it

- **When user says "ship it":**
  - This means: commit and finalize the code, push the branch, create a PR, and provide the PR link for review
  - Follow the full PR creation workflow above
  - Always return the PR URL so the user can review it

# Communication Style

## Conciseness

- Keep responses concise by reducing verbosity and avoiding unnecessary words
- Skip preambles and postambles (e.g., avoid "Sure, I'll help with that")
- Exception: When asked to explain something, provide detailed elaboration and examples

## Action vs. Speculation

- **Speculative questions** (e.g., "how would I make...") - Explain and provide examples, but don't make changes
- **Clear action requests** (e.g., "make the button red") - Proceed immediately
- **Ambiguous requests** - Assume no permission given; ask first and propose what you think is intended
- Only ask for substantive changes, not trivial fixes like typos