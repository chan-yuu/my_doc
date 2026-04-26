# 远程 SSH 的重构：基于服务器的开发

对 SSH 工具有了新的认识。虽然已经能够通过 VSCode 直连了，但是这里面还是配置了大量的内容才保证了连接是正常的。

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[WezTerm终端配置]]、[[从tmux到zellij-程序员需要隐藏起来的终端运行过程]]

---

## SSH 密钥

生成 SSH 是连接设备的起始步骤。之前其实做了两次这个步骤：先本机到跳板机，然后在跳板机也生成了公钥到开发机。

### 创建公钥

Windows 中，公钥的生成路径是 `C:\Users\你的用户名\.ssh\`，或者直接用 `%USERPROFILE%\.ssh`。可以直接在终端（cmd）上：`type %USERPROFILE%\.ssh\id_rsa.pub`。

如果还没有生成，可以通过 `ssh-keygen -t rsa -b 4096` 来生成（兼容性最好）。复制全体密钥到平台就可以了，这样就能正常通过 aicoder 的命令来连接跳板机。

同样的操作，也可以在跳板机上执行一下（这个存疑，貌似也不需要），然后把公钥传到开发机中。

在跳板机上执行，先看公钥内容：

```bash
ssh-keygen -t rsa -b 4096  # 一路回车
```

查看与复制：

```bash
cat /root/.ssh/id_rsa.pub
```

### 写入公钥

在跳板机上执行，登录开发机：

```bash
mkdir -p /root/.ssh
echo "刚才复制的那串公钥内容" >> /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys
chmod 700 /root/.ssh
```

### 连接结构

真正的结构是：

```bash
本地 Windows
      ↓ SSH -J 跳板
公网跳板机：ssh-jumper.cloud.infini-ai.com
      ↓ 内网转发
AI-Coder 内网机：root@aic-dbjftbnvf2sbsvmc
      ↓ 再进一层（你又 ssh 进去的）
真正开发机：ifcroot@is-dcnwsxyrj3j67fiy-devmachine-0
```

这是企业级开发、云厂商、内网平台的标准操作，是从公网跳板机转到了内网的 ai-coder 的过程。

电脑 → 公网跳板机（ssh-jumper.xxx）→ 内网目标机器，SSH 自动帮你做了两层转发，所以不需要 ping 通，也不需要目标有公网 IP。不通才安全，才正常。

只有把公钥传到了目标机器的 ~/.ssh/authorized_keys，SSH 认证通过 → 允许建立隧道 → 就能进内网机器。

# vscode server提前添加
```bash
# 由于文件是共享的,直接进行解压就行了,只是规定好文件的路径信息

# 定义你的 commit（你提供的）
COMMIT_ID="e7fb5e96c0730b9deb70b33781f98e2f35975036"
# 创建目录
mkdir -p ~/.vscode-server/bin/$COMMIT_ID

# 解压（核心步骤，直接用你现有的 tar）
tar -zxf /mnt/project_modelware_roce/zhaojian/cyun/app/vscode-server-linux-x64.tar.gz --strip-components=1 -C ~/.vscode-server/bin/$COMMIT_ID

# 创建标记文件
touch ~/.vscode-server/bin/$COMMIT_ID/0

# 查看是否安装成功
ls -lh ~/.vscode-server/bin/$COMMIT_ID
```

---

## 代理与下载

### SSH 直连

通过离线的形式把 vscode-serve 传过去了，这样就有了直接建立连接的基础。并且此时的公钥应该同时存在于内网的所有机器。

```bash
Host cloud-jump
  # 直连 dev
  HostName ssh-jumper.cloud.infini-ai.com
Host dev
  HostName 172.27.130.151
  User root
  ProxyJump cloud-jump,root@aic-dbjftbnvf2sbsvmc
  ForwardAgent yes
  StrictHostKeyChecking no
  RemoteForward 12345 127.0.0.1:7890
```

首先是一个真正的公网跳板机：ssh-jumper.cloud.infini-ai.com。这是公网跳板机，唯一能直接访问的外网入口。

`ProxyJump cloud-jump,root@aic-dbjftbnvf2sbsvmc` 是两层自动跳转：

本地 → cloud-jump（跳板机）→ aic-dbjftbnvf2sbsvmc（中转机）→ dev（172.27.130.151）

支持逗号分隔多级跳转。

`ForwardAgent yes`：把本地 SSH 密钥代理到远程，让远程机器能使用你的本地密钥。

`StrictHostKeyChecking no`：不弹安全确认，直接连接，不询问。

`RemoteForward 12345 127.0.0.1:7890`：把本地 Windows 的 7890 端口代理，转发到开发机的 12345 端口。这样之后，就能够通过 `http_proxy=http://127.0.0.1:12345` 设置来访问网络。

---

## 可能遇到的问题

### 下载问题

下载不能使用 sudo，只能通过 apt install 直接下载才可以。原因：sudo 不会继承你的环境变量，设置的代理只给普通用户用，sudo apt 完全看不到代理。或者通过 `sudo -E`。

### tmux 继承了 http_proxy

这是很多启动失败的元凶。有些终端执行了 set_net 之后，如果此时启动 tmux，将会直接继承环境变量，导致后续的 tmux 都带有这样的代理，从而引起问题。所以，尽量就不要在 tmux 外部使用 set_net 之后又使用 tmux。

解决方法：直接杀死所有的 tmux 进程：

```bash
tmux kill-server
```

然后用一个没有代理的 tmux 启动一下清理掉缓存：

```bash
env -u http_proxy -u https_proxy -u HTTP_PROXY -u HTTPS_PROXY -u ALL_PROXY -u all_proxy -u NO_PROXY -u no_proxy tmux new -As main
```

这样之后的 tmux 就不会出现问题了。

### 终端快捷环境变量

这里设置了三个环境变量来管理各种代理：

```bash
set_pro() {
export http_proxy=http://10.8.36.50:3143
export https_proxy=http://10.8.36.50:3143
export HTTP_PROXY=http://10.8.36.50:3143
export HTTPS_PROXY=http://10.8.36.50:3143
}

set_net() {
    export http_proxy=http://127.0.0.1:12345
    export https_proxy=http://127.0.0.1:12345
    export HTTP_PROXY=$http_proxy
    export HTTPS_PROXY=$https_proxy
    export NO_PROXY=127.0.0.1,localhost
    export no_proxy=127.0.0.1,localhost
}

unset_net() {
    unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY ALL_PROXY all_proxy
    # 本地服务推理时不转发
    export NO_PROXY=127.0.0.1,localhost
    export no_proxy=127.0.0.1,localhost
}
```

> 相关阅读：[[SSH 公钥私钥免密登录原理]]、[[WezTerm终端配置]]