# 从 tmux 到 Zellij：程序员需要隐藏起来的终端运行过程

终端多路复用器是程序员的重要工具，让终端会话可以在后台持续运行，断开连接也不会中断。

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[WezTerm终端配置]]、[[远程ssh的重构-基于服务器的开发]]

---

## tmux

tmux 是经典的终端多路复用器，主要功能包括：

- 会话保持：断开 SSH 连接后，终端会话继续运行
- 分屏功能：在一个窗口内分割成多个终端
- 窗口管理：创建多个窗口，每个窗口可以有多个分屏

### 常用操作

```bash
# 创建新会话
tmux new -s session_name

# 列出所有会话
tmux ls

# 连接到会话
tmux attach -t session_name

# 杀死会话
tmux kill-session -t session_name

# 杀死所有会话
tmux kill-server
```

### 常见问题

tmux 会继承环境变量。如果在 tmux 外部设置了代理（http_proxy），启动 tmux 后会继承这些环境变量，可能导致后续操作出现问题。

解决方法：

```bash
# 先杀死所有 tmux 进程
tmux kill-server

# 用一个没有代理的环境启动 tmux
env -u http_proxy -u https_proxy -u HTTP_PROXY -u HTTPS_PROXY -u ALL_PROXY -u all_proxy tmux new -As main
```

---

## Zellij

Zellij 是新一代终端多路复用器，用 Rust 编写，特点是：

- 更现代的界面设计
- 更直观的操作方式
- 更好的用户体验
- 原生支持持久化会话

Zellij 可以作为 tmux 的现代替代方案。

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[WezTerm终端配置]]