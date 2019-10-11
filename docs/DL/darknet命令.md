fork: https://github.com/AlexeyAB/darknet.git

1 训练

./darknet detector train data/obj.data yolo-obj.cfg darknet53.conv.74

- -gpus 0,1,2,3 指定GPU，默认使用GPU0
- -dont_show 不显示loss曲线图
- -mjpeg_port 8090 加上上一个选项，远程显示曲线，本地查看：http://ip-address:8090
- -json_port 8070 远程获取训练过程loss等的json数据
- -map 验证



2 聚类anchor

./darknet detector calc_anchors data/obj.data -num_of_clusters 9 -width 416 -height 416

./darknet detector calc_anchors cfg/safety_rope/rope.data -num_of_clusters 9 -width 416 -height 416

2 测试



3 获取部分权重

如从yolov3-tiny.weights中获取前15个卷积层权重：

./darknet partial cfg/yolov3-tiny.cfg yolov3-tiny.weights yolov3-tiny.conv.15 15