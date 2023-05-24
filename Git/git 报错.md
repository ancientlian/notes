# 删除分支 错误`error: Cannot delete branch 'xxx' checked out at 'D:/xx'`

解决办法：此错误是在git删除分支时报的错误，删除分支时，当前分支不能停留在要删除的分支上，要切换到其他任意分支，再去删除目标分支。

# git 删除时报 `the branch is not fully merged` 这是什么意思

原因：XXX分支有没有合并到当前分支的内容

解决方法：使用大写的D 强制删除 git branch -D XXX
