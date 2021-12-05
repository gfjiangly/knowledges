<<<<<<< HEAD
import，报**ModuleNotFoundError**错误

- 使用点的方式可以导入，但是不能直接运行。
- 使用根目录名称导入，需要有__init__.py文件，否则不认为文件夹名是一个包

=======
## 模块和包

python中，每个py文件被称之为模块；每个具有`__init__.py`文件的目录被称为包。

只要模块或者包所在的目录在sys.path中，就可以使用`import 模块`或`import 包`来使用



## 精确导入和模糊导入

精确导入：

```
from Root.Pack1 import Pack1Class

import Root.Pack1.Pack1Class
```

模糊导入：

```
from Root.Pack1 import *
```

模糊导入中的*中的模块是由`__all__`来定义的，`__init__.py`的另外一个作用就是定义package中的`__all__`，用来模糊导入，如`__init__.py`：

```
__all__ = ["Pack1Class","Pack1Class1"]
```

在包外部调用：

```
from Root.Pack1 import *

a = Pack1Class.Pack1_AA("Alvin")

a.PrintName()
```

正常情况下我们写from aaa.bbb import *会引入位于aaa/bbb目录下的所有modules——这是一个不好的实践，因为有些东西可能会有副作用。特别的如果某个package下有你明确不希望被引用的py文件，可以通过`__all__`明确说明哪些是希望引入的，这样在python处理import *时会忽略掉不在`__all__`列表的内容。如下面的`__init__.py`指明了在import *时只有persons这个module会被引入。


**dir**
dir(）是python中内置的命令返回一个字符串数组，可以用来查看一个module声明的名称(`__all__`的内容)。通过dir(module_name)查看一个module声明的名称，不提供参数的情况下会返回当前文件的声明信息。



## Module的查找路径

python会通过两个地方查找被引入的module——builtins和sys.path。

builtins是python内置的标准函数库，可能因不同的平台而有所不同，

sys.path则分为项目目录和PYTHONPATH两部分。当一个module被引入时，python先查找builtins，如果没有找到则尝试在**当前目录**查找，如果仍然没有则查找PYTHONPATH也就是python的site-package目录。因此如果在项目目录中声明了和PYTHONPATH中同名的module，那么项目目录中的module会优先加载。


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





- command line中执行python时每次都会重新编译，不会尝试加载。
- 不存在源文件时，python不会直接从缓存中加载*.pyc文件。
>>>>>>> 4e900c70f408abab6b3b371e58e0c7cd0f46eebf
