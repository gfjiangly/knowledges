 持续集成/持续部署

![img](../../assets/166a9260db2ce221)

 对于开发一个Python包而言，

持续集成，意味着每写一段代码都能通过测试，同时不影响其它模块正常运行（先前的测试依然要通过）。测试意思是测试代码通过pytest之类工具。因此每一段功能代码都要有其对应的测试代码。

持续部署，意味着将持续集成的代码构建成包上传到PYPI。

参考：

- https://juejin.im/post/5bd130876fb9a05d2b6dfd23
- https://www.cnblogs.com/lyq863987322/p/8516958.html