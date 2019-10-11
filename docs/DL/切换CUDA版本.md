sudo rm -rf /usr/local/cuda

sudo ln -s /usr/local/cuda-8.0 /usr/local/cuda



cuda 版本 
cat /usr/local/cuda/version.txt

cudnn 版本 
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2