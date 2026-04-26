# Rust 安装与编译入门

> 2026-04-16 | 通过构建 keyviz 项目学习

## Rust 是什么？

Rust 是一种现代编程语言，主打「安全、高效、并发」。它由 Mozilla 开发，用于解决 C/C++ 的内存安全问题。

**核心特点：**
- 内存安全：编译器在编译时检查内存使用，避免空指针、内存泄漏等问题
- 高性能：性能接近 C/C++
- 跨平台：支持 Windows、Linux、macOS

## 为什么 Tauri 需要 Rust？

Tauri 是一个桌面应用框架，架构分为两部分：

```
┌─────────────────────────────────────┐
│  前端 (你熟悉的)                      │
│  - React / Vue / Svelte              │
│  - HTML / CSS / JavaScript           │
│  - 负责 UI 渲染                       │
└─────────────────────────────────────┘
         ↓ IPC 通信
┌─────────────────────────────────────┐
│  后端 (Rust)                          │
│  - 系统级操作                         │
│  - 文件读写、窗口管理                 │
│  - 原生性能、小体积                   │
└─────────────────────────────────────┘
```

**简单类比：**
- 前端 = 汽车的仪表盘和座椅（用户看到和操作的）
- Rust 后端 = 汽车的引擎和底盘（底层功能实现）

Rust 负责：
1. 创建和管理应用窗口
2. 与操作系统交互（文件、托盘图标等）
3. 提供比 Electron 更小的安装包体积（几 MB vs 几百 MB）

## 安装过程详解

### 1. Rustup 安装器

Rustup 是 Rust 的官方安装工具，它会安装：
- **rustc**：Rust 编译器，把 Rust 代码编译成可执行文件
- **cargo**：Rust 的包管理器和构建工具（类似 npm）
- **rust-std**：Rust 标准库

安装命令：
```powershell
winget install Rustlang.Rustup
```

