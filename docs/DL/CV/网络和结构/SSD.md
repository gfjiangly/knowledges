> **SSD: Single Shot MultiBox Detector**
> https://arxiv.org/abs/1512.02325

多尺度检测、无特征融合、分类回归在一个分支

### 结构

![SSD网络结构](assets/SSD/SSD%E7%BD%91%E7%BB%9C%E7%BB%93%E6%9E%84.jpg)

输出框数量：38x38x4+19x19x6+10x10x6+5x5x6+3x3x4+1x1x4=8732，每个框(4+C)维