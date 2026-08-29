---
title: "Running Claude Code with Ollama: Build Local AI Coding Agents"
date: 2026-03-16
draft: false
description: "Learn how to run Claude Code with Ollama to create private AI coding agents on your local machine using open-source LLMs."
tags: ["ollama", "claude-code", "ai", "local-llm", "developer-tools", "devops", "tutorial"]
featuredImage: "feature.png"
---
# Running Claude Code with Ollama: Local AI Agents for Developers

The developer tooling ecosystem is rapidly evolving with the rise of **local large language models (LLMs)**. Tools like **Ollama** allow developers to run powerful models directly on their machines, while **Claude Code** introduces an agentic workflow for building and modifying applications.

With recent updates, these two technologies can now work together—allowing developers to run Claude-style coding agents **on local open-source models instead of proprietary cloud APIs**.

This article explores how Ollama and Claude Code integrate, why this matters for developers, and how to choose the best models for local AI-powered coding.

---

# What is Ollama?

**Ollama** is a runtime that allows developers to **run large language models locally** with a simple CLI and API. It simplifies model management by making it easy to download, run, and integrate open-source LLMs into applications.

Instead of calling remote APIs, developers can run models directly on their hardware.

### Key Benefits

- **Data privacy** – source code never leaves your machine  
- **Cost control** – no per-token API charges  
- **Offline capability** – models work without internet  
- **Customization** – easily switch between models  

Running models locally has become increasingly attractive for developers who want **full control over their AI tooling**.

---

# Choosing the Right Ollama Model

One of the biggest challenges with Ollama is selecting the right model.

The ecosystem now includes dozens of models optimized for different tasks such as coding, reasoning, or multimodal understanding.

## Hardware First

Model selection typically depends on **available system memory or GPU VRAM**.

| Model Size | Minimum Memory | Typical Hardware |
|------------|---------------|-----------------|
| 3B – 7B | ~8 GB | laptops |
| 13B – 14B | ~16 GB | gaming laptops |
| 32B – 34B | ~32 GB | high-end workstations |
| 70B+ | 48 GB+ | dedicated AI workstations |

Hardware constraints usually determine which models you can realistically run locally.

---

## Quantization

Most Ollama models are distributed in **quantized formats**, which reduce memory usage.

Common formats include:

- **Q4** – smaller and faster but less precise  
- **Q5_K_M** – good balance between quality and memory  
- **Q8** – higher precision but larger memory footprint  

In many cases, a **larger model with stronger quantization performs better than a smaller high-precision model**.

---

# Best Ollama Models for Coding

Several models stand out for development and programming tasks.

## DeepSeek-Coder

DeepSeek-Coder is considered one of the strongest open models for coding.

Strengths include:

- excellent reasoning ability  
- strong debugging capability  
- trained on massive code datasets  

It performs well across coding benchmarks such as **HumanEval** and **MBPP**.

---

## Qwen-Coder

Qwen-Coder is another highly capable coding model.

Key features:

- supports **90+ programming languages**
- strong multi-file understanding
- good reasoning performance

Many developers consider it one of the most balanced open-source coding models available.

---

## Other Notable Models

Additional models worth exploring:

- **Yi-Coder** – useful for full-stack development
- **StarCoder2** – strong performance in specific programming languages
- **CodeGemma** – lightweight option for smaller hardware setups

The best choice depends on **hardware resources and project needs**.

---

# What is Claude Code?

**Claude Code** is a terminal-based coding agent created by Anthropic.

Instead of acting as a simple chatbot, it can interact directly with your development environment.

Capabilities include:

- reading project files  
- editing and generating code  
- executing shell commands  
- running tests  
- iterating on solutions automatically  

This makes Claude Code more like an **AI development assistant inside your terminal**.

---

# The Breakthrough: Claude Code + Ollama

Recent updates introduced compatibility between **Ollama and the Anthropic Messages API**, which Claude Code uses internally.

Because of this compatibility, Claude Code can now run using **local models served by Ollama**.

From Claude Code’s perspective, nothing changes—it still makes API calls—but those calls are routed to a **local LLM instead of a cloud model**.

---

# Why This Matters

Running Claude Code with Ollama provides several advantages.

| Benefit | Description |
|--------|-------------|
| Privacy | your source code stays local |
| Cost Savings | no API token costs |
| Offline Development | AI works without internet |
| Flexibility | choose different open-source models |

This combination enables developers to run **AI coding agents entirely on their own machines**.

---

# Setting Up Claude Code with Ollama

## 1. Install Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

---

## 2. Configure Claude Code to Use Ollama

```bash
export ANTHROPIC_AUTH_TOKEN=ollama
export ANTHROPIC_BASE_URL=http://localhost:11434
```

---

## 3. Run Claude Code

```bash
ollama run qwen2.5-coder
```

or launch Claude Code directly with a model:

```bash
claude --model qwen2.5-coder
```

Claude Code will now communicate with the **local Ollama model** instead of Anthropic’s API.

---

# Recommended Models for Claude Code

For agent-based development workflows, models with **larger context windows** perform better.

Good options include:

Local models:

- `qwen2.5-coder`
- `deepseek-coder`
- `gpt-oss:20b`

These models provide good reasoning capabilities and support working with **larger codebases**.

---

# The Future of Local AI Development

The integration between Ollama and Claude Code represents a significant step toward **fully local AI-powered development environments**.

Developers can now:

- run powerful LLMs locally
- build agentic coding workflows
- maintain full control over their code and infrastructure

As open-source models continue to improve, local AI assistants will likely become a **standard part of the modern development stack**.

---

# Conclusion

Combining **Ollama** with **Claude Code** creates a powerful workflow for running AI coding agents locally.

This approach offers:

- improved privacy
- reduced costs
- offline capability
- flexibility in choosing models

For developers exploring **self-hosted AI and AI-assisted programming**, this setup represents one of the most exciting directions in modern developer tooling.