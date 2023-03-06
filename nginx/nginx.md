# nginx.conf配置文件

## （1）全局块

配置服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。

```text
worker_processes  1;
```

这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是
会受到硬件、软件等设备的制约

## （2）events 块

影响 Nginx 服务器与用户的网络连接

```
events {
    worker_connections  1024;
}
```

表示每个 work process 支持的最大连接数为 1024
这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置

## （3）http 块

# 反向代理

## 说明

## 示例一

第一步 在 windows 系统的 host 文件进行域名和 ip 对应关系的配置

第二步 在 nginx 进行请求转发的配置（反向代理配置）

![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103200326-1880157403.png)

**总结：**

监听 80 端口，访问域名为192.168.17.129，不加端口号时默认为 80 端口，故访问该域名时会跳转到127.0.0.1:8080 路径上

## 示例二

使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中，需要两个tomcat

（1）找到 nginx 配置文件，进行反向代理配置

![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103236151-575997990.png)

（2）开放对外访问的端口号 9001 8080 8081

## location 指令说明

`=`：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。

`~`：用于表示 uri 包含正则表达式，并且区分大小写。

`~*`：用于表示 uri 包含正则表达式，并且不区分大小写。

`^~`：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location块中的正则 uri 和请求字符串做匹配。

==注意：如果 uri 包含正则表达式，则必须要有 `~`或者 `~*`标识。==

# 负载均衡

## 说明

负载均衡即是将负载分摊到不同的服务单元，既保证服务的可用性，又保证响应足够快，给用户很好的体验。

## 分配方式(策略)

### 1、轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。

### 2、weight

weight 代表权,重默认为 1,权重越高被分配的客户端越多

指定轮询几率，weight 和访问比率成正比，用于后端服务器性能不均的情况。 例如：

```
upstream server_pool{
 server 192.168.5.21 weight=10;
 server 192.168.5.22 weight=10;
}
```

### 3、ip_hash

每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。 例如：

```text
upstream server_pool{
 ip_hash;
 server 192.168.5.21:80;
 server 192.168.5.22:80;
}
```

### 4、fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```text
upstream server_pool{
 server 192.168.5.21:80;
 server 192.168.5.22:80;
 fair;
}
```

# 动静分离

严格意义上说应该是动态请求跟静态请求分开，可以理解成使用 Nginx处理静态页面，Tomcat 处理动态页面。

动静分离从目前实现角度来讲大致分为两种，
**一种**是纯粹把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案；
**另外一种**方法就是动态跟静态文件混合在一起发布，通过 nginx 来分开。

## 示例

![image-20211208214524500](https://s2.loli.net/2021/12/08/PLnHaJzIsX2hQRg.png)

# nginx 原理与优化参数配置

# Keepalived+Nginx 高可用集群（主从模式）

# Keepalived+Nginx 高可用集群（双主模式）

# 其他

```bash
# 环境变量
vim /etc/profile
# 手动更新
source /etc/profile


#####防火墙
#停止firewall
systemctl stop firewalld.service 
 
#开启firewall
systemctl start firewalld.service

#禁止firewall开机启动
systemctl disable firewalld.service 
 
#查看默认防火墙状态（关闭后显示not running，开启后显示running）
firewall-cmd --state
# 重启
firewall-cmd --reload

#查看自己访问的端口是否开放
firewall-cmd --list-all

# 开放指定端口，记得要先开放端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=8847/tcp --permanent

#命令含义：
#--zone #作用域
#--add-port=8080/tcp  #添加端口，格式为：端口/通讯协议
#--permanent  #永久生效，没有此参数重启后失效

# 查看进程

ps -ef|grep nacos
ps -ef|grep nacos
ps -ef|grep nginx

# 一直不能正确启动tomcat的原因是因为环境变量配置错误了，吐血
#set java environment and tomcat
JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.282.b08-2.el8_3.x86_64
PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
CATALINA_HOME=/usr/src/apache-tomcat-8.5.65
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME CLASSPATH PATH CATALINA_HOME

```

192.168.153.128

成功启动了nginx，但是不能访问页面，原因是因为使用的是<https://192.168.153.128/，而正确的是http://192.168.153.128/>
