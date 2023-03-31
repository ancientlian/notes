# 配置 SSH

通过配置 ServerAliveInterval 来实现，在 `~/.ssh/config` 中加入： `ServerAliveInterval=30`

`ServerAliveInterval 30` 表示ssh客户端每隔30秒给远程主机发送一个no-op包，no-op是无任何操作的意思，这样远程主机就不会关闭这个SSH会话。

打开`vim ~/.ssh/config`, 新增：

```text
Host *
    ServerAliveInterval 60
```

基本去连的机器都保持，所以配置了*，如果有需要针对某个机器，可以自行配置为需要的serverHostName。
