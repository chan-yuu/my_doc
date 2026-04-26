# 远程 Code CLI 工具指南

本文档汇总了两种远程使用 AI 编程助手（如 Claude Code）的方案：Happy 和 CloudCLI。虽然是未来的趋势，但从我的使用上来说，我感觉这是变差了，无论是从通信还是从学习的角度来说，它都不符合进步的条件。所以我在这里郑重宣布放弃在手机中使用顶尖模型的习惯。

> 相关阅读：[[Agent开发全景指南]]、[[GSD (Get Shit Done) 完全使用指南]]、[[gemini cli注意事项]]

---

## 方案对比

| 特性 | Happy | CloudCLI |
| --- | --- | --- |
| 运行方式 | 本地设备 + 移动端控制 | 云端容器 或 自托管服务器 |
| 费用 | 完全免费开源 | 托管服务 €7-€20/月，自托管免费 |
| 网络要求 | 需要本地设备在线 | 云端 24/7 在线 |
| 移动端支持 | 手机/平板 App | 浏览器访问 |
| SSH 访问 | 不支持 | 支持 SSH/VS Code |
| 自托管 | N/A（本身就是本地运行） | 可部署到自己的服务器 |
| 适合场景 | 本地开发、隐私优先 | 云端开发、随时访问 |

---

# Happy

## 概述

Happy 是一个免费开源的移动端应用，让你可以从手机、平板或网页浏览器远程控制 Claude Code AI 编程代理。核心优势是 AI 编程代理运行在你自己的设备上（桌面电脑、笔记本电脑、服务器甚至树莓派），而不是云服务上。

## 核心特点

| 特性 | 说明 |
| --- | --- |
| 零工作流干扰 | 在你控制的任何电脑上运行 Claude Code，使用自定义配置、特殊硬件或独特工具 |
| 随处工作 | 桌面开始编码，手机继续；所有 Claude Code 功能在移动端都可用（计划模式、自定义代理、自定义 MCP 服务器） |
| 多会话支持 | 同时运行多个 Claude Code 实例——一个处理前端，另一个处理后端 |
| 端到端加密 | 代码保留在你的设备上；设备间的消息是加密的 |
| 无云费用 | 使用自己的电脑，无使用费或订阅费 |

## 安装

```bash
npm install -g happy-coder
```

## 命令详解

### 基本用法

```bash
happy [options]         # 启动 Claude 会话并启用移动端控制
happy auth              # 管理身份认证
happy resume            # 通过 Happy 会话 ID 恢复之前的会话
happy codex             # 启动 Codex 模式
happy gemini            # 启动 Gemini 模式 (ACP)
happy acp               # 启动通用 ACP 兼容代理
happy connect           # 连接 AI 供应商 API 密钥
happy sandbox           # 配置和管理操作系统级别的沙箱
happy notify            # 发送推送通知
happy daemon            # 管理后台服务，允许在离开电脑时启动新会话
happy doctor            # 系统诊断和故障排除
```

### 命令详细说明

#### happy

启动一个新的 Claude Code 会话，同时启用 Happy 的移动端控制功能。这是最常用的命令。

#### happy auth

管理身份认证，包括登录和登出。`happy auth login --force`：强制重新认证。

#### happy resume <session-id>

通过 Happy 会话 ID 恢复之前的会话。例如：

```bash
happy resume cmmij8
```

#### happy codex

启动 Codex 模式，用于 OpenAI Codex 相关操作。

#### happy gemini

启动 Gemini 模式，通过 ACP（Agent Communication Protocol）协议运行 Gemini AI。

#### happy acp

启动通用 ACP 兼容代理。可以配合其他工具使用：

```bash
happy acp gemini              # 通过 ACP 启动 Gemini
happy acp -- opencode --acp   # 启动自定义 ACP 命令
happy acp opencode --verbose  # 打印原始 ACP 后端/信封事件
```

#### happy connect

连接 AI 供应商的 API 密钥，配置你的 AI 服务凭证。

#### happy sandbox

配置和管理操作系统级别的沙箱安全设置，确保代码执行的安全性。

#### happy notify

手动发送推送通知，用于测试或特定场景提醒。

#### happy daemon

管理后台服务。这个服务允许你在离开电脑时远程启动新的会话。

#### happy doctor

运行系统诊断和故障排除，帮助检测配置问题。

## 选项参数

### --yolo

绕过权限确认，相当于 happy sugar 的 --dangerously-skip-permissions。适合信任的环境下快速操作。

### --chrome

为此会话启用 Chrome 浏览器访问。

### --no-chrome

禁用 Chrome，即使默认配置是启用的。

### --no-sandbox

为此会话禁用 Happy 沙箱。

### --js-runtime <runtime>

指定 JavaScript 运行时。例如使用 bun 代替 node：

```bash
happy --js-runtime bun
```

### --claude-env <env-vars>

设置自定义 Claude 环境变量。例如使用自定义 API 端点：

```bash
happy --claude-env ANTHROPIC_BASE_URL=http://127.0.0.1:3456
```

