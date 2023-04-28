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

## 安装git

```Bash
brew install git
# 查看版本
git --version



```

## 其他命令

```bash
brew list 


# 来查看所有有更新版本的软件
brew outdated 


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


brew bundle dump 来完成当前环境的导出,导出完成后，你会得到一个 Brewfile。
然后将 Brewfile 复制到新的电脑中，并执行 brew bundle 来开始安装的过程。
```

## 常用 tap

第三方库

## 参考文章

<https://blog.csdn.net/weixin_56878436/article/details/120081045>
    

[**从零开始，编写一个** **HomeBrew** **缓存清理脚本** **-** **少数派**](https://sspai.com/post/65842)
    

[**Homebrew** **升级** **4.0.0****，麦金塔上的啤酒会更香吗？** **-** **少数派**](https://sspai.com/post/78587)