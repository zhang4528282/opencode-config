# OpenCode 环境还原指南

> 在一台新设备上，按本文档逐步执行，即可完整还原当前 OpenCode 开发环境。

---

## 一、前置条件

### 1.1 系统要求

- Windows 10+ / macOS / Linux
- Git 已安装
- **bash** 作为 shell（OpenCode 配置了 `"shell": "bash"`）

### 1.2 安装 nvm（Node 版本管理）

```bash
# Windows 系统
winget install CoreyButler.NVMforWindows

# macOS / Linux 系统
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

安装后安装 Node.js（建议 LTS）：

```bash
nvm install --lts
nvm use --lts
```

### 1.3 安装 pnpm

```bash
npm install -g pnpm
```

### 1.4 安装 uv（Python 包管理）

```bash
# Windows 系统
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# macOS / Linux 系统
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## 二、克隆配置仓库

```bash
git clone https://github.com/zhang4528282/opencode-config.git ~/opencode-config
```

仓库中包含：
- `RESTORE.md` — 本文档
- `README.md` — 面向 AI 的配置指南

> 本地项目目录 `D:\ai-code` 对应此仓库，你可以 `git clone` 到任意路径后继续操作。

---

## 三、安装 OpenCode

```bash
# 全局安装 opencode CLI
pnpm add -g opencode@latest
```

---

## 四、配置 opencode.json

创建 `~/.config/opencode/opencode.json`：

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

> ⚠️ **Windows 注意**：`puppeteer` MCP 的 `executablePath` 需改为当前设备的 Edge/Chrome 路径。

> **说明**：以上 `opencode.json` 仅定义了 2 个本地 MCP。实际运行时还有 3 个 MCP（`websearch`、`context7`、`gh_grep`）由 OMO-Slim 插件自动注入，无需手动配置。总计加载 **5 个 MCP**。

---

## 五、配置 AGENTS.md

### 5.1 全局配置

创建 `~/.config/opencode/AGENTS.md`：

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

### 5.2 项目级配置

在项目根目录创建 `AGENTS.md`：

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

## 六、安装插件

```bash
# OMO-Slim（多智能体编排引擎）会自动注入 opencode.json 并生成 oh-my-opencode-slim.json
# 上一步已通过 opencode.json 的 plugin 字段配置，首次启动 opencode 后自动安装
```

---

## 七、配置 oh-my-opencode-slim.json

在首次启动 opencode 后，创建 `~/.config/opencode/oh-my-opencode-slim.json`：

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

> ⚠️ 首次启动 opencode 时，OMO-Slim 安装器会交互式配置。上述配置可在安装完成后手动替换。
> 模型名称可能因提供商而异，按实际可用模型调整。

---

## 八、安装 Skills

所有 skills 安装在 `~/.agents/skills/` 统一目录下。

### 8.1 Matt Pocock Skills（一次性安装全部）
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

### 8.2 Vue 相关
```bash
# Vue 3 核心生态（Anthony Fu）
npx skills add antfu/skills --skill vue -y

# Vue 最佳实践（hyf0）
npx skills add hyf0/vue-skills --skill vue-best-practices -y
```

### 8.3 前端设计
```bash
# Taste Skill（前端审美）
npx skills add Leonxlnx/taste-skill --skill design-taste-frontend -y
npx skills add Leonxlnx/taste-skill --skill redesign-existing-projects -y
```

### 8.4 Agent-Reach（联网搜索）
```bash
npx skills add Panniantong/agent-reach -y
```

### 8.5 其他通用 Skills
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

### 8.6 OMO-Slim 自带 Skills（插件安装时自动注入）
以下 skills 随 OMO-Slim 插件自动安装，无需手动操作：
- `clonedeps`
- `codemap`
- `deepwork`
- `oh-my-opencode-slim`
- `reflect`
- `simplify`
- `verification-planning`
- `worktrees`

---

## 九、运行 setup-matt-pocock-skills

在每个项目根目录执行一次，配置 issue tracker、triage labels 和 domain docs：

```bash
# 在 opencode 会话中运行
/setup-matt-pocock-skills
```

---

## 十、验证环境

重启 opencode 后，执行以下检查：

### 10.1 检查插件
在 opencode 中输入：
```
列出已安装的插件
```
应看到 `oh-my-opencode-slim`。

