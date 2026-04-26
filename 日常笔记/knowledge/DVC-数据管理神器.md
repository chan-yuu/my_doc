# DVC：数据管理神器

实验的大额数据需要好好保存和复用，像 Git 管理代码那样管理数据，这就是 DVC 的定位。

> 相关阅读：[[Nextcloud]]、[[坚果云+obsidian+zotero--云端笔记与论文存储计划]]

---

## 一、克隆原始仓库

```bash
git clone https://github.com/chan-yuu/dvc_demo.git
cd dvc_demo
pip install "dvc[s3]" "s3fs==2024.6.1" "aiobotocore==2.13.0" "pathspec==0.12.1"
```

这里的版本是 Python 3.10 下的，版本对于推送是否成功比较关键。

初始化 DVC：

```bash
dvc init
```

---

## 二、配置天翼云 S3 远端

```bash
dvc remote add -d huabei2 s3://bucket-researcher/smith/zhaojian/cyun/data
dvc remote modify huabei2 endpointurl https://huabei-2.zos.ctyun.cn
dvc remote modify --local huabei2 access_key_id 你的KEY
dvc remote modify --local huabei2 secret_access_key 你的SECRET
```

local 参数的意义是密钥不会随便泄露。

配置 aiobotocore 签名（这一步是推送成功的关键，一次就行）：

```bash
mkdir -p ~/.aws 
cat > ~/.aws/config << 'EOF'
[default]
s3 =
    payload_signing_enabled = true
    addressing_style = path
EOF
```

---

## 三、追踪数据并推送

追踪数据，忽略原始数据文件：

```bash
dvc add data/
```

这会自动把 data 文件夹加入 `.gitignore`。

提交到代码仓库：

```bash
git add .
git commit -m "add code and track data with dvc"
git push origin master
```

推送到天翼云 S3：

```bash
dvc push
```

---

## 四、别人如何使用

别人使用时，通过该仓库 pull，注意也需要配置 key/secret：

```bash
git clone https://github.com/chan-yuu/dvc_demo.git
cd dvc_demo
pip install "dvc[s3]" "s3fs==2024.6.1" "aiobotocore==2.13.0"
# 配置好 key/secret 后
dvc pull   # 从 S3 下载数据
```

---

## 五、进阶配置

以上配置还没有完全打通流程，以经典的创建到推送到拉取为例，尽量减少对重复配置的依赖。

把密码信息固化到配置文件：

```bash
mkdir -p ~/.aws

cat > ~/.aws/credentials << 'EOF'
[default]
aws_access_key_id = 7d11436982154ba7b32b4661b1dfc559
aws_secret_access_key = 37003551a4c84efc8119e3eddda068d2
EOF

cat > ~/.aws/config << 'EOF'
[default]
s3 =
    payload_signing_enabled = true
    addressing_style = path
EOF
```

这样密码信息就不用每次配置了。

---

## 六、仓库创建流程

由于 DVC 和 Git 是紧密绑定的，尽量保证 Git 和 DVC 同时存在。

```bash
git clone https://github.com/chan-yuu/dvc_demo.git 
cd dvc_demo

# DVC 的初始化与 Git 不同，需要联通远端
dvc init
dvc remote add -d huabei2 s3://bucket-researcher/smith/zhaojian/cyun/data 
dvc remote modify huabei2 endpointurl https://huabei-2.zos.ctyun.cn

dvc add data/
```

全都打通后，就可以推送：

```bash
# 提交到 Git
git add .
git commit -m "init dvc and track data" 
git push 

# 推送数据到 S3
dvc push
```

---

## 七、拉取正确数据

拉取需要凭借 `.dvc` 中的配置以及 `data.dvc` 文件：

```bash
dvc pull
```

---

## 八、数据变更处理

数据有变化时，专注于部分修改：

```bash
dvc add data/          # 重新追踪
git add data.dvc
git commit -m "update dataset"
git push               # 推送新的指针
dvc push               # 推送新的数据到 S3
```

`.dvc` 的配置信息不需要修改，只需连接对应的路径。全部测试已完美通过，接下来就是合理利用。

> 相关阅读：[[Nextcloud]]、[[坚果云+obsidian+zotero--云端笔记与论文存储计划]]