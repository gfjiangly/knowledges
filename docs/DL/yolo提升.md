一 精度

1 多尺度训练

2 增大网络分辨率

- 32倍数，如608

3 对自己的数据聚类生成anchor

- ./darknet detector calc_anchors data/obj.data -num_of_clusters 9 -width 416 -height 416

6 小目标多，将shortcut连到浅层

- 目标小于16*16（输入416x416）
- route layers = -1, 36 -> layers = -1, 11
- up sample stride=2 -> stride=4

7 同时要检测小目标和大目标

- 增加yolo层数
- 使用Spatial-full-model

3 添加无目标的背景图片

- label文件直接为空即可

4 迭代次数与训练集规模匹配

- 每个类别两千张，迭代次数2000*classes
- batch=64，或更大

5 增大最大检测数

- max=200，可以根据需求提高

8 需要检测出镜像的对象

- 在cfg [train]下 添加flip=0

9 训练集对象宽高尽量覆盖测试对象宽高

10 增大测试分辨率

- 不重新训练也会有提升，重新训练效果会更好
- 如448训练608测试，608训练832测试



二 速度

1 使用tiny-yolo

2 编译选项

- 开启CUDNN、CUDNN_HALF、OPENCV、AVX、OPENMP
- 关闭DEBUG

3 减小网络分辨率

