key不在dict中，也不在defaultdict中时，访问dict会出现keyError，而defaultdict则返回其默认的类型。

此外，defaultdict字典不能用for key, value in defaultdict方式同时访问键和值，要用defaultdict.items()



```python
from collections import defaultdict
d1=dict()
d2=defaultdict(list)
d3=defaultdict(int) 
# int默认为0，defaultdict(list)默认为一个空列表
print (d1['a'])		# KeyError
print (d2['a'])		# []
print (d3['a'])		# 0
```



再看一个例子：

```python
import collections
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
# defaultdict
d = collections.defaultdict(list)
for k, v in s:
d[k].append(v)
# Use dict and setdefault
g = {}
for k, v in s:
g.setdefault(k, []).append(v)
# Use dict
e = {}
for k, v in s:
    e[k] = v
```

输出结果：
```python
list(d.items())
[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
>>> list(g.items())
>>> [('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
>>> list(e.items())
>>> [('blue', 4), ('red', 1), ('yellow', 3)]
>>> d
>>> defaultdict(<class 'list'>, {'blue': [2, 4], 'red': [1], 'yellow': [1, 3]})
>>> g
>>> {'blue': [2, 4], 'red': [1], 'yellow': [1, 3]}
>>> e
>>> {'blue': 4, 'red': 1, 'yellow': 3}
>>> d.default_factory
>>> <class 'list'>
```
可以看出collections.defaultdict()使用起来效果和运用dict.setdefault()比较相似。