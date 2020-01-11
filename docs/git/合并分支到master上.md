[这篇文章](https://blog.csdn.net/boysky0015/article/details/78185879)写的很详细。命令总结如下：

```bash
# 查看当前分支，假设是dev
git branch

# 也可以通过IDE提交dev修改
git  add .
git  commit -m 'dev'
# 将dev修改推到远端
git push -u origin dev

### 切换到master分支上
git checkout master

# 如果是多人开发的话 需要把远程master上的代码pull下来，防止冲突
# 如果是自己一个开发就没有必要了，为了保险期间还是pull
git pull origin master

### 把dev分支的代码合并到master上
git merge dev

# 查看状态
git status

# 把同步后的master更新到远端
git push origin master
```

如果某个分支仅仅是为开发某个特性而产生多次commit，直接合并到master分支将导致master的commit看起来很乱，此时最好可以合并此特性分支的多次commit后再合并，参见[这篇文章](https://juejin.im/entry/5ae9706d51882567327809d0)。

