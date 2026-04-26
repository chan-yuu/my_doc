在终端中运行
```bash
sudo visudo
```
会出来一个类似 vim 的终端 edit 工具，此时把用户添加到文件夹的末尾即可：
```bash
mac ALL=(ALL) NOPASSWD: ALL
```