### --resume

恢复上一个会话（Claude Code 原生功能）。

## 实用示例

```bash
# 启动新会话
happy

# 绕过权限快速操作
happy --yolo

# 使用 bun 运行时启动
happy --js-runtime bun

# 使用自定义 API 端点
happy --claude-env ANTHROPIC_BASE_URL=http://127.0.0.1:3456

# 恢复指定会话
happy resume cmmij8

# 运行系统诊断
happy doctor

# 强制重新认证
happy auth login --force
```

## 相关资源

- Web 应用：https://app.happy.engineering
- GitHub 仓库：https://github.com/slopus/happy
- 文档：https://happy.engineering/docs/
- 安全与加密：https://happy.engineering/docs/security/

---

# CloudCLI

## 概述

CloudCLI 是一个开源的云端开发环境平台，让你可以从任何地方使用 AI 编程助手（Claude Code、Codex、Cursor CLI）。它提供两种使用方式：

1. CloudCLI Cloud — 托管云服务，提供即时的容器化开发环境
2. 自托管 — 在自己的机器或服务器上部署运行

## 核心特点

| 特性 | 说明 |
| --- | --- |
| 云端开发环境 | 即时创建容器化开发环境，支持 SSH 访问 |
| 多客户端访问 | 通过 SSH、VS Code 或浏览器终端连接 |
| 自定义子域名 | 每个环境分配独立子域名 |
| AI 助手集成 | 支持 Claude Code、Codex、Cursor CLI |
| 移动端支持 | 通过浏览器在手机或平板上编码 |
| 可扩展插件系统 | 支持自定义标签页、前端组件、后端服务 |

## 核心功能

### Chat 界面

AI 驱动的对话式编程助手，通过聊天完成编码任务。

### 文件浏览器与编辑器

内置文件管理和编辑功能，无需额外 IDE。

### Git 浏览器

集成的版本控制功能，直接在界面中管理 Git 操作。

### 会话管理

管理多个开发会话，轻松切换不同项目。

## 安装与部署

### 托管云服务（推荐新手）

直接访问 CloudCLI Cloud，无需自行部署。价格 €7-€20/月。

### 自托管部署

**快速启动（npx）**

```bash
npx cloudcli
```

**全局安装（npm）**

```bash
npm install -g cloudcli
cloudcli
```

**本地开发**

```bash
git clone https://github.com/siteboon/claudecodeui
cd claudecodeui
npm install
npm run dev
```

**远程服务器部署**

可将 CloudCLI UI 部署到自己的服务器，实现私有化云端开发环境。

## 配置选项

### 工具与权限配置

自定义 AI 助手可使用的工具和权限级别。

### 环境变量

设置开发环境所需的环境变量。

### MCP 服务器集成

支持 Model Context Protocol (MCP) 服务器，扩展 AI 助手能力。

## 插件系统

CloudCLI 提供可扩展的插件架构：自定义标签页（创建专属的功能标签页）；前端组件（扩展 UI 界面）；后端服务（添加自定义后端功能）；第三方工具集成（连接其他开发工具）。

### 插件开发资源

- Frontend API Reference
- Security Model Documentation
- Backend Server Configurations

## Tailscale + CloudCLI 组合方案

推荐组合：使用 Tailscale 组网 + CloudCLI 自托管，可以获得安全、私密的远程开发体验。

### 优势

安全访问：Tailscale 提供加密的私有网络，无需暴露端口到公网。零公网 IP：即使没有公网 IP 也能远程访问。自托管隐私：代码和数据完全在自己的服务器上。成本低：Tailscale 免费版足够个人使用，CloudCLI 自托管零费用。

### 典型架构

```
[手机/平板/笔记本] 
       ↓ Tailscale 私有网络
[家中/公司的服务器运行 CloudCLI]
       ↓ 
[容器化开发环境 + Claude Code]
```

### 部署步骤（简述）

1. 在服务器上安装 Tailscale 并加入网络
2. 在本地设备上安装 Tailscale（同一网络）
3. 在服务器上部署 CloudCLI 自托管版
4. 通过 Tailscale 分配的内网 IP 访问 CloudCLI

## 相关资源

- 文档：https://cloudcli.ai/docs
- API 文档：https://developer.cloudcli.ai
- GitHub 仓库：https://github.com/siteboon/claudecodeui
- 价格：€7-€20/月（托管版），自托管免费

---

# 总结：如何选择？

| 场景 | 推荐方案 |
| --- | --- |
| 本地电脑常开，想用手机控制 | Happy |
| 需要云端 24/7 在线环境 | CloudCLI Cloud |
| 有服务器，追求隐私和低成本 | CloudCLI 自托管 + Tailscale |
| 无公网 IP，需要远程访问 | Happy 或 CloudCLI + Tailscale |
| 预算有限 | Happy（免费）或 CloudCLI 自托管（免费） |
| 团队协作、需要稳定云环境 | CloudCLI Cloud |

> 相关阅读：[[Agent开发全景指南]]、[[gemini cli注意事项]]