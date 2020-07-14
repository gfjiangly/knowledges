V1

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-3c99adb46b4256e74a5abb9026a17220_hd.jpg)

这样的结构主要有以下改进：

1. 一层block就包含1x1卷积，3x3卷积，5x5卷积，3x3池化(使用这样的尺寸不是必需的，可以根据需要进行调整)。这样，网络中每一层都能学习到“稀疏”（3x3、5x5）或“不稀疏”（1x1）的特征，既增加了网络的宽度，也增加了网络对尺度的适应性；
2. 通过deep concat在每个block后合成特征，获得非线性属性。

按照这样的结构来增加网络的深度，虽然可以提升性能，但是还面临几个问题：

- 计算量大（参数多）的问题。为改善这种现象，GooLeNet借鉴Network-in-Network的思想，使用1x1的卷积核实现降维操作(也间接增加了网络的深度)，以此来减小网络的参数量(这里就不对两种结构的参数量进行定量比较了)，如图所示。
- 结构不整齐，不利用计算优化。
- 这种结构是人工设计尝试过来的而非学习出来的。

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-4c7116b1d363bcf9e492e8437dcc108a_hd.jpg)

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-c214cb7df7b2eace7b9ba12da5fdbb40_hd.jpg)

如表所示，实现的网络仍有一层全连接层，该层的设置是为了**迁移学习**的实现（下同）。

在之前的网络中，最后都有全连接层，经实验证明，全连接层并不是很必要的，因为可能会带来以下三点不便：

- 网络的输入需要固定
- 参数量多
- 易发生过拟合

实验证明，将其替换为平均池化层（或者1x1卷积层）不仅不影响精度，还可以减少参数量。

此外，实验室的小伙伴最近做了下实验，如果是小目标检测的话，网络的最后还是需要几层全连接层的，猜想可能是用池化的话会损失太多信息，毕竟是小目标。

## Inception v2

（**注意**，这里实现的inception v2的结构是在inception v3论文中有介绍）

2015年Google团队又提出了inception v2的结构，基于上面提到的一些原则，在V1的基础之上主要做了以下改进：

⑴ 使用BN层，将每一层的输出都规范化到一个N(0,1)的正态分布，这将有助于训练，因为下一层不必学习输入数据中的偏移，并且可以专注与如何更好地组合特征（也因为在v2里有较好的效果，BN层几乎是成了深度网络的必备）；

（在[Batch-normalized](https://link.zhihu.com/?target=http%3A//arxiv.org/abs/1502.03167)论文中只增加了BN层，而之后的[Inception V3](https://link.zhihu.com/?target=http%3A//arxiv.org/abs/1512.00567)的论文提及到的inception v2还做了下面的优化）

⑵ 使用2个3x3的卷积代替得到特征图为35x35的5x5的卷积，这样既可以获得相同的视野(经过2个3x3卷积得到的特征图大小等于1个5x5卷积得到的特征图)，还具有更少的参数，还间接增加了网络的深度，如下图。（**基于原则3**）

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-fa843c3f4bacbd516d97876a5278342a_hd.jpg)figure5

⑶ 3x3的卷积核表现的不错，那更小的卷积核是不是会更好呢？比如2x2。对此，v2在17x17的梯度中使用1*n和n*1这种非对称的卷积来代替n*n的对称卷积，既降低网络的参数，又增加了网络的深度（实验证明，该结构放于网络中部，取n=7，准确率更高），如下。（**基于原则3**）

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-b9dc2760e100531c3c06039faac57451_hd.jpg)figure6

⑷ 在梯度为8x8时使用可以增加滤波器输出的模块（如下图），以此来产生高维的稀疏特征。（**基于原则2**）

(原则2指出，在高维特征上，采用这种结构更好，因此该模块用在了8x8的梯度上)

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-70f9ead6efc04aa2d5732c958a5c8b1a_hd.jpg)figure7

⑸ 输入从224x224变为229x229。

最后实现的Inception v2的结构如下表。

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-046e1d0163f6bab87a97a05a049ea7df_hd.jpg)

经过网络的改进，inception v2得到更低的识别误差率，与其他网络识别误差率对比如表所示。

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-46316e160995689c6bcfaeb089e2b5c1_hd.jpg)

如表，inception v2相比inception v1在imagenet的数据集上，识别误差率由29%降为23.4%。



## Inception v3

inception模块之间特征图的缩小，主要有下面两种方式：

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-042844ea68d69cd693f6082d455a2ff0_hd.jpg)

右图是先进行inception操作，再进行池化来下采样，但是这样参数量明显多于左图(比较方式同前文的降维后inception模块)，因此v2采用的是左图的方式，即在不同的inception之间（35/17/8的梯度）采用池化来进行下采样。

但是，左图这种操作会造成表达瓶颈问题，也就是说特征图的大小不应该出现急剧的衰减(只经过一层就骤降)。如果出现急剧缩减，将会丢失大量的信息，对模型的训练造成困难。（上文提到的**原则1**）

因此，在2015年12月提出的[Inception V3](https://link.zhihu.com/?target=http%3A//arxiv.org/abs/1512.00567)结构借鉴inception的结构设计了采用一种并行的降维结构，如下图：

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-148019d36a05103c33075ee3cb0c6d02_hd.jpg)

具体来说，就是在35/17/8之间分别采用下面这两种方式来实现特征图尺寸的缩小，如下图：

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-bc696053bf16578d3804d0d3e14329c3_hd.jpg)figure 5' 35/17之间的特征图尺寸减小

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-baa71ea4f5723788326e9cf15d0bfc4f_hd.jpg)figure 6' 17/8之间的特征图尺寸缩小

这样就得到Inception v3的网络结构，如表所示。

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-1d2c062e89b55993b601128b3e4bf409_hd.jpg)inception v3

经过优化后的inception v3网络与其他网络识别误差率对比如表所示。

![img](assets/Inception%E6%A8%A1%E5%9D%97/v2-a805c924419b551ebdab90bbb785fd58_hd.jpg)

如表所示，在144x144的输入上，inception v3的识别错误率由v1的7.89%降为了4.2%。

此外，文章还提到了中间辅助层，即在网络中部再增加一个输出层。实验发现，中间辅助层在训练前期影响不大，而在训练后期却可以提高精度，相当于正则项。