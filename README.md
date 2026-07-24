# OpenCode Configuration Repository / OpenCode 配置仓库

> Setup guides and configuration templates for an OpenCode development environment.  
> Designed for AI agents to autonomously set up an OpenCode environment from scratch.
>
> OpenCode 开发环境的配置指南和模板。  
> 专为 AI 自主完成 OpenCode 环境搭建而设计。

---

<!-- TOC -->
- [English](#english)
  - [What This Is](#what-this-is)
  - [Prerequisites](#prerequisites)
  - [Step-by-Step Setup for AI Agents](#step-by-step-setup-for-ai-agents)
    - [1. Install OpenCode CLI](#1-install-opencode-cli)
    - [2. Configure opencode.json](#2-configure-opencodejson)
    - [3. Configure AGENTS.md](#3-configure-agentsmd)
    - [4. Configure oh-my-opencode-slim.json](#4-configure-oh-my-opencode-slimjson)
    - [5. Install Skills](#5-install-skills)
    - [6. Run Matt Pocock Setup](#6-run-matt-pocock-setup)
    - [7. Verify Environment](#7-verify-environment)
  - [Maintenance](#maintenance)
  - [Files in This Repo](#files-in-this-repo)
- [简体中文](#简体中文)
  - [这是什么](#这是什么)
  - [前置条件](#前置条件)
  - [AI 自主配置步骤](#ai-自主配置步骤)
    - [1. 安装 OpenCode CLI](#1-安装-opencode-cli)
    - [2. 配置 opencode.json](#2-配置-opencodejson)
    - [3. 配置 AGENTS.md](#3-配置-agentsmd)
    - [4. 配置 oh-my-opencode-slim.json](#4-配置-oh-my-opencode-slimjson)
    - [5. 安装 Skills](#5-安装-skills)
    - [6. 运行 Matt Pocock 初始化](#6-运行-matt-pocock-初始化)
    - [7. 验证环境](#7-验证环境)
  - [日常维护](#日常维护)
  - [仓库文件说明](#仓库文件说明)

---

## English

### What This Is

This repository contains setup guides and configuration templates for an **OpenCode AI coding assistant** development environment. Follow the steps below to configure:

- **opencode.json** — Core OpenCode configuration (model, MCP, plugins), create at `~/.config/opencode/`
- **oh-my-opencode-slim.json** — Multi-agent orchestration (7 specialized agents), create at `~/.config/opencode/`
- **AGENTS.md** — Behavioral conventions and tooling rules, create at `~/.config/opencode/`
- **Skills** — Domain-specific AI capabilities (Vue, TDD, UI design, web search, etc.), install via commands below
- **MCP services** — Browser debugging, web search, code search, documentation lookup (2 local + 3 auto-injected by OMO-Slim)

**Primary model:** `deepseek/deepseek-v4-pro`  
**Small model:** `deepseek/deepseek-v4-flash`

### Prerequisites

Ensure the following are installed **before** proceeding:

| Tool | Purpose | Install Command |
|------|---------|----------------|
| **Git** | Version control | `winget install Git.Git` (Windows) / `brew install git` (macOS) / `apt install git` (Linux) |
| **nvm** | Node.js version manager | [nvm-sh/nvm](https://github.com/nvm-sh/nvm#installing-and-updating) |
| **Node.js** (LTS) | Runtime | `nvm install --lts && nvm use --lts` |
| **pnpm** | Node package manager | `npm install -g pnpm`（仅首次安装时使用 npm，此后严格使用 pnpm） |
| **uv** | Python package manager | [astral.sh/uv](https://docs.astral.sh/uv/getting-started/installation/) |
| **bash** | Shell (required by OpenCode) | Pre-installed on macOS/Linux; on Windows use Git Bash or WSL |

### Step-by-Step Setup for AI Agents

This section provides exact instructions for AI agents to configure OpenCode autonomously.

---

#### 1. Install OpenCode CLI

```bash
pnpm add -g opencode@latest
```

---

#### 2. Configure opencode.json

Create `~/.config/opencode/opencode.json` with the following content:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": { "disable": true },
    "explore": { "disable": true },
    "general": { "disable": true },
    "plan": { "disable": true }
  },
  "disabled_providers": [],
  "lsp": true,
  "model": "deepseek/deepseek-v4-pro",
  "small_model": "deepseek/deepseek-v4-flash",
  "shell": "bash",
  "plugin": [
    "oh-my-opencode-slim@latest"
  ],
  "mcp": {
    "chrome-devtools": {
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true,
      "type": "local"
    },
    "puppeteer": {
      "command": [
        "npx", "-y", "chrome-devtools-mcp",
        "--headless",
        "--executablePath",
        "REPLACE_WITH_YOUR_BROWSER_PATH"
      ],
      "enabled": true,
      "type": "local"
    }
  },
  "provider": {
    "agnes": {
      "name": "Agnes",
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "https://apihub.agnes-ai.com/v1"
      },
      "models": {
        "agnes-2.0-flash": { "name": "agnes-2.0-flash" }
      }
    }
  }
}
```

> **Platform adaptation:**
> - On **macOS/Linux**, change puppeteer `executablePath` to your Chrome/Chromium path (e.g., `/usr/bin/google-chrome`).
> - On **macOS**, you can set `executablePath` to `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`.
> - The `$schema` line is optional but recommended for IDE autocompletion.

---

#### 3. Configure AGENTS.md

**Global config** — Create `~/.config/opencode/AGENTS.md`:

```markdown
# AGENTS.md — opencode Global Config

## Communication

- Always use **Chinese (简体中文)** to communicate with the user. The user is not fluent in English.
- Keep responses concise. The user prefers a Chinese-language environment.
- **For anything uncertain or questionable, ask the user first before acting. Do not make trade-off decisions on the user's behalf.**

## Package Managers

### Node.js
- Use **nvm** to manage Node versions.
- Use **pnpm** and **pnpm dlx** only. Do NOT use npm, yarn, or npx.

### Python
- Use **uv** to manage Python environments and packages. Do NOT use pip, pipenv, poetry, or venv.

## Project Conventions

- Read existing code before modifying; follow the existing code style.
- Do NOT create README or documentation files unless explicitly asked.
- Do NOT commit code unless explicitly asked.
- Run lint / typecheck after modifications to verify.
```

**Project-level config** — Create `AGENTS.md` in any project root:

```markdown
# AGENTS.md — opencode Project Config

## Communication

- Always use **Chinese (简体中文)** to communicate with the user. The user is not fluent in English.
- Keep responses concise. The user prefers a Chinese-language environment.

## Package Managers

### Node.js
- Use **nvm** to manage Node versions.
- Use **pnpm** and **pnpm dlx** only. Do NOT use npm, yarn, or npx.

### Python
- Use **uv** to manage Python environments and packages. Do NOT use pip, pipenv, poetry, or venv.

## Project Conventions

- Read existing code before modifying; follow the existing code style.
- Do NOT create README or documentation files unless explicitly asked.
- Do NOT commit code unless explicitly asked.
- Run lint / typecheck after modifications to verify.
```

---

#### 4. Configure oh-my-opencode-slim.json

Create `~/.config/opencode/oh-my-opencode-slim.json`:

```json
{
  "$schema": "https://unpkg.com/oh-my-opencode-slim@latest/oh-my-opencode-slim.schema.json",
  "disabled_agents": ["council"],
  "preset": "my-preset",
  "presets": {
    "my-preset": {
      "designer": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "high"
      },
      "explorer": {
        "model": "opencode/deepseek-v4-flash-free",
        "variant": "low"
      },
      "fixer": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "medium"
      },
      "librarian": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "low",
        "mcps": ["websearch", "context7", "gh_grep"]
      },
      "observer": {
        "model": "opencode/mimo-v2.5-free",
        "variant": "low"
      },
      "oracle": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "max",
        "skills": ["simplify", "code-review-expert"]
      },
      "orchestrator": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "medium",
        "mcps": ["*", "!context7"],
        "skills": ["*"]
      }
    }
  }
}
```

> This defines **7 specialized agents** with distinct models, MCP access, and skill sets.  
> The orchestrator has access to all MCPs and skills; the librarian handles web/lookup tasks.

---

#### 5. Install Skills

All skills are installed under `~/.agents/skills/`. Run the following commands:

**5a. Matt Pocock Engineering Skills (15 skills):**

```bash
npx skills@latest add mattpocock/skills \
  --skill code-review \
  --skill codebase-design \
  --skill diagnosing-bugs \
  --skill domain-modeling \
  --skill grill-me \
  --skill grill-with-docs \
  --skill handoff \
  --skill improve-codebase-architecture \
  --skill prototype \
  --skill setup-matt-pocock-skills \
  --skill teach \
  --skill to-issues \
  --skill to-prd \
  --skill triage \
  --skill zoom-out \
  -y
```

**5b. Vue Ecosystem (2 skills):**

```bash
npx skills add antfu/skills --skill vue -y
npx skills add hyf0/vue-skills --skill vue-best-practices -y
```

**5c. Frontend Design (2 skills):**

```bash
npx skills add Leonxlnx/taste-skill --skill design-taste-frontend -y
npx skills add Leonxlnx/taste-skill --skill redesign-existing-projects -y
```

**5d. Agent-Reach (Web Search):**

```bash
npx skills add Panniantong/agent-reach -y
```

**5e. General Purpose Skills (11 skills):**

```bash
npx skills add --skill caveman -y
npx skills add --skill code-review-expert -y
npx skills add --skill defuddle -y
npx skills add --skill edit-article -y
npx skills add --skill find-skills -y
npx skills add --skill obsidian-vault -y
npx skills add --skill setup-pre-commit -y
npx skills add --skill sigma -y
npx skills add --skill skill-forge -y
npx skills add --skill wiki-ingest -y
npx skills add --skill write-a-skill -y
```

**5f. OMO-Slim Built-in Skills (8 skills, auto-installed):**

These are automatically installed with the `oh-my-opencode-slim` plugin and require no manual steps:
`clonedeps`, `codemap`, `deepwork`, `oh-my-opencode-slim`, `reflect`, `simplify`, `verification-planning`, `worktrees`



---

#### 6. Run Matt Pocock Setup

Inside an OpenCode session, run the following command in each project root:

```
/setup-matt-pocock-skills
```

This configures per-project issue tracker, triage labels, and domain documentation.

---

#### 7. Verify Environment

After restarting OpenCode, run these checks:

```bash
# Check skill count
ls ~/.agents/skills/ | wc -l

```

Inside an OpenCode session, verify interactively:

| Check | OpenCode Prompt | Expected Result |
|-------|----------------|-----------------|
| Model | "当前使用什么模型？" | `deepseek/deepseek-v4-pro` |
| Plugin | "列出已安装的插件" | `oh-my-opencode-slim` |
| MCP | "列出可用的 MCP 工具" | 5 MCPs (2 local + 3 auto-injected): chrome-devtools, puppeteer, websearch, context7, gh_grep |
| Multi-agent | "@explorer 搜索一下 README" | Explorer agent responds with search results |

---

### Maintenance

```bash
# Update all skills
npx skills update

# Update OpenCode
pnpm update -g opencode

# Health check for Agent-Reach
agent-reach doctor

# List installed skills
npx skills list
```

---

### Files in This Repo

| File | Description |
|------|-------------|
| `RESTORE.md` | Environment restoration guide (human-oriented version) |
| `README.md` | This file — AI-oriented setup guide |

---

## 简体中文

### 这是什么

本仓库提供 **OpenCode AI 编程助手** 的配置指南和模板。按以下步骤配置：

- **opencode.json** — OpenCode 核心配置（模型、MCP、插件），创建于 `~/.config/opencode/`
- **oh-my-opencode-slim.json** — 多智能体编排（7 个专职 Agent），创建于 `~/.config/opencode/`
- **AGENTS.md** — 行为规范和工具使用规则，创建于 `~/.config/opencode/`
- **Skills** — 领域专属 AI 能力（Vue、TDD、UI 设计、联网搜索等），按下方命令安装
- **MCP 服务** — 浏览器调试、网页搜索、代码搜索、文档查询（2 个本地配置 + 3 个 OMO-Slim 自动注入）

**主模型：** `deepseek/deepseek-v4-pro`  
**小模型：** `deepseek/deepseek-v4-flash`

### 前置条件

开始之前，确保以下工具已安装：

| 工具 | 用途 | 安装命令 |
|------|------|----------|
| **Git** | 版本控制 | `winget install Git.Git` (Windows) / `brew install git` (macOS) / `apt install git` (Linux) |
| **nvm** | Node 版本管理 | [nvm-sh/nvm](https://github.com/nvm-sh/nvm) |
| **Node.js** (LTS) | 运行时 | `nvm install --lts && nvm use --lts` |
| **pnpm** | Node 包管理器 | `npm install -g pnpm`（仅首次安装时使用 npm，此后严格使用 pnpm） |
| **uv** | Python 包管理器 | [astral.sh/uv](https://docs.astral.sh/uv/getting-started/installation/) |
| **bash** | Shell（OpenCode 要求） | macOS/Linux 自带；Windows 使用 Git Bash 或 WSL |

### AI 自主配置步骤

以下是为 AI 提供的精确配置步骤，可自主完成 OpenCode 环境搭建。

---

#### 1. 安装 OpenCode CLI

```bash
pnpm add -g opencode@latest
```

---

#### 2. 配置 opencode.json

创建 `~/.config/opencode/opencode.json`，内容如下：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": { "disable": true },
    "explore": { "disable": true },
    "general": { "disable": true },
    "plan": { "disable": true }
  },
  "disabled_providers": [],
  "lsp": true,
  "model": "deepseek/deepseek-v4-pro",
  "small_model": "deepseek/deepseek-v4-flash",
  "shell": "bash",
  "plugin": [
    "oh-my-opencode-slim@latest"
  ],
  "mcp": {
    "chrome-devtools": {
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true,
      "type": "local"
    },
    "puppeteer": {
      "command": [
        "npx", "-y", "chrome-devtools-mcp",
        "--headless",
        "--executablePath",
        "REPLACE_WITH_YOUR_BROWSER_PATH"
      ],
      "enabled": true,
      "type": "local"
    }
  },
  "provider": {
    "agnes": {
      "name": "Agnes",
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "https://apihub.agnes-ai.com/v1"
      },
      "models": {
        "agnes-2.0-flash": { "name": "agnes-2.0-flash" }
      }
    }
  }
}
```

> **平台适配：**
> - **macOS/Linux** 上，需将 puppeteer 的 `executablePath` 改为 Chrome/Chromium 路径（如 `/usr/bin/google-chrome`）。
> - **macOS** 常见路径：`/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`。
> - `$schema` 行可选，保留用于 IDE 自动补全。

---

#### 3. 配置 AGENTS.md

**全局配置** — 创建 `~/.config/opencode/AGENTS.md`：

```markdown
# AGENTS.md — opencode 全局配置

## 交流规范

- 始终使用**中文**与用户交流。
- 回答简洁，避免冗长解释。用户偏好中文指令环境。
- **不确定或有疑问的操作必须先询问用户，确认后再执行。禁止自行替用户做取舍决策。**

## 包管理器

### Node.js
- 使用 **nvm** 管理 Node 版本。
- 仅使用 **pnpm** 和 **pnpm dlx**。禁止使用 npm、yarn。

### Python
- 仅使用 **uv** 管理 Python 环境和包。禁止使用 pip、pipenv、poetry、venv。

## 项目约定

- 修改代码时，先阅读上下文，遵循已有代码风格。
- 不主动创建 README、文档等文件，除非用户明确要求。
- 不主动提交代码，除非用户明确指示。
- 修改完成后运行 lint / typecheck 命令验证。
```

**项目级配置** — 在任意项目根目录创建 `AGENTS.md`：

```markdown
# AGENTS.md — opencode 项目级配置

## 交流规范

- 始终使用**中文**与用户交流。用户英语不流利。
- 回答简洁，避免冗长解释。用户偏好中文指令环境。

## 包管理器

### Node.js
- 使用 **nvm** 管理 Node 版本。
- 仅使用 **pnpm** 和 **pnpm dlx**。禁止使用 npm、yarn、npx。

### Python
- 仅使用 **uv** 管理 Python 环境和包。禁止使用 pip、pipenv、poetry、venv。

## 项目约定

- 修改代码时，先阅读上下文，遵循已有代码风格。
- 不主动创建 README、文档等文件，除非用户明确要求。
- 不主动提交代码，除非用户明确指示。
- 修改完成后运行 lint / typecheck 命令验证。
```

---

#### 4. 配置 oh-my-opencode-slim.json

创建 `~/.config/opencode/oh-my-opencode-slim.json`：

```json
{
  "$schema": "https://unpkg.com/oh-my-opencode-slim@latest/oh-my-opencode-slim.schema.json",
  "disabled_agents": ["council"],
  "preset": "my-preset",
  "presets": {
    "my-preset": {
      "designer": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "high"
      },
      "explorer": {
        "model": "opencode/deepseek-v4-flash-free",
        "variant": "low"
      },
      "fixer": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "medium"
      },
      "librarian": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "low",
        "mcps": ["websearch", "context7", "gh_grep"]
      },
      "observer": {
        "model": "opencode/mimo-v2.5-free",
        "variant": "low"
      },
      "oracle": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "max",
        "skills": ["simplify", "code-review-expert"]
      },
      "orchestrator": {
        "model": "deepseek/deepseek-v4-pro",
        "variant": "medium",
        "mcps": ["*", "!context7"],
        "skills": ["*"]
      }
    }
  }
}
```

> 定义了 **7 个专职 Agent**，各自使用不同的模型、MCP 权限和技能包。  
> 编排器 (orchestrator) 拥有全部 MCP 和技能权限；知识库 (librarian) 负责联网查询任务。

---

#### 5. 安装 Skills

所有 Skills 安装在 `~/.agents/skills/` 目录下。依次执行：

**5a. Matt Pocock 工程技能（15 个）：**

```bash
npx skills@latest add mattpocock/skills \
  --skill code-review \
  --skill codebase-design \
  --skill diagnosing-bugs \
  --skill domain-modeling \
  --skill grill-me \
  --skill grill-with-docs \
  --skill handoff \
  --skill improve-codebase-architecture \
  --skill prototype \
  --skill setup-matt-pocock-skills \
  --skill teach \
  --skill to-issues \
  --skill to-prd \
  --skill triage \
  --skill zoom-out \
  -y
```

**5b. Vue 生态（2 个）：**

```bash
npx skills add antfu/skills --skill vue -y
npx skills add hyf0/vue-skills --skill vue-best-practices -y
```

**5c. 前端设计（2 个）：**

```bash
npx skills add Leonxlnx/taste-skill --skill design-taste-frontend -y
npx skills add Leonxlnx/taste-skill --skill redesign-existing-projects -y
```

**5d. Agent-Reach 联网搜索：**

```bash
npx skills add Panniantong/agent-reach -y
```

**5e. 其他通用技能（11 个）：**

```bash
npx skills add --skill caveman -y
npx skills add --skill code-review-expert -y
npx skills add --skill defuddle -y
npx skills add --skill edit-article -y
npx skills add --skill find-skills -y
npx skills add --skill obsidian-vault -y
npx skills add --skill setup-pre-commit -y
npx skills add --skill sigma -y
npx skills add --skill skill-forge -y
npx skills add --skill wiki-ingest -y
npx skills add --skill write-a-skill -y
```

**5f. OMO-Slim 自带技能（8 个，自动安装）：**

以下技能随 `oh-my-opencode-slim` 插件自动安装，无需手动操作：
`clonedeps`、`codemap`、`deepwork`、`oh-my-opencode-slim`、`reflect`、`simplify`、`verification-planning`、`worktrees`



---

#### 6. 运行 Matt Pocock 初始化

在每个项目根目录的 OpenCode 会话中运行：

```
/setup-matt-pocock-skills
```

此操作为项目配置 issue tracker、triage labels 和领域文档。

---

#### 7. 验证环境

重启 OpenCode 后，执行以下检查：

```bash
# 检查技能数量
ls ~/.agents/skills/ | wc -l

```

在 OpenCode 会话中交互验证：

| 检查项 | OpenCode 提示词 | 期望结果 |
|--------|----------------|----------|
| 模型 | "当前使用什么模型？" | `deepseek/deepseek-v4-pro` |
| 插件 | "列出已安装的插件" | `oh-my-opencode-slim` |
| MCP | "列出可用的 MCP 工具" | 5 个 MCP（2 本地 + 3 自动注入）：chrome-devtools、puppeteer、websearch、context7、gh_grep |
| 多智能体 | "@explorer 搜索一下 README" | Explorer agent 返回搜索结果 |

---

### 日常维护

```bash
# 更新所有技能
npx skills update

# 更新 OpenCode
pnpm update -g opencode

# Agent-Reach 健康检查
agent-reach doctor

# 列出已安装技能
npx skills list
```

---

### 仓库文件说明

| 文件 | 说明 |
|------|------|
| `RESTORE.md` | 环境还原指南（面向人类） |
| `README.md` | 本文件 — 面向 AI 的配置指南 |
