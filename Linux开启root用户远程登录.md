# Linux开启root用户远程登录

## 开启 root 账户,给root用户设置密码

```bash
sudo passwd root
```

输入两遍密码

## 修改配置文件

打开SSH配置文件。

```bash
vim /etc/ssh/sshd_config
```

修改如下参数

- PermitRootLogin no修改为PermitRootLogin yes。
- PasswordAuthentication no修改为PasswordAuthentication yes。

按Esc键，输入:wq保存修改。

重启SSHD服务。

```bash
service sshd restart
```
