# Nextcloud

本地的数据存储管理空间，可以通过这个来构建自己的数据库（使用局域网，通过 Tailscale 构建），这样就能实现更加方便快捷的同步了。

> 相关阅读：[[DVC-数据管理神器]]、[[坚果云+obsidian+zotero--云端笔记与论文存储计划]]

---

## 安装配置

```bash
sudo snap install nextcloud
sudo nextcloud.enable-https self-signed  # https 加密
sudo nextcloud.occ config:system:get trusted_domains  # 查看允许的设备 ip
sudo nextcloud.occ config:system:set trusted_domains 1 --value=100.120.115.45  # 添加 Tailscale 字段的允许
```

配置完成后，局域网内的设备可以通过 Tailscale 分配的内网 IP 访问 Nextcloud 服务。

> 相关阅读：[[DVC-数据管理神器]]、[[远程ssh的重构-基于服务器的开发]]