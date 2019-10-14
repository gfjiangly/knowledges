import导入的两种方式

- 使用**点**的方式可以导入，但是不能直接运行。
- 使用**根目录名称**导入，需要有__init__.py文件，否则不认为文件夹名是一个包



python模块引用的顺序：

import引用时首先判断这个module是不是built-in即内建模块，比如import sys。如果是则引入内建模块，

如果不是则在一个称为sys.path的list中寻找（引用的时候会按照下面的顺序去引用）

sys.path在python脚本执行时动态生成，包括以下3个部分： 

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



同名模块冲突问题：

cvtools与pycocotools