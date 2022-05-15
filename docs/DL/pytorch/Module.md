
torch.nn.Module

- 所有神经网络模块的基类，自己写的模块也必须要继承此类
- 能包含其它Module（子模块），即可以形成嵌套的树形结构
- 方法`add_module`(*name*, *module*)用于添加子模块，被添加的子模块可以使用被给的名字访问



```python
import torch.nn as nn
import torch.nn.functional as F

class Model(nn.Module):
    def __init__(self):
        super(Model, self).__init__()
        self.conv1 = nn.Conv2d(1, 20, 5)
        self.conv2 = nn.Conv2d(20, 20, 5)

    def forward(self, x):
        x = F.relu(self.conv1(x))
 
```