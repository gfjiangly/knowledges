torch.nn.ModuleList

- 将子模块放在一个列表中，能够像一个python list一样索引
- 包含的子模块均已注册，可以被Module方法看见
- 只是将一系列层装入列表，并没有实现forward()方法，因此也不会有网络模型产生
- 只能容纳subModule类型
- 二次嵌套的list内部也必须额外使用一个nn.ModuleList修饰实例化，否则会无法识别类型而报错！


```python
# nn.ModuleList接受的必须是subModule类型
# 二次嵌套的list内部也必须额外使用一个nn.ModuleList修饰实例化，否则会无法识别类型而报错！
nn.ModuleList(
    [
        nn.ModuleList(
        [Conv(inp_dim + j * increase, oup_dim, 1, relu=False, bn=False) for j in range(5)]) 
        for i in range(nstack)
    ]
)
```



torch.nn.Sequential

- Module的子类，类似于Keras中的贯序模型
- 一个顺序容器，模块将按照在构造函数中传递的顺序添加进来。 或者，也可以传递模块的有序字典
- 在构建数个网络层之后会自动调用forward()方法，从而有网络模型生成

```python
# Example of using Sequential
model = nn.Sequential(
          nn.Conv2d(1,20,5),
          nn.ReLU(),
          nn.Conv2d(20,64,5),
          nn.ReLU()
        )

# Example of using Sequential with OrderedDict
model = nn.Sequential(OrderedDict([
          ('conv1', nn.Conv2d(1,20,5)),
          ('relu1', nn.ReLU()),
          ('conv2', nn.Conv2d(20,64,5)),
          ('relu2', nn.ReLU())
        ]))
```



比较

- nn.ModuleList和nn.Sequential都是Module的子类。
- nn.ModuleList比nn.Sequential可控性要大点
- 但如果只要单个输出，nn.Sequential显然更方便



nn.ModuleList类似于python中的list，那么它和list有什么区别？

- 如果模型的属性是list时，调用model.cuda()并不会把list中的层参数放入GPU中，
- 而如果模型的属性是nn.ModuleList，调用model.cuda()，在ModuleList中的层参数会进入GPU中执行，因为它是Module的子类。