# GSD (Get Shit Done) 完全使用指南

GSD 是一款轻量级、强大的元提示、上下文工程和规范驱动的开发系统，专为 AI 代码助手设计。它支持 Claude Code、OpenCode、Gemini CLI、Kilo、Codex、Copilot、Cursor、Windsurf、Antigravity、Augment、Trae、Qwen Code、Cline 和 CodeBuddy 等多种工具。

GitHub：https://github.com/gsd-build/get-shit-done

> 相关阅读：[[Agent开发全景指南]]、[[从OpenClaw认识Agent]]

---

## 简介

GSD 的核心目标是解决上下文腐烂问题——AI 在填充上下文窗口时发生的质量退化。通过结构化的文档管理和规范化的工作流程，GSD 确保 AI 始终保持清晰的项目理解和一致的开发方向。

核心特性包括：上下文工程（通过分层文档管理项目记忆）、XML 提示格式化（优化 AI 理解的结构化任务描述）、质量门控（四种规范门控确保质量一致性）、波浪执行（按依赖关系分组并行执行计划）、跨会话记忆（STATE.md 保持决策和障碍追踪）。

---

## 安装

### 标准安装

```bash
npx get-shit-done-cc@latest
```

安装程序会提示选择运行时（Claude Code、OpenCode、Gemini、Kilo、Codex、Copilot、Cursor、Windsurf 等）和位置（全局或本地）。

### 验证安装

| 运行时 | 验证命令 |
| --- | --- |
| Claude Code / Gemini / Copilot | `/gsd-help` |
| OpenCode / Kilo / Augment | `/gsd-help` |
| Codex | `$gsd-help` |
| Cline | 检查 `.clinerules` 文件 |

### 非交互式安装（Docker、CI、脚本）

```bash
# Claude Code
npx get-shit-done-cc --claude --global  # 全局安装
npx get-shit-done-cc --claude --local   # 本地安装

# 其他运行时
npx get-shit-done-cc --opencode --global
npx get-shit-done-cc --gemini --global
npx get-shit-done-cc --codex --global
npx get-shit-done-cc --copilot --global
npx get-shit-done-cc --cursor --global
npx get-shit-done-cc --windsurf --global

# 安装到所有运行时
npx get-shit-done-cc --all --global
```

### 开发安装

```bash
git clone https://github.com/gsd-build/get-shit-done.git
cd get-shit-done
npm run build:hooks
node bin/install.js --claude --local
```

### 推荐：跳过权限模式

GSD 设计用于无摩擦自动化，推荐使用：

```bash
claude --dangerously-skip-permissions
```

或在 `.claude/settings.json` 配置精细权限：

```json
{
  "permissions": {
    "allow": [
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git status:*)",
      "Bash(git log:*)",
      "Bash(git diff:*)",
      "Bash(git tag:*)"
    ]
  }
}
```

---

## 保持更新

GSD 演进快速，定期更新：

```bash
npx get-shit-done-cc@latest
```

或使用 GSD 内置更新命令：

```
/gsd-update
```

---

## 核心工作流程

```
/gsd-new-project → /gsd-plan-phase → /gsd-execute-phase → repeat
```

六步标准流程：初始化项目、讨论阶段愿景、创建详细执行计划、执行阶段计划、验证构建的功能、创建 PR 并提交。

自动流程：使用 `/gsd-next` 可自动检测并运行下一步。

| 步骤 | 命令 | 说明 |
| --- | --- | --- |
| 1 | `/gsd-new-project` | 初始化项目（研究 → 需求 → 路线图） |
| 2 | `/gsd-discuss-phase <n>` | 讨论阶段愿景 |
| 3 | `/gsd-plan-phase <n>` | 创建详细执行计划 |
| 4 | `/gsd-execute-phase <n>` | 执行阶段计划 |
| 5 | `/gsd-verify-work <n>` | 验证构建的功能 |
| 6 | `/gsd-ship <n>` | 创建 PR 并提交 |

---

## 文件结构

