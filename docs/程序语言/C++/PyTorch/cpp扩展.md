 PyTorch的C++接口并不会自动实现反向求导 ，因此如果用C++写PyTorch的模块，必须实现前向和反向两个过程。

步骤

1. C++写前向过程；
2. C++写反向过程
3. 绑定到Python
4. 编写setup.py编译

 使用Aten编写出来的tensor，只要在程序中`.cuda()`，就可以将Tensor移到GPU当中 



为`CUDA/C++`创建一个`setuptools.Extension`。 创建一个`setuptools.Extension`用于构建`CUDA/C ++`扩展的最少参数（但通常是足够的）的便捷方法。这里包括`CUDA`路径，库路径和运行库。 所有参数都被转发给`setuptools.Extension`构造函数。

```python
>>> from setuptools import setup
>>> from torch.utils.cpp_extension import BuildExtension, CppExtension
>>> setup(
        name='cuda_extension',
        ext_modules=[
            CUDAExtension(
                    name='cuda_extension',
                    sources=['extension.cpp', 'extension_kernel.cu'],
                    extra_compile_args={'cxx': ['-g'],
                                        'nvcc': ['-O2']})
        ],
        cmdclass={
            'build_ext': BuildExtension
        })
```



参考

- https://pytorch.apachecn.org/docs/1.0/cpp_extension.html
- https://oldpan.me/archives/pytorch-cuda-c-plus-plus