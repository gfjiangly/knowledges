这部分内容不关键，如果你是py3的坚定支持者，且在工作中用不到py2，可以忽略。



我在mmcv代码中看到一个函数：mmcv.is_str

心想这么简单的一个判断还需要一个函数，进入源码发现原来是要与py2保持兼容。

里面用了six.string_types

def is_str(x):
    """Whether the input is an string instance."""
    return isinstance(x, six.string_types)

six针对python2和python3中各自的string类型进行了区分：在python2中，使用的是basestring；在python3中，使用的是str。



更多关于six的知识：

six.PY2 返回一个表示当前运行环境是否为python2的boolean值

six.PY3 返回一个表示当前运行环境是否为python3的boolean值

源码解析：

```
# Useful for very coarse version differentiation.
PY2 = sys.version_info[0] == 2
PY3 = sys.version_info[0] == 3
PY34 = sys.version_info[0:2] >= (3, 4)
```



理解six库的过程实际上就是理解py3与py2差异性的过程。