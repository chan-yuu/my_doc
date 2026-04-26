## 表格对比

| 功能 | Copilot CLI | Cursor Agent |
|------|-------------|--------------|
| 认证 | GitHub 账号 | Cursor 账号 |
| 交互模式 | `copilot` | `agent` |
| 非交互模式 | `copilot -p` | `agent -p` |
| 计划模式 | `--plan` / `--mode plan` | `--plan` / `--mode plan` |
| 自动驾驶 | `--autopilot` | 无 |
| 问答模式 | 无 | `--mode ask` |
| Worktree 隔离 | 无 | `-w` |
| 推理深度控制 | `--effort` | 无（通过模型选择） |
| MCP 支持 | 内置 GitHub MCP | 支持 |
| 目录白名单 | `--add-dir` | `--workspace` |
| 权限控制 | 细粒度 `--allow-tool` | `--force` / `--trust` |

---

## 常见使用场景

### 场景 1：快速修复 Bug

```powershell
# Copilot
copilot -i "修复登录页面的验证逻辑错误" --allow-all-tools

# Cursor
agent "修复登录页面的验证逻辑错误"
```

### 场景 2：代码审查（只读）

```powershell
# Copilot
copilot --plan -i "审查 src/auth 目录的安全性"

# Cursor
agent --mode plan "审查 src/auth 目录的安全性"
```

### 场景 3：自动化脚本

```powershell
# Copilot（JSON 输出）
copilot -p "分析 package.json 中的依赖漏洞" --output-format json -s --allow-all-tools

# Cursor（打印模式）
agent -p "分析 package.json 中的依赖漏洞" --output-format json --trust
```

### 场景 4：隔离环境开发

```powershell
# Cursor 特有功能：在独立 worktree 中工作
agent -w new-feature --worktree-base develop
```

### 场景 5：多项目工作

```powershell
# Copilot
copilot --add-dir "C:\project1" --add-dir "D:\project2"

# Cursor
agent --workspace "C:\project1"
```

---

## 配置文件位置

| 工具 | 配置目录 |
|------|----------|
| Copilot CLI | `~/.copilot/` |
| Cursor Agent | `~/.cursor/` |

---

## 注意事项

1. **权限风险**：`--allow-all` / `--yolo` / `--force` 会跳过所有确认，谨慎使用
2. **成本控制**：长时间交互会消耗 API 配额，注意监控使用量
3. **敏感信息**：使用 `--secret-env-vars` 保护环境变量中的密钥
4. **路径问题**：Windows 路径包含空格时，使用引号包裹