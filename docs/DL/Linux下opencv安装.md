源码方式安装：

https://blog.csdn.net/cocoaqin/article/details/78163171

https://muzhan.blog.csdn.net/article/details/96976939

mkdir build

cd build

cmake -D WITH_CUDA=OFF \
      -D CMAKE_BUILD_TYPE=Release \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D WITH_IPP=OFF \
      -D BUILD_TIFF=ON \
      -D BUILD_ZLIB=ON \
      -D BUILD_JASPER=ON \
      -D BUILD_JPEG=ON \
      -D WITH_FFMPEG=ON \
      -D BUILD_TESTS=OFF \
      -D BUILD_PERF_TESTS=OFF \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D INSTALL_C_EXAMPLES=OFF \
      -D PYTHON_EXECUTABLE=/usr/bin/python3\

​      -D BUILD_EXAMPLES=OFF ..



注意不是为python中cv2，为C/C++程序调用



查看安装的opencv版本

pkg-config *--modversion opencv*