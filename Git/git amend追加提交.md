添加的内容与上次内容属于同一个提交内容, 将本次的提交追加到上次的提交

```shell
# 添加提交内容
git add . 

# 追加到上次提交,通过core.editor指定的编辑器进行编辑
git commit --amend 

# 使用如下命令不进入编辑页面
git commit --amend --no-edit 

```

## 报错

```text
error: There was a problem with the editor 'vi'.
Please supply the message using either -m or -F option.
```

通过`git config`命令,配置全局变量, 制定特定的编辑器;

```shell
git config --global core.editor /usr/bin/vim
```
