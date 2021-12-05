PyTorch 提供了大量与神经网络，任意张量代数（arbitrary tensor algebra），数据处理（data wrangling）和其他目的相关的操作。然而，你可能发现你还是会需要一些更加自定义的操作。例如，你有时可能希望使用一个你在某篇论文中找到的一个新型的激活函数，或者是实现一个为了你的研究所开发的新操作。

在 PyTorch 中集成这种自定义操作的最简单方法是通过 Python 语言对`Function`和`Module`进行扩写，正如在 [这里](https://pytorch.org/docs/master/notes/extending.html)所描述的那样。这种方式能让你充分的发挥自动微分（automatic differentiation）（让你不用去编写一些衍生的函数）与 Python 语言的常规情况下的表现力（usual expressiveness）的能力。





https://pytorch-cn.readthedocs.io/zh/latest/notes/extending/