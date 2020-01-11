## 模块导入的两种方式

- 使用**点**的方式可以导入，但是不能直接运行。
- 使用**根目录名称**导入，需要有`__init__.py`文件，否则不认为文件夹名是一个包



## 模块引用的顺序

import时首先判断这个module是不是built-in即内建模块，比如import sys。如果是，则引入内建模块；如果不是，则在一个称为sys.path的list中寻找。sys.path在python脚本执行时动态生成，包括以下4个部分： 

```
1.脚本执行的位置，即运行模块的路径 （顺序第一）
2.环境变量中的PYTHONPATH, 即.bash_profile （顺序第二）
3.第三方模块的位置，一般在site-package文件夹下 （顺序第三）
4.通过sys.path.append(path)添加到sys.path中的路径 （顺序第四）
```

可以看到脚本执行的位置是位于第一优先级的。

查看python模块查找路径

```python
import sys
print(sys.path)
```



## 循环导入问题

一般错误提示：

```
ImportError: cannot import name xxx
```

循环导入的例子：

Main:

```python
import time
from entity import Ent	# 1 从entity导入Ent类
```

Entity:

```python
from physics import Physics	# 2 从physics导入Physics类
class Ent:
    pass
```

Physics:

```python
from entity import Ent	# 3 这里发生了循环导入，Physics类实际上先于Ent类初始化
class Physics:
    pass
```

I then run from main.py and I get the following error:

> ```py
> Traceback (most recent call last):
> File "main.py", line 2, in <module>
>     from entity import Ent
> File ".../entity.py", line 5, in <module>
>     from physics import Physics
> File ".../physics.py", line 2, in <module>
>     from entity import Ent
> ImportError: cannot import name Ent
> ```

You have circular dependent imports. `physics.py` is imported from `entity` before class `Ent` is defined and `physics` tries to import `entity` that is already initializing. Remove the dependency to `physics` from `entity` module.

循环导入属于设计问题，无法通过合理安排导入顺序解决，必须重新设计，移除`physics` 对 `entity` 模块依赖。



## 导入顺序问题

导入顺序问题可以通过合理安排导入顺序解决，不属于设计问题，较好解决。

一般错误提示：

```
AttributeError: module 'xxx' has no attribute 'yyy'
```

例子

`cvtools.__init__.py`

```python
# ...省略若干非核心代码
from .evaluation import *
from .ops import *
```

`cvtools.ops.__init__.py`

```python
from .nms import py_cpu_nms
__all__ = ['py_cpu_nms']
```

`cvtools.evaluation.merge_dets.py`

```python
# ...省略若干非核心代码
class MergeCropDetResults(object):
    def merge(self, nms_method=cvtools.py_cpu_nms, nms_th=0.15):
        pass
```

py_cpu_nms还没导入就在merge_dets模块使用了，因此报AttributeError。需将ops的导入调到evaluation导入前。本例举的是写cvtools库过程犯的小错误，cvtools的[GitHub代码](https://github.com/gfjiangly/cvtools)是没有这种问题。

原则：

- 一般将低依赖的模块放在靠前的位置导入，因为它的依赖较少