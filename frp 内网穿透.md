# frp

## 服务器 Docker 配置

在新版 frp 中，使用 toml 配置文件替代了 ini 配置文件

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

```text
# frps.toml
serverPort = 7000
# The default value is 127.0.0.1. Change it to 0.0.0.0 when you want to access it from a public network.
webServer.addr = "0.0.0.0"
webServer.port = 7500
# dashboard's username and password are both optional
webServer.user = "admin"
webServer.password = "admin"
```

7000 是服务

## windows 配置

### 1.安装 ssh

下载地址：<https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v9.5.0.0p1-Beta>

选择对应版本的安装包，例如`OpenSSH-Win64-v9.5.0.0.msi`

安装之后检查服务里的是否正常启动
![服务](https://s2.loli.net/2024/02/23/jBnPdEDJRWKqiUZ.png)

### 防火墙开放端口

控制面板-Windows Defender 防火墙-新建入站规则

创建端口-TCP-特定端口 22-允许连接-所有网络位置都默认应用

### 检查端口开放

命令检查端口是否监听

```shell
netstat -ano | findstr 22
```

telnet 检查

```shell
telnet 127.0.0.1 22
```

### 参考文章

https://blog.csdn.net/gaoluhua/article/details/129208427

## 参考文章
