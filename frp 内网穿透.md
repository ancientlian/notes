# frp

原理使用公网服务器作为跳板机反向代理 将需要内网服务器的端口映射出来，达到访问内网服务器的目的。
![结构图](https://github.com/fatedier/frp/raw/dev/doc/pic/architecture.png)

frp 下载地址：<https://github.com/fatedier/frp/releases>
github 地址：<https://github.com/fatedier/frp>

## 服务器 Docker 配置

在新版 frp 中，使用 toml 配置文件替代了 ini 配置文件

使用 docker-compose 部署 docker 容器

```docker
# docker-compose.yml
version: '3.3'
services:
    frps:
        restart: always
        network_mode: host
        volumes:
            - './frps.toml:/etc/frp/frps.toml'
        container_name: frps
        image: snowdreamtech/frps
```

配置`frps.toml`配置文件

```text
# frps.toml
serverPort = 7000 # 服务运行端口号
# The default value is 127.0.0.1. Change it to 0.0.0.0 when you want to access it from a public network.
webServer.addr = "0.0.0.0"
webServer.port = 7500 # 监控面板端口
# dashboard's username and password are both optional
webServer.user = "admin" # 监控面板账号
webServer.password = "admin" # 监控面板账号
```

启动容器或者手动命令启动

```shell
# ./frps -c ./frps.ini
./frps -c ./frps.toml
```

### 阿里云服务器

放行对应 7000/7500 端口

## windows client 端配置

### 1.安装 ssh

下载地址：<https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v9.5.0.0p1-Beta>

选择对应版本的安装包，例如`OpenSSH-Win64-v9.5.0.0.msi`

安装之后检查服务里的是否正常启动
![服务](https://s2.loli.net/2024/02/23/jBnPdEDJRWKqiUZ.png)

### 2.防火墙开放端口

控制面板-Windows Defender 防火墙-新建入站规则

创建端口-TCP-特定端口 22-允许连接-所有网络位置都默认应用

#### 检查端口开放

命令检查端口是否监听

```shell
netstat -ano | findstr 22
```

telnet 检查

```shell
telnet 127.0.0.1 22
```

### 客户端配置及启动

frpc.toml 配置文件

```ini
serverAddr = "公网IP"
serverPort = 7000

[[proxies]]
name = "test-tcp"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = 3389
```

3389 也就是 winddows 主机对外 remote 开放的端口，可以自由设置

手动启动

```shell
./frpc.exe -c ./frpc.toml
```

### 参考文章

<https://blog.csdn.net/gaoluhua/article/details/129208427>

## 使用穿透

使用公网 ip 访问 内网机器 22 端口了。

```shell
ssh -p 7000 root@公网ip
```

## 参考文章

<https://zhuanlan.zhihu.com/p/654683420>
<https://zhuanlan.zhihu.com/p/599123751>
