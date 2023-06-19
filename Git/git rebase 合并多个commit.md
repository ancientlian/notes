# git rebase

开发过程中，本地通常会有无数次 commit ，可以合并“相同功能”的多个 commit，以保持历史的简洁.rebase之前记得先`git stash`当前改动

1、先 `git log` 查看分支

2、使用 `git rebase -i HEAD~5` 压缩5个commit，或者`git rebase -i 51efae` 

```bash
# 从HEAD版本开始往过去数3个版本 
git rebase -i HEAD~3 

# 合并指定版本号（不包含此版本)
git rebase -i [commitid]
```

-   `-i（--interactive）`：弹出交互式的界面进行编辑合并
-   `[commitid]`：要合并多个版本之前的版本号，注意：`[commitid]` 本身不参与合并

vim编辑器，按i编辑，将后4个commit的pick修改为fixup，保留第一个pick。按esc键，输入`:wq`保存退出。

3、`git push --force` 提交

## 冲突解决

在 `git rebase` 过程中，可能会存在冲突，此时需要解决冲突。

错误提示信息：`git rebase -i resumeerror: could not apply ...`。

```javascript
# 查看冲突
$ git status

# 解决冲突之后，本地提交
$ git add .

# rebase 继续
$ git rebase --continue
```

## 概要

```shell
git rebase [ -i | --interactive] [<options>] [--exec <cmd>]
	[--onto <newbase> | --keep-base] [<upstream> [<branch>]]
git rebase [ -i | --interactive] [<options>] [--exec <cmd>] [--onto <newbase>]
	--root [<branch>]
git rebase ( --continue | --skip | --abort | --quit | --edit-todo | --show-current-patch)
```


## rebase 修改命令

```text
# 命令:
# p, pick <提交> = 使用提交
# r, reword <提交> = 使用提交，但编辑提交说明
# e, edit <提交> = 使用提交，但停止以便修补提交
# s, squash <提交> = 使用提交，但挤压到前一个提交
# f, fixup [-C | -c] <提交> = 类似于 "squash"，但只保留前一个提交
#                    的提交说明，除非使用了 -C 参数，此情况下则只
#                    保留本提交说明。使用 -c 和 -C 类似，但会打开
#                    编辑器修改提交说明
# x, exec <命令> = 使用 shell 运行命令（此行剩余部分）
# b, break = 在此处停止（使用 'git rebase --continue' 继续变基）
# d, drop <提交> = 删除提交
# l, label <label> = 为当前 HEAD 打上标记
# t, reset <label> = 重置 HEAD 到该标记
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       创建一个合并提交，并使用原始的合并提交说明（如果没有指定
# .       原始提交，使用注释部分的 oneline 作为提交说明）。使用
# .       -c <提交> 可以编辑提交说明。
# u, update-ref <引用> = 为引用 <ref> 设置一个占位符，以将该引用更新为此处的新提交。
#                       此 <引用> 在变基结束后更新。
#
# 可以对这些行重新排序，将从上至下执行。
#
# 如果您在这里删除一行，对应的提交将会丢失。
#
# 然而，如果您删除全部内容，变基操作将会终止。
```

## rebase options

`--onto <newbase>`

`--keep-empty`

`--rebase-merges[=(rebase-cousins|no-rebase-cousins)]`

`--root`
Rebase所有可从 `<branch>` 访问的提交，而不是用 `<upstream>` 限制
