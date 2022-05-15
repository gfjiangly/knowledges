1/  git status -s 查看仓库状态

2/  git add src/components/文件名   添加需要提交的文件名（加路径--参考git status 打印出来的文件路径）

3/  git stash -u -k 忽略其他文件，把现修改的隐藏起来，这样提交的时候就不会提交未被add的文件

4/ git commit -m "哪里做了修改可写入..."

5/  git pull 拉取合并

6/  git push 推送到远程仓库

7/ git stash pop 恢复之前忽略的文件（非常重要的一步）


作者：前端热爱
链接：https://juejin.im/post/5d7f9ff6f265da03a53a69f1
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。