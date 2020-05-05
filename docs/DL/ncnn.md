NCNN是一个移动端推断框架（ARM），有ARM NEON优化，可以使用GPU推断，CPU端代码优化做的比较好，在CPU上运行可能比其它同类框架（如MNN）快。当然也支持x86平台运行，但是没什么优化，代码结构有些类似于caffe，但比caffe精简很多，毕竟不需要反向传播。模型文件由`.param`和`.bin`组成，`.param`内是关于网络层的描述，`.bin`是网络的权重，二进制形式。两者都可加密。

NCNN实现了很多CV层的前向传播代码，容易将caffe训练而来的模型转成ncnn模型。是否容易将其它框架（TensorFlow、PyTorch）训练的模型转成自己框架的模型，是这个框架是否容易上手、容易被接受的关键所在，毕竟谁也不想再用NCNN将模型实现一遍。如果很容易转换，NCNN的学习成本就被降低。

### caffe模型转NCNN模型

目前转的最好的就是caffe模型，这也不能理解，毕竟caffe就是使用cpp代码写的和用的。NCNN源码应该也有许多参考了caffe的设计。

ncnn源码内提供了caffe->ncnn的工具：caffe2ncnn，较为成熟，caffe不更新了。

### TensorFlow模型转NCNN模型

支持最不好的模型应该是TensorFlow模型，这个可以理解，TensorFlow太大了，同一个功能有很多种操作可以实现，这样在转换时就困难许多。

没尝试过转换。

### PyTorch模型转NCNN模型

对PyTorch的支持取决于PyTorch对ONNX的支持。PyTorch内部有工具可将模型转成ONNX模型，但是目前（PyTorch1.0）这个工具也比较不成熟，仅支持Python的部分特性（子集）。因为PyTorch模型是动态的网络，没有文件定义网络的结构，依靠运行时解析模型的Python代码（功能工作量大）（JIT模式）。NCNN对ONNX的支持还好，毕竟ONNX的初衷就是统一神经网络的ops标准，所以不会有太多重复性操作。目前ONNX项目发展较快，有些不支持的操作，可能过段时间就支持了，比较看好，无论谁把这个标准做好了，对于学习者都是好事。

在PyTorch1.0发布之前，PyTorch的模型部署问题，主要讨论ONNX，将PyTorch的模型通过ONNX转化为tensorflow的模型，然后freeze成pb文件部署。在PyTorch1.0发布之后，模型部署变得容易了，可以使用C++（另外一种部署PyTorch模型的思路，与本文讨论无关）或者Python（Torch Script）在不依赖任何Python代码来执行该PyTorch模型。不管怎样，必须要序列化模型。

Python代码 -> torch ops(及连接) -> Torch Script(Python子集) -> onnx ops(及连接) -> ONNX模型

有两种方法可以从Python代码 -> Torch Script(Python子集)。

#### Tracing

`torch.jit.trace`模块记录在所有张量上执行的操作，将生成的记录转换为`Torch Script`方法，例如：

```python
import torch

def foo(x, y):
    return 2*x + y

traced_foo = torch.jit.trace(foo, (torch.rand(3), torch.rand(3)))
```

trace仅记录张量上的操作，不会记录任何控制流操作，如if语句或循环。 当这个控制流在模块中保持不变时(不依赖输入)，这很好。 但有时控制流实际上是模型本身的一部分。 例如，序列到序列转换中的波束搜索是输入序列长度的循环(依赖输入)。 在这种情况下，跟踪不合适，并且应使用脚本(Torch Script)编写波束搜索这一块代码。

#### Scripting

更强大。使用Python语法直接编写`Torch Script`代码。 可以在`ScriptModule`的子类上使用`torch.jit.script`注解，在方法上使用`torch.jit.script_method`注解，注释函数的主体将直接转换为Torch脚本。 但`Torch Script`本身是Python语言的一个子集（使用Python有限的特性编写的），并非Python中的所有功能都可以工作，一般与张量计算、执行和控制相关的操作都支持。像MMDetection里面的模型大部分都有一些代码片段不支持，因为使用了很多Python的高级特性。

例子：

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.jit import ScriptModule, script_method, trace

class MyScriptModule(ScriptModule):
    def __init__(self):
        super(MyScriptModule, self).__init__()
        # trace produces a ScriptModule's conv1 and conv2
        self.conv1 = trace(nn.Conv2d(1, 20, 5), torch.rand(1, 1, 16, 16))
        self.conv2 = trace(nn.Conv2d(20, 20, 5), torch.rand(1, 20, 16, 16))

    @script_method
    def forward(self, input):
      input = F.relu(self.conv1(input))
      input = F.relu(self.conv2(input))
      return input
```



参考：

- https://pytorch.apachecn.org/docs/1.2/advanced/cpp_export.html
- https://zhpmatrix.github.io/2019/03/09/torch-jit-pytorch/