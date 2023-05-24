# git rebase

开发过程中，本地通常会有无数次 commit ，可以合并“相同功能”的多个 commit，以保持历史的简洁

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

>pick：使用commit
reword：使用commit，修改commit信息
e, edit = use commit, but stop for amending
squash：使用commit，将commit信息合入上一个commit
fixup：使用commit，丢弃commit信息
x, exec = run command (the rest of the line) using shell
d, drop = remove commit

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
