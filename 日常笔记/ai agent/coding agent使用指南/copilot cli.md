模型支持如下，主要还是接入claude code吧(已经确认，不能使用cc的反代机制，各家的agent其实还是没有很好的双向支持的。所以这里我们就不再使用另外的api了哈。。。。)
![[Pasted image 20260417163852.png]]

gemini-3.1-pro-preview
gpt-5.3-codex

---


https://github.com/features/copilot/cli
通过copilot来启动
## GitHub Copilot CLI

### 安装与认证

```powershell
# 安装（需要先安装 GitHub CLI）
gh extension install github/copilot

# 登录认证
copilot login

# 查看版本
copilot version
```

### 基本使用模式

```powershell
# 交互模式（推荐，持续对话）
copilot

# 交互模式 + 自动执行初始提示
copilot -i "修复 main.js 中的 bug"

# 非交互模式（单次执行后退出）
copilot -p "修复 main.js 中的 bug" --allow-all-tools

# 继续上次会话
copilot --continue

# 恢复特定会话
copilot --resume=<session-id>
```

### 权限控制

```powershell
# 允许所有操作（危险，谨慎使用）
copilot --allow-all
copilot --yolo  # 别名

# 仅允许所有工具
copilot --allow-all-tools

# 允许访问任意路径
copilot --allow-all-paths

# 允许访问所有 URL
copilot --allow-all-urls

# 允许特定工具
copilot --allow-tool='write'
copilot --allow-tool='shell(git:*)'

# 拒绝特定工具
copilot --deny-tool='shell(git push)'
```

### 目录访问

```powershell
# 添加允许访问的目录
copilot --add-dir "C:\Users\24541\projects"

# 添加多个目录
copilot --add-dir ~/workspace --add-dir /tmp

# 允许所有路径（跳过验证）
copilot --allow-all-paths
```

### 模式选择

```powershell
# 交互模式（默认）
copilot --mode interactive

# 计划模式（只读，不执行修改）
copilot --plan
copilot --mode plan

# 自动驾驶模式（自主执行）
copilot --autopilot
copilot --mode autopilot
```

### 模型选择

```powershell
# 指定模型
copilot --model gpt-5.2

# 设置推理深度
copilot --effort high
# 可选: low, medium, high, xhigh
```

### MCP 服务器

```powershell
# 启用所有 GitHub MCP 工具
copilot --enable-all-github-mcp-tools

# 添加特定 MCP 工具
copilot --add-github-mcp-tool "issues"

# 禁用内置 MCP 服务器
copilot --disable-builtin-mcps

# 额外 MCP 配置
copilot --additional-mcp-config @mcp-config.json
```

### 输出格式

```powershell
# JSON 输出（适合脚本）
copilot -p "分析代码" --output-format json

# 静默输出（仅显示结果）
copilot -p "修复 bug" -s --allow-all-tools
```

### 初始化项目

```powershell
# 在仓库中初始化 Copilot 指令
copilot init
```

---
