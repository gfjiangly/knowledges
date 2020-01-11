### 1 IPython介绍
ipython是一个python的交互式shell，比默认的python shell好用得多，支持变量自动补全，自动缩进，支持bash shell命令，内置了许多很有用的功能和函数。学习ipython将会让我们以一种更高的效率来使用python。同时它也是利用Python进行科学计算和交互可视化的一个最佳的平台。

IPython提供了两个主要的组件：

1. 一个强大的python交互式shell 
2. 供Jupyter notebooks使用的一个Jupyter内核（IPython notebook）

IPython的主要功能如下：

1. 运行ipython控制台 
2. 使用ipython作为系统shell 
3. 使用历史输入(history) 
4. Tab补全 
5. 使用%run命令运行脚本 
6. 使用%timeit命令快速测量时间 
7. 使用%pdb命令快速debug 
8. 使用pylab进行交互计算 
9. 使用IPython Notebook



### 2 安装IPython
ipython支持Python2.7版本或者3.3以上的版本，我用的是windows下的python 3.4版本。

安装ipython很简单，可以直接使用pip管理工具即可:

```bash
pip install ipython
```

这条命令会自动安装IPython以及它的各种依赖包

如果我们也想在notebook中或者在Qt console中使用IPython，我们还需要安装Jupyter，如下命令：

```bash
pip install jupyter
```



### 3 一些典型用法

调用系统Shell命令。只需要在命令前加!即可



### 参考

- https://blog.csdn.net/gavin_john/article/details/53086766