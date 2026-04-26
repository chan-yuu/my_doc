# Gemini CLI 注意事项

这里对 Gemini CLI 的能力和命令设置做一个剖析。

> 相关阅读：[[Agent开发全景指南]]、[[GSD (Get Shit Done) 完全使用指南]]、[[远程 Code CLI 工具指南]]

---

## 模型选择

首先只使用 pro 能力：

```bash
/model set pro --persist
```

模型选项：auto 是默认，会解析到 gemini-2.5-pro 或 gemini-3-pro-preview，如果启用了 preview 功能，优先走 preview。pro 用于复杂推理任务，解析到 gemini-2.5-pro 或 gemini-3-pro-preview。flash 是 gemini-2.5-flash，强调速度与平衡，适合大多数任务。flash-lite 是 gemini-2.5-flash-lite，最快，适合简单任务。

---

## 审批模式

`/plan` 只是其中的一种审批模式。能力如下：

### default

默认审批模式。需要操作时会请求确认，比较稳。官方 settings 文档就是这么定义的。

### auto_edit

自动批准"编辑类工具"，但不是所有操作都无脑放开。适合已经比较信任它改代码，但不想彻底放飞。

### plan

只读规划模式。它会先做分析、给方案，不直接进入修改执行。官方明确把它定义为 read-only mode。

### yolo

自动批准所有动作，风险最高。官方说明它只能通过命令行 `--yolo` 或 `--approval-mode=yolo` 打开。

---

## Gemini CLI 和 Codex CLI 自动审批设置

### Gemini CLI 自动审批设置

**启动时设置（命令行）**

开启完全自动审批（YOLO 模式）：在启动时添加参数 `gemini --yolo` 或 `gemini --approval-mode=yolo`。（注意：开启 YOLO 模式意味着 AI 可以不经确认直接执行修改文件或运行终端命令等操作。出于安全考虑，建议配合沙盒使用，例如设置环境变量 GEMINI_SANDBOX=true 或在 Docker 中运行。）

**进入应用后设置（交互中或配置文件）**

进入 Gemini CLI 后，可以通过 `/settings` 命令来调整，或者直接修改 settings.json 文件。

更改默认审批模式：将 general.defaultApprovalMode 修改为 "auto_edit"。这会自动审批诸如写入文件、替换代码等编辑类操作（但涉及高风险的 Shell 命令可能仍需要确认）。注意：完全的 YOLO 模式只能在启动时通过命令行参数开启。

开启永久审批选项：将 security.enablePermanentToolApproval 设置为 true。这样当工具弹窗让你确认时，会出现"允许所有未来会话 (Allow for all future sessions)"的选项，选中后以后执行相同操作就不会再弹窗了。

信任工作区：如果将 security.autoAddToPolicyByDefault 设置为 true，在受信任的工作区内，低风险操作默认就会被永久允许。

---

## 用量查询

Gemini CLI 的用量是有限的，通过 `/stats` 来查询。然后 `/reset` 来清空上下文，防止大量 token 输入的占用。

---

### Codex CLI 自动审批设置

**启动时设置（命令行）**

全自动模式（推荐）：使用 `codex --full-auto` 启动。它会自动审批绝大部分操作，但同时会默认开启工作区级别的沙盒保护（例如只允许修改当前项目的文件，禁止越权访问）。

极致无限制模式（危险）：使用 `codex --yolo` 或 `codex --dangerously-bypass-approvals-and-sandbox`。这会彻底绕过所有审批和沙盒，和你的个人电脑权限完全一样。只建议在隔离好的虚拟机或 Docker 中使用。

不暂停策略：使用 `codex -a never`，设置审批策略为"从不请求人工审批"。

**进入应用后设置（配置文件或交互）**

配置安全命令白名单：可以在项目的配置文件（通常是 .codex/config.toml 或通过 codex config 命令）中设置 safeCommands。例如：添加 `"safeCommands": ["npm test", "yarn build", "ls", "git status"]`。只要是这些命令被调用，就会自动执行而不再弹窗。

