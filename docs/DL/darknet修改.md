fork: https://github.com/AlexeyAB/darknet.git



1 类别数量与filter数量关系

filters=(classes + 5)x3，在三个yolo层中

相关位置：in the 3 `[convolutional]` before each `[yolo]` layer

修改方式：cfg



2 batch大小

- change line batch to [`batch=64`](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L3)
- change line subdivisions to [`subdivisions=8`](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L4)



3 最大迭代次数

- change line max_batches to (`classes*2000`), f.e. [`max_batches=6000`](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L20) if you train for 3 classes



4 obj.name和obj.data

obj.name中存放类名

```bash
head
hat

```

obj.data中存放数据集位置和weights保存位置

```bash
classes= 2
train  = data/train.txt
valid  = data/test.txt
names = data/obj.names
backup = backup/
```

标签注意

- `<object-class>` - integer object number from `0` to `(classes-1)`
- `<x_center> <y_center> <width> <height>` - float values **relative** to width and height of image, it can be equal from `(0.0 to 1.0]`
- for example: `<x> = <absolute_x> / <image_width>` or `<height> = <absolute_height> / <image_height>`
- atention: `<x_center> <y_center>` - are center of rectangle (are not top-left corner)



5 多尺度

按宽高比例缩放，默认448(14*32), 1/1.4-1.4内(11~20 * 32), 32整数倍。

相关位置：detector.c train_detector函数

修改方式：set flag random=1 in .cfg file and 改源码尺度（可选）





