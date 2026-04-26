https://cursor.com/cn/docs/cli/overview
通过agent来启动


## Cursor Agent CLI

### 安装与认证

```powershell
# 登录认证
agent login

# 登出
agent logout

# 查看认证状态
agent status
agent whoami

# 查看版本和系统信息
agent about
```

### 基本使用模式

```powershell
# 交互模式
agent

# 带初始提示启动
agent "修复 main.js 中的 bug"

# 打印模式（非交互，适合脚本）
agent -p "修复 bug" --trust

# 指定工作目录
agent --workspace "C:\Users\24541\projects"
```

### 模式选择

```powershell
# 计划模式（只读，分析和规划）
agent --plan
agent --mode plan

# 问答模式（只读，解释和问答）
agent --mode ask

# 云模式（打开 composer 选择器）
agent -c
agent --cloud
```

### 会话管理

```powershell
# 继续上次会话
agent --continue

# 恢复特定会话
agent --resume <chat-id>

# 列出会话
agent ls

# 创建空会话
agent create-chat
```

### 模型选择

```powershell
# 指定模型
agent --model sonnet-4
agent --model gpt-5
agent --model sonnet-4-thinking

# 列出可用模型
agent --list-models
agent models
```

### 权限控制

```powershell
# 强制允许所有命令
agent --force
agent --yolo  # 别名

# 信任当前工作区
agent --trust

# 自动批准 MCP 服务器
agent --approve-mcps
```

### Worktree 隔离

```powershell
# 在隔离的 git worktree 中工作
agent -w

# 指定 worktree 名称
agent -w feature-branch

# 指定 worktree 基础分支
agent -w feature-branch --worktree-base main
```

### MCP 管理

```powershell
# 管理 MCP 服务器
agent mcp
```

### 输出格式

```powershell
# 文本输出
agent -p "分析代码" --output-format text

# JSON 输出
agent -p "分析代码" --output-format json

# 流式 JSON 输出
agent -p "分析代码" --output-format stream-json --stream-partial-output
```

### 其他选项

```powershell
# 设置 API Key
agent --api-key <key>
# 或使用环境变量 CURSOR_API_KEY

# 添加自定义请求头
agent -H "X-Custom: value"

# 启用/禁用沙箱
agent --sandbox enabled
agent --sandbox disabled

# 更新到最新版本
agent update

# 生成 Cursor 规则
agent generate-rule
agent rule
```

---
