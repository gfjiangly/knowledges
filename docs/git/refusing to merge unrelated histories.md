问题产生场景：

本地的工程从github上download下来后在pyCharm中修改了，此时没有版本控制，然后想把改动的更新到github上fork的代码里面，就产生了这样的问题。



这个问题是因为两个根本不相干的git库，一个是本地库，一个是远端库， 然后本地要去推送到远端， 远端觉得这个本地库跟自己不相干， 所以告知无法合并。



解决方法：

第一种方法： 先从远端库拉下来，把本地要加入的代码放到刚刚从远端库下载到本地的库中，然后提交上去，因为这样的话，你基于的库就是远端的库，这是一次update操作



第二种方法：强制合并，然后修改冲突地方

git remote add origin git@github.com/gfjiangly/mmdetection.git

关联可以在IDE里面添加

git pull origin master --allow-unrelated-histories

后面加上--allow-unrelated-histories，把两个不相干的分支进行强行合并，后面再push就可以了

注意本地必须要先add、commit完了，才能推上去





参考：

https://www.cnblogs.com/UncleYong/p/10654244.html