# 网页内容提炼 + Telegram 推送

> 基于 n8n：发送网页 URL → 自动提取核心内容 → AI 总结 → 推送到 Telegram

[English](README.en.md) | 简体中文

## 简介

发送一篇文章的 URL 到 Webhook，工作流自动完成：

```
Webhook 收到 URL → Jina AI 去噪提取 → LLM 总结 → Telegram 推送 → 返回成功
```

适合这些场景：
- 快速摘要一篇长文
- 定时监控某个页面的更新
- 竞品动态跟踪
- 个人信息流整理

## 特性

| 特性 | 说明 |
|------|------|
| **失败自动重试** | HTTP Request 开启重试，5 秒间隔 |
| **TG HTML 排版** | Emoji、加粗、代码块，Telegram 原生渲染 |
| **支持主流大语言模型** | OpenAI / 通义千问 / DeepSeek / Gemini 等 |
| **排版安全** | Prompt 约束 HTML 格式，防止标签未闭合 |
| **Webhook 触发** | POST 调用，方便集成 |

## 快速开始

### 前置要求

| 工具/服务 | 用途 |
|-----------|------|
| [n8n](https://n8n.io/) | 工作流平台 |
| Telegram Bot | 消息推送 |
| 大模型 API Key | 内容提炼 |
| Jina AI API Key | 网页去噪（非必要、可选） |

### 部署步骤

#### 1. 导入工作流

```
n8n → 右上角菜单 → Import from file → 选择 flow.json
```

导入后会看到 6 个节点：

```
Webhook ──→ HTTP Request ──→ Basic LLM Chain ──→ Send a text message ──→ Respond to Webhook
                                      ↑
                              OpenAI Chat Model
```

#### 2. 配置 Telegram

**获取 Bot Token：**

1. Telegram 搜索 [@BotFather](https://t.me/BotFather)
2. 发送 `/newbot`，按提示起名（Username 必须以 `bot` 结尾）
3. 创建成功后会收到一串 Bot Token（如 `7890123456:AAFi...`）
4. 点击链接进入刚创建的机器人聊天框，点一次 **`/start`** 激活它

**获取 Chat ID：**

机器人不能主动给陌生人发消息，需要知道发给谁：

- **发给个人**：搜索 [@userinfobot](https://t.me/userinfobot)，发任意消息，它会返回你的个人 ID
- **发到频道/群组**：
  1. 把机器人拉进频道/群组，**提升为管理员**
  2. 转发频道的一条消息给 [@userinfobot](https://t.me/userinfobot)，它会返回该频道的 Chat ID

**在 n8n 中填入：**

1. 点开 `Send a text message` 节点
2. `Credential` → `Set up credential` → 填入 Access Token → 右上角 Save → 关闭 Telegram account 界面
3. `Chat ID` 填你的 Chat ID

#### 3. 配置大模型

1. 点开 `OpenAI Chat Model` 节点
2. `Credential` → `Set up credential` → 填 API Key 和 Base URL → 右上角 Save → 关闭 OpenAI account 界面
3. 在 `Model` 下拉框选模型（如 `gpt-4o`、`gpt-4o-mini`）

#### 4. 测试

1. 点击下方 **Execute workflow** 激活工作流
2. 点击 `Webhook` 节点，复制 Webhook URL
3. 发个测试请求：

```bash
curl -X POST http://localhost:5678/webhook/tg-summary \
  -H "Content-Type: application/json" \
  -d '{"url": "https://jina.ai/blog/reader-lm"}'
```

4. 看 Telegram 有没有收到消息

## 参数说明

### Webhook

- **Method**: `POST`
- **Path**: `/webhook/tg-summary`
- **Content-Type**: `application/json`

**Body 参数：**

| 参数 | 类型 | 必填 | 说明 | 示例 |
|------|------|------|------|------|
| `url` | string | 是 | 网页 URL | `"https://example.com/article"` |

```json
{ "url": "https://example.com/tech-article" }
```

成功响应：`"success"`

### Prompt

`Basic LLM Chain` 节点中的内置 Prompt：

```text
你是一个资深的科技/社交媒体博主。请帮我阅读以下网页内容，
并用中文提炼出 3 个最核心、最引人入胜的观点或要点。

排版要求：
- 适合 Telegram 浏览的排版
- 每条要点开头配 Emoji
- 语言幽默、干练

格式约束：
1. 用标准 HTML 格式，不用 Markdown
2. 只用 <b>、<i>、<code> 标签
3. 标签必须完全闭合
4. < > 转义为 &lt; &gt;
5. 4000 字以内
- 底部加一句 CTA
```

> 可以根据需要修改：要点数量、语言风格、CTA 内容等

## 高级配置

### 输出风格

修改 `Basic LLM Chain` 的 Prompt 角色设定：

| 风格 | Prompt 改法 |
|------|-------------|
| 新闻 | "你是新闻编辑，客观简洁地总结" |
| 社交 | "你是幽默的博主，干练带梗" |
| 学术 | "你是严谨的研究者，结构化输出并标注引用" |

## 预览

![预览](preview.png)

## 相关链接

- [n8n 文档](https://docs.n8n.io/)
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [Jina AI Reader](https://jina.ai/reader)
- [OpenAI API](https://platform.openai.com/docs)