```
.planning/
├── PROJECT.md            # 项目愿景（始终加载）
├── ROADMAP.md            # 当前阶段分解
├── STATE.md              # 项目记忆 & 上下文（跨会话）
├── RETROSPECTIVE.md      # 活动回顾（每个里程碑更新）
├── config.json           # 工作流模式 & 门控配置
├── todos/                # 捕获的想法和任务
│   ├── pending/          # 待处理的待办事项
│   └── done/             # 完成的待办事项
├── debug/                # 活动调试会话
│   └── resolved/         # 已归档的已解决问题
├── milestones/           # 里程碑归档
│   ├── v1.0-ROADMAP.md   # 归档的路线图快照
│   ├── v1.0-REQUIREMENTS.md # 归档的需求
│   └── v1.0-phases/      # 归档的阶段目录
├── codebase/             # 代码库映射（棕地项目）
│   ├── STACK.md          # 语言、框架、依赖
│   ├── ARCHITECTURE.md   # 模式、层级、数据流
│   ├── STRUCTURE.md      # 目录布局、关键文件
│   ├── CONVENTIONS.md    # 编码标准、命名
│   ├── TESTING.md        # 测试设置、模式
│   ├── INTEGRATIONS.md   # 外部服务、API
│   └── CONCERNS.md       # 技术债务、已知问题
├── research/             # 生态系统知识
└── phases/               # 当前阶段
    ├── 01-foundation/
    │   ├── 01-01-PLAN.md
    │   └── 01-01-SUMMARY.md
    └── 02-core-features/
        ├── 02-01-PLAN.md
        └── 02-01-SUMMARY.md
```

关键文档说明：PROJECT.md 是项目愿景，始终加载到上下文；STATE.md 是决策、障碍、位置——跨会话记忆；PLAN.md 是原子任务，XML 结构，验证步骤；SUMMARY.md 是发生的事、变更内容、提交历史；RESEARCH.md 是生态系统知识（栈、架构、陷阱）。

| 文档 | 作用 |
| --- | --- |
| PROJECT.md | 项目愿景，始终加载到上下文 |
| STATE.md | 决策、障碍、位置——跨会话记忆 |
| PLAN.md | 原子任务，XML 结构，验证步骤 |
| SUMMARY.md | 发生的事、变更内容、提交历史 |
| RESEARCH.md | 生态系统知识（栈、架构、陷阱） |

---

## 命令详解

### 项目初始化

#### `/gsd-new-project`

从想法到可规划状态的统一流程：深度提问理解你要构建的内容；可选领域研究（并行启动四个研究 agent）；需求定义（v1/v2/out-of-scope 划分）；路线图创建（阶段分解和成功标准）。

创建所有 `.planning/` 工件：PROJECT.md（愿景和需求）、config.json（工作流模式）、research/（领域研究）、REQUIREMENTS.md（带 REQ-ID 的范围需求）、ROADMAP.md（映射到需求的阶段）、STATE.md（项目记忆）。

#### `/gsd-map-codebase`

针对现有代码库的棕地项目：使用并行 Explore agents 分析代码库；创建 `.planning/codebase/` 的七个聚焦文档；涵盖栈、架构、结构、约定、测试、集成、关注点。在现有代码库上使用 `/gsd-new-project` 前先运行此命令。

---

### 阶段规划

#### `/gsd-discuss-phase <number>`

在规划前帮助表达你对阶段的愿景：捕获你想象这个阶段如何运作；创建 CONTEXT.md 包含愿景、要素和边界；当你对某事物应该如何有想法时使用；`--batch` 选项一次问 2-5 个相关问题。

```
/gsd-discuss-phase 2
/gsd-discuss-phase 2 --batch
/gsd-discuss-phase 2 --batch=3
```

#### `/gsd-research-phase <number>`

针对小众/复杂领域的综合生态系统研究：发现标准栈、架构模式、陷阱；创建 RESEARCH.md 包含"专家如何构建此"；用于 3D、游戏、音频、着色器、ML 等专业领域；超越"选择哪个库"到生态系统知识。

#### `/gsd-list-phase-assumptions <number>`

在开始前查看 Claude 的规划意图：显示 Claude 对阶段的预期方法；如果 Claude 误解了你的愿景，让你纠正方向；无文件创建，仅对话输出。

