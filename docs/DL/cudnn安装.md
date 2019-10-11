实际上不需要“安装”，只需要cudnn.h和libcudnn.so即可

分别放在/usr/local/cuda/include和/usr/local/cuda/lib64目录下

```bash
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```



![1569297352300](C:\Users\gfjiang\AppData\Roaming\Typora\typora-user-images\1569297352300.png)

下载时注意下载library，deb安装是没有.h文件的。如果后缀不是tgz，手动改成tgz。



官网指导：很详细

https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html