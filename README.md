# TL Mechs Guide

A static site documentation project for Throne and Liberty mechanics guides, built with Hugo and the Book theme.

Most of this codebase is generated with [Claude Code](https://claude.com/claude-code).

Vibe coders welcome.

## Tech Stack

- **Hugo**: Static site generator
- **Theme**: [Hugo Book](https://github.com/alex-shpak/hugo-book)
- **Deployment**: GitHub Pages

## Development

### Prerequisites

- Hugo installed (extended version recommended)
- Git

#### Installing Hugo on Mac

The easiest way to install Hugo on Mac is using Homebrew:

```bash
# Install Hugo (includes extended version)
brew install hugo

# Verify installation
hugo version
```

If you don't have Homebrew installed, you can install it first:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Alternative installation methods:
- Download from [Hugo releases](https://github.com/gohugoio/hugo/releases)
- Use MacPorts: `sudo port install hugo`

### Common Commands

```bash
# Start local development server
hugo server -D

# Build the site
hugo

# Build for production (with baseURL from config)
hugo --minify
```

The development server will be available at `http://localhost:1313/tl-mechs-static/`

## Deployment

The site is automatically deployed to GitHub Pages via GitHub Actions on every push to `main`.

- **Live Site**: https://xtopolis.github.io/tl-mechs-static/
- **CI/CD**: GitHub Actions workflow builds and deploys Hugo site automatically
- **Workflow**: `.github/workflows/deploy.yml`

### Manual Deployment

The workflow can also be triggered manually from the Actions tab in GitHub.

## Project Structure

- `content/` - Markdown content files
- `themes/hugo-book/` - Hugo Book theme (as submodule)
- `hugo.toml` - Hugo configuration
- `public/` - Generated site output (not committed)