#### `/gsd-plan-phase <number>`

为特定阶段创建详细执行计划：生成 `.planning/phases/XX-phase-name/XX-YY-PLAN.md`；将阶段分解为具体、可执行的任务；包含验证标准和成功衡量；支持每阶段多个计划（XX-01, XX-02 等）。

PRD 快速路径：`--prd path/to/requirements.md` 跳过讨论阶段，你的 PRD 成为 CONTEXT.md 的锁定决策。

---

### 执行

#### `/gsd-execute-phase <phase-number>`

执行阶段中的所有计划，或运行特定波浪：按波浪分组计划（从 frontmatter），顺序执行波浪；每波浪内的计划通过 Task 工具并行运行；`--wave N` 仅执行波浪 N，除非阶段已完成则停止；所有计划完成后验证阶段目标；更新 REQUIREMENTS.md、ROADMAP.md、STATE.md。

```
/gsd-execute-phase 5
/gsd-execute-phase 5 --wave 2
```

---

### 智能路由

#### `/gsd-do <description>`

自动将自由文本路由到正确的 GSD 命令：分析自然语言输入找到最佳匹配的 GSD 命令；作为调度器，本身不做工作；通过让你在顶级匹配间选择来解决歧义；当你知道想要什么但不知道用哪个 `/gsd-*` 命令时使用。

```
/gsd-do fix the login button
/gsd-do refactor the auth system
/gsd-do I want to start a new milestone
```

---

### 快速模式

#### `/gsd-quick [--full] [--validate] [--discuss] [--research]`

执行小型临时任务，保持 GSD 保证但跳过可选 agents：启动 planner + executor（默认跳过研究、检查、验证）；快速任务在 `.planning/quick/` 中，与规划阶段分开；更新 STATE.md 跟踪（不更新 ROADMAP.md）。

标志启用额外质量步骤：`--full` 完整质量管道（讨论 + 研究 + 计划检查 + 验证）；`--validate` 仅计划检查（最多两次迭代）和执行后验证；`--discuss` 规划前轻量讨论暴露灰色区域；`--research` 规划前专注研究 agent 调查方法。

标志可组合：`--discuss --research --validate` 等同 `--full`。

#### `/gsd-fast [description]`

执行琐碎任务的轻量模式——无子 agents、无规划文件、无开销：用于太小不值得规划的任务（错字修复、配置更改、忘记提交）；在当前上下文运行、做更改、提交、记录到 STATE.md；无 PLAN.md 或 SUMMARY.md 创建；不超过三文件编辑，非平凡任务重定向到 `/gsd-quick`；带规范消息的原子提交。

```
/gsd-fast "fix the typo in README"
/gsd-fast "add .env to gitignore"
```

---

### 路线图管理

#### `/gsd-add-phase <description>`

在当前里程碑末尾添加新阶段：追加到 ROADMAP.md；使用下一个顺序编号；更新阶段目录结构。

```
/gsd-add-phase "Add admin dashboard"
```

#### `/gsd-insert-phase <after> <description>`

在现有阶段间插入紧急工作作为小数阶段：创建中间阶段（如 7 和 8 之间创建 7.1）；用于必须在里程碑中期发现的紧急工作；保持阶段排序。

```
/gsd-insert-phase 7 "Fix critical auth bug"
```

#### `/gsd-remove-phase <number>`

移除未来阶段并重新编号后续阶段：删除阶段目录和所有引用；重新编号所有后续阶段以填补空缺；仅对未来（未开始）阶段有效；Git commit 保留历史记录。

```
/gsd-remove-phase 17
```

---

### 里程碑管理

#### `/gsd-new-milestone <name>`

通过统一流程开始新里程碑：深度提问理解接下来要构建什么；可选领域研究（并行启动四个研究 agents）；需求定义和范围划分；路线图创建和阶段分解；`--reset-phase-numbers` 从 Phase 1 重新开始编号。

镜像 `/gsd-new-project` 流程用于棕地项目。

