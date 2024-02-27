# nvm

## 安装

直接用`brew install nvm`

## 修改配置文件

在`~/.bash_profile`、`~/.zshrc`、`~/.profile` 或 `~/.bashrc` 其中一个。

通常 bash 在 `.bash_profile` 或 `.bashrc` ，zsh 在 `.zshrc`添加

```bash
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```

刷新

```bash
source ~/.zshrc
```

## 命令

```bash
nvm --version
nvm ls
nvm ls-remote
nvm install ${版本}
nvm use ${版本}
nvm alias default ${版本}
```
