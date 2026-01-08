# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# Project Overview

This is a Hugo-based static site for documenting Throne and Liberty game mechanics. It uses the Hugo Book theme and is deployed to GitHub Pages at https://tl-mechs.com/.

# Tech Stack

- **Hugo**: Static site generator (requires extended version)
- **Theme**: Hugo Book (included as git submodule in `themes/hugo-book/`)
- **Deployment**: GitHub Actions automatically builds and deploys to GitHub Pages on push to main

# Development Commands

```bash
# Start local development server with drafts
hugo server -D

# Build the site (output to public/)
hugo

# Build for production with minification
hugo --minify
```

Development server runs at http://localhost:1313/

# Content Structure

Content is organized in a hierarchical structure under `content/docs/`:
- `altar-of-calanthia/` - Raid boss mechanics (Dragaryle, Vulkan/Zairos, Calanthia)
- `trials-2star/`, `trials-3star/`, `trials-4star/` - Trial dungeon mechanics

Each mechanic page is a markdown file with frontmatter containing `title` and `weight` for ordering in the navigation.

# Custom Components

## Shortcodes

- **youtube**: Embeds YouTube videos. Usage: `{{< youtube VIDEO_ID >}}`
  - Supports parameters: `id`, `start`, `end`, `autoplay`, `controls`, `loading`, etc.
  - Example: `{{< youtube _xNUE5YmfUA >}}`

- **section-pages**: Automatically lists all child pages in a section with links
  - Usage: `{{< section-pages >}}`
  - Lists pages sorted by their `weight` frontmatter value
  - Useful for parent/overview pages that have nested mechanic pages

## Images

Images are stored in `static/images/` following the content hierarchy (e.g., `static/images/altar-of-calanthia/dragaryle/`).

Reference images using absolute paths: `/images/altar-of-calanthia/dragaryle/image.png`

# Hugo Configuration

Configuration is in `hugo.toml`:
- `baseURL`: https://tl-mechs.com/
- `theme`: hugo-book
- Theme settings: Table of contents enabled, auto theme (follows system preference)

# Deployment

- **Workflow**: `.github/workflows/deploy.yml`
- Automatically deploys on push to `main`
- Can be manually triggered from GitHub Actions tab

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