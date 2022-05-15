计算iou那一块有两种写法，一般实用的写法就是向量化写法。

```python
def nms(dets, thres):
    x1 = dets[:, 0]
    y1 = dets[:, 1]
    x2 = dets[:, 2]
    y2 = dets[:, 3]
    areas = (x2 - x1 + 1) * (y2 - y1 + 1)
    order = dets[:, 4].argsort()[::-1]  # 根据得分降序
    
    keeps = []
    while order.size() > 0:
        i = order[0]
        keeps.append(i)
        
        # 计算dets[i]与dets[order[1:]]的iou
        xx1 = np.maximum(x1[i], x1[order[1:]])
        yy1 = np.maximum(y1[i], y1[order[1:]])
        xx2 = np.minimum(x2[i], x2[order[1:]])
        yy2 = np.minimum(y2[i], y2[order[1:]])
        w = np.maximum(0., xx2 - xx1)
        h = np.maximum(0., yy2 - yy1)
        inter = w * h
        iou = inter / (areas[i] + areas[order[1:]] - inter)
        
        inds = np.where(iou < thres)[0]
        order = order[inds+1]
    return keeps
```

