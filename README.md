# TL Mechs Guide

A static site documentation project for Throne and Liberty mechanics guides, built with Hugo and the Book theme.

## Tech Stack

- **Hugo**: Static site generator
- **Theme**: [Hugo Book](https://github.com/alex-shpak/hugo-book)
- **Deployment**: GitHub Pages

## Development

### Prerequisites

- Hugo installed (extended version recommended)
- Git

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

The site is automatically deployed to GitHub Pages at:
https://xtopolis.github.io/tl-mechs-static/

## Project Structure

- `content/` - Markdown content files
- `themes/hugo-book/` - Hugo Book theme (as submodule)
- `hugo.toml` - Hugo configuration
- `public/` - Generated site output (not committed)