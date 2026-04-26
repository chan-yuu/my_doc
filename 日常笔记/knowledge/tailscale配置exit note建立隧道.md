# Tailscale Exit Node 配置指南

通过校内设备作为 Exit Node，让校外设备使用校园网流量上网。

## 校内设备（Exit Node）

### 1. 安装 Tailscale

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

### 2. 登录

```bash
sudo tailscale up
```

### 3. 开启 IP 转发

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

### 4. 声明为 Exit Node

```bash
sudo tailscale up --advertise-exit-node
```

### 5. 优化 UDP 转发性能（可选）

```bash
# 安装 ethtool
sudo apt install -y ethtool

# 启用 UDP GRO forwarding
sudo ethtool -K enp6s0 rx-udp-gro-forwarding on

# 设置开机自动启用
cat << 'EOF' | sudo tee /etc/systemd/system/tailscale-udp-gro.service
[Unit]
Description=Enable UDP GRO forwarding for Tailscale
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -K enp6s0 rx-udp-gro-forwarding on

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable tailscale-udp-gro.service
```

> 注意：`enp6s0` 是网卡名称，根据实际情况修改，可用 `ip link` 查看。

### 6. 管理后台启用 Exit Node 权限

1. 访问 https://login.tailscale.com/admin/machines
2. 找到校内设备
3. 点击 `...` → `Edit route settings`
4. 开启 `Use as exit node`

---

## 校外设备

### 1. 安装并登录

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

### 2. 使用 Exit Node

```bash
sudo tailscale up --exit-node=<校内设备主机名或IP>
```

例如：
```bash
sudo tailscale up --exit-node=cyun-4090
# 或使用 Tailscale IP
sudo tailscale up --exit-node=100.71.48.84
```

### 3. 停止使用 Exit Node

```bash
sudo tailscale up --exit-node=
```

---

## 验证

在校外设备上检查出口 IP：
```bash
curl ip.sb
```

应显示校园网的公网 IP。

---

## 常用命令

| 命令 | 说明 |
|------|------|
| `tailscale status` | 查看所有设备状态 |
| `tailscale status --self` | 查看当前设备详情 |
| `tailscale ip` | 查看当前设备的 Tailscale IP |
| `tailscale ping <设备名>` | 测试设备间连通性 |


# ssh
通过设置：
sudo tailscale set --ssh

然后就能直接建立ssh了，不需要认证
tailscale ssh cyun@cyun-4090