# 使用电脑的小笔记们

## Linux小笔记

### 网络端口的相关命令

```bash
ss -lntup
# 查看接口的状态
netstat -tln
# 查看接口（少了process的状态）
lsof -i:9999
# 查看占用9999端口的进程信息
kill -9 pid
# 删去pid进程
```

### tmux使用

```bash
tmux new -s roclinux
# 激活新的名叫roclinux的窗口

第一步：按 Ctrl+B 组合键，然后松开。
第二步：再单独按一下 c 键。
# 创建新窗口

第一步：按 Ctrl-B 组合键，然后松开。
第二步：按数字 0 键
# 切换窗口

第一步：输入组合键 Ctrl+B，然后松开。
第二步：输入字母 d。
# 隐藏窗口

tmux ls
# 查看当前有哪些窗口

tmux a -t roclinux
# 进入roclinux窗口
```

