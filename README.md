# DevOps Site

A Hugo static site for DevOps knowledge sharing, combined with OpenSpec workflow for managing changes.

## Overview

This repository contains two main components:

1. **Hugo Static Site** (`site/`): A documentation and blog site for DevOps topics, using the [Blowfish theme](https://blowfish.page/).
2. **OpenSpec Workflow** (`openspec/`): A spec-driven change management system for tracking and implementing site changes.

The project uses Claude Code with custom skills to streamline the OpenSpec workflow.

## Prerequisites

- [Hugo](https://gohugo.io/) (extended version recommended) for building the static site
- [OpenSpec CLI](https://github.com/openspec/openspec) for change management
- [Claude Code](https://claude.ai/code) with custom skills configured

## Quick Start

### 1. Clone and Setup
```bash
git clone <repository-url>
cd DevOpsSite

# Initialize and update git submodules (for the Blowfish theme)
git submodule update --init --recursive
```

### 2. Install Hugo
```bash
# macOS with Homebrew
brew install hugo

# Other platforms: see https://gohugo.io/installation/
```

### 3. Install OpenSpec
```bash
# Requires Node.js
npm install -g openspec
```

### 4. Run the Site Locally
```bash
cd site
hugo server
```
Visit http://localhost:1313 to see the site.

## Project Structure

```
├── site/                    # Hugo static site
│   ├── archetypes/         # Content templates
│   ├── config/             # Hugo configuration
│   ├── content/            # Site content (pages, posts, knowledge base)
│   ├── themes/blowfish/    # Hugo theme (git submodule)
│   └── hugo.toml           # Site configuration
│
├── openspec/               # OpenSpec change management
│   ├── config.yaml        # OpenSpec configuration
│   ├── changes/           # Active changes
│   └── specs/             # Schema definitions
│
├── .claude/               # Claude Code custom commands and skills
│   ├── commands/opsx/     # OpenSpec workflow commands
│   └── skills/openspec-*/ # Skill implementations
│
└── CLAUDE.md              # Project instructions for Claude Code
```

## Content Types

The site supports several content types via Hugo archetypes:

- **Posts** (`site/content/posts/`): Blog posts and articles
- **Knowledge** (`site/content/knowledge/`): Technical documentation and guides
- **Portfolio** (`site/content/portfolio/`): Project showcases
- **Pages** (`site/content/pages/`): Static pages (About, Contact)

## OpenSpec Workflow

OpenSpec manages changes through a structured process:

### Creating a Change
```bash
# Use Claude Code skill
/opsx:propose "Add new blog post about Kubernetes"
```

This creates:
- `openspec/changes/add-new-blog-post-about-kubernetes/`
- `proposal.md` – What and why of the change
- `design.md` – How the change will be implemented
- `tasks.md` – Implementation steps (checklist)

### Implementing a Change
```bash
# Use Claude Code skill
/opsx:apply [change-name]
```

Work through tasks in `tasks.md`, marking each as completed (`- [ ]` → `- [x]`).

### Archiving a Change
```bash
# After all tasks are complete
/opsx:archive
```

Moves the change to `openspec/changes/archive/`.

### Manual OpenSpec Commands
```bash
# List all changes
openspec list

# Check change status
openspec status --change <name>

# Get instructions for creating an artifact
openspec instructions proposal --change <name>
```

## Custom Claude Code Skills

The `.claude/` directory contains custom skills for OpenSpec workflows:

- **`/opsx:propose`** – Create a new change and generate all artifacts
- **`/opsx:apply`** – Implement tasks from a change
- **`/opsx:explore`** – Enter explore mode for investigating problems
- **`/opsx:archive`** – Archive a completed change

## Development Workflow

### Adding New Content
1. Use `/opsx:propose` to create a change for the new content
2. Follow the generated tasks in `/opsx:apply`
3. Typically involves:
   - Creating a new markdown file in the appropriate `content/` subdirectory
   - Adding front matter (title, date, tags, etc.)
   - Writing content
   - Testing locally with `hugo server`
4. Archive the change with `/opsx:archive`

### Modifying Site Configuration
1. Use `/opsx:propose` for the configuration change
2. Modify `hugo.toml` or files in `config/` directory
3. Test changes locally
4. Archive the change

### Theme Customizations
The Blowfish theme is included as a git submodule. For customizations:
1. Fork the theme or create a local copy
2. Modify theme files as needed
3. Update the git submodule reference

## Deployment

### Building the Site
```bash
cd site
hugo  # Outputs to site/public/
```

### Deployment Options
- **Netlify**: Connect repository, set build command to `hugo`, publish directory to `public`
- **Vercel**: Similar setup
- **GitHub Pages**: Use GitHub Actions to build and deploy
- **Self-hosted**: Copy `public/` contents to web server

### Example GitHub Pages Deployment
1. Enable GitHub Pages in repository settings
2. Create `.github/workflows/hugo.yml`:
```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build
        run: hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
        working-directory: ./site
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site/public
```

## Contributing

1. Use OpenSpec workflow for all changes
2. Keep changes minimal and focused
3. Test locally before archiving changes
4. Follow existing patterns for content structure

## License

[Add appropriate license]

## Acknowledgments

- [Hugo](https://gohugo.io/) - Static site generator
- [Blowfish theme](https://blowfish.page/) - Hugo theme
- [OpenSpec](https://github.com/openspec/openspec) - Change management system
- [Claude Code](https://claude.ai/code) - AI-powered development assistant