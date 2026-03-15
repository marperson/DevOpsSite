---

title: "Building a Personal Website with Hugo, Claude Code, and SubRouter.ai on GitHub Pages"
date: 2026-03-15
tags: ["hugo", "github-pages", "claude", "ai", "subrouter", "static-site"]
description: "A step-by-step guide to building and deploying a Hugo-powered personal website using Claude Code and SubRouter.ai as the token provider."
featuredImage: "feature.png"
---

# Building a Personal Website with Hugo, Claude Code, and SubRouter.ai

In this tutorial, we'll build a **modern personal website** using:

* **Hugo** for static site generation
* **GitHub Pages** for hosting
* **Claude Code** for AI-assisted development
* **SubRouter.ai** as the **token / API provider**

This stack allows you to **generate content, automate coding, and publish a fast static site for free**.

---

# Architecture Overview

{{< mermaid >}}
flowchart LR
    User[Developer]
    Claude[Claude Code CLI]
    Subrouter[SubRouter.ai Token Provider]
    API[LLM APIs]
    Hugo[Hugo Static Site Generator]
    GitHub[GitHub Repository]
    Pages[GitHub Pages Hosting]

    User --> Claude
    Claude --> Subrouter
    Subrouter --> API
    Claude --> Hugo
    Hugo --> GitHub
    GitHub --> Pages
{{< /mermaid >}}

Explanation:

1. You interact with **Claude Code CLI**
2. Claude requests tokens through **SubRouter.ai**
3. SubRouter routes requests to **LLM providers**
4. Claude helps generate **Hugo content/code**
5. Hugo builds the site
6. GitHub hosts the repo
7. GitHub Pages serves the website

---

# Step 1: Install Hugo

Install Hugo on your machine.

### macOS

```bash
brew install hugo
```

### Linux

```bash
sudo apt install hugo
```

### Verify installation

```bash
hugo version
```

---

# Step 2: Create a Hugo Website

Create a new site:

```bash
hugo new site mysite
cd mysite
```

Initialize Git:

```bash
git init
```

---

# Step 3: Add a Theme

Example using the **PaperMod theme**:

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

Update `hugo.toml`:

```toml
baseURL = "https://YOUR_USERNAME.github.io/"
languageCode = "en-us"
title = "My Personal Site"

theme = "PaperMod"
```

---

# Step 4: Install Claude Code

Install Claude Code CLI.

```bash
npm install -g @anthropic-ai/claude-code
```

Verify:

```bash
claude --version
```

---

# Step 5: Configure SubRouter.ai

Instead of connecting Claude directly to a provider, route requests through **SubRouter.ai**.

Set environment variables:

```bash
export ANTHROPIC_BASE_URL=https://api.subrouter.ai
export ANTHROPIC_API_KEY=YOUR_SUBROUTER_KEY
```

Now **Claude Code will use SubRouter tokens automatically**.

---

# Token Flow

{{< mermaid >}}
sequenceDiagram
    participant Dev as Developer
    participant Claude as Claude Code
    participant Subrouter as SubRouter.ai
    participant LLM as AI Model Provider

    Dev->>Claude: Request code/content
    Claude->>Subrouter: API request
    Subrouter->>LLM: Route request
    LLM-->>Subrouter: Response
    Subrouter-->>Claude: Response
    Claude-->>Dev: Generated output
{{< /mermaid >}}

---

# Step 6: Use Claude to Generate Content

Claude can help generate posts.

Example prompt:

```
Create a Hugo blog post about building a homelab with Kubernetes.
```

Create a post file:

```bash
hugo new posts/homelab-kubernetes.md
```

Paste Claude's generated content.

---

# Step 7: Run Hugo Locally

Start the development server:

```bash
hugo server -D
```

Open:

```
http://localhost:1313
```

---

# Step 8: Deploy to GitHub Pages

Create a repository:

```
username.github.io
```

Push your site:

```bash
git add .
git commit -m "initial site"
git branch -M main
git remote add origin https://github.com/username/username.github.io.git
git push -u origin main
```

---

# GitHub Pages Deployment Flow

{{< mermaid >}}
flowchart TD

    A[Write Content]
    B[Claude Code Assist]
    C[Hugo Build]
    D[Git Commit]
    E[GitHub Repository]
    F[GitHub Pages]

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
{{< /mermaid >}}

---

# Optional: Automate Deployment with GitHub Actions

Create:

```
.github/workflows/hugo.yml
```

Example workflow:

```yaml
name: Deploy Hugo site

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

---

# Benefits of This Setup

## Fast

Hugo generates static pages extremely quickly.

## Free Hosting

GitHub Pages provides free hosting.

## AI-Assisted Writing

Claude Code can:

* generate posts
* refactor site code
* automate documentation

## Flexible Token Routing

SubRouter.ai allows:

* provider switching
* cost optimization
* centralized API management

---

# Recommended Project Structure

```
mysite/
├── content/
│   └── posts/
├── themes/
├── static/
├── layouts/
├── hugo.toml
└── public/
```

---

# Conclusion

Combining **Hugo**, **Claude Code**, and **SubRouter.ai** creates a powerful workflow for building and maintaining a personal website.

You gain:

* fast static site generation
* AI development assistance
* flexible LLM provider routing
* free global hosting

Perfect for:

* developer blogs
* technical documentation
* personal portfolios
* AI-assisted writing workflows

---

Happy building 🚀
