> **SSD: Single Shot MultiBox Detector**
> https://arxiv.org/abs/1512.02325

多尺度检测、无特征融合、分类回归在一个分支

### 结构

![SSD网络结构](assets/SSD/SSD%E7%BD%91%E7%BB%9C%E7%BB%93%E6%9E%84.jpg)

输出框数量：38x38x4+19x19x6+10x10x6+5x5x6+3x3x4+1x1x4=8732，每个框(4+C)维

每个位置输出4个框的是去掉了3和1/3两种宽高比的anchor

## anchor匹配规则

首先，对于图片中每个ground truth，找到与其IOU最大的先验框，该先验框与其匹配，这样，可以保证每个ground truth一定与某个先验框匹配。通常称与ground truth匹配的先验框为正样本（其实应该是先验框对应的预测box，不过由于是一一对应的就这样称呼了），反之，若一个先验框没有与任何ground truth进行匹配，那么该先验框只能与背景匹配，就是负样本。一个图片中ground truth是非常少的， 而先验框却很多，如果仅按第一个原则匹配，很多先验框会是负样本，正负样本极其不平衡，所以需要第二个原则。

第二个原则是：对于剩余的未匹配先验框，若某个ground truth的 ![[公式]](assets/SSD/equation.svg) 大于某个阈值（一般是0.5），那么该先验框也与这个ground truth进行匹配。这意味着某个ground truth可能与多个先验框匹配，这是可以的。但是反过来却不可以

## Loss

交叉熵+Smooth_L1

## 数据扩增

主要采用的技术有水平翻转（horizontal flip），随机裁剪加颜色扭曲（random crop & color distortion），随机采集块域（Randomly sample a patch）（获取小目标训练样本）