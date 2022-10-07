现象: 
创建一个文件夹python，里面建了一些文档，过了时间发现python小写不太好，于是乎右击重命名改成大写，然后push到github上发现有两个文件夹python和Python。

添加`git config core.ignorecase false`没有用。

重命名方法：
```bash
git mv -f python python_tmp
git mv -f python_tmp Python
```
注意不能直接`git mv -f python Python`，windows上认为python和Python是一个文件夹。

在mac上就没有这个问题。