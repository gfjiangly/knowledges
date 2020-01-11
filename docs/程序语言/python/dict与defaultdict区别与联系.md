## 区别

### KeyError(`__missing__`)处理

key不在dict中，访问dict会出现KeyError；key不在defaultdict中时，访问defaultdict则返回其默认的类型。如：

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

### 嵌套字典创建

用defaultdict实现不能直接defaultdict(defaultdict(list))之类，否则会报TypeError: first argument must be callable or None错误。正确用法是：

```python
# 这种写法的数据是不能被pickle序列化的，得传入模块级别的函数
nest_dict = defaultdict(lambda: defaultdict(list))  # __missing__方法中处理KeyError调用的处理函数

# 这种写法可被序列化
def d_list():
    return defaultdict(list)
dd = defaultdict(d_list)
```

当然也能用下面写法制作一个嵌套字典，但不能提供默认值，使用时需用get方法提供默认值。最重要的是不能保证里面嵌套的真的是字典（实际上defaultdict实现的嵌套字典也不能保证，Python数据对象自由度很高）。

```python
# 这种写法，不能保证里面类型一定还是个字典
dd = dict()
dd['a'] = {'b': 2}
```



## 联系

### 迭代方式相同

这一点是肯定的。因为defaultdict包装了dict，实现自定义`__missing__`处理。

```python
from collections import defaultdict
d = dict()
# d = defaultdict(list)
for key in d:
    print(key)
for key in d.keys():
    print(key)
for value in d.values():
    print(value)
for key, value in d.items():
    print(key, value)
```

defaultdict的迭代和dict是一样的。注意`d.keys()`不是list，是一个可迭代对象。

### 默认值处理

`collections.defaultdict()`使用起来效果和运用`dict.setdefault()`比较相似。

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


