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

