# WezTerm 终端配置

WezTerm 是一个跨平台的高性能终端模拟器，使用 Rust 编写，支持 GPU 加速渲染。

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[远程ssh的重构-基于服务器的开发]]、[[从tmux到zellij-程序员需要隐藏起来的终端运行过程]]

---

## 为什么用 WezTerm？

| 对比 | cmd/PowerShell | WezTerm |
| --- | --- | --- |
| 分屏 | 不支持 | 任意分屏，一个窗口多终端 |
| 标签页 | Windows Terminal 才有 | 原生支持 |
| GPU 加速 | CPU 渲染 | 流畅滚动 |
| 透明背景 | 不支持 | 支持 |
| 远程会话保持 | 断开就没了 | multiplexing 保持会话 |
| 终端显示图片 | 不支持 | Sixel/Kitty 协议 |
| 跨平台 | 仅 Windows | Win/macOS/Linux 通用 |
| 配置 | 难以自定义 | Lua 灵活配置 |

---

## 安装位置

```
D:\Program Files\WezTerm\
```

## 配置文件位置

```
C:\Users\24541\.wezterm.lua
```

修改后自动热加载生效。

---

## 全局快捷键启动（AutoHotKey 方案）

使用 AutoHotKey 实现 `Ctrl+Alt+T` 全局唤起 WezTerm，比 Windows 快捷方式方式更稳定。

### 安装 AutoHotKey

```bash
winget install AutoHotkey.AutoHotkey
```

### 脚本内容

脚本位置：`C:\Users\24541\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\WezTermToggle.ahk`

```ahk
; WezTerm Toggle - Ctrl+Alt+T
#Requires AutoHotkey v2.0

^!t:: {
    static weztermPath := "D:\Program Files\WezTerm\wezterm.exe"
    static weztermClass := "org.wezfurlong.wezterm"

    if WinExist("ahk_class " weztermClass) {
        if WinActive("ahk_class " weztermClass) {
            WinMinimize("ahk_class " weztermClass)
        } else {
            WinActivate("ahk_class " weztermClass)
        }
    } else {
        Run(weztermPath, , "Hide")
    }
}
```

### 功能

`Ctrl+Alt+T` 启动 WezTerm。WezTerm 已存在但不在前台 → 激活窗口。WezTerm 当前是活动窗口 → 最小化（toggle 效果）。"Hide" 参数避免启动时弹出额外控制台窗口。

### 开机自启

使用注册表启动项（比 Startup 文件夹更稳定）：

```powershell
Set-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' -Name 'WezTermToggle' -Value '"C:\Users\24541\AppData\Local\Programs\AutoHotkey\v2\AutoHotkey64.exe" "C:\Users\24541\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\WezTermToggle.ahk"'
```

注册表路径：`HKCU\Software\Microsoft\Windows\CurrentVersion\Run\WezTermToggle`

注意：直接把 `.ahk` 文件放 Startup 文件夹不会自动执行，需要通过注册表或快捷方式启动 AutoHotKey 进程。

---

## 当前配置

### 外观

字体：Consolas, 14pt。背景：黑色 + 75% 透明。滚动条：启用，浅灰色。光标：稳定竖线。标签栏：原生风格，顶部显示。

### 启动程序

默认启动 PowerShell 7，启动菜单包含：PowerShell 7、Command Prompt、WSL Ubuntu。

---

## 快捷键

### 分屏与窗格

| 快捷键 | 功能 |
| --- | --- |
| Ctrl+Shift+A | 上下分屏 |
| Ctrl+Shift+S | 左右分屏 |
| Ctrl+D | 关闭当前分屏（最后一个关闭标签页） |
| Alt + 方向键 | 切换分屏 |

### 标签页

| 快捷键 | 功能 |
| --- | --- |
| Ctrl+Shift+T | 新建标签页 |
| Ctrl+Tab | 下一个标签页 |
| Ctrl+Shift+Tab | 上一个标签页 |
| 点击标签 × | 关闭标签页 |

### 其他

| 快捷键 | 功能 |
| --- | --- |
| Ctrl+Shift+L | 启动菜单 |
| Ctrl + +/-/0 | 放大/缩小/重置字体 |

---

## 命令行用法

```
wezterm [OPTIONS] [COMMAND]

Commands:
  start                  启动 GUI，可指定运行程序 [别名: -e]
  ssh                    建立 SSH 会话
  serial                 打开串口
  connect                连接到 wezterm 多路复用器
  ls-fonts               显示字体信息
  show-keys              显示快捷键绑定
  cli                    与 mux 服务器交互
  imgcat                 在终端输出图片
  set-working-directory  设置工作目录（OSC 7 转义序列）
  record                 录制终端会话（asciicast 格式）
  replay                 回放 asciicast 会话
  shell-completion       生成 shell 补全信息

Options:
  -n, --skip-config              跳过加载配置文件
  --config-file <FILE>           指定配置文件
  --config <name=value>          覆盖特定配置项
  -h, --help                     显示帮助
  -V, --version                  显示版本
```

### 常用命令示例

```bash
# 启动 WezTerm
wezterm start

# 启动并运行指定程序
wezterm start -- pwsh.exe -NoLogo

# SSH 连接
wezterm ssh user@server.com

# 显示图片
wezterm imgcat image.png

# 录制终端会话
wezterm record -o session.cast

# 回放录制
wezterm replay session.cast

# 查看快捷键
wezterm show-keys

# 查看字体信息
wezterm ls-fonts

# 临时覆盖配置
wezterm start --config window_background_opacity=0.5
```

---

## 特色功能

### 终端显示图片

WezTerm 支持 Sixel 和 Kitty Graphics Protocol。

```bash
# 显示图片
wezterm imgcat "C:\path\to\image.png"

# 或使用 Python 工具
pip install timg
timg "image.png"
```

### 远程开发（SSH）

在配置文件中设置 ssh_domains：

```lua
config.ssh_domains = {
  {
    name = 'my.server',
    remote_address = 'your-server.com:22',
    username = 'your_username',
    multiplexing = 'WezTerm',  -- 保持会话
  },
}
```

连接方式：`Ctrl+Shift+L` 打开启动菜单，选择 SSH 连接；或命令行：`wezterm ssh my.server`。

优势：`multiplexing = 'WezTerm'` 会保持会话，断网重连后之前的命令和输出还在。

### WSL 集成

已在配置中添加 WSL Ubuntu 域，可直接从启动菜单进入。

### 会话录制与回放

```bash
# 录制
wezterm record -o demo.cast

# 回放
wezterm replay demo.cast
```

---

## 相关链接

- 官网：https://wezfurlong.org/wezterm/
- 配置文档：https://wezfurlong.org/wezterm/config/files.html
- 快捷键列表：https://wezfurlong.org/wezterm/config/keys.html

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[远程ssh的重构-基于服务器的开发]]、[[从tmux到zellij-程序员需要隐藏起来的终端运行过程]]