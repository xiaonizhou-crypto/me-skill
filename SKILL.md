---
name: me
description: "Digital Twin Agent — Build your AI clone from Feishu/Lark data. It learns your tone, habits, and relationships, then helps you catch up on messages, draft replies in your voice, and prep for meetings. | 数字分身 Agent——从飞书数据构建你的 AI 分身，用你的语气回消息、看动态、准备会议。"
---

# Digital Twin Agent — "me"

> **English** | [中文](#中文说明)

Your AI clone for Feishu/Lark. It learns how you talk, who you work with, and what you care about — then handles your daily Feishu workflow in your voice.

**This is not a generic AI assistant. This is another YOU.**

---

## Quick Start

### 1. Prerequisites

- [lark-cli](https://github.com/larksuite/cli) installed and authorized
- Claude Code with skills support

### 2. Install

```bash
# Clone the skill into your Claude Code skills directory
git clone https://github.com/xiaonizhou-crypto/me-skill.git ~/.claude/skills/me
```

### 3. Setup — Build Your Digital Twin

Run in Claude Code:

```
/me setup
```

This will:
1. Authenticate with your Feishu account via `lark-cli`
2. With your approval, retrieve your own messages from a chosen date range (up to 30 days) into a temporary local directory
3. Analyze your communication patterns, tone, and relationships
4. Generate approved personal profile files in `private/` (local files excluded from normal Git adds)

**Local storage is not local-only processing.** `private/` is excluded from normal Git commits, but an AI coding tool may send message content and derived profiles to its configured model provider for analysis. Review account, provider and organization policies first; use redacted input and the minimum date range needed.

### 4. Use

```
/me                     — Catch up on recent messages (default)
/me catch-up            — See what happened in the last 4 hours
/me reply <group-name>  — Draft a reply in your voice
/me meeting-prep        — Prep brief for your next meeting
/me meeting-followup    — Extract action items from last meeting
```

---

## Capabilities

### catch-up — What did I miss?

Scans recent Feishu messages and sorts them into:
- **Need your reply** — DMs, @mentions, important threads
- **Worth knowing** — Key discussions in your active groups
- **Noise** — Bot messages, system notifications, irrelevant chats

Each suggested reply is drafted in your personal tone with a confidence tag (high/medium/low).

### reply — Talk like me

Given a chat name or message, pulls context and drafts a reply that sounds like you wrote it. Shows the draft for your approval before sending — **never sends automatically**.

### meeting-prep — Get me ready

Before a meeting, gathers:
- Meeting info (time, attendees, VC link)
- Recent related discussions from relevant chats
- Key topics you should be prepared for

### meeting-followup — What do I owe people?

After a meeting, pulls notes/minutes and:
- Extracts action items assigned to you
- Drafts follow-up messages
- Shows everything for approval before sending

---

## How Setup Works

The `/me setup` command analyzes your Feishu data to build three personal profile files:

| File | What it captures |
|------|------------------|
| `private/persona.md` | Your communication stats, catchphrases, emoji habits, active hours, message style |
| `private/voice-rules.md` | How you say yes/no/thanks, who you call nicknames, your level of formality |
| `private/relationship-map.md` | Your frequent collaborators, their roles, how you address them, which groups matter |

All files are stored in `private/` which is **git-ignored**. This describes local file storage, not where AI processing occurs. Analysis can send content to the configured model provider.

### What data is accessed?

| Data | Purpose | Stored? |
|------|---------|---------|
| Your sent messages (30 days) | Analyze tone, catchphrases, timing | Temporarily during setup, then deleted |
| Your meeting list | Identify collaborators and meeting patterns | Not stored |
| Your chat list | Map your active groups | Stored in relationship-map only as group names |

### What data is NOT accessed?

- Other people's private messages
- File contents or attachments
- Anything outside your authorized Feishu scope

---

## Safety Rules

1. **Never auto-sends** — All replies require your explicit confirmation
2. **Sensitive topics flagged** — HR, salary, legal, contracts → marked as "reply yourself"
3. **No fake decisions** — Approvals, commitments, budgets → won't reply on your behalf
4. **Confidence tagged** — Every suggested reply shows high/medium/low confidence
5. **No hallucination** — If unsure, says so and suggests you verify

---

## File Structure

```
me/
├── SKILL.md              ← You are here (public, safe to share)
├── reference/
│   └── setup-prompt.md   ← Instructions for /me setup analysis
├── private/              ← YOUR data (git-ignored; model access requires consent)
│   ├── persona.md
│   ├── voice-rules.md
│   └── relationship-map.md
└── .gitignore            ← Excludes private/ from normal Git adds; force-add and other upload tools can bypass it
```

---

## Requirements

- `lark-cli` >= 1.0.4
- Authorized domains: `im`, `vc`, `calendar`, `contact`, `docs`, `minutes`
- Claude Code with skill support

---

<a id="中文说明"></a>
## 中文说明

### 这是什么？

你的飞书 AI 分身。它学习你说话的方式、你和谁协作、你关心什么——然后用你的语气帮你处理飞书日常。

**它不是通用 AI 助手，是另一个你。**

### 快速开始

1. 安装 [lark-cli](https://github.com/larksuite/cli) 并完成授权
2. 克隆到 Claude Code skills 目录：
   ```bash
   git clone https://github.com/xiaonizhou-crypto/me-skill.git ~/.claude/skills/me
   ```
3. 在 Claude Code 里运行 `/me setup`，自动分析你的飞书数据，生成专属分身
4. 之后直接用 `/me` 就行

### 核心能力

| 命令 | 用途 |
|------|------|
| `/me` | 默认看最近消息（catch-up） |
| `/me catch-up` | 帮你看飞书发生了什么 |
| `/me reply <群名>` | 用你的语气帮你回消息 |
| `/me meeting-prep` | 会前准备简报 |
| `/me meeting-followup` | 会后提取 action items |

### 数据安全

- 画像文件写入本地 `private/` 目录；本地保存不等于仅在本地处理
- `private/` 已加入 `.gitignore`，默认不会被 Git 添加；强制添加或其他上传工具仍可能上传它
- 分析过程中的临时数据用完即删
- 只按已授权范围读取本人消息；AI 分析可能把消息和画像发送给所配置的模型服务商，必须先确认数据范围与处理方式

### 授权域

安装时需要授权这些飞书权限：

```bash
lark-cli auth login --domain im,vc,calendar,contact,docs,minutes
```

## Consent and limits / 授权与边界

Before setup, show the date range, data categories, model provider, and planned files. Explain that local execution and `.gitignore` do not guarantee local-only AI processing. Wait for explicit consent before retrieving or analyzing messages. Do not send messages, upload files, or add contacts as part of calibration.

首次使用前明确告知读取的日期范围、数据类别、模型服务商及拟生成文件，得到本人同意后再取数和分析。默认先做小范围校准；本人发送的消息也可能包含他人隐私或客户秘密。先脱敏，不把客户保密信息写入可复用画像。

Temporary-file cleanup does not delete model-provider logs, AI-tool transcripts, caches or backups. Their retention and deletion depend on the configured services. Do not promise complete deletion or local-only processing without verifying the actual configuration.
