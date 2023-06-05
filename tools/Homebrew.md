# Homebrew

## 安装Homebrew

[https://brew.sh/index_zh-cn](https://brew.sh/index_zh-cn)

切换镜像源

```Bash
# 替换brew.git:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
# 替换homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
# 应用生效
brew update
# 替换homebrew-bottles:
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc

```

## 安装路径

Homebrew会将套件安装到独立目录，并将文件软链接至`/usr/local`。Homebrew的所有文件均会被安装到预定义目录下，所以无需担心Homebrew的安装位置。

## 安装git

```Bash
brew install git
# 查看版本
git --version

```

### 覆盖mac系统自带的git

```shell
brew link --overwrite git
```

记得改完之后重启终端

## 软件清理

1.旧版本的清除

Homebrew将会把老版本的包缓存下来，以便当你想回滚至旧版本时使用。但这是比较少使用的情况，长期缓存会占用大量存储空间，需要定时通过`brew cleanup`清除

2.软件安装包的清除

使用Homebrew安装软件包时，会自动先下载软件包，然后解压安装，安装成功后不会自动将软件安装包清除，如果为了节省空间需要将安装包清除，可以通过`brew --cache`命令查看存放路径，去进行删除

## 导出 

```text
brew bundle dump 来完成当前环境的导出,导出完成后，你会得到一个 Brewfile。
然后将 Brewfile 复制到新的电脑中，并执行 brew bundle 来开始安装的过程。
```

## 其他命令

```bash
# 显示已经安装软件列表
brew list 

# 来查看所有有更新版本的软件
brew outdated 

# 卸载
brew uninstall [软件名]

brew info [软件名]

#命令来清理系统中所有软件的历史版本，或者可以使用 来清理特定软件的旧版
brew cleanup 
brew cleanup [软件名]

# 管理后台软件
brew services list： 查看所有服务
brew services run [服务名]: 单次运行某个服务
brew services start [服务名]: 运行某个服务，并设置开机自动运行。
brew services stop [服务名]：停止某个服务
brew services restart：重启某个服务。

brew doctor 来开启 Homebrew 自带的检查，从而确认有哪些问题，并进行修复。

# 更新 brew
brew update

更新某具体软件: brew upgrade git

锁定某个软件禁止更新: brew pin git

解除禁更锁定: brew unpin git

查看brew存放安装包的缓存地址: brew --cache

```

## 避免报错 “Error: It seems the symlink source ...... is not there.”

```text
lian@172 Caskroom % brew install --cask docker
==> Downloading https://desktop.docker.com/mac/main/arm64/95914/Docker.dmg
Already downloaded: /Users/lian/Library/Caches/Homebrew/downloads/5c9e5db4eb8f5aa601896f2054c3021a3f093b4f41144e1f4dbe3c9b4e010880--Docker.dmg
==> Installing Cask docker
==> Moving App 'Docker.app' to '/Applications/Docker.app'
==> Backing App 'Docker.app' up to '/opt/homebrew/Caskroom/docker/4.16.2,95914/Docker.app'
==> Removing App '/Applications/Docker.app'
==> Purging files for version 4.16.2,95914 of Cask docker
Error: It seems the symlink source '/opt/homebrew/Caskroom/docker/4.16.2,95914/$APPDIR/Docker.app/Contents/Resources/etc/docker.zsh-completion' is not there.
```

### 解决方法

执行`export HOMEBREW_NO_INSTALL_FROM_API=1`

### 备注

注：自 brew 4.0 起，`HOMEBREW_INSTALL_FROM_API` 会成为默认行为，无需设置；大部分用户无需再克隆 homebrew/core 仓库，故无需设置 `HOMEBREW_CORE_GIT_REMOTE` 环境变量。但若需要运行 brew 的开发命令或者 brew 安装在非官方支持的默认 prefix 位置，则仍需设置 `HOMEBREW_CORE_GIT_REMOTE` 环境变量；如果不想通过 API 安装，可以设置 `HOMEBREW_NO_INSTALL_FROM_API=1`。

清华大学 Homebrew / Linuxbrew 镜像使用帮助
[https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/) 

## 常用 tap

第三方库

## 参考文章

<https://blog.csdn.net/weixin_56878436/article/details/120081045>
    

[**从零开始，编写一个** **HomeBrew** **缓存清理脚本** **-** **少数派**](https://sspai.com/post/65842)
    

[**Homebrew** **升级** **4.0.0****，麦金塔上的啤酒会更香吗？** **-** **少数派**](https://sspai.com/post/78587)