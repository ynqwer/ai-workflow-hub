<div align="center">

# AI Workflow Hub

**Open-source AI workflow blueprint library**

[![License](https://img.shields.io/github/license/ynqwer/ai-workflow-hub)](LICENSE)
[![Workflows](https://img.shields.io/badge/Workflows-2+-blue)](workflows/)
[![PR Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](#-contributing)

English | [简体中文](README.md)

</div>

---

## About

**AI Workflow Hub** is an open-source workflow collection for developers, creators, ops folks, and more.

> **Disclaimer**: This project provides workflow blueprints only. Users are solely responsible for any third-party LLM API costs, Jina AI quota consumption, and legal risks related to copyright and anti-scraping when fetching target websites.

## Features

| Feature | Description |
|---------|-------------|
| **Battle-tested** | Every workflow has been verified in real scenarios |
| **One-click import** | JSON format, import directly into n8n / Dify etc. |
| **Parameterized** | Core parameters are configurable for different use cases |
| **Well-documented** | Each workflow has its own setup guide |
| **Cross-platform** | Supports major workflow platforms |
| **Privacy-first** | Local deployment supported, data never leaves your server |

## Quick Start

```bash
git clone https://github.com/ynqwer/ai-workflow-hub.git
```

Navigate to the workflow you need and read its documentation:

```text
workflows/[workflow]/README.md   ← setup guide
```

## Available Workflows

| Workflow | Platform | Description | Difficulty | Status |
|----------|----------|-------------|------------|--------|
| [web-summary-to-tg](workflows/web-summary-to-tg/) | n8n | Web content extraction + Telegram push | Medium | Ready |
| [GitHub Release Monitor](workflows/web-update-to-tg/) | n8n | Scheduled GitHub release detection + Telegram push | Medium | Ready |

## Directory Structure

```text
ai-workflow-hub/
├── README.md
├── README.en.md
└── workflows/
    ├── web-summary-to-tg/
    │   ├── README.md
    │   ├── flow.json
    │   └── preview.png
    └── web-update-to-tg/
        ├── README.md
        ├── flow.json
        └── preview.png
```

## Use Cases

### Indie Developers
- Content monitoring & auto-push
- Customer support & ticket processing
- Data collection pipelines

### Content Creators
- Cross-platform content sync
- Trend tracking & summarization
- Competitor monitoring

### Tech Enthusiasts
- Knowledge base automation
- AI Agent experiments
- Ops automation

## Contributing

Contributions of all kinds are welcome:

- File bugs or feature requests → [Issues](https://github.com/ynqwer/ai-workflow-hub/issues)
- Improve documentation
- Submit new workflows
- Star this repo