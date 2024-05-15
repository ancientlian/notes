# 防火墙

```shell
# 停止firewall
systemctl stop firewalld.service

# 开启firewall
systemctl start firewalld.service

# 禁止firewall开机启动
systemctl disable firewalld.service

# 查看默认防火墙状态（关闭后显示 not running，开启后显示 running）
firewall-cmd --state

# 重启, 重新加载
firewall-cmd --reload

# 查看自己访问的端口是否开放
firewall-cmd --list-all

# 开放指定端口，记得要先开放端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=8847/tcp --permanent

#命令含义：
#--zone #作用域
#--add-port=8080/tcp  #添加端口，格式为：端口/通讯协议
#--permanent  #永久生效，没有此参数重启后失效

```
