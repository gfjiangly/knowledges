在pytorch0.4中，结合c语言和cuda进行拓展，使用torch.utils.ffi模块

pytorch中，官方更推荐使用C++接口来拓展pytorch，Pytorch正在将底层由C慢慢转化为C++

在pytorch中使用`.c`和`.cu`文件时，需要首先编译`.cu`，然后将编译好的`.cu`包含在路径中和`.c`一起再用cffi进行编译才能使用。

参考：

- https://oldpan.me/archives/pytorch-cuda-c-plus-plus





但在pytoch1.0之后，这个模块就用不了了，用torch.utils.cpp_extension来进行代替，该模块是对c++进行拓展，而且它具有最小（但通常足够的）的参数来构建c++扩展的优点

参考：

- https://oldpan.me/archives/pytorch-cuda-c-plus-plus
- https://github.com/pytorch/extension-cpp
- https://pytorch.org/tutorials/advanced/cpp_extension.html



不论是ffi模块或是cpp_extension模块，它们的构建在内部都使用了setuptools，pytorch帮我们找到c或c++文件，定义编译方式

，实际上具体的编译和安装操作都是setuptools来完成的。