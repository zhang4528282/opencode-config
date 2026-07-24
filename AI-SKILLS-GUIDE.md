# OpenCode Vue 前端开发者 AI 技能与工具指南

> 本文档记录当前 `opencode` 环境中已安装的全部 AI 提效组件，涵盖 Skills（技能包）、MCP（模型上下文协议服务）、联网检索能力及其使用方式。

---

## 一、总览

| 类别 | 名称 | 作用 |
|:---|:---|:---|
| Skills | `antfu/skills@vue` | Vue 3 / Vite / VueUse 核心生态 |
| Skills | `Leonxlnx/taste-skill` | 前端排版审美，拒绝模板化 UI |
| Skills | `mattpocock/skills` | TDD 驱动、科学排障、项目术语库 |
| Skills | `hyf0/vue-skills@vue-best-practices` | Vue 最佳实践、Pinia、性能优化 |
| 联网检索 | Agent-Reach | 网页阅读、Exa 语义搜索、YouTube / B站字幕 |
| MCP | `chrome-devtools-mcp` | 用 Edge 截图、审查 DOM、模拟交互 |

---

## 二、Agent Skills（技能包）

技能包是注入 AI 的「领域知识 + 工作规范」，存放在 `~/.agents/skills/` 目录，OpenCode 会在对应场景自动激活。

---

### 3.1 `antfu/skills@vue` — Vue 3 核心生态

- **安装来源：** Anthony Fu（Vue / Vite 核心团队成员）
- **能力注入：**
  - Vue 3 组合式 API（Composition API）与 `<script setup>` 语法
  - 单文件组件（SFC）最佳实践
  - Vite 配置与构建优化
  - VueUse 工具库
  - Pinia 状态管理
  - UnoCSS 原子化 CSS
- **何时生效：** 当您编写 `.vue` 文件、配置 `vite.config.ts` 或讨论 Vue 相关代码时自动激活
- **典型用法：**
  ```
  帮我用 Composition API 写一个带防抖的搜索输入组件
  这个 Vite 配置怎样优化构建速度？
  ```

---

### 3.2 `Leonxlnx/taste-skill` — 前端审美与质感

- **安装来源：** Taste Skill（Anti-Slop Frontend Framework）
- **能力注入：**
  - 拒绝 AI 生成的「模板化/简陋」UI
  - 精确控制排版、间距（Padding/Margin 节奏）
  - 高级 CSS 动效（GSAP 骨架代码）
  - 多风格支持：极简风（minimalist-ui）、高奢风（high-end-visual-design）、工业风（brutalist-ui）
- **可调参数（1-10 档）：**

  | 参数 | 含义 | 低值 | 高值 |
  |:---|:---|:---|:---|
  | DESIGN_VARIANCE | 布局实验性 | 居中/干净 | 不对称/现代 |
  | MOTION_INTENSITY | 动画深度 | 仅 hover | 滚动/磁吸效果 |
  | VISUAL_DENSITY | 信息密度 | 宽敞留白 | 密集仪表盘 |

- **子技能速查：**

  | 安装名 | 风格 |
  |:---|:---|
  | `design-taste-frontend` | 通用默认（v2） |
  | `minimalist-ui` | Notion/Linear 极简风 |
  | `high-end-visual-design` | 高级温润质感 |
  | `industrial-brutalist-ui` | 硬朗机械风格 |
  | `redesign-existing-projects` | 已有项目 UI 审计整改 |

- **典型用法：**
  ```
  用 minimalist-ui 风格写一个登录页
  调高 MOTION_INTENSITY，给这个导航栏加上磁吸动效
  ```

---

### 3.3 `mattpocock/skills` — 工程规范与 TDD

- **安装来源：** Matt Pocock（知名 TypeScript 专家）
- **能力注入：**

  | 核心技能 | 功能 |
  |:---|:---|
  | `/grill-with-docs` | 代码编写前，AI 烤问你所有需求细节，并自动建立项目术语库 CONTEXT.md |
  | `/tdd` | 严格红 → 绿 → 重构循环，先写测试再写实现 |
  | `/diagnose` | 科学排障：复现 → 缩小范围 → 假设 → 打桩 → 修复 → 回归测试 |
  | `/improve-codebase-architecture` | 揪出烂代码（Ball of Mud），给出模块深化建议 |
  | `/zoom-out` | 让 AI 从全系统角度解释某段代码 |
  | `/grill-me` | 通用版烤问：非代码场景也可用 |

- **典型用法：**
  ```
  /grill-with-docs — 我要给后台管理加一个权限路由
  /tdd — 为这个 Pinia store 写单元测试
  /diagnose — 这个页面为什么打开空白？
  ```

---

### 3.4 `hyf0/vue-skills@vue-best-practices` — Vue 最佳实践

- **能力注入：**
  - Vue 3 项目架构设计模式
  - TypeScript 与 Vue 的类型安全集成
  - Pinia store 设计规范
  - 组件拆分与复用粒度
  - 性能优化策略（懒加载、KeepAlive、虚拟滚动）