### 10.2 检查 Skills
```bash
ls ~/.agents/skills/ | wc -l
```
应看到已安装的 skills 目录列表。

### 10.3 检查 MCP
在 opencode 中输入：
```
列出可用的 MCP 工具
```
应看到 `chrome-devtools`、`puppeteer`、`websearch`、`context7`、`gh_grep` 共 **5 个 MCP** 的相关工具。

### 10.4 检查模型
在 opencode 中输入：
```
当前使用什么模型？
```
应输出 `deepseek/deepseek-v4-pro`。

### 10.5 多智能体验证
在 opencode 中输入：
```
@explorer 在项目里搜索一下 README 文件
```
应看到 explorer agent 的查询结果。

---

## 十一、快速还原脚本（一键）

将上述步骤汇总为脚本：

```bash
#!/bin/bash
set -e

echo "=== 1. 安装 pnpm 全局包 ==="
pnpm add -g opencode@latest

echo "=== 2. 创建 opencode.json ==="
mkdir -p ~/.config/opencode
cat > ~/.config/opencode/opencode.json << 'JSONEOF'
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "build": { "disable": true },
    "explore": { "disable": true },
    "general": { "disable": true },
    "plan": { "disable": true }
  },
  "lsp": true,
  "model": "deepseek/deepseek-v4-pro",
  "small_model": "deepseek/deepseek-v4-flash",
  "shell": "bash",
  "plugin": ["oh-my-opencode-slim@latest"],
  "mcp": {
    "chrome-devtools": {
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true,
      "type": "local"
    },
    "puppeteer": {
      "command": ["npx", "-y", "chrome-devtools-mcp", "--headless",
        "--executablePath", "REPLACE_WITH_YOUR_BROWSER_PATH"],
      "enabled": true,
      "type": "local"
    }
  }
}
JSONEOF

echo "=== 3. 创建 AGENTS.md ==="
cp ~/.config/opencode/AGENTS.md ~/.config/opencode/AGENTS.md.bak 2>/dev/null || true
# （手动创建或从 Git 仓库拉取）

echo "=== 4. 安装 Matt Pocock 工程技能 ==="
npx skills@latest add mattpocock/skills \
  --skill code-review --skill codebase-design --skill diagnosing-bugs \
  --skill domain-modeling --skill grill-me --skill grill-with-docs \
  --skill handoff --skill improve-codebase-architecture --skill prototype \
  --skill setup-matt-pocock-skills --skill teach --skill to-issues \
  --skill to-prd --skill triage --skill zoom-out -y

echo "=== 5. 安装 Vue 技能 ==="
npx skills add antfu/skills --skill vue -y
npx skills add hyf0/vue-skills --skill vue-best-practices -y

echo "=== 6. 安装前端设计技能 ==="
npx skills add Leonxlnx/taste-skill --skill design-taste-frontend -y
npx skills add Leonxlnx/taste-skill --skill redesign-existing-projects -y

echo "=== 7. 安装 Agent-Reach 联网搜索 ==="
npx skills add Panniantong/agent-reach -y

echo "=== 8. 安装其他通用技能 ==="
for skill in caveman code-review-expert defuddle edit-article find-skills \
  obsidian-vault setup-pre-commit sigma skill-forge wiki-ingest write-a-skill; do
  npx skills add --skill "$skill" -y || echo "  [跳过] $skill"
done

echo ""
echo "=== 还原完成！==="
echo "请重启 opencode，然后运行 /setup-matt-pocock-skills"
echo "并手动创建 oh-my-opencode-slim.json（见文档第七章）"
```

保存为 `restore.sh`，在新设备上执行：

```bash
chmod +x restore.sh
./restore.sh
```

---

## 十二、维护

### 更新所有 Skills
```bash
npx skills update
```

### 更新 OMO-Slim
```bash
# OMO-Slim 随 opencode 启动自动更新
# 手动强制更新：
pnpm update -g opencode
```

### 检查当前环境
在 opencode 中运行：
```
列出所有已安装的 skills
列出已启用的 MCP 服务
```

---

> **最后更新：** 2026-07-24
> **插件：** oh-my-opencode-slim
> **MCP：** chrome-devtools, puppeteer, websearch, context7, gh_grep（共 5 个：2 本地配置 + 3 自动注入）
> **主模型：** deepseek/deepseek-v4-pro
