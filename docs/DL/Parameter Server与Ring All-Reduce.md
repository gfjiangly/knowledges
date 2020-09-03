# PS 

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191227205724834-1310233014.png)

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191225220003605-713669215.png)

PS的分布式训练流程：

1. 每个worker载入一部分训练数据
2. worker节点从server节点pull最新的所需模型参数
3. worker节点利用本节点数据计算梯度
4. worker节点将梯度push到server节点
5. server节点汇总梯度更新模型
6. goto step2 直到迭代次数上限或模型收敛

## 同步更新与异步更新

worker节点的梯度下降不是“同步阻断式”（严格意义上一致性最强的梯度下降方法），根据调度逻辑可以是“异步非阻断式”的，如9图，节点在做第11次迭代（iter 11）计算时，第10次迭代后的push&pull过程并没有结束，也就是说最新的模型权重参数还没有被拉取到本地，该节点仍使用的是iter 10的权重参数计算的iter 11的梯度。但是iter12必须获取最新参数后再计算梯度，这是**通过一些“最大延迟”等参数来限制异步的程度**。比如可以限定在三轮迭代之内，模型参数必须更新一次，那么如果某worker节点计算了三轮梯度，该节点还未完成一次从server节点pull最新模型参数的过程，那么该worker节点就必须停下等待pull操作的完成。即iter12这个task是依赖与前面pull task的完成，如图10c，这是同步和异步之间的折衷方法。

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191225220251810-1479792538.png)

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191225220306697-24906326.png)

### 同步

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191225220615794-1808026337.gif)

### 异步

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191225220656556-1790518144.gif)

# Ring-AllReduce

在PS框架下，同步更新，然后将参数广播到worker node(虽然是worker node去PULL参数，但是同步更新会造成同时PULL就是广播)，会网络通信瓶颈，虽然说PS里用了多个server来减轻带宽压力，而worker的带宽没充分有用起来。

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191226165132835-2040058505.png)

 图15 Ring-AllReduce架构示意图

Ring AllReduce是一种分布式规约算法，《Bringing HPC Techniques to Deep Learning》将其用到深度学习里，用同步更新，但是没有通信压力，如图15，该方法所有节点都是等同的worker节点共N个，将梯度分成N份，梯度传播分为两步：

1. scatter-reduce：如图16左边，逐步交换彼此的梯度并融合，最后每个节点都会包含完整融合梯度的一部分。共N-1次通信，每次发送数据量S/N，S是总数据SIZE，B是带宽，因此`Data Transferred = (N-1)*S/(NB)`,计算量为`(N-1)*(S/N)*C`
2. allgather：如图16右边，逐步交换彼此不完整的融合梯度，最后所有 节点都会得到完整的融合梯度。共N-1次通信，`Data Transferred = (N-1)*S/(NB)`，没有计算。

总通讯量2(N-1)*S/(NB)不会随着节点个数增加而增加，具有线性加速的能力。

相比PS架构，Ring-allreduce架构是带宽优化的，因为集群中每个节点的带宽都被充分利用。此外，在深度学习训练过程中，计算梯度采用BP算法，其特点是后面层的梯度先被计算，而前面层的梯度慢于前面层，Ring-allreduce架构可以充分利用这个特点，在前面层梯度计算的同时进行后面层梯度的传递，从而进一步减少训练时间。在百度的实验中，他们发现训练速度基本上线性正比于worker节点数。

![img](assets/Parameter%20Server%E4%B8%8ERing%20All-Reduce/1031972-20191226165236807-878002249.png)



