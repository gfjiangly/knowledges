```python
import cv2.cv2 as cv
import numpy as np


points = np.array([[0, 0], [10, 10], [20,50], [100, 100]])
# 从点集获得按顺时针或逆时针顺序的凸集
hull_points = cv.convexHull(points)	# shape为[n, 2]

# 最小外接矩形
xywha = cv.minAreaRect(hull_points)	# 返回元组((x,y),(w,h),a)
xyxyxyxy = cv.boxPoints(xywha)	# 返回带方向矩形顶点坐标，shape为[4, 2]

# 最小外接正矩形
# 返回一个元组：(x1, y1, w, h), 与coco的bbox形同
xywh = cv.boundingRect(points)
```