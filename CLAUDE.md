# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo-based academic personal website using the Hugo Blox (formerly Academic) theme. The site is built with Hugo v0.123.3+ and uses Go modules for theme management.

## Architecture

- **Static Site Generator**: Hugo with Academic CV theme
- **Theme**: Hugo Blox Builder with Academic CV template
- **Content Structure**:
  - `content/` - Main content (posts, publications, authors, projects, events)
  - `config/_default/` - Site configuration split into multiple YAML files
  - `static/` - Static assets
  - `assets/` - Build-time assets
  - `public/` - Generated site output (not committed)

## Development Commands

### Local Development
```bash
# Start development server with drafts and future content
hugo server -D --buildFuture

# Start development server (production-like)
hugo server
```

### Building
```bash
# Build for production
hugo --gc --minify

# Build with specific base URL
hugo --gc --minify -b https://yoursite.com/
```

### Content Management
```bash
# Create new post
hugo new post/my-post/index.md

# Create new publication
hugo new publication/my-paper/index.md

# Create new project
hugo new project/my-project/index.md
```

## Configuration

The site configuration is split across multiple files in `config/_default/`:
- `hugo.yaml` - Main Hugo configuration
- `params.yaml` - Theme-specific parameters
- `menus.yaml` - Site navigation
- `languages.yaml` - Multi-language support
- `module.yaml` - Go module configuration

## Content Types

- **Authors** (`content/authors/`) - Author profiles with biographies
- **Posts** (`content/post/`) - Blog posts and news
- **Publications** (`content/publication/`) - Academic papers and publications
- **Projects** (`content/project/`) - Research projects and portfolio items
- **Events** (`content/event/`) - Talks and presentations

## Deployment

The site is configured for multiple deployment options:

### Netlify (via netlify.toml)
- Automatic deployment on push to main branch
- Hugo version: 0.123.3

### GitHub Pages (via .github/workflows/publish.yaml)
- Automatic deployment on push to main branch
- Uses GitHub Actions with Hugo v0.124.1

## Go Modules

The theme is managed via Go modules. Key dependencies:
- `github.com/HugoBlox/hugo-blox-builder/modules/blox-bootstrap/v5`
- `github.com/HugoBlox/hugo-blox-builder/modules/blox-plugin-netlify`
- `github.com/HugoBlox/hugo-blox-builder/modules/blox-plugin-reveal`

Update modules with: `hugo mod get -u`