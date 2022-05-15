Git 全局设置:

```bash
# --global去掉时仅对单个仓库配置
git config --global user.name "********"
git config --global user.email "********@***.com"
```

创建 git 仓库:

```bash
git init .
git add .
git commit -m "first commit"
# 执行下面步骤前，https://github.com/gfjiangly/AerialDetection仓库要先在github网站上创建
# 关联到github仓库
git remote add origin https://github.com/gfjiangly/AerialDetection.git
git push -u origin master
```

已有项目?

```bash
cd existing_git_repo
git remote add origin https://github.com/gfjiangly/AerialDetection.git
git push -u origin master
```

使用本地项目创建 git 仓库过程是：选择要push的本地项目根目录

1、`git init` // 初始化项目，执行完此命令后会生成一个.git文件夹
2、`git add .` // 将本地项目所有文件添加到git管理，.指全部文件
3、`git commit -m “提交描述信息"`
4、`git remote add origin 刚刚新建的Github地址` // 将本地项目与远程git仓库关联
5、`git push -u origin master` /`git push -f origin master`  //-f 强制将本地项目push到远程仓库



参考：

- https://blog.csdn.net/north1989/article/details/53471439