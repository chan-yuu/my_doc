# Windows 资源管理器崩溃重启方案

资源管理器（explorer.exe）是独立的进程，崩溃时可以用以下方法重启。

> 相关阅读：[[WezTerm终端配置]]

---

## 方法一：任务管理器（最常用）

`Ctrl + Shift + Esc` 直接打开任务管理器，或 `Ctrl + Alt + Del` 选择"任务管理器"。点击"文件" → "运行新任务"，输入 `explorer.exe` → 回车。

## 方法二：运行对话框

如果桌面没了但快捷键还能用：`Win + R` → 输入 `explorer.exe` → 回车。

## 方法三：PowerShell/CMD 窗口

如果正好有终端窗口开着：

```cmd
explorer.exe
```

## 方法四：创建应急脚本

可以在桌面放一个 `restart_explorer.bat`：

```cmd
@echo off
taskkill /f /im explorer.exe 2>nul
timeout /t 2 /nobreak >nul
start explorer.exe
```

## 方法五：注册桌面快捷键

创建一个 `.vbs` 脚本，绑定全局热键重启资源管理器。

---

推荐：方法一最可靠，因为任务管理器是独立进程，资源管理器崩溃不影响它。

> 相关阅读：[[WezTerm终端配置]]