安装后文件位置：
- `C:\Users\<用户名>\.rustup\` — Rust 工具链本体
- `C:\Users\<用户名>\.cargo\` — cargo 和编译出的二进制文件

### 2. 为什么需要 Visual Studio Build Tools？

Rust 在 Windows 上需要「链接器」来生成最终的可执行文件。

**编译过程分为两个阶段：**
```
Rust 源代码 → rustc 编译 → 目标文件(.obj) → link.exe 链接 → 可执行文件(.exe)
```

- **rustc**：把 Rust 代码编译成中间的目标文件
- **link.exe**：把目标文件和系统库组合成最终的 .exe

`link.exe` 来自 Microsoft Visual C++ (MSVC)，所以需要安装：
```powershell
winget install Microsoft.VisualStudio.2022.BuildTools
```

安装时选择「C++ 构建工具」工作负载。

### 3. 环境变量 PATH

安装后，需要把 Rust 工具添加到 PATH：
- `%USERPROFILE%\.cargo\bin` 包含 `cargo.exe`、`rustc.exe` 等

安装程序通常会自动添加。如果终端找不到命令，检查：
```powershell
# 查看 PATH 中是否包含 cargo
[Environment]::GetEnvironmentVariable("Path", "User") -like "*cargo*"
```

## 编译过程详解

### Tauri 构建命令

```powershell
npm run tauri build
```

这个命令做的事情：

```
┌────────────────────────────────────────────────────────┐
│  1. beforeBuildCommand (npm run build)                 │
│     - TypeScript 编译检查 (tsc)                         │
│     - Vite 打包前端代码 → dist/ 目录                    │
├────────────────────────────────────────────────────────┤
│  2. Rust 编译                                           │
│     - cargo build --release                             │
│     - 编译 src-tauri/src/*.rs 文件                      │
│     - 生成 keyviz.exe                                   │
├────────────────────────────────────────────────────────┤
│  3. 打包安装程序                                         │
│     - MSI 安装包 (Windows Installer)                    │
│     - NSIS 安装包 (另一种 Windows 安装格式)             │
└────────────────────────────────────────────────────────┘
```

### Cargo 的工作流程

**1. 依赖下载：**
Rust 使用 `Cargo.toml` 文件管理依赖（类似 `package.json`）：
```toml
[dependencies]
tauri = "2.9.5"
serde = "1.0"
```

首次编译时，cargo 会从 crates.io（Rust 的 npm 仓库）下载依赖。

**2. 编译顺序：**
Cargo 按依赖关系顺序编译：
- 先编译依赖库（proc-macro2、quote、serde 等）
- 最后编译主程序（keyviz）

**3. 编译产物：**
```
src-tauri/target/
├── debug/          # 开发版本（体积大，包含调试信息）
└── release/        # 发布版本（体积小，性能优化）
    ├── keyviz.exe  # 可执行文件
    └── bundle/     # 安装包
        ├── msi/
        └── nsis/
```

### 编译时间为什么很长？

首次编译约 2-3 分钟，原因：
1. 需要下载约 200+ 个依赖库
2. 每个库都需要编译
3. Rust 编译器做很多优化和安全检查

好消息：第二次编译会快很多，因为已编译的库会被缓存。

## 常用命令速查

| 命令 | 说明 | 类比 npm |
|------|------|----------|
| `cargo new 项目名` | 创建新项目 | `npm init` |
| `cargo build` | 编译项目 | - |
| `cargo build --release` | 编译发布版本 | `npm run build` |
| `cargo run` | 编译并运行 | `npm run dev` |
| `cargo check` | 快速检查错误（不生成文件） | `tsc --noEmit` |
| `cargo add 依赖名` | 添加依赖 | `npm install` |
| `cargo update` | 更新依赖 | `npm update` |

## 关键文件说明

```
src-tauri/
├── Cargo.toml       # Rust 项目配置（类似 package.json）
├── Cargo.lock       # 锁定依赖版本（类似 package-lock.json）
├── tauri.conf.json  # Tauri 配置（应用名称、窗口设置等）
├── src/
│   ├── main.rs      # 程序入口
│   ├── lib.rs       # 库入口
│   └── app/         # 应用逻辑模块
└── crates/          # 本地依赖库
    └── rdev/        # 键盘/鼠标事件监听库
```

## 学习资源

- **Rust 官方教程**：https://doc.rust-lang.org/book/
- **Rust by Example**：https://doc.rust-lang.org/rust-by-example/
- **Tauri 文档**：https://tauri.app/start/
- **Cargo 指南**：https://doc.rust-lang.org/cargo/

## 快速理解 Rust 语法（与 JS 对比）

```rust
// 变量声明（Rust 默认不可变）
let x = 5;           // 类似 JS const
let mut y = 10;      // 类似 JS let（可变）

// 函数
fn add(a: i32, b: i32) -> i32 {
    a + b
}

// 条件语句（不需要括号）
if x > 0 {
    println!("positive");
}

// 循环
for i in 0..10 {     // 0 到 9
    println!("{}", i);
}

// 结构体（类似 JS 对象/类）
struct Point {
    x: i32,
    y: i32,
}

// 打印
println!("Hello, {}", name);  // 类似 console.log
```

## 总结

构建 Tauri 应用时：
1. 前端用熟悉的 JS/TS + React
2. Rust 只负责底层系统交互
3. 你不需要深入学习 Rust，理解基本概念即可
4. 大部分时间你还是在前端代码中工作

Rust 在这里是一个「工具」，不是主要开发语言。

主要过程如下：
- rustc = 编译器（Rust → 机器码）
- cargo = 包管理器 + 构建工具（类似 npm + webpack 合体）
- VS Build Tools = 提供 link.exe 链接器（把编译结果连接成 exe）
流程：Rust 源码 → rustc 编译 → link.exe 链接 → exe → 打包成 MSI/NSIS