交互过程中的快捷选项：当运行过程中请求审批时，终端通常会提供几个选项：Accept once（仅允许本次操作）；Accept for session（允许该操作，并且在本次会话期间相同的操作不再询问）；Accept and add to policy（允许该操作，并将其永久加入到执行策略 execpolicy 中，以后永远自动审批）。

Codex 用量查询：`/status`。

---

## 新 Agent 补充：Multica / Hermes Agent / Copilot CLI

### Multica

**作用**

Multica 是一个"把 AI Agent 当团队成员管理"的平台：可以给 agent 分配 issue、跟踪执行进度、复用技能。它本身不直接替代 Codex/Claude/OpenCode，而是统一调度这些 CLI（通过本地 runtime/daemon）。

**本机状态（当前环境）**

`multica daemon status` 已显示运行中。已检测到本机可用 agent CLI：openclaw, claude, codex, opencode。

**最短使用路径**

```bash
multica login
multica daemon start
multica daemon status

multica workspace list
multica agent list
multica issue list
```

**常用命令**

```bash
# 创建任务
multica issue create --title "修复登录页样式" --description "按钮在移动端错位" --priority high

# 创建时直接指定执行者（成员或 agent 名）
multica issue create --title "补充单测" --assignee "my-agent"

# 给已有 issue 指派 agent
multica issue assign <issue-id> --to "my-agent"

# 过滤查看
multica issue list --status todo
multica issue list --assignee "my-agent"

# 停止本地 runtime
multica daemon stop
```

**日常理解**

Runtime = 执行环境（你的本机 daemon 或云端 runtime）。Agent = 在某个 runtime 上执行任务的"数字同事"。Issue = 给 agent 的工作项（可分配、可追踪、可复用流程）。

---

### Hermes Agent

**作用**

Hermes 是 Nous Research 的通用 agent：可在 CLI 内交互，也可接入 Telegram/Discord/Slack 等消息网关。支持多模型提供方切换（OpenAI/OpenRouter/自定义 endpoint 等），并有记忆、技能、会话管理。

**安装后快速开始**

```bash
hermes setup        # 首次建议先跑向导
hermes              # 进入交互
hermes model        # 选择模型/提供方
hermes tools        # 管理可用工具
hermes status       # 查看整体状态
hermes doctor       # 诊断问题
hermes update       # 更新
```

**常用命令（实战）**

```bash
hermes sessions list     # 查看历史会话
hermes insights          # 用量与分析
hermes config set ...    # 设置项

# 如果要接入消息平台
hermes gateway setup
hermes gateway start
```

**交互里常用 slash 命令**

`/new` 或 `/reset`：新会话。`/model`：切模型。`/usage`：查看使用情况。`/compress`：压缩上下文。`/skills`：查看/使用技能。`/retry`、`/undo`：重试/回退最近一步。

---

### Copilot CLI

**作用**

GitHub Copilot 的终端代理工具，可在本地仓库里对话式改代码、运行命令、执行多步任务。

**使用方式（新版）**

```bash
copilot

# 单次非交互执行
copilot -p "总结这周的提交并给出风险点"
```

**交互中常用**

`/model`：切换模型。`/context`：查看上下文/token 使用情况。`/compact`：压缩上下文。

**自动审批相关（高风险）**

`--allow-tool='shell(git)'`：只放行指定工具。`--allow-all-tools`：放行全部工具（风险高）。`--deny-tool='shell(rm)'`：显式禁用高风险工具。

**用量与额度查看**

个人页面：https://github.com/settings/copilot。组织/企业：组织的 Copilot Billing/Usage 页面。

**兼容说明**

旧的 `gh copilot`（`gh copilot suggest/explain`）已被新 Copilot CLI 路线替代，尽量使用 `copilot` 新命令。

> 相关阅读：[[Agent开发全景指南]]、[[远程 Code CLI 工具指南]]