```
/gsd-new-milestone "v2.0 Features"
/gsd-new-milestone --reset-phase-numbers "v2.0 Features"
```

#### `/gsd-complete-milestone <version>`

归档完成的里程碑并为下一版本准备：创建带统计的 MILESTONES.md 条目；归档完整详情到 milestones/ 目录；为发布创建 git tag；为下一版本准备工作空间。

```
/gsd-complete-milestone 1.0.0
```

---

### 进度追踪

#### `/gsd-progress`

检查项目状态并智能路由到下一步：显示可视进度条和完成百分比；从 SUMMARY 文件总结最近工作；显示当前位置和下一步；列出关键决策和开放问题；提供执行下一计划或创建缺失计划；检测 100% 里程碑完成。

---

### 会话管理

#### `/gsd-resume-work`

从上一会话恢复工作，完整上下文恢复：读取 STATE.md 获取项目上下文；显示当前位置和最近进度；根据项目状态提供下一步。

#### `/gsd-pause-work`

在阶段中期暂停工作时创建上下文交接：创建 .continue-here 文件记录当前状态；更新 STATE.md 会话连续性部分；捕获进行中的工作上下文。

---

### 调试

#### `/gsd-debug [issue description]`

系统化调试，跨上下文重置保持状态：通过适应性提问收集症状；创建 `.planning/debug/[slug].md` 跟踪调查；使用科学方法调查（证据 → 假设 → 测试）；在 `/clear` 后存活——无参数运行 `/gsd-debug` 恢复；归档已解决问题到 `.planning/debug/resolved/`。

```
/gsd-debug "login button doesn't work"
/gsd-debug  # 恢复活动会话
```

---

### 快速笔记

#### `/gsd-note <text>`

零摩擦想法捕获——一条命令、即时保存、无问题：保存带时间戳的笔记到 `.planning/notes/`（或 `$HOME/.claude/notes/` 全局）；三个子命令：append（默认）、list、promote；Promote 将笔记转换为结构化 todo；无项目时也可工作（回退到全局范围）。

```
/gsd-note refactor the hook system
/gsd-note list
/gsd-note promote 3
/gsd-note --global cross-project idea
```

---

### Todo 管理

#### `/gsd-add-todo [description]`

从当前对话捕获想法或任务为 todo：从对话提取上下文（或使用提供的描述）；在 `.planning/todos/pending/` 创建结构化 todo 文件；从文件路径推断区域用于分组；创建前检查重复；更新 STATE.md todo 计数。

```
/gsd-add-todo  # 从对话推断
/gsd-add-todo Add auth token refresh
```

#### `/gsd-check-todos [area]`

列出待处理 todos 并选择一个工作：列出所有待处理 todos，带标题、区域、年龄；可选区域过滤（如 `/gsd-check-todos api`）；为选中的 todo 加载完整上下文；路由到适当操作（现在工作、添加到阶段、头脑风暴）；工作开始时移动 todo 到 done/。

```
/gsd-check-todos
/gsd-check-todos api
```

---

### 用户验收测试

#### `/gsd-verify-work [phase]`

通过对话式 UAT 验证构建的功能：从 SUMMARY.md 文件提取可测试交付物；一次呈现一个测试（yes/no 响应）；自动诊断失败并创建修复计划；如发现问题准备好重新执行。

```
/gsd-verify-work 3
```

---

### 提交工作

#### `/gsd-ship [phase]`

从完成的阶段工作创建 PR，自动生成 body：推送分支到远程；从 SUMMARY.md、VERIFICATION.md、REQUIREMENTS.md 创建 PR 总结；可选请求代码审查；更新 STATE.md 提交状态。

前提：阶段已验证，`gh` CLI 已安装并认证。

```
/gsd-ship 4
/gsd-ship 4 --draft
```

#### `/gsd-review --phase N [--gemini] [--claude] [--codex] [--coderabbit] [--all]`

跨 AI peer review——调用外部 AI CLIs 独立审查阶段计划：检测可用 CLIs（gemini、claude、codex、coderabbit）；每个 CLI 用相同结构化提示独立审查计划；CodeRabbit 审查当前 git diff（不是提示）——可能最多五分钟；产生带每个 reviewer 反馈和共识总结的 REVIEWS.md；反馈回规划：`/gsd-plan-phase N --reviews`。

