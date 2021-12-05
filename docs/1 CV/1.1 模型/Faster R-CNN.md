>**Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks**
>
>https://arxiv.org/abs/1506.01497

判定anchor的正负样本准则：

1. IoU>positive_iou_thresh(0.7)为正样本，IoU<negative_iou_thresh(0.3)为负样本。此规则搞定大部分负样本anchor；
2. 对没有anchor认领的GT(无>0.7的anchor了)，将与之IoU阈值最大的anchor分配给此GT，即为正样本anchor；
3. 其余anchor均忽略，既不是正样本也不是负样本。

这个准则对算法有一定影响，是一个可修改点。



## 为什么需要正负样本，仅仅只有正样本不行吗

不行。但看检测框的坐标预测是不需要负样本的，只需要有GT即可，但是还需要为每个检测框赋予一个类别，否则就没有办法区分前景框和背景框了。一般把负样本当做第0类。