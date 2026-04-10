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
2. Pull your recent 30 days of messages (stored locally, never uploaded)
3. Analyze your communication patterns, tone, and relationships
4. Generate your personal profile files in `private/` (git-ignored, only on your machine)

**Your data never leaves your machine. The `private/` directory is in `.gitignore`.**

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

All files are stored in `private/` which is **git-ignored**. Your personal data stays on your machine.

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
├── private/              ← YOUR data (git-ignored, never shared)
│   ├── persona.md
│   ├── voice-rules.md
│   └── relationship-map.md
└── .gitignore            ← Ensures private/ is never committed
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

- 所有个人数据只存在你本地的 `private/` 目录
- `private/` 已加入 `.gitignore`，不会被提交到 git
- 分析过程中的临时数据用完即删
- 不访问别人的私聊、不上传任何数据

### 授权域

安装时需要授权这些飞书权限：

```bash
lark-cli auth login --domain im,vc,calendar,contact,docs,minutes
```