```
/gsd-review --phase 3 --all
```

#### `/gsd-pr-branch [target]`

通过过滤 .planning/ commits 创建干净的 PR 分支：分类 commits：仅代码（包含）、仅规划（排除）、混合（包含但无 .planning/）；Cherry-pick 代码 commits 到干净分支；Reviewers 只看到代码更改，无 GSD 工件。

```
/gsd-pr-branch
/gsd-pr-branch main
```

#### `/gsd-plant-seed [idea]`

捕获带触发条件的前瞻性想法，自动浮现：Seeds 保存 WHY、WHEN 浮现和相关代码的面包屑；在 `/gsd-new-milestone` 时当触发条件匹配自动浮现；比延迟项更好——触发被检查，不被遗忘。

```
/gsd-plant-seed "add real-time notifications when we build the events system"
```

#### `/gsd-audit-uat`

跨阶段审计所有待处理的 UAT 和验证项：扫描每个阶段的 pending、skipped、blocked、human_needed 项；对比代码库检测过时文档；产生按可测试性分组的优先级人工测试计划；在开始新里程碑前使用以清除验证债务。

---

### 里程碑审计

#### `/gsd-audit-milestone [version]`

对照原始意图审计里程碑完成：读取所有阶段 VERIFICATION.md 文件；检查需求覆盖；启动集成检查器检查跨阶段连接；创建带差距和技术债务的 MILESTONE-AUDIT.md。

#### `/gsd-plan-milestone-gaps`

创建阶段以填补审计发现的差距：读取 MILESTONE-AUDIT.md 并将差距分组为阶段；按需求优先级（must/should/nice）优先排序；添加差距填补阶段到 ROADMAP.md；准备在新阶段上运行 `/gsd-plan-phase`。

---

### 配置

#### `/gsd-settings`

交互式配置工作流开关和模型 profile：切换研究、计划检查、验证 agents；选择模型 profile（quality/balanced/budget/inherit）；更新 `.planning/config.json`。

#### `/gsd-set-profile <profile>`

快速切换 GSD agents 的模型 profile：

| Profile | 说明 |
| --- | --- |
| quality | Opus 全部位置（验证除外） |
| balanced | Opus 规划，Sonnet 执行（默认） |
| budget | Sonnet 写代码，Haiku 研究/验证 |
| inherit | 使用当前会话模型用于所有 agents |

```
/gsd-set-profile budget
```

---

### 工具命令

#### `/gsd-cleanup`

归档已完成里程碑累积的阶段目录：识别已完成里程碑仍在 `.planning/phases/` 的阶段；移动前显示 dry-run 总结；移动阶段 dirs 到 `.planning/milestones/v{X.Y}-phases/`；多个里程碑后使用以减少 `.planning/phases/` 混乱。

#### `/gsd-help`

显示命令参考。

#### `/gsd-update`

更新 GSD 到最新版本，带 changelog 预览：显示已安装 vs 最新版本对比；显示你错过版本的 changelog 条目；高亮破坏性更改；安装前确认；比原始 `npx get-shit-done-cc` 更好。

#### `/gsd-join-discord`

加入 GSD Discord 社区：获取帮助、分享你正在构建的内容、保持更新；与其他 GSD 用户连接。

---

## 工作流模式

在 `/gsd-new-project` 时设置：

### Interactive Mode（交互模式）

每个重大决策确认；在检查点暂停等待批准；全程更多指导。

### YOLO Mode（全自动模式）

自动批准大多数决策；无确认执行计划；仅在关键检查点停止。

可随时编辑 `.planning/config.json` 更改。

---

## 规划配置

在 `.planning/config.json` 中配置：

### `planning.commit_docs`（默认：true）

true：规划工件提交到 git（标准工作流）。false：规划工件仅本地保存，不提交。

当 `commit_docs: false`：添加 `.planning/` 到 `.gitignore`；用于 OSS 贡献、客户项目或保持规划私密；所有规划文件正常工作，只是不在 git 追踪。

