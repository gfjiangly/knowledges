固定函数参数，返回一个新的函数。当函数参数太多，需要固定某些参数时，可以使用 `functools.partial` 创建一个新的函数。

```python
from functools import partial

def subtraction(x, y):
    return x - y

f = partial(subtraction, 4)  # 4 赋给了 x
>>> f(10)   # 4 - 10
-6
```