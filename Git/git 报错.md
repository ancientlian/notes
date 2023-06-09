# 删除分支 错误`error: Cannot delete branch 'xxx' checked out at 'D:/xx'`

解决办法：此错误是在git删除分支时报的错误，删除分支时，当前分支不能停留在要删除的分支上，要切换到其他任意分支，再去删除目标分支。

# git 删除时报 `the branch is not fully merged` 这是什么意思

原因：XXX分支有没有合并到当前分支的内容

解决方法：使用大写的D 强制删除 git branch -D XXX

# amend提交报错 `There was a problem with the editor 'vi'`

```text
error: There was a problem with the editor 'vi'.
Please supply the message using either -m or -F option.
```

原因：之前没有配置 core.editor 选项。
配置core.editor 选项后再次运行git commit --amend 即可。
```shell
git config --global core.editor /usr/bin/vim
```

配置core.editor 后依然出现错误，可能是vim 有修改，或使用macvim 替换。
解决：使用如下命令`git config --global core.editor $(which vim)`不指定vim 实际目录，使用变量引用。
