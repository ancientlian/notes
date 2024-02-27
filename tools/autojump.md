# cli 工具 Autojump

## 下载和安装 Autojump

```shell
sudo apt install autojump      #在 Debian, Ubuntu 和 Mint 上
sudo yum install autojump       #在 RHEL/CentOS/Fedora and Rocky/AlmaLinux 上
sudo emerge -a autojump        #在 Gentoo Linux 上
sudo apk add autojump          #在 Alpine Linux 上
sudo pacman -S autojump        #在 Arch Linux 上
sudo zypper install autojump   #在 OpenSUSE 上
```

## Autojump 配置

为了永久激活，添加到 BASH shell，需要运行以下命令。

```shell
echo '. /usr/share/autojump/autojump.sh' >> ~/.bashrc
#或者
echo '. /usr/share/autojump/autojump.bash' >> ~/.bashrc
```

提示：autojump 存储运行日志和错误日志文件的文件位于 .local/share/autojump/ 文件夹中。不要覆盖这些文件，否则您可能会丢失所有的状态。

```shell
ls -l ~/.local/share/autojump/
```

## 使用 Autojump

使用选项 -v 检查安装的 autojump 版本

```shell
j -v
#或者
autojump -v
```
