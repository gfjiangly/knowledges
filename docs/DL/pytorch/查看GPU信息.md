为什么将数据转移至GPU的方法叫做.cuda而不是.gpu，就像将数据转移至CPU调用的方法是.cpu？这是因为GPU的编程接口采用CUDA，而目前并不是所有的GPU都支持CUDA，只有部分Nvidia的GPU才支持。PyTorch未来可能会支持AMD的GPU，而AMD GPU的编程接口采用OpenCL，因此PyTorch还预留着.cl方法，用于以后支持AMD等的GPU。

```python
torch.cuda.is_available()
# cuda是否可用；

torch.cuda.device_count()
# 返回gpu数量；

torch.cuda.get_device_name(0)
# 返回gpu名字，设备索引默认从0开始；

torch.cuda.current_device()
# 返回当前设备索引；
```




