## 标量计算

```python
def calc_iou(b1, b2, eps=1e-6):
    """b1和b2均为[x1 y1 x2 y2]左上角和右下角两个点表示形式"""
    # 公共区域面积
    nx1 = max(b1[0], b2[0])
    ny1 = max(b1[1], b2[1])
    ny2 = min(b1[2], b2[2])
    ny2 = min(b1[3], b2[3])
    w = max(0., nx2 - nx1)  # 防止bbox不是一个真的box, 即x2 < x1 或 y2 < y1
    h = max(0., ny2 - ny1)
    over = w * h
    
    s1 = (b1[2] - b1[0]) * (b1[3] - b1[1])
    s2 = (b2[2] - b2[0]) * (b2[3] - b2[1])
    union = max(eps, s1 + s2 - over)
    
    iou = over / union
    return iou
```



## 向量化计算

使用numpy的broadcasting能力，numpy是C实现的，本身比python的for循环快很多，加上可以充分利用CPU的SIMD等数据并行指令

```python
def calc_ious(b1, b2, eps=1e-6):
    """Return iou array
    Parameters
    ----------
    b1: array, shape=(N, 4), x1y1x2y2
    b2: array, shape=(M, 4), x1y1x2y2
    Returns
    -------
    iou: array, shape=(N, M)"""
    
    # 这是学习数组广播一个非常好的例子
    # 当两个数组维度无法广播时，不要尝试手动扩展复制数据，
    # 而应该在需要广播的方向插入一个维度，这样数据就会顺着这个插入的1维度进行广播

    # Expand dim to apply broadcasting.
    b1 = b1[:, np.newaxis, :]
    b1_wh = b1[..., [2, 3]] - b1[..., [0, 1]]
    b1_lt = b1[..., :2]
    b1_rb = b1[..., 2:4]

    # Expand dim to apply broadcasting.
    b2 = b2[np.newaxis, :, :]
    b2_wh = b2[..., [2, 3]] - b2[..., [0, 1]]
    b2_lt = b2[..., :2]
    b2_rb = b2[..., 2:4]

    inter_lt = np.maximum(b1_lt, b2_lt)
    inter_rb = np.minimum(b1_rb, b2_rb)
    inter_wh = np.maximum(inter_rb - inter_lt, 0.)
    inter_area = inter_wh[..., 0] * inter_wh[..., 1]
    
    b1_area = b1_wh[..., 0] * b1_wh[..., 1]
    b2_area = b2_wh[..., 0] * b2_wh[..., 1]
    union = np.maximum(eps, b1_area + b2_area - inter_area)
    
    iou = inter_area / union
    return iou
```

