# Windows 自启动方式

## 一、软件自启（应用程序自启）

用户**登录后**启动应用程序，运行在用户会话中，有界面。

### 1. 注册表 Run 键

| 位置 | 作用域 |
|------|--------|
| `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | 当前用户 |
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | 所有用户（需管理员权限） |

**特点**：
- 用户登录时执行
- 简单直接，大多数软件采用此方式
- 无法设置依赖关系

**查看命令**：
```powershell
reg query "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
```

### 2. 启动文件夹

| 位置 | 作用域 |
|------|--------|
| `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup` | 当前用户 |
| `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup` | 所有用户 |

**特点**：
- 本质是快捷方式，易于管理
- 用户登录时执行
- 无法设置依赖关系

### 3. 任务计划程序（LogonTrigger）

通过 Task Scheduler 创建登录触发任务。

**特点**：
- 最灵活，支持延迟、条件、最高权限等
- 可设置网络条件、电源条件
- 可配置重复执行

**查看命令**：
```powershell
Get-ScheduledTask | Where-Object { $_.Triggers -is [Microsoft.Management.Infrastructure.CimInstance] -and $_.Triggers[0].CimClass -like '*LogonTrigger*' }
```

---

## 二、服务自启（Windows Service）

**系统启动时**（登录前）启动后台服务，运行在 Session 0，无界面。

### 1. 服务管理器

通过 `services.msc` 或命令行管理。

**启动类型**：
- `Automatic`：系统启动时自动启动
- `Automatic (Delayed Start)`：系统启动后延迟启动
- `Manual`：手动启动
- `Disabled`：禁用

**服务依赖**：
```powershell
# 查看服务依赖关系
Get-Service -Name "ServiceName" | Select-Object -Property *
sc.exe query "ServiceName"
```

**特点**：
- 在用户登录前就启动
- 支持设置服务依赖链（A 依赖 B，B 先启动）
- 运行在系统会话（Session 0），无交互界面

### 2. 任务计划程序（BootTrigger）

系统启动时触发，非登录时。

---

## 三、关键区别

| 特性 | 软件自启 | 服务自启 |
|------|----------|----------|
| 触发时机 | 用户登录后 | 系统启动时（登录前） |
| 运行环境 | 用户会话，有桌面 | Session 0，无界面 |
| 依赖设置 | 不支持原生依赖 | 支持服务依赖链 |
| 适用场景 | GUI 程序、托盘程序 | 后台守护进程、网络服务 |
| 权限 | 当前用户权限 | Local System / Network Service 等 |

---

## 四、常见软件的自启方式

| 软件 | 方式 | 位置 |
|------|------|------|
| Clash Verge | 任务计划程序 | 任务：`Clash Verge (Admin)` |
| Tailscale | Windows 服务 | 服务：`Tailscale` (Automatic) |
| 大多数软件 | 注册表 Run 键 | `HKCU\...\Run` |

---

## 五、实现服务启动后触发应用程序

如果需要"A 服务启动后再启动 B 应用程序"，可用的方案：

### 方案 A：任务计划程序事件触发器（可能失败）

监听服务启动事件（Event ID 7036），触发应用程序。

**问题**：Windows 默认可能不记录 7036 事件（服务运行状态），只记录异常事件（7000, 7023, 7026 等）。需要先检查系统是否有此类事件：

```powershell
Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Service Control Manager'; Id=7036} -MaxEvents 10
```

如果无输出，则事件触发器方案不可行。

### 方案 B：登录触发 + PowerShell 等待脚本（推荐）

用户登录时触发脚本，脚本内循环等待服务就绪后再启动应用程序。

**脚本示例**（保存为 `.ps1` 文件）：
```powershell
param(
    [string]$ServiceName = "Tailscale",
    [string]$TargetPath = "D:\Program Files\Clash Verge\clash-verge.exe"
)

function Wait-ServiceRunning {
    param([string]$Name)
    $maxWait = 120
    $elapsed = 0
    while ($elapsed -lt $maxWait) {
        $service = Get-Service -Name $Name -ErrorAction SilentlyContinue
        if ($service -and $service.Status -eq 'Running') {
            return $true
        }
        Start-Sleep -Seconds 3
        $elapsed += 3
    }
    return $false
}

if (Wait-ServiceRunning -Name $ServiceName) {
    Start-Sleep -Seconds 5  # 网络稳定等待
    Start-Process -FilePath $TargetPath
}
```

**创建任务计划**：
```powershell
schtasks /create /tn "Clash Verge (Tailscale Wait)" /tr 'powershell.exe -NoProfile -ExecutionPolicy Bypass -WindowStyle Hidden -File "D:\Desktop\StartTrigger\StartClashVerge.ps1"' /sc onlogon /rl highest /f
```

**关键点**：
- 使用 `-File` 参数指定脚本路径，避免复杂的命令行解析问题
- 脚本文件需要 UTF-8 编码（中文注释可能导致解析失败）
- `-WindowStyle Hidden` 隐藏执行窗口

### 方案 C：设置服务依赖（仅限服务）

如果目标也是服务，可以通过注册表设置依赖：

```powershell
sc.exe config "TargetService" depend= "DependencyService"
```

---

## 六、踩坑记录：为什么某些方案失败

### 1. 事件触发器失败原因

**现象**：创建了监听 Event ID 7036 的任务触发器，但服务启动时不触发。

**原因**：
- Windows 默认不记录正常的"服务进入运行状态"事件（7036）
- 系统日志只记录异常事件：7000（启动失败）、7023（异常终止）、7031（崩溃重启）等
- 需先检查 `Get-WinEvent` 确认系统有此类事件再使用此方案

### 2. PowerShell 命令直接嵌入失败

**现象**：把 PowerShell 命令直接写在任务计划的 Arguments 中，返回错误码 `267009` 或 `3221225786`。

**原因**：
- 任务计划的命令行解析有特殊规则，嵌套引号和特殊字符容易出错
- 解决：使用独立的 `.ps1` 脚本文件 + `-File` 参数

### 3. 批处理脚本失败

**现象**：`sc query Tailscale | find "RUNNING"` 返回错误码 `255`。

**原因**：
- 在 Git Bash 环境执行 cmd.exe 时，路径和环境变量解析可能出问题
- PowerShell 脚本更可靠

### 4. 脚本编码问题

**现象**：脚本包含中文注释时执行报错 `RedirectionNotSupported`。

**原因**：
- PowerShell 脚本需要 UTF-8 编码
- 解决：使用英文注释或确保 UTF-8 编码保存