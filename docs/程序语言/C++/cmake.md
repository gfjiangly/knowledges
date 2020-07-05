所在文件执行CMakeLists.txt `cmake .`进行cmake配置生成makefile文件。如果不允许直接在源码文件编译，可以：

```bash
# 进入源码目录
mkdir build && cd build
cmake ..
make -j8
# install如果没有指定目录，.so默认安装到/usr/local/lib/, .h在/usr/local/include
# 这两个目录均需要root权限
sudo make install
```



在CMakeList.txt中经常有各种编译选项，用于控制makefile编译指定模块，例如：

```
################################################################################
# Define options.
################################################################################

option(JAS_ENABLE_SHARED "Enable building of shared library" true)
option(JAS_ENABLE_LIBJPEG "Enable the use of the JPEG Library" true)
option(JAS_ENABLE_OPENGL "Enable the use of the OpenGL/GLUT Library" true)
option(JAS_ENABLE_STRICT "Enable pedantic error checking" false)
option(JAS_ENABLE_AUTOMATIC_DEPENDENCIES "Enable automatic dependencies" true)
option(JAS_LOCAL "Enable local hacks for developers (do not enable)" false)
option(JAS_ENABLE_DOC "Enable building of the documentation" true)
option(JAS_ENABLE_PROGRAMS "Enable building of the programs" true)

################################################################################
#
################################################################################
```

