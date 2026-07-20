# GitHub Release Update Monitor + Telegram Push

> Built on n8n: scheduled GitHub release detection → AI-powered changelog summarization → Telegram push

English | [简体中文](README.md)

## Overview

Scheduled detection of a GitHub repo's Release updates. When a new version is published, it automatically pushes a notification:

```
Scheduled trigger → Request Release API → LLM extracts changes → Check if updated → Push to Telegram
```

Use cases:
- Monitor open-source project releases
- Track dependency updates
- Get notified of tech stack version changes

## Features

| Feature | Description |
|---------|-------------|
| **Scheduled detection** | Defaults to every minute, interval customizable |
| **Smart deduplication** | No push when no updates, avoids spam |
| **Changelog extraction** | Auto-extracts version number, new features, bug fixes |
| **Supports major LLMs** | OpenAI / Qwen / DeepSeek / Gemini etc. |
| **TG HTML formatting** | Emoji, bold, code blocks — native Telegram rendering |

## Quick Start

### Prerequisites

| Tool/Service | Purpose |
|--------------|---------|
| [n8n](https://n8n.io/) | Workflow platform |
| Telegram Bot | Message delivery |
| LLM API Key | Content summarization |
| GitHub repo | Monitoring target |

### Setup

#### 1. Import the workflow

```
n8n → top-right menu → Import from file → select flow.json
```

After import you'll see 6 nodes:

```
Schedule Trigger ──→ HTTP Request ──→ Basic LLM Chain ──→ If ──→ Send a text message
                                              ↑
                                      OpenAI Chat Model
```

#### 2. Configure monitoring target

Default monitors `https://github.com/ynqwer/ai-workflow-hub`. To change:

1. Open the `HTTP Request` node
2. Change `URL` to the GitHub Release API you want to monitor:
   ```
   https://api.github.com/repos/{owner}/{repo}/releases/latest
   ```
   Example: `https://api.github.com/repos/openai/openai-node/releases/latest`

#### 3. Configure Telegram

**Get Bot Token:**

1. Search [@BotFather](https://t.me/BotFather) on Telegram
2. Send `/newbot`, follow the prompts to name your bot (Username must end with `bot`)
3. You'll receive a Bot Token (e.g. `7890123456:AAFi...`)
4. Click the link to open your new bot's chat, then click **`/start`** to activate it

**Get Chat ID:**

Bots can't message strangers — they need to know who to send to:

- **For personal messages**: Search [@userinfobot](https://t.me/userinfobot), send any message, it returns your personal ID
- **For channels/groups**:
  1. Add the bot to the channel/group and **promote it to administrator**
  2. Forward a message from the channel to [@userinfobot](https://t.me/userinfobot), it returns the channel's Chat ID

**Fill in n8n:**

1. Open the `Send a text message` node
2. `Credential` → `Set up credential` → enter Access Token → Save (top right) → close Telegram account dialog
3. Enter your Chat ID in the `Chat ID` field

#### 4. Configure LLM

1. Open the `OpenAI Chat Model` node
2. `Credential` → `Set up credential` → enter API Key and Base URL → Save (top right) → close OpenAI account dialog
3. Select a model from the `Model` dropdown (e.g. `gpt-4o`, `gpt-4o-mini`)

#### 5. Configure detection interval

Default is every minute. To change:

1. Open the `Schedule Trigger` node
2. Modify `Minutes Interval`

#### 6. Test

1. Click **Execute workflow** at the bottom to activate
2. Wait for scheduled trigger, or click `Execute workflow` to manually trigger once
3. Check if Telegram received the message

## Parameters

### Monitoring Target

- **Default**: `https://github.com/ynqwer/ai-workflow-hub`
- **Modify**: Change URL in `HTTP Request` node

### Detection Interval

- **Default**: 1 minute
- **Modify**: Change `Minutes Interval` in `Schedule Trigger` node

### Prompt

Built-in prompt in the `Basic LLM Chain` node:

```text
You are a seasoned tech broadcaster and open-source community intelligence analyst. 
Read the following GitHub repo's latest Release JSON data.

### Task Requirements:
1. Extract the latest version number (tag_name) and the release log (body).
2. Distill the typically lengthy, English changelog with scattered commits into 3-5 
   key Chinese updates.
3. Highlight: new features (Features), major bug fixes (Bug Fixes), and breaking changes.

### 🚨 Core Deduplication Mechanism:
Carefully check the fetched data. If after analysis you find no valid new features, 
major fixes, or business updates, or if the body is empty, strictly output ONLY:

无更新

(no extra punctuation or explanation allowed)

### Format Constraints (only when updates exist):
1. Use standard HTML format, absolutely no Markdown markers (**, _, *, `, ### etc.)
2. Only use <b>, <i>, <code> tags
3. All opened HTML tags must be properly closed
4. Escape < and > as &lt; and &gt;
5. Keep under 500 characters, start with Emoji, keep it sharp
```

> Customize as needed: number of points, language style, focus areas

## Advanced

### Monitor Other Pages

Default monitors GitHub Release API. You can change to other pages:

1. Modify `HTTP Request` node's URL
2. Modify `Basic LLM Chain`'s prompt to match new data format

### Historical Version Comparison

Current workflow only checks the latest version. To compare historical versions:

1. Add a database node (e.g. Redis / PostgreSQL)
2. Store the previous version number
3. Add version comparison logic in the `If` node

## Preview

![Preview](preview.png)

## Links

- [n8n Docs](https://docs.n8n.io/)
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [GitHub Releases API](https://docs.github.com/en/rest/releases/releases)
- [OpenAI API](https://platform.openai.com/docs)