**ldconfig命令**的用途主要是在默认搜寻目录

- `/lib`
- `/usr/lib`
- `/etc/ld.so.conf`内所列的目录下，

搜索出可共享的动态链接库（格式如lib*.so*）,进而创建出动态装入程序(ld.so)所需的连接和缓存文件。缓存文件默认为`/etc/ld.so.cache`，此文件保存已排好序的动态链接库名字列表，为了让动态链接库为系统所共享，需运行动态链接库的管理命令ldconfig，此执行程序存放在`/sbin`目录下。

ldconfig通常在系统启动时运行，而当用户安装了一个新的动态链接库时，就需要手工运行这个命令。



**ldconfig几个需要注意的地方：**

1. 往`/lib`和`/usr/lib`里面加东西，是不用修改`/etc/ld.so.conf`的，但是完了之后要调一下ldconfig，不然这个library会找不到。
2. 想往上面两个目录以外加东西的时候，一定要修改`/etc/ld.so.conf`，然后再调用ldconfig，不然也会找不到。
3. 比如安装了一个[mysql](http://man.linuxde.net/mysql)到`/usr/local/mysql`，mysql有一大堆library在`/usr/local/mysql/lib`下面，这时就需要在`/etc/ld.so.conf`下面加一行`/usr/local/mysql/lib`，保存过后ldconfig一下，新的library才能在程序运行时被找到。
4. 如果想在这两个目录以外放lib，但是又不想在`/etc/ld.so.conf`中加东西（或者是没有权限加东西）。那也可以，就是[export](http://man.linuxde.net/export)一个全局变量LD_LIBRARY_PATH，然后运行程序的时候就会去这个目录中找library。一般来讲这只是一种临时的解决方案，在没有权限或临时需要的时候使用。
5. ldconfig做的这些东西都与运行程序时有关，跟编译时一点关系都没有。编译的时候还是该加-L就得加，不要混淆了。
6. 总之，就是不管做了什么关于library的变动后，最好都ldconfig一下，不然会出现一些意想不到的结果。不会花太多的时间，但是会省很多的事。
7. 再有，诸如libdb-4.3.so文件头中是会含有库名相关的信息的（即含“libdb-4.3.so”，可用strings命令察看），因此仅通过修改文件名以冒充某已被识别的库（如libdb-4.8.so）是行不通的。为此可在编译库的Makefile中直接修改配置信息，指定特别的库名。



参考：

- https://man.linuxde.net/ldconfig