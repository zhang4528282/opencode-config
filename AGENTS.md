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