### `planning.search_gitignored`（默认：false）

true：在广泛 ripgrep 搜索中添加 `--no-ignore`。仅在 `.planning/` 被 gitignore 且希望项目范围搜索包含它时需要。

```json
{
  "planning": {
    "commit_docs": false,
    "search_gitignored": true
  }
}
```

---

## 常见工作流示例

### 启动新项目

```
/gsd-new-project        # 统一流程：提问 → 研究 → 需求 → 路线图
/clear
/gsd-plan-phase 1       # 为第一阶段创建计划
/clear
/gsd-execute-phase 1    # 执行阶段中的所有计划
```

### 在休息后恢复工作

```
/gsd-progress  # 看你做到哪里并继续
```

### 添加紧急中期里程碑工作

```
/gsd-insert-phase 5 "Critical security fix"
/gsd-plan-phase 5.1
/gsd-execute-phase 5.1
```

### 完成里程碑

```
/gsd-complete-milestone 1.0.0
/clear
/gsd-new-milestone  # 开始下一里程碑
```

### 工作中捕获想法

```
/gsd-add-todo                    # 从对话上下文捕获
/gsd-add-todo Fix modal z-index  # 带明确描述捕获
/gsd-check-todos                 # 检查并工作 todos
/gsd-check-todos api             # 按区域过滤
```

### 调试问题

```
/gsd-debug "form submission fails silently"  # 开始调试会话
# ... 调查发生，上下文填满 ...
/clear
/gsd-debug                                    # 从离开处恢复
```

---

## XML 任务格式示例

每个计划采用结构化 XML 格式，优化用于 AI：

```xml
<task type="auto">
 <name>Create login endpoint</name>
 <files>src/app/api/auth/login/route.ts</files>
 <action>
  Use jose for JWT (not jsonwebtoken - CommonJS issues).
  Validate credentials against users table.
  Return httpOnly cookie on success.
 </action>
 <verify>curl -X POST localhost:3000/api/auth/login returns 200 + Set-Cookie</verify>
 <done>Valid credentials return cookie, invalid return 401</done>
</task>
```

---

## 获取帮助

读取 `.planning/PROJECT.md` 获取项目愿景；读取 `.planning/STATE.md` 获取当前上下文；检查 `.planning/ROADMAP.md` 获取阶段状态；运行 `/gsd-progress` 检查你做到哪里；加入 Discord：`/gsd-join-discord`。

---

## 命令速查表

| 命令 | 功能 |
| --- | --- |
| `/gsd-new-project` | 初始化新项目 |
| `/gsd-map-codebase` | 扫描并索引现有代码库 |
| `/gsd-discuss-phase <n>` | 讨论阶段愿景 |
| `/gsd-plan-phase <n>` | 规划阶段 |
| `/gsd-execute-phase <n>` | 执行阶段 |
| `/gsd-verify-work <n>` | 验证工作 |
| `/gsd-ship <n>` | 从验证工作创建 PR |
| `/gsd-complete-milestone` | 归档里程碑并标记发布 |
| `/gsd-new-milestone` | 开始下一版本 |
| `/gsd-quick` | 快速模式（临时任务） |
| `/gsd-fast` | 轻量模式（琐碎任务） |
| `/gsd-next` | 自动检测并运行下一步 |
| `/gsd-progress` | 检查进度 |
| `/gsd-resume-work` | 恢复上次会话 |
| `/gsd-pause-work` | 暂停并创建交接 |
| `/gsd-debug` | 系统化调试 |
| `/gsd-note` | 快速笔记 |
| `/gsd-add-todo` | 添加 todo |
| `/gsd-check-todos` | 检查 todos |
| `/gsd-do` | 智能路由 |
| `/gsd-settings` | 配置工作流 |
| `/gsd-help` | 显示帮助 |
| `/gsd-update` | 更新 GSD |

---

> 相关阅读：[[Agent开发全景指南]]、[[从OpenClaw认识Agent]]、[[gemini cli注意事项]]
>
> 文档来源：https://github.com/gsd-build/get-shit-done
> 生成日期：2026-04-12