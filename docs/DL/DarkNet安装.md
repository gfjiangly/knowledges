安装

- 可选，cuda8.0-10.1均可
- 可选，cudnn，要与cuda版本匹配
- 可选，OpenCV 4.x/3.x/2.4.x，如果用cuda10，最好opencv版本高点



其实都是可选，因为darknet支持CPU运行，但有cuda快500倍（官网说的）。

cuda直接运行官网的deb，然后配置环境变换即可，很简单。

cudnn只需要两个文件一个cudnn.h还有一个libcudnn.so，放在/usr/local/cuda中对应位置即可。

关键是opencv，需要从源码安装，编译时间非常长，比较繁琐。



darknet最好的Makefile配置是这样的：

```bash
GPU=1

CUDNN=1

CUDNN_HALF=1

OPENCV=1

AVX=0

OPENMP=1

LIBSO=1

ZED_CAMERA=0

# set GPU=1 and CUDNN=1 to speedup on GPU

# set CUDNN_HALF=1 to further speedup 3 x times (Mixed-precision on Tensor Cores) GPU: Volta, Xavier, Turing and higher

# set AVX=1 and OPENMP=1 to speedup on CPU (if error occurs then set AVX=0)

DEBUG=0
```

开启debug训练会慢很多。