- **典型用法：**
  ```
  帮我审查这个 Vuex → Pinia 的迁移方案是否合理
  这个大型表单组件如何拆分成可复用的子组件？
  ```

---

## 四、Agent-Reach — 联网检索能力

### 是什么

为 AI 提供**免费的互联网访问能力**，能搜索网页、读取文档、提取视频字幕，无需 API Key。

### 已激活渠道

| 渠道 | 状态 | 说明 |
|:---|:---:|:---|
| 全网语义搜索（Exa） | ✅ | AI 级语义搜索，比关键词搜索更精准 |
| 任意网页阅读（Jina Reader） | ✅ | 自动清洗 HTML 为纯净 Markdown |
| YouTube 字幕提取 | ✅ | `yt-dlp` 提取视频字幕/元数据 |
| B站搜索 | ✅ | 搜索 B站视频（无需登录） |
| RSS/Atom 订阅源 | ✅ | 解析任意 RSS 源 |
| V2EX | ✅ | 浏览热门帖子、节点 |

### 如何使用

**无需命令行。** 直接在对话中用自然语言指令：

```
搜索 2025 年 Vue 3.5 Suspense 最新用法
帮我读这个 GitHub Issue：https://github.com/vuejs/core/issues/xxx
这个 YouTube 教程讲了什么？https://youtube.com/watch?v=xxx
B站搜一下 Vue 3 入门教程
```

### 诊断命令

```bash
# 查看所有渠道状态
agent-reach doctor
```

---

## 五、MCP — Chrome DevTools 浏览器调试

### 是什么

基于 Google 官方 `chrome-devtools-mcp`，用 **Microsoft Edge**（Chromium 内核）在 headless 模式下提供：

- 页面截图（PNG）
- DOM 树审查与元素定位
- JavaScript 执行与返回值捕获
- 控制台日志读取
- 网络请求拦截与分析
- 页面导航与表单自动填写

### 配置

Edge 路径：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`

### 如何使用

**1. 确保本地 dev server 已启动：**
```bash
pnpm run dev   # 例如 Vite 默认 http://localhost:5173
```

**2. 直接在对话中让 AI 操作浏览器：**
```
打开 http://localhost:5173 并截图
截图中导航栏的文字是不是偏左了？检查一下 CSS
点击 "登录" 按钮，看看有没有报错
用移动端视口（375x812）截图这个页面
```

**3. 典型调试流程：**
```
1. 我改了一下首页的 CSS，帮我截图 localhost:5173 看看效果
2. 导航栏看起来和设计稿不一致，审查一下 .nav 元素的 computed styles
3. 执行 document.querySelector('.nav').offsetHeight 看看实际高度
```

---

## 六、完整实战示例

以一个「用 AI 从零开发一个 Vue 仪表盘页面」为例，展示各组件的协作流程：

```
用户：/grill-with-docs — 我要做一个数据仪表盘页面，
      包含卡片统计、折线图和最近订单表格

AI：【烤问】使用什么图表库？数据从哪个 API 来？
    是否需要暗色模式？……好，写入 CONTEXT.md 和 ADR。

用户：帮我搜索 2026 年 ECharts 5 在 Vue 3 中的最新用法

AI：【Agent-Reach 搜索】找到 ECharts 5 官方文档，确认
    useECharts composable 的正确导入方式。

用户：/tdd — 先为统计卡片组件写测试

AI：【TDD 红绿重构】先写 vitest 用例 → 再实现 Card.vue
    → 测试通过 → 重构为可配置的通用组件

用户：pnpm run dev 启动了，帮我看看卡片在页面上的效果

AI：【chrome-devtools-mcp】启动 Edge，截图 localhost:5173，
    发现卡片间距不均匀，.grid 的 gap 用了绝对值。

用户：用 taste-skill 优化一下整体视觉，降低 VISUAL_DENSITY

AI：【taste-skill】调整为 spacious 风格，增大留白、统一
    圆角半径、给卡片加上微妙的 hover 阴影过渡。
```

---

## 七、常用命令速查

```bash
# 查看已安装的全部技能
npx skills list

# 更新所有技能
npx skills check
npx skills update

# Agent-Reach 健康检查
agent-reach doctor

# Agent-Reach 更新
agent-reach check-update

# 安装可选渠道（Twitter / 小红书 / Reddit 等）
agent-reach install --channels=twitter,xiaohongshu

# 查看 opencode 配置
cat ~/.config/opencode/config.json
```

---

## 八、目录结构参考

```
~/.agents/skills/            # 全局技能包（所有 Agent 共享）
  ├── vue/                   # antfu/skills@vue
  ├── design-taste-frontend/ # taste-skill（默认风格）
  ├── minimalist-ui/         # taste-skill（极简）
  ├── diagnose/              # mattpocock 科学排障
  ├── tdd/                   # mattpocock 测试驱动
  ├── vue-best-practices/    # hyf0 Vue 最佳实践
  ├── agent-reach/           # 联网检索技能索引
  └── ...                    # 更多子技能

~/.config/opencode/
  ├── config.json            # opencode 主配置（含 MCP）
  └── AGENTS.md              # 用户级行为约定
```

---

> **最后更新：** 2026-06-12
