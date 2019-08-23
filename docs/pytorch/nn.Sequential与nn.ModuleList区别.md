简而言之就是，nn.Sequential类似于Keras中的贯序模型，它是Module的子类，在构建数个网络层之后会自动调用forward()方法，从而有网络模型生成。而nn.ModuleList仅仅类似于python中的list类型，只是将一系列层装入列表，并没有实现forward()方法，因此也不会有网络模型产生的副作用。

 

需要注意的是，nn.ModuleList接受的必须是subModule类型，例如：

 

nn.ModuleList(

​            [nn.ModuleList([Conv(inp_dim + j * increase, oup_dim, 1, relu=False, bn=False) for j in range(5)]) for i in range(nstack)])

 

其中，二次嵌套的list内部也必须额外使用一个nn.ModuleList修饰实例化，否则会无法识别类型而报错！

 

因此nn.ModuleList比nn.Sequential可控性要大点，如果只要单个输出，nn.Sequential显然更方便。

 

nn.ModuleList类似于python中的list，那么它和list有什么区别？

一个直观的感受是如果模型的属性是list时，调用model.cuda()并不会把list中的层参数放入GPU中，而如果使用nn.ModuleList，调用model.cuda()它的层参数会进入GPU中执行。所以区别就是nn.ModuleList和nn.Sequential一样，都是Module的子类。