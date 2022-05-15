默认是以工作目录为相对路径起始参数，可修改为以debug的文件为相对路径：

**更改launch.json设置**

在configurations中加入语句

```
"cwd": "${fileDirname}"
```

即可变成常规的相对路径模式

参考：

- https://www.zhihu.com/question/313379182

