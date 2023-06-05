## 安装zsh 和 starship（没有用 fish shell）

[https://github.com/ohmyzsh/ohmyzsh](https://github.com/ohmyzsh/ohmyzsh)

[https://starship.rs/](https://starship.rs/)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# 更新
omz update
# 刷新
source ~/.zshrc

# starship 是 rust 写的 prompt 工具，极快
brew install starship

# 在 zsh 中配置 starship 其实就是在配置文件最后一行添加配置；
# 如果使用的是fish shell，在 # ~/.config/fish/config.fish 添加 starship init fish | source
echo 'eval "$(starship init zsh)"' >> ~/.zshrc

```


问题 ：

连接失败，需要修改host

[https://blog.csdn.net/weixin_41111088/article/details/107845413](https://blog.csdn.net/weixin_41111088/article/details/107845413)

### 安装 zsh 的插件

- zsh-autosuggestions [https://github.com/zsh-users/zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) ctrl+e补全

- zsh-completions

- fast-syntax-highlighting

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/fast-syntax-highlighting
```

### 配置 ~/.zshrc 配置文件

配置插件、函数、source、alias
