1 权重保存

`yolo-obj_last.weights` will be saved to the `backup/` for each 100 iterations

`yolo-obj_xxxx.weights` will be saved to the `backup/` for each 1000 iterations



2 训练显存不够不要改小batch，增加subdivisions



3 数据增强

使用了多线程技术

load_data(detector.c)->load_threads(data.c)->load_data_in_thread(data.c)->load_thread(data.c)->load_data_detection

唉，确实有点绕，多线程代码也比较难调试。

读标签文件函数：read_boxes()

数据结构：box_label(darknet.h)

typedef struct box_label {

​    int id;

​    float x, y, w, h;

​    float left, right, top, bottom;

} box_label;

按固定大小（最大box数）分配truth内存

box坐标变换的一个例子：correct_boxes(data.c)

修改函数时注意有一模一样的参数，因为条件编译选择代码块，如OPENCV。

不要改错代码位置（哭，我就是！本来想添加有OPENCV的代码，结果改到了没OPENCV的代码块去了！）