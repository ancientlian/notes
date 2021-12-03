# nginx.conf配置文件

### （1）全局块：配置服务器整体运行的配置指令

### （2）events 块：影响 Nginx 服务器与用户的网络连接

### （3）http 块

# 反向代理

示例一：

第一步 在 windows 系统的 host 文件进行域名和 ip 对应关系的配置

第二步 在 nginx 进行请求转发的配置（反向代理配置）

![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103200326-1880157403.png)



示例二：

使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中，需要两个tomcat

（1）找到 nginx 配置文件，进行反向代理配置

![img](https://img2018.cnblogs.com/blog/1455597/201910/1455597-20191029103236151-575997990.png)

（2）开放对外访问的端口号 9001 8080 8081



















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

成功启动了nginx，但是不能访问页面，原因是因为使用的是https://192.168.153.128/，而正确的是http://192.168.153.128/